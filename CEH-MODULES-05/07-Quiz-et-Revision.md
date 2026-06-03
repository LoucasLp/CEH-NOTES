# 📝 Module 05 — Quiz & Révision : Vulnerability Analysis

> **CEH v13 | EC-Council**  
> `#Quiz` `#CVSS` `#CVE` `#Nessus` `#VulnerabilityManagement` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- Vulnerability Analysis = identifier, classer et prioriser les vulnérabilités **sans les exploiter**
- **CVSS** = score de sévérité 0-10 (4 groupes : Base, Threat, Environmental, Supplemental)
- **CVE** = identifiant unique (ex: CVE-2021-44228) ; **NVD** = base de données avec score CVSS
- **Nessus** = outil de scan de vulnérabilités le plus connu au CEH
- Cycle : Pre-Assessment → Assessment → Post-Assessment

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **CVE** | Common Vulnerabilities and Exposures — identifiant unique par vulnérabilité |
| **NVD** | National Vulnerability Database (NIST) — base CVSS officielle |
| **CVSS** | Score 0-10 de sévérité d'une vulnérabilité |
| **CWE** | Common Weakness Enumeration — catalogue des types de faiblesses logicielles |
| **Zero-Day** | Vulnérabilité inconnue du vendor, non patchée |
| **CVE-2021-44228** | Log4Shell — Apache Log4j (RCE critique, CVSS 10.0) |
| **Nessus** | Scanner de vulnérabilités commercial (Tenable) — référence CEH |
| **OpenVAS** | Alternative open-source à Nessus |
| **SAST** | Static Application Security Testing — analyse du code source |
| **DAST** | Dynamic Application Security Testing — tests en exécution |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **Seuils CVSS NVD** : None=0.0, Low=0.1-3.9, Medium=4.0-6.9, High=7.0-8.9, **Critical=9.0-10.0**
2. **CVSS 4 groupes** : Base (intrinsèque), Threat (exploit actif), Environmental (impact org.), Supplemental
3. **CVE format** : CVE-ANNÉE-NUMÉRO (ex: CVE-2021-44228 = Log4Shell)
4. **Nessus** = scanner commercial principal CEH ; **OpenVAS** = alternative gratuite
5. **Zero-Day** = vulnérabilité inconnue du vendor (pas de patch disponible)
6. **Pre-Assessment** = identification des assets + critères ; **Post-Assessment** = rapport + remédiation
7. **Scan actif** = interaction directe avec la cible ; **Scan passif** = observation du trafic
8. **CVE-2017-0144** = EternalBlue/MS17-010 (WannaCry) ; **CVE-2014-0160** = Heartbleed

---

## ❓ Questions de révision

1. Quelle est la différence entre CVE, NVD et CVSS ?
2. Expliquez les 4 groupes de métriques CVSS.
3. Quelle est la différence entre un scan de vulnérabilités et un pentest ?
4. Qu'est-ce qu'une vulnérabilité Zero-Day et pourquoi est-elle particulièrement dangereuse ?
5. Quelles sont les phases du cycle de gestion des vulnérabilités ?
6. Quelle est la différence entre Nessus et OpenVAS ?
7. Comment interpréter un score CVSS de 9.8 ?
8. Quel est le rôle de la phase "Post-Assessment" ?

---

## 📋 Quiz QCM

**Question 1.** Un score CVSS de 8.5 correspond à quelle sévérité selon NVD ?
- a) Medium
- b) High
- c) Critical
- d) Low

**Question 2.** CVE-2021-44228 est associé à quelle vulnérabilité critique ?
- a) Heartbleed (OpenSSL)
- b) EternalBlue (SMB)
- c) Log4Shell (Apache Log4j)
- d) Shellshock (Bash)

**Question 3.** Le groupe de métriques CVSS "Base" représente :
- a) L'impact de la vulnérabilité sur l'organisation spécifique
- b) Les caractéristiques intrinsèques et constantes de la vulnérabilité
- c) La disponibilité d'un exploit actif dans la nature
- d) Les informations contextuelles supplémentaires

**Question 4.** Quelle est la principale différence entre SAST et DAST ?
- a) SAST scanne le réseau ; DAST scanne les applications
- b) SAST analyse le code source statiquement ; DAST teste l'application en exécution
- c) SAST est commercial ; DAST est open-source
- d) SAST est automatique ; DAST est manuel uniquement

**Question 5.** Quel outil de scan de vulnérabilités est le plus fréquemment cité dans le CEH ?
- a) Nmap
- b) Metasploit
- c) Nessus
- d) Burp Suite

**Question 6.** Une vulnérabilité Zero-Day se caractérise par le fait que :
- a) Elle a été découverte il y a exactement 0 jours
- b) Le vendor n'est pas au courant et aucun patch n'est disponible
- c) Elle a un score CVSS de 10.0
- d) Elle cible uniquement les systèmes Windows

**Question 7.** La phase "Pre-Assessment" dans le cycle de gestion des vulnérabilités comprend :
- a) L'exploitation des vulnérabilités découvertes
- b) La rédaction du rapport final
- c) L'identification des assets et la définition du périmètre
- d) L'application des correctifs

**Question 8.** CWE (Common Weakness Enumeration) catalogue :
- a) Les CVE de l'année en cours
- b) Les types de faiblesses logicielles par catégorie
- c) Les outils de scanning approuvés par NIST
- d) Les correctifs disponibles pour les vulnérabilités critiques

---

## ✅ Points de révision — Checklist

- [ ] Je connais les seuils CVSS (None/Low/Medium/High/Critical)
- [ ] Je comprends la différence CVE (identifiant) vs NVD (base de données) vs CVSS (score)
- [ ] Je connais les 4 groupes de métriques CVSS
- [ ] Je peux citer des CVE historiques importants (Log4Shell, EternalBlue, Heartbleed)
- [ ] Je sais ce qu'est un Zero-Day
- [ ] Je connais Nessus vs OpenVAS
- [ ] Je comprends le cycle Pre/Assessment/Post-Assessment
- [ ] Je sais la différence entre scan actif et passif

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | 8.5 = 7.0-8.9 = High (Critical commence à 9.0) |
| 2 | **c** | CVE-2021-44228 = Log4Shell |
| 3 | **b** | Base = caractéristiques intrinsèques, constantes |
| 4 | **b** | SAST = code source statique ; DAST = app en exécution |
| 5 | **c** | Nessus = scanner de référence CEH |
| 6 | **b** | Zero-Day = vendor ne sait pas + pas de patch |
| 7 | **c** | Pre-Assessment = identifier assets + définir scope |
| 8 | **b** | CWE = catalogue types de faiblesses logicielles |

---

*⬅️ [Rapports d'évaluation](./06-assessment-reports.md) | ➡️ [README Module 05](./README.md)*
