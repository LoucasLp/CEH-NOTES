# 🔴 Module 18 — Outils & Labs : IoT & OT Hacking

> **CEH v13 | EC-Council**  
> `#Shodan` `#Binwalk` `#Nmap` `#Mosquitto` `#Metasploit`

---

## 📌 Table des matières

1. [Tableau des Outils](#1-tableau-des-outils)
2. [Commandes Nmap pour ICS/SCADA](#2-commandes-nmap-pour-icsscada)
3. [Binwalk — Flux de travail complet](#3-binwalk--flux-de-travail-complet)
4. [MQTT — Tests pratiques](#4-mqtt--tests-pratiques)

---

## 1. Tableau des Outils

| Outil | Usage | Type |
|-------|-------|------|
| **Shodan** | Reconnaissance IoT/SCADA en ligne | Reconnaissance passive |
| **Binwalk** | Analyse et extraction de firmware | Analyse firmware |
| **Firmwalker** | Scan de fichiers sensibles dans firmware | Analyse firmware |
| **Nmap + NSE** | Scan ports + scripts ICS (modbus, s7, bacnet) | Scan réseau |
| **mosquitto_sub/pub** | Client MQTT en ligne de commande | Exploitation |
| **modpoll** | Client Modbus TCP | Exploitation SCADA |
| **Metasploit** | Modules SCADA/ICS (modbusdetect, etc.) | Exploitation |
| **OpenPLC** | Simulateur PLC pour labs | Lab/formation |
| **MQTT Explorer** | Interface graphique pour explorer un broker | Analyse |

---

## 2. Commandes Nmap pour ICS/SCADA

```bash
# Scanner les protocoles industriels courants :
nmap --script modbus-discover -p 502 NETWORK     # Modbus (PLCs génériques)
nmap --script s7-info -p 102 NETWORK             # PLCs Siemens S7
nmap --script dnp3-info -p 20000 NETWORK         # DNP3 (utilities eau/élec)
nmap --script bacnet-info -p 47808 NETWORK       # BACnet (bâtiments)

# Scan général des ports industriels courants :
nmap -p 102,502,503,1089,1090,1091,2222,4840,20000,44818 NETWORK

# Détection de versions sur équipements industriels :
nmap -sV -p 502,102,47808 --script=banner 192.168.1.0/24

# Scan complet avec OS detection :
nmap -A -p 502,102 --script modbus-discover 192.168.1.0/24
```

---

## 3. Binwalk — Flux de travail complet

```bash
# 1. Télécharger le firmware du fabricant :
wget https://firmware.vendor.com/router_fw_v1.2.bin

# 2. Analyse initiale (identifier les composants) :
binwalk router_fw_v1.2.bin
# Identifier : architecture CPU, système de fichiers, bootloader, compression

# 3. Extraction complète :
binwalk -e router_fw_v1.2.bin
cd _router_fw_v1.2.bin.extracted/

# 4. Recherche de fichiers sensibles :
find . -name "*.conf" -o -name "*.cfg" -o -name "*.ini" | xargs grep -l "password"
find . -name "shadow" -o -name "passwd"           # Fichiers de mots de passe
strings * 2>/dev/null | grep -i "API\|KEY\|SECRET\|PASS\|TOKEN"

# 5. Identifier les binaires vulnérables :
find . -name "*.so" | xargs file                  # Bibliothèques partagées
find . -type f -executable | head -20             # Binaires exécutables

# 6. Firmwalker (automatisation de l'analyse) :
firmwalker.sh _router_fw_v1.2.bin.extracted/
```

---

## 4. MQTT — Tests pratiques

```bash
# Installation client Mosquitto :
apt install mosquitto-clients

# Écoute passive sur un broker (reconnaissance) :
mosquitto_sub -h 192.168.1.100 -t "#" -v
# → "#" = wildcard : abonnement à TOUS les topics
# → Voir tous les échanges entre appareils IoT

# Écoute sur un topic spécifique :
mosquitto_sub -h 192.168.1.100 -t "home/+/temperature" -v
# → "+" = wildcard un niveau (ex: home/salon/temperature, home/cuisine/temperature)

# Publier sur un topic (test d'écriture) :
mosquitto_pub -h 192.168.1.100 -t "home/alarm/status" -m "disarmed"

# Broker avec authentification (si configuré) :
mosquitto_sub -h broker.example.com -u username -P password -t "#" -v

# Scanner les brokers MQTT sur le réseau local :
nmap -p 1883,8883 192.168.1.0/24
```

---

## 🧠 Points clés à retenir pour l'examen

- **Binwalk** = outil principal pour l'analyse de firmware IoT (extraction + analyse)
- **Shodan requêtes clés** : `port:502` (Modbus), `port:1883` (MQTT), `product:SCADA`
- **Nmap `--script modbus-discover`** = détecter Modbus sur port 502
- **mosquitto_sub -t "#"** = s'abonner à tous les topics MQTT (wildcard total)
- **PLCs Siemens** = protocole S7comm, port 102 (nmap `--script s7-info`)
- **Firmwalker** = automatise la recherche de secrets dans un firmware extrait

---

*⬅️ [Techniques d'attaques](./03_techniques_attaques.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
