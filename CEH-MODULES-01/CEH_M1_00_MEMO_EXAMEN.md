# CEH v13 — Module 1 : FICHE MÉMO EXAMEN

---

## Les 5 Phases du Hacking — Dans l'ordre ⭐

```
1. RECONNAISSANCE → 2. SCANNING → 3. GAINING ACCESS → 4. MAINTAINING ACCESS → 5. COVERING TRACKS
```

| Phase | Mots-clés | Outils |
|---|---|---|
| Reconnaissance | OSINT, WHOIS, DNS, passive vs active | Maltego, Shodan, theHarvester |
| Scanning | Ports, services, OS, vulnérabilités | Nmap, Nessus, Angry IP Scanner |
| Gaining Access | Exploit, password cracking, SQLi, buffer overflow | Metasploit, Hydra, SQLmap |
| Maintaining Access | Backdoor, rootkit, trojan, RAT | Netcat, Meterpreter |
| Covering Tracks | Logs clearing, stéganographie, rootkit | Auditpol, timestomp |

---

## Cyber Kill Chain — 7 étapes ⭐

| # | Phase | Mémo 1 mot |
|---|---|---|
| 1 | Reconnaissance | **Chercher** |
| 2 | Weaponization | **Forger** |
| 3 | Delivery | **Envoyer** |
| 4 | Exploitation | **Déclencher** |
| 5 | Installation | **Installer** |
| 6 | C2 (Command & Control) | **Contrôler** |
| 7 | Actions on Objectives | **Agir** |

> Mémo : **R-W-D-E-I-C-A**

---

## Triade CIA + Propriétés — Mémo rapide

| Pilier | En 1 mot | Mécanisme clé |
|---|---|---|
| **Confidentiality** | Secret | Chiffrement, ACL |
| **Integrity** | Fiabilité | Hash (SHA, MD5), signatures |
| **Availability** | Accès | Redondance, backups |
| **Authentication** | Identité | MFA, biométrie |
| **Non-repudiation** | Preuve | Logs, certificats numériques |

---

## Types de Hackers — Mémo rapide

| Type | 1 phrase |
|---|---|
| **White Hat** | Autorisé, améliore la sécurité |
| **Black Hat** | Non autorisé, motivation criminelle |
| **Gray Hat** | Sans permission mais divulgue responsablement |
| **Script Kiddie** | Utilise des outils sans comprendre |
| **Hacktivist** | Attaque pour une cause politique/sociale |
| **Nation-State** | Gouvernement, espionnage, très sophistiqué |
| **Insider** | Employé abusant de ses accès légitimes |
| **Cyber Terrorist** | Motivé par l'idéologie, vise la déstabilisation |

---

## Contrôles de Sécurité — Classification

### Par type
- **Administratif** : Politiques, procédures, formation
- **Technique** : Firewall, IDS/IPS, antivirus, chiffrement
- **Physique** : Serrures, CCTV, badges, biométrie

### Par fonction
- **Preventive** → Empêche (firewall, chiffrement)
- **Detective** → Détecte (IDS, logs, SIEM)
- **Corrective** → Corrige (patches, AV, backups)
- **Deterrent** → Dissuade (panneaux, monitoring visible)

---

## TTPs — Mémo

| Niveau | = | Exemple |
|---|---|---|
| **Tactics** | Objectifs généraux | "Voler des données CB" |
| **Techniques** | Méthodes utilisées | XSS, Magecart, e-skimming |
| **Procedures** | Étapes détaillées + outils | Script précis, séquence d'actions |

---

## Diamond Model — 4 composants

```
    ADVERSARY
   /          \
Infrastructure — VICTIM
   \          /
    CAPABILITY
```

---

## IoC par catégorie

| Catégorie | Ce qu'on surveille |
|---|---|
| Email | Expéditeur, sujet, pièces jointes, URLs |
| Network | IPs C2, domaines, patterns trafic |
| Host | Fichiers, processus, clés registre |
| Behavioral | Connexions anormales, accès massif données |

---

## Lois & Standards — Mémo rapide

| Loi/Standard | Pays/Scope | Sujet |
|---|---|---|
| CFAA | USA | Criminalise l'accès non autorisé |
| GDPR | Europe | Protection données personnelles |
| HIPAA | USA | Données médicales |
| PCI-DSS | International | Sécurité cartes bancaires |
| ISO 27001 | International | SMSI — management sécurité info |
| NIST CSF | USA | Framework cybersécurité |
| IT Act 2000 | Inde | Cybercriminalité |

---

## Questions types d'examen Module 1

| Question | Réponse |
|---|---|
| Hacker accède sans permission mais divulgue responsablement → ? | **Gray Hat** |
| Première phase du hacking éthique → ? | **Reconnaissance** |
| Pilier CIA qui garantit que les données ne sont pas altérées → ? | **Integrity** |
| Quel contrôle empêche une attaque avant qu'elle survienne → ? | **Preventive** |
| Quel framework décrit les 7 étapes d'une cyberattaque → ? | **Cyber Kill Chain** |
| Quel framework répertorie les TTPs réelles d'adversaires → ? | **MITRE ATT&CK** |
| Différence entre ethical hacking et pentest → ? | Ethical hacking est plus large (inclut audit, formation) ; pentest est structuré avec scope défini |
| Quel est l'élément fondamental avant tout test éthique → ? | **Autorisation écrite** (written scope of engagement) |
| Attaque qui observe sans modifier → ? | **Passive Attack** (sniffing) |
| Attaque par un employé interne → ? | **Insider Attack** |
| Réseau de machines compromises contrôlées à distance → ? | **Botnet** |
| Exploit sur une faille non encore patchée → ? | **Zero-Day Exploit** |
