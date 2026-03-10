# 🖥️ 05 — OS Discovery & Banner Grabbing

> **Module 3 — CEH v13** | [← Retour au sommaire](./README.md)

---

## 1. Pourquoi identifier l'OS cible ?

> *"Un attaquant aura une probabilité de succès bien plus élevée s'il connaît l'OS de la cible."*

L'identification de l'OS permet à l'attaquant de :
- Cibler des **vulnérabilités spécifiques** à un OS
- Adapter sa **stratégie d'attaque**
- Choisir les **exploits** les plus appropriés

---

## 2. Paramètres clés pour l'identification d'OS

Deux paramètres principaux dans l'en-tête IP permettent de déduire l'OS :

| Paramètre | Description |
|-----------|-------------|
| **TTL** (Time To Live) | Durée de vie maximale d'un paquet dans le réseau |
| **TCP Window Size** | Longueur du paquet rapporté |

### Table de référence OS ↔ TTL / Window Size

| Système d'exploitation | TTL | TCP Window Size |
|------------------------|-----|----------------|
| **Linux** | 64 | 5840 |
| **FreeBSD** | 64 | 65535 |
| **OpenBSD** | 255 | 16384 |
| **Windows** | 128 | 65535 bytes à 1 Gigabyte |
| **Cisco Routers** | 255 | 4128 |
| **Solaris** | 255 | 8760 |
| **AIX** | 255 | 16384 |

> 💡 **Astuce :** TTL observé sur le réseau ≠ TTL initial → le TTL décrémente à chaque saut (hop). Pour retrouver le TTL initial, additionner le nombre de hops parcourus.

---

## 3. Méthodes de Banner Grabbing

### 3.1 Banner Grabbing Actif

**Principe :** L'OS d'une machine a une façon unique de répondre aux paquets TCP spécialement forgés. Les différences d'implémentation de la pile TCP/IP révèlent l'OS.

#### Les 9 tests Nmap pour l'OS fingerprinting

| Test | Description |
|------|-------------|
| **Test 1** | Paquet TCP avec flags SYN + ECN-Echo → port TCP **ouvert** |
| **Test 2** | Paquet TCP **NULL** (aucun flag) → port TCP **ouvert** |
| **Test 3** | Paquet TCP avec URG + PSH + SYN + FIN → port TCP **ouvert** |
| **Test 4** | Paquet TCP avec flag ACK → port TCP **ouvert** |
| **Test 5** | Paquet TCP avec flag SYN → port TCP **fermé** |
| **Test 6** | Paquet TCP avec flag ACK → port TCP **fermé** |
| **Test 7** | Paquet TCP avec URG + PSH + FIN → port TCP **fermé** |
| **Test 8 (PU)** | Paquet UDP → port UDP **fermé** (objectif : extraire "ICMP port unreachable") |
| **Test 9 (TSeq)** | 6 paquets TCP SYN → port TCP **ouvert** (analyse patterns ISN, IPID, timestamps) |

#### Catégories de patterns ISN (Initial Sequence Number)

