# 🛡️ 07 — Détection & Contre-mesures

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 7/8 — Détecter les sniffers, contre-mesures réseau et chiffrement

---

## 🔍 PARTIE 1 : DÉTECTER UN SNIFFER SUR LE RÉSEAU

> Détecter une machine en **mode promiscuous** est difficile car le sniffing passif est par nature invisible. Cependant, plusieurs méthodes permettent de le repérer.

---

### Méthode 1 : Ping Method

> Si une machine est en mode promiscuous, sa pile réseau traite **tous les paquets**, même ceux non destinés à elle. En envoyant un ping avec une **mauvaise adresse MAC** mais une **IP correcte**, seule une machine en mode promiscuous répondra.

```
Test mode promiscuous via Ping

Mécanisme :
├── Ping normal : paquet dst MAC = MAC-Cible → répondu par cible
├── Ping test :   paquet dst MAC = FAUSSE MAC mais IP = IP-Cible
│
│   Machine normale : NIC rejette la trame (fausse MAC) → pas de réponse
│   Machine promiscuous : NIC accepte TOUTES les trames → répond au ping !

Conclusion : Si réponse reçue → NIC en mode promiscuous → sniffer probable
```

```bash
# Test manuel (Linux)
ping -c 1 192.168.1.10   # Ping normal pour confirmer connectivité

# Envoyer avec fausse MAC - utiliser Scapy :
python3 -c "
from scapy.all import *
p = Ether(dst='AA:BB:CC:00:00:01')/IP(dst='192.168.1.10')/ICMP()
resp = srp1(p, timeout=2)
if resp: print('PROMISCUOUS DETECTED!')
"
```

---

### Méthode 2 : ARP Method

> Envoyer un **paquet ARP non-broadcast** (unicast ARP) avec une adresse MAC de destination invalide. Une machine en mode promiscuous mettra quand même l'IP en cache (car elle a reçu et traité la trame).

```
Test ARP promiscuous :

[1] Envoyer ARP Request avec MAC dst invalide vers la cible suspectée

[2] Envoyer un ping à la cible (requête ARP normale)

[3] Observer :
    - Machine normale : pas de cache ARP → demande ARP classique
    - Machine promiscuous : IP déjà en cache (traité la trame invalide)
      → répond directement au ping sans ARP supplémentaire
```

---

### Méthode 3 : DNS Method

> Les sniffers effectuent souvent des **résolutions DNS inverses** (reverse DNS) pour chaque IP capturée — ce qui génère des requêtes DNS inhabituelles.

```
Indicateur :
Machine suspecte fait beaucoup de requêtes PTR (reverse DNS)
→ Signe qu'elle analyse des IPs capturées → sniffer probable

Surveillance :
- Monitorer les requêtes DNS PTR sur le réseau
- Alertes SIEM sur volume anormal de requêtes PTR depuis une IP
```

---

### Outils de détection de sniffers

| Outil | Plateforme | Description |
|-------|-----------|-------------|
| **Promqry** | Windows | Détecte interfaces en mode promiscuous |
| **PromiscDetect** | Windows | Détection mode promiscuous |
| **Sniff-em** | Windows | Détecte sniffers sur le réseau |
| **AntiSniff** | Linux/Windows | Détecte sniffers via méthodes DNS/ARP/Ping |
| **ARPWatch** | Linux | Surveille les changements ARP → détecte ARP spoofing |

```bash
# ARPWatch - surveille le cache ARP et alerte sur les changements
arpwatch -i eth0 -m admin@example.com

# Vérifier les interfaces en mode promiscuous localement (Linux)
ip link show | grep PROMISC
ifconfig | grep PROMISC
```

---

### Indicateurs d'un sniffer actif (réseau)

```
Signes d'activité de sniffing / MITM sur le réseau

Réseau
├── Latence anormalement élevée entre deux hôtes
├── Trafic réseau dupliqué inexpliqué
├── Changements fréquents dans le cache ARP
│    → arp -a : 192.168.1.1 change de MAC = ARP spoofing
├── Volume élevé de requêtes ARP
└── Requêtes DNS inverses (PTR) inhabituelles

Système
├── Interface réseau en mode PROMISC
│    → ip link show | grep PROMISC
├── Processus inattendus (wireshark, tcpdump, ettercap)
└── Fichiers .pcap récents dans /tmp ou répertoires suspects
```

---

## 🛡️ PARTIE 2 : CONTRE-MESURES GLOBALES

### Architecture réseau sécurisée

```
SÉCURISATION DU RÉSEAU CONTRE LE SNIFFING

Couche physique
├── Contrôle d'accès physique aux locaux et baies réseau
├── Utiliser des câbles dédiés (pas de hubs partagés)
└── Câbles blindés (limitation des écoutes physiques)

Couche 2 (Switch)
├── Port Security         → limite MACs par port
├── DHCP Snooping         → valide serveurs DHCP
├── Dynamic ARP Inspection→ valide réponses ARP
├── IP Source Guard       → valide IP source vs binding table
├── 802.1X                → authentification port réseau
└── Storm Control         → limite broadcast/multicast

Couche 3+ (Réseau)
├── Segmentation VLAN     → isoler les segments sensibles
├── ACL strictes          → limiter les communications
├── IPSec                 → chiffrement au niveau IP
└── VPN                   → tunnels chiffrés
```

