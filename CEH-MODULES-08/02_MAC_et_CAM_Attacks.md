# 🔀 02 — Attaques MAC & Table CAM

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 2/8 — MAC Flooding, Switch Port Stealing, CAM Table

---

## 🧩 La Table CAM — Content Addressable Memory

> La **table CAM** (ou table MAC) est la mémoire d'un switch qui associe chaque **adresse MAC** à un **port physique**. C'est grâce à elle que le switch envoie les trames uniquement au bon destinataire.

```
Table CAM d'un switch (exemple)
┌──────────────────────┬──────────┬────────┐
│   Adresse MAC        │  Port    │  VLAN  │
├──────────────────────┼──────────┼────────┤
│ AA:BB:CC:DD:EE:01    │  Fa0/1   │   1    │  ← PC-Alice
│ AA:BB:CC:DD:EE:02    │  Fa0/2   │   1    │  ← PC-Bob
│ AA:BB:CC:DD:EE:03    │  Fa0/3   │   1    │  ← Serveur
│ AA:BB:CC:DD:EE:FF    │  Fa0/5   │   1    │  ← Attaquant
└──────────────────────┴──────────┴────────┘
```

**Fonctionnement :**
1. Switch reçoit une trame → lit l'adresse MAC source → enregistre dans la table CAM
2. Switch cherche l'adresse MAC destination → envoie sur le port correspondant
3. Si MAC destination inconnue → **flooding** (broadcast sur tous les ports)

> 💡 La table CAM a une **taille limitée** et un **timer d'expiration** (défaut : 300 secondes).

---

## 💥 MAC Flooding

### Principe

> Saturer la table CAM avec des milliers de fausses entrées MAC/IP jusqu'à ce qu'elle soit **pleine**. Le switch, incapable de gérer de nouvelles entrées, se comporte alors comme un **hub** et diffuse toutes les trames sur tous les ports.

```
ÉTAT NORMAL (switch)
Attaquant → ne voit que son propre trafic
PC-A ←→ Serveur : trafic unicast, invisible pour attaquant

APRÈS MAC FLOODING
Table CAM = PLEINE (fausses MACs)
Switch → mode hub → broadcast sur TOUS les ports
Attaquant → voit TOUT le trafic du réseau ✅
```

### Étapes de l'attaque

```
[1] Attaquant active le mode promiscuous sur sa NIC

[2] Lance macof pour générer des milliers de MACs aléatoires
    macof -i eth0

[3] Table CAM du switch sature (~quelques secondes)

[4] Switch bascule en mode "fail-open" (hub)

[5] Attaquant capture tout le trafic avec Wireshark/tcpdump
```

### Outil : macof

```bash
# Syntaxe de base
macof -i eth0

# Avec cible spécifique
macof -i eth0 -d 192.168.1.1

# Spécifier le nombre de paquets
macof -i eth0 -n 10000
```

> `macof` fait partie du package **dsniff** (Linux).  
> Il génère des trames Ethernet avec des adresses MAC et IP **aléatoires** à très haute vitesse.

---

## 🔄 Switch Port Stealing

### Principe

> L'attaquant envoie des trames **ARP avec l'adresse MAC de la victime** en source. Le switch met à jour sa table CAM et redirige le trafic destiné à la victime vers le port de l'attaquant.

```
Situation initiale :
Table CAM : MAC-Victime → Port 2

Attaque Switch Port Stealing :
Attaquant envoie : [src: MAC-Victime | dst: Broadcast]
Switch met à jour : MAC-Victime → Port 5 (attaquant !)

Résultat :
Trafic destiné à Victime → envoyé à Attaquant
```

### Différence avec MAC Flooding

| Technique | Mécanisme | Discrétion |
|-----------|-----------|-----------|
| **MAC Flooding** | Inonde la CAM → switch devient hub | Bruyant |
| **Switch Port Stealing** | Hijack une entrée précise de la CAM | Plus ciblé |

---

## 🎭 MAC Spoofing / MAC Duplication

### Principe

> Changer son adresse MAC pour **usurper l'identité** d'un autre appareil sur le réseau.

```
Attaquant scanne le réseau → identifie MAC active (ex: MAC-Victime)

Attaquant change sa propre MAC :
  ip link set eth0 address AA:BB:CC:DD:EE:02

Switch met à jour la CAM :
  MAC-Victime → Port-Attaquant

Résultat : Trafic de/vers Victime intercepté
```

**Cas d'usage :**
- Contourner les **filtres MAC** sur les points d'accès Wi-Fi
- Intercepter le trafic d'un utilisateur spécifique
- Usurpation d'identité sur le réseau

### Commandes MAC Spoofing

```bash
# Linux - méthode ip
ip link set eth0 down
ip link set eth0 address AA:BB:CC:DD:EE:02
ip link set eth0 up

# Linux - méthode ifconfig
ifconfig eth0 down
ifconfig eth0 hw ether AA:BB:CC:DD:EE:02
ifconfig eth0 up

# Vérification
ip link show eth0
```

---

## 🛡️ Contre-mesures

### Port Security (Cisco IOS)

> Limite le nombre d'adresses MAC autorisées par port et définit une action en cas de violation.

```cisco
! Activer port security sur une interface
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 2
Switch(config-if)# switchport port-security violation shutdown
Switch(config-if)# switchport port-security mac-address sticky

! Vérification
Switch# show port-security interface FastEthernet0/1
Switch# show port-security address
```

**Modes de violation :**

| Mode | Action |
|------|--------|
| `shutdown` | Désactive le port (err-disabled) |
| `restrict` | Bloque les trames non autorisées + log |
| `protect` | Bloque silencieusement (pas de log) |

---

### Résumé des contre-mesures MAC

```
DÉFENSE CONTRE MAC FLOODING
├── Port Security → limite le nombre de MACs par port
├── 802.1X → authentification avant accès au réseau
└── IDS/SIEM → détecter une explosion de requêtes ARP

DÉFENSE CONTRE MAC SPOOFING
├── DHCP Snooping Binding Table → valide IP/MAC/Port
├── Dynamic ARP Inspection (DAI) → valide les paquets ARP
├── IP Source Guard → vérifie IP source contre binding table
└── 802.1X → auth par certificat, pas par MAC
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **MAC Flooding** : le switch sature et bascule en mode **hub** (broadcast)
- Outil MAC Flooding : **`macof`** (package dsniff)
- La table CAM a une **taille fixe** → vulnérabilité fondamentale
- **Port Security** = contre-mesure principale contre MAC Flooding
- **MAC Spoofing** = changer sa MAC pour se faire passer pour quelqu'un d'autre
- **Switch Port Stealing** = basé sur MAC flooding pour voler un port spécifique
- La table CAM associe MAC → Port physique → VLAN

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [01 — Concepts fondamentaux](./01_Concepts_Fondamentaux.md) | [03 — DHCP Attacks](./03_DHCP_Attacks.md) |
