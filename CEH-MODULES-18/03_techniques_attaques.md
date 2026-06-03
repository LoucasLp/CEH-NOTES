# 🔴 Module 18 — Techniques d'Attaques : IoT & OT

> **CEH v13 | EC-Council**  
> `#Shodan` `#Binwalk` `#MQTT` `#Modbus` `#FirmwareAnalysis`

---

## 1. Reconnaissance avec Shodan

```bash
# Shodan = moteur de recherche pour appareils connectés

# Requêtes Shodan IoT :
port:1883                           # MQTT non chiffré exposés
product:"SCADA"                     # Systèmes SCADA
title:"Admin Panel" port:80         # Panels admin
"default password"                   # Appareils avec mention de MDP par défaut
webcamxp                            # Caméras IP vulnérables
"Modbus" port:502                   # Interfaces Modbus
"BACnet" port:47808                 # Bâtiments intelligents
"s7comm" port:102                   # PLCs Siemens

# Shodan CLI :
shodan search --fields ip_str,port,org "MQTT" > mqtt_targets.txt
shodan host 1.2.3.4                 # Informations sur une IP
shodan count "webcam"               # Nombre de résultats
```

---

## 2. Analyse de Firmware

```bash
# Obtention du firmware :
# 1. Site du fabricant (download page)
# 2. Extraction physique (JTAG, UART, SPI flash)
# 3. Man-in-the-Middle des mises à jour OTA

# ─── BINWALK ──────────────────────────────────────────────────
# Analyser le firmware :
binwalk firmware.bin

# Résultat typique :
# DECIMAL   HEXADECIMAL   DESCRIPTION
# 0         0x0           BIN data
# 512       0x200         U-Boot bootloader
# 131072    0x20000       LZMA compressed data
# 1048576   0x100000      Squashfs filesystem

# Extraire le contenu :
binwalk -e firmware.bin         # Extraction automatique
binwalk -Me firmware.bin        # Extraction récursive

# Après extraction :
ls _firmware.bin.extracted/
# → Trouver : etc/passwd, etc/shadow, etc/config, bin/, usr/

# Chercher des secrets dans le firmware :
strings firmware.bin | grep -i "password\|key\|secret\|admin"
grep -r "api_key" _firmware.bin.extracted/

# Firmwalker (scan de fichiers sensibles) :
firmwalker.sh _firmware.bin.extracted/
```

---

## 3. Exploitation MQTT

```bash
# MQTT = protocole pub/sub IoT
# Broker = serveur central
# Topics = chemins de message (ex: home/living/temperature)

# Scanner les brokers MQTT exposés (port 1883) :
nmap -p 1883 192.168.1.0/24

# Se connecter à un broker public (exemple Mosquitto) :
mosquitto_sub -h broker.example.com -t "#" -v
#                                        ↑ Abonnement à TOUS les topics !
# → Voir tous les messages publiés !

# Publier un message (si write autorisé) :
mosquitto_pub -h broker.example.com -t "home/alarm/control" -m "disable"
# → Si le broker l'accepte → désactiver une alarme !

# MQTT Explorer (outil graphique) :
# → Interface graphique pour explorer un broker MQTT
# → Voir la hiérarchie des topics
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
# → Lire 100 registres depuis le registre 1

# Avec Metasploit :
use auxiliary/scanner/scada/modbusdetect
set RHOSTS 192.168.1.100
run

# Écrire un registre (dangereux !) :
# Dans un contexte de test uniquement :
# modpoll -w -r 1 192.168.1.100 1
# → Pourrait déclencher une action physique (ouvrir une valve !)
```

---

## 5. Attaques Firmware OTA

```
Man-in-the-Middle des mises à jour :
  1. Positionner un MITM entre l'appareil et le serveur de mise à jour
  2. Si update via HTTP → Intercepter et remplacer le firmware
  3. Injecter un firmware malveillant
  
Replay attack :
  1. Capturer une mise à jour légitime
  2. Rejouer une ancienne version (avec vulnérabilités connues)
  
Protection :
  → HTTPS pour les mises à jour
  → Vérification de signature cryptographique du firmware
  → Secure Boot (vérifie la signature au démarrage)
```

---

## 🧠 Points clés à retenir pour l'examen

- **Shodan** = moteur de recherche pour trouver des appareils IoT/SCADA exposés
- **Binwalk** = analyser et extraire le contenu d'un firmware
- **MQTT port 1883** = non chiffré, abonnement `#` = voir tous les messages
- **Modbus port 502** = pas d'authentification, commandes directes aux PLCs
- **Stuxnet** = malware via clé USB ciblant les PLCs Siemens (Iran 2010)
- **JTAG/UART** = accès physique pour extraire ou modifier le firmware

---

*⬅️ [Vulnérabilités IoT/OT](./02_vulnerabilites_iot_ot.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
