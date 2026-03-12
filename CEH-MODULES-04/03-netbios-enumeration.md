# 🖥️ 03 — Énumération NetBIOS

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

## 📖 Rappel NetBIOS

**NetBIOS** (Network Basic Input/Output System) est un protocole Windows permettant :
- Le partage de fichiers et d'imprimantes
- La communication entre systèmes sur un réseau local

| Port | Protocole | Service |
|------|-----------|---------|
| **137** | UDP | NetBIOS Name Service (NBNS) |
| **138** | UDP | NetBIOS Datagram Service |
| **139** | TCP | NetBIOS Session Service (SMB) |

> ⚠️ Microsoft **ne supporte pas** la résolution de noms NetBIOS pour **IPv6**

---

## 🎯 Ce qu'un attaquant peut obtenir (port 139 ouvert)

```
Port 139 ouvert
      ↓
Vérification du partage fichiers/imprimantes activé
      ↓
Énumération des noms NetBIOS
      ↓
┌─────────────────────────────────────┐
│  • Lecture/écriture sur le système  │
│  • Liste du système de fichiers     │
│  • Lancement d'attaque DoS          │
└─────────────────────────────────────┘
```

---

## 📊 Table des codes NetBIOS

| Nom | Code NetBIOS | Type | Information obtenue |
|-----|-------------|------|---------------------|
| `<host name>` | `<00>` | UNIQUE | Hostname |
| `<domain>` | `<00>` | GROUP | Nom de domaine |
| `<host name>` | `<03>` | UNIQUE | Service Messenger (ordinateur) |
| `<username>` | `<03>` | UNIQUE | Service Messenger (utilisateur connecté) |
| `<host name>` | `<20>` | UNIQUE | Service serveur en cours |
| `<domain>` | `<1D>` | GROUP | Nom du Master Browser pour le sous-réseau |
| `<domain>` | `<1B>` | UNIQUE | Nom du Domain Master Browser (PDC) |
| `<domain>` | `<1E>` | GROUP | Élections du service Browser |

---

## 🔧 Nbtstat — Outil Windows

**Nbtstat** est un utilitaire Windows de dépannage pour la résolution de noms NetBIOS.

### Syntaxe
```bash
nbtstat [-a <remotename>] [-A <IPaddress>] [-c] [-n] [-r] [-R] [-RR] [-s] [-S] [<interval>] [-?]
```

### Paramètres clés

| Paramètre | Fonction |
|-----------|----------|
| `-a <remotename>` | Table des noms NetBIOS d'un ordinateur distant (par nom) |
| `-A <IPaddress>` | Table des noms NetBIOS d'un ordinateur distant (par IP) |
| `-c` | Contenu du cache NetBIOS (noms + IPs résolues) |
| `-n` | Noms enregistrés localement par les applications NetBIOS |
| `-r` | Compteur de noms résolus par broadcast ou WINS |
| `-R` | Purge le cache et recharge les entrées #PRE du fichier Lmhosts |
| `-RR` | Libère et re-enregistre tous les noms auprès du serveur de noms |
| `-s` | Table des sessions NetBIOS (IPs → noms NetBIOS) |
| `-S` | Sessions NetBIOS en cours + statut avec IPs |
| `<interval>` | Réaffiche les statistiques, pause chaque N secondes |
| `-?` | Aide |

---

## 💻 Exemples de commandes

### Obtenir la table de noms d'une machine distante
```bash
# Par nom NetBIOS
nbtstat -a <nom_machine>

# Par adresse IP
nbtstat -A 10.10.1.11
```

**Exemple de sortie :**
```
Ethernet 2:
Node IpAddress: [10.10.1.19] Scope Id: []

        NetBIOS Remote Machine Name Table

    Name               Type         Status
    ─────────────────────────────────────────
    WINDOWS11    <00>  UNIQUE        Registered
    WORKGROUP    <00>  GROUP         Registered
    WINDOWS11    <20>  UNIQUE        Registered
    WORKGROUP    <1E>  GROUP         Registered
    WORKGROUP    <1D>  UNIQUE        Registered
    ☺☺_MSBROWSE_☺<01> GROUP         Registered

MAC Address = XX-XX-XX-XX-XX-XX
```

---

### Afficher le cache NetBIOS
```bash
nbtstat -c
```

**Exemple de sortie :**
```
NetBIOS Remote Cache Name Table

    Name          Type    Host Address   Life [sec]
    ──────────────────────────────────────────────
    WINDOWS11  <20> UNIQUE   10.10.1.11      488
```

---

## 🛠️ Outils d'énumération NetBIOS

### NetBIOS Enumerator
> Source : https://nbtenum.sourceforge.net

- Énumère les **noms NetBIOS**, noms d'utilisateurs, noms de domaine et adresses **MAC**
- Fonctionne sur une plage d'adresses IP

### Nmap + NSE Script
```bash
# Énumération NetBIOS avec Nmap
nmap -sV -v --script nbstat.nse <target IP>
```

> Par défaut : affiche le nom de la machine et l'utilisateur connecté  
> Avec verbosité augmentée : affiche **tous** les noms associés au système

### Autres outils

| Outil | URL |
|-------|-----|
| Global Network Inventory | https://magnetosoft.com |
| Advanced IP Scanner | https://www.advanced-ip-scanner.com |
| Hyena | https://www.systemtools.com |
| Nsauditor Network Security Auditor | https://www.nsauditor.com |

---

## 👤 Énumération des comptes utilisateurs — PsTools

La suite **PsTools** permet de contrôler et gérer des systèmes distants en ligne de commande.

### PsExec
Remplacement léger de Telnet — exécute des processus sur des systèmes distants.
```bash
psexec [\computer] [-u user [-p psswd]] [-h] [-l] [-s] executable [arguments]
```

### PsFile
Liste les fichiers ouverts à distance sur un système.
```bash
psfile [\\RemoteComputer [-u Username [-p Password]]] [[Id | path] [-c]]
```

### PsGetSid
Traduit les SIDs en noms d'affichage et vice versa.
```bash
psgetsid [\\computer] [-u username [-p password]] [account|SID]
```

### PsKill
Termine des processus locaux ou distants.
```bash
pskill [-t] [\\computer [-u username] [-p password]] <process name | process id>
```

### PsInfo
Collecte des informations clés sur le système (OS, CPU, RAM, date d'installation…).

### PsLoggedOn
Affiche les utilisateurs connectés localement et via les partages réseau.
```bash
psloggedon [-l] [-x] [\\computername | username]
```

### PsLogList
Vide le contenu d'un journal d'événements (local ou distant).

### PsPasswd
Change les mots de passe de comptes (locaux ou distants).
```bash
pspasswd [\\computer] [-u user [-p psswd]] Username [NewPassword]
```

### PsShutdown
Arrête ou redémarre un ordinateur local ou distant.

---

## 🌐 Net View — Partages réseau

```bash
# Afficher les ressources partagées d'un ordinateur
net view \\<computername>

# Afficher TOUS les partages (y compris cachés)
net view \\<computername> /ALL

# Afficher tous les partages du domaine
net view /domain

# Afficher les partages d'un domaine spécifique
net view /domain:<domain_name>
```

---

## ⚡ Synthèse — Ce que révèle l'énumération NetBIOS

```
NetBIOS Enumeration
├── Noms de machines (hostnames)
├── Noms de domaine
├── Adresses MAC
├── Utilisateurs connectés
├── Partages réseau
├── Services actifs
└── Contrôleur de domaine principal (PDC)
```

---

[← Ports & Services](./02-ports-services.md) | [→ SNMP Enumeration](./04-snmp-enumeration.md)
