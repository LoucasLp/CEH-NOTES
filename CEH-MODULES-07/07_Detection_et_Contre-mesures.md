# 🛡️ 07 — Détection & Contre-mesures

> **CEH v13 | Module 07 : Malware Threats**  
> 📌 Section 7/8 — Outils de défense, EDR, SIEM & stratégies

---

## 🏛️ Modèle de défense en couches (Defense-in-Depth)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
            DÉFENSE EN PROFONDEUR — MALWARE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

     ┌─────────────────────────────────────────┐
     │           GOUVERNANCE / POLITIQUES      │ Couche 7
     └─────────────────────────────────────────┘
          ┌───────────────────────────────┐
          │     SENSIBILISATION / FORMATION│ Couche 6
          └───────────────────────────────┘
               ┌─────────────────────┐
               │   SÉCURITÉ RÉSEAU   │ Couche 5
               └─────────────────────┘
                    ┌───────────────┐
                    │    ENDPOINT   │ Couche 4
                    └───────────────┘
                         ┌────────┐
                         │  DATA  │ Couche 3
                         └────────┘
                           ┌────┐
                           │APP │ Couche 2
                           └────┘
                            ┌──┐
                            │OS│ Couche 1
                            └──┘
```

---

## 🔍 PARTIE 1 : MÉTHODES DE DÉTECTION

### Détection basée sur les signatures

```
SIGNATURE-BASED DETECTION

  Malware A → Hash MD5 / Séquence binaire → Base AV
                         │
  Fichier suspect → Hash MD5 → Comparaison base → ✅ Détecté / ❌ Inconnu

  ✅ Avantages : Rapide, faible faux positifs
  ❌ Inconvénients : Inefficace contre malware nouveau / polymorphique
```

### Détection comportementale

```
BEHAVIORAL DETECTION

  Processus en cours → Surveillance des actions
          │
          ├─ Appels API suspects ?
          ├─ Modification fichiers système ?
          ├─ Injection dans autres processus ?
          ├─ Connexion réseau inattendue ?
          └─ Chiffrement massif de fichiers ?
                    │
              → ALERTE / Blocage

  ✅ Avantages : Détecte variantes inconnues, 0-day
  ❌ Inconvénients : Plus de faux positifs
```

### Détection heuristique

> Analyse le code à la recherche de **patterns suspects** sans comparaison exacte de signature.

### Détection par Machine Learning / IA

> Modèles ML entraînés à classer les fichiers/comportements comme malveillants ou légitimes.

---

## 🛡️ PARTIE 2 : CATÉGORIES D'OUTILS

### 🔴 Antivirus / Antimalware (couche de base)

| Solution | Type |
|---------|------|
| **Windows Defender** | Intégré Windows, gratuit |
| **Malwarebytes** | Anti-malware spécialisé |
| **Kaspersky** | AV complet |
| **Bitdefender** | AV complet + ML |
| **ESET NOD32** | Léger, efficace |

---

### 🟠 EDR — Endpoint Detection & Response

> Les **EDR** vont au-delà de l'AV classique : ils enregistrent tous les événements sur l'endpoint, permettent la **détection avancée** et la **réponse aux incidents**.

```
AV Classique                    EDR
─────────────                   ───
Détecte fichier malveillant     Détecte comportement malveillant
Basé signatures                 Basé comportement + ML
Alerte uniquement               Alerte + Isolement + Forensique
Pas d'historique                Historique complet des événements
```

**EDR leaders du marché :**

| EDR | Éditeur |
|-----|---------|
| **CrowdStrike Falcon** | CrowdStrike |
| **Microsoft Defender for Endpoint** | Microsoft |
| **Carbon Black** | VMware |
| **SentinelOne** | SentinelOne |
| **Cortex XDR** | Palo Alto |
| **Cybereason** | Cybereason |

---

### 🟡 SIEM — Security Information & Event Management

> Centralise les **logs de toutes les sources** (endpoints, réseau, cloud) pour détecter les menaces par corrélation.

```
Sources → SIEM → Corrélation → Alertes → SOC

