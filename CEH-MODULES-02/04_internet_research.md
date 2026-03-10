# 📌 04 — Footprinting via Services Internet & Dark Web

> **Module 2 — Footprinting & Reconnaissance** | CEH v13

---

## 🏢 Sous-domaines & Top-Level Domains (TLDs)

### Pourquoi cibler les sous-domaines ?

```
example.com          → Site public (visible de tous)
dev.example.com      → Environnement de test (souvent non sécurisé !)
internal.example.com → Accès restreint (infos critiques)
mail.example.com     → Serveur de messagerie
```

> ⚠️ Les sous-domaines de test sont souvent **vulnérables** et peuvent exposer du code source, des fichiers de config, etc.

### Méthode avec Google Dork
```
site:microsoft.com -inurl:www
```

### Outils de découverte de sous-domaines

| Outil | Source | Fonctionnalités |
|-------|--------|-----------------|
| **Netcraft** | netcraft.com | Sous-domaines, OS, technologies web |
| **DNSdumpster** | dnsdumpster.com | Hosts, sous-domaines, DNS, IPs |
| **Pentest-Tools** | pentest-tools.com | Sous-domaines, IPs, OS, serveurs |
| **subfinder** | github.com | Découverte passive multi-sources |

### Commandes avancées (dig + AI)

```bash
# Découverte via dig (name servers → sous-domaines)
dig +short google.com NS | xargs -I{} dig +nocmd +noall +answer @{} google.com \
  | grep -E 'CNAME|A|AAAA'

# Avec shellGPT (automatisation IA)
sgpt --chat domain --shell "Discover all the subdomains of 'google.com' using dig command"

# Avec Sublist3r
sublist3r -d eccouncil.org -o eccouncil_subdomains.txt
```

---

## 👤 People Search Services

Sites permettant de trouver des informations personnelles sur les employés d'une cible :

| Service | Informations disponibles |
|---------|------------------------|
| **Spokeo** | Téléphones, emails, historique adresses, famille, réseaux sociaux |
| **Intelius** | Casiers judiciaires, historique, données de contact |
| **pipl** | Profils complets, réseaux professionnels |
| **BeenVerified** | Vérification d'identité complète |
| **Whitepages** | Téléphone, adresse, données publiques |
| **Instant Checkmate** | Contrôles criminels, localisation |
| **PeekYou** | Présence sur les réseaux sociaux |

---

## 💼 Footprinting via Offres d'Emploi

> Les annonces d'emploi révèlent involontairement l'**infrastructure technique** de l'organisation

### Informations extraites des job sites

```
Annonce "Network Administrator" → révèle :
  ├── Type de firewall utilisé
  ├── Système d'exploitation interne
  ├── Appliances réseau (Cisco, Juniper...)
  ├── Technologies de virtualisation (VMware, Hyper-V)
  └── Logiciels et versions déployés
```

### Sites cibles

| Site | Type d'informations |
|------|-------------------|
| **LinkedIn** | CV complets, stack technique, historique professionnel |
| **Glassdoor** | Culture interne, outils utilisés, témoignages employés |
| **Dice** | Profils IT, compétences techniques spécifiques |
| **Simply Hired** | Offres + profils candidats |

---

## 🌑 Dark Web Footprinting

### Les 3 couches du web

```
┌─────────────────────────────────────────────────────┐
│  SURFACE WEB  │ Indexé par Google, Bing, etc.        │
│               │ ~5% du web total                     │
├───────────────┼─────────────────────────────────────┤
│  DEEP WEB     │ Non indexé, bases de données,        │
│               │ portails gouvernementaux             │
│               │ ~90% du web total                    │
├───────────────┼─────────────────────────────────────┤
│  DARK WEB     │ Anonymat total, accès via Tor        │
│  (Darknet)    │ Activités légales ET illégales       │
│               │ Sous-ensemble du Deep Web            │
└───────────────┴─────────────────────────────────────┘
```

### Outils d'accès

| Outil | Usage |
|-------|-------|
| **Tor Browser** | Navigation anonyme, sites .onion |
| **ExoneraTor** | Vérification d'adresses Tor |
| **OnionLand Search** | Moteur de recherche .onion |

### Informations trouvables sur le Dark Web

- 💳 Données de cartes bancaires, comptes
- 🪪 Passeports, cartes d'identité, dossiers médicaux
- 👤 Comptes de réseaux sociaux compromis
- 🔑 Identifiants (logins/passwords)
- 📊 Numéros de sécurité sociale

---

### 🔍 Recherches Avancées sur le Dark Web

