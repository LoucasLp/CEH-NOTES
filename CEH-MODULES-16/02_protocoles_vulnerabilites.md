# 🔴 Module 16 — Protocoles Wi-Fi et Vulnérabilités

> **CEH v13 | EC-Council**  
> `#WEP` `#WPA` `#WPA2` `#WPA3` `#WPS` `#8021X` `#KRACK`

---

## 📌 Table des matières

1. [WEP — Wired Equivalent Privacy (obsolète)](#1-wep)
2. [WPA — Wi-Fi Protected Access](#2-wpa)
3. [WPA2 — Sécurité actuelle](#3-wpa2)
4. [WPA3 — Standard moderne](#4-wpa3)
5. [WPS — Wi-Fi Protected Setup](#5-wps)
6. [802.1X et EAP — Enterprise](#6-8021x-et-eap)

---

## 1. WEP — Wired Equivalent Privacy (Obsolète)

```
Chiffrement : RC4 (stream cipher)
IV (Initialization Vector) : 24 bits (seulement 16 millions de valeurs !)
Authentification : Open ou Shared Key

Faiblesses fondamentales :
┌─────────────────────────────────────────────────────────────┐
│ 1. IV court (24 bits) → Réutilisation des IVs inévitable    │
│    → Après ~5000 paquets, collisions statistiques           │
│    → Attaque statistique FMS/KoreK/PTW                     │
│                                                             │
│ 2. RC4 mal implémenté                                       │
│    → Le keystream peut être déduit si l'IV se répète        │
│                                                             │
│ 3. CRC32 pour l'intégrité (pas d'authentification)         │
│    → Bit flipping possible (modifier le payload)            │
│                                                             │
│ 4. Longueur de clé faible : 40 ou 104 bits                 │
└─────────────────────────────────────────────────────────────┘

Temps de cracking WEP :
  ~5000-50000 IVs nécessaires
  → Quelques minutes à heures selon le trafic
  → Peut accélérer avec aireplay-ng (injection ARP)

Commandes :
  airodump-ng --bssid TARGET_BSSID -c CHANNEL wlan0mon -w capture
  aireplay-ng -3 -b TARGET_BSSID wlan0mon  ← Injection ARP
  aircrack-ng capture-01.cap
```

> ⚠️ **Rappel CEH** : **WEP est totalement cassé** et ne doit jamais être utilisé. La faille principale = réutilisation des IVs de 24 bits.

---

## 2. WPA — Wi-Fi Protected Access

```
Conçu comme solution intérimaire pendant le développement de WPA2

Chiffrement : TKIP (Temporal Key Integrity Protocol)
  → Clé de session par paquet (corrige IV réutilisation de WEP)
  → MIC (Michael Message Integrity Code) contre bit flipping

Authentification :
  PSK (Personal) : passphrase partagée
  Enterprise : 802.1X + serveur RADIUS

Vulnérabilités WPA TKIP :
  → MICHAEL MIC attack : Si 2 erreurs MIC en 60s → AP désactive TKIP
  → Beck-Tews attack (2008) : Déchiffrer/injecter des paquets courts
  → Encore vulnérable au handshake cracking (dictionnaire)

WPA est considéré obsolète, même si moins vulnérable que WEP.
```

---

## 3. WPA2 — Sécurité Actuelle

```
Chiffrement : AES-CCMP (Counter Mode CBC-MAC Protocol)
  → AES 128 bits → Chiffrement fort
  → Remplacement de RC4 et TKIP

Modes :
  WPA2-Personal (PSK) :
    → Pre-Shared Key (passphrase)
    → Vulnérable si passphrase faible (attaque dictionnaire)
    
  WPA2-Enterprise (802.1X) :
    → Authentification par certificat ou identifiants via RADIUS
    → Chaque utilisateur a ses propres credentials
    → Bien plus sécurisé que PSK

Vulnérabilités WPA2 :
  1. Handshake cracking (si PSK faible) :
     → Capturer le 4-way handshake lors d'une déauth
     → Brute force hors ligne avec dictionnaire (hashcat, aircrack-ng)
     
  2. PMKID Attack (2018, Jens Steube) :
     → Pas besoin de capturer un handshake !
     → Extraire le PMKID depuis le premier frame EAPOL
     → Brute force hors ligne
     
  3. KRACK (Key Reinstallation Attack, 2017) :
     → Forcer la réinstallation de la clé de session
     → Permet le déchiffrement et potentiellement l'injection
     → Patché sur la plupart des appareils modernes
```

---

## 4. WPA3 — Standard Moderne

```
Améliorations WPA3 :

1. SAE (Simultaneous Authentication of Equals) — "Dragonfly"
   → Remplace PSK
   → Résiste aux attaques dictionnaire (même avec une passphrase faible)
   → Forward secrecy : compromission d'une clé ne déchiffre pas le passé
   
2. OWE (Opportunistic Wireless Encryption)
   → Chiffrement des réseaux ouverts (sans authentification)
   → Remplace les hotspots non chiffrés
   
3. 192-bit security mode (Enterprise)
   → Pour les environnements haute sécurité
   
4. PMF (Protected Management Frames) obligatoire
   → Protège les trames de déauthentification contre le forgeage
   → Empêche les attaques de déauth !

Vulnérabilités WPA3 :
  Dragonblood (2019) : Plusieurs CVE contre SAE
  → Side-channel attacks sur l'implémentation
  → Patchées depuis
```

---

## 5. WPS — Wi-Fi Protected Setup

```
But : Faciliter la connexion des appareils (bouton WPS)
Mécanismes : PIN (8 chiffres), PBC (bouton), NFC, USB

VULNÉRABILITÉ CRITIQUE — PIN Brute Force :
  Le PIN WPS de 8 chiffres est divisé en deux moitiés de 4 :
  [1234][5678]
  
  L'AP vérifie les deux moitiés SÉPARÉMENT :
  → 10^4 + 10^4 = 20000 tentatives max (pas 10^8 !)
  
  Résultat : Crackable en quelques heures
  
  Reaver : outil dédié au brute force WPS PIN
  reaver -i wlan0mon -b TARGET_BSSID -vv
  
  Outils :
  → Reaver
  → Bully
  → wifite (automatisé)

Protection :
  → Désactiver WPS sur tous les routeurs
  → Certains routeurs modernes ont un rate limiting mais WPS reste risqué
```

---

## 6. 802.1X et EAP — Enterprise Wi-Fi

```
Architecture 802.1X :

[Supplicant]     [Authenticator]    [Authentication Server]
(Client Wi-Fi) → (AP / Switch)   →  (RADIUS Server)

1. Client tente de se connecter à l'AP
2. AP bloque tout sauf le trafic 802.1X (EAP)
3. AP relaie les messages EAP vers le serveur RADIUS
4. RADIUS valide les credentials/certificats
5. Si OK : AP déverrouille le port pour le client

Types EAP importants :
  LEAP (Cisco) : Vulnérable, mots de passe challengé-réponse
  PEAP         : Tunnel TLS + MSCHAPv2, très répandu
  EAP-TLS      : Certificats mutuels, le plus sécurisé
  EAP-TTLS     : Tunnel TLS pour autres méthodes
  EAP-FAST     : Remplacement LEAP par Cisco

Vulnérabilité LEAP :
  MSCHAPv2 dans le tunnel → Susceptible au dictionnaire
  asleap : outil de cracking LEAP
```

---

## 🧠 Points clés à retenir pour l'examen

| Protocole | Chiffrement | Vulnérabilité principale |
|-----------|-------------|--------------------------|
| WEP | RC4 + IV 24-bit | Réutilisation IV, crackable rapidement |
| WPA | TKIP | Handshake cracking dictionnaire |
| WPA2-Personal | AES-CCMP | Handshake cracking + PMKID |
| WPA2-Enterprise | AES-CCMP | Plus sécurisé, LEAP vulnérable |
| WPA3 | AES + SAE | Dragonblood (patché) |
| WPS | PIN 8 digits | Brute force PIN (20000 max) |

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Attaques Wi-Fi](./03_attaques_wifi.md)*
