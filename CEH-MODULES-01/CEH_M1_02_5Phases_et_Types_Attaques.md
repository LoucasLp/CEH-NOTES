# CEH v13 — Module 1 : Introduction à l'Ethical Hacking
## Partie 2 : Les 5 Phases du Hacking & Types d'Attaques

---

## 1. Les 5 Phases de l'Ethical Hacking ⭐

> **À connaître par cœur — très fréquemment testé à l'examen**

| Phase | Nom | Objectif | Outils/Techniques |
|---|---|---|---|
| **1** | **Reconnaissance (Footprinting)** | Collecter un maximum d'informations sur la cible **avant** toute interaction | DNS, WHOIS, OSINT, réseaux sociaux, Google Dorks, Shodan |
| **2** | **Scanning & Enumeration** | Identifier hôtes actifs, ports ouverts, services, détails système | Nmap, Nessus, Angry IP Scanner |
| **3** | **Gaining Access** | Exploiter les vulnérabilités pour pénétrer les systèmes | Password cracking, SQLi, buffer overflow, exploits CVE |
| **4** | **Maintaining Access** | Établir une persistance pour revenir sans être détecté | Backdoors, Trojans, rootkits, RATs |
| **5** | **Covering Tracks** | Effacer les traces pour éviter la détection et l'analyse forensique | Nettoyage des logs, suppression de fichiers, rootkits, stéganographie |

### Sous-types de Reconnaissance

| Type | Description |
|---|---|
| **Passive** | Collecte d'info **sans contact direct** avec la cible (recherche web, OSINT, réseaux sociaux) |
| **Active** | Interaction directe avec la cible (scan DNS, traceroute, scan réseau) |

---

## 2. Types d'Attaques

### Par méthode d'exécution

| Type | Description | Exemples |
|---|---|---|
| **Passive Attack** | Observe/intercepte les données **sans les modifier** — difficile à détecter | Sniffing, eavesdropping, traffic analysis |
| **Active Attack** | Modifie les données ou perturbe les services — plus visible | DoS, MitM, session hijacking, SQL injection |
| **Close-in Attack** | L'attaquant est **physiquement proche** de la cible | Shoulder surfing, eavesdropping physique, dumpster diving |
| **Insider Attack** | Attaque depuis l'**intérieur** de l'organisation par un employé ou contractant | Abus de privilèges, vol de données, sabotage |
| **Distribution Attack** | Compromission du matériel ou logiciel **à la source ou en transit** (supply chain) | Backdoors dans firmware, logiciels malveillants intégrés avant livraison |

### Vecteurs d'attaque modernes

| Vecteur | Description |
|---|---|
| **Phishing / Spear-phishing** | Email frauduleux ciblé pour voler des credentials ou déployer des malwares |
| **Malware** | Virus, ransomware, Trojans, spyware, worms |
| **Web Exploits** | SQL injection, XSS, CSRF, IDOR |
| **Cloud Exploits** | Mauvaises configurations, buckets S3 exposés, violations de la responsabilité partagée |
| **IoT Exploits** | Authentification faible, mots de passe par défaut, firmware non patché |
| **Mobile Exploits** | Vulnérabilités d'apps, rooting/jailbreaking, faux réseaux WiFi |
| **Insider Threats** | Employés abusant de leurs accès légitimes |
| **AI-Powered Attacks** | Deepfakes pour social engineering, phishing automatisé et personnalisé par IA |

---

## 3. Concepts de Hacking Avancés

### APT — Advanced Persistent Threat
- Attaque **longue durée**, furtive, menée par des acteurs étatiques ou groupes organisés
- Objectif : espionnage, vol de données stratégiques, sabotage d'infrastructures critiques
- Caractéristiques : persistance, discrétion, ressources importantes, techniques sophistiquées

### Zero-Day Exploit
- Exploite une vulnérabilité **non encore patchée** et inconnue du vendor
- Délai entre découverte et patch = fenêtre d'exploitation = "zero day"
- Très prisé sur les marchés noirs (dark web)

### Botnet
- Réseau de machines compromises (**bots/zombies**) contrôlées à distance via un **C&C (Command & Control)**
- Utilisés pour : DDoS, spam massif, minage crypto, vol de credentials
- Contrôle via IRC, HTTP, ou protocoles P2P

---

## 4. Terminologie Red / Blue / Purple Team

| Équipe | Rôle | Activités |
|---|---|---|
| **Red Team** | Sécurité **offensive** — simule l'attaquant | Pentest, exploit, social engineering, élévation de privilèges |
| **Blue Team** | Sécurité **défensive** — protège et détecte | SOC, monitoring, SIEM, réponse à incidents, durcissement |
| **Purple Team** | **Collaboration** Red + Blue pour efficacité maximale | Partage d'informations, amélioration continue des défenses via les retours offensifs |

---

## 5. Hacking vs Ethical Hacking vs Pentest

| Notion | Définition |
|---|---|
| **Hacking** | Accès non autorisé à des systèmes pour exploitation |
| **Ethical Hacking** | Pratique large : tester la sécurité d'un réseau/système **avec autorisation** |
| **Penetration Testing** | Évaluation sécurité **structurée et contrôlée** avec scope défini, méthodologie formelle, rapport final |

> 💡 L'Ethical Hacking est plus large que le Pentest — il inclut aussi la formation, l'audit, le conseil.

---

## 6. Methodologies & Frameworks de Référence

| Framework | Objet |
|---|---|
| **OWASP** | Sécurité des applications web — Top 10 vulnérabilités |
| **PTES** (Penetration Testing Execution Standard) | Framework multi-phases pour le pentest |
| **OSSTMM** | Manuel méthodologique open-source — sécurité opérationnelle |
| **NIST** Guidelines | Standards et bonnes pratiques en cybersécurité |
| **MITRE ATT&CK** | Base de données de TTPs réelles d'adversaires (voir Partie 3) |
| **Cyber Kill Chain** | Modèle Lockheed Martin des étapes d'une cyberattaque (voir Partie 3) |
| **Diamond Model** | Modèle d'analyse d'intrusion en 4 composants (voir Partie 3) |