| Catégorie | OS concernés |
|-----------|-------------|
| Traditional 64K | Anciens UNIX |
| Random increments | Solaris, IRIX, FreeBSD, Digital UNIX |
| True random | Linux 2.0.*, OpenVMS, newer AIX |
| Time-dependent | Windows (ISN incrémenté d'une valeur fixe à chaque occurrence) |

---

### 3.2 Banner Grabbing Passif

Capture de paquets **sans scanner** l'hôte cible → **plus furtif** que le banner grabbing actif.

#### Méthodes de banner grabbing passif

| Méthode | Description |
|---------|-------------|
| **Banners d'erreur** | Les messages d'erreur révèlent le type de serveur, OS, outils SSL |
| **Sniffing réseau** | Capturer et analyser des paquets pour déduire l'OS |
| **Extensions de pages** | `.aspx` → IIS/Windows ; `.php` → Linux/Apache |

#### Les 4 critères d'analyse passive

| Critère | Question |
|---------|----------|
| **TTL** | Quelle valeur TTL l'OS définit-il pour les paquets sortants ? |
| **Window Size** | Quelle taille de fenêtre l'OS définit-il ? |
| **DF bit** | L'OS active-t-il le bit "Don't Fragment" ? |
| **TOS** | L'OS configure-t-il le Type of Service ? |

#### Exemple d'analyse d'un paquet sniffé

```
2024-03-15 11:5.330465  10.10.1.11 → 10.10.1.22
  Time To Live   : 45
  Protocol       : ICMP (1)
  Fragment Offset: 0
  DSField        : 0x00
  Ack            : 0xE3C65D7
  Win            : 0x7D78
```

**Analyse :**
```
TTL observé     = 45
Nombre de hops  = 19  (traceroute)
TTL initial     = 45 + 19 = 64  → Linux ou FreeBSD

Window Size = 0x7D78 = 32120 en décimal → taille par défaut Linux
DF bit      = activé (courant sur la plupart des systèmes)
TOS         = 0x0

→ Conclusion : Linux kernel 2.2.x
```

> ⚠️ **Limitations du fingerprinting passif :**
> - Les applications qui buildent leurs propres paquets (Nmap, Hunt, Nemesis...) n'utilisent pas les mêmes signatures que l'OS
> - Un hôte distant peut facilement modifier TTL, Window Size, DF ou TOS

---

## 4. Outils de découverte d'OS

### 4.1 Nmap (OS Discovery)
**Option Zenmap :** `-O`

```bash
nmap -O 10.10.1.11
```

**Exemple de résultat :**
```
Running: Microsoft Windows 10
OS CPE: cpe:/o:microsoft:windows_10:1703
OS details: Microsoft Windows 10 1703
Network Distance: 1 hop
```

---

### 4.2 Wireshark (Passive)
> 🌐 Source : [https://www.wireshark.org](https://www.wireshark.org)

Capturer les réponses générées par la machine cible et **observer TTL + TCP Window Size** dans le premier paquet TCP capturé.

```
TTL = 128 → Possible OS : Windows
TTL = 64  → Possible OS : Linux / FreeBSD
TTL = 255 → Possible OS : Cisco / Solaris / OpenBSD
```

---

### 4.3 Unicornscan
> 🌐 Source : [https://sourceforge.net](https://sourceforge.net)

```bash
unicornscan <target IP address>
```

Identifier l'OS en observant la **valeur TTL** dans le résultat du scan.

```
ttl = 128 → OS probablement Microsoft Windows
ttl = 64  → OS probablement Linux
```

---

### 4.4 Nmap Script Engine (NSE)
**Option Zenmap :** `-sC` (scripts par défaut) ou `--script`

```bash
# Script SMB pour la découverte d'OS
nmap --script smb-os-discovery.nse 10.10.1.22
```

**Exemple de résultat NSE :**
```
Host script results:
| smb-os-discovery:
|   OS: Windows Server 2022 Standard 20348
|   Computer name: Server2022
```

> Scripts NSE exécutés en parallèle avec la même efficacité et vitesse que Nmap.

---

### 4.5 IPv6 Fingerprinting
**Option Zenmap :** `-6 -O`

```bash
nmap -6 -O <target IPv6>
```

**Différence avec IPv4 :** Utilise des probes IPv6-spécifiques supplémentaires + un moteur de détection OS dédié.

**Ordre des ~18 probes Nmap IPv6 :**
```
S1-S6      → Sequence generation
IE1, IE2   → ICMPv6 echo
NI         → Node Information Query
NS         → Neighbor Solicitation
U1         → UDP
TECN       → TCP explicit congestion notification
T2-T7      → TCP probes
```

---

## 5. Réduction du temps de scan Nmap

| Technique | Description |
|-----------|-------------|
| **Omettre les tests non-critiques** | Éviter `-sC`, `-sV`, `-O`, `--traceroute`, `-A` si non nécessaires |
| **Limiter les ports** | Scanner uniquement les ports pertinents |
| **Optimiser les timing** | Option `-T` (0-5) pour ajuster l'agressivité |
| **Séparer UDP** | Scanner UDP séparément (performances différentes) |
| **Mettre à jour Nmap** | La dernière version inclut des améliorations algorithmiques |
| **Instances concurrentes** | Diviser le réseau en groupes et lancer plusieurs scans en parallèle |
| **Localisation réseau** | Scanner depuis le réseau local → meilleure performance |
| **Augmenter bande passante/CPU** | Mode verbose `-v` pour tester l'utilisation bande passante |

### Niveaux de timing Nmap

| Option | Niveau | Nom | Usage |
|--------|--------|-----|-------|
| `-T0` | 0 | Paranoid | IDS evasion - très lent |
| `-T1` | 1 | Sneaky | IDS evasion - lent |
| `-T2` | 2 | Polite | Moins de ressources |
| `-T3` | 3 | Normal | Par défaut |
| `-T4` | 4 | Aggressive | Réseau rapide |
| `-T5` | 5 | Insane | Réseau très rapide |

---

## 6. Résumé — Choisir la bonne méthode

```
┌─────────────────────────────────────────────────────────┐
│  OBJECTIF         │  MÉTHODE           │  OPTION NMAP   │
├───────────────────┼────────────────────┼────────────────┤
│  OS Detection     │  Nmap actif        │  -O            │
│  OS (réseau)      │  Wireshark passif  │  (sniffing)    │
│  OS (TTL)         │  Unicornscan       │  —             │
│  OS (SMB)         │  NSE script        │  --script      │
│  OS (IPv6)        │  IPv6 fingerprint  │  -6 -O         │
└─────────────────────────────────────────────────────────┘
```

---

[← Techniques de Port Scanning](./04_techniques_port_scanning.md) | [→ Évasion IDS & Firewall](./06_evasion_ids_firewall.md)
