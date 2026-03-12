# 📚 CEH v13 — Module 4 : Énumération (Enumeration)

> **Certified Ethical Hacker v13** | Notes de cours complètes  
> 📅 Dernière mise à jour : Mars 2026

---

## 🗂️ Table des matières

| # | Fichier | Contenu |
|---|---------|---------|
| 01 | [Introduction & Techniques](./01-introduction.md) | Définition, objectifs, techniques d'énumération |
| 02 | [Ports & Services](./02-ports-services.md) | TCP/UDP, protocoles et ports clés |
| 03 | [NetBIOS Enumeration](./03-netbios-enumeration.md) | NetBIOS, Nbtstat, outils |
| 04 | [SNMP Enumeration](./04-snmp-enumeration.md) | SNMP, MIB, SnmpWalk, outils |
| 05 | [LDAP Enumeration](./05-ldap-enumeration.md) | LDAP, AD, ldapsearch, outils |
| 06 | [NTP & NFS Enumeration](./06-ntp-nfs-enumeration.md) | NTP commands, NFS, RPCScan |
| 07 | [SMTP & DNS Enumeration](./07-smtp-dns-enumeration.md) | SMTP, DNS zone transfer, cache snooping, DNSSEC |
| 08 | [IPsec, VoIP, RPC, Linux](./08-other-enumeration.md) | IPsec/IKE, VoIP/SIP, RPC, Unix/Linux, SMB |
| 09 | [Countermeasures](./09-countermeasures.md) | Contre-mesures par protocole |

---

## 🎯 Objectifs du module

À la fin de ce module, vous serez capable de :

- ✅ Définir l'énumération et ses techniques
- ✅ Identifier les services et ports vulnérables à l'énumération
- ✅ Utiliser les outils d'énumération (Nmap, Nbtstat, SnmpWalk, ldapsearch…)
- ✅ Effectuer une énumération NetBIOS, SNMP, LDAP, NTP, NFS, SMTP, DNS
- ✅ Appliquer les contre-mesures appropriées

---

## 🔑 Concepts clés à retenir

```
Énumération = Connexions ACTIVES avec la cible (≠ scanning passif)
```

| Concept | Définition rapide |
|---------|------------------|
| **Enumeration** | Extraction active d'infos : users, shares, services, routing tables |
| **NetBIOS** | Protocole Windows de partage de fichiers/imprimantes (ports 137-139) |
| **SNMP** | Gestion réseau à distance — community strings = mots de passe |
| **LDAP** | Accès aux annuaires (Active Directory) — port 389 |
| **DNS Zone Transfer** | Copie complète de la base DNS — vecteur d'attaque majeur |
| **MIB** | Base de données virtuelle des objets gérés par SNMP |

---

## ⚡ Commandes rapides (Cheat Sheet)

```bash
# NetBIOS
nbtstat -a <IP>                          # Name table distante
nmap -sV --script nbstat.nse <IP>        # NetBIOS via Nmap

# SNMP
snmpwalk -v2c -c public <IP>             # Enumération SNMP
nmap -sU -p 161 --script=snmp-processes <IP>

# LDAP
ldapsearch -h <IP> -x -b "DC=domain,DC=local"
nmap -p 389 --script ldap-brute <IP>

# DNS Zone Transfer
dig ns <domain>
dig @<nameserver> <domain> axfr
dnsrecon -t axfr -d <domain>

# SMTP
nmap -p 25 --script=smtp-enum-users <IP>
smtp-user-enum.pl -M VRFY -U users.txt -t <IP>

# SMB
nmap -p 445 -A <IP>
nmap -p 445 --script smb-protocols <IP>
```

---

## 📊 Vue d'ensemble des ports

| Port | Protocole | Service |
|------|-----------|---------|
| 21/22 | TCP | FTP / SSH-SFTP |
| 23 | TCP | Telnet |
| 25 | TCP | SMTP |
| 53 | TCP/UDP | DNS |
| 69 | UDP | TFTP |
| 123 | UDP | NTP |
| 135 | TCP/UDP | RPC Endpoint Mapper |
| 137 | UDP | NetBIOS Name Service |
| 139 | TCP | NetBIOS Session (SMB) |
| 161/162 | UDP | SNMP / SNMP Trap |
| 179 | TCP | BGP |
| 389 | TCP/UDP | LDAP |
| 445 | TCP/UDP | SMB Direct |
| 500 | UDP | IKE/ISAKMP |
| 2049 | TCP | NFS |
| 3268 | TCP/UDP | Global Catalog |
| 5060/5061 | TCP/UDP | SIP (VoIP) |

---

> 💡 **Rappel éthique** : L'énumération sans autorisation est **illégale**. Toujours obtenir une autorisation écrite avant tout test de pénétration.
