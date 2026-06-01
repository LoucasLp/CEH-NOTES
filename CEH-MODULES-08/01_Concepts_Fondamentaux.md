# 🌐 01 — Concepts Fondamentaux du Sniffing

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 1/8 — Bases, mode promiscuous & protocoles vulnérables

---

## 🔑 Définition

> Le **sniffing** (ou packet sniffing) est le processus de **capture et d'analyse du trafic réseau** transitant sur un réseau. Il permet à un attaquant d'intercepter des données sensibles (credentials, cookies, fichiers) circulant en clair.

Le sniffing opère principalement à la **couche 2 (Data Link)** du modèle OSI.

---

## ⚙️ Mode Promiscuous

Par défaut, une carte réseau (NIC) n'accepte que les trames **destinées à sa propre adresse MAC**.

En **mode promiscuous**, la NIC capture **TOUTES les trames** qui transitent sur le segment réseau, quelle que soit leur destination.

```
Mode Normal (par défaut)
─────────────────────────
Trame MAC:AA → Acceptée   ✅  (destinée à cette NIC)
Trame MAC:BB → Rejetée    ❌  (destinée à autre NIC)

Mode Promiscuous (sniffing activé)
─────────────────────────────────
Trame MAC:AA → Acceptée   ✅
Trame MAC:BB → Acceptée   ✅  (toutes les trames capturées)
Trame MAC:CC → Acceptée   ✅
```

**Activation sous Linux :**
```bash
# Activer le mode promiscuous
ip link set eth0 promisc on

# Vérifier
ip link show eth0 | grep PROMISC
```

---

## 🔀 Hub vs Switch — Impact sur le sniffing

```
RÉSEAU HUB (sniffing passif facile)
────────────────────────────────────────────────
  PC-A ──┐
  PC-B ──┼── HUB ── Diffuse TOUT à tous les ports
  PC-C ──┘
Attaquant voit TOUT le trafic → Sniffing passif

RÉSEAU SWITCHÉ (sniffing actif requis)
────────────────────────────────────────────────
  PC-A ──┐
  PC-B ──┼── SWITCH ── Envoie uniquement au port destinataire
  PC-C ──┘
Attaquant ne voit QUE son propre trafic
→ Doit utiliser MAC flooding, ARP spoofing, etc.
```

| Environnement | Type de sniffing | Difficulté |
|--------------|------------------|------------|
| **Hub** | Passif | Facile |
| **Switch** | Actif | Modéré |
| **Wi-Fi ouvert** | Passif | Facile |
| **Wi-Fi WPA2** | Actif (clé requise) | Difficile |

---

## 📋 Sniffing Passif vs Actif

### Sniffing Passif

> Capture le trafic **sans envoyer aucun paquet** sur le réseau. Indétectable par définition.

- Fonctionne sur les **hubs** et réseaux **Wi-Fi ouverts**
- L'attaquant écoute passivement le segment partagé
- **Outils :** Wireshark, tcpdump (en mode écoute seule)

**Méthodes passives :**
- Compromission physique du réseau (accès au câble)
- Utilisation d'un Trojan pour capturer le trafic local
- Port SPAN/mirroring (avec accès admin au switch)

---

### Sniffing Actif

> Injection de paquets sur le réseau pour **forcer le trafic à traverser** l'attaquant.

- Nécessaire sur les **réseaux switchés** (majorité des LAN modernes)
- Génère du trafic supplémentaire → détectable
- **Techniques :**

```
Techniques de sniffing actif
├── MAC Flooding       → Saturer la table CAM du switch
├── ARP Spoofing       → Empoisonner le cache ARP des victimes
├── DHCP Starvation    → Épuiser le pool DHCP
├── Rogue DHCP         → Serveur DHCP malveillant
├── DNS Spoofing       → Empoisonner le cache DNS
├── Switch Port Steal  → Voler le port switch d'une victime
└── VLAN Hopping       → Sauter entre VLANs
```

---

## 🚨 Protocoles vulnérables au sniffing