| Type d'information | Requête Tor/Dark Web | Explication |
|-------------------|----------------------|-------------|
| **Sensitive PDFs** | `filetype:pdf site:onion confidential` | PDFs confidentiels sur .onion |
| **Passwords in Config Files** | `inurl:config filetype:txt password` | Fichiers texte avec passwords |
| **Financial Documents** | `filetype:xlsx site:onion financial` | Fichiers Excel financiers |
| **Database Dumps** | `filetype:sql site:onion dump` | Dumps SQL |
| **Email Lists** | `filetype:csv site:onion email` | Listes d'emails CSV |
| **Login Credentials** | `intitle:"login credentials" filetype:docx` | Docs Word avec logins |
| **Private Keys** | `filetype:key site:onion private` | Clés privées |
| **Medical Records** | `filetype:pdf site:onion "medical records"` | Dossiers médicaux |
| **Business Plans** | `filetype:ppt site:onion "business plan"` | Plans stratégiques |
| **Bank Statements** | `filetype:pdf site:onion "bank statement"` | Relevés bancaires |
| **Security Vulnerabilities** | `filetype:txt inurl:exploit "security vulnerability"` | Exploits |

---

## 🔭 Renseignement Concurrentiel (Competitive Intelligence)

> Processus légal et éthique de collecte d'informations sur les **concurrents**

### Approches

| Approche | Techniques |
|----------|-----------|
| **Directe** | Salons professionnels, social engineering léger |
| **Indirecte** | Sites web, offres d'emploi, réseaux sociaux, rapports annuels |

### Bases de données & Sources

| Source | URL | Utilité |
|--------|-----|---------|
| **EDGAR (SEC)** | sec.gov/edgar | Dépôts légaux entreprises US |
| **D&B Hoovers** | dnb.com | 120M+ records commerciaux |
| **LexisNexis** | lexisnexis.com | Documents légaux et publics |
| **Business Wire** | businesswire.com | Communiqués de presse |
| **Factiva** | dowjones.com | 33 000+ sources mondiales |
| **MarketWatch** | marketwatch.com | Actualité marchés/entreprises |
| **Euromonitor** | euromonitor.com | Études de marché consommateurs |
| **SEMRush** | semrush.com | Keywords concurrents, SEO/SEM |
| **SimilarWeb** | similarweb.com | Trafic web, géographie, référents |
| **USPTO** | uspto.gov | Brevets et marques déposées |

---

## 📍 Géolocalisation des Cibles

### Pourquoi la localisation physique est importante ?

```
Connaissance localisation → 
  ├── Dumpster Diving (fouille des poubelles)
  ├── Surveillance physique
  ├── Social Engineering sur site
  └── Attaques sans contact (Wi-Fi, Bluetooth)
```

### Outils de Géolocalisation

| Outil | Usage |
|-------|-------|
| **Google Earth** | Images satellite 3D haute résolution, coordonnées, vues de rue |
| **Google Maps** | Itinéraires, trafic, informations de contact |
| **Wikimapia** | Annotations collaboratives de lieux |
| **Apple Maps / Waze** | Navigation, points d'intérêt |

---

## 📈 Services Financiers

Sources d'information financière sur les cibles :

| Service | Informations |
|---------|-------------|
| **Google Finance** | Actualité, cours boursiers, événements corporate |
| **Yahoo Finance** | Rapports financiers, profil entreprise |
| **MSN Money** | Concurrents, valorisation |
| **Investing.com** | Taux de change, marchés |

---

## 🔔 Monitoring & Alertes

> Surveillance automatisée des mentions de la cible

| Outil | Fonction |
|-------|---------|
| **Google Alerts** | Notifications email sur nouveaux contenus |
| **X (Twitter) Alerts** | Mentions en temps réel |
| **Giga Alerts** | Suivi web multicanal |
| **Mention** | Surveillance web + réseaux sociaux + blogs |

---

## 🌐 Archive.org (Wayback Machine)

> 📌 Source : [https://archive.org](https://archive.org)

Permet de récupérer **les versions archivées** d'un site web depuis sa création.

```bash
# Récupérer les URLs archivées avec Photon
photon.py -u <URL_CIBLE> -l 3 -t 200 --wayback

# Récupérer uniquement les URLs
python photon.py -u <URL_CIBLE> -l 3 -t 200 --only-urls
```

**Informations récupérables :** pages supprimées, fichiers audio/vidéo, images, code source, documents anciens.

---

## 💬 Groupes, Forums & Blogs

Les employés révèlent involontairement des informations sensibles :

```
Informations collectables :
  ├── Nom complet et lieu de travail
  ├── Numéros de téléphone perso/pro
  ├── Adresses email personnelles et professionnelles
  ├── Photos de bureaux (révèlent infos visuelles)
  └── Objectifs professionnels / projets en cours
```

---

## 💻 Dépôts de Code Source Publics

| Plateforme | Risques |
|-----------|---------|
| **GitHub** | Clés SSH/SSL, tokens API, mots de passe en dur |
| **GitLab** | Fichiers de configuration, bibliothèques |
| **SourceForge** | Code source, outils internes |
| **BitBucket** | Projets privés parfois mal configurés |

```bash
# Outil de découverte de dépôts publics
# Recon-ng — module brute_hosts
recon-ng > use recon/domains-hosts/brute_hosts
```

---

> ⬅️ [03 - Search Engines](./03_search_engines.md) | ➡️ [05 - WHOIS, DNS & Géolocalisation](./05_whois_dns_geo.md)
