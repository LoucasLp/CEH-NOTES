# 🛡️ CEH v13 — Module 09 | Partie 5 : Contremesures & Outils
> **Défenses contre le Social Engineering**

---

## 1. Stratégie de Défense Globale

> ⚠️ **Principe fondamental** : Aucune technologie seule ne peut protéger d'une attaque de social engineering. La défense doit être **humaine, organisationnelle et technique**.

```
┌─────────────────────────────────────────────────────────┐
│              DÉFENSE EN PROFONDEUR                       │
├────────────────┬────────────────┬───────────────────────┤
│  HUMAIN        │  ORGANISATIONNEL│  TECHNIQUE            │
├────────────────┼────────────────┼───────────────────────┤
│  Formation     │  Politiques    │  Filtrage email        │
│  Sensibilisation│  Procédures   │  MFA                   │
│  Culture sécu  │  Audit régulier│  Anti-phishing         │
│  Reporting     │  Gestion accès │  DLP / UEBA            │
└────────────────┴────────────────┴───────────────────────┘
```

---

## 2. Contremesures Organisationnelles

### 2.1 Politique de Sécurité

- Définir des **politiques claires** sur le traitement des informations sensibles
- Établir des **procédures de vérification d'identité** pour toute demande inhabituelle
- Mettre en place un **processus d'approbation** pour les virements financiers
- Politique de **clean desk** : pas d'informations sensibles laissées à vue
- Procédure de **destruction des documents** (broyage, déchiquetage)

### 2.2 Formation et Sensibilisation — La Défense #1

> **La formation est la contremesure la plus efficace contre le social engineering.**

**Programme de sensibilisation :**

| Élément | Description |
|---------|-------------|
| **Simulations de phishing** | Envoyer de faux phishing aux employés et mesurer le taux de clic |
| **Formation régulière** | Sessions de sensibilisation trimestrielles minimum |
| **Exercices de Red Team** | Tester les employés avec de vraies techniques de SE |
| **Politique de signalement** | Procédure simple pour reporter un incident suspect |
| **Gamification** | Jeux, quiz, challenges pour renforcer l'apprentissage |

**Sujets à couvrir en formation :**
- Reconnaître un e-mail de phishing
- Vérifier l'identité d'un appelant
- Ne jamais divulguer de mot de passe (même au "helpdesk")
- Signaler les comportements suspects
- Politique sur les clés USB trouvées

---

## 3. Contremesures Techniques

### 3.1 Sécurité Email — DMARC / SPF / DKIM

> Ces protocoles protègent contre l'usurpation d'adresses email (spoofing).

```
SPF  (Sender Policy Framework)
 → Définit quels serveurs sont autorisés à envoyer pour un domaine
 → Enregistrement DNS TXT : "v=spf1 include:_spf.google.com ~all"

DKIM (DomainKeys Identified Mail)
 → Signature cryptographique attachée aux emails
 → Vérifie que l'email n'a pas été modifié en transit

DMARC (Domain-based Message Authentication)
 → Politique qui utilise SPF + DKIM
 → Indique quoi faire si les vérifications échouent
 → Valeurs : none | quarantine | reject
```

**Vérification DNS :**
```bash
# Vérifier SPF
nslookup -type=TXT entreprise.fr

# Vérifier DMARC
nslookup -type=TXT _dmarc.entreprise.fr
```

### 3.2 Multi-Factor Authentication (MFA)

> Même si un mot de passe est volé via social engineering, la MFA bloque l'accès.

| Facteur | Type | Exemple |
|---------|------|---------|
| **Ce que je sais** | Knowledge | Mot de passe, PIN |
| **Ce que je possède** | Possession | Token OTP, smartphone |
| **Ce que je suis** | Inherence | Empreinte, visage, iris |

**Méthodes MFA recommandées :**
- Application d'authentification (Google Authenticator, Authy, Microsoft Authenticator)
- Clés physiques FIDO2 (YubiKey)
- Certificats client

### 3.3 Solutions Anti-Phishing

| Outil / Solution | Fonction |
|-----------------|---------|
| **Netcraft** | Extension détectant les sites de phishing connus |
| **PhishTank** | Base de données collaborative d'URLs malveillantes |
| **VirusTotal** | Analyse d'URLs et fichiers suspects |
| **Microsoft Defender** | Filtrage email anti-phishing intégré |
| **Proofpoint / Mimecast** | Solutions entreprise de sécurité email |
| **Google Safe Browsing** | Blocage des sites malveillants connus |

### 3.4 Data Loss Prevention (DLP)

