# 📚 CEH v13 — Module 05 : Vulnerability Analysis

> **Certified Ethical Hacker v13 — AI Powered**  
> EC-Council | Formation officielle

---

## 🗂️ Structure des notes

| Fichier | Contenu |
|--------|---------|
| [`01-vulnerability-classification.md`](./01-vulnerability-classification.md) | Types, catégories (design, OS, app), exemples concrets |
| [`02-scoring-systems.md`](./02-scoring-systems.md) | CVSS v4, CVE, NVD, CWE — scores et seuils |
| [`03-vulnerability-management-lifecycle.md`](./03-vulnerability-management-lifecycle.md) | Phases Pre-Assessment / Assessment / Post-Assessment |
| [`04-vulnerability-scanning.md`](./04-vulnerability-scanning.md) | Types de scans, actif vs passif, external vs internal |
| [`05-assessment-tools.md`](./05-assessment-tools.md) | Nessus, OpenVAS, Qualys, Nexpose, outils IA |
| [`06-assessment-reports.md`](./06-assessment-reports.md) | Structure d'un rapport VA, composants, livrables |
| [`07-Quiz-et-Revision.md`](./07-Quiz-et-Revision.md) | Questions, rappels, points clés d'examen |

---

## 🎯 Objectifs du module

À l'issue de ce module, vous serez capable de :

- ✅ Classifier les vulnérabilités par type (design, OS, applicatif)
- ✅ Utiliser et interpréter les scores CVSS (v3/v4), CVE et CWE
- ✅ Appliquer le cycle de gestion des vulnérabilités (pre/assessment/post)
- ✅ Distinguer scan actif, passif, externe, interne
- ✅ Utiliser Nessus, OpenVAS et Qualys pour les scans
- ✅ Rédiger un rapport de vulnerability assessment professionnel

---

## 📊 Poids dans l'examen CEH (312-50)

```
Module 05 — Vulnerability Analysis
Poids estimé : ~3-5% des questions de l'examen
Nb de labs officiels : ~4 labs dans la plateforme iLabs/CyberQ
Thèmes récurrents : CVSS scores (seuils 4.0/7.0/9.0),
                    CVE format, Nessus vs OpenVAS,
                    cycle de gestion des vulnérabilités
```

---

## 🔗 Ressources complémentaires

- [EC-Council Official CEHv13](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/)
- [CyberQ Platform (Labs)](https://cyberq.eccouncil.org)
- [NVD (National Vulnerability Database)](https://nvd.nist.gov/)
- [CVE Search](https://cve.mitre.org/)
- [Tenable Nessus](https://www.tenable.com/products/nessus)
- [OpenVAS / Greenbone](https://www.openvas.org/)

---

## 🧠 Mémo rapide — Scores CVSS

```
Sévérité CVSS v3/v4 (NVD) :
  None     : 0.0
  Low      : 0.1 – 3.9
  Medium   : 4.0 – 6.9
  High     : 7.0 – 8.9
  Critical : 9.0 – 10.0

4 métriques CVSS v4 :
  Base  → Caractéristiques intrinsèques de la vulnérabilité
  Threat → Disponibilité d'un exploit actif
  Environmental → Impact sur l'organisation spécifique
  Supplemental → Informations contextuelles additionnelles
```

---

*Notes rédigées dans le cadre de la préparation à la certification CEH v13 — EC-Council*  
*Format : Markdown — Compatible GitHub Pages*
