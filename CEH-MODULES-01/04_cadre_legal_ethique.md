# 🔴 Module 01 — Cadre Légal, Éthique et Types de Tests

> **CEH v13 | EC-Council**  
> `#EthicalHacking` `#ScopeOfEngagement` `#PenTest` `#Lois` `#Autorisation`

---

## 📌 Table des matières

1. [Éthique du hacking — Code of Conduct CEH](#1-éthique-ceh)
2. [Types de tests de sécurité](#2-types-de-tests)
3. [Boîtes de test : Black / White / Grey Box](#3-boites-de-test)
4. [Scope of Engagement et Rules of Engagement](#4-scope-et-roe)
5. [Cadre légal international](#5-cadre-légal)
6. [Types de hackers et motivations (synthèse examen)](#6-synthèse-examen)

---

## 1. Éthique du Hacking — Code of Conduct CEH

```
L'ethical hacker doit :
  ✓ Toujours obtenir une autorisation ÉCRITE avant tout test
  ✓ Respecter le scope défini (périmètre, systèmes, durée)
  ✓ Protéger la confidentialité des données découvertes
  ✓ Signaler toutes les vulnérabilités découvertes
  ✓ Ne pas nuire ni endommager les systèmes testés
  ✓ Ne jamais dépasser le périmètre autorisé
  
L'ethical hacker NE doit PAS :
  ✗ Installer des backdoors pour « revenir plus tard »
  ✗ Partager des résultats avec des tiers non autorisés
  ✗ Utiliser les accès obtenus à des fins personnelles
  ✗ Conserver des données sensibles après le test
```

> ⚠️ **Rappel CEH** : La **seule** différence entre un hacker éthique et un hacker malveillant est **l'autorisation écrite**. Tout test sans permission est une infraction pénale.

---

## 2. Types de Tests de Sécurité

| Type | Description | Exemple |
|------|-------------|---------|
| **Vulnerability Assessment** | Identifier et classer les vulnérabilités (sans exploitation) | Scan Nessus, rapport CVE |
| **Penetration Testing** | Exploiter les vulnérabilités pour démontrer l'impact | Metasploit, exploitation réelle |
| **Red Team Exercise** | Simulation d'APT sur longue durée, adversaire réaliste | Red team vs Blue team |
| **Security Audit** | Vérification de conformité aux standards (ISO, PCI-DSS) | Audit ISO 27001 |
| **Bug Bounty** | Programme de récompense pour découverte de vulnérabilités | HackerOne, Bugcrowd |

### Différence Pentest vs Red Team

```
Pentest :
  ├── Durée : quelques jours/semaines
  ├── Périmètre : défini et fixe (scope contractuel)
  ├── Objectif : trouver un maximum de vulnérabilités
  └── Rapport : liste exhaustive avec remédiation

Red Team :
  ├── Durée : semaines/mois
  ├── Périmètre : large, parfois "objectif" plutôt que scope
  ├── Objectif : simuler un adversaire réel, tester les défenses
  └── Rapport : scénarios d'attaque aboutis, gaps de détection
```

---

## 3. Boîtes de Test : Black / White / Grey Box

| Type | Connaissance des infos | Réalisme | Usage |
|------|------------------------|---------|-------|
| **Black Box** | Aucune info sur la cible (simuler un attaquant externe) | Très réaliste | Test de l'exposition externe |
| **White Box** | Accès complet (code source, architecture, credentials) | Moins réaliste | Audit approfondi, pentest interne |
| **Grey Box** | Infos partielles (ex: compte utilisateur standard) | Équilibré | Test de l'impact d'un compte compromis |

```
Black Box → attaquant extérieur sans info
Grey Box  → attaquant avec un compte utilisateur volé
White Box → auditeur interne avec accès total
```

---

## 4. Scope of Engagement (SoE) et Rules of Engagement (RoE)

### Scope of Engagement

```
Document contractuel définissant :
  ├── PÉRIMÈTRE : systèmes, réseaux, applications autorisées
  ├── HORS PÉRIMÈTRE : ce qui est EXPLICITEMENT interdit
  ├── DURÉE : dates de début et fin du test
  ├── MÉTHODES : techniques autorisées (scan, exploitation, SE...)
  ├── CONTACT D'URGENCE : qui appeler si incident grave
  └── GESTION DES DONNÉES : comment traiter les données sensibles trouvées
```

### Rules of Engagement

```
Règles opérationnelles plus précises :
  - Horaires autorisés pour les tests (ex: hors heures de pointe)
  - Notification préalable en cas d'exploitation critique
  - Procédure d'arrêt d'urgence
  - Gestion des systèmes tiers découverts (cloud, prestataires)
```

> ⚠️ **Rappel CEH** : Sans SoE signé, même un test bien intentionné peut être **illégal** selon le CFAA (Computer Fraud and Abuse Act).

---

## 5. Cadre Légal International

| Loi / Standard | Portée | Objet principal |
|----------------|--------|-----------------|
| **CFAA** (Computer Fraud and Abuse Act) | USA | Criminalise l'accès non autorisé aux systèmes informatiques |
| **GDPR** (General Data Protection Regulation) | Europe | Protection des données personnelles des citoyens européens |
| **HIPAA** | USA (santé) | Protection des données médicales des patients |
| **PCI-DSS** | International | Sécurité des données de cartes de paiement |
| **SOX** (Sarbanes-Oxley) | USA (finance) | Intégrité des données financières, audit trail |
| **IT Act 2000** | Inde | Cybercriminalité et signature électronique |
| **ISO/IEC 27001** | International | Système de Management de la Sécurité de l'Information (SMSI) |
| **NIST CSF** | USA | Framework cybersécurité : Identify, Protect, Detect, Respond, Recover |

### Conséquences légales d'un test non autorisé (CFAA)

```
Accès non autorisé intentionnel :
  → Peine de prison jusqu'à 20 ans (selon les dommages)
  → Amendes
  → Responsabilité civile (dommages et intérêts)

Défense possible : Autorisation écrite préalable
→ C'est pourquoi le SoE est NON NÉGOCIABLE
```

---

## 6. Synthèse — Examen CEH

### Questions-type fréquentes

```
Q : John est employé, il accède à des données client au-delà de ses droits par curiosité.
A : Insider Threat (attaque interne)

Q : Quelle est la PREMIÈRE chose à faire avant un pentest ?
A : Obtenir une autorisation écrite (scope of engagement signé)

Q : Un hacker découvre une faille critique et la divulgue publiquement sans en informer
    l'entreprise d'abord. Quel type est-il ?
A : Gray Hat (divulgation responsable non respectée → frise la zone grise)

Q : Quelle loi américaine criminalise l'accès non autorisé aux systèmes informatiques ?
A : CFAA (Computer Fraud and Abuse Act)

Q : Quel standard protège les données médicales aux USA ?
A : HIPAA

Q : Quel type de test simule un attaquant sans aucune information sur la cible ?
A : Black Box Penetration Testing
```

### Types d'assessments — récapitulatif

| Terme CEH | Ce qu'il teste | Résultat |
|-----------|---------------|---------|
| **VA** (Vulnerability Assessment) | Présence de vulnérabilités | Liste + score CVSS |
| **Penetration Test** | Exploitation réelle | Impact démontré |
| **Risk Assessment** | Risque métier global | Priorités de remédiation |
| **Security Audit** | Conformité à des standards | Pass/Fail par contrôle |

---

## 🧠 Points clés à retenir pour l'examen

- **Autorisation écrite = condition sine qua non** de tout test éthique
- **Black Box** = sans info (externe) ; **White Box** = info complète (interne) ; **Grey Box** = partiel
- **CFAA** = loi américaine anti-cybercriminalité
- **GDPR** = protection données personnelles en Europe
- **Pentest ≠ Red Team** : Pentest = scope, liste de vulnés ; Red Team = APT simulé, durée longue
- **Scope of Engagement** = contrat définissant le périmètre autorisé

---

*⬅️ [Kill Chain & MITRE](./03_killchain_mitre_ioc.md) | ➡️ [Quiz & Révision](./05_quiz_et_revision.md)*
