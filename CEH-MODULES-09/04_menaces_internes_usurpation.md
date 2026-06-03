# 🔐 CEH v13 — Module 09 | Partie 4 : Menaces Internes & Usurpation d'Identité
> **Insider Threats & Identity Theft**

---

## 1. Les Menaces Internes (Insider Threats)

> **Définition** : Risque de sécurité provenant de personnes **internes à l'organisation** — employés, anciens employés, sous-traitants ou partenaires — qui ont un accès légitime aux systèmes et ressources.

---

### 1.1 Pourquoi les Insiders sont dangereux ?

```
┌─────────────────────────────────────────────────────┐
│         AVANTAGES D'UN INSIDER MALVEILLANT           │
├─────────────────────────────────────────────────────┤
│  ✓ Accès légitime aux systèmes et données           │
│  ✓ Connaissance des procédures internes             │
│  ✓ Confiance accordée par défaut                    │
│  ✓ Contourne les contrôles de sécurité externes     │
│  ✓ Peut agir sur la durée sans éveiller soupçons    │
└─────────────────────────────────────────────────────┘
```

---

### 1.2 Types d'Insiders

| Type | Description | Motivation |
|------|-------------|-----------|
| **Malicious Insider** | Agit délibérément contre l'organisation | Vengeance, gain financier, idéologie |
| **Negligent Insider** | Cause des dommages par inadvertance | Erreur humaine, manque de formation |
| **Compromised Insider** | Contrôlé par un acteur externe | Chantage, manipulation, phishing réussi |
| **Turncloack** | Recruté par un concurrent ou État | Espionnage industriel / étatique |

---

### 1.3 Indicateurs comportementaux d'un Insider Malveillant

**Signes avant-coureurs :**

| Catégorie | Indicateurs |
|-----------|-------------|
| **Accès** | Accès à des ressources hors de son périmètre, connexions nocturnes |
| **Comportement** | Mécontentement exprimé, conflits avec la hiérarchie |
| **Données** | Téléchargements massifs, copie sur supports externes |
| **Réseau** | Trafic inhabituel vers des destinations inconnues |
| **Physique** | Présence en dehors des horaires normaux |

---

### 1.4 Scénarios d'Attaque Insider

**Scénario 1 — Vol de propriété intellectuelle :**
```
Employé sur le départ → copie les bases de données clients
→ Revend à un concurrent
```

**Scénario 2 — Sabotage :**
```
Employé licencié → installe une bombe logique
→ Déclenchement après son départ
```

**Scénario 3 — Compromission involontaire :**
```
Employé clique sur un email de phishing
→ Ses credentials sont volés
→ L'attaquant extérieur opère depuis l'intérieur
```

---

### 1.5 Contremesures contre les Insiders

- **Principe du moindre privilège** : N'accorder que les droits strictement nécessaires
- **Séparation des tâches** : Aucun employé ne doit contrôler seul un processus critique
- **DLP (Data Loss Prevention)** : Surveiller et bloquer les transferts de données sensibles
- **UEBA (User and Entity Behavior Analytics)** : Détecter les anomalies comportementales
- **PAM (Privileged Access Management)** : Contrôler les comptes à hauts privilèges
- **Journalisation et audit** : Enregistrement de toutes les actions sensibles
- **Procédure de départ** : Révocation immédiate des accès à la fin du contrat

---

## 2. L'Usurpation d'Identité (Identity Theft)

> **Définition** : Vol et utilisation frauduleuse de l'identité d'une personne pour réaliser des activités illégales — fraudes financières, accès à des systèmes, escroqueries.

---

### 2.1 Données Ciblées

```
┌─────────────────────────────────────────────────────┐
│           DONNÉES D'IDENTITÉ RECHERCHÉES             │
├─────────────────────────────────────────────────────┤
│  📋 Nom complet, date et lieu de naissance          │
│  🪪 Numéro de sécurité sociale / CNI / Passeport    │
│  💳 Numéros de carte bancaire, codes                │
│  🏦 Coordonnées bancaires (IBAN, BIC)               │
│  📧 Adresses email + mots de passe                  │
│  🏠 Adresse postale, numéro de téléphone            │
│  🩺 Données médicales / dossiers de santé           │
│  💼 Informations professionnelles                   │
└─────────────────────────────────────────────────────┘
```

