# 07 — Outils & Commandes · Cheat Sheet

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## ⚡ Tableau des Outils CEH v13

| Outil | Protocole | OS | Usage |
|-------|-----------|----|-------|
| `nbtstat` | NetBIOS | Windows | Tables NetBIOS |
| `enum4linux` | NetBIOS/SMB | Linux | Énumération complète Windows |
| `smbclient` | SMB | Linux | Connexion aux partages |
| `smbmap` | SMB | Linux | Permissions des partages |
| `snmpwalk` | SNMP | Linux | Parcours arbre MIB |
| `snmp-check` | SNMP | Linux | Énumération SNMP complète |
| `onesixtyone` | SNMP | Linux | Brute force community strings |
| `ldapsearch` | LDAP | Linux | Requêtes annuaire |
| `dig` | DNS | Linux | Requêtes DNS avancées |
| `dnsenum` | DNS | Linux | Énumération DNS + brute force |
| `dnsrecon` | DNS | Linux | Reconnaissance DNS complète |
| `fierce` | DNS | Linux | Découverte sous-domaines |
| `subfinder` | DNS | Linux | Sous-domaines passifs |
| `smtp-user-enum` | SMTP | Linux | Enumération comptes email |
| `showmount` | NFS | Linux | Lister les exports NFS |
| `rpcinfo` | RPC | Linux | Services RPC disponibles |
| `rpcscan` | RPC | Linux | Scan services RPC |
| `ntpq` | NTP | Linux | Informations serveur NTP |
| `ntptrace` | NTP | Linux | Hiérarchie NTP |
| `ike-scan` | IPSec | Linux | Énumération VPN IPSec |
| `svmap` | VoIP | Linux | Scanner équipements SIP |
| `nmap` | Tous | Linux/Win | Scanner universel + scripts NSE |
| `Metasploit` | Tous | Linux | Modules d'énumération |
| `NetScanTools Pro` | Tous | Windows | Suite graphique |

---

## 🗂️ Commandes par Protocole

### 📌 NetBIOS (Port 137)
```bash
# === WINDOWS ===
nbtstat -n                          # Table locale
nbtstat -a <IP>                     # Table distante (par nom)
nbtstat -A <IP>                     # Table distante (par IP)
nbtstat -c                          # Cache NetBIOS
net use \\<IP>\IPC$ "" /user:""     # Session nulle
net view \\<IP>                     # Partages disponibles

# === LINUX ===
nbtscan <IP>                        # Scan NetBIOS
nbtscan -r <IP_RANGE>               # Scan de plage
enum4linux -a <IP>                  # Énumération complète
enum4linux -u <IP>                  # Utilisateurs seulement
enum4linux -s <IP>                  # Partages seulement
```

### 📌 SMB (Port 139/445)
```bash
# Lister les partages
smbclient -L //<IP> -N              # Sans credentials
smbclient -L //<IP> -U user         # Avec credentials

# Se connecter à un partage
smbclient //<IP>/share -U user

# SMBMap - Permissions
smbmap -H <IP>
smbmap -H <IP> -u user -p pass -d domain
smbmap -H <IP> -R                   # Récursif

# Nmap NSE
nmap --script smb-enum-shares <IP>
nmap --script smb-enum-users <IP>
nmap --script smb-os-discovery <IP>
nmap --script smb-vuln* <IP>        # Vulnérabilités SMB
nmap -p 139,445 --script smb-security-mode <IP>
```

### 📌 SNMP (Port 161/162)
```bash
# snmpwalk
snmpwalk -v2c -c public <IP>        # Parcours complet
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.1.1.0  # Desc système
snmpwalk -v2c -c public <IP> 1.3.6.1.2.1.25.4.2  # Processus
snmpwalk -v2c -c public <IP> 1.3.6.1.4.1.77.1.2.25  # Users

# snmp-check
snmp-check <IP>
snmp-check -v 2c -c public <IP>

# Brute force
onesixtyone -c community.txt <IP>
nmap -sU -p 161 --script snmp-brute <IP>

# Nmap NSE SNMP
nmap -sU -p 161 --script snmp-info <IP>
nmap -sU -p 161 --script snmp-interfaces <IP>
nmap -sU -p 161 --script snmp-processes <IP>
```

