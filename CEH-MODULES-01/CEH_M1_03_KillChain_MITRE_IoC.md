# CEH v13 — Module 1 : Introduction à l'Ethical Hacking
## Partie 3 : Cyber Kill Chain, MITRE ATT&CK, Diamond Model, TTPs & IoC

---

## 1. Cyber Kill Chain — Modèle Lockheed Martin ⭐

> Framework développé par Lockheed Martin pour décrire les étapes d'une cyberattaque, de la reconnaissance initiale jusqu'à l'objectif final. Permet aux défenseurs de **détecter et couper** l'attaque à chaque phase.

| # | Phase | Description | Contre-mesure |
|---|---|---|---|
| 1 | **Reconnaissance** | Collecte d'info sur la cible (DNS, WHOIS, OSINT, réseaux sociaux, scan) | Limiter l'info publique, surveiller les DNS queries |
| 2 | **Weaponization** | Création/acquisition du payload malveillant (exploit + backdoor = "weapon") | Analyse des menaces, threat intelligence |
| 3 | **Delivery** | Envoi du payload vers la cible (email, USB, lien web, watering hole) | Filtrage email, sensibilisation utilisateurs |
| 4 | **Exploitation** | Déclenchement de l'exploit sur le système cible (exécution de code) | Patches, sandboxing, EDR |
| 5 | **Installation** | Installation du malware / backdoor sur le système compromis | Antivirus, EDR, monitoring intégrité fichiers |
| 6 | **Command & Control (C2/C&C)** | Établissement d'un canal de contrôle entre attaquant et machine compromise | Blocage des flux C2, DNS filtering, IPS |
| 7 | **Actions on Objectives** | Réalisation des objectifs finaux (vol de données, destruction, mouvement latéral) | DLP, monitoring, segmentation réseau |

> 💡 **Mémo** : **R-W-D-E-I-C-A** → **R**econ, **W**eaponize, **D**eliver, **E**xploit, **I**nstall, **C**ommand, **A**ctions

> ℹ️ L'ancienne terminologie militaire utilisait **F2T2EA** : Find, Fix, Track, Target, Engage, Assess — le principe est identique.

---

## 2. TTPs — Tactics, Techniques & Procedures

> Les TTPs décrivent les **comportements et méthodes caractéristiques** d'un acteur de menace ou groupe d'attaquants.

| Niveau | Définition | Exemple |
|---|---|---|
| **Tactics** | **Objectifs généraux** de l'attaque — ce que l'attaquant cherche à faire | Infiltrer un site e-commerce pour voler des données CB |
| **Techniques** | **Méthode technique** utilisée pour atteindre l'objectif tactique | E-skimming, injection JavaScript, XSS, Magecart |
| **Procedures** | **Description étape par étape** de l'attaque — outils et séquences précises | Script de skimming → exfiltration vers C2 → chiffrement des données |

> Les TTPs servent à créer un **profil/fingerprint** d'un acteur de menace pour anticiper ses prochaines actions.

---

## 3. MITRE ATT&CK Framework

> Base de connaissances structurée répertoriant les **TTPs réelles d'adversaires** documentées lors d'attaques confirmées.

### Structure du framework

| Niveau | Description |
|---|---|
| **Tactics** (14 tactiques) | Les "pourquoi" — objectifs de chaque phase de l'attaque |
| **Techniques** (~200+) | Les "comment" — méthodes spécifiques pour chaque tactique |
| **Sub-techniques** | Variantes précises d'une technique |
| **Procedures** | Implémentation réelle par un acteur spécifique (groupe APT) |

### Les 14 Tactiques MITRE ATT&CK

| # | Tactique | Objectif |
|---|---|---|
| 1 | Reconnaissance | Collecter info avant l'attaque |
| 2 | Resource Development | Acquérir/développer des ressources offensives |
| 3 | Initial Access | Pénétrer le réseau cible |
| 4 | Execution | Exécuter du code malveillant |
| 5 | Persistence | Maintenir l'accès |
| 6 | Privilege Escalation | Obtenir des droits plus élevés |
| 7 | Defense Evasion | Éviter la détection |
| 8 | Credential Access | Voler des identifiants |
| 9 | Discovery | Explorer l'environnement compromis |
| 10 | Lateral Movement | Se déplacer dans le réseau |
| 11 | Collection | Rassembler des données d'intérêt |
| 12 | Command & Control | Maintenir le contrôle à distance |
| 13 | Exfiltration | Extraire les données volées |
| 14 | Impact | Manipuler, interrompre ou détruire |

> **Usages** : threat detection, risk assessment, red teaming, évaluation d'outils, stratégie défensive.

