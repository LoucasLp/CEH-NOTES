# 📧 07 — Énumération SMTP & DNS

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

# PARTIE 1 — SMTP Enumeration

## 📖 Présentation de SMTP

**SMTP** (Simple Mail Transfer Protocol) est le protocole TCP/IP de distribution d'emails.

| Attribut | Valeur |
|----------|--------|
| **Ports** | TCP **25**, **2525**, **587** |
| **Protocole** | TCP (connexion orientée) |
| **Combiné avec** | POP3 et IMAP |
| **Routage** | Via serveurs MX (via DNS) |

---

## 🔑 Trois commandes SMTP exploitables

### VRFY — Valide l'existence d'un utilisateur
```
$ telnet 192.168.168.1 25
220 NYmailserver ESMTP Sendmail 8.9.3
HELO x
250 NYmailserver Hello [10.0.0.86], pleased to meet you
VRFY Jonathan
250 Super-User <Jonathan@NYmailserver>    ← ✅ Utilisateur valide
VRFY Smith
550 Smith ... User unknown                 ← ❌ Utilisateur inexistant
```

---

### EXPN — Révèle les adresses de livraison réelles (aliases)
```
$ telnet 192.168.168.1 25
220 NYmailserver ESMTP Sendmail 8.9.3
HELO x
250 NYmailserver Hello [10.0.0.86], pleased to meet you
EXPN Jonathan
250 Super-User <Jonathan@NYmailserver>    ← ✅ Alias valide
EXPN Smith
550 Smith ... User unknown                 ← ❌ Alias inexistant
```

---

### RCPT TO — Définit les destinataires
```
$ telnet 192.168.168.1 25
HELO x
MAIL FROM: Jonathan
250 Jonathan ... Sender ok
RCPT TO: Ryder
250 Ryder ... Recipient ok               ← ✅ Destinataire valide
RCPT TO: Smith
550 Smith ... User unknown               ← ❌ Destinataire inexistant
```

---

## 💻 Énumération SMTP avec Nmap

```bash
# Lister toutes les commandes SMTP disponibles
nmap -p 25,365,587 --script=smtp-commands <Target IP>

# Identifier les relais SMTP ouverts
nmap -p 25 --script=smtp-open-relay <Target IP>

# Énumérer tous les utilisateurs mail
nmap -p 25 --script=smtp-enum-users <Target IP>
```

---

## 💻 Énumération SMTP avec Metasploit

### Étapes

**1. Lancer msfconsole et charger le module**
```bash
msf > use auxiliary/scanner/smtp/smtp_enum
msf auxiliary(smtp_enum) >
```

**2. Voir les options disponibles**
```bash
msf auxiliary(smtp_enum) > show options
# ou pour l'évasion :
msf auxiliary(smtp_enum) > show evasion
```

**3. Configurer la cible**
```bash
msf auxiliary(smtp_enum) > set RHOSTS <Target IP>
# ou plage d'IPs :
msf auxiliary(smtp_enum) > set RHOSTS 192.168.1.0/24
```

**4. Configurer la wordlist (optionnel)**
```bash
# Par défaut : /usr/share/metasploit-framework/data/wordlists/unix_users.txt
msf auxiliary(smtp_enum) > set USER_FILE /path/to/wordlist.txt
```

**5. Options avancées**
```bash
msf auxiliary(smtp_enum) > show advanced
```

**6. Lancer l'énumération**
```bash
msf auxiliary(smtp_enum) > run
# → Liste des utilisateurs SMTP valides
```

---

## 🛠️ Outils SMTP

### NetScanTools Pro
> Source : https://www.netscantools.com

- Teste l'envoi d'email via un serveur SMTP
- Extrait tous les paramètres d'en-tête email (flags confirm/urgent)
- Enregistre les sessions dans un fichier log

### smtp-user-enum
> Source : https://pentestmonkey.net

Outil d'énumération des comptes utilisateurs OS via SMTP (Solaris/sendmail).

```bash
# Syntaxe
smtp-user-enum.pl [options] (-u username|-U file) (-t host|-T file)
```

**Options principales :**

| Option | Description |
|--------|-------------|
| `-M mode` | Commande SMTP : EXPN, VRFY, RCPT TO (défaut: VRFY) |
| `-u user` | Vérifier un utilisateur spécifique |
| `-U file` | Fichier contenant la liste d'utilisateurs |
| `-t host` | Serveur SMTP cible |
| `-T file` | Fichier de serveurs SMTP cibles |
| `-p port` | Port TCP SMTP (défaut: 25) |
| `-f addr` | Adresse FROM pour "RCPT TO" (défaut: user@example.com) |
| `-D dom` | Domaine à ajouter aux usernames |
| `-m n` | Nombre max de processus (défaut: 5) |
| `-d` | Sortie de débogage |
| `-v` | Verbose |

---

---

# PARTIE 2 — DNS Enumeration

## 📖 DNS Zone Transfer

Le **transfert de zone DNS** copie le fichier de zone DNS du serveur primaire vers les secondaires.

**Objectif légitime** : Redondance et sauvegarde.  
**Objectif malveillant** : Si mal configuré → attaquant obtient **toute la base DNS** !

### Informations obtenues
- Noms de serveurs DNS
- Hostnames et noms de machines
- Noms d'utilisateurs
- Adresses IP
- Aliases

---

## 💻 Outils DNS Zone Transfer

