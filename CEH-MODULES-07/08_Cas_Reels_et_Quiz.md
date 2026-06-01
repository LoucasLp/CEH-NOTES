# 📚 08 — Cas Réels & Questions d'Examen

> **CEH v13 | Module 07 : Malware Threats**  
> 📌 Section 8/8 — Révision finale & préparation examen

---

## 🌍 PARTIE 1 : INCIDENTS RÉELS (à connaître pour l'examen)

### 🔴 WannaCry (2017)

```
┌────────────────────────────────────────────────────┐
│  WANNACRY — FICHE INCIDENT                         │
├────────────────────────────────────────────────────┤
│  Type        : Ransomware + Worm                   │
│  Date        : Mai 2017                            │
│  Exploit     : EternalBlue (MS17-010) sur SMBv1    │
│  Origine     : Lazarus Group (Corée du Nord)       │
│  Impact      : 230 000 victimes en 24h             │
│                150+ pays touchés                   │
│                NHS UK paralysé                     │
│  Rançon      : $300-600 en Bitcoin                 │
│  Kill switch : Domaine non enregistré stoppait WC  │
│  Leçon       : Patcher MS17-010 / Désactiver SMBv1 │
└────────────────────────────────────────────────────┘
```

---

### 🔴 NotPetya (2017)

```
┌────────────────────────────────────────────────────┐
│  NOTPETYA — FICHE INCIDENT                         │
├────────────────────────────────────────────────────┤
│  Type        : Wiper (déguisé en ransomware)       │
│  Date        : Juin 2017                           │
│  Exploit     : EternalBlue + Mimikatz (LSASS)      │
│  Origine     : Sandworm (GRU russe)                │
│  Impact      : $10 milliards de dégâts             │
│                Maersk, Merck, FedEx touchés        │
│  Particularité: PAS de vraie clé de déchiffrement  │
│  Supply chain : Via mise à jour MEDoc (Ukraine)    │
│  Leçon       : Sauvegardes offline + segmentation  │
└────────────────────────────────────────────────────┘
```

---

### 🔴 Stuxnet (2010)

```
┌────────────────────────────────────────────────────┐
│  STUXNET — FICHE INCIDENT                          │
├────────────────────────────────────────────────────┤
│  Type        : APT Worm ciblant ICS/SCADA          │
│  Date        : ~2007, découvert 2010               │
│  Exploit     : 4 zero-days Windows + Siemens PLC   │
│  Origine     : NSA + Unité 8200 (USA/Israël)       │
│  Cible       : Centrifugeuses nucléaires iraniennes│
│  Méthode     : USB → SCADA Siemens S7 → PLC        │
│  Impact      : 1000+ centrifugeuses détruites      │
│  Leçon       : 1er cyberarme nationale connue      │
│                Air-gap ≠ protection absolue (USB)  │
└────────────────────────────────────────────────────┘
```

---

### 🔴 SolarWinds / SUNBURST (2020)

```
┌────────────────────────────────────────────────────┐
│  SOLARWINDS — FICHE INCIDENT                       │
├────────────────────────────────────────────────────┤
│  Type        : APT Supply Chain Attack             │
│  Date        : ~Oct 2019, découvert Déc 2020       │
│  Malware     : SUNBURST backdoor                   │
│  Origine     : APT29 / Cozy Bear (Russie)          │
│  Méthode     : Mise à jour Orion corrompue         │
│  Victimes    : 18 000+ organisations               │
│                US Gov, FireEye, Fortune 500        │
│  Durée       : ~9 mois sans détection              │
│  Leçon       : Supply chain = vecteur critique     │
└────────────────────────────────────────────────────┘
```

---

### 🔴 ILOVEYOU (2000)

