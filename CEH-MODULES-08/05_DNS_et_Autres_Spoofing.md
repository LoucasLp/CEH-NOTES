# 🕸️ 05 — DNS Poisoning & Autres Spoofing Avancés

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 5/8 — DNS Cache Poisoning, IRDP Spoofing, VLAN Hopping, STP

---

## 🌐 PARTIE 1 : DNS POISONING

### Rappel — Fonctionnement DNS

```
Client                  Resolver DNS            Root / TLD
  │                         │                       │
  │── Requête : google.com ─┤                       │
  │                         │── Query → Root DNS ───┤
  │                         │── Query → .com TLD ───┤
  │                         │── Query → Google NS ──┤
  │                         │←─ Réponse : 142.250.x.x
  │←── 142.250.x.x ─────────│
  │                         │
  [Cache DNS mis à jour : google.com = 142.250.x.x]
```

> ⚠️ **Vulnérabilité** : Le DNS cache les réponses pour une durée (TTL). Si on peut **injecter une fausse réponse** dans ce cache, les victimes seront redirigées vers un site malveillant.

---

### DNS Cache Poisoning (Kaminsky Attack)

> Injecter une **fausse entrée DNS** dans le cache d'un résolveur DNS pour rediriger les utilisateurs vers des adresses IP malveillantes.

```
ATTAQUE DNS CACHE POISONING
─────────────────────────────────────────
[1] Client demande : "Quelle est l'IP de banque.fr ?"

[2] Résolveur DNS n'a pas banque.fr en cache
    → Envoie requête au serveur DNS autoritaire

[3] Attaquant (sur le réseau ou avec accès) répond en PREMIER
    avec une fausse IP : banque.fr = IP-Attaquant

[4] Résolveur met en cache : banque.fr = IP-Attaquant (TTL hours/days)

[5] TOUS les clients utilisant ce résolveur
    → Redirigés vers le site de l'attaquant
```

---

### Techniques de DNS Spoofing

| Technique | Description |
|-----------|-------------|
| **Intranet DNS Spoofing** | Via ARP poisoning sur LAN → intercepte requêtes DNS et répond avec fausses IPs |
| **Internet DNS Poisoning** | Injection de fausses réponses dans le cache DNS récursif public |
| **Proxy Server DNS Poisoning** | Modification des paramètres DNS sur le proxy → tout le trafic redirigé |
| **DNS Cache Poisoning** | Exploitation de failles dans le serveur DNS (Kaminsky, 2008) |

---

### DNS Spoofing avec Ettercap

```bash
# 1. Modifier le fichier etter.dns pour définir les redirections
nano /etc/ettercap/etter.dns

# Ajouter les entrées à spoofer :
# *.google.com   A   192.168.1.99   (IP attaquant)
# banque.fr      A   192.168.1.99

# 2. Lancer Ettercap avec le plugin dns_spoof
ettercap -T -q -M arp:remote /192.168.1.10/ /192.168.1.1/ -P dns_spoof

# Les victimes qui naviguent vers google.com
# seront redirigées vers 192.168.1.99
```

---

### Outil dnsspoof

```bash
# Créer un fichier de hosts malveillants
echo "192.168.1.99 www.banque.fr" > /tmp/fakehosts

# Lancer dnsspoof (après ARP poisoning)
dnsspoof -i eth0 -f /tmp/fakehosts
```

---

### Contre-mesures DNS Poisoning

```
DÉFENSE CONTRE DNS POISONING
│
├── DNSSEC → Signatures cryptographiques sur les réponses DNS
│    └─ Valide l'authenticité des réponses
│
├── DNS over HTTPS (DoH) → Chiffre les requêtes DNS
├── DNS over TLS (DoT)   → Chiffre les requêtes DNS
│
├── Randomisation des ports sources (anti-Kaminsky)
│    └─ Rend plus difficile de deviner le port pour injecter
│
├── Filtrer requêtes DNS externes sur le réseau interne
├── Limiter la récursivité DNS (serveurs publics)
└── Monitoring DNS → détecter réponses inattendues
```

---

## 🔁 PARTIE 2 : IRDP SPOOFING

> **IRDP** (ICMP Router Discovery Protocol) permet aux hôtes de découvrir automatiquement les routeurs sur leur réseau.

### Attaque

```
Attaquant envoie des messages IRDP Router Advertisement
avec sa propre IP comme meilleure passerelle (priorité max)

Hôtes mettent à jour leur route par défaut
→ Trafic redirigé vers l'attaquant → MITM
```

> ⚠️ **Rare dans les environnements modernes** (IRDP désactivé par défaut sur la plupart des OS modernes).

---

## 🏷️ PARTIE 3 : VLAN HOPPING

