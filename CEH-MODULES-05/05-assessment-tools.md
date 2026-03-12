# 05 — Outils d'Évaluation des Vulnérabilités

> 📁 CEH v13 | Module 5 | [← Retour au README](./README.md)

---

## 🗂️ Approches d'évaluation

### 4 types de solutions de vulnerability assessment

| Solution | Description | Avantage | Limite |
|----------|-------------|----------|--------|
| **Product-Based** | Installée sur le réseau interne (privé ou DMZ) | Contrôle total | Ne détecte pas toujours les attaques externes |
| **Service-Based** | Offerte par des tiers (audit, consulting) | Expertise externe | Scan réseau possible depuis internet |
| **Tree-Based** | Stratégies différentes par machine/composant | Précision par plateforme | Dépend de l'intelligence initiale de l'admin |
| **Inference-Based** | Commence par inventorier les protocoles, puis services, puis vulnérabilités | Ciblage progressif | Séquentiel — peut être lent |

---

## ✅ Caractéristiques d'une bonne solution d'évaluation

```
✓ Couvre un large éventail d'assets (réseau, serveurs, endpoints, web apps, cloud)
✓ Assure des résultats corrects (réseau, ports, protocoles, OS)
✓ Utilise une approche inference-based organisée
✓ Scans automatiques contre bases de données continuellement mises à jour
✓ Rapports concis, actionnables et personnalisables (par niveau de sévérité + trend analysis)
✓ Supporte plusieurs réseaux
✓ Suggère remèdes et contournements appropriés
✓ Imite la vue externe de l'attaquant
✓ Minimise faux positifs et faux négatifs
✓ Supporte le scan en temps réel + mises à jour fréquentes
✓ Supporte l'automatisation et l'intégration avec les outils SOAR
✓ Intègre la priorisation basée sur le risque (sévérité + criticité + contexte)
```

---

## 🔧 Types d'outils de vulnerability assessment

### Host-Based Vulnerability Assessment Tools
- Appropriés pour les serveurs (Web, fichiers critiques, BDD, accès distants)
- Détectent hauts niveaux de vulnérabilités
- Identifient l'OS et testent les déficiences connues
- Recherchent apps et services communs

