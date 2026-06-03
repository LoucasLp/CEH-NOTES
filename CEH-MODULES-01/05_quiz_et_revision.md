# 📝 Module 01 — Quiz & Révision : Introduction au CEH

> **CEH v13 | EC-Council**  
> `#Quiz` `#IntroductionCEH` `#EthicalHacking` `#KillChain` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- L'ethical hacking = tests autorisés avec **accord écrit** obligatoire
- **5 phases** : Reconnaissance → Scanning → Gaining Access → Maintaining Access → Covering Tracks
- **Cyber Kill Chain** (Lockheed Martin) = 7 étapes : R-W-D-E-I-C-A
- **MITRE ATT&CK** = 14 tactiques + ~200 techniques réelles d'adversaires
- **Gray Hat** = agit sans permission mais divulgue responsablement
- **CFAA** = loi américaine criminalisant l'accès non autorisé
- **Triade CIA** : Confidentiality, Integrity, Availability

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **Ethical Hacking** | Test de sécurité légal et autorisé, avec accord écrit |
| **White Hat** | Hacker éthique autorisé, améliore la sécurité |
| **Black Hat** | Hacker malveillant, accès non autorisé, motivation criminelle |
| **Gray Hat** | Sans permission mais divulgue responsablement |
| **Script Kiddie** | Utilise des outils sans compréhension technique |
| **Nation-State Actor** | Sponsorisé par un gouvernement, très sophistiqué |
| **Kill Chain** | 7 étapes d'une cyberattaque (Lockheed Martin) |
| **MITRE ATT&CK** | Base de connaissances des TTPs d'adversaires réels |
| **IoC** | Indicators of Compromise — artefacts d'une attaque |
| **Pentest** | Test d'intrusion structuré avec scope défini |
| **Black Box** | Pentest sans information sur la cible |
| **White Box** | Pentest avec accès complet aux informations |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **L'autorisation écrite** est toujours la PREMIÈRE étape d'un pentest éthique
2. **Gray Hat** = hacker sans permission qui divulgue responsablement (pas Black Hat !)
3. **5 phases CEH** dans l'ordre : Recon → Scan → Access → Maintain → Cover
4. **Kill Chain** = Lockheed Martin, 7 étapes (R-W-D-E-I-C-A)
5. **MITRE ATT&CK** = TTPs d'adversaires documentés, 14 tactiques
6. **CIA** = Confidentiality (chiffrement), Integrity (hash), Availability (redondance)
7. **Passive recon** = sans contact direct ; **Active recon** = contact direct avec la cible
8. **CFAA** = loi américaine anti-intrusion ; **GDPR** = Europe (données personnelles)
9. **Pentest ≠ VA** : Pentest exploite les failles ; VA les identifie seulement
10. **Black/White/Grey Box** : la boîte = niveau d'information fourni à l'auditeur

---

## ❓ Questions de révision

1. Quelle est la différence fondamentale entre un hacker Black Hat et un Gray Hat ?
2. Dans quel ordre se déroulent les 5 phases du hacking éthique CEH ?
3. Expliquez la différence entre Cyber Kill Chain et MITRE ATT&CK.
4. Pourquoi une autorisation écrite est-elle indispensable avant tout pentest ?
5. Quelle est la différence entre Vulnerability Assessment et Penetration Test ?
6. Quels sont les 3 types de "boîtes" en pentest et quand utiliser chacune ?
7. Quelle loi américaine criminalise l'accès non autorisé aux systèmes informatiques ?
8. Qu'est-ce qu'un APT et comment se distingue-t-il d'un hacker individuel ?

---

## 📋 Quiz QCM

**Question 1.** John découvre une faille de sécurité sur un site web sans y être autorisé, puis contacte l'entreprise pour la signaler. Quel type de hacker est-il ?
- a) White Hat
- b) Black Hat
- c) Gray Hat
- d) Script Kiddie