> Technique permettant à un attaquant de **sauter d'un VLAN à un autre** sans autorisation, en exploitant les protocoles de trunking.

### Double Tagging Attack

```
RÉSEAU NORMAL
VLAN 10 : Comptabilité   (isolé)
VLAN 20 : Direction      (isolé)
VLAN 1  : Attaquant (VLAN natif du trunk)

DOUBLE TAGGING ATTACK
Attaquant envoie une trame avec 2 tags 802.1Q :
  - Tag externe : VLAN 1 (natif → retiré par Switch 1)
  - Tag interne : VLAN 10 (lu par Switch 2 → livrée dans VLAN 10 !)
```

```
Trame malveillante :
[VLAN 1][VLAN 10][Payload]
    ↓ Switch 1 retire le tag VLAN 1 (natif)
[VLAN 10][Payload]
    ↓ Switch 2 route vers VLAN 10
Payload livré dans VLAN Comptabilité !
```

### Switch Spoofing Attack

```
Attaquant se fait passer pour un switch trunk
→ Négocie un trunk DTP avec le switch cible
→ Accès à TOUS les VLANs du trunk
```

### Contre-mesures VLAN Hopping

```cisco
! Désactiver DTP (Dynamic Trunking Protocol) sur les ports access
Switch(config-if)# switchport mode access
Switch(config-if)# switchport nonegotiate

! Changer le VLAN natif (ne pas utiliser VLAN 1)
Switch(config-if)# switchport trunk native vlan 999

! Désactiver les ports inutilisés
Switch(config-if)# shutdown
Switch(config-if)# switchport access vlan 999   (VLAN blackhole)
```

---

## 🌳 PARTIE 4 : STP ATTACKS (Spanning Tree Protocol)

> **STP** empêche les boucles réseau en calculant un **arbre de spanning** (chemins sans boucle). Il élit un **Root Bridge** en fonction du **Bridge ID** (priorité + MAC).

### STP Attack — Root Bridge Takeover

```
Réseau normal :
Root Bridge = Switch principal (priorité 4096)

Attaque :
Attaquant envoie BPDU avec priorité = 0 (maximum)
→ Switch élit l'attaquant comme nouveau Root Bridge
→ Topologie réseau recalculée
→ Certains liens passent par l'attaquant → sniffing

Outil : Yersinia (attaque STP)
```

### Contre-mesures STP

```cisco
! BPDU Guard - rejette les BPDUs sur les ports access
Switch(config-if)# spanning-tree bpduguard enable

! Root Guard - empêche un port de devenir Root Bridge
Switch(config-if)# spanning-tree guard root

! Portfast - pour les ports terminaux (pas de STP delay)
Switch(config-if)# spanning-tree portfast
```

---

## 📋 Récapitulatif des attaques de couche 2

```
┌─────────────────────────────────────────────────────────────────┐
│            ATTAQUES COUCHE 2 — RÉSUMÉ                          │
├──────────────────┬─────────────────────┬───────────────────────┤
│  Attaque         │  Cible              │  Contre-mesure        │
├──────────────────┼─────────────────────┼───────────────────────┤
│ MAC Flooding     │ Table CAM switch    │ Port Security         │
│ Switch Port Steal│ Entrée CAM spécifique│ Port Security        │
│ MAC Spoofing     │ Identité réseau     │ DAI + DHCP Snooping   │
│ DHCP Starvation  │ Pool DHCP           │ DHCP Snooping         │
│ Rogue DHCP       │ Paramètres clients  │ DHCP Snooping         │
│ ARP Poisoning    │ Cache ARP           │ DAI + ARP statique    │
│ DNS Spoofing     │ Cache DNS           │ DNSSEC + DoH/DoT      │
│ VLAN Hopping     │ Isolation VLAN      │ Désactiver DTP, VLAN  │
│                  │                     │ natif ≠ VLAN 1        │
│ STP Attack       │ Topologie réseau    │ BPDU Guard, Root Guard│
└──────────────────┴─────────────────────┴───────────────────────┘
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- **DNS Cache Poisoning** = injecter fausse IP dans cache DNS → redirection
- **Ettercap + plugin dns_spoof** = outil courant pour DNS spoofing post-MITM
- **DNSSEC** = contre-mesure DNS (signatures cryptographiques)
- **VLAN Hopping** = double tagging ou switch spoofing (DTP)
- **Contre-mesure VLAN** = désactiver DTP + changer VLAN natif
- **STP Attack** = envoyer BPDUs pour devenir Root Bridge → Yersinia
- **BPDU Guard** = contre-mesure STP sur ports access
- **IRDP Spoofing** = moins fréquent mais à connaître pour l'examen

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [04 — ARP Poisoning](./04_ARP_Poisoning.md) | [06 — Outils de Sniffing](./06_Outils_de_Sniffing.md) |