### 📌 LDAP (Port 389/636)
```bash
# Connexion anonyme
ldapsearch -x -H ldap://<IP> -b "DC=domain,DC=com"

# Avec auth
ldapsearch -x -H ldap://<IP> \
  -D "CN=user,DC=domain,DC=com" -w "password" \
  -b "DC=domain,DC=com" "(objectClass=*)"

# Utilisateurs
ldapsearch -x -H ldap://<IP> -b "DC=domain,DC=com" \
  "(objectClass=user)" cn sAMAccountName

# Administrateurs
ldapsearch -x -H ldap://<IP> -b "DC=domain,DC=com" \
  "(adminCount=1)" cn

# Nmap LDAP
nmap -p 389 --script ldap-rootdse <IP>
nmap -p 389 --script ldap-search <IP>
nmap -p 389 --script ldap-brute <IP>
```

### 📌 DNS (Port 53)
```bash
# dig - Zone Transfer
dig axfr @<NS_SERVER> <DOMAIN>
dig ns <DOMAIN>
dig any <DOMAIN>
dig mx <DOMAIN>
dig -x <IP>                         # Reverse DNS

# nslookup
nslookup -type=any <DOMAIN> <NS_SERVER>

# dnsenum
dnsenum <DOMAIN>
dnsenum --enum -f subdomains.txt <DOMAIN>

# dnsrecon
dnsrecon -d <DOMAIN>
dnsrecon -d <DOMAIN> -t axfr
dnsrecon -d <DOMAIN> -t brt -D subdomains.txt

# fierce
fierce --domain <DOMAIN>

# subfinder (CEH v13)
subfinder -d <DOMAIN> -resolve
```

### 📌 SMTP (Port 25)
```bash
# Connexion manuelle
telnet <IP> 25
nc <IP> 25
EHLO test.local
VRFY admin
EXPN users

# smtp-user-enum
smtp-user-enum -M VRFY -U users.txt -t <IP>
smtp-user-enum -M RCPT -U users.txt -t <IP> -f test@test.com
smtp-user-enum -M EXPN -U users.txt -t <IP>

# Nmap
nmap -p 25 --script smtp-commands <IP>
nmap -p 25 --script smtp-enum-users <IP>
nmap -p 25 --script smtp-open-relay <IP>
```

### 📌 NFS (Port 2049)
```bash
# Lister les exports
showmount -e <IP>

# Monter un partage
mount -t nfs <IP>:/share /mnt/nfs

# rpcinfo
rpcinfo -p <IP>

# Nmap
nmap -p 111,2049 --script nfs-ls,nfs-showmount,nfs-statfs <IP>
```

---

## 🔥 One-Liners Essentiels

```bash
# === SCAN INITIAL RAPIDE ===
nmap -sV -p 53,88,111,135,137,139,161,389,445,2049,3268 <IP>

# === ÉNUMÉRATION SMB COMPLÈTE ===
enum4linux -a <IP> | tee enum4linux_results.txt

# === SNMP DUMP COMPLET ===
snmp-check <IP> | tee snmp_results.txt

# === DNS ZONE TRANSFER ===
dig axfr @$(dig ns target.com +short | head -1) target.com

# === TOUS LES SCRIPTS NMAP ENUMÉRATION ===
nmap -sV -sC -p- --script "smb-enum*,snmp*,ldap*" <IP>

# === SCAN UDP SNMP ===
nmap -sU -p 161 --script snmp-info,snmp-processes <IP>
```

---

## 🔧 Suite Metasploit

```bash
msfconsole

# SMB
use auxiliary/scanner/smb/smb_enumusers
use auxiliary/scanner/smb/smb_enumshares
use auxiliary/scanner/smb/smb_version

# SNMP
use auxiliary/scanner/snmp/snmp_enum
use auxiliary/scanner/snmp/snmp_login

# SMTP
use auxiliary/scanner/smtp/smtp_enum

# FTP
use auxiliary/scanner/ftp/anonymous
use auxiliary/scanner/ftp/ftp_version
```

---

## 📊 Wordlists Utiles (Kali Linux)

```bash
/usr/share/wordlists/rockyou.txt             # Mots de passe généraux
/usr/share/wordlists/dirb/common.txt         # Répertoires web
/usr/share/metasploit-framework/data/wordlists/unix_users.txt  # Usernames Unix
/usr/share/metasploit-framework/data/wordlists/unix_passwords.txt

# Générer une wordlist personnalisée
crunch 4 8 -t @@@@ -o wordlist.txt
```

---

[← NFS · RPC · FTP](./06-nfs-rpc-ftp.md) | [Contre-mesures →](./08-contre-mesures.md)
