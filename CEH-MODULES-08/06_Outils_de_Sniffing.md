# 🛠️ 06 — Outils de Sniffing

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 6/8 — Wireshark, tcpdump, Ettercap, Bettercap, NetworkMiner

---

## 🦈 WIRESHARK — L'outil de référence

> **Wireshark** est l'analyseur de protocoles réseau le plus utilisé au monde. Interface graphique, multi-plateforme, open source.

- Anciennement nommé **Ethereal** (renommé en 2006 pour problème de marque)
- Capture en temps réel ou analyse des fichiers `.pcap`
- Decode des centaines de protocoles
- **Fonctionnalité TCP Stream** : reconstitue les sessions complètes

---

### Interface Wireshark

```
┌──────────────────────────────────────────────────────────────┐
│  [Barre de menus]  File Edit View Capture Analyze Statistics │
├──────────────────────────────────────────────────────────────┤
│  [Barre de filtres]  http.request.method == "POST"    [Apply]│
├──────────────────────────────────────────────────────────────┤
│  No. │ Time  │ Source      │ Destination │ Protocol │ Info   │
│  1   │ 0.000 │ 192.168.1.10│ 192.168.1.1 │ TCP      │ SYN    │
│  2   │ 0.001 │ 192.168.1.1 │ 192.168.1.10│ TCP      │ SYN-ACK│
│  3   │ 0.002 │ 192.168.1.10│ 192.168.1.1 │ HTTP     │ GET /  │
├──────────────────────────────────────────────────────────────┤
│  [Détail du paquet sélectionné - décomposition par couche]   │
│  ▼ Ethernet II, Src: AA:BB:CC:DD, Dst: 11:22:33:44          │
│  ▼ Internet Protocol Version 4, Src: 192.168.1.10           │
│  ▼ Transmission Control Protocol, Src Port: 54231           │
│  ▼ Hypertext Transfer Protocol                              │
├──────────────────────────────────────────────────────────────┤
│  [Hexdump du paquet]                                         │
└──────────────────────────────────────────────────────────────┘
```

---

### Filtres Wireshark essentiels

#### Filtres de capture (BPF - Berkeley Packet Filter)

```bash
# Capturer uniquement le trafic HTTP
tcp port 80

# Capturer depuis/vers une IP
host 192.168.1.10

# Capturer une plage de ports
portrange 80-443

# Combinaisons
host 192.168.1.10 and port 80
not port 22
tcp or udp
```

#### Filtres d'affichage (Display Filters)

```wireshark
# Protocoles
http
dns
ftp
ssh
arp
icmp
tcp
udp

# Par IP
ip.addr == 192.168.1.10
ip.src == 192.168.1.10
ip.dst == 192.168.1.1

# Par port
tcp.port == 80
tcp.dstport == 443
udp.port == 53

# Méthodes HTTP
http.request.method == "POST"
http.request.method == "GET"

# Contenu HTTP
http contains "password"
http contains "login"

# ARP
arp.opcode == 1     (ARP Request)
arp.opcode == 2     (ARP Reply)

# DNS
dns.qry.name contains "google"

# Filtres combinés
ip.addr == 192.168.1.10 && tcp.port == 80
http.request.method == "POST" && http contains "password"

# Exclure un protocole
!arp
!icmp
not tcp.port == 22

# Flags TCP
tcp.flags.syn == 1
tcp.flags.rst == 1
tcp.flags.syn == 1 && tcp.flags.ack == 0   (SYN seul = ouverture connexion)
```

---

### Fonctionnalités clés Wireshark

```
1. Follow TCP Stream
   Clic droit sur paquet → Follow → TCP Stream
   → Reconstitue la session complète (ex: session HTTP, FTP...)
   → Très utile pour voir credentials, contenu de formulaires

2. Export Objects
   File → Export Objects → HTTP
   → Extrait les fichiers téléchargés (images, docs...)

3. Statistics → Protocol Hierarchy
   → Vue d'ensemble des protocoles utilisés (%)

4. Statistics → Conversations
   → Pairs qui communiquent + volume de données

5. Statistics → IO Graph
   → Volume de trafic dans le temps (détection d'anomalies)

6. Coloring Rules
   → Code couleur par protocole / alerte
```

---

## 📟 TCPDUMP — Le sniffer en ligne de commande

> **tcpdump** est le sniffer CLI de référence sur Linux/Unix. Léger, scriptable, idéal pour les serveurs sans interface graphique.

