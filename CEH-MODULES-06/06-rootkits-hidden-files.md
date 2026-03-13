# 🕳️ 06 — Rootkits & Fichiers Cachés (NTFS ADS)

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## PARTIE 1 — Rootkits

## 📖 Définition

Un **rootkit** est un ensemble de programmes qui dissimulent la présence d'un attaquant et ses activités malveillantes, tout en lui accordant un accès complet au serveur ou à l'hôte, de manière actuelle ou future.

```
Rootkit typique contient :
├─ Programmes backdoor
├─ Programmes DDoS
├─ Packet sniffers
├─ Utilitaires d'effacement de logs
├─ IRC bots
└─ Outils de surveillance réseau
```

> 🔴 **Danger** : Un rootkit kernel réside **sous l'OS** — les antivirus ne peuvent pas le voir car ils fonctionnent à un niveau supérieur.

---

## 🗂️ Types de Rootkits

```
┌─────────────────────────────────────────────────────────────────┐
│                    NIVEAUX DES ROOTKITS                         │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │             HARDWARE / FIRMWARE                         │    │
│  │   Caché dans le firmware (BIOS, UEFI, HDD, NIC)         │    │
│  │   Survit au formatage du disque                         │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▲                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │             HYPERVISOR LEVEL                            │    │
│  │   L'OS légitime tourne comme VM sous le rootkit         │    │
│  │   Contrôle total via virtualisation                     │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▲                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │             KERNEL LEVEL                                │    │
│  │   Modifie le kernel OS et les pilotes                   │    │
│  │   DKOM : cache les processus en modif. liste kernel     │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▲                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │             BOOT LOADER LEVEL                           │    │
│  │   Remplace le bootloader original                       │    │
│  │   S'exécute avant l'OS                                  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▲                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │             LIBRARY LEVEL                               │    │
│  │   Remplace les system calls légitimes                   │    │
│  │   (DLL hijacking, .so substitution)                     │    │
│  └─────────────────────────────────────────────────────────┘    │
│                          ▲                                      │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │             APPLICATION LEVEL                           │    │
│  │   Remplace les binaires légitimes                       │    │
│  │   Modifie le comportement d'applications                │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 📋 Rootkits connus (CEH)

| Rootkit | Niveau | Description |
|---------|--------|-------------|
| **Horse Pill** | Kernel (Linux) | Réside dans initrd — modifie l'espace de noms des conteneurs |
| **GrayFish** | Kernel (Windows) | Injecte dans le boot record, stockage caché, exécution de commandes |
| **Azazel** | User-space (Linux) | Rootkit LD_PRELOAD — cache fichiers, processus, connexions |
| **Sirefef / ZeroAccess** | Kernel | Modifie les processus internes de l'OS pour éviter l'AV |
| **Necurs** | Kernel | Backdoor + accès distant, filtre réseau, spam, installe faux AV |
| **Flame** | Rootkit APT | Espionnage niveau état — capture clavier, écran, audio |
| **Rustock** | Boot | Rootkit bootkit spammeur |

---

## 🔬 Comment fonctionne un rootkit Kernel

```
Technique : DKOM (Direct Kernel Object Manipulation)

Liste normale des processus :
Process1 → Process2 → Process3[MALWARE] → Process4

Après DKOM :
Process1 → Process2 ─────────────────► Process4
                        Process3 retiré de la liste !
                        (toujours en mémoire, invisible)

→ cmd.exe et tasklist ne voient plus le processus malveillant
```

---

## 🔍 Détection des Rootkits

### Méthodes de détection

| Méthode | Description |
|---------|-------------|
| **Integrity-Based** | Snapshots de l'état système — compare nouveau vs baseline |
| **Signature-Based** | Base de données de signatures de rootkits connus |
| **Heuristics/Behavior** | Détecte les déviations par rapport à la normale |
| **Runtime Execution Path** | Compare les chemins d'exécution avant et après infection |
| **Cross-View Based** | Énumère les éléments critiques, hache les résultats, compare à une baseline |

### Outils de détection

| Outil | Plateforme | Description |
|-------|-----------|-------------|
| **GMER** | Windows | Détecte les rootkits kernel par cross-view analysis |
| **RootkitRevealer** | Windows (Sysinternals) | Compare registry et filesystem |
| **chkrootkit** | Linux | Vérifie les fichiers/commandes systèmes compromis |
| **rkhunter** | Linux | Rootkit Hunter — analyse les signatures |
| **Sophos Anti-Rootkit** | Windows | Scan et nettoyage |
| **McAfee RootkitRemover** | Windows | Outil de suppression spécialisé |
| **Malwarebytes Anti-Rootkit** | Windows | Détection et nettoyage |

```bash
# Linux — vérification rapide
chkrootkit
rkhunter --check --sk