Firewalls ─────┐
Endpoints ─────┤→ SIEM → Règles → Alerte → Analyste
Serveurs ──────┤       → ML     → Ticket
Cloud ─────────┘
```

**SIEM courants :**
- **Splunk** (référence)
- **IBM QRadar**
- **Microsoft Sentinel** (cloud)
- **Elastic SIEM** (open source)
- **ArcSight** (Micro Focus)

---

### 🟢 Sandboxing

> Exécuter un fichier suspect dans un **environnement virtuel isolé** pour observer son comportement sans risque.

| Solution | Type |
|---------|------|
| **Cuckoo Sandbox** | Open source, auto-hébergé |
| **ANY.RUN** | En ligne, interactif |
| **Hybrid Analysis** | En ligne, gratuit |
| **Joe Sandbox** | En ligne, avancé |
| **FireEye AX** | Entreprise |

---

### 🔵 IDS/IPS — Détection/Prévention d'intrusion réseau

| Outil | Type |
|-------|------|
| **Snort** | IDS/IPS open source (règles signatures) |
| **Suricata** | IDS/IPS multi-thread, haute performance |
| **Zeek (Bro)** | Analyse réseau + scripting |

---

## 🛡️ PARTIE 3 : CONTRE-MESURES PAR CATÉGORIE

### ✅ Prévention

```
PRÉVENTION — Actions clés

Système & Réseau
├── Patch management rigoureux (OS, apps, firmware)
├── Désactiver services inutiles (SMBv1, Telnet, RDP non nécessaire)
├── Segmentation réseau (VLAN, DMZ)
├── Firewall entrant ET sortant configuré
├── Désactiver l'autorun USB/CD
└── Application whitelisting (WDAC, AppLocker)

Utilisateurs
├── Formation et sensibilisation (phishing, ingénierie sociale)
├── Politique de mots de passe forts + MFA
├── Principe du moindre privilège (PoLP)
└── Pas de droits admin pour usage courant

Email
├── Filtrage des pièces jointes (extensions dangereuses)
├── SPF / DKIM / DMARC configurés
├── Sandbox email automatique
└── Anti-spam / Anti-phishing
```

---

### 🔍 Détection

```
DÉTECTION — Outils & pratiques

Endpoint
├── EDR avec behavioral detection
├── AMSI pour scripts PowerShell/VBS
├── Script Block Logging PowerShell activé
└── Sysmon configuré (logs détaillés)

Réseau
├── IDS/IPS (Snort/Suricata)
├── Surveillance DNS (requêtes vers domaines suspects)
├── Détection trafic C2 (beaconing patterns)
└── Analyse NetFlow

Centralisation
├── SIEM avec règles de corrélation
├── Threat Intelligence Feeds (IOC automatiques)
└── Honeypots pour détecter reconnaissance interne
```

---

### 🚨 Réponse aux incidents (IR)

```
RÉPONSE AUX INCIDENTS MALWARE

[1] IDENTIFICATION
     └─ Détecter et confirmer l'infection

[2] CONTAINMENT
     └─ Isoler la machine infectée (réseau)
     └─ Bloquer les IPs/domaines C2 identifiés

[3] ERADICATION
     └─ Supprimer le malware et ses persistances
     └─ Nettoyer clés registre, fichiers, services

[4] RECOVERY
     └─ Restaurer depuis sauvegarde propre connue
     └─ Réinstaller si nécessaire
     └─ Patcher la vulnérabilité exploitée

[5] POST-INCIDENT
     └─ Analyse forensique (comment ? quand ? quoi volé ?)
     └─ Rapport d'incident
     └─ Amélioration des contrôles
```

---

## 📊 PARTIE 4 : THREAT INTELLIGENCE

### Indicators of Compromise (IOCs)

> Les IOC sont des **artefacts observables** qui indiquent qu'un système a probablement été compromis.

| Type d'IOC | Exemples |
|-----------|---------|
| **Hash** | MD5/SHA256 de fichier malveillant |
| **IP** | Adresse serveur C2 |
| **Domain** | Domaine DGA ou C2 |
| **URL** | URL de téléchargement payload |
| **Registry key** | Clé de persistance créée |
| **Mutex** | Nom de mutex spécifique |
| **Email** | Adresse expéditeur phishing |
| **Filename** | Nom de fichier malveillant |

### Sources de Threat Intelligence

```
Sources Threat Intelligence

Open Source (OSINT)
├── VirusTotal → Hashes, domaines, IPs
├── AlienVault OTX → IOC partagés
├── MalwareBazaar → Samples malware
├── Abuse.ch → Ransomware, botnet trackers
└── Shodan → Infrastructure C2 exposée

Commerciale
├── Recorded Future
├── CrowdStrike Intelligence
└── Mandiant Threat Intel
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **EDR** = Endpoint Detection & Response (va au-delà de l'AV)
- **SIEM** = corrélation de logs multi-sources
- **Snort** = IDS/IPS open source le plus connu
- **Splunk** = SIEM le plus répandu en entreprise
- **IOC** = Indicator of Compromise (pas indicateur d'attaque = IoA)
- **Defense-in-Depth** = sécurité par couches multiples
- Réponse incident : Identification → Containment → Eradication → Recovery → Post-incident

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [06 — Analyse Malware](./06_Analyse_Malware.md) | [08 — Cas réels & Quiz](./08_Cas_Reels_et_Quiz.md) |
