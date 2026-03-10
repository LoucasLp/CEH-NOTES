# CEH v13 — Module 1 : Introduction à l'Ethical Hacking
## Partie 1 : Sécurité de l'Information & Types de Hackers

---

## 1. Définition de l'Ethical Hacking

> L'Ethical Hacking est le processus **légal et autorisé** de sonder des systèmes, applications et réseaux pour identifier des vulnérabilités exploitables — dans le but de les corriger.

- L'ethical hacker simule des attaques réelles selon une **méthodologie structurée**
- **Différence fondamentale** avec un hacker malveillant : **consentement + intention**
- L'ethical hacker opère **toujours avec une autorisation écrite** (scope of engagement)
- EC-Council (fondé 2001) délivre la certification CEH — neutralité vis-à-vis des vendors

---

## 2. Éléments clés de la Sécurité de l'Information

### Triade CIA — Le socle fondamental

| Pilier | Définition | Mécanismes |
|---|---|---|
| **Confidentiality** (Confidentialité) | Protéger les données contre l'accès non autorisé | Chiffrement, ACL, authentification forte |
| **Integrity** (Intégrité) | Garantir l'exactitude et la fiabilité des données | Hashing (MD5, SHA), signatures numériques |
| **Availability** (Disponibilité) | Assurer l'accès aux données/services quand nécessaire | Redondance, failover, sauvegardes, DDoS protection |

### Propriétés complémentaires

| Propriété | Définition |
|---|---|
| **Authentication** | Vérification de l'identité (MDP, MFA, biométrie) |
| **Authorization** | Attribution des droits après authentification |
| **Non-repudiation** | Impossibilité de nier une action (logs, certificats numériques) |
| **Accountability** | Traçabilité des actions via audits et logs |

---

## 3. Terminologie de Sécurité — Définitions Clés

| Terme | Définition précise |
|---|---|
| **Threat (Menace)** | Danger potentiel pouvant exploiter une vulnérabilité sur un actif |
| **Vulnerability (Vulnérabilité)** | Faiblesse dans un système, application ou réseau exploitable |
| **Exploit** | Code ou technique utilisé pour tirer parti d'une vulnérabilité |
| **Risk (Risque)** | **Probabilité × Impact** d'une menace exploitant une vulnérabilité |
| **Attack Surface** | Ensemble des points d'entrée potentiels pour un attaquant |
| **Attack Vector** | Chemin/moyen utilisé pour attaquer (phishing, malware, cloud misconfig...) |
| **Payload** | Code malveillant exécuté après la compromission d'un système |

---

## 4. Types de Hackers

| Type | Caractéristiques | Motivation |
|---|---|---|
| **White Hat (Ethical Hacker)** | Autorisation légale, pentesters, red teamers | Améliorer la sécurité des organisations |
| **Black Hat** | Accès non autorisé, criminel | Gain financier, vol de données, sabotage |
| **Gray Hat** | Entre légal et illégal — découvre des failles sans permission mais peut les divulguer | Variable (gloire, argent, responsabilité) |
| **Script Kiddie** | Utilise des outils existants sans compétences techniques réelles | Reconnaissance, amusement |
| **Hacktivist** | Attaques motivées par des causes politiques/sociales | Activisme (Anonymous, etc.) |
| **Nation-State Actor** | Sponsorisé par un gouvernement, très bien financé | Espionnage, sabotage d'infrastructures critiques |
| **Insider (Initié)** | Employé ou contractant abusant de ses privilèges | Rancœur, corruption, coercition |
| **Cyber Terrorist** | Motivé par des croyances religieuses ou politiques extrêmes | Terreur, déstabilisation |
| **Cracker** | Utilise les outils à des fins destructrices ou de gain personnel | Profit illicite |

> 💡 **Question type exam** : *"John accède à un système sans permission mais divulgue ensuite la faille responsablement. Quel type de hacker est-il ?"* → **Gray Hat**

---

## 5. Information Assurance (IA)

- L'IA désigne les pratiques visant à gérer les **risques liés à l'utilisation, traitement, stockage et transmission** de l'information
- Englobe : politiques de sécurité, formation, contrôles techniques, continuité d'activité
- Basée sur les 5 piliers : **Disponibilité, Intégrité, Authentification, Confidentialité, Non-répudiation**

---

## 6. Contrôles de Sécurité

### Par type d'implémentation

| Type | Exemples |
|---|---|
| **Administratifs** | Politiques de sécurité, formations, vérifications d'antécédents, procédures |
| **Techniques** | Firewalls, IDS/IPS, antivirus, DLP, chiffrement, MFA |
| **Physiques** | CCTV, serrures, accès biométrique, gardes, cages de Faraday |

### Par fonction

| Fonction | Rôle | Exemples |
|---|---|---|
| **Preventive** | Empêcher les incidents | Firewall, serrures, chiffrement |
| **Detective** | Identifier les incidents | IDS, monitoring, audit logs |
| **Corrective** | Corriger les dommages post-incident | Patches, antivirus, backups |
| **Deterrent** | Dissuader les attaquants | Panneaux d'avertissement, monitoring visible |

---

## 7. Lois, Standards et Éthique

| Loi / Standard | Périmètre | Objet |
|---|---|---|
| **CFAA** (Computer Fraud and Abuse Act) | USA | Criminalise l'accès non autorisé aux systèmes |
| **GDPR** | Europe | Protection des données personnelles |
| **HIPAA** | USA (santé) | Protection des données médicales |
| **PCI-DSS** | International | Sécurité des données de cartes bancaires |
| **IT Act 2000** | Inde | Cybercriminalité et transactions électroniques |
| **ISO/IEC 27001** | International | Standard SMSI (Système de Management de la Sécurité) |
| **NIST CSF** | USA | Framework de cybersécurité (Identify, Protect, Detect, Respond, Recover) |
| **SOX** (Sarbanes-Oxley) | USA (finance) | Contrôle et reporting financier, audit trail |

> ⚠️ **Règle absolue CEH** : toujours opérer sous **accord écrit** définissant le scope avant tout test.
