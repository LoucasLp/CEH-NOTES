# 🛡️ CEH v13 — Module 5 : Vulnerability Analysis

> **Certified Ethical Hacker v13** | Notes de cours structurées  
> 📅 Dernière mise à jour : Mars 2026

---

## 📋 Table des matières

| # | Fichier | Contenu |
|---|---------|---------|
| 01 | [Classification des vulnérabilités](./01-vulnerability-classification.md) | Types, catégories, exemples concrets |
| 02 | [Systèmes de scoring](./02-scoring-systems.md) | CVSS, CVE, NVD, CWE |
| 03 | [Cycle de vie de la gestion](./03-vulnerability-management-lifecycle.md) | Phases Pre/Assessment/Post |
| 04 | [Scanning & Analyse](./04-vulnerability-scanning.md) | Types de scans, outils, limites |
| 05 | [Outils d'évaluation](./05-assessment-tools.md) | Nessus, OpenVAS, Qualys, IA |
| 06 | [Rapports d'évaluation](./06-assessment-reports.md) | Structure, composants, livrables |

---

## 🎯 Objectifs du module

- Comprendre les **différentes catégories de vulnérabilités**
- Maîtriser les **systèmes de scoring** (CVSS, CVE, NVD, CWE)
- Appliquer le **cycle de vie de gestion des vulnérabilités**
- Utiliser les **outils de scanning** appropriés
- Rédiger des **rapports d'évaluation** professionnels

---

## 🧠 Concepts clés à retenir

```
Vulnérabilité → Scoring (CVSS) → Identification (CVE) → Base de données (NVD/CWE)
       ↓
Pre-Assessment → Vulnerability Assessment → Post-Assessment
       ↓
Scanning (Actif/Passif) → Analyse → Rapport → Remédiation
```

---

## ⚡ Cheat Sheet rapide

| Terme | Définition |
|-------|------------|
| **CVSS** | Score de sévérité 0-10 (Base, Threat, Environmental, Supplemental) |
| **CVE** | Identifiant unique pour chaque vulnérabilité |
| **NVD** | Base de données US gov basée sur SCAP |
| **CWE** | Catalogue de +600 types de faiblesses logicielles |
| **Zero-Day** | Vulnérabilité inconnue, non patchée |
| **Active Scan** | Interaction directe avec la cible |
| **Passive Scan** | Observation sans interaction directe |

---

> 💡 **Tip d'exam CEH :** Retenez les 4 groupes CVSS (Base, Threat, Environmental, Supplemental) et les seuils de sévérité NVD (None=0.0, Low=0.1-3.9, Medium=4.0-6.9, High=7.0-8.9, Critical=9.0-10.0)
