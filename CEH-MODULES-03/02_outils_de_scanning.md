# 🔧 02 — Outils de Scanning

> **Module 3 — CEH v13** | [← Retour au sommaire](./README.md)

---

## 1. Nmap (Network Mapper)

> 🌐 Source : [https://nmap.org](https://nmap.org)

Nmap est le **scanner de sécurité réseau** de référence. Il permet de découvrir hôtes, ports et services en créant une véritable "carte" du réseau.

```bash
# Syntaxe générale
nmap <options> <Target IP address>
```

### Ce que Nmap permet de détecter

| Élément | Description |
|---------|-------------|
| Hôtes actifs | Live hosts sur le réseau |
| Ports ouverts | TCP et UDP |
| Services | Nom et version de l'application |
| OS | Type et version du système d'exploitation |
| Filtres/Firewalls | Type de packet filters en place |
| Détails MAC | Adresses MAC des interfaces |

### Usages légitimes vs malveillants

| Administrateur réseau | Attaquant |
|----------------------|-----------|
| Inventaire réseau | Extraction d'infos sur les hôtes |
| Gestion des mises à jour | Découverte de ports ouverts |
| Monitoring uptime | Identification des vulnérabilités |

---

## 2. Hping3

> 🌐 Source : [https://salsa.debian.org](https://salsa.debian.org)

Outil **en ligne de commande** pour le scanning réseau et le crafting de paquets TCP/IP. Envoie des requêtes ICMP et supporte TCP, UDP, ICMP et raw-IP.

```bash
# Syntaxe générale
hping3 <options> <Target IP address>
```

### Fonctionnalités clés
- Audit de sécurité réseau
- Tests de firewall
- Traceroute avancé
- Fingerprinting OS distant
- Estimation de l'uptime distant
- IP spoofing & scanning anonyme
- Scanning d'hôtes idle
- Détection de ports ouverts derrière des firewalls

---

### 📋 Commandes Hping3 — Référence complète

#### ICMP Ping
```bash
hping3 -1 10.0.0.25
```
> Envoie une requête ICMP echo (ping) à la cible. Détermine si un hôte est actif.

---

#### ACK Scan sur port 80
```bash
hping3 -A 10.0.0.25 -p 80
```
> Sonde l'existence d'un firewall et ses règles.
> - Firewall simple → laisse passer les paquets ACK
> - Firewall stateful → bloque la connexion
> - Si hôte vivant + port ouvert → retourne **RST**

---

#### UDP Scan sur port 80
```bash
hping3 -2 10.0.0.25 -p 80
```
> Utilise le mode UDP (`-2` ou `--udp`).
> - Port **fermé** → message ICMP port unreachable
> - Port **ouvert** → pas de réponse

---

#### Collecte de numéros de séquence TCP
```bash
hping3 192.168.1.103 -Q -p 139
```
> Collecte tous les numéros de séquence TCP générés par l'hôte cible.

---

#### Firewalls et Timestamps
```bash
hping3 -S 72.14.207.99 -p 80 --tcp-timestamp
```
> Beaucoup de firewalls bloquent les paquets TCP sans l'option Timestamp.
> `--tcp-timestamp` active cette option et permet d'estimer la fréquence de mise à jour et l'uptime de la cible.

---

#### SYN Scan sur plage de ports 50-60
```bash
hping3 -8 50-60 -S 10.0.0.25 -V
```
> `-8` ou `--scan` → mode scan sur une plage de ports.
> `-S` → SYN scan sur les ports 50 à 60.

---

#### FIN, PUSH et URG Scan sur port 80
```bash
hping3 -F -P -U 10.0.0.25 -p 80
```
> - Port **ouvert** → aucune réponse
> - Port **fermé** → réponse RST

---

#### Scan de tout un sous-réseau (hôtes actifs)
```bash
hping3 -1 10.0.1.x --rand-dest -I eth0
```
> ICMP ping sur tout le subnet `10.0.1.0` à `10.0.1.255` via l'interface `eth0`.
> Hôtes avec port ouvert → répondent avec ICMP reply.

---

#### Intercepter le trafic HTTP
```bash
hping3 -9 HTTP -I eth0
```
> Mode écoute (`-9`). Intercepte tous les paquets contenant la signature HTTP sur eth0.

---

#### SYN Flooding (DoS)
```bash
hping3 -S 192.168.1.1 -a 192.168.1.254 -p 22 --flood
```
> ⚠️ **Technique DoS** — Utilise des adresses IP spoofées pour inonder la cible de paquets SYN.

---

## 3. Metasploit

> 🌐 Source : [https://www.metasploit.com](https://www.metasploit.com)

Framework open-source pour les tests d'intrusion. Fournit infrastructure, contenu et outils pour réaliser des pentests complets.

### Fonctionnalités principales

```
┌─────────────────────────────────────────┐
│            METASPLOIT                   │
├─────────────────────────────────────────┤
│  • Scanner ports ouverts et services    │
│  • Exploiter des vulnérabilités         │
│  • Pivoter dans le réseau               │
│  • Collecter des preuves                │
│  • Générer des rapports                 │
│  • Développement de signatures IDS      │
└─────────────────────────────────────────┘
```

> 💡 **Approche modulaire :** Permet de combiner n'importe quel exploit avec n'importe quel payload.

---

## 4. NetScanTools Pro

> 🌐 Source : [https://www.netscantools.com](https://www.netscantools.com)

Outil d'investigation réseau pour troubleshooting, monitoring, découverte et détection de périphériques.

### Capacités
- Collecte infos sur LAN local et utilisateurs Internet
- Listage automatique/manuel : adresses IPv4/IPv6, noms d'hôtes, domaines, emails, URLs
- Combinaison d'outils réseau catégorisés : **actif, passif, DNS, ordinateur local**
- Identification des vulnérabilités et ports exposés

---

## 5. Outils supplémentaires

| Outil | URL | Usage |
|-------|-----|-------|
| **sx** | github.com | Scanner réseau rapide |
| **RustScan** | github.com | Scanner ultra-rapide (pré-Nmap) |
| **MegaPing** | magnetosoft.com | Suite de diagnostics réseau |
| **SolarWinds Engineer's Toolset** | solarwinds.com | Suite complète d'outils réseau |
| **PRTG Network Monitor** | paessler.com | Monitoring réseau en temps réel |

---

## 6. Ports et Services courants — Référence

| Service | Port/Protocole | Description |
|---------|---------------|-------------|
| `ftp-data` | 20/tcp | Transfert de données FTP |
| `ftp` | 21/tcp | Commandes FTP |
| `ssh` | 22/tcp | Secure Shell |
| `telnet` | 23/tcp | Telnet |
| `smtp` | 25/tcp | Serveur email |
| `domain` | 53/tcp,udp | Serveur DNS |
| `www-http` | 80/tcp,udp | Web HTTP |
| `www-https` | 443/tcp | Web HTTPS |
| `kerberos` | 88/tcp,udp | Kerberos |
| `imap` | 143/tcp,udp | Internet Message Access Protocol |
| `snmp` | 161/tcp,udp | SNMP |
| `irc` | 194/tcp,udp | Internet Relay Chat |
| `ms-sql-s` | 1433/tcp,udp | Microsoft SQL Server |
| `nfs` | 2049/tcp,udp | Network File System |
| `sip` | 5060/tcp,udp | Session Initiation Protocol |
| `x11` | 6000-6063 | X Window System |

---

[← Types de Scanning](./01_types_de_scanning.md) | [→ Découverte d'Hôtes](./03_decouverte_hotes.md)
