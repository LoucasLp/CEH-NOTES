# 📌 03 — Footprinting via Moteurs de Recherche

> **Module 2 — Footprinting & Reconnaissance** | CEH v13

---

## 🔎 Google Hacking (Google Dorking)

Les **Google Dorks** sont des opérateurs de recherche avancés permettant de trouver des informations sensibles non accessibles via une recherche normale.

> 📌 Source : [https://www.googleguide.com](https://www.googleguide.com)

---

### 🧰 Opérateurs Google Avancés

| Opérateur | Description | Exemple |
|-----------|-------------|---------|
| `site:` | Résultats limités à un site/domaine | `games site:certifiedhacker.com` |
| `allinurl:` | Tous les termes dans l'URL | `allinurl: google career` |
| `inurl:` | Un terme dans l'URL | `inurl:copy site:www.google.com` |
| `intext:` | Terme dans le corps de la page | `intext:"vpn configuration"` |
| `allintitle:` | Tous les termes dans le titre | `allintitle: detect malware` |
| `intitle:` | Terme dans le titre | `malware detection intitle:help` |
| `inanchor:` | Terme dans le texte d'ancre | `Anti-virus inanchor:Norton` |
| `allinanchor:` | Tous les termes dans l'ancre | `allinanchor: best cloud service provider` |
| `cache:` | Version en cache Google | `cache:www.eff.org` |
| `link:` | Pages pointant vers une URL | `link:www.googleguide.com` |
| `related:` | Sites similaires à une URL | `related:www.microsoft.com` |
| `info:` | Infos sur une page web | `info:gothotel.com` |
| `filetype:` | Résultats par extension | `jasmine:jpg` |
| `source:` | Source dans Google News | `Malware news source:"Hacker News"` |
| `before:` | Contenu publié avant une date | `ransomware before:2020-06-29` |
| `after:` | Contenu publié après une date | `site:wikipedia.org after:2023-01-01 AI` |
| `phonebook:` | Numéros de téléphone | `phonebook:Sundar Pichai` |
| `location:` | Infos sur un lieu | `location: 4 seasons restaurant` |

---

### 🗄️ Google Hacking Database (GHDB)

Base de données de dorks classés par catégorie sur [exploit-db.com/google-hacking-database](https://www.exploit-db.com/google-hacking-database)

| Catégorie | Description |
|-----------|-------------|
| Footholds | Points d'entrée dans des systèmes |
| Files Containing Usernames | Fichiers exposant des noms d'utilisateurs |
| Sensitive Directories | Répertoires sensibles accessibles |
| Web Server Detection | Identification du type de serveur web |
| Vulnerable Files | Fichiers vulnérables exposés |
| Vulnerable Servers | Serveurs avec des failles connues |
| Error Messages | Messages d'erreur révélateurs |
| Files Containing Juicy Info | Informations sensibles dans des fichiers |
| Files Containing Passwords | Fichiers contenant des mots de passe |
| Pages Containing Login Portals | Portails de connexion exposés |
| Network or Vulnerability Data | Données réseau et vulnérabilités |
| Various Online Devices | Équipements en ligne (caméras, routeurs...) |

---

### 🔐 Dorks VPN — Exemples Pratiques

| Google Dork | Description |
|-------------|-------------|
| `inurl:"/sslvpn_logon.shtml" intitle:"User Authentication" "WatchGuard Technologies"` | Pages de connexion WatchGuard |
| `inurl:/sslvpn/Login/Login` | Portails VPN SSL |
| `site:vpn.*.*/intitle:"login"` | Pages login VPN génériques |
| `intext:Please Login SSL VPN inurl:remote/login intext:FortiClient` | Pages Fortinet VPN |
| `site:vpn.*.*/intext:"login" intitle:"login"` | Pages VPN variées |
| `intitle:"index of" /etc/openvpn/` | Répertoires OpenVPN sensibles |
| `"-----BEGIN OpenVPN Static key V1-----" ext:key` | Clés statiques OpenVPN |
| `intitle:"SSL VPN Service" + intext:"Your system administrator..."` | Pages Cisco ASA |

---

### 🛡️ Dorks FTP — Exemples Pratiques

| Google Dork | Description |
|-------------|-------------|
| `site:.in \| .com \| .net intitle:"index of" ftp` | Fichiers FTP avec infos juteuses |
| `intitle:"Index of ftp passwords"` | Fichiers contenant des mots de passe FTP |
| `inurl:/ftp intitle:"office"` | Détection de serveur web |
| `intitle:"Index of" ws_ftp.ini` | Fichier ws_ftp.ini (users/pass FTP) |
| `allintitle:"CrushFTP WebInterface"` | Interface CrushFTP (login + reset) |
| `"ws_ftp.log" ext:log` | Répertoires sensibles |
| `"index of" /ftp/logs` | Fichiers de logs potentiels |

---

### 🤖 VPN Footprinting avec IA (shellGPT)

```bash
# Exemple avec shellGPT pour trouver des pages Fortinet VPN
sgpt --chat footprint --shell "Use inurl search operator to obtain the Fortinet VPN login pages"

# Commande générée automatiquement :
lynx --dump "http://www.google.com/search?q=inurl:%22remote+login%22+fortinet+OR+fortigate+OR+%22ssl+vpn%22" \
  | grep "http" | cut -d "=" -f2 | grep -o "http[^&]*"
```

> 💡 L'IA automatise la génération de dorks et l'extraction des résultats via des outils CLI comme `lynx`.

---

## 📡 SHODAN — Moteur de Recherche IoT

> 📌 Source : [https://www.shodan.io](https://www.shodan.io)

**Shodan** indexe les **appareils et services connectés à Internet** (serveurs, routeurs, caméras IP, systèmes SCADA, etc.).

### Utilisations en footprinting

- 🔎 Recherche de systèmes VPN et VoIP exposés
- 🏭 Accès aux systèmes SCADA / ICS
- 📷 Caméras CCTV sans authentification
- 🔌 Appareils IoT avec credentials par défaut

```
Shodan Query Examples :
  port:22 country:FR          → Serveurs SSH en France
  os:"Windows Server 2019"    → Serveurs Windows exposés
  product:nginx               → Serveurs Nginx
  "default password"          → Systèmes avec mot de passe par défaut
```

---

## 📹 Autres Techniques de Recherche

### Recherche d'Images (Reverse Image Search)

| Outil | Usage |
|-------|-------|
| Google Images / Google Lens | Recherche inversée par image |
| TinEye | Trouver l'origine d'une image |
| Yahoo Image Search | Alternative Google |
| Bing Image Search | Source Microsoft |
| Pinterest Reverse Image | Recherche visuelle |

> **Reverse Image Search** → Identifier localisation, employés, infrastructure physique de la cible

---

### Moteurs de Recherche Vidéo

- YouTube, Google Videos, Yahoo Videos, Bing Videos
- Outils d'analyse : **YouTube Metadata**, **EZGif**, **VideoReverser.com**
- Extraction d'informations cachées : date/heure, miniatures, métadonnées

---

### Meta Search Engines

> Interrogent plusieurs moteurs simultanément

| Outil | Particularité |
|-------|--------------|
| **Startpage** | Anonymat (cache l'IP utilisateur) |
| **MetaGer** | Open source, résultats agrégés |
| **eTools.ch** | Comparaison multi-moteurs |

---

### 🌐 Moteurs de Recherche IoT

| Outil | Spécialité |
|-------|-----------|
| **Shodan** | Appareils, services, OS, ports ouverts |
| **Censys** | Infrastructure complète, certificats SSL |
| **ZoomEye** | Ciblage géographique précis |

Informations obtenues : fabricant, localisation GPS, IP, hostname, ports ouverts → **accès backdoor potentiel aux appareils IoT**

---

### 📁 Moteurs de Recherche FTP

| Outil | Source |
|-------|--------|
| **NAPALM FTP Indexer** | searchftps.net |
| **FreewareWeb FTP File Search** | – |
| **Mamont** | – |
| **Globalfilesearch.com** | – |

> Permettent de trouver : stratégies d'affaires, documents fiscaux, dossiers personnels, logiciels sous licence

---

> ⬅️ [02 - Concepts du Footprinting](./02_concepts_footprinting.md) | ➡️ [04 - Services Internet](./04_internet_research.md)
