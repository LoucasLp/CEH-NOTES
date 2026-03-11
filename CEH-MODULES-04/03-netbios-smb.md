# 03 — Énumération NetBIOS & SMB

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## 🔵 NetBIOS — Network Basic Input/Output System

### Qu'est-ce que NetBIOS ?
NetBIOS permet aux applications sur des réseaux locaux de communiquer. Windows l'utilise pour :
- Le partage de fichiers et d'imprimantes
- La résolution de noms de machines
- Les communications inter-processus

### Structure du Nom NetBIOS
```
┌─────────────────────────────────────────┐
│  15 caractères : Nom de la machine      │
│  + 1 caractère : Code du service (hex)  │
└─────────────────────────────────────────┘
Exemple : WORKSTATION01   <00>  → Workstation Service
          WORKSTATION01   <20>  → File Server
```

### Codes NetBIOS Importants
| Code (Hex) | Type | Description |
|------------|------|-------------|
| `<00>` | Unique | Workstation Service |
| `<03>` | Unique | Messenger Service |
| `<06>` | Unique | RAS Server Service |
| `<20>` | Unique | **File Server Service** ← cible principale |
| `<21>` | Unique | RAS Client Service |
| `<1B>` | Unique | Domain Master Browser |
| `<1C>` | Group | Domain Controllers |
| `<1D>` | Unique | Master Browser |
| `<1E>` | Group | Browser Service Elections |

---

## 🛠️ Outils NetBIOS

### 1. `nbtstat` (Windows natif)

```cmd
# Afficher la table NetBIOS locale
nbtstat -n

# Afficher la table NetBIOS d'une machine distante
nbtstat -a <IP_TARGET>
nbtstat -A <IP_TARGET>

# Afficher le cache de noms NetBIOS
nbtstat -c

# Statistiques
nbtstat -s
```

**Exemple de sortie :**
```
NetBIOS Remote Machine Name Table
   Name               Type         Status
---------------------------------------------
WORKGROUP      <00>  GROUP        Registered
WIN-TARGET     <00>  UNIQUE       Registered
WIN-TARGET     <20>  UNIQUE       Registered
```

### 2. `net use` — Sessions Nulles

```cmd
# Établir une session nulle (anonyme)
net use \\<IP_TARGET>\IPC$ "" /user:""

# Vérifier les partages disponibles
net view \\<IP_TARGET>

# Lister les utilisateurs (si session nulle acceptée)
net user /domain

# Lister les groupes
net group /domain
```

### 3. `enum4linux` (Linux — outil clé CEH)

```bash
# Énumération complète
enum4linux -a <IP_TARGET>

# Lister les utilisateurs
enum4linux -u <IP_TARGET>

# Lister les partages
enum4linux -s <IP_TARGET>

# Lister les groupes
enum4linux -g <IP_TARGET>

# Informations sur le domaine
enum4linux -n <IP_TARGET>

# Politique de mots de passe
enum4linux -P <IP_TARGET>
```

### 4. Nmap — Scripts NSE NetBIOS

```bash
# Informations NetBIOS
nmap -sV --script nbstat.nse <IP_TARGET>

# Enumération SMB
nmap --script smb-enum-users <IP_TARGET>
nmap --script smb-enum-shares <IP_TARGET>
nmap --script smb-enum-groups <IP_TARGET>
nmap --script smb-os-discovery <IP_TARGET>

# Toutes les énumérations SMB
nmap --script smb-enum-* <IP_TARGET>

# Vérification des vulnérabilités SMB
nmap --script smb-vuln* <IP_TARGET>
```

---

## 🔵 SMB — Server Message Block

### Architecture SMB
```
Client Windows              Serveur Windows
      │                           │
      │  ── TCP 445 ─────────────→│
      │  (SMB Direct Host)        │
      │                           │
      │  ── TCP 139 ─────────────→│
      │  (SMB over NetBIOS)       │
      │                           │
      └──────── Partages ─────────┘
              Fichiers, imprimantes,
              IPC$, ADMIN$, C$
```

### Partages Administratifs Windows
| Partage | Description | Usage attaquant |
|---------|-------------|-----------------|
| `IPC$` | Inter-Process Communication | Sessions nulles, énumération |
| `ADMIN$` | Accès administrateur distant | Accès au dossier Windows |
| `C$` | Partage administratif du C: | Accès complet au disque |
| `D$`, `E$`... | Autres disques | Accès aux partages disques |
| `PRINT$` | Pilotes d'imprimantes | Rarement utile |
| `SYSVOL` | Politiques de groupe | Scripts de connexion, GPO |
| `NETLOGON` | Scripts de connexion | Scripts d'authentification |

### Commandes SMB Essentielles

```bash
# --- LINUX ---

# smbclient - Connexion à un partage SMB
smbclient //<IP_TARGET>/share -U username
smbclient //<IP_TARGET>/IPC$ -N  # connexion nulle

# Lister les partages disponibles
smbclient -L //<IP_TARGET> -N
smbclient -L //<IP_TARGET> -U username

# --- WINDOWS ---
# Se connecter à un partage
net use Z: \\<IP_TARGET>\C$ password /user:domain\admin

# Voir les partages d'une machine
net view \\<IP_TARGET>

# --- NMAP ---
nmap -p 139,445 --script=smb-security-mode <IP_TARGET>
```

### SMBMap (outil CEH v13)
```bash
# Énumérer les partages avec les permissions
smbmap -H <IP_TARGET>

# Avec credentials
smbmap -H <IP_TARGET> -u username -p password -d domain

# Lister récursivement le contenu
smbmap -H <IP_TARGET> -R

# Rechercher des fichiers spécifiques
smbmap -H <IP_TARGET> -R -A ".txt"
```

---

## 🔵 WINS — Windows Internet Name Service

WINS est l'implémentation Microsoft de NBNS (NetBIOS Name Service).

```
Client                    Serveur WINS
  │                            │
  │── Enregistrement nom ─────→│ (au démarrage)
  │                            │
  │── Requête résolution ─────→│
  │←── Réponse IP ─────────────│
```

**Fonctions :**
- Résolution `nom NetBIOS ↔ adresse IP`
- Réduction du broadcast NetBIOS
- Maintien d'une base de données dynamique

---

## 📋 Récapitulatif des Commandes

```bash
# === ÉNUMÉRATION NETBIOS/SMB COMPLÈTE ===

# 1. Découverte rapide
nbtstat -A <IP>                    # Windows
nbtscan <IP_RANGE>                 # Linux scan de plage

# 2. Énumération complète Linux
enum4linux -a <IP>

# 3. Partages disponibles
smbclient -L //<IP> -N

# 4. Scripts Nmap
nmap -p 137,139,445 --script smb-enum-shares,smb-enum-users <IP>

# 5. Sessions nulles Windows
net use \\<IP>\IPC$ "" /user:""
net view \\<IP>

# 6. Informations système via SMB
nmap --script smb-os-discovery <IP>
```

---

## 🛡️ Contre-mesures

- Désactiver les sessions nulles via la registry :
  ```
  HKLM\SYSTEM\CurrentControlSet\Control\LSA
  RestrictAnonymous = 2
  ```
- Bloquer les ports 137, 139, 445 sur le périmètre
- Désactiver NetBIOS si non nécessaire
- Auditer régulièrement les partages accessibles
- Utiliser SMBv3 avec chiffrement obligatoire

---

[← Ports & Services](./02-ports-services.md) | [SNMP & LDAP →](./04-snmp-ldap.md)
