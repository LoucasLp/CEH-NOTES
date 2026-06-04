# 🔴 Module 18 — Techniques d'Attaques : IoT & OT

> **CEH v13 | EC-Council**  
> `#Shodan` `#Binwalk` `#MQTT` `#Modbus` `#FirmwareAnalysis`

---

## 📌 Table des matières

1. [Reconnaissance avec Shodan](#1-reconnaissance-avec-shodan)
2. [Analyse de Firmware](#2-analyse-de-firmware)
3. [Exploitation MQTT](#3-exploitation-mqtt)
4. [Exploitation Modbus](#4-exploitation-modbus)
5. [Attaques Firmware OTA](#5-attaques-firmware-ota)

---

## 1. Reconnaissance avec Shodan

```bash
# Shodan = moteur de recherche pour appareils connectés (IoT/SCADA/ICS)

# Requêtes Shodan IoT :
port:1883                           # Brokers MQTT non chiffrés exposés
product:"SCADA"                     # Systèmes SCADA accessibles
title:"Admin Panel" port:80         # Panels admin accessibles
"default password"                   # Appareils avec mention de MDP par défaut
webcamxp                            # Caméras IP vulnérables
"Modbus" port:502                   # Interfaces Modbus exposées
"BACnet" port:47808                 # Bâtiments intelligents (HVAC)
"s7comm" port:102                   # PLCs Siemens

# Shodan CLI :
shodan search --fields ip_str,port,org "MQTT" > mqtt_targets.txt
shodan host 1.2.3.4                 # Informations détaillées sur une IP
shodan count "webcam"               # Nombre de résultats pour une requête
```

> 💡 **Question type exam** : *Quel outil est utilisé pour trouver des appareils IoT/SCADA exposés sur Internet ?* → **Shodan**

---

## 2. Analyse de Firmware

```bash
# Obtention du firmware :
# 1. Site du fabricant (download page officielle)
# 2. Extraction physique (JTAG, UART, SPI flash chip)
# 3. Man-in-the-Middle des mises à jour OTA (si non signées)

# ─── BINWALK ──────────────────────────────────────────────────
# Analyser la structure du firmware :
binwalk firmware.bin

# Résultat typique :
# DECIMAL   HEXADECIMAL   DESCRIPTION
# 0         0x0           BIN data
# 512       0x200         U-Boot bootloader
# 131072    0x20000       LZMA compressed data
# 1048576   0x100000      Squashfs filesystem

# Extraire le contenu :
binwalk -e firmware.bin         # Extraction automatique
binwalk -Me firmware.bin        # Extraction récursive (firmwares imbriqués)

# Après extraction :
ls _firmware.bin.extracted/
# → Trouver : etc/passwd, etc/shadow, etc/config, bin/, usr/

# Chercher des secrets dans le firmware :
strings firmware.bin | grep -i "password\|key\|secret\|admin"
grep -r "api_key" _firmware.bin.extracted/

# Firmwalker (scan automatique de fichiers sensibles) :
firmwalker.sh _firmware.bin.extracted/
# → Cherche automatiquement : mots de passe, clés, configs, binaires vulnérables
```

---

## 3. Exploitation MQTT

```bash
# MQTT = protocole pub/sub IoT
# Broker = serveur central de messages
# Topics = chemins de message (ex: home/living/temperature)

# Scanner les brokers MQTT exposés (port 1883) :
nmap -p 1883 192.168.1.0/24

# Se connecter à un broker (écoute passive) :
mosquitto_sub -h broker.example.com -t "#" -v
#                                        ↑ Abonnement à TOUS les topics !
# → Voir tous les messages publiés par tous les appareils !

# Publier un message malveillant (si write autorisé) :
mosquitto_pub -h broker.example.com -t "home/alarm/control" -m "disable"
# → Si le broker l'accepte → désactiver une alarme à distance !

# Autres exemples de topics sensibles :
mosquitto_pub -h target -t "factory/plc1/command" -m "stop"
mosquitto_pub -h target -t "home/lock/frontdoor" -m "unlock"
```

---

## 4. Exploitation Modbus

```bash
# Modbus = protocole industriel sans auth ni chiffrement
# Port TCP 502

# Scanner les appareils Modbus :
nmap --script modbus-discover -p 502 192.168.1.0/24

# Lire des registres Modbus avec modpoll :
modpoll -1 -r 1 -c 100 192.168.1.100
# → Lire 100 registres depuis le registre 1 (valeurs des capteurs/actionneurs)

# Avec Metasploit :
use auxiliary/scanner/scada/modbusdetect
set RHOSTS 192.168.1.100
run

# Écrire un registre (dangereux — test uniquement en lab !) :
# modpoll -w -r 1 192.168.1.100 1
# → Pourrait déclencher une action physique (ouvrir une valve, démarrer un moteur !)
```

> ⚠️ **Rappel CEH** : Écrire dans des registres Modbus en environnement réel peut causer des **dommages physiques** sur les équipements industriels. Toujours agir en environnement de test isolé.

---

## 5. Attaques Firmware OTA

```
Man-in-the-Middle des mises à jour (update sans HTTPS) :
  1. Positionner un MITM entre l'appareil et le serveur de mise à jour
  2. Si update via HTTP → Intercepter et remplacer le firmware légitime
  3. Injecter un firmware malveillant → persistance totale sur l'appareil

Replay attack (downgrade) :
  1. Capturer un paquet de mise à jour légitime
  2. Rejouer une ancienne version vulnérable
  → Réintroduire des vulnérabilités connues, patchées dans les versions récentes

Protection contre ces attaques :
  → HTTPS obligatoire pour les mises à jour (TLS + pinning du certificat)
  → Vérification de signature cryptographique du firmware (RSA/ECC)
  → Secure Boot (vérifie la signature au démarrage, avant exécution)
  → Version pinning (rejeter les versions plus anciennes que la courante)
```

---

## 🧠 Points clés à retenir pour l'examen

- **Shodan** = moteur de recherche pour trouver des appareils IoT/SCADA exposés sur Internet
- **Binwalk** = analyser et extraire le contenu d'un firmware (structure, système de fichiers)
- **Firmwalker** = scanner automatique de fichiers sensibles dans un firmware extrait
- **MQTT port 1883** = non chiffré ; `#` = wildcard pour s'abonner à TOUS les topics
- **Modbus port 502** = pas d'authentification, lecture/écriture directe des registres PLCs
- **Stuxnet** = malware via clé USB ciblant les PLCs Siemens S7 (Iran 2010)
- **JTAG/UART** = accès physique pour extraire ou modifier le firmware sans credentials
- Écrire dans Modbus = peut causer des **actions physiques réelles** (danger !)

---

*⬅️ [Vulnérabilités IoT/OT](./02_vulnerabilites_iot_ot.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
