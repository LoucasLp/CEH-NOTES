# 🔐 08 — Énumération IPsec, VoIP, RPC, Linux & SMB

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

# PARTIE 1 — IPsec Enumeration

## 📖 Présentation IPsec

**IPsec** est la technologie la plus utilisée pour les VPN (gateway-to-gateway et remote access).

| Composant | Rôle |
|-----------|------|
| **ESP** | Encapsulating Security Payload — chiffrement |
| **AH** | Authentication Header — intégrité |
| **IKE** | Internet Key Exchange — gestion des clés |
| **ISAKMP** | Internet Security Association Key Management Protocol |
| **Port** | UDP **500** |

---

## 🔍 Énumération IPsec

### Étape 1 — Détecter la présence d'un VPN gateway
```bash
# Scan ISAKMP sur UDP 500
nmap -sU -p 500 <target IP>
```

### Étape 2 — Fingerprinting avec ike-scan
```bash
# Découverte initiale du VPN IPsec
ike-scan -M <target gateway IP>
```

---

## 🛠️ ike-scan

> Source : https://github.com

**ike-scan** découvre les hôtes IKE et les fingerprinte via le pattern de retransmission.

### Fonctionnalités

| Fonction | Description |
|----------|-------------|
| **Discovery** | Identifie les hôtes exécutant IKE sur une plage IP |
| **UDP Backoff Fingerprinting** | Compare les délais de retransmission contre les patterns connus |
| **Vendor ID Fingerprinting** | Compare les Vendor ID payloads avec les patterns connus |
| **Transform Enumeration** | Identifie les algorithmes IKE phase 1 supportés (chiffrement, hash) |
| **User Enumeration** | Découvre des usernames VPN valides (certains systèmes) |
| **PSK Cracking** | Crack offline des pre-shared keys (mode Agressif) |

### Flux d'attaque
```
ike-scan → hash + paramètres
              ↓
         psk-crack (inclus dans ike-scan)
              ↓
         Dictionnaire / Brute-force offline
```

---

# PARTIE 2 — VoIP Enumeration

## 📖 Présentation VoIP

**VoIP** remplace le réseau téléphonique commuté (PSTN) traditionnel.  
**SIP** (Session Initiation Protocol) gère les appels voix/vidéo sur IP.

| Ports SIP | Protocole | Usage |
|-----------|-----------|-------|
| **2000, 2001** | TCP/UDP | VoIP général |
| **5060** | TCP/UDP | SIP non chiffré |
| **5061** | TCP/UDP | SIP chiffré (TLS) |

---

## 🎯 Informations extraites via VoIP

```
VoIP Enumeration →
├── Gateways/serveurs VoIP
├── Systèmes IP-PBX
├── Adresses IP des User-Agents
└── Extensions utilisateurs (softphones/téléphones VoIP)
```

---

## ⚠️ Attaques possibles après énumération VoIP

- 🛑 Attaques DoS
- 👥 Détournement de session
- 📞 Usurpation de Caller ID
- 👂 Écoute clandestine
- 📩 SPIT (Spam over Internet Telephony)
- 🎣 VoIP Phishing (Vishing)

---

## 🛠️ Outils VoIP

### Svmap
> Source : https://github.com

Scanner open-source identifiant les équipements SIP et serveurs PBX.

```bash
# Énumération SIP sur un réseau
svmap <target network range/IP>
```

**Fonctionnalités :**
- Identifie équipements SIP sur ports standard et non-standard
- Scan de grandes plages réseau
- Scan d'un hôte sur différents ports
- Faire sonner tous les téléphones (méthode INVITE)

### Metasploit — SIP Username Enumerator
Scan des usernames/extensions numériques de téléphones VoIP.

---

# PARTIE 3 — RPC Enumeration

## 📖 Présentation RPC

**RPC** (Remote Procedure Call) permet la communication client/serveur dans les programmes distribués.

| Composant | Rôle |
|-----------|------|
| **Client** | Initie les appels |
| **Serveur** | Exécute les procédures |
| **Endpoint** | Port sur lequel le serveur écoute |
| **Endpoint Mapper** | Mappe les services RPC vers les ports |
| **Portmapper** | Écoute TCP/UDP **111** |

---

## 💻 Commandes Nmap pour RPC

```bash
# Scanner les services RPC
nmap -sR <target IP/network>

# Scan agressif (OS, version, scripts, traceroute)
nmap -T4 -A <target IP/network>
```

> 💡 **Note** : Les pare-feux filtrent souvent le portmapper (port 111). L'attaquant scanne alors de larges plages de ports pour trouver les services RPC directement accessibles.

