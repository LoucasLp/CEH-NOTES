# 📡 CEH v13 — Module 08 : Sniffing

> **Certified Ethical Hacker v13** | EC-Council | Formation officielle  
> 📅 Mis à jour : 2025 | ✍️ Notes de cours personnelles

---

## 📋 Table des matières

| Fichier | Contenu | Thème |
|--------|---------|-------|
| [01_Concepts_Fondamentaux.md](./01_Concepts_Fondamentaux.md) | Packet sniffing, types passif/actif, protocoles vulnérables | 🌐 Bases du sniffing |
| [02_MAC_et_CAM_Attacks.md](./02_MAC_et_CAM_Attacks.md) | MAC Flooding, Switch Port Stealing, CAM Table | 🔀 Couche 2 |
| [03_DHCP_Attacks.md](./03_DHCP_Attacks.md) | DHCP Starvation, Rogue DHCP Server, DORA | 📦 DHCP |
| [04_ARP_Poisoning.md](./04_ARP_Poisoning.md) | ARP Spoofing, MITM, outils arpspoof/Ettercap | ☠️ ARP & MITM |
| [05_DNS_et_Autres_Spoofing.md](./05_DNS_et_Autres_Spoofing.md) | DNS Cache Poisoning, MAC Spoofing, IRDP, VLAN Hopping | 🕸️ Spoofing avancé |
| [06_Outils_de_Sniffing.md](./06_Outils_de_Sniffing.md) | Wireshark, tcpdump, Ettercap, Bettercap, NetworkMiner | 🛠️ Outils |
| [07_Detection_et_Contre-mesures.md](./07_Detection_et_Contre-mesures.md) | Détection mode promiscuous, DAI, DHCP Snooping, chiffrement | 🛡️ Défense |
| [08_Quiz_et_Revision.md](./08_Quiz_et_Revision.md) | Questions type examen CEH, mémo, checklist | 📚 Révision |

---

## 🎯 Objectifs du module

À la fin de ce module, vous serez capable de :

- ✅ Expliquer comment fonctionne le sniffing réseau (promiscuous mode)
- ✅ Distinguer le sniffing passif du sniffing actif
- ✅ Comprendre et exploiter les attaques MAC Flooding & Switch Port Stealing
- ✅ Réaliser une attaque DHCP Starvation et déployer un Rogue DHCP
- ✅ Mener une attaque ARP Spoofing (Man-in-the-Middle)
- ✅ Comprendre le DNS Cache Poisoning et ses variantes
- ✅ Utiliser Wireshark, tcpdump, Ettercap et Bettercap
- ✅ Détecter un sniffer sur le réseau (méthodes Ping, ARP)
- ✅ Appliquer les contre-mesures (DAI, DHCP Snooping, chiffrement)

---

## 🧠 Mémo rapide — Sniffing en une page

```
TYPES DE SNIFFING
─────────────────────────────────────────────────────
PASSIF   → Hub / réseau sans-fil open → pas de paquets injectés
ACTIF    → Réseau switché → injection nécessaire
           ├── MAC Flooding    (macof)
           ├── ARP Spoofing    (arpspoof, Ettercap)
           ├── DHCP Starvation (Yersinia, dhcpstarv)
           ├── DNS Spoofing    (dnsspoof, Ettercap plugin)
           └── VLAN Hopping    (Yersinia)

ATTAQUE MITM TYPE (active sniffing)
─────────────────────────────────────────────────────
[Attaquant] <-> [Victime] <-> [Routeur/Serveur]
Tout le trafic passe par l'attaquant → interception

PROTOCOLES VULNÉRABLES (cleartext)
─────────────────────────────────────────────────────
HTTP  (80)   FTP (21)    Telnet (23)
SMTP  (25)   POP3 (110)  IMAP (143)
SNMP v1/v2 (161)         DNS (53)

CONTRE-MESURES CLÉS
─────────────────────────────────────────────────────
→ DHCP Snooping        → Port Security
→ Dynamic ARP Inspection (DAI)
→ Utiliser TLS/HTTPS/SSH/SFTP
→ VPN (chiffrement de bout en bout)
→ 802.1X (authentification port réseau)
```

---

## 📊 Poids dans l'examen CEH

> Le sniffing représente environ **10% des questions** du CEH v13 (domaine "Sniffing").

| Sujet | Fréquence examen |
|-------|-----------------|
| ARP Spoofing / MITM | Très élevée |
| MAC Flooding | Élevée |
| DHCP Attacks | Élevée |
| DNS Poisoning | Élevée |
| Wireshark / outils | Moyenne |
| Contre-mesures (DAI, DHCP Snooping) | Très élevée |
| Détection mode promiscuous | Moyenne |

---

## 🔗 Modules adjacents

| Module 07 | Module 09 |
|-----------|-----------|
| Malware Threats | Social Engineering |

---

*Notes préparées pour la certification CEH v13 — Usage personnel et GitHub*