---

### 2.2 Méthodes de Collecte des Données d'Identité

| Méthode | Description |
|---------|-------------|
| **Phishing** | Vol de credentials par e-mail frauduleux |
| **Dumpster Diving** | Relevés bancaires, courriers jetés non détruits |
| **Data Breach** | Exploitation de bases de données piratées |
| **Skimming** | Lecteur piégé sur un distributeur automatique |
| **Shoulder Surfing** | Observation directe (codes, numéros) |
| **Vishing** | Appel téléphonique frauduleux |
| **Malware / Spyware** | Keylogger enregistrant les frappes clavier |
| **OSINT / Réseaux Sociaux** | Collecte d'informations publiques en ligne |
| **Fake Surveys / Quizzes** | Sondages en ligne collectant des données personnelles |

---

### 2.3 Usages Frauduleux de l'Identité Volée

```
Identité volée
     │
     ├── Fraude financière
     │    ├── Ouverture de comptes bancaires
     │    ├── Demandes de crédit
     │    └── Achats en ligne frauduleux
     │
     ├── Accès aux systèmes
     │    ├── Connexion aux services en ligne de la victime
     │    └── Ingénierie sociale facilitée (crédibilité renforcée)
     │
     ├── Fraude fiscale / administrative
     │    └── Déclarations d'impôts frauduleuses
     │
     └── Usurpation sur les réseaux sociaux
          └── Arnaque aux proches de la victime
```

---

### 2.4 Impersonation sur les Réseaux Sociaux

> L'attaquant crée un **faux profil** imitant une personne réelle ou une organisation.

**Objectifs :**
- Collecter des informations sur les contacts
- Envoyer des liens malveillants via des messages "de confiance"
- Usurper l'identité professionnelle (LinkedIn) pour du spear phishing
- Arnaques sentimentales (romance scam)

**Plateformes visées :**
- LinkedIn → Espionnage industriel, BEC
- Facebook / Instagram → Arnaques personnelles
- Twitter / X → Désinformation, phishing de masse

---

### 2.5 Video Cloning & Voice Cloning (CEH v13 — IA)

> **Nouveauté CEH v13** : L'IA permet désormais de cloner une apparence ou une voix de façon convaincante.

| Technique IA | Description | Impact |
|-------------|-------------|--------|
| **Voice Cloning** | Reproduire la voix de n'importe qui à partir d'un échantillon audio | Appels frauduleux convaincants |
| **Deepfake Vidéo** | Générer de fausses vidéos avec le visage d'une personne réelle | Fraude aux dirigeants, désinformation |
| **AI Chatbots** | Simuler des conversations naturelles et personnalisées | Phishing conversationnel |

**Exemple réel** : En 2024, une entreprise hongkongaise a perdu **25 millions USD** suite à une réunion Zoom avec de faux deepfakes de collègues.

---

### 2.6 Contremesures contre l'Usurpation d'Identité

**Pour les individus :**
- Activer la **MFA (Authentification Multi-Facteurs)**
- Surveiller ses relevés bancaires régulièrement
- Ne pas oversharer sur les réseaux sociaux
- Utiliser un gestionnaire de mots de passe
- Vérifier les URLs avant de saisir ses identifiants

**Pour les organisations :**
- Mettre en place des **processus de vérification d'identité** renforcés
- Former les équipes à reconnaître les deepfakes et voice cloning
- Implémenter des **mot de passe de confirmation** pour les virements (ex : phrase secrète)
- Utiliser des solutions **anti-phishing** (DMARC, SPF, DKIM)

---

## 📌 Récapitulatif

| Concept | Points Clés |
|---------|-------------|
| **Insider Threat** | Menace interne, 4 types : Malicious / Negligent / Compromised / Turncloack |
| **Identity Theft** | Vol d'identité via phishing, dumpster diving, data breach, malware |
| **Indicateurs Insider** | Accès anormaux, téléchargements massifs, comportement suspect |
| **Deepfakes (v13)** | IA pour cloner voix et visage → amplification des attaques |
| **Contremesures** | Moindre privilège, MFA, DLP, UEBA, sensibilisation |

---

*← [Techniques Informatiques](./03_techniques_informatiques.md) | Suite → [Contremesures & Outils](./05_contremesures_outils.md)*