```
┌────────────────────────────────────────────────────┐
│  ILOVEYOU — FICHE INCIDENT                         │
├────────────────────────────────────────────────────┤
│  Type        : Worm / virus (VBScript)             │
│  Date        : Mai 2000                            │
│  Vecteur     : Email "ILOVEYOU" + pièce jointe     │
│  Impact      : $10 milliards de dégâts             │
│                50+ millions de PC infectés         │
│  Origine     : Philippines (Onel de Guzman)        │
│  Méthode     : Se renvoyait à tous les contacts    │
│  Leçon       : Sensibilisation email critique      │
└────────────────────────────────────────────────────┘
```

---

### 🔴 Emotet

```
┌────────────────────────────────────────────────────┐
│  EMOTET — FICHE INCIDENT                           │
├────────────────────────────────────────────────────┤
│  Type        : Banking Trojan → MaaS               │
│  Dates       : 2014 → 2021 (takedown), 2022 (retour│
│  Vecteur     : Email phishing (macros Office)      │
│  Évolution   : Trojan → Loader → MaaS platform     │
│  Charge utile: Ryuk, TrickBot, QBot, Dridex        │
│  Impact      : #1 menace mondiale 2019-2021        │
│  Takedown    : Jan 2021 (Europol/FBI)              │
│  Leçon       : Désactiver macros Office par défaut │
└────────────────────────────────────────────────────┘
```

---

## 🧠 PARTIE 2 : MÉMO FINAL

### 📋 Tableau récapitulatif des types de malware

| Malware | Hôte requis | Action humaine | Principale menace | Détection |
|---------|------------|----------------|-------------------|-----------|
| **Virus** | ✅ Oui | ✅ Oui | Corruption fichiers | Signature AV |
| **Worm** | ❌ Non | ❌ Non | Congestion réseau | IDS, NetFlow |
| **Trojan** | ✅ (masqué) | ✅ Oui | Backdoor, vol | Comportemental |
| **RAT** | Non | Oui | Contrôle total | EDR, réseau |
| **Ransomware** | Non | Oui | Chiffrement données | Comportemental |
| **Rootkit** | Non | Non | Persistance cachée | Analyse mémoire |
| **Botnet** | Non | Non | DDoS, spam | Réseau, C2 |
| **Spyware** | Non | Oui | Vol d'infos | Comportemental |
| **Fileless** | Non | Variable | Évasion AV | EDR, mémoire |

---

### 🎯 Mnémotechniques

```
TYPE DE MALWARE
"Very Wild Tigers Really Run Swiftly Backwards"
 Virus  Worms Trojans Rootkit Ransomware Spyware Botnets

CYCLE DE VIE VIRUS
"Design Really Launches Detection Incorporating Everyone"
 Design Replication Launch Detection Incorporation Elimination

RÉPONSE INCIDENT (IR)
"I Can't Erase Recovery Problems"
 Identification Containment Eradication Recovery Post-incident

RÈGLE SAUVEGARDE
"3-2-1" = 3 copies, 2 médias différents, 1 hors site
```

---

## ❓ PARTIE 3 : QUESTIONS D'EXAMEN TYPE

> 🎯 *Format identique aux questions CEH 312-50*

---

**Q1.** Un malware se propage à travers le réseau sans nécessiter d'action de la part de l'utilisateur en exploitant une vulnérabilité SMB. De quel type de malware s'agit-il ?

- A) Virus
- B) Trojan
- C) **Worm ✅**
- D) Rootkit

> 💡 Un worm se propage automatiquement sur le réseau sans intervention humaine.

---

**Q2.** Lequel des outils suivants est utilisé pour effectuer une **analyse dynamique** d'un malware ?

- A) IDA Pro
- B) Ghidra
- C) **Cuckoo Sandbox ✅**
- D) VirusTotal

> 💡 IDA Pro et Ghidra = statique. Cuckoo Sandbox = dynamique (exécute le malware).

---

**Q3.** WannaCry a utilisé quelle vulnérabilité pour se propager ?

- A) MS08-067
- B) CVE-2014-6271 (Shellshock)
- C) **MS17-010 (EternalBlue) ✅**
- D) CVE-2021-44228 (Log4Shell)

