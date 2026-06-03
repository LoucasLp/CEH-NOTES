# 📚 CEH v13 — Module 08 : Sniffing

> **Certified Ethical Hacker v13 — AI Powered**  
> EC-Council | Formation officielle

---

## 🗂️ Structure des notes

| Fichier | Contenu |
|--------|---------|
| [`01_Concepts_Fondamentaux.md`](./01_Concepts_Fondamentaux.md) | Packet sniffing, promiscuous mode, passif vs actif, protocoles vulnérables |
| [`02_MAC_et_CAM_Attacks.md`](./02_MAC_et_CAM_Attacks.md) | MAC Flooding, Switch Port Stealing, CAM Table overflow |
| [`03_DHCP_Attacks.md`](./03_DHCP_Attacks.md) | DHCP Starvation, Rogue DHCP Server, processus DORA |
| [`04_ARP_Poisoning.md`](./04_ARP_Poisoning.md) | ARP Spoofing, MITM, arpspoof, Ettercap, Bettercap |
| [`05_DNS_et_Autres_Spoofing.md`](./05_DNS_et_Autres_Spoofing.md) | DNS Cache Poisoning, MAC Spoofing, IRDP, VLAN Hopping |
| [`06_Outils_de_Sniffing.md`](./06_Outils_de_Sniffing.md) | Wireshark, tcpdump, Ettercap, Bettercap, NetworkMiner |
| [`07_Detection_et_Contre-mesures.md`](./07_Detection_et_Contre-mesures.md) | DAI, DHCP Snooping, Port Security, chiffrement, détection promiscuous |
| [`08_Quiz_et_Revision.md`](./08_Quiz_et_Revision.md) | Questions type examen CEH, mémo, checklist |

---

## 🎯 Objectifs du module

À l'issue de ce module, vous serez capable de :

- ✅ Expliquer le fonctionnement du sniffing réseau (mode promiscueux)
- ✅ Distinguer sniffing passif (hub/Wi-Fi) et actif (réseau switché)
- ✅ Réaliser une attaque MAC Flooding et ARP Spoofing (MITM)
- ✅ Monter une attaque DHCP Starvation et un Rogue DHCP Server
- ✅ Comprendre DNS Cache Poisoning et VLAN Hopping
- ✅ Utiliser Wireshark, tcpdump, Ettercap et Bettercap
- ✅ Détecter un sniffer sur le réseau (Ping, ARP, promiscuous detection)
- ✅ Appliquer les contre-mesures (DAI, DHCP Snooping, 802.1X)

---

## 📊 Poids dans l'examen CEH (312-50)

```
Module 08 — Sniffing
Poids estimé : ~5-7% des questions de l'examen
Nb de labs officiels : ~6 labs dans la plateforme iLabs/CyberQ
Thèmes récurrents : ARP Spoofing / MITM, MAC Flooding (macof),
                    DAI + DHCP Snooping comme contre-mesures,
                    protocoles en clair (HTTP/FTP/Telnet/SNMP v1)
```

---

## 🔗 Ressources complémentaires

- [EC-Council Official CEHv13](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/)
- [CyberQ Platform (Labs)](https://cyberq.eccouncil.org)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [Ettercap](https://www.ettercap-project.org/)
- [Bettercap](https://www.bettercap.org/)

---

## 🧠 Mémo rapide — Types de sniffing

```
PASSIF  → Réseau Hub / Wi-Fi open → capture transparente, aucune injection
ACTIF   → Réseau switché → injection nécessaire :
           ├── MAC Flooding    : macof → inonde la CAM table → switch en hub
           ├── ARP Spoofing    : arpspoof / Ettercap → MITM
           ├── DHCP Starvation : Yersinia → vide le pool DHCP
           └── VLAN Hopping    : Yersinia → accès à d'autres VLANs

CONTRE-MESURES CLÉS :
  DHCP Snooping     → bloque les Rogue DHCP Servers
  Dynamic ARP Insp. → valide les réponses ARP
  Port Security     → limite les MACs par port
  802.1X            → authentification par port réseau
```

---

*Notes rédigées dans le cadre de la préparation à la certification CEH v13 — EC-Council*  
*Format : Markdown — Compatible GitHub Pages*
