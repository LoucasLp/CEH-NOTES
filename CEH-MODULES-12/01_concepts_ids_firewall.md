# 🔴 Module 12 — Concepts : IDS, IPS, Firewalls & Honeypots

> **CEH v13 | EC-Council**  
> `#IDS` `#IPS` `#Firewall` `#Honeypot` `#Snort` `#NIDS`

---

## 📌 Table des matières

1. [IDS — Intrusion Detection System](#1-ids)
2. [IPS — Intrusion Prevention System](#2-ips)
3. [Types de firewalls](#3-firewalls)
4. [Honeypots et Honeynets](#4-honeypots)
5. [Snort — Basics](#5-snort-basics)
6. [Architecture de défense en profondeur](#6-défense-en-profondeur)

---

## 1. IDS — Intrusion Detection System

### Définition

Un IDS **détecte** les activités suspectes et génère des alertes — il ne bloque pas le trafic.

### Types d'IDS par placement

```
┌─────────────────────────────────────────────┐
│                INTERNET                      │
└──────────────────┬──────────────────────────┘
                   │
         ┌─────────▼─────────┐
         │     FIREWALL       │
         └─────────┬─────────┘
                   │
         ┌─────────▼─────────┐
         │  NIDS (Network)    │ ← Analyse le trafic réseau
         │  [Passive/Inline]  │
         └─────────┬─────────┘
                   │
    ┌──────────────┼──────────────┐
    │              │              │
 [Serveur 1]   [Serveur 2]   [Serveur 3]
 + HIDS         + HIDS         + HIDS
 (Host-based)
```

### Types de détection IDS

| Type | Mécanisme | Avantages | Inconvénients |
|------|-----------|-----------|---------------|
| **Signature-based** | Comparer aux signatures connues | Faible faux positifs | Ne détecte pas les 0-days |
| **Anomaly-based** | Baseline du comportement normal | Détecte les nouveaux patterns | Taux de faux positifs élevé |
| **Stateful Protocol** | Analyse le respect des protocoles | Détecte les violations RFC | Coûteux en ressources |
| **Heuristic** | Algorithmes d'apprentissage | Adaptable | Complexe à tuner |

### NIDS vs HIDS

| Critère | NIDS | HIDS |
|---------|------|------|
| **Emplacement** | Réseau (tap ou inline) | Sur chaque hôte |
| **Analyse** | Trafic réseau | Logs système, fichiers |
| **Aveugle à** | Trafic chiffré (TLS) | Attaques réseau externes |
| **Exemples** | Snort, Suricata, Zeek | OSSEC, Wazuh, Tripwire |

---

## 2. IPS — Intrusion Prevention System

L'IPS est un IDS en mode **inline** qui peut **bloquer** activement le trafic suspect.

```
Mode IDS (passif) :
Trafic ──▶ [COPY] ──▶ Destination (trafic non interrompu)
                │
                ▼
          [IDS analyse]
          [Alert seulement]

Mode IPS (inline) :
Trafic ──▶ [IPS] ──▶ Destination (trafic INTERROMPU si suspect)
              │
              ▼
        [Bloquer / Permettre / Logger]
```

> ⚠️ **Rappel CEH** : Un IPS mal configuré peut bloquer du trafic légitime (faux positifs) causant du déni de service. La tolérance aux faux positifs est l'enjeu principal du tuning IPS.

---

## 3. Firewalls

### Types de firewalls (évolution historique)

```
Génération 1 : PACKET FILTERING
├── Analyse : IP src/dst, port src/dst, protocole
├── Stateless : chaque paquet traité indépendamment
├── Limite : ne voit pas les états de connexion
└── Exemples : iptables (règles simples), ACL routeur

Génération 2 : STATEFUL INSPECTION
├── Analyse : état des connexions TCP (SYN, ESTABLISHED, FIN)
├── Table d'état : suit les connexions actives
├── Bloque les paquets hors-état (ex: ACK sans SYN préalable)
└── Exemples : Windows Firewall, iptables avec conntrack

Génération 3 : APPLICATION LAYER (PROXY)
├── Analyse : contenu applicatif (HTTP, DNS, FTP...)
├── Proxy : connexions séparées client-firewall et firewall-serveur
├── Inspection profonde du payload
└── Exemples : Squid Proxy, pfSense

Génération 4 : NEXT-GENERATION FIREWALL (NGFW)
├── Tout le précédent +
├── Application awareness (reconnaît Facebook, Zoom, etc.)
├── User identity (qui fait quoi)
├── SSL inspection (déchiffre le TLS)
├── Threat intelligence intégrée
└── Exemples : Palo Alto, Fortinet, Cisco FTD
```

### Zones typiques d'un firewall

```
INTERNET
   │
[FIREWALL EXTERNE]
   │
  DMZ ──── [Web Server] [Mail Server] [DNS]
   │
[FIREWALL INTERNE]
   │
RÉSEAU INTERNE ──── [Workstations] [AD] [DB Server]
```

---

## 4. Honeypots et Honeynets

### Définition

Un honeypot est un **leurre** — un système délibérément vulnérable conçu pour attirer et étudier les attaquants.

```
Réseau de production :
[DB Server réel] [Web Server réel] [AD réel]
       ↑                ↑               ↑
   Précieux        Précieux         Précieux

Honeypot :
[Faux serveur] ← Pas de trafic légitime attendu
                   → Toute connexion = suspecte
```

### Types de honeypots

| Type | Niveau d'interaction | Usage |
|------|---------------------|-------|
| **Low interaction** | Émule des services (ports ouverts) | Production, détection rapide |
| **High interaction** | Vrai OS, vrais services | Recherche, analyse comportement |
| **Production** | Intégré au réseau de l'entreprise | Détection d'intrus internes |
| **Research** | Isolé, pour étude | Analyse de malware |

### Honeynets

```
Honeynet = réseau de plusieurs honeypots interconnectés
→ Permet d'observer une attaque complète (latéral movement, etc.)
→ Contrôle via "honeywall" (routeur/bridge spécial)

Outils : Honeyd, Kippo (SSH honeypot), Cowrie, T-Pot
```

### Détection d'un honeypot (attaquant)

```
Indicateurs possibles :
- Trop peu de services "attendus" pour un serveur
- Pas de trafic "normal" sur la machine
- Réponses anormalement lentes ou parfaites
- Latence artificielle (émulation)
- Nmap : trop de ports ouverts sans cohérence
- Certains outils détectent les honeypots connus (Kippo, Honeyd)
```

---

## 5. Snort — Basics

Snort est un IDS/IPS open-source, signature-based.

```bash
# Modes Snort :
snort -v                    # Mode sniffer (afficher paquets)
snort -vd                   # Mode sniffer détaillé
snort -c /etc/snort.conf    # Mode IDS (utiliser fichier de règles)
snort -A alert              # Mode alerte
```

### Structure d'une règle Snort

```
alert tcp any any -> 192.168.1.0/24 80 (msg:"HTTP GET suspect"; content:"GET"; sid:1001; rev:1;)
│     │   │   │      │               │   │
│     │   │   │      │               │   └── Options
│     │   │   │      │               └────── Port destination
│     │   │   │      └────────────────────── IP destination
│     │   │   └───────────────────────────── Port source (any)
│     │   └───────────────────────────────── IP source (any)
│     └───────────────────────────────────── Protocole
└─────────────────────────────────────────── Action (alert/drop/log)
```

---

## 6. Architecture de défense en profondeur

```
COUCHE 1 — PÉRIMÈTRE
  Firewall externe, IPS, anti-DDoS

COUCHE 2 — RÉSEAU
  VLAN, segmentation, NIDS

COUCHE 3 — HÔTE
  HIDS, EDR, antivirus, patching

COUCHE 4 — APPLICATION
  WAF, authentification forte, chiffrement

COUCHE 5 — DONNÉES
  Chiffrement, DLP, backup, IAM

COUCHE 6 — DÉTECTION
  SIEM, threat hunting, SOC
```

---

## 🧠 Points clés à retenir pour l'examen

- IDS = **détection** uniquement ; IPS = **prévention** (bloque)
- NIDS = réseau ; HIDS = hôte
- Signature-based = connu ; Anomaly-based = comportement baseline
- Honeypot = leurre ; trafic vers honeypot = forcément suspect
- NGFW = combine stateful + application awareness + user identity + SSL inspection

---

*⬅️ [README](./README.md) | ➡️ [Techniques d'évasion IDS](./02_techniques_evasion_ids.md)*
