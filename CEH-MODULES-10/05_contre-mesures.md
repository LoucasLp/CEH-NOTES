# 🟢 Module 10 — Contre-mesures & Défense DoS/DDoS

> **CEH v13 | EC-Council**  
> `#Countermeasures` `#Mitigation` `#SYNCookies` `#RateLimit` `#CDN` `#WAF`

---

## 📌 Table des matières

1. [Stratégie globale de défense](#1-stratégie-globale)
2. [Contre-mesures par type d'attaque](#2-contre-mesures-par-type)
3. [Techniques de mitigation réseau](#3-techniques-réseau)
4. [Solutions de protection avancées](#4-solutions-avancées)
5. [Détection des attaques](#5-détection)
6. [Analyse post-attaque (Forensics)](#6-forensics)
7. [Bonnes pratiques & checklist](#7-checklist)

---

## 1. Stratégie globale de défense

Le CEH v13 définit **6 axes** pour contrer les attaques DoS/DDoS :

```
┌─────────────────────────────────────────────────────────┐
│           STRATÉGIE DE DÉFENSE DoS/DDoS — CEH v13       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. PROTÉGER les victimes secondaires                   │
│     (machines pouvant être transformées en bots)        │
│                                                         │
│  2. DÉTECTER ET NEUTRALISER les Handlers C&C            │
│     (infrastructure de contrôle des botnets)            │
│                                                         │
│  3. PRÉVENIR les attaques potentielles                  │
│     (hardening, patch management, filtrage)             │
│                                                         │
│  4. DÉVIER les attaques                                 │
│     (rediriger le trafic malveillant)                   │
│                                                         │
│  5. ATTÉNUER l'impact                                   │
│     (absorber ou filtrer le trafic malveillant)         │
│                                                         │
│  6. FORENSICS post-attaque                              │
│     (analyser, documenter, améliorer)                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 2. Contre-mesures par type d'attaque

### 🔸 SYN Flood

| Contre-mesure | Description |
|--------------|-------------|
| **SYN Cookies** | Le serveur ne crée pas d'entrée en file d'attente jusqu'à réception du ACK final |
| **SYN Cache** | Réduction de la taille des entrées dans la listen queue |
| **Firewall SYN Proxy** | Le firewall termine le 3-way handshake avant de relayer |
| **Rate Limiting SYN** | Limite le nombre de SYN par seconde par IP source |
| **Réduction du timeout** | Réduire le timeout des connexions semi-ouvertes (< 75s) |

```
SYN Cookies — Fonctionnement :

Client → SYN → Serveur
                  │
         Génère un cookie cryptographique
         (hash : IP src, port src, timestamp)
                  │
Client ← SYN-ACK (avec numéro de séquence = cookie)
         │
Client → ACK (echo du cookie)
                  │
         Vérification du cookie → Connexion établie
         
→ Aucune ressource consommée avant la vérification !
```

---

### 🔸 UDP Flood

| Contre-mesure | Description |
|--------------|-------------|
| **Firewall filtering** | Bloquer les paquets UDP entrants non sollicités |
| **Rate limiting** | Limiter le taux de paquets UDP |
| **Blackhole routing** | Rediriger le trafic malveillant vers une interface null |
| **ACL (Access Control Lists)** | Règles de filtrage sur les routeurs |

---

### 🔸 ICMP Flood

| Contre-mesure | Description |
|--------------|-------------|
| **Désactiver ICMP** | Bloquer les ping entrants (attention : impact débogage) |
| **Rate limiting ICMP** | Limiter les réponses ICMP |
| **Filtrage aux frontières** | Bloquer les ICMP sur les interfaces WAN |

---

### 🔸 HTTP Flood / Slowloris

| Contre-mesure | Description |
|--------------|-------------|
| **Nginx** | Architecture asynchrone — résistant à Slowloris par défaut |
| **Timeout agressifs** | Réduire les timeouts HTTP (keepalive, headers) |
| **Limiter les connexions** | Max connexions par IP (`mod_limitipconn`) |
| **WAF (Web Application Firewall)** | Détecter les patterns d'attaque HTTP anormaux |
| **CAPTCHA** | Différencier humains et bots pour les requêtes suspectes |

---

### 🔸 Smurf Attack

| Contre-mesure | Description |
|--------------|-------------|
| **Désactiver les broadcasts dirigés** | `no ip directed-broadcast` sur Cisco IOS |
| **Filtrage d'IP spoofée** | Ingress filtering (RFC 2827) |
| **Rate limiting ICMP** | Limiter les réponses ICMP |

---

### 🔸 Amplification DNS/NTP/Memcached

| Contre-mesure | Description |
|--------------|-------------|
| **Fermer les Open Resolvers DNS** | Configurer les DNS pour refuser les requêtes externes |
| **Désactiver NTP monlist** | `ntpdc -c "disable monitor"` |
| **Firewall Memcached** | Bloquer UDP 11211 depuis Internet |
| **Ingress Filtering (BCP38)** | Empêcher le spoofing d'adresses IP |

```bash
# Désactiver monlist NTP
ntpdc -c "disable monitor" <ntp_server>

# Ou dans /etc/ntp.conf :
disable monitor

# Bloquer Memcached depuis Internet (iptables)
iptables -A INPUT -p udp --dport 11211 -s 0.0.0.0/0 -j DROP
```

---

## 3. Techniques réseau

### Ingress / Egress Filtering (RFC 2827 / BCP38)

```
INGRESS FILTERING : Bloquer à l'entrée les paquets avec IP source invalide
                    (spoofée, réservée, etc.)

EGRESS FILTERING  : Bloquer à la sortie les paquets avec IP source
                    n'appartenant pas au réseau de l'opérateur

Résultat : Le spoofing d'adresses IP devient inefficace
```

### Blackhole Routing (RTBH)

```
Remote Triggered Blackhole (RTBH) :
                                    
Trafic malveillant vers IP X ──→ Routeur annonce X vers /dev/null
                                  (interface Null0)
                                  
→ Le trafic est absorbé sans impact sur l'infrastructure
→ Peut blackholer par IP source (SRTBH) ou destination (DRTBH)
```

### Rate Limiting

```
Commande Cisco IOS (exemple) :
  ip access-list extended LIMIT_ICMP
    permit icmp any any
  
  class-map match-all ICMP-CLASS
    match access-group name LIMIT_ICMP
  
  policy-map ICMP-RATE-LIMIT
    class ICMP-CLASS
      police rate 1000 pps conform-action transmit exceed-action drop
```

---

## 4. Solutions de protection avancées

### CDN (Content Delivery Network)

```
Sans CDN :
  Attaquant → [Internet] → Serveur unique → Saturé

Avec CDN :
  Attaquant → [Internet] → Points de présence CDN (PoP)
                                    │
                          Absorption du trafic malveillant
                                    │
                          Serveur d'origine protégé

Exemple : Cloudflare, Akamai, AWS CloudFront
```

### WAF (Web Application Firewall)

```
Client (potentiellement malveillant)
         │
         ▼
    [WAF] ← Analyse les requêtes HTTP
      │       - Détecte HTTP Flood
      │       - Détecte Slowloris
      │       - Bloque patterns anormaux
      │
      ▼
  Serveur web (protégé)
```

### Load Balancer

```
Trafic entrant (légitime + malveillant)
         │
         ▼
   [Load Balancer]
         │
    ┌────┴────┐
    ▼         ▼
 Serveur 1  Serveur 2  → Distribution de la charge
```

### Solutions DDoS dédiées

| Solution | Type | Description |
|----------|------|-------------|
| **Cloudflare Magic Transit** | Cloud | Protection réseau complète |
| **AWS Shield Advanced** | Cloud | Protection AWS native |
| **Arbor Networks (NETSCOUT)** | On-premise | Détection et mitigation avancée |
| **Radware DefensePro** | On-premise | Protection comportementale |
| **Anti DDoS Guardian** | On-premise | Protection Windows |

---

## 5. Détection

### Indicateurs d'une attaque DoS/DDoS

```
RÉSEAU :
  ✗ Pic soudain de bande passante (x10 ou plus)
  ✗ Grand nombre de connexions semi-ouvertes (SYN_RECV)
  ✗ Latence réseau anormalement élevée
  ✗ Perte de paquets massive

SYSTÈME :
  ✗ CPU à 100% sans raison apparente
  ✗ Mémoire épuisée
  ✗ Logs système pleins de connexions refusées
  ✗ Nombre anormal de processus en attente

APPLICATION :
  ✗ Temps de réponse web extrêmement lent
  ✗ Erreurs 503 / 504 en masse
  ✗ Logs Apache/Nginx : millions de requêtes depuis peu d'IPs
```

### Commandes de diagnostic

```bash
# Voir les connexions TCP actives (SYN_RECV = SYN Flood)
netstat -an | grep SYN_RECV | wc -l

# Voir le top des IPs en connexion
netstat -ntu | awk '{print $5}' | cut -d: -f1 | sort | uniq -c | sort -nr | head 20

# Surveiller les paquets en temps réel
tcpdump -i eth0 -nn host <target_ip>

# Voir la consommation bande passante
iftop -i eth0

# Wireshark filter SYN Flood
tcp.flags.syn == 1 && tcp.flags.ack == 0
```

---

## 6. Forensics Post-attaque

```
Processus de forensics après une attaque DoS/DDoS :

┌─────────────────────────────────────────────────────┐
│ 1. PRÉSERVATION                                     │
│    → Capturer les logs, pcaps, état du système      │
├─────────────────────────────────────────────────────┤
│ 2. IDENTIFICATION                                   │
│    → Identifier les IPs sources, vecteurs d'attaque │
│    → Analyser les patterns de trafic                │
├─────────────────────────────────────────────────────┤
│ 3. ANALYSE                                          │
│    → Reconstruire la timeline de l'attaque          │
│    → Estimer l'impact (durée, pertes financières)   │
├─────────────────────────────────────────────────────┤
│ 4. REPORTING                                        │
│    → Documenter l'attaque                           │
│    → Notifier les autorités si nécessaire           │
├─────────────────────────────────────────────────────┤
│ 5. AMÉLIORATION                                     │
│    → Mettre à jour les règles de firewall           │
│    → Améliorer la capacité de mitigation            │
│    → Former les équipes                             │
└─────────────────────────────────────────────────────┘
```

---

## 7. Checklist de défense

### ✅ Infrastructure réseau

- [ ] Activer l'ingress filtering sur tous les routeurs de bordure
- [ ] Configurer le rate limiting sur les protocoles ICMP, UDP, SYN
- [ ] Mettre en place le RTBH (Remote Triggered Blackhole)
- [ ] Désactiver les services inutiles (CHARGEN, ECHO, etc.)
- [ ] Bloquer les paquets IP spoofés (BCP38/RFC 2827)

### ✅ Serveurs Web

- [ ] Réduire les timeouts HTTP (keepalive, headers, body)
- [ ] Limiter le nombre de connexions par IP
- [ ] Déployer un WAF
- [ ] Utiliser Nginx (plus résistant que Apache aux Slowloris)
- [ ] Activer les SYN Cookies au niveau du kernel

```bash
# Activer SYN Cookies sur Linux
echo 1 > /proc/sys/net/ipv4/tcp_syncookies

# Ou dans /etc/sysctl.conf :
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
```

### ✅ Services DNS/NTP

- [ ] Désactiver les open resolvers DNS
- [ ] Désactiver `monlist` sur les serveurs NTP
- [ ] Implémenter Response Rate Limiting (RRL) sur les serveurs DNS
- [ ] Bloquer UDP 11211 (Memcached) depuis Internet

### ✅ Monitoring

- [ ] Configurer des alertes de bande passante (seuils)
- [ ] Surveiller les compteurs de connexions SYN_RECV
- [ ] Activer la journalisation des flux réseau (NetFlow/IPFIX)
- [ ] Tester régulièrement la résistance aux DoS (pen test autorisé)

---

## 🧠 Points clés à retenir pour l'examen

> 📝 **À mémoriser absolument :**

- **SYN Cookies** = défense principale contre SYN Flood — ne nécessite pas de file d'attente
- **BCP38 / RFC 2827** = standard pour empêcher l'IP spoofing
- **`no ip directed-broadcast`** = commande Cisco pour contrer Smurf
- **Désactiver `monlist` NTP** = contre-mesure NTP Amplification
- **Nginx > Apache** pour la résistance à Slowloris
- **RTBH** = technique de routage pour blackholer le trafic malveillant
- 6 axes de défense : Protéger / Détecter-Neutraliser / Prévenir / Dévier / Atténuer / Forensics

---

## 📊 Récapitulatif général du Module 10

```
DoS/DDoS
│
├── ATTAQUES VOLUMÉTRIQUES (bps)
│   ├── UDP Flood
│   ├── ICMP Flood
│   ├── Smurf Attack
│   ├── Ping of Death
│   └── Pulse Wave
│
├── ATTAQUES PROTOCOLE (pps)
│   ├── SYN Flood
│   ├── ACK Flood
│   ├── Fragmentation (Teardrop)
│   ├── Spoofed Session Flood
│   └── TCP SACK Panic
│
├── ATTAQUES APPLICATIVES (rps)
│   ├── HTTP GET/POST Flood
│   ├── Slowloris
│   ├── RUDY
│   └── RDoS (Ransom DDoS)
│
├── TECHNIQUES AVANCÉES
│   ├── Botnets (C&C, Handlers, Zombies)
│   ├── Amplification (DNS, NTP, Memcached)
│   └── Réflexion (Smurf, CHARGEN)
│
├── OUTILS
│   ├── LOIC / HOIC
│   ├── Hping3
│   └── Metasploit
│
└── CONTRE-MESURES
    ├── SYN Cookies
    ├── Rate Limiting
    ├── Ingress Filtering (BCP38)
    ├── WAF / CDN
    ├── RTBH
    └── Forensics
```

---

*⬅️ [Outils & Labs](./04_outils_et_labs.md) | ⬆️ [README](./README.md)*
