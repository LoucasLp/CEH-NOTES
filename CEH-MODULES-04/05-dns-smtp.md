# 05 — Énumération DNS & SMTP

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## 🟣 DNS — Domain Name System

### Rappel du Fonctionnement DNS
```
Client                    Serveur DNS Récursif         Serveur Autoritaire
  │                              │                            │
  │── Requête "www.target.com" →│                            │
  │                              │── Requête ───────────────→│
  │                              │← Réponse (IP) ────────────│
  │← Réponse (IP) ──────────────│
  
UDP port 53 : Requêtes standard (< 512 octets)
TCP port 53 : Zone Transfer / Grandes requêtes
```

### Types d'Enregistrements DNS
| Type | Description | Exemple |
|------|-------------|---------|
| `A` | IPv4 → Nom de domaine | `target.com → 93.184.216.34` |
| `AAAA` | IPv6 → Nom de domaine | `target.com → 2606:2800::1` |
| `MX` | Serveur mail | `mail.target.com` |
| `NS` | Serveur de noms (nameserver) | `ns1.target.com` |
| `CNAME` | Alias vers un autre nom | `www → target.com` |
| `PTR` | IP → Nom (reverse DNS) | `34.216.184.93 → target.com` |
| `SOA` | Start of Authority | Infos sur la zone DNS |
| `TXT` | Texte libre | SPF, DKIM, vérifications |
| `SRV` | Service locator | `_ldap._tcp.dc._msdcs.domain` |

---

### 🎯 DNS Zone Transfer (AXFR)

Le **zone transfer** est une réplication des données DNS entre serveurs.
Si mal configuré, un attaquant peut récupérer **toute la zone DNS** :

```
Requête AXFR  →  Transfert de TOUS les enregistrements
                 Noms d'hôtes + IPs
                 Sous-domaines
                 Infrastructure interne
```

#### Via `nslookup`
```bash
# Méthode Windows/Linux
nslookup
> server ns1.target.com
> set type=any
> ls -d target.com

# Ou directement :
nslookup -type=any target.com ns1.target.com
```

#### Via `dig`
```bash
# Zone Transfer complet
dig axfr @ns1.target.com target.com

# Lister les nameservers
dig ns target.com

# Tous les enregistrements
dig any target.com @ns1.target.com

# Reverse DNS
dig -x 93.184.216.34

# Enregistrements MX
dig mx target.com

# Enregistrements SOA
dig soa target.com
```

#### Via `fierce`
```bash
# Découverte de sous-domaines
fierce --domain target.com

# Avec wordlist personnalisée
fierce --domain target.com --wordlist /usr/share/wordlists/dns.txt
```

#### Via `dnsenum`
```bash
# Énumération DNS complète
dnsenum target.com

# Avec zone transfer
dnsenum --dnsserver ns1.target.com target.com

# Brute force de sous-domaines
dnsenum --enum -f /usr/share/wordlists/subdomains.txt target.com
```

#### Via `dnsrecon`
```bash
# Énumération standard
dnsrecon -d target.com

# Zone transfer
dnsrecon -d target.com -t axfr

# Brute force sous-domaines
dnsrecon -d target.com -t brt -D /usr/share/wordlists/subdomains.txt

# Reverse lookup d'une plage
dnsrecon -r 192.168.1.0/24
```

---

### Subfinder — Découverte de Sous-domaines (CEH v13)
```bash
# Découverte passive de sous-domaines
subfinder -d target.com

# Avec sortie détaillée
subfinder -d target.com -v

# Résoudre les IPs
subfinder -d target.com -resolve

# Sortie dans un fichier
subfinder -d target.com -o results.txt
```

---

### 🔍 Exemple de Résultat Zone Transfer
```
; Zone Transfer for target.com
$ORIGIN target.com.
@       3600    SOA   ns1.target.com. admin.target.com. (
                      2024010101 ; Serial
                      3600       ; Refresh
                      900        ; Retry
                      604800     ; Expire
                      300 )      ; Minimum TTL

; Nameservers
        NS    ns1.target.com.
        NS    ns2.target.com.

; A Records
@       A     93.184.216.34
www     A     93.184.216.34
mail    A     93.184.216.10
ftp     A     93.184.216.11
vpn     A     93.184.216.12          ← Infrastructure critique !
admin   A     192.168.1.1            ← IP interne exposée !
```

---

## 🟢 SMTP — Simple Mail Transfer Protocol