### Depth Assessment Tools
- Découvrent des **vulnérabilités inconnues**
- Utilisent des **fuzzers** (inputs arbitraires à l'interface)
- Testent la résistance à des vulnérabilités connues via signatures

### Application-Layer Vulnerability Assessment Tools
- Tous types d'OS et d'applications
- Vue externe via routeur, firewall, webserver = **external vulnerability assessment**
- Dirigés vers serveurs web ou bases de données
- Mises à jour régulières des informations de vulnérabilité réseau

### Scope Assessment Tools
- Évaluent la sécurité des applications ET de l'OS
- Interface de reporting standardisée
- Certains outils ciblés sur une application/type spécifique

### Active & Passive Tools

| Type | Caractéristique | Avantage | Limite |
|------|----------------|----------|--------|
| **Active Scanner** | Consomme ressources réseau | Contrôle timing & paramètres | Ne pas utiliser sur systèmes critiques |
| **Passive Scanner** | N'affecte pas les ressources | Observe, analyse séparément | Vue moins profonde |

### Location & Data Examination Tools

| Type | Description |
|------|-------------|
| **Network-Based Scanner** | Réside sur un système central, scanne à distance |
| **Agent-Based Scanner** | Agents installés sur les machines cibles, résultats envoyés centralement |
| **Proxy Scanner** | Scanner réseau pouvant scanner depuis n'importe quelle machine du réseau |
| **Cluster Scanner** | Comme proxy scanner, mais **2+ scans simultanés** sur différentes machines |

---

## 🛠️ Outils principaux

### 🔴 Nessus Essentials
> 🌐 [https://www.tenable.com](https://www.tenable.com)

**Cas d'usage :** Évaluation complète de vulnérabilités, configuration, conformité et malware

**Technologies supportées :** OS, devices réseau, hypervisors, BDD, tablettes/téléphones, serveurs web, infrastructure critique

| Fonctionnalité | Description |
|----------------|-------------|
| High-speed asset discovery | Découverte rapide des assets |
| Vulnerability Assessment | Évaluation complète des vulnérabilités |
| Malware & Botnet Detection | Détection de malware et botnets |
| Cloud & Virtual Scanning | Scan des plateformes virtualisées et cloud |

---

### 🔵 GFI LanGuard
> 🌐 [https://www.gfi.com](https://www.gfi.com)

**Cas d'usage :** Scan, détection, évaluation et correction avec effort admin minimal

| Fonctionnalité | Description |
|----------------|-------------|
| Patch Management | OS + applications tierces |
| Vulnerability Assessment | Évaluation complète |
| Web Reporting Console | Interface de reporting web |
| Track vulnerabilities | Suivi des vulnérabilités + MAJ manquantes |
| Network Device Checks | Vérifications devices réseau |
| Virtual Env Support | Support des environnements virtuels |

---

### 🟢 OpenVAS
> 🌐 [https://www.openvas.org](https://www.openvas.org)

**Cas d'usage :** Solution open-source complète de scanning et gestion de vulnérabilités

- Fait partie de la solution commerciale **Greenbone Networks**
- Contributions open-source depuis **2009**
- **+50 000 Network Vulnerability Tests (NVTs)** mis à jour régulièrement

---

### 🟡 Nikto
> 🌐 [https://cirt.net](https://cirt.net)

**Cas d'usage :** Scanner web server open-source (GPL)

| Capacité | Détail |
|---------|--------|
| Fichiers dangereux | +6 700 fichiers/programmes potentiellement dangereux |
| Versions serveur | Vérification des versions obsolètes (+1 250 serveurs) |
| Problèmes spécifiques | +270 serveurs (problèmes par version) |
| Config serveur | Fichiers index multiples, options HTTP |

**Features :**
```
✓ Support SSL (Unix/OpenSSL ou Windows/Perl)
✓ Support HTTP proxy complet
✓ Vérification des composants serveur obsolètes
✓ Rapports : plaintext, XML, HTML, NBE, CSV
✓ Template engine pour rapports personnalisés
✓ Scan de ports multiples / serveurs multiples (fichier input)
✓ Techniques d'encodage IDS (LibWhisker)
✓ Identification du logiciel installé (headers, favicons, fichiers)
✓ Authentification hôte Basic et NTLM
✓ Devinette de sous-domaines
✓ Énumération Apache/cgiwrap
✓ Devinette de credentials pour realms d'autorisation
```

---

### 🟣 Qualys Vulnerability Management
> 🌐 [https://www.qualys.com](https://www.qualys.com)

**Cas d'usage :** Service cloud de visibilité globale sur les vulnérabilités IT

| Feature | Description |
|---------|-------------|
| Agent-based Detection | Fonctionne avec Qualys Cloud Agents, couvre les assets non-scannables |
| Constant Monitoring | Alertes proactives (pairable avec CM - Continuous Monitoring) |
| Comprehensive Coverage | Assets on-premises, cloud et endpoints mobiles |
| Executive Dashboard | Vue d'ensemble posture sécurité + accès détails remédiation |
| Custom Reports | Rapports role-based pour multiple stakeholders |
| Perimeter-less | Adapté aux environnements hybrides (cloud + mobilité) |
| Device Discovery | Détecte devices oubliés, access points inattendus |
| Risk Identification | Trend analysis, Zero-Day, Patch impact predictions |

---

## 🤖 Outils de Vulnerability Assessment Alimentés par l'IA

### Limites des outils traditionnels vs IA

| Point | Outils Traditionnels | Outils IA |
|-------|---------------------|-----------|
| **Scope & Coverage** | Vulnérabilités connues (règles prédéfinies) | Détecte vulnérabilités connues ET inconnues |
| **Prioritization** | Basée uniquement sur la sévérité technique | Basée sur criticité asset + compliance + probabilité d'exploit |
| **Efficiency** | Gourmand en temps et ressources | Automatisation et algorithmes optimisés |
| **Adaptability** | Règles figées — difficile à adapter | Apprentissage continu sur nouvelles menaces |

> 💡 Les scanners IA peuvent **s'adapter à l'environnement spécifique** d'une organisation, réduisant faux positifs et faux négatifs.

---

### 🔷 Equixly
> 🌐 [https://equixly.com](https://equixly.com)

**Focus :** Sécurisation des **APIs** via IA/ML

| Feature | Description |
|---------|-------------|
| AI-Driven Vulnerability Detection | ML pour scanner et identifier les vulnérabilités API |
| Automated Threat Analysis | Automatisation de l'analyse des données de menace |
| Real-Time Security Monitoring | Monitoring continu + alertes temps réel |
| Adaptive Learning | Amélioration continue de la détection |

---

### 🔶 SmartScanner
> 🌐 [https://www.thesmartscanner.com](https://www.thesmartscanner.com)

**Focus :** Sécurité des **sites web** via ML

| Feature | Description |
|---------|-------------|
| Supervised & Unsupervised ML | Apprend les patterns bénins ET malveillants |
| Baseline Establishment | Établit un comportement normal pour chaque site |
| Anomaly Detection | Alerte sur les déviations du comportement baseline |
| Real-time Analytics & Response | Réponse automatique aux menaces identifiées |

---

### 📋 Autres outils IA notables

| Outil | URL | Spécialité |
|-------|-----|------------|
| **CodeDefender** | codedefender.ro | Détection et fix auto de vulnérabilités dans le code |
| **Corgea** | corgea.com | Génération et déploiement auto de patches de sécurité |
| **Fluxguard** | fluxguard.com | Analyse comportementale du trafic réseau |
| **DryRun Security** | dryrun.security | Pentest + identification de failles web et infra |
| **Pentest Copilot** | copilot.bugbase.ai | Assistant IA pour pentest (recon → exploitation) |
| **Beagle Security** | beaglesecurity.com | Scanning auto + pentest manuel, détection OWASP Top 10 |
| **Hackules** | hackules.com | NLP + ML pour insights sécurité web et infra |
| **Coderbuds** | coderbuds.com | Intégration dev tools + scans auto + risk assessments |

---

### Autres outils classiques à connaître

| Outil | URL |
|-------|-----|
| InsightVM | rapid7.com |
| Acunetix Web Vulnerability Scanner | acunetix.com |
| Nexpose | rapid7.com |
| Tripwire IP360 | tripwire.com |
| SAINT Security Suite | carson-saint.com |
| Core Impact Pro | coresecurity.com |
| ManageEngine Vulnerability Manager Plus | manageengine.com |
| Astra Pentest | getastra.com |

---

## 🎯 Critères de sélection d'un outil

```
✓ Capacité à tester de 10 à 30 000+ vulnérabilités différentes
✓ Base de données solide + signatures d'attaques fréquemment mises à jour
✓ Adapté à l'environnement et au niveau d'expertise
✓ Moteur de scan mis à jour régulièrement
✓ Mapping réseau/application précis + tests de pénétration
✓ Scripts de vulnérabilité régulièrement mis à jour par plateforme
✓ Gestion des patches appliqués
✓ Nombre et exportabilité des rapports
✓ Niveaux de pénétration différents (éviter les lockups)
✓ Rapidité de scan
✓ Support multi-protocoles
✓ Compréhension de la topologie réseau
✓ Haute allocation de bande passante (grands réseaux)
✓ Query throttling avancé
✓ Capacité à évaluer systèmes fragiles et assets non-traditionnels
```

### Critères formels d'achat

| Critère | Description |
|---------|-------------|
| Types de vulnérabilités | Combien de types peut-il découvrir ? |
| Capacité de scanning | Peut-il exécuter TOUS les tests sélectionnés ? |
| Précision des rapports | Rapports courts, clairs et actionnables |
| Efficacité et précision | Temps par hôte, ressources requises, perte de services |
| Smart search | Intelligence lors du scanning |
| Tests personnalisés | Peut-il créer ses propres tests pour nouvelles vulnérabilités ? |
| Scheduling | Planification des scans aux heures creuses |
| Vitesse | Détection rapide sans overhead performance |
| Compatibilité | Systèmes legacy + environnements divers |
| Configuration support | On-premises et cloud |
| Compliance | Standards, réglementations, best practices |
| Licensing & Coût | Modèle adapté à la taille/budget de l'org |
| Scalabilité | Gère l'augmentation des volumes sans dégradation |

---

## 📝 Points clés pour l'examen CEH

| Question | Réponse |
|----------|---------|
| Outil open-source avec +50 000 NVTs ? | **OpenVAS** |
| Scanner web server GPL, teste +6700 fichiers ? | **Nikto** |
| Solution cloud de Qualys ? | **Qualys VM** |
| Outil IA focalisé sur les APIs ? | **Equixly** |
| Outil Tenable pour vulnérabilités ? | **Nessus Essentials** |
| Différence active vs passive scan ? | Actif = interaction directe, Passif = observation |
| Outil qui ne peut pas détecter les Zero-Days ? | **Tous les outils traditionnels** |

---

[← Cycle de vie](./03-vulnerability-management-lifecycle.md) | [→ Rapports d'évaluation](./06-assessment-reports.md)
