# 02 — Systèmes de Scoring & Bases de données

> 📁 CEH v13 | Module 5 | [← Retour au README](./README.md)

---

## 🗂️ Vue d'ensemble

```
Vulnérabilité identifiée
         ↓
    CVE ID assigné  ←──── CVE (MITRE)
         ↓
    Analysée par   ←──── NVD (NIST)
         ↓
    Score CVSS     ←──── CVSS (FIRST.org)
         ↓
    Catégorisée    ←──── CWE (MITRE)
```

---

## 📊 1. CVSS — Common Vulnerability Scoring System

> 🌐 Source : [https://www.first.org](https://www.first.org)

### Qu'est-ce que le CVSS ?

Système de scoring **standardisé et open-source** pour communiquer la sévérité des vulnérabilités IT.  
Géré par **FIRST.Org, Inc** — score de **0 à 10** (10 = le plus critique).

### Les 4 groupes de métriques CVSS

---

#### 🔴 Groupe 1 : Base Metric
> Caractéristiques **intrinsèques** de la vulnérabilité, constantes dans le temps.

**Exploitability Metrics** (facilité d'exploitation) :

| Métrique | Description |
|----------|-------------|
| **Attack Vector** | Comment l'attaque est menée (réseau, local, physique) |
| **Attack Complexity** | Complexité technique requise |
| **Attack Requirements** | Prérequis nécessaires à l'attaque |
| **Privileges Required** | Niveau de privilège nécessaire |
| **User Interaction** | Interaction utilisateur requise ou non |

**Impact Metrics** (conséquences d'un exploit réussi) :

| Métrique | Description |
|----------|-------------|
| **Vulnerable System Confidentiality** | Impact sur la confidentialité du système cible |
| **Vulnerable System Integrity** | Impact sur l'intégrité du système cible |
| **Vulnerable System Availability** | Impact sur la disponibilité du système cible |
| **Subsequent System Confidentiality** | Impact sur d'autres systèmes — confidentialité |
| **Subsequent System Integrity** | Impact sur d'autres systèmes — intégrité |
| **Subsequent System Availability** | Impact sur d'autres systèmes — disponibilité |

---

#### 🟡 Groupe 2 : Threat Metric
> Caractéristiques pouvant **changer dans le temps** (mais pas selon l'environnement).

| Métrique | Description |
|----------|-------------|
| **Exploit Maturity** | Niveau de maturité des exploits disponibles |

---

#### 🟠 Groupe 3 : Environmental Metric
> Caractéristiques **propres à l'environnement** de l'organisation.

- Version modifiée des métriques d'exploitabilité et d'impact du Groupe 1
- Inclut les exigences de **confidentialité, intégrité et disponibilité** selon le contexte métier

---

#### 🔵 Groupe 4 : Supplemental Metric
> Contexte additionnel et attributs extrinsèques.

| Métrique | Description |
|----------|-------------|
| **Automatable** | L'exploitation peut-elle être automatisée ? |
| **Recovery** | Facilité de récupération après exploitation |
| **Safety** | Impact sur la sécurité physique/humaine |
| **Value Density** | Densité de valeur des ressources ciblées |
| **Vulnerability Response Effort** | Effort requis pour la réponse |
| **Provider Urgency** | Niveau d'urgence selon le fournisseur |

---

### 🎯 Scores CVSS — Traduction qualitative

| Score numérique | Sévérité | Couleur |
|-----------------|----------|---------|
| 0.0 | None | ⚪ |
| 0.1 – 3.9 | Low | 🟢 |
| 4.0 – 6.9 | Medium | 🟡 |
| 7.0 – 8.9 | High | 🟠 |
| 9.0 – 10.0 | **Critical** | 🔴 |

> 💡 **Rappel exam :** Le score est généré sous forme de **vecteur string** (bloc de texte).  
> Deux usages principaux : **priorisation de la remédiation** et **calcul de sévérité**.

---

## 🏷️ 2. CVE — Common Vulnerabilities and Exposures

> 🌐 Source : [https://cve.mitre.org](https://cve.mitre.org)

### Caractéristiques

| Point | Détail |
|-------|--------|
| **Gestion** | MITRE Corporation, avec support de US-CERT |
| **Attribution** | CVE IDs assignés par les **CNA** (CVE Numbering Authorities) mondiales |
| **Accès** | Gratuit, public, téléchargeable |
| **Nature** | Dictionnaire (pas une base de données) |

### Ce qu'est le CVE ✅

```
✓ Un identifiant unique par vulnérabilité
✓ Une description standardisée pour chaque vulnérabilité
✓ Un dictionnaire (≠ base de données)
✓ Un langage commun entre outils et bases de données
✓ La base d'interopérabilité en cybersécurité
✓ Un outil d'évaluation comparative de couverture
✓ Gratuit et open-source
✓ Endorsé par l'industrie (CNAs, CVE Board)
```

### Format d'un CVE ID

```
CVE-[ANNÉE]-[NUMÉRO]
Exemple : CVE-2024-12345
```

> 🔗 Les produits compatibles CVE offrent **meilleure couverture**, **interopérabilité** et **sécurité renforcée**.

---

## 🏛️ 3. NVD — National Vulnerability Database

> 🌐 Source : [https://nvd.nist.gov](https://nvd.nist.gov)  
> Géré par le **NIST** (National Institute of Standards and Technology)

### Fonctionnement

```
CVE publié dans CVE Dictionary
          ↓
Analyse par le NVD Staff
          ↓
Agrégation : description + références + données publiques
          ↓
Attribution de :
  • Score CVSS (impact metrics)
  • Type de faiblesse (CWE)
  • Déclarations d'applicabilité (CPE)
  • Métadonnées complémentaires
```

### Contenu de la NVD

- Références de checklists de sécurité
- Failles logicielles liées à la sécurité
- Mauvaises configurations
- Noms de produits
- Métriques d'impact

### Protocole utilisé : SCAP

**Security Content Automation Protocol** — permet l'automatisation de :
- La gestion des vulnérabilités
- La mesure de sécurité
- La conformité

> ⚠️ Le NVD **ne teste pas activement** les vulnérabilités — il s'appuie sur les vendeurs, chercheurs et coordinateurs.

---

## 🗃️ 4. CWE — Common Weakness Enumeration

> 🌐 Source : [https://cwe.mitre.org](https://cwe.mitre.org)  
> Sponsorisé par **National Cybersecurity FFRDC / MITRE**, avec support US-CERT et DHS

### Caractéristiques

| Point | Détail |
|-------|--------|
| **Version actuelle** | 3.2+ (publiée janvier 2019) |
| **Nombre de catégories** | +600 types de faiblesses |
| **Usage** | Baseline pour identification, mitigation et prévention |

### Concepts de recherche dans CWE

```
Recherche par :
├── Research Concepts    → Vue théorique/académique
├── Development Concepts → Vue développeur/code
└── Architectural Concepts → Vue architecture système
```

> 💡 **Différence CVE vs CWE :**  
> - **CVE** = instance spécifique d'une vulnérabilité dans un produit  
> - **CWE** = type/catégorie de faiblesse (ex: "Buffer Overflow", "SQL Injection")

---

## 🔗 Tableau comparatif des 4 systèmes

| Système | Géré par | Rôle | URL |
|---------|----------|------|-----|
| **CVSS** | FIRST.org | Scorer la sévérité | first.org |
| **CVE** | MITRE | Identifier uniquement chaque vulnérabilité | cve.mitre.org |
| **NVD** | NIST (US Gov) | Base de données enrichie des CVEs | nvd.nist.gov |
| **CWE** | MITRE/DHS | Catégoriser les types de faiblesses | cwe.mitre.org |

---

## 📝 Points clés pour l'examen CEH

> 💡 **Questions fréquentes :**

| Question | Réponse |
|----------|---------|
| Score CVSS maximum ? | **10.0** (Critical) |
| Qui gère le CVE ? | **MITRE** |
| Qui gère le NVD ? | **NIST** (gouvernement US) |
| Protocole utilisé par NVD ? | **SCAP** |
| Nombre de catégories CWE ? | **+600** |
| CVE = base de données ? | **Non**, c'est un dictionnaire |
| CVSS groupes métriques ? | **4** : Base, Threat, Environmental, Supplemental |

---

[← Classification](./01-vulnerability-classification.md) | [→ Cycle de vie](./03-vulnerability-management-lifecycle.md)
