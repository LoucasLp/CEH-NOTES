# 🟡 Module 10 — Techniques Avancées : Botnets, Amplification & Réflexion

> **CEH v13 | EC-Council**  
> `#Botnet` `#C2` `#Amplification` `#Réflexion` `#Scanning`

---

## 📌 Table des matières

1. [Les Botnets](#1-les-botnets)
2. [Techniques de propagation des Bots](#2-techniques-de-propagation)
3. [Attaques par Amplification et Réflexion](#3-amplification-et-réflexion)
4. [Attaques Multi-Vecteurs](#4-attaques-multi-vecteurs)
5. [Techniques de Scanning des Botnets](#5-techniques-de-scanning)
6. [Zero-Day DoS](#6-zero-day-dos)

---

## 1. Les Botnets

### Définition

Un **botnet** est un réseau de machines compromises (**bots** ou **zombies**) contrôlées à distance par un attaquant via une infrastructure de **Command & Control (C&C)**.

### Architecture d'un Botnet

```
┌─────────────────────────────────────────────────┐
│                   BOTMASTER                     │
│              (Attaquant anonyme)                │
└──────────────────────┬──────────────────────────┘
                       │  IRC / HTTP / P2P / HTTPS
                       ▼
┌─────────────────────────────────────────────────┐
│           SERVEUR C&C (Command & Control)        │
│   IRC Server / HTTP Panel / FastFlux Domain     │
└──────┬──────────────┬──────────────┬────────────┘
       │              │              │
       ▼              ▼              ▼
  [Handler 1]    [Handler 2]    [Handler 3]
       │              │              │
   ┌───┴──┐       ┌───┴──┐      ┌───┴──┐
  [Z][Z][Z]      [Z][Z][Z]     [Z][Z][Z]
   Zombies        Zombies       Zombies
```

### Canaux de communication C&C

| Canal | Description | Exemple |
|-------|-------------|---------|
| **IRC** | Canal textuel temps réel | Bot rejoint un salon IRC secret |
| **HTTP/HTTPS** | Requêtes web normales | Bot interroge un site de commande |
| **P2P** | Peer-to-peer (décentralisé) | Plus résistant aux takedowns |
| **Domain Generation Algorithm (DGA)** | Génère dynamiquement des domaines | Difficile à bloquer |
| **Fast Flux DNS** | IPs changent rapidement | Évite le blocage IP |

---

### Activités malveillantes des Botnets

```
Un botnet peut être utilisé pour :

  DDoS Attacks ──────── Submerger des cibles avec du trafic
  Spamming ───────────── Envoi massif d'emails indésirables
  Phishing ───────────── Sites frauduleux hébergés sur les bots
  Sniffing ───────────── Capture de données sensibles locales
  Keylogging ─────────── Vol de credentials
  Cryptomining ───────── Minage de cryptomonnaie
  Click Fraud ────────── Génération de faux clics publicitaires
  Malware Distribution ── Propagation de nouveaux malwares
  Proxy Services ──────── Anonymisation du trafic attaquant
```

---

## 2. Techniques de propagation

Les bots se propagent en scannant et en compromettant des machines vulnérables :

### Modes de Scanning

| Technique | Description |
|-----------|-------------|
| **Random Scanning** | Sonde des adresses IP aléatoires dans la plage cible |
| **Hit-list Scanning** | Utilise une liste préétablie d'hôtes vulnérables |
| **Topological Scanning** | Exploite les infos de la machine compromise pour trouver ses voisins |
| **Local Subnet Scanning** | Scanne le réseau local de la machine infectée |
| **Permutation Scanning** | Utilise une permutation pseudo-aléatoire pour couvrir l'espace IP sans doublons |

```
Machine infectée
     │
     ├── Scan Random IP → Hôte vulnérable trouvé
     │                         │
     │                    Exploitation
     │                         │
     │                    Infection
     │                         │
     └──────────────────── Nouveau bot rejoint le C&C
```

---

## 3. Amplification et Réflexion

Ces techniques permettent d'**amplifier massivement** le volume d'attaque tout en **masquant l'origine** réelle.

### Principe général

```
Attaquant (IP Spoofée = IP Victime)
     │
     │  Petite requête → Serveur Réflecteur
     │                        │
     │              Grande réponse → Victime
     │
Résultat : La victime reçoit un volume amplifié sans que l'attaquant soit exposé
```

---

### 🔸 DNS Amplification

```
Attaquant → ANY query (60 bytes, IP source = victime) → Serveur DNS Open Resolver
Serveur   → Réponse DNS complète (3 000 bytes)        → Victime

Facteur d'amplification : jusqu'à 50x
```

```
Requête   : dig ANY @open-resolver.com target.com  ←  ~60 octets
Réponse   : Enregistrements DNS complets           → ~3000 octets
Amplification x50 environ
```

---

### 🔸 NTP Amplification (monlist)

```
Attaquant → monlist request (petit paquet) → Serveur NTP
Serveur   → Liste des 600 derniers clients → Victime

Facteur d'amplification : jusqu'à 556x
```

> ⚠️ `monlist` est désactivé dans les versions NTP modernes (CVE-2013-5211)

---

### 🔸 Memcached Amplification

```
Attaquant → Requête UDP port 11211 (~15 bytes) → Serveur Memcached
Serveur   → Réponse données en cache (~750 KB) → Victime

Facteur d'amplification : jusqu'à 51 000x
```

> 🔴 C'est cette technique qui a permis l'attaque GitHub 2018 (1,35 Tbps)

---

### 🔸 SSDP Amplification

```
Protocole : Simple Service Discovery Protocol (UPnP)
Port      : UDP 1900
Amplification : ~30x

Attaquant → M-SEARCH request → Dispositif UPnP
Dispositif → Réponse complète → Victime
```

---

### 🔸 CHARGEN Amplification (Spoofed Attack)

```
CHARGEN = Character Generator Protocol (port 19/UDP)
Génère des caractères en boucle

Attaquant → Requête CHARGEN spoofée → Serveur CHARGEN
Serveur   → Flux continu de caractères → Victime

Défense : Désactiver le service CHARGEN
```

---

### Tableau des facteurs d'amplification

| Protocole | Port | Facteur d'amplification |
|-----------|------|------------------------|
| **Memcached** | UDP 11211 | ~51 000x 🔴 |
| **NTP (monlist)** | UDP 123 | ~556x |
| **DNS (ANY)** | UDP 53 | ~28-54x |
| **SSDP** | UDP 1900 | ~30x |
| **CHARGEN** | UDP 19 | ~358x |
| **SNMP** | UDP 161 | ~650x |

---

## 4. Attaques Multi-Vecteurs

Les attaques modernes combinent **plusieurs techniques simultanément** :

```
Phase 1 : Attaque volumétrique (saturer les liens)
     +
Phase 2 : Attaque protocole (épuiser les équipements réseau)
     +
Phase 3 : Attaque applicative (cibler les serveurs backend)
     =
Attaque multi-couches → Très difficile à mitiger
```

> **Exemple** : Mirai Botnet (2016) — Combinait UDP Flood + TCP SYN Flood + HTTP Flood

---

## 5. Techniques de Scanning des Botnets

### Random Scanning

```
Bot infecté → Génère des IP aléatoires dans 10.x.x.x
            → Tente d'exploiter port 22 (SSH) ou 23 (Telnet)
            → Si vulnérable → infect et recrutement
```

### Hit-list Scanning

```
Pre-computed list d'IPs vulnérables
            → Distribution à chaque bot
            → Chaque bot scanne sa portion
            → Propagation très rapide
```

> 📈 Permet d'infecter des milliers d'hôtes en quelques minutes

---

## 6. Zero-Day DoS

```
Définition : Attaque exploitant une vulnérabilité inconnue
             AVANT qu'un patch soit disponible

Cycle :
  1. Chercheur/Attaquant découvre la vulnérabilité
  2. Développement de l'exploit
  3. Attaque lancée
  4. Vendeur prend connaissance
  5. Développement du patch (jours/semaines)
  6. Déploiement du patch

→ Pendant tout ce laps de temps, aucune défense n'est possible
```

---

## 🧠 Points clés à retenir pour l'examen

> 📝 **À mémoriser absolument :**

- **C&C** = Command & Control — Infrastructure de contrôle du botnet
- **Memcached** = amplification la plus massive (~51 000x)
- **NTP monlist** = CVE-2013-5211 — amplification ~556x
- **CHARGEN** : désactiver ce service pour stopper les attaques par réflexion
- **DNS Amplification** : utilise les requêtes `ANY` vers des **open resolvers**
- Les botnets **P2P** sont plus résistants aux takedowns que les C&C centralisés
- **Zero-day DoS** = attaque avant qu'un patch soit disponible

---

*⬅️ [Catégories d'attaques](./02_categories_attaques.md) | ➡️ [Outils & Labs](./04_outils_et_labs.md)*
