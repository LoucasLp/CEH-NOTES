# 🔴 Module 18 — Concepts Fondamentaux : IoT & OT

> **CEH v13 | EC-Council**  
> `#IoT` `#OT` `#SCADA` `#ICS` `#PLC` `#Modbus` `#MQTT`

---

## 📌 Table des matières

1. [Architecture IoT](#1-architecture-iot)
2. [OT/SCADA/ICS — Définitions](#2-otscadaics--définitions)
3. [Protocoles Industriels Importants](#3-protocoles-industriels-importants)
4. [Différences IT vs OT](#4-différences-it-vs-ot)

---

## 1. Architecture IoT

```
Couche 1 — PERCEPTION (objets physiques) :
  Capteurs, actionneurs, caméras, thermostats, compteurs intelligents
  Protocoles : Zigbee, Z-Wave, Bluetooth LE, RFID

Couche 2 — RÉSEAU (connectivity) :
  Wi-Fi, 4G/5G, LoRaWAN, NB-IoT, Ethernet
  Protocoles : MQTT, CoAP, AMQP, WebSockets

Couche 3 — APPLICATION (traitement, cloud) :
  AWS IoT, Azure IoT Hub, Google Cloud IoT
  Dashboard, analytics, contrôle
```

### Protocoles IoT importants

| Protocole | Port | Usage | Sécurité |
|-----------|------|-------|---------|
| **MQTT** | 1883 / 8883 (TLS) | Messagerie IoT légère (pub/sub) | Auth faible par défaut |
| **CoAP** | 5683 UDP | Appareils contraints (REST-like) | DTLS optionnel |
| **AMQP** | 5672 / 5671 (TLS) | Messagerie enterprise IoT | Plus sécurisé |
| **Zigbee** | 2.4 GHz | Domotique (Z-Wave aussi) | Chiffrement AES |
| **LoRaWAN** | RF | IoT longue portée | AES-128 |

> 💡 **Question type exam** : *Quel port MQTT est chiffré ?* → **8883 (TLS)** ; le port 1883 est non chiffré.

---

## 2. OT/SCADA/ICS — Définitions

```
OT (Operational Technology) :
  → Hardware/software qui contrôle les équipements physiques
  → Usines, centrales électriques, stations de traitement d'eau

ICS (Industrial Control System) :
  → Famille de systèmes de contrôle industriels
  → Comprend SCADA, DCS, PLC

SCADA (Supervisory Control and Data Acquisition) :
  → Supervision et contrôle à distance
  → Interface homme-machine (HMI)
  → Collecte de données (historique)

DCS (Distributed Control System) :
  → Contrôle distribué dans une installation (raffinerie, centrale)

PLC (Programmable Logic Controller) :
  → Automate programmable, contrôle les processus physiques
  → Ex: ouvrir/fermer une valve, démarrer un moteur

RTU (Remote Terminal Unit) :
  → Collecte données terrain, envoie au SCADA

HMI (Human-Machine Interface) :
  → Interface graphique de supervision pour les opérateurs
```

### Architecture SCADA typique

```
[Internet] ─── [Corporate Network] ─── [DMZ] ─── [Control Network]
                                                        │
                                              [SCADA Server / HMI]
                                                        │
                                    ┌───────────────────┼───────────────────┐
                                    │                   │                   │
                               [PLC 1]              [PLC 2]             [RTU]
                                    │                   │                   │
                             [Valve, Pump]     [Conveyor]          [Capteurs]
```

---

## 3. Protocoles Industriels Importants

| Protocole | Port | Usage | Sécurité |
|-----------|------|-------|---------|
| **Modbus** | TCP/502 | Protocole série/TCP industriel, très répandu | Pas d'authentification, pas de chiffrement ! |
| **DNP3** | 20000 | SCADA, utilities (eau, électricité) | Version sécurisée disponible mais rare |
| **IEC 61850** | — | Sous-stations électriques | Chiffrement possible |
| **BACnet** | 47808 UDP | Bâtiments intelligents (HVAC) | Authentification optionnelle |
| **PROFIBUS** | — | Automatisation industrielle | Limité |
| **EtherNet/IP** | 44818 | Réseaux industriels Allen-Bradley | Peu sécurisé par défaut |
| **S7comm** | 102 | PLCs Siemens (cible de Stuxnet) | Authentification optionnelle |

> ⚠️ **Rappel CEH** : **Modbus** = aucune authentification, aucun chiffrement. Quiconque accède au réseau OT peut envoyer des commandes directes aux PLCs.

---

## 4. Différences IT vs OT

| Critère | IT (Informatique) | OT (Industriel) |
|---------|------------------|-----------------|
| **Priorité** | CIA (Confidentialité d'abord) | **Disponibilité** avant tout |
| **Cycle de vie** | 3-5 ans | **20-40 ans** |
| **Patchage** | Régulier, automatisé | Rare (arrêts de production planifiés) |
| **Tolérance panne** | Quelques heures tolérables | Quasi-zéro (systèmes critiques) |
| **OS courants** | Windows 10+, Linux récent | Windows XP non patché encore courant ! |
| **Tests de sécu** | Tests réguliers acceptables | Peut endommager l'équipement physique ! |
| **Redémarrage** | Simple et rapide | Peut prendre des heures (redémarrage four aciérie) |

> ⚠️ **Rappel CEH** : En OT, la **disponibilité** est la priorité absolue, avant la confidentialité. Un arrêt d'une centrale électrique = catastrophe. Impossible de redémarrer un PLC pendant les tests !

---

## 🧠 Points clés à retenir pour l'examen

- **SCADA** = supervision/contrôle à distance (Supervisory Control and Data Acquisition)
- **PLC** = automate programmable (contrôle les processus physiques)
- **HMI** = interface graphique opérateur (Human-Machine Interface)
- **Modbus port 502** = protocole industriel sans authentification ni chiffrement
- **MQTT port 1883** = non chiffré ; **port 8883** = TLS (chiffré)
- **S7comm port 102** = protocole PLCs Siemens (cible de Stuxnet)
- OT priorité = **Disponibilité > Intégrité > Confidentialité** (inversé vs IT)
- Air gap = séparation physique du réseau OT d'Internet (souvent un mythe — Stuxnet clé USB !)

---

*⬅️ [README](./README.md) | ➡️ [Vulnérabilités IoT/OT](./02_vulnerabilites_iot_ot.md)*
