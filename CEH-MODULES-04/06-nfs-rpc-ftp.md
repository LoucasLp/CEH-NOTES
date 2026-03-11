# 06 — Énumération NFS · RPC · FTP · NTP · IPSec · BGP

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## 🔴 NFS — Network File System

### Qu'est-ce que NFS ?
NFS permet de **monter des systèmes de fichiers distants** comme s'ils étaient locaux.  
Port : **TCP 2049**

```
Client NFS                    Serveur NFS
    │                              │
    │── showmount -e <IP> ────────→│  (liste les exports)
    │← Liste des partages ─────────│
    │                              │
    │── mount <IP>:/share /mnt ───→│  (montage local)
    │← Accès accordé ──────────────│
    │                              │
    └────── Lecture/Écriture ──────┘
```

### Risques NFS mal configuré
- Accès à des fichiers sensibles (configs, clés SSH)
- **Élévation de privilèges** (si UID mapping non restreint)
- Injection de backdoors ou malwares
- Lecture de `/etc/passwd` ou fichiers confidentiels

### Commandes d'Énumération NFS

```bash
# Lister les exports NFS disponibles
showmount -e <IP_TARGET>

# Exemple de sortie :
# Export list for 192.168.1.100:
# /home         *
# /var/nfsshare 192.168.1.0/24
# /etc          *        ← DANGEREUX !

# Scanner les services RPC (dont NFS)
rpcinfo -p <IP_TARGET>

# Monter un partage NFS découvert
mkdir /tmp/nfs_mount
mount -t nfs <IP_TARGET>:/home /tmp/nfs_mount
ls -la /tmp/nfs_mount/

# Nmap NSE pour NFS
nmap -sV -p 111,2049 --script nfs-ls,nfs-showmount,nfs-statfs <IP_TARGET>

# Scanner port RPC (portmapper)
nmap -sV -p 111 --script rpcinfo <IP_TARGET>
```

### `rpcscan` — Outil CEH v13
```bash
# Scanner les services RPC
rpcscan <IP_TARGET>

# Informations détaillées RPC
rpcinfo -p <IP_TARGET>
```

---

## 🔵 RPC — Remote Procedure Call

### Fonctionnement RPC
```
Client                 RPC Endpoint Mapper       Serveur RPC
  │                    (Port 135)                     │
  │── "Quel port pour service X ?" ────────────→      │
  │← Réponse "Port YYYY" ──────────────────────       │
  │                                                   │
  │── Connexion directe port YYYY ───────────────────→│
  │← Résultat de la procédure ───────────────────────│
```

### Énumération RPC

```bash
# Linux - rpcinfo
rpcinfo -p <IP_TARGET>

# Résultat exemple :
#   program vers proto   port  service
#    100000    4   tcp    111  portmapper
#    100000    3   tcp    111  portmapper
#    100003    3   tcp   2049  nfs
#    100003    4   tcp   2049  nfs
#    100005    1   udp  32768  mountd

# Nmap - RPC et services associés
nmap -sV -p 111 --script rpcinfo <IP_TARGET>
nmap -sV --script msrpc-enum <IP_TARGET>

# Windows - Enumération RPC avec rpcdump
rpcdump.py <IP_TARGET>                  # impacket
```

### Vulnérabilités RPC Connues
| CVE | Description |
|-----|-------------|
| MS03-026 | Buffer overflow RPC/DCOM → vers Blaster |
| MS03-039 | Buffer overflow RPC → worm Nachi |
| CVE-2017-0143 | EternalBlue SMB → WannaCry |

---

## 🟢 FTP — File Transfer Protocol

### Fonctionnement FTP
```
Client FTP              Serveur FTP
    │                       │
    │── PORT 21 (contrôle) →│  (commandes)
    │── PORT 20 (données) ──│  (transferts)
    │                       │
    Mode Actif  : Serveur initie la connexion données
    Mode Passif : Client initie les deux connexions
```

### Accès Anonyme FTP

```bash
# Tenter une connexion anonyme
ftp <IP_TARGET>
# Login : anonymous
# Password : (vide ou email@email.com)

# Via nmap
nmap -p 21 --script ftp-anon <IP_TARGET>
# Résultat : Anonymous FTP login allowed

# Énumération des fichiers accessibles
ftp> ls -la
ftp> get fichier_sensible.txt
```

