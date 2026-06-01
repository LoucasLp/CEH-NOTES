# 💰 04 — Ransomware, Rootkits & Botnets

> **CEH v13 | Module 07 : Malware Threats**  
> 📌 Section 4/8 — Extorsion, Persistance & Réseaux zombies

---

## 💰 PARTIE 1 : RANSOMWARE

### Définition

> Le **ransomware** est un malware qui **chiffre les fichiers** de la victime (ou bloque l'accès au système) et exige le **paiement d'une rançon** (généralement en cryptomonnaie) pour restaurer l'accès.

---

### 🔄 Cycle d'attaque Ransomware

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            KILL CHAIN — RANSOMWARE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[1] Infection
     └─ Phishing email / RDP exploit / Web drive-by

[2] Exécution
     └─ Payload lancé, contact C2 pour clé de chiffrement

[3] Reconnaissance interne
     └─ Cartographie des partages réseau, sauvegardes

[4] Chiffrement
     └─ Fichiers chiffrés (AES-256 + RSA-2048)
          └─ Extensions modifiées (.locked, .cry, .wncry...)

[5] Ransom Note
     └─ Message affiché / fichier README créé
          └─ Instructions de paiement (Bitcoin/Monero)

[6] Paiement (si effectué)
     └─ Clé de déchiffrement transmise (parfois...)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### 🗂️ Types de Ransomware

| Type | Description | Exemple |
|------|-------------|---------|
| **Crypto Ransomware** | Chiffre les fichiers (documents, photos, DB) | WannaCry, Ryuk |
| **Locker Ransomware** | Bloque l'accès complet au système/desktop | Winlocker |
| **Master Boot Record** | Chiffre le MBR → système inaccessible | Petya |
| **Mobile Ransomware** | Cible smartphones Android | DoubleLocker |
| **Ransomware-as-a-Service (RaaS)** | Modèle commercial : location à des affiliés | DarkSide, REvil |

---

### 🌍 Incidents Ransomware majeurs (pour l'examen)

| Ransomware | Année | Particularité |
|-----------|-------|--------------|
| **CryptoLocker** | 2013 | Premier grand ransomware RSA + AES |
| **CryptoWall** | 2014 | Plus d'1 milliard $ de rançons |
| **WannaCry** | 2017 | Se propage via EternalBlue (SMB) |
| **NotPetya** | 2017 | Destructeur (pas de vraie clé) — $10Mds |
| **Ryuk** | 2018 | Ciblage entreprises, $150M+ rançons |
| **REvil/Sodinokibi** | 2020 | Double extorsion : chiffre + menace publication |
| **DarkSide** | 2021 | Colonial Pipeline (USA, crise carburant) |
| **Conti** | 2021 | Groupe RaaS, impact gouvernements |

---

### 💡 Double Extorsion (tendance moderne)

```
Ransomware 2.0 — Double extorsion

ÉTAPE 1: Exfiltration des données AVANT chiffrement
ÉTAPE 2: Chiffrement des fichiers
ÉTAPE 3: Demande de rançon pour déchiffrement
          +
          Menace de publication des données si non-paiement
```

---

### 🛡️ Contre-mesures Ransomware

```
PRÉVENTION
├── ✅ Sauvegardes régulières OFFLINE (règle 3-2-1)
│    └─ 3 copies, 2 médias différents, 1 hors site
├── ✅ Patch management (WannaCry = MS17-010 non patché)
├── ✅ Formation anti-phishing des utilisateurs
├── ✅ MFA sur RDP et accès distants
├── ✅ Segmentation réseau (limiter la propagation)
└── ✅ Application whitelisting

DÉTECTION
├── 🔍 Surveillance des modifications massives de fichiers
├── 🔍 Alertes sur chiffrement inhabituel (honeypot files)
└── 🔍 EDR avec behavioral detection

RÉPONSE
├── 🚨 Isoler immédiatement la machine
├── 🚨 NE PAS payer (pas de garantie + encourage les criminels)
├── 🚨 Restaurer depuis sauvegardes propres
└── 🚨 Contacter CERT / autorités
```

---

## 🫥 PARTIE 2 : ROOTKITS

### Définition

> Un **rootkit** est un ensemble d'outils malveillants qui permettent un **accès privilégié** (root) tout en **dissimulant leur présence** au système d'exploitation, aux AV et aux administrateurs.

---

### 🏗️ Types de Rootkits

```
ROOTKITS
│
├── 🔴 Kernel-Level Rootkit (Ring 0)
│    └─ Modifie le noyau OS → contrôle total
│    └─ Détection très difficile
│    └─ Exemples : Necurs, Azazel
│
├── 🟠 User-Mode Rootkit (Ring 3)
│    └─ Fonctionne en espace utilisateur
│    └─ Accroche les API Windows (hooking)
│    └─ Plus facile à détecter qu'un kernel rootkit
│    └─ Exemples : Hacker Defender
│
├── 🟡 Bootkit (MBR Rootkit)
│    └─ S'installe dans le MBR / boot sector
│    └─ S'exécute AVANT l'OS → échappe à tout AV
│    └─ Exemples : TDL4/TDSS, Mebroot
│
├── 🔵 Firmware Rootkit
│    └─ Intégré dans le firmware (BIOS/UEFI, NIC, disque)
│    └─ Survit au formatage complet
│    └─ Extrêmement difficile à supprimer
│
└── 🟣 Hypervisor Rootkit (Virtual Machine Rootkit)
     └─ S'installe entre le hardware et l'OS
     └─ OS tourne dans une VM contrôlée par attaquant
     └─ Exemples : SubVirt, Blue Pill
```

---

### 🎯 Techniques de dissimulation des Rootkits

| Technique | Description |
|-----------|-------------|
| **DKOM** (Direct Kernel Object Manipulation) | Modifie les structures kernel pour cacher processus |
| **API Hooking** | Intercepte les appels système, retourne des résultats falsifiés |
| **IAT Hooking** | Modifie Import Address Table pour rediriger les appels |
| **SSDT Hooking** | Modifie System Service Descriptor Table |
| **File Hiding** | Cache les fichiers malveillants du système de fichiers |
| **Network Hiding** | Cache les connexions réseau malveillantes |

---

### 🔍 Détection des Rootkits

```
Méthodes de détection

├── Cross-view comparison → Comparer résultats OS vs outil bas-niveau
│    (Si différence = rootkit probable)
│
├── Integrity checking → Vérifier signatures des fichiers système
│    (MD5/SHA256 des binaires critiques)
│
├── Memory analysis → Analyser la RAM à la recherche d'anomalies
│    (Volatility Framework)
│
└── Boot-time scanning → Scanner avant chargement OS
     (AV avec rescue disk)
```

**Outils de détection :**
- `GMER`, `RootkitRevealer` (Sysinternals)
- `Sophos Anti-Rootkit`, `TDSSKiller` (Kaspersky)
- `Volatility` (analyse mémoire)

---

## 🤖 PARTIE 3 : BOTNETS

### Définition

> Un **botnet** est un réseau de machines compromises (**bots / zombies**) contrôlées à distance par un attaquant via un serveur **C2 (Command & Control)**.

---

### 🏗️ Architecture d'un Botnet

```
Architecture Botnet (modèle centralisé)

    ┌─────────────────┐
    │   ATTAQUANT     │  ← Botmaster
    └────────┬────────┘
             │ Instructions
    ┌────────▼────────┐
    │  Serveur C2     │  ← Command & Control
    └──┬──────────┬───┘
       │          │
  ┌────▼──┐   ┌───▼───┐
  │ Bot 1 │   │ Bot 2 │  ← Machines zombies
  └───────┘   └───────┘   (victimes infectées)
       ...        ...
```

```
Architecture P2P Botnet (décentralisé — plus résilient)

  Bot A ←→ Bot B ←→ Bot C
    ↕              ↕
  Bot D ←→ Bot E ←→ Bot F
  
  (Pas de point central → plus difficile à démanteler)
```

---

### 🎯 Utilisations des Botnets

| Usage | Description |
|-------|-------------|
| **DDoS** | Inonder une cible de trafic (volumétrique) |
| **Spam** | Envoyer des millions d'emails de phishing |
| **Credential Stuffing** | Tester des millions de credentials volés |
| **Crypto Mining** | Miner des cryptomonnaies sur machines des victimes |
| **Click Fraud** | Générer des faux clics sur publicités |
| **Proxy Networks** | Anonymiser le trafic de l'attaquant |
| **Ransomware Distribution** | Distribuer ransomware via le botnet |

---

### 🌍 Botnets célèbres

| Botnet | Année | Particularité |
|--------|-------|--------------|
| **Storm** | 2007 | Jusqu'à 50 millions de bots |
| **Conficker** | 2008 | 9-15 millions de PC infectés |
| **Zeus** | 2007-2011 | Spécialisé vol bancaire |
| **Mirai** | 2016 | Cible objets IoT (caméras, routeurs) → DDoS 620Gbps |
| **Emotet** | 2014-2021 | Distribué comme botnet-as-a-service |

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **WannaCry** exploite `MS17-010` (EternalBlue) sur **SMBv1**
- **RaaS** = Ransomware-as-a-Service : affiliés paient pour utiliser la plateforme
- **Kernel rootkit** = le plus difficile à détecter et supprimer
- **Bootkit** = survit même à une réinstallation OS si MBR non nettoyé
- **Mirai** = botnet de terminaux **IoT** (pas PCs) → DDoS massif
- Règle des sauvegardes : **3-2-1** (3 copies, 2 médias, 1 hors site)

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [03 — Virus & Worms](./03_Virus_et_Vers.md) | [05 — Techniques avancées](./05_Techniques_Avancees.md) |