# Windows — GMER (GUI)
# Onglet "Rootkit" → Scan complet
# Rechercher processus cachés, pilotes dissimulés, hooks
```

---

## 🛡️ Défense contre les Rootkits

```
✅ Installer les rootkits après un BOOT PROPRE (OS de confiance)
✅ Vérifier l'intégrité des fichiers système (SFC /scannow)
✅ Utiliser Secure Boot (UEFI) pour les rootkits firmware
✅ Maintenir une baseline des fichiers système
✅ Activer Secure Boot + TPM
✅ Monitorer les pilotes chargés
✅ Analyser régulièrement avec des outils cross-view
✅ Réinstaller l'OS si compromission confirmée
✅ Mettre à jour le firmware (BIOS/UEFI)
✅ Activer Windows Defender Credential Guard
✅ Activer Virtualization-Based Security (VBS) sur Windows 10/11
```

---

---

## PARTIE 2 — NTFS Alternate Data Streams (ADS)

## 📖 Définition

**NTFS ADS** (Alternate Data Streams) est une fonctionnalité du système de fichiers Windows NTFS permettant d'associer plusieurs flux de données à un seul fichier.

```
Fichier NTFS :
┌─────────────────────────────────────┐
│  readme.txt                         │
│  ├─ :$DATA (flux principal visible) │
│  │   "Contenu légitime du fichier"  │
│  └─ :malware.exe (ADS CACHÉ !)      │
│       [code malveillant ici]        │
└─────────────────────────────────────┘
```

> 💡 **Origine** : ADS a été créé pour assurer la compatibilité avec le système de fichiers HFS de macOS — chaque fichier Mac avait un flux principal et un flux ressource.

---

## 🔴 Exploitation des NTFS ADS

### Cacher un fichier dans un ADS

```bash
# Cacher malware.exe dans le flux ADS de readme.txt
type malware.exe > readme.txt:malware.exe

# Cacher une chaîne de texte dans un ADS
echo "texte caché" > readme.txt:secret.txt

# Exécuter depuis un ADS (Windows Vista+)
wmic process call create "C:\chemin\readme.txt:malware.exe"

# Ou via rundll32/start
start C:\chemin\readme.txt:malware.exe
```

### Ce qui est visible vs caché

```bash
# dir standard — NE MONTRE PAS les ADS !
dir readme.txt
# → readme.txt     1,234  ← Taille FAUSSE (ne compte pas l'ADS)

# Afficher les ADS
dir /r readme.txt
# → readme.txt     1,234
# → readme.txt:malware.exe:$DATA    250,112  ← ADS visible !
```

---

### Techniques avancées avec ADS

```batch
REM Stocker des données de configuration cachées
notepad.exe C:\legit.txt:config.ini

REM Créer un ADS dans un répertoire
echo "payload" > C:\Windows:hidden.exe

REM Créer un ADS null byte (plus discret)
type malware.exe > "legit.txt: .exe"
```

---

## 🔍 Détection des NTFS ADS

```bash
# Windows natif
dir /r C:\          # Affiche tous les ADS du répertoire
dir /r /s C:\       # Récursif

# Sysinternals Streams
streams.exe -s C:\Windows\Temp
streams.exe -d C:\Windows\Temp\readme.txt:malware.exe  # Supprimer l'ADS

# PowerShell
Get-Item -Path "C:\temp\*" -Stream "*" | Where-Object {$_.Stream -ne ":$DATA"}

# Ou récursif
Get-ChildItem -Recurse | ForEach-Object {
    Get-Item $_.FullName -Stream * | Where-Object {$_.Stream -ne ":$DATA"}
}
```

**Outils tiers :**
- **LADS** (List Alternate Data Streams)
- **ADS Spy** (GUI)
- **ADSSpy** (Merijn)
- **HijackThis** (inclut la détection ADS)
- **Tripwire** (vérification d'intégrité)

---

## 🛡️ Défense contre NTFS ADS

```
✅ Utiliser un vérificateur d'intégrité tiers (Tripwire, AIDE)
✅ Auditer régulièrement avec "dir /r" ou Sysinternals Streams
✅ Appliquer les politiques de restriction logicielles
✅ Surveiller les Event IDs 4663 (accès objet) avec auditing activé
✅ Utiliser des systèmes de fichiers non-NTFS pour les données critiques (FAT32 ne supporte pas ADS)
✅ Déployer un EDR qui détecte les écritures ADS suspectes
```

---

## 🎯 Résumé comparatif

| Technique | Détection | Persistance | Niveau |
|-----------|-----------|-------------|--------|
| **Rootkit Kernel** | Très difficile | Survit reboot | Profond |
| **Rootkit Hypervisor** | Quasi impossible | Permanent | Très profond |
| **Rootkit Firmware** | Extrêmement difficile | Survit formatage | Maximum |
| **NTFS ADS** | Difficile si pas d'outil spécifique | Persistant | Système de fichiers |
| **Rootkit Application** | Plus facile (AV) | Limité | Surface |

---

[← Maintaining Access](./05-maintaining-access.md) | [→ Stéganographie](./07-steganography.md)
