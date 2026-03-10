# 📌 07 — Outils de Footprinting & Contre-mesures

> **Module 2 — Footprinting & Reconnaissance** | CEH v13

---

## 🧰 Outils Principaux de Footprinting

### Maltego

> 📌 Source : [https://www.maltego.com](https://www.maltego.com)

Outil d'**analyse des relations** entre personnes, organisations, domaines, IPs, etc.

**Informations extraites :**
- Adresses email associées à un domaine
- Numéros de téléphone
- Infrastructure réseau (domaines, DNS, netblocks, IPs)
- Relations entre entités (personnes ↔ organisations ↔ serveurs)

```
Workflow Maltego :
1. Ajouter une entité "Website"
2. Renommer avec le domaine cible
3. Transformer → Extraction automatique d'emails, téléphones, infrastructure
```

---

### Recon-ng

> 📌 Source : [https://github.com](https://github.com)

Framework de reconnaissance web **modulaire** pour la collecte OSINT.

```bash
# Lancer Recon-ng
recon-ng

# Utiliser un module de découverte d'hôtes
use recon/domains-hosts/brute_hosts

# Configurer le domaine cible
set SOURCE microsoft.com

# Lancer la reconnaissance
run
```

---

### FOCA (Fingerprinting Organizations with Collected Archives)

> 📌 Source : [https://www.elevenpaths.com](https://www.elevenpaths.com)

Outil d'extraction de **métadonnées et informations cachées** dans les documents.

**Documents analysés :** Microsoft Office, OpenOffice, PDF

**Fonctionnalités :**
| Module | Description |
|--------|-------------|
| **Web Search** | Trouve hôtes et domaines via URLs |
| **DNS Search** | Vérifie NS, MX, SPF pour nouveaux hôtes |
| **IP Resolution** | Résolution hostname → IP via DNS interne |
| **PTR Scanning** | Scan PTR pour découvrir plus de serveurs |
| **Bing IP** | Recherche de domaines associés à chaque IP |
| **Common Names** | Attaques par dictionnaire contre le DNS |

---

### subfinder

> 📌 Source : [https://github.com](https://github.com)

Outil de découverte de **sous-domaines passif** via sources OSINT en ligne.

```bash
subfinder -d microsoft.com -o output.txt
```

---

### OSINT Framework

> 📌 Source : [https://osintframework.com](https://osintframework.com)

Framework web répertoriant les outils OSINT par **catégorie** sous forme d'arbre.

**Indicateurs dans l'interface :**
| Indicateur | Signification |
|-----------|--------------|
| `(T)` | Outil à installer et exécuter localement |
| `(D)` | Google Dork |
| `(R)` | Nécessite une inscription |
| `(M)` | URL à modifier manuellement |

---

### Recon-Dog

> 📌 Source : [https://www.github.com](https://github.com)

Outil tout-en-un utilisant des **APIs** pour collecter des informations.

**Fonctionnalités :**
- Censys — informations sur une IP
- NS lookup
- Port scan (ports TCP courants)
- Détection de CMS (400+)
- Whois lookup
- Détection de honeypot (via Shodan)
- Recherche de sous-domaines
- Reverse IP lookup
- Détection de technologies (via Wappalyzer, 1000+)

---

### BillCipher

> 📌 Source : [https://www.github.com](https://github.com)

Outil d'information gathering pour sites web ou IPs (Python 2/3 + Ruby).

**Options :**
- DNS lookup, Whois lookup
- Port scanning, Zone transfer
- Host finder, Reverse IP lookup

---

### Script Python — Footprinting Automatisé

```python
# website_footprinting.py — Footprinting complet d'un domaine
import subprocess

def dns_lookup(domain):
    return subprocess.getoutput(f"dig {domain} ANY +noall +answer")

def whois_lookup(domain):
    return subprocess.getoutput(f"whois {domain}")

def email_enumeration(domain):
    return subprocess.getoutput(f"theHarvester -d {domain} -b all -l 100")

def run_footprinting(domain):
    print("Performing DNS Lookup...")
    print(dns_lookup(domain))
    
    print("\nPerforming Whois Lookup...")
    print(whois_lookup(domain))
    
    print("\nEnumerating Emails...")
    print(email_enumeration(domain))

domain = 'www.microsoft.com'
run_footprinting(domain)
```

```bash
# Exécution
python3 website_footprinting.py
```

---

## 🤖 Outils OSINT Basés sur l'IA

| Outil | Source | Description |
|-------|--------|-------------|
| **DorkGPT** | dorkgpt.com | Génération de Google Dorks via GPT |
| **DorkGenius** | dorkgenius.com | Automatisation du Google Dorking avancé |
| **Google Word Sniper** | googlewordsniper.eu | Affinage de requêtes pour mots-clés spécifiques |
| **Cylect.io** | cylect.io | OSINT multi-bases de données, interface unifiée |
| **ChatPDF** | chatpdf.com | Extraction d'informations depuis des PDFs via chat |
| **Bardeen.ai** | bardeen.ai | Automatisation de collecte de données en ligne |
| **DarkGPT** | github.com/luijait/DarkGPT | GPT-4 pour interroger des bases de données leakées |
| **PenLink Cobwebs** | cobwebs.com | OSINT avancé, visualisation de données |
| **Explore AI** | exploreai.vercel.app | Moteur de recherche YouTube basé sur l'IA |
| **AnyPicker** | app.anypicker.com | Web scraper visuel sans code |

---

## 🛡️ Contre-mesures du Footprinting

### Réduction de la Surface d'Exposition

```
Principe : Minimiser les informations publiquement accessibles
```

**Politique réseau & systèmes :**
- ✅ Restreindre l'accès des employés aux réseaux sociaux depuis le réseau de l'entreprise
- ✅ Configurer les serveurs web pour **éviter la divulgation d'informations**
- ✅ Désactiver les listings de répertoires sur les serveurs web
- ✅ Séparer DNS interne et externe (**Split DNS**) + restreindre les transferts de zone aux serveurs autorisés
- ✅ Ne pas activer les protocoles inutiles
- ✅ Utiliser des filtres TCP/IP et IPsec pour une **défense en profondeur**
- ✅ Configurer IIS pour éviter la divulgation via **banner grabbing**
- ✅ Masquer l'IP publique via **VPN** ou serveur derrière un proxy sécurisé
- ✅ Déployer des **honeypots/honeynets** pour attirer et détecter les attaquants

---

**Politique de publication :**
- ✅ Limiter les informations publiées sur les sites web et Internet
- ✅ Ne pas révéler d'informations critiques dans les communiqués de presse, rapports annuels, catalogues
- ✅ Éviter le **cross-linking** entre domaines pour les actifs critiques
- ✅ Chiffrer et protéger par mot de passe les informations sensibles
- ✅ Placer les documents critiques (business plans, propriété intellectuelle) **hors ligne**
- ✅ Demander à archive.org de **supprimer l'historique** du site
- ✅ Garder le profil de domaine **privé**

---

**Politique pour les employés :**
- ✅ Former les employés à l'utilisation de **pseudonymes** sur blogs/forums
- ✅ Former les employés aux techniques d'**ingénierie sociale**
- ✅ Sensibiliser périodiquement aux **risques de divulgation**
- ✅ Utiliser des services d'enregistrement anonyme (**anonymous registration services**)
- ✅ Opt-in pour les **privacy services** dans les bases WHOIS
- ✅ Implémenter l'**authentification multi-facteurs (MFA)**
- ✅ Mettre en place des **politiques de sécurité** (information security, password policies)
- ✅ Désactiver la **géolocalisation** sur les appareils mobiles si non nécessaire
- ✅ Désactiver la **fonction géo-tagging** des caméras
- ✅ Ne pas partager sa localisation/plans de voyage sur les réseaux sociaux
- ✅ Désactiver/supprimer les comptes des employés qui ont quitté l'organisation

---

**Technique avancée :**
- ✅ Implémenter des **captchas et rate limiting** sur les services publics
- ✅ Empêcher les moteurs de recherche de **mettre en cache** les pages web
- ✅ Configurer les serveurs mail pour **ignorer les mails d'individus anonymes**
- ✅ Sanitiser les détails fournis aux registrars Internet

---

## 📊 Récapitulatif — Tableau de Synthèse

| Phase | Technique | Outils clés |
|-------|-----------|-------------|
| Passive | Moteurs de recherche | Google Dorks, SHODAN, Censys |
| Passive | Archives web | Archive.org + Photon |
| Passive | People search | Spokeo, Intelius, pipl |
| Passive | Réseaux sociaux | Sherlock, Social Searcher, BuzzSumo |
| Passive | WHOIS | DomainTools, Active Whois |
| Passive | Dark Web | Tor Browser, DarkGPT |
| Passive | Compétiteurs | SEMRush, SimilarWeb, EDGAR |
| Active | DNS | Fierce, SecurityTrails, DNSRecon |
| Active | Réseau | Traceroute, NetScanTools Pro |
| Active | Email | eMailTrackerPro, MxToolbox |
| Active | Sous-domaines | Sublist3r, subfinder, DNSdumpster |
| Active | OSINT complet | Maltego, Recon-ng, FOCA, theHarvester |
| AI-powered | Automatisation | DorkGPT, DarkGPT, shellGPT, ChatGPT |

---

## 🎯 Points Clés pour l'Examen CEH

> ⚡ Les questions d'examen portent souvent sur :

1. **Types de footprinting** → Passif (aucune interaction) vs Actif (interaction directe)
2. **WHOIS** → Port TCP 43, 3 modèles (Thick/Thin/Decentralized), 5 RIRs
3. **DNS Records** → Connaître tous les types (A, AAAA, MX, NS, CNAME, SOA, PTR...)
4. **Traceroute** → Utilise TTL + ICMP ; ICMP (Windows), UDP (Linux), TCP (Layer 4)
5. **Google Dorks** → `site:`, `intitle:`, `inurl:`, `filetype:`, `cache:`, `before:`/`after:`
6. **MITRE ATT&CK** → PRE-ATT&CK (Recon/Weaponize) + Enterprise ATT&CK (Exploit→Maintain)
7. **Dark Web** → Surface Web → Deep Web → Dark Web (accès via Tor)
8. **Countermeasures** → Split DNS, MFA, privacy WHOIS, désactiver géo-tagging

---

> ⬅️ [06 - Réseau, Email & Social](./06_network_email_social.md) | ⬆️ [README](./README.md)
