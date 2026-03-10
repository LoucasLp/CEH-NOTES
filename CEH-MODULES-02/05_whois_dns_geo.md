# 📌 05 — WHOIS, DNS & Géolocalisation IP

> **Module 2 — Footprinting & Reconnaissance** | CEH v13

---

## 🔎 WHOIS Lookup

### Qu'est-ce que WHOIS ?

Protocole de **requête/réponse** (port TCP 43) pour interroger les bases de données des registres Internet. Il fournit des informations sur les propriétaires de domaines, plages IP et systèmes autonomes.

### Les 3 Modèles de Stockage WHOIS

| Modèle | Description |
|--------|-------------|
| **Thick WHOIS** (Distributed) | Toutes les infos WHOIS de tous les registrars centralisées |
| **Thin WHOIS** (Centralized) | Seul le nom du serveur WHOIS du registrar est stocké |
| **Decentralized WHOIS** | Infos complètes gérées par plusieurs entités indépendantes |

---

### Informations retournées par une requête WHOIS

```
┌──────────────────────────────────────────────────────────┐
│  WHOIS RESPONSE — example.com                            │
├──────────────────────────────────────────────────────────┤
│  Domain name          : example.com                      │
│  Registrar            : GoDaddy / Namecheap / etc.       │
│  Owner contact        : Nom, adresse, email, téléphone   │
│  Name Servers (NS)    : ns1.example.com                  │
│  NetRange             : 93.184.216.0 - 93.184.216.255    │
│  Creation date        : 1995-08-13                       │
│  Expiry date          : 2025-08-13                       │
│  Last updated         : 2023-01-15                       │
│  Domain status        : clientTransferProhibited         │
│  IP address info      : 93.184.216.34                    │
└──────────────────────────────────────────────────────────┘
```

---

### 🌍 Regional Internet Registries (RIRs)

| RIR | Zone géographique | URL |
|-----|-------------------|-----|
| **ARIN** | Amérique du Nord | arin.net |
| **AFRINIC** | Afrique | afrinic.net |
| **APNIC** | Asie-Pacifique | apnic.net |
| **RIPE NCC** | Europe, Moyen-Orient, Asie centrale | ripe.net |
| **LACNIC** | Amérique Latine, Caraïbes | lacnic.net |

---

### Outils WHOIS

| Outil | URL |
|-------|-----|
| DomainTools WHOIS | whois.domaintools.com |
| Tamos | tamos.com |
| WHOIS Domain Lookup | – |
| Active Whois | – |
| Batch IP Converter | sabsoft.com (supporte IPv6 et IDN) |

---

## 🌐 DNS Footprinting

### Pourquoi le DNS Footprinting ?

```
WHOIS → Informations sur le domaine
     ↓
DNS Footprinting → Cartographie complète des hôtes du réseau
```

Le DNS Footprinting révèle : noms de domaine, noms d'hôtes, adresses IP, serveurs présents dans le réseau cible.

---

### 📋 Types d'Enregistrements DNS

| Type | Description |
|------|-------------|
| **A** | Pointe vers l'adresse IPv4 d'un hôte |
| **AAAA** | Pointe vers l'adresse IPv6 d'un hôte |
| **MX** | Serveur de messagerie du domaine |
| **NS** | Serveur de noms (Name Server) |
| **CNAME** | Alias canonique vers un hôte |
| **SOA** | Autorité sur un domaine (Start of Authority) |
| **SRV** | Enregistrements de service |
| **PTR** | Résolution inverse : IP → Hostname |
| **RP** | Personne responsable |
| **HINFO** | Informations hôte (type CPU + OS) |
| **TXT** | Enregistrements texte non structurés |

---

### 🛠️ Outils d'Interrogation DNS

| Outil | Source | Fonctionnalités |
|-------|--------|-----------------|
| **SecurityTrails** | securitytrails.com | Enregistrements DNS historiques + actuels, brute-force sous-domaines |
| **Fierce** | github.com | Reconnaissance DNS, sous-domaines, espaces IP non-contigus |
| **DNSChecker** | – | Vérification multi-serveurs |
| **zdns** | – | Requêtes DNS haute performance |
| **DNSdumpster** | dnsdumpster.com | Cartographie DNS visuelle |

---

### Commandes Fierce