**Question 2.** Quelle est la PREMIÈRE phase du hacking éthique selon la méthodologie CEH ?
- a) Scanning
- b) Gaining Access
- c) Reconnaissance
- d) Covering Tracks

**Question 3.** Le framework MITRE ATT&CK est principalement utilisé pour :
- a) Chiffrer les communications réseau
- b) Scanner les ports ouverts d'un système
- c) Répertorier les TTPs (Tactics, Techniques, Procedures) réels d'adversaires
- d) Évaluer le score CVSS d'une vulnérabilité

**Question 4.** La Cyber Kill Chain a été développée par :
- a) MITRE Corporation
- b) EC-Council
- c) Lockheed Martin
- d) NIST

**Question 5.** Un test de pénétration de type "Black Box" signifie :
- a) Le testeur a accès complet au code source et à l'architecture
- b) Le testeur dispose de quelques informations sur la cible
- c) Le testeur n'a aucune information préalable sur la cible
- d) Le test se déroule la nuit (en dehors des heures de bureau)

**Question 6.** Quelle loi américaine criminalise spécifiquement l'accès non autorisé aux systèmes informatiques ?
- a) GDPR
- b) HIPAA
- c) CFAA
- d) PCI-DSS

**Question 7.** Lequel des éléments suivants est un indicateur de compromission (IoC) ?
- a) Une politique de sécurité documentée
- b) Un certificat SSL valide
- c) Des connexions réseau inhabituelles vers des IPs externes inconnues
- d) Un scan Nmap régulier du réseau

**Question 8.** Quel pilier de la triade CIA est ciblé par une attaque DoS/DDoS ?
- a) Confidentiality
- b) Integrity
- c) Availability
- d) Authentication

**Question 9.** La 6ème étape de la Cyber Kill Chain est :
- a) Installation
- b) Exploitation
- c) Command & Control (C2)
- d) Actions on Objectives

**Question 10.** Un pentest "White Box" offre quel avantage principal ?
- a) Il simule parfaitement un attaquant externe réel
- b) Il permet un audit approfondi car le testeur connaît l'architecture
- c) Il ne nécessite aucune autorisation écrite
- d) Il est moins coûteux qu'un Black Box

---

## ✅ Points de révision — Checklist

- [ ] Je connais les 8 types de hackers (White, Black, Gray, Script Kiddie, Hacktivist, Nation-State, Insider, Cyber Terrorist)
- [ ] Je peux citer les 5 phases CEH dans l'ordre exact
- [ ] Je connais les 7 étapes de la Kill Chain et le mémo R-W-D-E-I-C-A
- [ ] Je comprends la différence MITRE ATT&CK vs Cyber Kill Chain
- [ ] Je sais ce qu'est un IoC et je peux en citer 3 exemples
- [ ] Je connais les 3 piliers CIA et leurs mécanismes
- [ ] Je comprends la différence Pentest / VA / Red Team
- [ ] Je sais distinguer Black Box / White Box / Grey Box
- [ ] Je connais CFAA, GDPR, HIPAA et leur portée
- [ ] Je comprends pourquoi l'autorisation écrite est non négociable

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **c** | Gray Hat = accès sans permission mais divulgation responsable |
| 2 | **c** | Reconnaissance = toujours la première phase |
| 3 | **c** | MITRE ATT&CK = base de TTPs documentées d'adversaires réels |
| 4 | **c** | Kill Chain = Lockheed Martin (adaptation du concept militaire) |
| 5 | **c** | Black Box = aucune info fournie au testeur |
| 6 | **c** | CFAA = Computer Fraud and Abuse Act (USA) |
| 7 | **c** | Connexions inhabituelles = indicateur de compromission |
| 8 | **c** | DoS/DDoS cible la Disponibilité (Availability) |
| 9 | **c** | Kill Chain étape 6 = Command & Control |
| 10 | **b** | White Box = testeur connaît l'archi → audit plus complet |

---

*⬅️ [Cadre légal et éthique](./04_cadre_legal_ethique.md) | ➡️ [Mémo examen](./00_memo_examen.md)*
