# 📦 03 — Attaques DHCP

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 3/8 — DHCP Starvation, Rogue DHCP Server

---

## 🔑 Rappel — Comment fonctionne DHCP ?

> **DHCP** (Dynamic Host Configuration Protocol) attribue automatiquement des adresses IP et paramètres réseau aux clients.

### Processus DORA (IPv4)

```
CLIENT                          SERVEUR DHCP
  │                                  │
  │──── DHCP DISCOVER ─────────────→│  [1] Broadcast : "Qui peut me donner une IP ?"
  │                                  │
  │←─── DHCP OFFER ─────────────────│  [2] "Je t'offre 192.168.1.100 pour 24h"
  │                                  │
  │──── DHCP REQUEST ───────────────→│  [3] "J'accepte cette IP"
  │                                  │
  │←─── DHCP ACK ───────────────────│  [4] "OK, elle est à toi !"
  │                                  │

Ports : UDP 67 (serveur) | UDP 68 (client)
```

### Processus SARR (IPv6)

```
CLIENT                          SERVEUR DHCP
  │──── SOLICIT ────────────────────→│
  │←─── ADVERTISE ──────────────────│
  │──── REQUEST ────────────────────→│
  │←─── REPLY ──────────────────────│

Ports : UDP 546 (client) | UDP 547 (serveur)
```

**Paramètres fournis par DHCP :**
- Adresse IP + masque de sous-réseau
- Passerelle par défaut (Default Gateway)
- Serveur(s) DNS
- Durée du bail (Lease Time)

---

## 💣 Attaque 1 : DHCP Starvation

### Principe

> L'attaquant envoie des milliers de **requêtes DHCP DISCOVER** avec des adresses MAC **falsifiées** pour **épuiser le pool d'adresses IP** du serveur DHCP. Le serveur ne peut plus attribuer d'IP aux clients légitimes → **Déni de service**.

```
DHCP Pool : 192.168.1.100 → 192.168.1.200 (100 adresses)

Attaquant (Yersinia/dhcpstarv)
│
├── DISCOVER [MAC: AA:BB:CC:11:22:01] → ACK → IP .100 attribuée
├── DISCOVER [MAC: AA:BB:CC:11:22:02] → ACK → IP .101 attribuée
├── DISCOVER [MAC: AA:BB:CC:11:22:03] → ACK → IP .102 attribuée
│   ... (100 requêtes)
└── Pool ÉPUISÉ

Nouveau client légitime :
└── DISCOVER → SERVEUR : "Désolé, plus d'adresses disponibles !" ❌
```

### Impact

```
Conséquences d'un DHCP Starvation
├── Déni de service (nouveaux clients ne peuvent pas se connecter)
├── Clients existants perdent leur bail à expiration
├── Souvent utilisé EN COMBINAISON avec Rogue DHCP
└── Peut cibler une plage horaire précise (maintenance, réunion...)
```

### Outils

```bash
# Yersinia (interface texte)
yersinia -I
# → Sélectionner DHCP → Send DISCOVER packet

# Yersinia (ligne de commande)
yersinia dhcp -attack 1 -interface eth0

# dhcpstarv
dhcpstarv -i eth0

# Metasploit module
use auxiliary/scanner/dhcp/dhcp_exhaustion
```

---

## 👹 Attaque 2 : Rogue DHCP Server

### Principe

> L'attaquant déploie un **faux serveur DHCP** sur le réseau. Il répond aux requêtes DISCOVER des clients **avant le vrai serveur** et leur fournit de **faux paramètres réseau** (fausse passerelle, faux DNS) pour rediriger tout leur trafic.

```
Scénario combiné : Starvation + Rogue DHCP

PHASE 1 : DHCP Starvation
└── Épuiser le pool du vrai serveur DHCP

PHASE 2 : Rogue DHCP
└── Déployer un faux serveur DHCP
    ├── Fausse passerelle = IP attaquant → MITM
    ├── Faux DNS = IP attaquant → DNS Spoofing
    └── Clients légitimes se connectent via l'attaquant
```

