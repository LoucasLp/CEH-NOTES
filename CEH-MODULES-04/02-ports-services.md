# 02 — Ports & Services à Énumérer

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## 📡 TCP vs UDP — Rappel

| Critère | TCP | UDP |
|---------|-----|-----|
| **Type** | Orienté connexion | Sans connexion |
| **Fiabilité** | ✅ Accusés de réception | ❌ Pas de garantie |
| **Retransmission** | ✅ Automatique | ❌ Non |
| **Contrôle flux** | ✅ Oui | ❌ Non |
| **Utilisation** | HTTP, FTP, SSH, SMTP | DNS, SNMP, streaming |

---

## 🗂️ Tableau des Ports d'Énumération

| Port | Proto | Service | Risques & Exploitation |
|------|-------|---------|------------------------|
| **53** | TCP/UDP | DNS Zone Transfer | Transfert de zone → liste complète des hôtes DNS |
| **135** | TCP/UDP | MS RPC Endpoint Mapper | DoS possible, énumération des services RPC |
| **137** | UDP | NetBIOS Name Service (NBNS/WINS) | Résolution noms NetBIOS, first target |
| **139** | TCP | NetBIOS Session Service (SMB) | Sessions nulles, partage fichiers/imprimantes |
| **161** | UDP | SNMP | Lecture config réseau via community strings |
| **162** | UDP | SNMP Trap | Réception alertes SNMP |
| **389** | TCP/UDP | LDAP | Accès annuaire, extraction users/groupes |
| **445** | TCP/UDP | SMB over TCP (Direct Host) | Partage fichiers Windows moderne |
| **2049** | TCP | NFS | Montage systèmes de fichiers distants |
| **25** | TCP | SMTP | Énumération comptes email (VRFY, EXPN) |
| **110** | TCP | POP3 | Accès boîtes mail |
| **143** | TCP | IMAP | Accès boîtes mail avancé |
| **3268** | TCP | LDAP Global Catalog | Requêtes AD globales |
| **3269** | TCP | LDAP GC over SSL | Requêtes AD globales sécurisées |
| **88** | TCP/UDP | Kerberos | Authentification AD, AS-REP roasting |
| **636** | TCP | LDAPS | LDAP sécurisé (SSL) |
| **5900** | TCP | VNC | Accès bureau distant |

---

## 🔎 Détail des Services Critiques

### 🔵 Port 53 — DNS (Zone Transfer)
```
UDP 53  → Requêtes DNS standard (< 512 octets)
TCP 53  → Requêtes DNS longues / Zone Transfer
```

**Fonctionnement du Zone Transfer :**
```
Client              Serveur DNS
  |                     |
  |-- axfr request ---→ |
  |                     |
  |← tous les records --|
  (noms, IPs, sous-domaines...)
```

Malwares connus utilisant le port 53 : **ADM worm**, **Bonk Trojan**

---

### 🔵 Port 135 — RPC Endpoint Mapper
```
Client RPC  →  demande quel port pour service X
                         ↓
           RPC Endpoint Mapper (port 135)
                         ↓
           Répond avec le numéro de port
```
> ⚠️ Faille dans le traitement des messages TCP/IP → possible attaque **DoS**

---

### 🔵 Port 137 — NetBIOS Name Service
```
NetBIOS Name : 16 caractères
  ├── 15 premiers : nom de la machine
  └── 16ème       : type de service (code hexa)
```

| Code | Type de service |
|------|----------------|
| `00` | Workstation Service |
| `03` | Messenger Service |
| `06` | RAS Server |
| `20` | File Server Service |
| `1B` | Domain Master Browser |
| `1D` | Master Browser |

---

### 🔵 Port 139 — NetBIOS Session (SMB over NetBIOS)
- Transfert de fichiers via réseau
- Établissement de **sessions nulles**
- Partage imprimantes

> ⚠️ Port 139 mal configuré = accès non autorisé au système de fichiers complet

---

### 🔵 Port 445 — SMB Direct
- Version moderne de SMB (sans NetBIOS)
- Windows Vista+ utilise directement le port 445
- Compatible TCP et UDP

```
SMB over NetBIOS  →  Port 139 (ancien)
SMB over TCP      →  Port 445 (moderne)
```

---

### 🔵 Port 161/162 — SNMP
```
Manager                    Agent
   |                         |
   |-- GET Request (161) --> |
   |                         |
   |<-- Response (161) ------|
   
   Agent --> TRAP (162) --> Manager
```

**Community Strings par défaut :**
| String | Accès |
|--------|-------|
| `public` | Read-Only (RO) |
| `private` | Read-Write (RW) |

---

### 🔵 Port 389 — LDAP
- Protocole d'accès aux annuaires distribués
- Active Directory utilise LDAP comme protocole principal
- Permet d'extraire : utilisateurs, groupes, OUs, politiques

---

### 🔵 Port 2049 — NFS
> ⚠️ NFS mal configuré permet :
> - Contrôle du système distant
> - Élévation de privilèges
> - Injection de backdoors/malwares

---

### 🔵 Port 25 — SMTP
**Commandes SMTP pour l'énumération :**

| Commande | Syntaxe | Description |
|----------|---------|-------------|
| `HELO` | `HELO domain.com` | Identifier le client |
| `EHLO` | `EHLO domain.com` | Version étendue de HELO |
| `VRFY` | `VRFY username` | Vérifier si un utilisateur existe |
| `EXPN` | `EXPN listname` | Développer une liste de diffusion |
| `RCPT TO` | `RCPT TO:<user@domain>` | Spécifier le destinataire |
| `MAIL FROM` | `MAIL FROM:<sender@domain>` | Spécifier l'expéditeur |

```bash
# Exemple d'énumération SMTP
telnet mail.target.com 25
EHLO test
VRFY admin
VRFY root
EXPN administrators
```

---

## 🗺️ Schéma Récapitulatif

```
 RÉSEAU CIBLE
      │
      ├── Port 53  ────────→ DNS → Cartographie des hôtes
      ├── Port 135 ────────→ RPC → Services disponibles  
      ├── Port 137 ────────→ NetBIOS → Noms de machines
      ├── Port 139 ────────→ SMB → Partages & sessions
      ├── Port 161 ────────→ SNMP → Config & topologie
      ├── Port 389 ────────→ LDAP → Users & groupes AD
      ├── Port 445 ────────→ SMB Direct → Fichiers
      ├── Port 25  ────────→ SMTP → Comptes email
      └── Port 2049────────→ NFS → Systèmes de fichiers
```

---

[← Introduction](./01-introduction-concepts.md) | [NetBIOS & SMB →](./03-netbios-smb.md)
