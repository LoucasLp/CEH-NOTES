# 🧹 08 — Covering Tracks (Effacement des Traces)

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## 📖 Définition

**Covering Tracks** est le processus par lequel un attaquant efface toute preuve de sa présence sur un système compromis afin d'éviter la détection, l'investigation forensique et les poursuites légales.

```
Objectifs :
├─ Effacer les preuves de l'intrusion
├─ Cacher les outils installés
├─ Éviter la détection par les équipes de sécurité
├─ Empêcher l'identification de l'attaquant
└─ Maintenir un accès non détecté sur le long terme
```

---

## 🪟 Covering Tracks — Windows

### Effacement des Event Logs

#### Via wevtutil (ligne de commande)
```bash
# Effacer le journal Système
wevtutil cl System

# Effacer le journal Sécurité
wevtutil cl Security

# Effacer le journal Applications
wevtutil cl Application

# Effacer TOUS les journaux en une commande
wevtutil el | Foreach-Object {wevtutil cl "$_"}

# Lister tous les journaux disponibles
wevtutil el

# Exporter un log avant effacement (discret)
wevtutil epl Security C:\backup_security.evtx
```

---

#### Via Meterpreter
```bash
# Effacer tous les event logs (une seule commande)
meterpreter > clearev
```

---

#### Via PowerShell
```powershell
# Effacer tous les journaux d'événements
Get-WinEvent -ListLog * | ForEach-Object {
    [System.Diagnostics.Eventing.Reader.EventLogSession]::GlobalSession.ClearLog($_.LogName)
}

# Effacer un journal spécifique
Clear-EventLog -LogName Security
Clear-EventLog -LogName System
Clear-EventLog -LogName Application
```

---

### Effacement de l'historique des fichiers récents

#### Windows 10 — Paramètres
```
Démarrer → Paramètres → Personnalisation → Démarrer
→ Désactiver "Afficher les applications les plus utilisées"
→ Désactiver "Afficher les éléments récemment ouverts dans les listes de raccourcis"
```

#### Registre Windows
```bash
# Supprimer les documents récents
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs" /f

# Supprimer les programmes récents
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\RunMRU" /f

# Supprimer l'historique des recherches
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\WordWheelQuery" /f
```

---

### Timestomping (Modification des timestamps)

> Modifier les dates de création, modification et accès pour tromper l'analyse forensique

```powershell
# PowerShell — modifier le timestamp d'un fichier
$file = Get-Item "C:\malware.exe"
$file.CreationTime = "01/01/2023 12:00:00"
$file.LastWriteTime = "01/01/2023 12:00:00"
$file.LastAccessTime = "01/01/2023 12:00:00"

# Copier les timestamps d'un fichier légitime
$legit = Get-Item "C:\Windows\notepad.exe"
$malware = Get-Item "C:\malware.exe"
$malware.CreationTime = $legit.CreationTime
$malware.LastWriteTime = $legit.LastWriteTime
```

```bash
# Meterpreter — timestomp
meterpreter > timestomp C:\\malware.exe -z   # Effacer les timestamps
meterpreter > timestomp C:\\malware.exe -f C:\\Windows\\notepad.exe  # Copier de notepad
```

---

## 🐧 Covering Tracks — Linux

### Effacement de l'historique Bash

```bash
# Méthode 1 : Définir la taille de l'historique à 0
export HISTSIZE=0
export HISTFILESIZE=0

# Méthode 2 : Effacer l'historique en mémoire
history -c
history -w

# Méthode 3 : Effacer le fichier d'historique
cat /dev/null > ~/.bash_history

# Méthode 4 : Effacer et sortir proprement
cat /dev/null > ~/.bash_history && history -c && exit

# Méthode 5 : Shred (écrasement sécurisé)
shred ~/.bash_history && cat /dev/null > ~/.bash_history && history -c && exit

# Méthode 6 : Désactiver l'enregistrement au démarrage
# Ajouter dans ~/.bashrc :
unset HISTFILE

# Méthode 7 : Ne pas logger les commandes commençant par espace
export HISTCONTROL=ignoreboth
 ls -la /etc/shadow   # ← espace en début → non loggé
```

---

### Effacement des logs système Linux

```bash
# Effacer les logs authentification
cat /dev/null > /var/log/auth.log

# Effacer les logs système
cat /dev/null > /var/log/syslog
cat /dev/null > /var/log/messages

# Effacer les logs Apache
cat /dev/null > /var/log/apache2/access.log
cat /dev/null > /var/log/apache2/error.log

# Effacer les logs de dernière connexion
cat /dev/null > /var/log/lastlog
cat /dev/null > /var/log/wtmp
cat /dev/null > /var/log/btmp

# Effacer les logs d'audit
cat /dev/null > /var/log/audit/audit.log

# Outil shred — écrasement sécurisé (difficile à récupérer)
shred -zuv /var/log/auth.log   # z=remplir de zéros, u=supprimer, v=verbose
```

---

### Effacer les traces de connexion SSH

```bash
# Effacer les fichiers de connexion
echo > /var/log/wtmp    # Connexions actuelles/passées
echo > /var/log/btmp    # Tentatives de connexion échouées
echo > /var/log/lastlog # Dernières connexions par utilisateur

# Supprimer des entrées spécifiques (utmp)
# Nécessite utmpdump ou un éditeur hex
```