```bash
# Scan basique
fierce --domain certifiedhacker.com

# Scan de sous-domaines spécifiques
fierce --domain certifiedhacker.com --subdomains write admin mail

# Scanner les domaines proches (range de 10)
fierce --domain certifiedhacker.com --subdomains mail --traverse 10

# Tenter une connexion HTTP sur les domaines découverts
fierce --domain certifiedhacker.com --subdomains mail --connect

# Scan complet
fierce --domain certifiedhacker.com --wide
```

---

### DNS Lookup avec IA

```bash
# shellGPT — Installer et utiliser DNSRecon
sgpt --chat dns --shell "Install and use DNSRecon to perform DNS enumeration on www.certifiedhacker.com"

# Commande générée :
sudo apt-get update && sudo apt-get install -y dnsrecon && \
  dnsrecon -d certifiedhacker.com -t std
```

---

### 🔄 Reverse DNS Lookup

**DNS normal :** Nom de domaine → Adresse IP  
**Reverse DNS :** Adresse IP → Nom de domaine (via enregistrements PTR)

```bash
# DNSRecon — Reverse lookup sur plage IP
dnsrecon -r 162.241.216.0-162.241.216.255
# (-r = range d'IPs pour reverse lookup par brute force)

# MXToolbox Reverse Lookup
# https://mxtoolbox.com → Reverse Lookup
```

**Outils :**
- DNSRecon
- Reverse Lookup (mxtoolbox.com)
- puredns
- Reverse IP Domain Check
- Reverse IP Lookup

---

## 📍 Géolocalisation IP

### Informations obtenues

```
IP Address → Country → Region/State → City
          → Latitude/Longitude → ZIP Code
          → Timezone → Connection speed
          → ISP (hosting company) → Domain name
          → IDD country code → Area code
          → Weather station → Mobile carrier
          → Elevation
```

### Exploitation des informations de géolocalisation

```
Géolocalisation IP obtenue →
  ├── Social engineering ciblé (phishing régional)
  ├── Serveur compromis proche de la victime
  ├── Malware spécifique à la région
  └── Surveillance physique / dumpster diving
```

### Outils de Géolocalisation IP

| Outil | Source | Informations |
|-------|--------|-------------|
| **IP2Location** | ip2location.com | Géolocalisation complète, ISP, carrier mobile |
| **IP Location Finder** | – | Localisation sur carte |
| **IP Address Geographical Location Finder** | – | Localisation détaillée |

---

### Détermination de l'OS (OS Fingerprinting)

> Technique : interroger des outils en ligne pour identifier l'OS des serveurs de la cible

| Outil | Usage |
|-------|-------|
| **Netcraft** | Identification OS + technologies (What's that site running?) |
| **Shodan** | OS, IP, protocoles, localisation des devices |
| **Censys** | Infrastructure complète, tous appareils exposés |

---

## 🔗 LinkedIn & Réseaux Sociaux — Footprinting

### theHarvester — Énumération LinkedIn

```bash
# Trouver les employés d'une organisation
theHarvester -d microsoft -l 200 -b linkedin

# Extraire les emails d'un domaine via Baidu
theHarvester -d microsoft.com -l 200 -b baidu

# Version complète avec sortie XML
theHarvester -d microsoft.com -l 200 -b Baidu -f Microsoft_emails.xml
```

**Paramètres :**
- `-d` → Domaine / nom de l'entreprise
- `-l` → Limite du nombre de résultats
- `-b` → Source de données (linkedin, baidu, google, bing...)
- `-f` → Fichier de sortie (XML)

---

### Sherlock — Recherche Multi-Plateformes

```bash
# Trouver tous les profils d'un utilisateur
sherlock SundarPichai --output recon2
```

Sherlock recherche un **username** sur des centaines de réseaux sociaux et retourne les URLs des profils trouvés.

---

### Social Searcher

> [https://www.social-searcher.com](https://www.social-searcher.com)

- Recherche en temps réel sur les réseaux sociaux
- Analytics approfondies sur les profils
- Tracking d'utilisateurs, URLs complètes

---

### BuzzSumo — Analyse de Contenu

> [https://buzzsumo.com](https://buzzsumo.com)

- Contenu le plus partagé sur un sujet/domaine
- Activité sur Twitter, Facebook, LinkedIn, Pinterest
- Identification des influenceurs liés à la cible

---

> ⬅️ [04 - Services Internet](./04_internet_research.md) | ➡️ [06 - Réseau, Email & Social](./06_network_email_social.md)