> Surveille et bloque les transferts de données sensibles non autorisés.

**Ce que le DLP surveille :**
- Emails avec pièces jointes contenant des données sensibles
- Copies sur supports USB
- Upload vers des services cloud non autorisés
- Impression de documents confidentiels

### 3.5 UEBA — Analyse Comportementale

> **User and Entity Behavior Analytics** : Détection d'anomalies comportementales.

```
Baseline normale de l'utilisateur
        ↓
Comparaison avec le comportement actuel
        ↓
Détection d'anomalie (accès nocturne, téléchargement massif)
        ↓
Alerte + intervention automatique ou manuelle
```

### 3.6 Contrôles d'Accès Physique

| Contrôle | Description |
|----------|-------------|
| **Badges RFID** | Authentification pour accès aux zones |
| **Sas de sécurité (Mantrap)** | Sas bloquant le tailgating |
| **Caméras de surveillance** | Enregistrement des accès |
| **Réceptionniste formé** | Vérification systématique des visiteurs |
| **Politique des visiteurs** | Badge visiteur, accompagnement obligatoire |

---

## 4. Procédures de Vérification d'Identité

> Tout appel ou email demandant des informations sensibles doit déclencher une **vérification d'identité indépendante**.

**Procédure recommandée :**
```
1. Recevoir une demande suspecte (email/téléphone)
2. NE PAS répondre directement
3. Raccrocher / Ne pas cliquer
4. Trouver le vrai numéro / email via un annuaire officiel
5. Rappeler/recontacter de façon indépendante
6. Vérifier la légitimité de la demande
```

**Phrase clé à appliquer :**
> *"Si vous n'avez pas sollicité la demande, vérifiez avant d'agir."*

---

## 5. Analyse des Lacunes (Gap Analysis)

> Identifier les faiblesses dans la posture de sécurité actuelle.

**Étapes :**
1. **Audit** : Évaluation des politiques et procédures existantes
2. **Tests** : Simulation d'attaques de social engineering (Red Team)
3. **Identification** : Cartographie des failles détectées
4. **Remédiation** : Mise en place de correctifs
5. **Vérification** : Nouveau test pour valider l'amélioration

---

## 6. Récapitulatif des Outils Clés

### Outils offensifs (Tests de pénétration)

| Outil | Usage | Plateforme |
|-------|-------|-----------|
| **SET (Social-Engineer Toolkit)** | Framework complet de SE | Kali Linux |
| **Shellphish** | Pages de phishing automatisées | Kali Linux |
| **Gophish** | Campagnes de phishing simulées | Multi-plateforme |
| **King Phisher** | Phishing avancé avec reporting | Linux |
| **Metasploit** | Exploitation post-phishing | Kali Linux |

### Outils défensifs

| Outil | Usage | Type |
|-------|-------|------|
| **Netcraft** | Détection phishing | Extension navigateur |
| **PhishTank** | Base de données phishing | Web |
| **VirusTotal** | Analyse URLs/fichiers | Web |
| **KnowBe4** | Formation + simulation phishing | SaaS |
| **Proofpoint** | Sécurité email entreprise | SaaS |
| **Mimecast** | Filtrage email | SaaS |

---

## 7. Checklist de Sécurité Anti-Social Engineering

```
POLITIQUE & PROCÉDURES
☐ Politique de sécurité documentée et à jour
☐ Procédure de vérification d'identité définie
☐ Politique de destruction des documents
☐ Procédure de signalement des incidents

TECHNIQUE
☐ MFA activé sur tous les comptes critiques
☐ SPF, DKIM, DMARC configurés pour le domaine
☐ Solution anti-phishing déployée
☐ DLP en place
☐ Journalisation des accès activée

HUMAIN
☐ Formation annuelle (minimum) à la cybersécurité
☐ Simulations de phishing régulières
☐ Affichage de rappels visuels dans les locaux
☐ Canal de signalement simple et connu

PHYSIQUE
☐ Contrôle d'accès par badge
☐ Politique visiteurs documentée
☐ Clean desk policy appliquée
☐ Broyeur disponible et utilisé
```

---

## 📌 Règle d'Or

> 💡 **"Vérifiez. Toujours. Indépendamment."**
>
> Face à toute demande urgente d'informations ou d'actions inhabituelles,
> raccrochez, cherchez le vrai contact, et rappelez vous-même.

---

*← [Menaces Internes](./04_menaces_internes_usurpation.md) | Suite → [Labs & Exam Tips](./06_labs_et_exam_tips.md)*
