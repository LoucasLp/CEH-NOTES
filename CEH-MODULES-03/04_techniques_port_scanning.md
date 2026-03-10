# 🔍 04 — Techniques de Port Scanning

> **Module 3 — CEH v13** | [← Retour au sommaire](./README.md)

---

## Vue d'ensemble

```
┌─────────────────────────────────────────────────────────────┐
│                  TECHNIQUES DE PORT SCANNING                │
├────────────────┬────────────────┬───────────────────────────┤
│  TCP SCANNING  │  UDP SCANNING  │  SCTP SCANNING            │
│  ├─ Full-Open  │  Raw ICMP      │  ├─ INIT Scan             │
│  ├─ Stealth    │  RECVFROM()    │  └─ COOKIE ECHO           │
│  ├─ Inverse TCP│                │                           │
│  │  ├─ FIN     │  AUTRES        │  SSDP / List / IPv6       │
│  │  ├─ Xmas    │                │                           │
│  │  └─ NULL    │                │                           │
│  ├─ TCP Maimon │                │                           │
│  ├─ ACK Flag   │                │                           │
│  └─ IDLE/IPID  │                │                           │
└────────────────┴────────────────┴───────────────────────────┘
```

---

## 1. TCP Connect / Full-Open Scan

**Option Zenmap :** `-sT`  
> 🌐 Source : [https://insecure.org](https://insecure.org)

Utilise l'appel système `connect()` du kernel pour **tenter d'ouvrir une connexion** sur chaque port d'intérêt. Complète le **three-way handshake** complet.

```
Client                          Serveur
  │── SYN ────────────────────►│
  │◄─ SYN+ACK ─────────────────│  Port OUVERT
  │── ACK ────────────────────►│
  │── RST ────────────────────►│  (scan terminé)

  │── SYN ────────────────────►│
  │◄─ RST ─────────────────────│  Port FERMÉ
```

### Caractéristiques

| ✅ Avantages | ❌ Inconvénients |
|------------|----------------|
| Fiable, pas besoin de privilèges root | Facilement **détectable** |
| Fonctionne sans accès raw socket | Laisse des **logs** sur la cible |
| Accélération via sockets non-bloquants I/O | Filtrable par firewalls |

---

## 2. Stealth Scan (Half-Open / SYN Scan)

**Option Zenmap :** `-sS`

Le scan furtif **réinitialise la connexion TCP** avant la fin du three-way handshake → connexion **half-open**. Appelé aussi **SYN Scan** car seul le paquet SYN est envoyé.

```
Client                          Serveur
  │── SYN ────────────────────►│
  │◄─ SYN+ACK ─────────────────│  Port OUVERT → client envoie RST
  │── RST ────────────────────►│

  │── SYN ────────────────────►│
  │◄─ RST ─────────────────────│  Port FERMÉ
```

> 💡 Le service ne reçoit **jamais** la notification d'une connexion entrante → pas de logs générés.

**Usages :** Contournement des règles firewall et des systèmes de logging.

---

## 3. Inverse TCP Flag Scan

Les attaquants envoient des paquets TCP avec des flags inversés (FIN, URG, PSH) ou **sans flags** du tout.

| État du port | Réponse reçue |
|-------------|--------------|
| **Ouvert** | Aucune réponse |
| **Fermé** | RST reçu |

> ⚠️ Basé sur **RFC 793** : efficace uniquement sur les systèmes **UNIX/BSD** — Windows ignore ce standard.

### Trois variantes d'Inverse TCP

#### 3.1 FIN Scan
**Option Zenmap :** `-sF`

```bash
# Seul le flag FIN est positionné
```
- Port ouvert → **pas de réponse**
- Port fermé → **RST/ACK**

#### 3.2 NULL Scan
**Option Zenmap :** `-sN`

```bash
# Aucun flag TCP positionné
```
- Port ouvert → **pas de réponse**
- Port fermé → **RST/ACK**

#### 3.3 Xmas Scan
**Option Zenmap :** `-sX`

```bash
# FIN + URG + PSH flags activés (pattern "Xmas tree")
hping3 -F -P -U 10.0.0.25 -p 80
```
- Port ouvert → **pas de réponse**
- Port fermé → **RST**
- Certains systèmes **plantent** quand tous les flags sont activés → pattern URG-PSH-FIN

### ✅ / ❌ Inverse TCP Flag Scan

| ✅ Avantages | ❌ Inconvénients |
|------------|----------------|
| Contourne de nombreux IDS et systèmes de logs | Nécessite accès raw socket + **privilèges root** |
| Très furtif | Efficace seulement sur **BSD/UNIX** (pas Windows) |

---

## 4. TCP Maimon Scan

**Option Zenmap :** `-sM`

Similaire aux scans NULL/FIN/Xmas, mais utilise la probe **FIN/ACK**.

| Réponse | État du port |
|---------|-------------|
| Aucune réponse | `open|filtered` |
| RST | Fermé |
| ICMP unreachable (type 3, code 1,2,3,9,10,13) | Filtered |

> Sur les systèmes **BSD**, le port est considéré **ouvert** si le paquet est simplement abandonné.

---

## 5. ACK Flag Probe Scan

**Option Zenmap :** `-sA`

Envoie des paquets TCP avec le flag ACK pour analyser les champs **TTL** et **WINDOW** des paquets RST reçus.

> Exploite les vulnérabilités dans les piles TCP/IP dérivées de **BSD**.

### 5.1 TTL-Based ACK Flag Probe
**Syntaxe Zenmap :** `nmap -ttl [time] [target]`

```
Si TTL du paquet RST < 64  → Port OUVERT
Si TTL du paquet RST > 64  → Port FERMÉ
```

**Exemple de log :**
```
1: host 10.2.2.11 port 20: F:RST → ttl: 80 win: 0  ← Fermé
2: host 10.2.2.11 port 21: F:RST → ttl: 80 win: 0  ← Fermé
3: host 10.2.2.11 port 22: F:RST → ttl: 50 win: 0  ← OUVERT (50 < 64)
4: host 10.2.2.11 port 23: F:RST → ttl: 80 win: 0  ← Fermé
```

### 5.2 Window-Based ACK Flag Probe
**Option Zenmap :** `-sW`

Utilisé quand **tous les ports retournent le même TTL**.

```
Si Window value du RST ≠ 0  → Port OUVERT
Si Window value du RST = 0  → Port FERMÉ
Si ICMP unreachable         → Port FILTRÉ
```

**Exemple de log :**
```
1: host 10.2.2.12 port 20: F:RST → ttl: 64 win: 0    ← Fermé
2: host 10.2.2.12 port 21: F:RST → ttl: 64 win: 0    ← Fermé
3: host 10.2.2.12 port 22: F:RST → ttl: 64 win: 512  ← OUVERT (win ≠ 0)
4: host 10.2.2.12 port 23: F:RST → ttl: 64 win: 0    ← Fermé
```

### Vérification du Firewall via ACK Probe
```
ACK probe → Pas de réponse  → Port FILTRÉ   (stateful firewall présent)
ACK probe → RST             → Port NON FILTRÉ (pas de firewall)
```

| ✅ Avantages | ❌ Inconvénients |
|------------|----------------|
| Contourne IDS dans la plupart des cas | Extrêmement **lent** |
| | Efficace seulement sur anciens OS BSD |

---

## 6. IDLE / IPID Header Scan

**Option Zenmap :** `-sI`

Technique de scan **complètement aveugle** utilisant une machine intermédiaire (le **"zombie"**) pour scanner la cible.

> 💡 Chaque paquet IP possède un **IP Identifier (IPID)** qui augmente de 1 à chaque paquet envoyé. En sondant l'IPID, on peut savoir combien de paquets ont été envoyés depuis la dernière probe.

### Fonctionnement en 3 étapes

#### Étape 1 — Choisir un zombie et sonder son IPID
```
Attaquant                    Zombie
    │─── SYN+ACK ───────────►│
    │◄─── RST (IPID=31337) ──│  ← X = 31337
```

#### Étape 2.1 — Port OUVERT sur la cible
```
Attaquant               Cible                  Zombie
    │                    │                        │
    │── SYN (IP=Zombie)─►│                        │
    │                    │─── SYN+ACK ───────────►│
    │                    │◄── RST (IPID=31338) ───│  ← X+1
```

#### Étape 2.2 — Port FERMÉ sur la cible
```
Attaquant               Cible                  Zombie
    │                    │                        │
    │── SYN (IP=Zombie)─►│                        │
    │                    │─── RST ───────────────►│ (zombie reste idle)
```

#### Étape 3 — Re-sonder le zombie
```
Si IPID zombie = X+2  → Port OUVERT sur la cible
Si IPID zombie = X+1  → Port FERMÉ sur la cible
```

**Commande Nmap :**
```bash
nmap -Pn -p- -sI 10.10.1.11 10.10.1.19
# -sI [zombie IP] [target IP]
```

---

## 7. UDP Scan

**Option Zenmap :** `-sU`

Utilise le protocole **UDP** — pas de three-way handshake.

```
Attaquant                           Cible
    │── UDP packet ────────────────►│
    │                               │  Port OUVERT → pas de réponse
    │── UDP packet ────────────────►│
    │◄── ICMP Port Unreachable ─────│  Port FERMÉ
```

### Logique des réponses

| Réponse | État |
|---------|------|
| Réponse UDP | Port **ouvert** |
| ICMP Port Unreachable | Port **fermé** |
| Autres erreurs ICMP | Port **filtré** |
| Pas de réponse | Port **ouvert ou filtré** |

### ✅ / ❌ UDP Scan

| ✅ Avantages | ❌ Inconvénients |
|------------|----------------|
| Moins de surcharge qu'un TCP | Très **lent** (rate limiting ICMP) |
| Efficace sur Windows (pas de rate limiting ICMP) | Information de port seulement |
| Détecte services UDP vulnérables | Nécessite **privilèges root** |

**Compléments utiles :**
```bash
nmap -sU -sV [target]  # + version detection
nmap -sU -O [target]   # + OS fingerprinting
```

---

## 8. SCTP Scans

**SCTP** (Stream Control Transport Protocol) — alternative à TCP et UDP, utilisé pour VoIP, IP telephony, SS7/SIGTRAN.

### 8.1 SCTP INIT Scan
**Option Zenmap :** `-sY`

```
Attaquant ──── INIT chunk ────────────►  Cible
          ◄─── INIT+ACK chunk ──────────  (Port ouvert)
          ◄─── ABORT chunk ─────────────  (Port fermé)
          [Pas de réponse / ICMP]         (Port filtré)
```

**Avantage :** Différencie clairement les états open / closed / filtered. Similaire au SYN scan : **furtif** (connexion half-open).

### 8.2 SCTP COOKIE ECHO Scan
**Option Zenmap :** `-sZ`

```
Attaquant ──── COOKIE ECHO chunk ────►  Cible
          [Silence]                       (Port ouvert → paquet silencieusement abandonné)
          ◄─── ABORT chunk ─────────────  (Port fermé)
```

**Avantage :** Non bloqué par les firewalls non-stateful (contrairement à INIT).  
**Inconvénient :** Ne différencie pas open et filtered → affiche `open|filtered`.

---

## 9. SSDP et List Scan

### SSDP Scan
**SSDP** (Simple Service Discovery Protocol) — gère la communication pour la fonctionnalité **UPnP** (Universal Plug and Play).

- Communique via adresses multicast IPv4/IPv6
- Fonctionne même à travers certains firewalls
- Les attaquants l'utilisent pour détecter les **vulnérabilités UPnP** (buffer overflow, DoS)

### List Scan
**Option Zenmap :** `-sL`

Génère et affiche simplement une liste d'IPs/noms **sans pinger ni scanner** les hôtes.

```bash
nmap -sL 10.10.1.0/24  # Liste toutes les IPs sans scanner
```

**Résultat :** Toutes les IPs affichées comme "not scanned" (0 hosts up).

**Avantage :** Vérification de cohérence (sanity check) et correction d'erreurs d'adressage.

---

## 10. IPv6 Scan

**Option Zenmap :** `-6`

L'espace d'adressage IPv6 (128 bits vs 32 bits en IPv4) rend le scanning **beaucoup plus complexe**.

```
Plage IPv6 subnet : 2^64 adresses = ~18 milliards de milliards
À 1 probe/seconde → ~5 milliards d'années pour scanner un subnet !
```

**Stratégie des attaquants :**
1. Récupérer les adresses IPv6 depuis le trafic réseau, les logs, les emails archivés
2. Si un hôte du subnet est compromis → sonder l'adresse multicast "all-hosts"

```bash
nmap -6 <target IPv6>  # Scan IPv6
```

---

## 11. Tableau de référence — Toutes les techniques

| Technique | Option | Protocole | Logs ? | Privilèges root ? | OS ciblé |
|-----------|--------|-----------|--------|-------------------|----------|
| TCP Full-Open | `-sT` | TCP | ✅ Oui | ❌ Non | Tous |
| TCP SYN (Stealth) | `-sS` | TCP | ❌ Non | ✅ Oui | Tous |
| FIN Scan | `-sF` | TCP | ❌ Non | ✅ Oui | UNIX/BSD |
| NULL Scan | `-sN` | TCP | ❌ Non | ✅ Oui | UNIX/BSD |
| Xmas Scan | `-sX` | TCP | ❌ Non | ✅ Oui | UNIX/BSD |
| Maimon Scan | `-sM` | TCP | ❌ Non | ✅ Oui | BSD |
| ACK Flag Probe | `-sA` | TCP | ❌ Non | ✅ Oui | BSD (vieux OS) |
| IDLE/IPID | `-sI` | TCP | ❌ Non | ✅ Oui | Tous |
| UDP Scan | `-sU` | UDP | ❌ Non | ✅ Oui | Tous |
| SCTP INIT | `-sY` | SCTP | ❌ Non | ✅ Oui | Tous |
| SCTP COOKIE | `-sZ` | SCTP | ❌ Non | ✅ Oui | Tous |
| List Scan | `-sL` | — | ❌ Non | ❌ Non | — |
| IPv6 Scan | `-6` | IPv6 | Variable | Variable | Tous |

---

[← Découverte d'Hôtes](./03_decouverte_hotes.md) | [→ OS Discovery & Banner Grabbing](./05_os_discovery_banner_grabbing.md)
