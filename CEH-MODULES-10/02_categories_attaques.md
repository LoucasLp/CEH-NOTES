# 🟠 Module 10 — Catégories d'Attaques DoS / DDoS

> **CEH v13 | EC-Council**  
> `#Volumétrique` `#Protocole` `#ApplicationLayer` `#TCP` `#UDP` `#HTTP`

---

## 📌 Table des matières

1. [Vue d'ensemble des 3 catégories](#1-vue-densemble)
2. [Attaques Volumétriques](#2-attaques-volumétriques)
3. [Attaques Protocole](#3-attaques-protocole)
4. [Attaques Couche Applicative](#4-attaques-couche-applicative)
5. [Tableau récapitulatif](#5-tableau-récapitulatif)

---

## 1. Vue d'ensemble

```
┌────────────────────────────────────────────────────────┐
│               CATÉGORIES D'ATTAQUES DoS/DDoS           │
├────────────────┬───────────────────┬───────────────────┤
│  VOLUMÉTRIQUE  │    PROTOCOLE      │   APPLICATIVE     │
│  (Layer 3/4)   │   (Layer 3/4)     │   (Layer 7)       │
├────────────────┼───────────────────┼───────────────────┤
│ Mesure : bps   │ Mesure : pps      │ Mesure : rps      │
│ (bits/s)       │ (paquets/s)       │ (requêtes/s)      │
├────────────────┼───────────────────┼───────────────────┤
│ Cible :        │ Cible :           │ Cible :           │
│ Bande passante │ Équip. réseau     │ Application/Serv. │
│ (routeurs,     │ (firewalls,       │ (serveurs web,    │
│  serveurs)     │  load balancers)  │  bases de données)│
└────────────────┴───────────────────┴───────────────────┘
```

---

## 2. Attaques Volumétriques

L'objectif est de **saturer la bande passante** du réseau cible.  
Unité de mesure : **bps (bits per second)**

---

### 🔸 UDP Flood Attack

```
Attaquant → Envoie des milliers de paquets UDP → Port aléatoire cible
                                                          │
                                            Cible vérifie le port
                                                          │
                                    Port libre → Envoie ICMP "Unreachable"
                                                          │
                                        CPU/Bande passante épuisés
```

- La cible tente de répondre à chaque paquet UDP
- Aucune connexion préalable requise (connectionless)
- **Défense** : Firewall filtrant les paquets UDP non sollicités

---

### 🔸 ICMP Flood Attack (Ping Flood)

```
Attaquant → Ping massif (ICMP Echo Request) → Cible
Cible     → Répond avec ICMP Echo Reply → Double consommation bande passante
```

- Exploite le protocole ICMP (couche 3)
- Consomme la bande passante dans les **deux sens**
- **Défense** : Limiter (rate-limiting) les réponses ICMP

---

### 🔸 Ping of Death

```
Paquet ICMP normal  : ≤ 65 535 octets
Ping of Death       : > 65 535 octets (fragmentation malveillante)
Résultat            : Buffer overflow → Crash / Reboot du système
```

> ⚠️ Vulnérabilité historique (patché dans la plupart des OS modernes)

---

### 🔸 Smurf Attack

```
Attaquant (IP Spoofée = IP Victime)
     │
     │  ICMP Broadcast Request
     ▼
Réseau broadcast (ex: 192.168.1.255)
     │
     │  Tous les hôtes répondent à l'IP "spoofée"
     ▼
Victime ← Flood de réponses ICMP (amplification x N hôtes)
```

- **Amplification** : 1 paquet envoyé = N réponses reçues par la victime
- **Défense** : Désactiver les réponses aux broadcasts

---

### 🔸 Pulse Wave Attack

- Attaques répétées par **vagues courtes et intenses**
- Chaque vague dure ~10 secondes, pause, puis recommence
- Difficile à mitiger avec les systèmes réactifs classiques (ils détectent trop tard)

---

## 3. Attaques Protocole

L'objectif est d'**épuiser les ressources** des équipements réseau (tables de connexions, CPU).  
Unité de mesure : **pps (packets per second)**

---

### 🔸 SYN Flood Attack

Exploitation du **3-way handshake TCP** :

```
Handshake normal :
Client → SYN      → Serveur
Client ← SYN-ACK  ← Serveur
Client → ACK       → Serveur  ✓ Connexion établie

SYN Flood :
Attaquant → SYN (IP spoofée) → Serveur
            ← SYN-ACK         ← Serveur  (attend l'ACK)
            ← SYN-ACK         ← Serveur  (retransmission)
            ← SYN-ACK         ← Serveur  (timeout ~75 sec)

→ La "Listen Queue" du serveur est saturée
→ Les connexions légitimes sont refusées
```

- **Délai de timeout** : 75 secondes par connexion semi-ouverte
- **Défense** : SYN Cookies, Rate Limiting SYN

---

### 🔸 ACK Flood Attack

```
Attaquant → Packets ACK massifs (sans SYN préalable) → Cible
Cible     → Vérifie chaque ACK dans sa table de connexions
          → Saturation du CPU et de la table d'états
```

---

### 🔸 Fragmentation Attack

```
Attaquant → Fragments de paquets TCP/UDP → Cible
Cible     → Tente de réassembler les fragments
          → Fragments invalides/incomplets = ressources gaspillées
          → Buffer overflow possible
```

Types :
- **Teardrop Attack** : Fragments qui se chevauchent → crash à la réassemblage
- **Tiny Fragment Attack** : Fragments si petits que les headers sont fragmentés

---

### 🔸 Spoofed Session Flood

```
Attaquant → Fausses sessions TCP complètes (SYN, SYN-ACK, ACK spoofés)
          → Épuisement de la table de session du serveur
          → Les vraies connexions sont rejetées
```

---

### 🔸 TCP SACK Panic

- Exploite une vulnérabilité dans **TCP Selective Acknowledgment (SACK)**
- CVE-2019-11477 (Linux kernel)
- Peut provoquer un **kernel panic** sur les systèmes Linux non patchés

---

## 4. Attaques Couche Applicative

L'objectif est d'**épuiser les ressources applicatives** (threads, connexions, mémoire).  
Unité de mesure : **rps (requests per second)**

---

### 🔸 HTTP GET/POST Flood

```
Requête GET légitime  : 1 requête = petite réponse
Requête GET malveillante : Des milliers de requêtes/seconde

Requête POST flood : Envoie de grandes quantités de données
                     → Épuise CPU et I/O du serveur web
```

---

### 🔸 Slowloris Attack

Technique **"low and slow"** — très furtive :

```
Attaquant → Ouvre des centaines de connexions HTTP
          → Envoie des headers HTTP partiels (incomplets)
          → Maintient chaque connexion ouverte en envoyant 1 header toutes les ~X secondes
          → Le serveur attend la fin des headers
          → Toutes les connexions disponibles sont occupées
          → Les utilisateurs légitimes ne peuvent plus se connecter

Serveur Apache : thread par connexion → très vulnérable
Serveur Nginx  : architecture asynchrone → plus résistant
```

> 💡 **Exemple de header partiel Slowloris :**
> ```
> GET / HTTP/1.1\r\n
> Host: target.com\r\n
> X-Custom-Header: \r\n   ← Header envoyé lentement, jamais terminé
> ```

---

### 🔸 UDP Application Layer Flood

- Cible les services UDP applicatifs (DNS, SNMP, NTP)
- Envoie des requêtes légitimes en très grand volume
- Difficile à filtrer car le trafic ressemble à du trafic normal

---

### 🔸 DDoS Extortion (Ransom DDoS — RDoS)

```
Phase 1 : L'attaquant envoie un email de menace
Phase 2 : Démontre sa capacité avec une courte attaque test
Phase 3 : Exige une rançon (souvent en crypto)
Phase 4 : Lance l'attaque complète si non-paiement
```

---

## 5. Tableau récapitulatif

| Catégorie | Exemples | Unité | Cible | Difficulté de défense |
|-----------|----------|-------|-------|----------------------|
| **Volumétrique** | UDP Flood, ICMP Flood, Smurf, Ping of Death | bps | Bande passante | Moyen (filtrage IP) |
| **Protocole** | SYN Flood, ACK Flood, Teardrop, SACK Panic | pps | Équip. réseau | Difficile |
| **Applicative** | Slowloris, HTTP Flood, RDoS | rps | Serveur applicatif | Très difficile |

---

## 🧠 Points clés à retenir pour l'examen

> 📝 **À mémoriser absolument :**

- **SYN Flood** exploite le 3-way handshake TCP — connexions semi-ouvertes pendant **75 secondes**
- **Smurf** = broadcast ICMP avec **IP spoofée** → amplification
- **Slowloris** = attaque "low and slow" maintenant des connexions ouvertes **incomplètes**
- **Ping of Death** = paquet ICMP **> 65 535 octets** → buffer overflow
- **Teardrop** = fragments qui **se chevauchent** → crash à la réassemblage
- Volumétrique = **bps** | Protocole = **pps** | Applicative = **rps**

---

*⬅️ [Concepts Fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques Avancées](./03_techniques_avancees.md)*