```bash
# Capture basique sur eth0
tcpdump -i eth0

# Capture avec résolution de noms désactivée (plus rapide)
tcpdump -i eth0 -n

# Capturer N paquets
tcpdump -i eth0 -c 100

# Sauvegarder dans un fichier .pcap
tcpdump -i eth0 -w /tmp/capture.pcap

# Lire un fichier .pcap existant
tcpdump -r /tmp/capture.pcap

# Filtrer par IP
tcpdump -i eth0 host 192.168.1.10

# Filtrer par port
tcpdump -i eth0 port 80
tcpdump -i eth0 port 21 or port 23

# Filtrer par protocole
tcpdump -i eth0 icmp
tcpdump -i eth0 arp

# Afficher le contenu (ASCII)
tcpdump -i eth0 -A port 80

# Afficher en hex et ASCII
tcpdump -i eth0 -X port 80

# Capturer trafic FTP (credentials en clair)
tcpdump -i eth0 -A -n port 21

# Capturer trafic HTTP POST (formulaires)
tcpdump -i eth0 -A -s 0 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
```

---

## 🔱 ETTERCAP

> **Ettercap** est un framework complet de MITM avec sniffing intégré, support de plugins, et mode GUI/texte.

```bash
# Mode texte, ARP MITM, sniffing réseau entier
ettercap -T -q -M arp:remote //// 

# MITM ciblé entre victime et gateway
ettercap -T -q -M arp:remote /192.168.1.10/ /192.168.1.1/

# Avec plugin DNS spoofing
ettercap -T -q -M arp:remote /192.168.1.10/ /192.168.1.1/ -P dns_spoof

# Mode graphique (GTK)
ettercap -G

# Paramètres importants
# -T  : mode texte
# -q  : quiet (moins verbeux)
# -M  : attaque MITM (arp, icmp, dhcp...)
# -P  : plugin
# -i  : interface réseau
```

**Plugins Ettercap utiles :**

| Plugin | Fonction |
|--------|----------|
| `dns_spoof` | Spoofer les réponses DNS |
| `autoadd` | Ajouter automatiquement nouvelles cibles |
| `dos_attack` | DoS sur une cible |
| `find_ip` | Scanner les IPs du réseau |
| `isolate` | Isoler un hôte du réseau |

---

## ⚡ BETTERCAP (version moderne)

> **Bettercap** est le successeur d'Ettercap — plus moderne, modulaire, avec interface web.

```bash
# Lancer bettercap
bettercap -iface eth0

# Commandes dans la console interactive
net.probe on          # Scanner le réseau
net.show              # Lister les hôtes découverts

# ARP Spoofing
set arp.spoof.targets 192.168.1.10
set arp.spoof.fullduplex true    # Bidirectionnel
arp.spoof on

# Sniffing
set net.sniff.verbose true
net.sniff on

# DNS Spoofing
set dns.spoof.domains google.com,facebook.com
set dns.spoof.address 192.168.1.99
dns.spoof on

# Capture des credentials HTTP
set http.proxy.sslstrip true
http.proxy on

# Interface web
set api.rest.username admin
set api.rest.password admin
api.rest on          # → http://127.0.0.1:8083
```

---

## 🔍 NETWORKMINER

> **NetworkMiner** est un outil d'analyse forensique réseau (Windows) qui extrait automatiquement les fichiers, credentials, et métadonnées des captures .pcap.

```
Fonctionnalités NetworkMiner :
├── Extraction automatique des fichiers (HTTP, FTP, SMB)
├── Reconstruction des sessions
├── Extraction d'images
├── Identification des credentials
├── DNS queries et réponses
├── Détection des OS (fingerprinting passif)
└── Support IPv4, IPv6, TCP, UDP, ARP...
```

---

## 📋 Autres outils de sniffing

| Outil | Type | Description |
|-------|------|-------------|
| **Cain & Abel** | Windows | MITM, ARP poison, crack passwords |
| **dsniff** | Linux | Suite : arpspoof, dnsspoof, urlsnarf, mailsnarf |
| **urlsnarf** | Linux | Capture URLs depuis le trafic HTTP |
| **mailsnarf** | Linux | Capture emails (SMTP, POP) |
| **msgsnarf** | Linux | Capture messages instantanés |
| **Sniffpass** | Windows | Capture passwords depuis trafic réseau |
| **NetSniff-ng** | Linux | Sniffer haute performance |
| **Kismet** | Linux | Sniffing Wi-Fi (802.11) + IDS |
| **Responder** | Linux | LLMNR/NBT-NS/MDNS poisoning |
| **PcapXray** | Multiplateforme | Visualisation graphique de captures .pcap |

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **Wireshark** anciennement appelé **Ethereal**
- Filtre Wireshark pour HTTP POST : `http.request.method == "POST"`
- **Follow TCP Stream** = reconstituer une session complète
- **tcpdump** = outil CLI, très utilisé sur serveurs Linux
- **Ettercap** = framework MITM + plugins (dns_spoof...)
- **Bettercap** = successeur d'Ettercap, plus moderne
- **NetworkMiner** = outil forensique Windows pour analyse .pcap
- **dsniff** = suite d'outils (arpspoof, dnsspoof, urlsnarf)

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [05 — DNS & Autres Spoofing](./05_DNS_et_Autres_Spoofing.md) | [07 — Détection & Contre-mesures](./07_Detection_et_Contre-mesures.md) |
