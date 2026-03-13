# 🎯 01 — Introduction & Méthodologie du System Hacking

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## 📖 Définition

Le **System Hacking** est le processus d'exploitation des vulnérabilités d'un système d'exploitation pour :

- 🔓 Obtenir un accès non autorisé
- ⬆️ Escalader les privilèges jusqu'au niveau administrateur/root
- 📋 Exécuter des applications malveillantes
- 🗂️ Voler des informations sensibles
- 🔒 Créer une persistance pour un accès à long terme
- 🧹 Effacer les traces de compromission

---

## 🔄 Place dans la méthodologie CEH globale

```
Phase 1 → Footprinting & Reconnaissance
Phase 2 → Scanning Networks
Phase 3 → Énumération
Phase 4 → Vulnerability Analysis
Phase 5 ◄══ SYSTEM HACKING (Module 6) ══►
Phase 6 → Malware Threats
...
```

> 💡 **Le System Hacking exploite les informations collectées dans les phases précédentes.**  
> Sans footprinting, scanning et énumération préalables, cette phase est aveugle.

---

## 🏗️ La méthodologie CEH de System Hacking

### Vue d'ensemble

```
┌─────────────────────────────────────────────────────────────────┐
│                CEH SYSTEM HACKING METHODOLOGY                   │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  ÉTAPE 1 : GAINING ACCESS                                │   │
│  │  ├─ Password Cracking (brute force, dictionnaire...)     │   │
│  │  └─ Vulnerability Exploitation (Metasploit, BoF...)      │   │
│  └──────────────────────────────────────────────────────────┘   │
│                          ↓                                      │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  ÉTAPE 2 : ESCALATING PRIVILEGES                         │   │
│  │  ├─ DLL Hijacking / Injection                           │   │
│  │  ├─ UAC Bypass                                          │   │
│  │  └─ Spectre/Meltdown, Named Pipe, Service Exploits       │   │
│  └──────────────────────────────────────────────────────────┘   │
│                          ↓                                      │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  ÉTAPE 3 : MAINTAINING ACCESS                            │   │
│  │  ├─ Executing Applications (keyloggers, backdoors...)    │   │
│  │  └─ Hiding Files (rootkits, NTFS ADS, stéganographie)   │   │
│  └──────────────────────────────────────────────────────────┘   │
│                          ↓                                      │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  ÉTAPE 4 : CLEARING LOGS                                 │   │
│  │  ├─ Effacement des journaux système                      │   │
│  │  └─ Anti-forensics (outils de couverture)                │   │
│  └──────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Objectifs détaillés par étape

### Étape 1 — Gaining Access (Obtenir l'accès)

| Technique | Description |
|-----------|-------------|
| **Password Cracking** | Contourner les contrôles d'accès par craquage de mots de passe |
| **Vulnerability Exploitation** | Exploiter les vulnérabilités connues (CVE) |
| **Buffer Overflow** | Injecter du code malveillant via débordement de tampon |
| **Social Engineering** | Manipuler les utilisateurs pour obtenir des credentials |

---

### Étape 2 — Escalating Privileges (Escalade de privilèges)

```
Accès utilisateur standard
        ↓
Exploitation des vulnérabilités OS/logiciels
        ↓
Privilèges administrateur / SYSTEM (Windows)
        ↓
Accès root (Linux)
        ↓
Contrôle total du système
```

---

### Étape 3 — Maintaining Access (Maintien de l'accès)

| Action | Méthode |
|--------|---------|
| **Exécuter des applications** | Trojans, spyware, backdoors, keyloggers |
| **Cacher des fichiers** | Rootkits, NTFS ADS, stéganographie |
| **Persistance** | Registry Run keys, tâches planifiées, cron jobs |

---

### Étape 4 — Clearing Logs (Effacement des traces)

```
Objectif : Ne laisser AUCUNE trace de la compromission

Méthodes :
├─ Effacement des journaux système (Windows Event Log, syslog)
├─ Modification des timestamps (timestomping)
├─ Suppression des fichiers temporaires
└─ Outils anti-forensics
```

---

## 🖥️ Authentification Windows — Concepts clés

### Authentification NTLM

```
Client                    Serveur / Domain Controller
  │                              │
  ├─1─► Username ──────────────►│
  │                              │
  │◄─2─ Challenge (nonce) ───────┤
  │                              │
  ├─3─► Response (NTLM hash      │
  │      + Challenge) ──────────►│
  │                              │
  │◄─4─ Access Granted/Denied ───┤
```

> ⚠️ **Vulnérabilité** : NTLM ne valide pas l'identité du serveur → susceptible au Pass-the-Hash, MITM, Relay

---

### Authentification Kerberos

```
Client              KDC (Key Distribution Center)         Service
  │                    ┌─────┐  ┌─────┐                     │
  │                    │ AS  │  │ TGS │                     │
  │                    └─────┘  └─────┘                     │
  ├─1─► AS-REQ ──────►│     │                              │
  │◄─2─ TGT ──────────┤     │                              │
  ├─3─► TGS-REQ + TGT──────────►│     │                   │
  │◄─4─ Service Ticket ──────────┤     │                   │
  ├─5─► Service Ticket ──────────────────────────────────►│
  │◄─6─ Access Granted ─────────────────────────────────── │
```

**Composants :**
- **KDC** : Key Distribution Center (= Domain Controller en AD)
- **AS** : Authentication Server — vérifie l'identité, délivre le TGT
- **TGS** : Ticket Granting Server — délivre les tickets de service
- **TGT** : Ticket Granting Ticket — passport d'authentification
- **ST** : Service Ticket — accès à un service spécifique

---

### SAM Database (Security Account Manager)

```
C:\Windows\System32\config\SAM
```

- Base de données chiffrée stockant les **hash** des mots de passe locaux
- Format : `username:RID:LMhash:NThash:::`
- **Exemple** : `Administrator:500:NO PASSWORD:AAD3B435B51404EEAAD3B435B51404EE:::` (LM vide)
- Protégée par **SYSKEY** — ne peut être lue pendant que Windows est en cours d'exécution
- **Objectif attaquant** : Extraire les hash pour les craquer ou les utiliser en Pass-the-Hash

---

## 🔐 Types de hash Windows

| Hash | Longueur | Sécurité | Notes |
|------|----------|----------|-------|
| **LM** | 14 chars max | 🔴 Très faible | Insensible à la casse, divisé en 2×7 chars |
| **NTLM (NT)** | Illimité | 🟡 Moyen | MD4 du mot de passe Unicode |
| **NTLMv2** | - | 🟡 Amélioré | Challenge/response avec timestamp |
| **Kerberos AES** | - | 🟢 Fort | AES-128/256 — recommandé |

---

## 🌐 Contexte Active Directory (v13)

Le CEH v13 ajoute une section dédiée aux **attaques Active Directory** (AD), reflétant leur importance croissante dans les pentest réels :

```
Active Directory = Cœur du réseau d'entreprise Windows

Vulnérabilités exploitées :
├─ Pass-the-Hash (PtH)
├─ Pass-the-Ticket (PtT)
├─ Kerberoasting
├─ Golden Ticket / Silver Ticket
├─ DCSync
└─ AD Certificate Services (AD CS) attacks
```

---

[→ Password Cracking](./02-password-cracking.md)
