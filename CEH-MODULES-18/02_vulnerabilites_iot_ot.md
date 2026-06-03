# 🔴 Module 18 — Vulnérabilités IoT & OT

> **CEH v13 | EC-Council**  
> `#OWASPIoT` `#DefaultCredentials` `#SCADA` `#InsecureUpdate` `#Modbus`

---

## OWASP IoT Top 10 (2018)

---

### I1 — Weak, Guessable, or Hardcoded Passwords

```
Exemples :
  admin/admin sur routeur Zyxel
  root/12345 sur caméra IP
  Credentials hardcodés dans le firmware (ichangeables !)
  
Impact : Accès complet à l'appareil, mouvement latéral
Contre-mesure : Changer obligatoirement les credentials par défaut
```

### I2 — Insecure Network Services

```
Exemples :
  Telnet actif (port 23) au lieu de SSH
  FTP non chiffré
  Services inutiles exposés (HTTP admin sur port 80)
  UPnP activé (ouverture automatique de ports)
  
Test :
  nmap -p- 192.168.1.1    ← Scanner tous les ports d'un appareil IoT
```

### I3 — Insecure Ecosystem Interfaces

```
API REST, interfaces web, apps mobile, services cloud non sécurisés

Exemples :
  Console web sans auth (ou auth faible)
  API sans chiffrement
  App mobile stockant le token en clair
```

### I4 — Lack of Secure Update Mechanism

```
Exemples :
  Pas de vérification de signature des mises à jour
  Mises à jour en HTTP (non chiffré)
  Pas de mécanisme de rollback
  
Risque : Attack supply chain / firmware malveillant
```

### I5 — Use of Insecure or Outdated Components

```
OpenSSL 1.0.x dans des routeurs et caméras fabriqués en 2015
Noyau Linux non patché (années de lag)
Bibliothèques tierces avec CVEs connues
```

### I6 — Insufficient Privacy Protection

```
Données personnelles collectées excessivement
Microphone/caméra activés sans indicateur
Données envoyées à des serveurs tiers sans consentement
```

### I7 — Insecure Data Transfer and Storage

```
Données en transit non chiffrées (HTTP au lieu de HTTPS)
Credentials stockés en clair dans la flash
Logs non chiffrés accessibles via JTAG
```

### I8 — Lack of Device Management

```
Pas de moyen de patcher à distance
Pas d'inventaire des appareils
Pas de monitoring de sécurité
Appareils zombies (abandonnés, non maintenus)
```

### I9 — Insecure Default Settings

```
Configuration par défaut permissive :
  Wi-Fi Password "12345678"
  Remote access activé par défaut
  Debug mode actif en production
```

### I10 — Lack of Physical Hardening

```
Accès JTAG non protégé → Extraction du firmware
Port série (UART) exposé → Shell root
Absence de tamper detection
Bootloader non verrouillé → Flash du firmware custom
```

---

## Vulnérabilités Spécifiques OT/SCADA

```
1. Protocols non sécurisés (Modbus, DNP3)
   → Pas d'authentification
   → Pas de chiffrement
   → N'importe qui sur le réseau peut envoyer des commandes
   
2. Legacy OS non patchés
   → Windows XP/7 encore très répandus en 2024
   → Impossible de patcher sans risquer l'arrêt de production
   
3. Air gap mythique
   → Beaucoup de systèmes OT "isolés" ont des connexions cachées
   → Clés USB (vecteur Stuxnet)
   → VPN de maintenance temporaire oublié
   
4. Engineering Workstations connectées à IT et OT
   → Pont entre les deux réseaux
   → Vecteur d'attaque classique
   
5. Protocoles de maintenance
   → Remote Desktop Protocol (RDP) ouvert
   → TeamViewer/VNC pour maintenance fournisseur
```

---

## Cas Réel : Stuxnet (2010)

```
Stuxnet = premier cyberweapon documenté ciblant l'OT

Cible : Centrifugeuses d'enrichissement d'uranium à Natanz (Iran)

Vecteur d'infection : Clé USB (air gap contourné !)
  → Exploitait 4 zero-days Windows
  → Se propageait silencieusement

Payload :
  → Ciblait SPÉCIFIQUEMENT les PLCs Siemens S7-315/S7-417
  → Modifiait subtilement la vitesse des centrifugeuses
  → Affichait des données normales à l'opérateur (rootkit SCADA)
  → ~1000 centrifugeuses détruites sur 6000

Impact : Premier exemple de cyberattaque causant des dommages physiques
```

---

## 🧠 Points clés à retenir pour l'examen

- **I1 (Default passwords)** = vulnérabilité IoT #1
- **Modbus** = aucune authentification ni chiffrement
- **Air gap** = souvent un mythe (clés USB, VPN oublié)
- **Stuxnet** = premier cyberweapon OT (centrifugeuses Iran, 2010)
- **OT priorité** = disponibilité (arrêt = conséquences physiques)
- **JTAG/UART** = accès physique pour extraire firmware

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques d'attaques](./03_techniques_attaques.md)*
