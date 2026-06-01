# 🔬 03 — Virus & Vers (Worms)

> **CEH v13 | Module 07 : Malware Threats**  
> 📌 Section 3/8 — Réplication, Infection & Propagation

---

## 🦠 PARTIE 1 : LES VIRUS

### Définition

> Un **virus informatique** est un code malveillant qui **s'attache à des fichiers ou programmes hôtes** et se **réplique** lorsqu'ils sont exécutés. Il nécessite **une action humaine** pour se propager.

---

### ♻️ Cycle de vie d'un Virus

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
   CYCLE DE VIE D'UN VIRUS (6 phases)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

  [1] Design
       └─ Création du virus (depuis zéro ou via kits)

  [2] Replication
       └─ Virus se réplique et s'attache à d'autres fichiers

  [3] Launch
       └─ Utilisateur exécute accidentellement le fichier infecté

  [4] Detection
       └─ Comportement suspect détecté / virus identifié

  [5] Incorporation
       └─ Éditeurs AV développent une signature défensive

  [6] Elimination
       └─ Mise à jour AV → virus éliminé
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

### 📋 Caractéristiques d'un Virus

| Caractéristique | Description |
|----------------|-------------|
| **Self-Replication** | Se copie lui-même dans d'autres fichiers |
| **Host Dependency** | A besoin d'un programme hôte pour exister |
| **Triggered Activation** | Actif seulement lors de l'exécution |
| **Payload** | Code malveillant exécuté après déclenchement |
| **Alteration** | Modifie données ou fichiers |
| **Corruption** | Peut corrompre/détruire des données |
| **Encryption** | Certains chiffrent leur code pour l'évasion |

---

### 🗂️ Types de Virus

#### 🔵 Boot Sector Virus
> Infecte le **MBR (Master Boot Record)** ou la partition de démarrage.

```
Disque dur
├── MBR [INFECTÉ ← virus ici]
├── Partition 1
└── Partition 2

→ S'exécute AVANT le système d'exploitation
→ Très difficile à supprimer (nécessite outil de rescue)
```

#### 🔵 File Infector Virus
> S'attache aux **fichiers exécutables** (.exe, .com, .sys, .dll).
- Actif à chaque lancement du programme infecté.

#### 🔵 Macro Virus
> Écrit dans un **langage macro** (VBA) intégré dans des documents Office.

```
Document Word/Excel infecté
└── Macro malveillante (VBA)
     └─ S'exécute à l'ouverture du document
          └─ Infection d'autres documents Office
```

**Exemples :** `Melissa`, `Concept`

#### 🔵 Polymorphic Virus
> **Change sa signature** à chaque infection → échappe aux AV basés sur signatures.

```
Infection 1 : [CODE_MALVEILLANT + CLE_1] → Signature A
Infection 2 : [CODE_MALVEILLANT + CLE_2] → Signature B  (différente !)
Infection 3 : [CODE_MALVEILLANT + CLE_3] → Signature C  (différente !)
```

#### 🔵 Metamorphic Virus
> **Réécrit entièrement son code** à chaque propagation (plus sophistiqué que polymorphique).
- Aucune signature constante possible.
- Détection : analyse comportementale uniquement.

#### 🔵 Stealth Virus
> **Se cache** en interceptant les appels système pour masquer sa présence.

#### 🔵 Multipartite Virus
> Infecte **à la fois** le boot sector ET les fichiers exécutables.

#### 🔵 Sparse Infector
> N'infecte qu'**occasionnellement** pour rester discret.

---

## 🐛 PARTIE 2 : LES VERS (WORMS)

### Définition

> Un **ver** est un malware **auto-réplicant** qui se propage **sans nécessiter de fichier hôte** ni d'action humaine. Il exploite des **vulnérabilités réseau** pour se répandre.

### Virus vs Worm — Différences clés

| Critère | Virus | Worm |
|---------|-------|------|
| **Fichier hôte requis** | ✅ Oui | ❌ Non |
| **Action humaine** | ✅ Nécessaire | ❌ Non nécessaire |
| **Vitesse de propagation** | Lente | Rapide / Explosive |
| **Impact réseau** | Minimal | Congestion majeure |
| **Cible** | Fichiers locaux | Réseau entier |

---

### 🔄 Comment un Worm se propage

```
Worm sur Machine A
│
├─ Scan réseau → trouve Machine B (vulnérable)
│   └─ Exploite vulnérabilité (ex: SMB, RDP)
│        └─ Copie du worm sur Machine B
│             └─ Machine B infectée → répète le cycle
│
├─ Scan réseau → trouve Machine C
│   └─ Même processus...
│
└─ Résultat : propagation exponentielle
```

---

### 🌍 Worms célèbres

| Worm | Année | Méthode | Impact |
|------|-------|---------|--------|
| **Morris Worm** | 1988 | Exploit UNIX (sendmail, finger, rsh) | 1er grand worm Internet |
| **ILOVEYOU** | 2000 | Email (VBScript en pièce jointe) | $10 milliards de dégâts |
| **SQL Slammer** | 2003 | Exploit SQL Server (UDP 1434) | Internet ralenti mondialement en 10 min |
| **Blaster** | 2003 | Exploit RPC DCOM (Windows) | Millions de machines |
| **Conficker** | 2008 | Exploit MS08-067 (SMB) | 9-15 millions de PC |
| **WannaCry** | 2017 | EternalBlue (SMB exploit NSA) | 230 000 victimes en 24h |
| **NotPetya** | 2017 | EternalBlue + Mimikatz | $10 milliards, destructeur |

---

### ⚙️ Mécanismes de propagation des Worms

1. **Email propagation** → s'envoie à tous les contacts
2. **Internet propagation** → scan IP aléatoires, exploite services ouverts
3. **Network propagation** → utilise partages réseau (SMB, NFS)
4. **IRC propagation** → canaux IRC pour distribuer
5. **P2P propagation** → réseaux peer-to-peer (BitTorrent, etc.)

---

### 🎯 Effets des Worms

```
Impact d'une infection Worm
├── 🔴 Consommation bande passante (congestion réseau)
├── 🔴 Surcharge CPU/RAM des machines infectées
├── 🔴 Installation de backdoors / payloads supplémentaires
├── 🔴 Création de botnets (armées de zombies)
├── 🔴 Vol de données sensibles
└── 🔴 Utilisation comme plateforme DDoS
```

---

## 🛡️ Contre-mesures — Virus & Worms

### Prévention
```
✅ Antivirus/Antimalware à jour (signatures & comportemental)
✅ Patch management rigoureux (Wanna Cry = patch non appliqué)
✅ Filtrage email (pièces jointes, liens)
✅ Désactiver macros Office par défaut
✅ Segmentation réseau (limiter propagation des worms)
✅ Firewall entrant/sortant correctement configuré
✅ Désactiver services inutiles (SMBv1, Telnet...)
```

### Détection
```
🔍 Surveillance des anomalies trafic réseau
🔍 SIEM / Alertes sur comportements suspects
🔍 Analyse comportementale (sandbox)
🔍 EDR sur les endpoints
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **WannaCry** est un **ransomware** qui se propage comme un **worm** (via EternalBlue/SMB)
- **Polymorphique** = change signature | **Métamorphique** = réécrit son code
- **Macro virus** → infecte les documents Office (VBA)
- Les worms causent de la **congestion réseau** (caractéristique distinctive)
- Cycle de vie virus : Design → Replication → Launch → Detection → Incorporation → Elimination

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [02 — Trojans & Backdoors](./02_Trojans_et_Backdoors.md) | [04 — Ransomware & Rootkits](./04_Ransomware_et_Rootkits.md) |
