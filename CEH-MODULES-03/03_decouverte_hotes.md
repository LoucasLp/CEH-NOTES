# 🌐 03 — Techniques de Découverte d'Hôtes (Host Discovery)

> **Module 3 — CEH v13** | [← Retour au sommaire](./README.md)

---

## Vue d'ensemble

La découverte d'hôtes consiste à identifier les machines **actives/vivantes** sur un réseau avant de lancer des scans plus approfondis.

```
┌──────────────────────────────────────────────────┐
│           HOST DISCOVERY TECHNIQUES              │
├──────────────┬───────────────┬───────────────────┤
│  ARP Ping    │  UDP Ping     │  ICMP Ping        │
│              │               │  ├─ ECHO           │
│  TCP Ping    │  IP Protocol  │  ├─ ECHO Sweep     │
│  ├─ SYN Ping │  Scan         │  ├─ Timestamp      │
│  └─ ACK Ping │               │  └─ Address Mask   │
└──────────────┴───────────────┴───────────────────┘
```

---

## 1. ARP Ping Scan

**Option Nmap/Zenmap :** `-PR`  
**Option pour désactiver ARP :** `--disable-arp-ping`

L'ARP Ping Scan envoie des **paquets ARP** pour découvrir tous les périphériques actifs dans une plage IPv4, même ceux cachés derrière des firewalls restrictifs.

### Comment ça fonctionne

```
Attaquant                           Réseau LAN
    │                                   │
    │─── ARP Request (who has X.X.X.X?)─►│
    │                                   │
    │◄── ARP Reply (MAC: XX:XX:XX:XX)───│  ← Hôte actif
    │                                   │
    │─── ARP Request ────────────────►  │
    │    [pas de réponse]               │  ← Hôte inactif
```

### Caractéristiques
- Montre l'adresse **MAC** de l'interface réseau
- Révèle les MAC de tous les appareils partageant la même adresse IPv4 sur le LAN
- Si pas de réponse → entrée **incomplète** ajoutée dans la table ARP du kernel

> ⚠️ **Note :** `-sn` désactive le port scan dans Nmap. Comme Nmap utilise l'ARP ping par défaut, utiliser `--disable-arp-ping` pour effectuer d'autres types de ping scans.

---

## 2. UDP Ping Scan

**Option Nmap/Zenmap :** `-PU`  
**Port par défaut :** `40125` (port peu commun configuré via `DEFAULT_UDP_PROBE_PORT_SPEC`)

Similaire au TCP Ping Scan mais utilise le protocole **UDP**.

### Logique de réponse

| Réponse | Signification |
|---------|--------------|
| UDP Response | Hôte **actif** |
| Host/Network unreachable | Hôte **offline** ou injoignable |
| TTL exceeded | Hôte **injoignable** |

### ✅ Avantage clé
> Détecte les systèmes **derrière des firewalls** avec un filtrage TCP strict, laissant le trafic UDP passer.

---

## 3. ICMP Ping Scans

### 3.1 ICMP ECHO Ping Scan
**Option Nmap/Zenmap :** `-PE`

Envoie des requêtes **ICMP ECHO** à l'hôte. Si actif → répond avec **ICMP ECHO Reply**.

```
Attaquant ──── ICMP ECHO Request ────► Cible
          ◄─── ICMP ECHO Reply  ──────  (si actif)
```