### Fonctionnement SMTP
```
Expéditeur           Serveur SMTP Envoi        Serveur SMTP Réception
    │                       │                         │
    │── HELO/EHLO ─────────→│                         │
    │← 250 OK ──────────────│                         │
    │── MAIL FROM ─────────→│                         │
    │← 250 OK ──────────────│                         │
    │── RCPT TO ───────────→│                         │
    │← 250 OK / 550 ERROR ──│ ← Révèle si user existe!│
    │── DATA ──────────────→│                         │
    │── [message] ─────────→│                         │
    │── QUIT ──────────────→│────── Relay ───────────→│
```

### Commandes SMTP d'Énumération

| Commande | Usage | Réponse si valide |
|----------|-------|-------------------|
| `VRFY user` | Vérifie si l'utilisateur existe | `250 user@domain.com` |
| `EXPN list` | Développe une liste de diffusion | `250 user1, user2...` |
| `RCPT TO:<user>` | Tente d'envoyer à un destinataire | `250 OK` |

### Exemple d'Énumération Manuelle via Telnet
```bash
# Connexion au serveur SMTP
telnet mail.target.com 25

# Réponse attendue :
220 mail.target.com ESMTP Postfix

# Identification
EHLO test.local

# Vérification d'un utilisateur (VRFY)
VRFY admin
250 2.1.5 admin <admin@target.com>    ← utilisateur valide !

VRFY nobody
550 5.1.1 nobody... User unknown      ← utilisateur invalide

# Développer une liste
EXPN helpdesk
250 2.1.5 <alice@target.com>
250 2.1.5 <bob@target.com>

# Tenter RCPT TO
MAIL FROM:<test@test.com>
250 OK
RCPT TO:<admin@target.com>
250 OK                                ← utilisateur existe !

QUIT
```

---

### 🛠️ Outils d'Énumération SMTP

#### `smtp-user-enum` (Kali Linux)
```bash
# Brute force utilisateurs via VRFY
smtp-user-enum -M VRFY -U /usr/share/wordlists/users.txt \
  -t mail.target.com

# Via RCPT TO
smtp-user-enum -M RCPT -U /usr/share/wordlists/users.txt \
  -t mail.target.com -f test@test.com

# Via EXPN
smtp-user-enum -M EXPN -U /usr/share/wordlists/users.txt \
  -t mail.target.com
```

#### Nmap Scripts SMTP
```bash
# Informations bannière SMTP
nmap -p 25 --script smtp-commands <IP_TARGET>

# Énumération utilisateurs
nmap -p 25 --script smtp-enum-users \
  --script-args smtp-enum-users.methods={VRFY,EXPN,RCPT} \
  <IP_TARGET>

# Vérification si relay ouvert
nmap -p 25 --script smtp-open-relay <IP_TARGET>

# Informations NTLM
nmap -p 25 --script smtp-ntlm-info <IP_TARGET>
```

#### Metasploit
```bash
# Module d'énumération SMTP
use auxiliary/scanner/smtp/smtp_enum
set RHOSTS <IP_TARGET>
set USER_FILE /usr/share/wordlists/users.txt
run
```

---

## 📊 Comparatif DNS vs SMTP

| Critère | DNS | SMTP |
|---------|-----|------|
| **Port** | 53 TCP/UDP | 25 TCP |
| **Données** | Infrastructure réseau | Comptes utilisateurs |
| **Technique principale** | Zone Transfer (AXFR) | VRFY / EXPN / RCPT TO |
| **Outils** | dig, dnsenum, dnsrecon | smtp-user-enum, nmap |
| **Risque** | Cartographie complète | Énumération des emails |

---

## 🛡️ Contre-mesures

### DNS
- Restreindre le **zone transfer** aux seuls serveurs DNS secondaires autorisés
- Configurer `allow-transfer` dans BIND :
  ```
  zone "target.com" {
    allow-transfer { 192.168.1.2; };  // Seulement le DNS secondaire
  };
  ```
- Utiliser des **vues DNS** pour séparer les zones internes/externes (split-horizon)
- Monitorer les requêtes AXFR dans les logs

### SMTP
- Désactiver les commandes **VRFY** et **EXPN** :
  ```
  # Postfix
  disable_vrfy_command = yes
  
  # Sendmail
  O PrivacyOptions=goaway
  ```
- Implémenter un **rate limiting** sur le port 25
- Activer l'authentification SMTP (SMTP AUTH)
- Utiliser des réponses génériques pour les erreurs

---

[← SNMP & LDAP](./04-snmp-ldap.md) | [NFS · RPC · FTP →](./06-nfs-rpc-ftp.md)
