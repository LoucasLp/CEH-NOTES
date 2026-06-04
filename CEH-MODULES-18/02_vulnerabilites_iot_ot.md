# 🔴 Module 18 — Vulnérabilités IoT & OT

> **CEH v13 | EC-Council**  
> `#OWASPIoT` `#DefaultCredentials` `#SCADA` `#InsecureUpdate` `#Modbus`

---

## 📌 Table des matières

1. [OWASP IoT Top 10 (2018)](#1-owasp-iot-top-10-2018)
2. [Vulnérabilités Spécifiques OT/SCADA](#2-vulnérabilités-spécifiques-otscada)
3. [Cas Réel : Stuxnet (2010)](#3-cas-réel--stuxnet-2010)

---

## 1. OWASP IoT Top 10 (2018)

### I1 — Weak, Guessable, or Hardcoded Passwords

```
Exemples :
  admin/admin sur routeur Zyxel
  root/12345 sur caméra IP
  Credentials hardcodés dans le firmware (inchangeables !)

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

Risque : Attack supply chain / firmware malveillant injecté via MITM
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
Microphone/caméra activés sans indicateur visible
Données envoyées à des serveurs tiers sans consentement clair
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
Appareils zombies (abandonnés, non maintenus par le fabricant)
```

### I9 — Insecure Default Settings

```
Configuration par défaut permissive :
  Wi-Fi Password "12345678"
  Remote access activé par défaut
  Debug mode actif en production
  Services d'admin exposés sur toutes les interfaces
```

### I10 — Lack of Physical Hardening

```
Accès JTAG non protégé → Extraction du firmware
Port série (UART) exposé → Shell root sans mot de passe
Absence de tamper detection
Bootloader non verrouillé → Flash du firmware custom
```

> 💡 **Question type exam** : *Quelle est la vulnérabilité IoT la plus répandue selon OWASP ?* → **I1 : Credentials faibles ou par défaut**

---

## 2. Vulnérabilités Spécifiques OT/SCADA

```
1. Protocoles non sécurisés (Modbus, DNP3)
   → Pas d'authentification
   → Pas de chiffrement
   → N'importe qui sur le réseau peut envoyer des commandes

2. Legacy OS non patchés
   → Windows XP/7 encore très répandus en 2024
   → Impossible de patcher sans risquer l'arrêt de production

3. Air gap mythique
   → Beaucoup de systèmes OT "isolés" ont des connexions cachées
   → Clés USB (vecteur Stuxnet)
   → VPN de maintenance temporaire oublié ouvert
   → Laptops de maintenance passant des deux côtés

4. Engineering Workstations connectées à IT et OT
   → Pont entre les deux réseaux
   → Vecteur d'attaque classique (pivot)

5. Protocoles de maintenance exposés
   → Remote Desktop Protocol (RDP) ouvert sur le réseau OT
   → TeamViewer/VNC pour maintenance fournisseur (non supprimé)
   → Accès direct aux PLCs depuis Internet via port forwarding
```

### Tableau des vulnérabilités OT

| Vulnérabilité | Impact potentiel | Exemple réel |
|---------------|-----------------|--------------|
| **Modbus sans auth** | Envoi de commandes directes aux PLCs | Stuxnet |
| **Windows XP non patché** | Exploitation de vulnérabilités connues (EternalBlue) | Ukraine power grid 2015 |
| **Air gap contourné** | Infection du réseau isolé | Stuxnet (clé USB) |
| **Credentials par défaut** | Accès admin au SCADA | Multiples incidents |\
| **RDP ouvert** | Accès distant non autorisé | Colonial Pipeline 2021 |

---

## 3. Cas Réel : Stuxnet (2010)

```
Stuxnet = premier cyberweapon documenté ciblant l'OT

Cible : Centrifugeuses d'enrichissement d'uranium à Natanz (Iran)

Vecteur d'infection : Clé USB (air gap contourné !)
  → Exploitait 4 zero-days Windows simultanément
  → Se propageait silencieusement via LNK files

Payload :
  → Ciblait SPÉCIFIQUEMENT les PLCs Siemens S7-315/S7-417
  → Modifiait subtilement la vitesse de rotation des centrifugeuses
  → Affichait des données normales à l'opérateur (rootkit SCADA)
  → ~1000 centrifugeuses détruites sur 6000 en fonctionnement

Impact : Premier exemple documenté de cyberattaque causant des dommages physiques
Attribué à : USA + Israël (NSA + Unit 8200) — jamais confirmé officiellement
```

> ⚠️ **Rappel CEH** : Stuxnet prouve que l'air gap n'est pas une protection suffisante si des supports physiques (clés USB) ne sont pas contrôlés.

---

## 🧠 Points clés à retenir pour l'examen

- **I1 (Default passwords)** = vulnérabilité IoT #1 selon OWASP
- **Modbus** = aucune authentification ni chiffrement (port TCP 502)
- **Air gap** = souvent un mythe (clés USB, VPN oublié, laptops de maintenance)
- **Stuxnet** = premier cyberweapon OT (centrifugeuses Iran, 2010, 4 zero-days Windows)
- **OT priorité** = disponibilité (arrêt = conséquences physiques graves)
- **JTAG/UART** = accès physique permettant d'extraire ou modifier le firmware
- **I4** = mises à jour non sécurisées → attaque supply chain possible

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques d'attaques](./03_techniques_attaques.md)*