> ⚠️ **Limitation :** Ne fonctionne **pas** sur les réseaux Windows (la pile TCP/IP ne répond pas aux probes ICMP vers l'adresse broadcast).  
> ✅ Fonctionne sur **UNIX/Linux/BSD**.

**Options Nmap complémentaires :**
- `-L` → augmenter le nombre de pings en parallèle
- `-T` → ajuster le timeout

---

### 3.2 ICMP ECHO Ping Sweep
**Option Nmap/Zenmap :** `-PE` avec liste d'IPs

Un **ping sweep** (ou ICMP sweep) détermine la plage d'adresses IP correspondant à des hôtes actifs en envoyant des requêtes ICMP ECHO à **plusieurs hôtes simultanément**.

#### Structure d'un paquet ICMP
```
┌────────────────────────────────────┐
│  Paquet ICMP Ping : 64 octets      │
│  ├── 8 octets  : En-tête protocole │
│  └── 56 octets : Données           │
└────────────────────────────────────┘
```

#### Informations fournies par un ping
- **Round-trip time** : temps aller-retour du paquet
- **Résolution hostname** : associe nom ↔ IP
- **Disponibilité** : hôte actif ou non

> 💡 Les attaquants calculent d'abord les **subnet masks** pour identifier le nombre d'hôtes, puis utilisent le ping sweep pour créer un inventaire des systèmes actifs.

**Exemple Nmap :**
```bash
nmap -sn -PE 10.10.1.5-24
```
*Résultat : affiche les hôtes actifs avec "Host is up"*

---

### 3.3 ICMP Timestamp Ping Scan
**Option Nmap/Zenmap :** `-PP`

Envoie un message de **requête timestamp** pour obtenir l'heure courante de l'hôte cible.

- La réponse est **conditionnelle** (dépend de la configuration admin)
- Généralement utilisé pour la **synchronisation horaire**
- Efficace quand l'admin **bloque** l'ICMP ECHO traditionnel

---

### 3.4 ICMP Address Mask Ping Scan
**Option Nmap/Zenmap :** `-PM`

Envoie une requête **ICMP Address Mask** pour obtenir des infos sur le **subnet mask**.

- Réponse conditionnelle (selon configuration admin)
- Alternative efficace quand ICMP Echo est bloqué

---

## 4. TCP Ping Scans

### 4.1 TCP SYN Ping Scan
**Option Nmap/Zenmap :** `-PS`  
**Port par défaut :** 80

```
Attaquant                        Cible
    │                               │
    │─── SYN (vide) ───────────────►│
    │                               │
    │◄── ACK ──────────────────────│  ← Hôte actif confirmé
    │                               │
    │─── RST (fin de découverte) ──►│
```

**Spécification de ports multiples :**
```bash
nmap -PS22-25,80,113,1050,35000 <target>
# Pas d'espace entre -PS et le numéro de port !
```

#### ✅ Avantages
- Scan des machines **en parallèle** → jamais de timeout
- Détermine si l'hôte est actif **sans créer de connexion**
- **Aucune trace** dans les logs système ou réseau

---

### 4.2 TCP ACK Ping Scan
**Option Nmap/Zenmap :** `-PA`  
**Port par défaut :** 80

Envoie un paquet **TCP ACK vide** directement à la cible.

```
Attaquant                        Cible
    │                               │
    │─── ACK (vide) ───────────────►│
    │                               │
    │◄── RST ──────────────────────│  ← Hôte actif confirmé
```

> La réception du paquet RST indique que l'hôte est **actif**.

#### ✅ Avantage clé
> Les firewalls bloquent souvent les paquets **SYN**. Dans ce cas, le **ACK probe** peut contourner ces règles firewall plus efficacement.

---

## 5. IP Protocol Ping Scan
**Option Nmap/Zenmap :** `-PO`

Envoie des paquets IP avec des **numéros de protocole IP différents**, en espérant obtenir une réponse.

**Protocoles envoyés par défaut :**
- ICMP (protocole 1)
- IGMP (protocole 2)
- IP-in-IP (protocole 4)

> Configuration via `DEFAULT_PROTO_PROBE_PORT_SPEC` dans `nmap.h`

---

## 6. Outils de Ping Sweep

### Angry IP Scanner
> 🌐 Source : [https://angryip.org](https://angryip.org)

Scanner d'adresses IP et de ports avec approche **multi-threadée** (un thread par IP).

**Fonctionnalités :**
- Scan de plages IP configurables
- Résolution hostname
- Détection adresses MAC
- Informations **NetBIOS** (nom machine, workgroup, utilisateur connecté)
- Export : CSV, TXT, XML, IP-Port list
- Détection de serveurs web

### Autres outils de Ping Sweep

| Outil | URL |
|-------|-----|
| SolarWinds Engineer's Toolset | solarwinds.com |
| NetScanTools Pro | netscantools.com |
| Colasoft Ping Tool | colasoft.com |
| Advanced IP Scanner | advanced-ip-scanner.com |
| OpUtils | manageengine.com |

---

## 7. Tableau récapitulatif

| Technique | Option Zenmap | Protocole | Contourne Firewall |
|-----------|--------------|-----------|-------------------|
| ARP Ping | `-PR` | ARP | ✅ (LAN only) |
| UDP Ping | `-PU` | UDP | ✅ (TCP strict) |
| ICMP ECHO | `-PE` | ICMP | ❌ (souvent bloqué) |
| ICMP Timestamp | `-PP` | ICMP | ✅ (si ECHO bloqué) |
| ICMP Addr Mask | `-PM` | ICMP | ✅ (si ECHO bloqué) |
| TCP SYN Ping | `-PS` | TCP | ✅ (discret, pas de logs) |
| TCP ACK Ping | `-PA` | TCP | ✅ (si SYN bloqué) |
| IP Protocol | `-PO` | Multi | ✅ (protocoles variés) |

---

[← Outils de Scanning](./02_outils_de_scanning.md) | [→ Techniques de Port Scanning](./04_techniques_port_scanning.md)