---

## 🌐 Covering Tracks — Réseau

### Reverse HTTP Shell
```
Victime exécute une requête HTTP GET vers l'attaquant
→ Ressemble à du trafic web normal dans les logs
→ Difficile à distinguer du trafic légitime
```

### Reverse ICMP Tunnel
```
ICMP Echo/Reply utilisés comme transporteur de charge utile TCP
→ Les firewalls laissent souvent passer ICMP
→ Les IDS ne l'inspectent pas toujours
```

### DNS Tunnel
```
Payload encodé dans les requêtes/réponses DNS
→ Exfiltration via le protocole DNS
→ Difficile à bloquer sans casser la résolution DNS
→ Exemple : iodine, dnscat2
```

### Paramètres TCP de couverture
```
Utilisation des paramètres TCP pour cacher des données :
├─ IP ID field
├─ TCP ACK number
└─ Initial Sequence Number (ISN)
→ Canaux couverts (Covert Channels)
```

---

## 🛠️ Outils de Covering Tracks

### CCleaner (Windows)
- Nettoyage complet du système
- Registre, fichiers temp, historiques navigateurs
- Suppression sécurisée (DoD 5220.22-M)
- Effacement de l'espace libre (Wipe Free Space)

### DBAN (Darik's Boot and Nuke)
- Outil de démarrage pour **effacement total** d'un disque
- Algorithmes : DoD 5220.22-M, Gutmann (35 passes), PRNG
- Rend la récupération forensique quasi impossible

### Privacy Eraser
- Nettoyage traces Windows complètes
- Historiques navigateurs
- Données de récupération de fichiers

### Wipe
- Outil Linux d'effacement sécurisé
```bash
wipe -rf /path/to/sensitive/
wipe -f malware.exe
```

### Meterpreter clearev
```bash
meterpreter > clearev
[*] Wiping 97 records from the Application log...
[*] Wiping 415 records from the System log...
[*] Wiping 7 records from the Security log...
```

---

## 🔎 Event IDs Windows à surveiller

| Event ID | Description | Attaquant essaie d'effacer... |
|----------|-------------|-------------------------------|
| **1102** | Security log effacé | Traces de connexion/actions |
| **104** | System log effacé | Événements système |
| **4688** | Processus créé | Exécution de programmes |
| **4624** | Connexion réussie | Activité de compte |
| **4625** | Connexion échouée | Tentatives de brute-force |
| **4634** | Déconnexion | Sessions |
| **4698** | Tâche planifiée créée | Persistance |
| **7045** | Nouveau service installé | Backdoor |

---

## 🎯 Techniques anti-forensiques avancées

### Encryption Before Deletion

```bash
# Chiffrer avant de supprimer = plus difficile à récupérer
gpg --symmetric secret.txt    # Chiffrer
gpg -d secret.txt.gpg         # Déchiffrer
shred -u secret.txt            # Supprimer de manière sécurisée
```

### Overwriting Deleted Files

```bash
# Linux — scrub (écrasement de l'espace libre)
scrub -p dod /dev/sda

# Windows — sdelete (Sysinternals)
sdelete -z C:    # Effacer l'espace libre
sdelete -p 7 secret.txt   # 7 passes d'écrasement
```

### Live Forensics Evasion

```
Techniques pour éviter l'analyse en direct :
├─ Rootkits en mémoire (pas de fichiers sur disque)
├─ Fileless malware (PowerShell, VBA macros)
├─ Processus injection (code dans processus légitimes)
└─ Encrypted C2 communications (HTTPS, DNS-over-HTTPS)
```

---

## 🛡️ Détection du Covering Tracks

| Mesure défensive | Description |
|-----------------|-------------|
| **SIEM centralisé** | Logs envoyés vers un serveur externe — ne peuvent être effacés localement |
| **Immutable logging** | Logs en lecture seule (syslog-ng, journald avec forward) |
| **File integrity monitoring** | AIDE, Tripwire — alerte si les logs sont modifiés |
| **Audit Policy** | Activer "Audit Log Clearing" dans les GPO |
| **Canary Files** | Fichiers "honeypot" dans les logs — alert si accédés |
| **Forensic timeline** | Comparer timestamps avec les logs journaux |
| **Alertes Event 1102/104** | Déclencher une alerte immédiate si les logs sont effacés |

---

## 💡 Bonne pratique défensive

```
RÈGLE D'OR : Les logs ne doivent JAMAIS être stockés uniquement sur le système cible

Architecture recommandée :
┌─────────────────────────────────────────────────────┐
│  Systèmes surveillés                                │
│  (Windows / Linux)                                  │
│       │                                             │
│       └──── Logs envoyés en TEMPS RÉEL ────────────►│
│                                                     │
│  ┌──────────────────────────────────────────────┐   │
│  │     SIEM / Log Server (hors réseau cible)    │   │
│  │     Splunk, ELK, Graylog, Wazuh              │   │
│  └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘

→ L'attaquant peut effacer les logs locaux
→ Les logs du SIEM restent intacts → Evidence forensique préservée
```

---

[← Stéganographie](./07-steganography.md) | [→ Active Directory Attacks](./09-active-directory-attacks.md)