Ces protocoles transmettent les données **en clair (cleartext)** → directement lisibles par un sniffer :

| Protocole | Port | Données exposées |
|-----------|------|-----------------|
| **HTTP** | 80 | Credentials, cookies de session, formulaires |
| **FTP** | 21 | Nom d'utilisateur, mot de passe en clair |
| **Telnet** | 23 | Tout le contenu de la session (commandes + réponses) |
| **SMTP** | 25 | Emails, credentials |
| **POP3** | 110 | Emails, credentials |
| **IMAP** | 143 | Emails, credentials |
| **SNMP v1/v2c** | 161 | Community strings (= mots de passe SNMP) |
| **DNS** | 53 | Requêtes et réponses DNS |
| **LDAP** | 389 | Credentials d'annuaire |
| **NFS** | 2049 | Données de partage de fichiers |
| **NetBIOS** | 137-139 | Noms de machines, partages |

### Protocoles SÉCURISÉS (chiffrés)

| Protocole sécurisé | Protocole remplacé | Port |
|--------------------|--------------------|------|
| **HTTPS** | HTTP | 443 |
| **SFTP / FTPS** | FTP | 22 / 990 |
| **SSH** | Telnet | 22 |
| **SMTPS** | SMTP | 465/587 |
| **POP3S** | POP3 | 995 |
| **IMAPS** | IMAP | 993 |
| **SNMPv3** | SNMP v1/v2 | 161 |
| **LDAPS** | LDAP | 636 |

---

## 🔌 Hardware Protocol Analyzers

> Dispositifs **physiques** dédiés à la capture de trafic réseau — sans perturber le flux.

```
Réseau
  │
  ├── Tap physique ──→ Hardware Analyzer ──→ Capture
  │                   (Finisar, NetScout...)
  │
  └── Port SPAN ──→ Wireshark (logiciel)
```

**Avantages des hardware analyzers :**
- Capture à **line rate** (sans perte de paquets)
- **Invisible** sur le réseau (pas d'adresse IP)
- Portables, flexibles
- Capturent même les trames malformées

**Exemples :** Finisar Surveyor, NetScout nGenius, Fluke Networks

---

## 🔍 Port SPAN / Port Mirroring

> Fonctionnalité des switches qui copie le trafic d'un ou plusieurs ports vers un **port de monitoring**.

```
Switch
├── Port 1 : PC-A  ──────┐ trafic copié
├── Port 2 : PC-B  ──────┤──→ Port SPAN ──→ Wireshark (analyste/attaquant)
├── Port 3 : Serveur ────┘
└── Port SPAN : Analyste
```

- Légitime pour les administrateurs réseau
- Nécessite un accès admin au switch
- Outil : `Wireshark` sur le port miroir

---

## 📡 Wiretapping

> Interception des communications sur les lignes physiques (téléphonie, fibre, cuivre).

| Type | Description |
|------|-------------|
| **Active Wiretapping** | Surveillance + modification/injection dans la communication |
| **Passive Wiretapping** | Surveillance seule, sans altération |
| **Lawful Interception** | Wiretapping légal (autorisation judiciaire) |

> ⚠️ Wiretapper sans autorisation = **crime dans la majorité des pays**.

**PRISM (NSA)** : Programme de surveillance massif des communications Internet transitant par des serveurs américains, révélé par Edward Snowden en 2013.

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- Les sniffers opèrent à la **couche 2 (Data Link Layer)** du modèle OSI
- Le **mode promiscuous** permet de capturer le trafic non destiné à la NIC
- **Sniffing passif** = hub (pas de paquets envoyés) | **Actif** = switch (injection nécessaire)
- **Telnet** (port 23) transmet tout en **cleartext** → très vulnérable
- **FTP** transmet les credentials en clair (port 21)
- Un **port SPAN** est un outil légitime d'administration réseau

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [README](./README.md) | [02 — MAC & CAM Attacks](./02_MAC_et_CAM_Attacks.md) |