### NetScanTools Pro
L'outil RPC Info de NetScanTools Pro détecte et accède au daemon portmapper (port 111) sur les machines Unix/Linux.

---

# PARTIE 4 — Unix/Linux User Enumeration

## 💻 Commandes d'énumération Unix/Linux

### rusers
Affiche les utilisateurs connectés aux machines distantes du réseau local.
```bash
/usr/bin/rusers [-a] [-l] [-u|-h|-i] [Host ...]
```

| Option | Description |
|--------|-------------|
| `-a` | Rapport même si aucun utilisateur connecté |
| `-h` | Tri alphabétique par hostname |
| `-l` | Format long (similaire à `who`) |
| `-u` | Tri par nombre d'utilisateurs |
| `-i` | Tri par temps d'inactivité |

---

### rwho
Affiche les utilisateurs connectés aux hôtes du réseau local.
```bash
rwho [-a]
```
| Option | Description |
|--------|-------------|
| `-a` | Inclut tous les utilisateurs (même inactifs depuis 1h+) |

---

### finger
Affiche des informations détaillées sur les utilisateurs système.
```bash
finger [-l] [-m] [-p] [-s] [user ...] [user@host ...]
```

| Option | Description |
|--------|-------------|
| `-s` | Login, vrai nom, terminal, idle time, login time, bureau, téléphone |
| `-l` | Format multi-ligne + home dir, téléphone, shell, mail, .plan, .project, .forward |
| `-p` | Ne pas afficher .plan, .project, .pgpkey |
| `-m` | Ne pas faire de correspondance sur les usernames |

---

# PARTIE 5 — SMB Enumeration

## 📖 Présentation SMB

**SMB** (Server Message Block) est un protocole de transport Windows pour :
- Partage de fichiers et d'imprimantes
- Accès distant aux services Windows

| Port | Protocole | Usage |
|------|-----------|-------|
| **445** | TCP/UDP | SMB direct (moderne) |
| **137** | UDP | NetBIOS Name Service |
| **138** | UDP | NetBIOS Datagram |
| **139** | TCP | SMB via NetBIOS (ancien) |

---

## 🎯 Informations extraites via SMB

```
SMB Enumeration (Banner Grabbing) →
├── Détails OS et version
├── Services en cours
├── Versions des services
└── Base pour → SMB relay attacks + brute-force
```

---

## 💻 Commandes Nmap pour SMB

```bash
# Énumération SMB complète (OS, version, scripts, traceroute)
nmap -p 445 -A <target IP>

# Vérifier les protocoles SMB supportés
nmap -p 445 --script smb-protocols <Target IP>
nmap -p 139 --script smb-protocols <Target IP>
```

**Résultat attendu :**
```
PORT    STATE SERVICE
445/tcp open  microsoft-ds
|_smb-protocols: SMBv1, SMBv2, SMBv3
OS: Windows Server 2019
```

---

## 🛠️ Outils SMB

| Outil | Usage |
|-------|-------|
| **Nmap** | Scan, scripts NSE |
| **SMBMap** | Mapping des partages, permissions |
| **enum4linux** | Énumération complète Linux vers Windows |
| **nullinux** | Énumération via null sessions |
| **SMBeagle** | Découverte de partages non sécurisés |
| **NetScanTools Pro** | Scan réseau intégré |

---

## ⚡ Flux d'attaque SMB typique

```
1. Scanner le port 445
   nmap -p 445 <IP>
          ↓
2. Identifier la version SMB
   nmap -p 445 --script smb-protocols <IP>
          ↓
3. Banner grabbing + info OS
   nmap -p 445 -A <IP>
          ↓
4. Selon la version → exploitation
   ├─ SMBv1 → EternalBlue (MS17-010)
   ├─ Null session → enum4linux
   └─ Credentials → accès partages
```

---

## 🎯 Synthèse du module 08

| Type | Port clé | Outil principal | Risque |
|------|----------|-----------------|--------|
| **IPsec/IKE** | UDP 500 | ike-scan | Enumération VPN, PSK cracking |
| **VoIP/SIP** | 5060/5061 | Svmap, Metasploit | DoS, écoute, vishing |
| **RPC** | TCP/UDP 111 | Nmap -sR | Services vulnérables exposés |
| **Linux Users** | N/A | finger, rusers, rwho | Fuite d'infos utilisateurs |
| **SMB** | 445/139 | Nmap, enum4linux | Accès fichiers, escalade privileges |

---

[← SMTP & DNS](./07-smtp-dns-enumeration.md) | [→ Countermeasures](./09-countermeasures.md)