### dig (Linux)
```bash
# Obtenir les serveurs de noms du domaine cible
dig ns <target domain>

# Tenter un transfert de zone (axfr)
dig @<nameserver_domain> <target domain> axfr

# Exemple complet
dig ns certifiedhacker.com
dig @ns1.certifiedhacker.com certifiedhacker.com axfr
```

### nslookup (Windows)
```bash
# Définir le type de requête SOA
nslookup set querytype=soa <target domain>

# Tenter le transfert de zone
/ls -d <nameserver_domain>
```

### DNSRecon
```bash
# Tester tous les NS pour les transferts de zone
dnsrecon -t axfr -d <target domain>
# -t : type d'énumération (axfr = test zone transfer sur tous les NS)
# -d : domaine cible
```

---

## 🔍 DNS Cache Snooping

L'attaquant interroge le serveur DNS pour un **enregistrement DNS caché spécifique** afin de déterminer les sites récemment visités.

### Informations révélées
- Sites visités récemment
- Fournisseur de services
- Informations bancaires potentielles
- Données exploitables pour ingénierie sociale

### Méthode 1 — Non-récursive (RD bit = 0)
```bash
dig @<IP_DNS> <target_domain> A +norecurse
```
- Si `NOERROR` sans réponse → **aucun utilisateur n'a visité ce site**
- Si enregistrement présent → **quelqu'un a visité ce site**

### Méthode 2 — Récursive
```bash
dig @<IP_DNS> <target_domain> A +recurse
```
- Compare le TTL obtenu avec le TTL initial
- **TTL résultat < TTL initial** → enregistrement en cache → site visité
- **TTL élevé** → enregistrement PAS en cache → site non visité récemment

---

## 🔒 DNSSEC Zone Walking

**DNSSEC** (Domain Name System Security Extensions) ajoute des signatures numériques basées sur la cryptographie à clé publique.

### Vulnérabilité : Zone Enumeration / Zone Walking
Si la zone DNS n'est pas correctement configurée, un attaquant peut obtenir les **enregistrements internes**.

```
DNSSEC Zone Walking →
├── Obtention des enregistrements internes
├── Construction de la carte réseau
└── Lancement d'attaques Internet ciblées
```

### Solution : NSEC3
**NSEC3** (Next Secure version 3) : fournit les mêmes fonctionnalités que NSEC mais avec des **noms d'enregistrements hachés cryptographiquement** pour prévenir l'énumération.

---

## 💻 Outils DNSSEC Zone Walking

### LDNS (ldns-walk)
```bash
# Énumérer la zone DNSSEC du domaine iana.org via DNS 8.8.8.8
ldns-walk @8.8.8.8 iana.org
```

### DNSRecon
```bash
# Énumération de zone DNSSEC
dnsrecon -d <target domain> -z
```

---

## 💻 DNS Enumeration avec Nmap

```bash
# Lister tous les services disponibles
nmap --script=broadcast-dns-service-discovery <Target Domain>

# Brute-force des sous-domaines + IPs
nmap -T4 -p 53 --script dns-brute <Target Domain>

# Vérifier si la récursion DNS est activée
nmap -Pn -sU -p 53 --script=dns-recursion 192.168.1.150
```

### DNSSEC avec Nmap
```bash
# Énumérer les sous-domaines via NSEC
nmap -sU -p 53 --script dns-nsec-enum \
  --script-args dns-nsec-enum.domains=eccouncil.org <target>
```

---

## 🤖 DNS Enumeration assistée par IA (CEH v13)

Les attaquants utilisent des outils IA (comme ChatGPT) pour générer des commandes d'énumération automatisées :

**Prompt exemple :**
> "Use Nmap to perform DNS Enumeration on target domain www.certifiedhacker.com"

**Commande générée :**
```bash
# DNS brute-force + sauvegarde des résultats
nmap --script dns-brute \
  --script-args dns-brute.domain=certifiedhacker.com \
  -oN ~/enumeration_results/dns_brute_certifiedhacker.txt \
&& \
# DNS zone transfer + sauvegarde
nmap --script dns-zone-transfer \
  -p 53 certifiedhacker.com \
  -oN ~/enumeration_results/dns_zonetransfer_certifiedhacker.txt
```

---

## 🛠️ Outils OWASP Amass

```bash
# Énumération passive
amass enum -passive -d <Target Domain> -src

# Énumération active avec brute-force
amass enum -active -d <Target Domain> \
  -brute -w /usr/share/wordlists/amass/all.txt

# Comparer les 2 derniers scans
amass track -config /root/amass/config.ini \
  -dir amass4owasp -d <Target Domain> -last 2

# Afficher les résultats de la base de données
amass db -dir amass4owasp -list

# Générer un graphe HTML d3-force
amass viz -d3 -dir amass4owasp
```

### Autres outils DNS

| Outil | URL |
|-------|-----|
| Knock | https://github.com |
| Raccoon | https://github.com |
| Subfinder | https://github.com |
| Turbolist3r | https://github.com |

---

## 🎯 Synthèse SMTP & DNS

| Protocole | Commandes clés | Risque |
|-----------|---------------|--------|
| **SMTP** | VRFY, EXPN, RCPT TO | Enumération d'utilisateurs valides |
| **DNS Zone Transfer** | dig axfr, dnsrecon -t axfr | Dump complet de la base DNS |
| **DNS Cache Snooping** | dig +norecurse / +recurse | Sites visités, ingénierie sociale |
| **DNSSEC Zone Walking** | ldns-walk, dnsrecon -z | Cartographie réseau interne |

---

[← NTP & NFS](./06-ntp-nfs-enumeration.md) | [→ Autres Enumerations](./08-other-enumeration.md)