### Flux de l'attaque Rogue DHCP

```
CLIENT              ROGUE DHCP (attaquant)      VRAI DHCP
  │                         │                       │
  │── DISCOVER (broadcast) ─┼───────────────────────┤
  │                         │                       │
  │←── OFFER (faux params)──│    (répond le 1er)     │
  │    Gateway: IP-attaquant│                       │
  │    DNS: IP-attaquant    │                       │
  │                         │                       │
  │── REQUEST ──────────────┤                       │
  │                         │                       │
  │←── ACK ─────────────────│                       │
  │                         │                       │
  Tout le trafic client passe par l'attaquant → interception !
```

### Paramètres malveillants fournis

| Paramètre | Valeur légitime | Valeur Rogue |
|-----------|-----------------|--------------|
| Default Gateway | 192.168.1.1 | 192.168.1.X (attaquant) |
| DNS Server | 8.8.8.8 | 192.168.1.X (attaquant) |
| Lease Time | 24h | Très courte (oblige renouvellement) |

---

## 🛡️ Contre-mesures DHCP

### DHCP Snooping (Cisco)

> Fonctionnalité switch qui distingue les **ports de confiance** (trusted = côté serveur DHCP légitime) des **ports non fiables** (untrusted = côté clients). Les réponses DHCP ne sont acceptées que depuis les ports trusted.

```
Architecture DHCP Snooping

SERVEUR DHCP ──── PORT TRUSTED ──── SWITCH ──── PORT UNTRUSTED ──── CLIENTS
                                       │
                              DHCP Snooping activé
                              ├── Ports trusted : accepte OFFER, ACK
                              └── Ports untrusted : refuse OFFER, ACK
                                  (Rogue DHCP bloqué !)
```

```cisco
! Activer DHCP Snooping globalement
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 1,10,20

! Définir port trusted (vers le vrai serveur DHCP)
Switch(config-if)# ip dhcp snooping trust

! Les autres ports sont untrusted par défaut
! Vérification
Switch# show ip dhcp snooping
Switch# show ip dhcp snooping binding
```

**DHCP Snooping Binding Table :**

```
Adresse MAC        | IP            | Bail   | VLAN | Interface
AA:BB:CC:DD:EE:01  | 192.168.1.100 | 86400s |  1   | Fa0/1
AA:BB:CC:DD:EE:02  | 192.168.1.101 | 86400s |  1   | Fa0/2
```

> Cette table est ensuite utilisée par **DAI** et **IP Source Guard**.

---

### Port Security

```cisco
! Limiter les MACs par port → réduire starvation
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation restrict
```

---

### Résumé des contre-mesures

```
CONTRE DHCP STARVATION
├── Port Security → limite le nombre de MACs par port
├── DHCP Rate Limiting → limite le nb de requêtes/seconde par port
└── 802.1X → authentification avant accès réseau

CONTRE ROGUE DHCP SERVER
├── DHCP Snooping → seuls les ports trusted acceptent les OFFER/ACK
├── Surveillance réseau → détecter plusieurs serveurs DHCP
└── Alertes SIEM → nouvelles réponses DHCP depuis IP inconnues
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **DHCP Starvation** = DoS en épuisant le pool d'IP avec de fausses MACs
- Outil DHCP Starvation : **Yersinia**, **dhcpstarv**
- Ports DHCP : **UDP 67** (serveur) et **UDP 68** (client)
- **Rogue DHCP** = faux serveur qui fournit fausse gateway/DNS → MITM
- **DHCP Snooping** = contre-mesure principale → ports trusted vs untrusted
- Starvation + Rogue DHCP = combinaison classique pour MITM
- Le processus DHCP IPv4 = **DORA** (Discover, Offer, Request, Acknowledge)

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [02 — MAC & CAM Attacks](./02_MAC_et_CAM_Attacks.md) | [04 — ARP Poisoning](./04_ARP_Poisoning.md) |