---

## 4. Diamond Model of Intrusion Analysis

> Modèle d'analyse d'intrusion structurant les événements autour de **4 composants interconnectés** formant un diamant.

```
          Adversary (Attaquant)
              /        \
             /          \
       Infrastructure — Victim (Cible)
             \          /
              \        /
           Capability (Capacité)
```

| Composant | Description |
|---|---|
| **Adversary** | L'acteur de la menace (individu, groupe, nation-state) |
| **Capability** | Les outils, exploits et malwares utilisés |
| **Infrastructure** | Les serveurs, domaines, IPs utilisés pour l'attaque |
| **Victim** | La cible de l'attaque (organisation, système, personne) |

> Le Diamond Model permet de **corréler des événements apparemment distincts** et d'identifier les relations entre attaques — utile pour l'attribution et la threat intelligence.

---

## 5. Indicateurs de Compromission (IoC)

> Les IoC sont des **artefacts forensiques** trouvés dans les systèmes/réseaux indiquant une activité malveillante potentielle ou une intrusion en cours.

### 4 Catégories d'IoC

| Catégorie | Description | Exemples |
|---|---|---|
| **Email IoCs** | Indicateurs dans les emails malveillants | Adresses expéditeur suspectes, sujets, pièces jointes, URLs |
| **Network IoCs** | Indicateurs dans le trafic réseau | IPs C2, domaines malveillants, patterns de connexion inhabituels |
| **Host-based IoCs** | Indicateurs sur les machines compromises | Fichiers créés, clés registre, processus suspects, persistence |
| **Behavioral IoCs** | Comportements anormaux des utilisateurs/systèmes | Connexions inhabituelles, accès massif aux données, mouvements latéraux |

> 💡 Les **cyber threats évoluent continuellement** — les professionnels doivent surveiller les IoC en permanence pour détecter et répondre aux nouvelles TTPs.

---

## 6. Threat Intelligence (Renseignement sur les Menaces)

> Collecte et analyse structurée de données sur les menaces actuelles et futures pour prendre de meilleures décisions de sécurité.

### Cycle de vie du Threat Intelligence

```
Planning → Collection → Processing → Analysis → Dissemination → Feedback
```

| Phase | Action |
|---|---|
| **Planning** | Définir les besoins en renseignement |
| **Collection** | Collecter données (OSINT, dark web, honeypots, partenaires) |
| **Processing** | Normaliser et filtrer les données brutes |
| **Analysis** | Transformer les données en renseignement actionnable |
| **Dissemination** | Distribuer aux équipes concernées (SOC, management, IR) |
| **Feedback** | Améliorer le processus en continu |

### Types de Threat Intelligence

| Type | Niveau | Usage |
|---|---|---|
| **Strategic** | Haut niveau | Management, RSSI — tendances et risques business |
| **Tactical** | TTPs | Equipes sécurité — comprendre les méthodes adversaires |
| **Operational** | Campagnes spécifiques | IR, SOC — détails d'attaques en cours |
| **Technical** | IoCs, signatures | Outils, SIEM, firewalls — blocage automatisé |

---

## 7. Gestion des Risques — Notions clés

| Concept | Définition |
|---|---|
| **Risk** | Probabilité × Impact d'une menace exploitant une vulnérabilité |
| **Risk Assessment** | Identifier, analyser et évaluer les risques |
| **Risk Mitigation** | Réduire le risque à un niveau acceptable |
| **Risk Transfer** | Transférer le risque (assurance cyber) |
| **Risk Acceptance** | Accepter le risque résiduel si coût de mitigation > impact |
| **Residual Risk** | Risque restant après application des contrôles |

### Niveaux de risque
- **High** : Impact sévère, probabilité élevée → action immédiate
- **Medium** : Impact modéré OU probabilité élevée → plan d'action requis
- **Low** : Impact faible ET probabilité faible → monitoring

---

## 8. Incident Response (IR) — Processus

> L'IH&R (Incident Handling & Response) est l'ensemble des procédures pour réagir de manière organisée à un incident de sécurité.

| Phase | Action |
|---|---|
| **Preparation** | Audit des assets, définition des politiques et procédures IR |
| **Identification** | Détection et signalement initial de l'incident |
| **Analysis** | Validation, catégorisation et priorisation de l'incident |
| **Notification** | Information des parties prenantes (management, tiers, clients) |
| **Containment** | Isoler les systèmes compromis pour stopper la propagation |
| **Eradication** | Supprimer la cause racine (malware, vulnérabilité) |
| **Recovery** | Restaurer les systèmes en état opérationnel |
| **Post-Incident** | Lessons learned, amélioration des contrôles |
