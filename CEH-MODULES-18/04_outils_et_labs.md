# 🔴 Module 18 — Outils & Labs : IoT & OT Hacking

> **CEH v13 | EC-Council**  
> `#Shodan` `#Binwalk` `#Nmap` `#Mosquitto` `#Metasploit`

---

## Tableau des Outils

| Outil | Usage | Type |
|-------|-------|------|
| **Shodan** | Reconnaissance IoT/SCADA en ligne | Reconnaissance passive |
| **Binwalk** | Analyse et extraction de firmware | Analyse firmware |
| **Firmwalker** | Scan de fichiers sensibles dans firmware | Analyse firmware |
| **Nmap + NSE** | Scan ports + scripts ICS | Scan réseau |
| **mosquitto_sub/pub** | Client MQTT | Exploitation |
| **modpoll** | Client Modbus | Exploitation SCADA |
| **Metasploit** | Modules SCADA/ICS | Exploitation |
| **OpenPLC** | Simulateur PLC pour labs | Lab |

---

## Commandes Nmap pour ICS/SCADA

```bash
# Scanner les protocoles industriels :
nmap --script modbus-discover -p 502 NETWORK
nmap --script s7-info -p 102 NETWORK     # PLCs Siemens
nmap --script dnp3-info -p 20000 NETWORK # DNP3
nmap --script bacnet-info -p 47808 NETWORK # BACnet

# Scan général des ports industriels :
nmap -p 102,502,503,1089,1090,1091,2222,4840,20000,44818 NETWORK
```

## Binwalk — Flux de travail complet

```bash
# 1. Télécharger le firmware :
wget https://firmware.vendor.com/router_fw_v1.2.bin

# 2. Analyse initiale :
binwalk router_fw_v1.2.bin
# Identifier : architecture, système de fichiers, bootloader

# 3. Extraction :
binwalk -e router_fw_v1.2.bin
cd _router_fw_v1.2.bin.extracted/

# 4. Trouver les secrets :
find . -name "*.conf" -o -name "*.cfg" -o -name "*.ini" | xargs grep -l "password"
find . -name "shadow" -o -name "passwd"
strings * | grep -i "API\|KEY\|SECRET\|PASS"

# 5. Identifier les binaires vulnérables :
find . -name "*.so" | xargs file  # Bibliothèques partagées
find . -type f -executable | head -20  # Binaires exécutables
```

---

## 🧠 Points clés à retenir pour l'examen

- **Binwalk** = outil principal pour l'analyse de firmware IoT
- **Shodan** = `port:502`, `port:1883`, `product:SCADA`
- **Nmap `--script modbus-discover`** = détecter Modbus sur port 502
- **mosquitto_sub -t "#"** = s'abonner à tous les topics MQTT
- Les **PLCs Siemens** utilisent le protocole S7 (port 102)

---

*⬅️ [Techniques d'attaques](./03_techniques_attaques.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