---

### Tableau des contre-mesures par attaque

```
┌────────────────────┬──────────────────────────────────────────┐
│  Attaque           │  Contre-mesures                          │
├────────────────────┼──────────────────────────────────────────┤
│ Sniffing passif    │ Chiffrement (TLS/SSH/VPN), Switch (≠ Hub)│
│ MAC Flooding       │ Port Security, 802.1X                    │
│ DHCP Starvation    │ DHCP Snooping, Port Security             │
│ Rogue DHCP         │ DHCP Snooping (trusted/untrusted ports)  │
│ ARP Poisoning      │ DAI + DHCP Snooping, IP Source Guard     │
│ DNS Poisoning      │ DNSSEC, DoH/DoT, filtrage DNS            │
│ SSL Stripping      │ HSTS, HSTS Preload, TLS partout          │
│ VLAN Hopping       │ Désactiver DTP, changer VLAN natif       │
│ STP Attack         │ BPDU Guard, Root Guard                   │
└────────────────────┴──────────────────────────────────────────┘
```

---

### Chiffrement — La contre-mesure universelle

> Même si un attaquant capture le trafic, le **chiffrement** rend les données illisibles.

```
PROTOCOLES CLEARTEXT → REMPLACER PAR
──────────────────────────────────────────────
HTTP  (80)  → HTTPS / TLS  (443)
FTP   (21)  → SFTP         (22) ou FTPS (990)
Telnet(23)  → SSH          (22)
SMTP  (25)  → SMTPS        (465/587 + STARTTLS)
POP3  (110) → POP3S        (995)
IMAP  (143) → IMAPS        (993)
SNMP  (161) → SNMPv3       (161 avec AuthPriv)
LDAP  (389) → LDAPS        (636)
DNS   (53)  → DoH / DoT    (443 / 853)
```

---

### Dynamic ARP Inspection (DAI) — Configuration complète

```cisco
! Prérequis : DHCP Snooping doit être activé
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 1

! Activer DAI sur les VLANs
Switch(config)# ip arp inspection vlan 1

! Définir les ports trusted (vers routeur/serveur DHCP)
Switch(config)# interface GigabitEthernet0/1
Switch(config-if)# ip arp inspection trust

! Optionnel : ARP Rate Limiting sur ports untrusted
Switch(config-if)# ip arp inspection limit rate 100

! Vérification
Switch# show ip arp inspection
Switch# show ip arp inspection statistics vlan 1
Switch# show ip arp inspection interfaces
```

---

### IP Source Guard

> Valide que l'adresse IP source de chaque paquet correspond à l'entrée de la DHCP Snooping Binding Table. Empêche le spoofing IP.

```cisco
! Activer IP Source Guard sur port untrusted
Switch(config-if)# ip verify source

! Avec validation IP et MAC
Switch(config-if)# ip verify source port-security

! Vérification
Switch# show ip verify source
```

---

### 802.1X — Authentification Port-Based

> Standard IEEE qui **authentifie les équipements** avant de leur accorder l'accès au réseau. Même si un attaquant se branche physiquement, il doit s'authentifier.

```
Architecture 802.1X

Supplicant          Authenticator       Authentication Server
(Client)            (Switch)            (RADIUS)
    │                    │                    │
    │── EAP Request ─────┤                    │
    │                    │── RADIUS Access ───┤
    │                    │   Request           │
    │                    │←─ RADIUS Challenge ─┤
    │←── EAP Challenge ──┤                    │
    │── EAP Response ────┤                    │
    │                    │── RADIUS Response ─┤
    │                    │←─ RADIUS Accept ───┤
    │                    │   (ou Reject)       │
    │←── EAP Success ────┤                    │
    │                    │
    │  PORT OUVERT       │
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **Ping method** = détecter mode promiscuous via fausse MAC + ping IP
- **ARP method** = détecter via unicast ARP non-broadcast
- **ARPWatch** = surveille les changements ARP → détecte ARP spoofing
- **DAI** nécessite **DHCP Snooping** pour fonctionner (utilise la binding table)
- **IP Source Guard** = valide IP source vs DHCP Snooping table
- **802.1X** = authentification port avant accès réseau (supplicant/authenticator/RADIUS)
- Le meilleur moyen contre le sniffing = **chiffrement de bout en bout** (TLS, VPN, SSH)
- **HSTS** = contre-mesure SSL stripping (force HTTPS)

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [06 — Outils de Sniffing](./06_Outils_de_Sniffing.md) | [08 — Quiz & Révision](./08_Quiz_et_Revision.md) |