> 💡 WannaCry exploite MS17-010, une faille SMBv1 révélée par EternalBlue (NSA).

---

**Q4.** Un malware change sa signature à chaque infection mais conserve le même comportement. De quel type s'agit-il ?

- A) Metamorphic
- B) Armored
- C) **Polymorphic ✅**
- D) Stealth

> 💡 Polymorphique = même comportement, signature différente. Métamorphique = code entièrement réécrit.

---

**Q5.** Quel outil est principalement utilisé pour l'**analyse forensique de la mémoire vive** ?

- A) Wireshark
- B) ProcMon
- C) Cuckoo Sandbox
- D) **Volatility ✅**

> 💡 Volatility est le framework de référence pour l'analyse de dumps mémoire.

---

**Q6.** Un virus qui s'installe dans le MBR et s'exécute avant le chargement du système d'exploitation est appelé :

- A) File infector
- B) Macro virus
- C) **Boot sector virus ✅**
- D) Multipartite virus

---

**Q7.** Quel type de rootkit est le plus difficile à détecter et supprimer ?

- A) User-mode rootkit
- B) Bootkit
- C) **Kernel-level rootkit ✅** (ou Firmware rootkit selon contexte)
- D) Library rootkit

> 💡 Le kernel rootkit opère au niveau du noyau (Ring 0), il a accès total au système et peut cacher tout ce qu'il veut.

---

**Q8.** Le botnet **Mirai** est principalement connu pour :

- A) Cibler des serveurs Windows pour le minage de cryptomonnaie
- B) Distribuer des ransomware via email
- C) **Infecter des appareils IoT pour mener des attaques DDoS ✅**
- D) Voler des informations bancaires via form grabbing

---

**Q9.** Quelle technique permet à un malware de s'exécuter sans écrire de fichier sur le disque ?

- A) Polymorphism
- B) Packing
- C) **Fileless execution (in-memory) ✅**
- D) Rootkit

> 💡 Le fileless malware utilise PowerShell, WMI, LOLBins pour s'exécuter uniquement en RAM.

---

**Q10.** La règle de sauvegarde **3-2-1** signifie :

- A) 3 sauvegardes, 2 fois par semaine, 1 vérification mensuelle
- B) **3 copies, 2 types de médias différents, 1 copie hors site ✅**
- C) 3 outils, 2 firewalls, 1 SIEM
- D) 3 rotations, 2 serveurs, 1 cloud

---

## 📋 PARTIE 4 : CHECKLIST RÉVISION MODULE 7

Avant l'examen, assurez-vous de maîtriser :

- [ ] Différences Virus / Worm / Trojan / Rootkit / Ransomware
- [ ] Cycle de vie d'un virus (6 phases)
- [ ] Types de Trojans (RAT, Banking, Downloader...)
- [ ] Types de Ransomware (Crypto, Locker, RaaS)
- [ ] Différence Polymorphique vs Métamorphique
- [ ] Fileless malware : vecteurs et outils (PowerShell, WMI, LOLBins)
- [ ] APT : définition, cycle de vie, exemples
- [ ] Analyse statique : outils (IDA, Ghidra, strings, PEiD)
- [ ] Analyse dynamique : outils (Cuckoo, ProcMon, Wireshark)
- [ ] Analyse mémoire : Volatility, commandes de base
- [ ] IOC : types et sources
- [ ] EDR vs AV classique
- [ ] SIEM : Splunk, QRadar, Sentinel
- [ ] Incidents majeurs : WannaCry, Stuxnet, Emotet, ILOVEYOU
- [ ] Règle 3-2-1 pour les sauvegardes
- [ ] Réponse incident : 5 phases

---

## 🔗 Navigation

| ← Précédent | Retour →  |
|-------------|-----------|
| [07 — Détection & Contre-mesures](./07_Detection_et_Contre-mesures.md) | [README — Index](./README.md) |

---

*✨ Bonne chance pour l'examen CEH v13 ! Remember : "Very Wild Tigers Really Run Swiftly Backwards"*