### Commandes FTP d'Énumération
```bash
# Connexion et exploration
ftp <IP_TARGET>
> USER anonymous
> PASS test@test.com
> LIST              # Lister les fichiers
> PASV              # Mode passif
> RETR fichier      # Télécharger

# Banner grabbing FTP
nc <IP_TARGET> 21
telnet <IP_TARGET> 21

# Nmap complet
nmap -sV -p 21 --script ftp-* <IP_TARGET>

# Brute force FTP
hydra -L users.txt -P passwords.txt ftp://<IP_TARGET>
medusa -H hosts.txt -u admin -P passwords.txt -M ftp
```

---

## 🟡 NTP — Network Time Protocol

### Rôle du NTP
NTP synchronise les horloges des systèmes — port **UDP 123**. Important car :
- Kerberos exige une synchronisation < 5 minutes
- Les logs forensiques nécessitent des horodatages cohérents

### Énumération NTP
```bash
# Lister les pairs NTP
ntpq -p <IP_TARGET>

# ntptrace — Tracer la hiérarchie NTP
ntptrace <IP_TARGET>

# Lister les clients connectés au serveur NTP
ntpdc -c monlist <IP_TARGET>    ← Peut révéler des IPs internes !

# Nmap
nmap -sU -p 123 --script ntp-info <IP_TARGET>
nmap -sU -p 123 --script ntp-monlist <IP_TARGET>
```

> ⚠️ La commande `monlist` révèle les 600 dernières adresses IP ayant contacté le serveur NTP — mine d'or pour la reconnaissance !

---

## 🔶 IPSec — IP Security

### Énumération IPSec / IKE
```bash
# Identifier les endpoints IPSec (UDP 500, 4500)
nmap -sU -p 500,4500 <IP_TARGET>

# ike-scan — Identifier les paramètres IKE
ike-scan <IP_TARGET>
ike-scan --aggressive <IP_TARGET>

# Résultat possible :
# Main Mode Handshake returned
# SA=(Enc=3DES Hash=MD5 Auth=PSK Group=2:modp1024 LifeType=Seconds LifeDuration=28800)
```

### Informations Extractibles
- Algorithmes de chiffrement supportés
- Type d'authentification (PSK / certificats)
- Mode IKE (Main / Aggressive)
- Version IKE (v1 / v2)

---

## 🟠 VoIP — Voice over IP

### Énumération VoIP (SIP)
```bash
# Port SIP : UDP/TCP 5060

# svmap — Scanner les équipements SIP
svmap <IP_RANGE>

# svwar — Énumérer les extensions SIP
svwar -e100-200 <IP_TARGET>

# Nmap SIP
nmap -sU -p 5060 --script sip-enum-users <IP_TARGET>
nmap -sU -p 5060 --script sip-methods <IP_TARGET>
```

---

## 🔵 BGP — Border Gateway Protocol

### Énumération BGP (CEH v13)
BGP gère le routage inter-AS sur Internet — port **TCP 179**

```bash
# Nmap BGP
nmap -p 179 --script bgp-open <IP_TARGET>

# Obtenir les routes BGP annoncées
# Via looking glass en ligne : bgp.he.net

# Whois BGP
whois -h whois.radb.net target.com
```

---

## 📋 Récapitulatif Ports

| Service | Port | Proto | Outil principal |
|---------|------|-------|-----------------|
| NFS | 2049 | TCP | showmount, rpcscan |
| RPC/Portmapper | 111 | TCP/UDP | rpcinfo, rpcdump |
| FTP | 21 | TCP | nmap ftp-*, hydra |
| NTP | 123 | UDP | ntpq, ntptrace |
| IPSec/IKE | 500, 4500 | UDP | ike-scan |
| SIP/VoIP | 5060 | TCP/UDP | svmap, svwar |
| BGP | 179 | TCP | nmap bgp-open |

---

## 🛡️ Contre-mesures

### NFS
- Restreindre les exports à des plages IP spécifiques
- Désactiver NFS si non nécessaire
- Utiliser NFSv4 avec Kerberos (auth obligatoire)
- Auditer `/etc/exports` régulièrement

### FTP
- Désactiver l'accès **anonyme FTP**
- Remplacer FTP par **SFTP** (SSH) ou **FTPS**
- Implémenter des ACL strictes
- Surveiller les tentatives de connexion

### NTP
- Désactiver la commande `monlist`
- Utiliser NTPv4 avec authentification
- Filtrer UDP 123 depuis l'extérieur

---

[← DNS & SMTP](./05-dns-smtp.md) | [Outils & Commandes →](./07-outils-commandes.md)
