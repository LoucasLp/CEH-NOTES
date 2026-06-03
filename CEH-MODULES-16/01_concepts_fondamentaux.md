# 🔴 Module 16 — Concepts Fondamentaux : Réseaux Sans Fil

> **CEH v13 | EC-Council**  
> `#80211` `#BSS` `#ESS` `#Canaux` `#Trames` `#MonitorMode`

---

## 📌 Table des matières

1. [Normes IEEE 802.11](#1-normes-ieee-80211)
2. [Architecture et topologies Wi-Fi](#2-architecture)
3. [Trames 802.11](#3-trames-80211)
4. [Canaux et fréquences](#4-canaux)
5. [Modes d'interface Wi-Fi](#5-modes-interface)

---

## 1. Normes IEEE 802.11

| Norme | Fréquence | Débit max | Portée | Sécurité |
|-------|-----------|-----------|--------|---------|
| **802.11b** | 2.4 GHz | 11 Mbps | ~35m | WEP (obsolète) |
| **802.11a** | 5 GHz | 54 Mbps | ~25m | WEP/WPA |
| **802.11g** | 2.4 GHz | 54 Mbps | ~38m | WPA/WPA2 |
| **802.11n** (Wi-Fi 4) | 2.4/5 GHz | 600 Mbps | ~70m | WPA2 |
| **802.11ac** (Wi-Fi 5) | 5 GHz | 3.5 Gbps | ~35m | WPA2/WPA3 |
| **802.11ax** (Wi-Fi 6) | 2.4/5/6 GHz | 9.6 Gbps | ~35m | WPA3 |

---

## 2. Architecture et Topologies

```
BSS (Basic Service Set) :
  [AP] ────── [Client 1]
        ────── [Client 2]    ← Infrastructure mode
        ────── [Client 3]
  
IBSS / Ad-hoc :
  [Client 1] ────── [Client 2]   ← Pas d'AP
  
ESS (Extended Service Set) :
  [AP1] ─── [DS (Distribution System)] ─── [AP2]
               (câble Ethernet / backhaul)
  Même SSID, plusieurs APs → Mobilité des clients

BSSID : Adresse MAC de l'AP
SSID  : Nom du réseau (ex: "HomeNetwork")
ESSID : Nom du réseau dans un ESS multi-APs
```

---

## 3. Trames 802.11

```
Types de trames :

Management Frames :
  ├── Beacon        : L'AP annonce sa présence (toutes les 100ms)
  ├── Probe Request : Le client recherche des réseaux
  ├── Probe Response: L'AP répond au probe
  ├── Association   : Le client se connecte à l'AP
  ├── Deauthentication: Déconnexion (peut être forgée !)
  └── Authentication : Processus d'authentification

Control Frames :
  ├── ACK           : Accusé de réception
  ├── RTS           : Request to Send
  └── CTS           : Clear to Send

Data Frames :
  └── Transport des données utilisateur

⚠️ Les management frames ne sont PAS chiffrées (hors WPA3 Protected Management Frames)
→ Attaque de déauthentification possible !
```

---

## 4. Canaux et Fréquences

```
Bande 2.4 GHz — 13 canaux (France) :
Canaux non chevauchants : 1, 6, 11
  Ch1  : 2412 MHz
  Ch6  : 2437 MHz
  Ch11 : 2462 MHz

Bande 5 GHz — Plus de canaux, moins de congestion :
  Canaux 36, 40, 44, 48 (bande UNII-1)
  Canaux 52, 56, 60, 64 (bande UNII-2)
  ...

DFS (Dynamic Frequency Selection) :
  Certains canaux 5GHz partagés avec le radar militaire
  L'AP doit changer de canal si radar détecté
```

---

## 5. Modes d'Interface Wi-Fi

```
Modes importants pour l'audit :

MANAGED (Infrastructure) :
  Interface normale connectée à un AP
  
MONITOR :
  Capture TOUS les paquets Wi-Fi (tous SSID, tous canaux)
  Requis pour les attaques avec aircrack-ng, airodump-ng
  
  airmon-ng start wlan0       ← Passer en mode monitor
  → Crée wlan0mon (ou wlan0_mon)
  
MASTER (AP) :
  L'interface joue le rôle d'un AP
  Utilisé pour les Rogue AP et Evil Twin
  
AD-HOC :
  Communication P2P sans AP
```

> ⚠️ **Rappel CEH** : **Le mode Monitor** est indispensable pour capturer les handshakes WPA2 et les IVs WEP. Toutes les attaques Wi-Fi avancées commencent par `airmon-ng start wlan0`.

---

## 🧠 Points clés à retenir pour l'examen

- **802.11n** = Wi-Fi 4, dual-band ; **802.11ac** = Wi-Fi 5, 5GHz uniquement
- **BSS** = 1 AP + ses clients ; **ESS** = plusieurs APs même SSID
- **BSSID** = MAC de l'AP ; **SSID** = nom du réseau
- Les management frames sont **non chiffrées** → attaques de déauth possibles
- **Mode Monitor** = capturer tous les paquets Wi-Fi
- Canaux non chevauchants 2.4GHz : 1, 6, 11

---

*⬅️ [README](./README.md) | ➡️ [Protocoles et vulnérabilités](./02_protocoles_vulnerabilites.md)*
