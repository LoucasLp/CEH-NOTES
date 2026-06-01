# 📚 08 — Quiz & Révision Finale

> **CEH v13 | Module 08 : Sniffing**  
> 📌 Section 8/8 — Questions type examen CEH, mémo final, checklist

---

## 🧠 MÉMO FINAL — Module 8 en un coup d'œil

```
╔══════════════════════════════════════════════════════════════╗
║           CEH v13 — MODULE 08 : SNIFFING                    ║
║                      CHEAT SHEET                             ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  TYPES DE SNIFFING                                           ║
║  Passif  → Hub, Wi-Fi open → AUCUN paquet envoyé            ║
║  Actif   → Switch → injection nécessaire                     ║
║                                                              ║
║  ATTAQUES ACTIVES                                            ║
║  MAC Flooding     → macof → switch = hub                     ║
║  DHCP Starvation  → Yersinia → pool épuisé                  ║
║  Rogue DHCP       → fausse gateway/DNS → MITM               ║
║  ARP Spoofing     → arpspoof/Ettercap → MITM                ║
║  DNS Spoofing     → Ettercap dns_spoof → redirection         ║
║  VLAN Hopping     → Yersinia → accès inter-VLAN              ║
║  STP Attack       → Yersinia → Root Bridge hijack            ║
║                                                              ║
║  OUTILS                                                      ║
║  Wireshark  → GUI sniffer (ex-Ethereal)                      ║
║  tcpdump    → CLI sniffer Linux                              ║
║  Ettercap   → MITM framework + plugins                       ║
║  Bettercap  → Successeur Ettercap, moderne                   ║
║  macof      → MAC Flooding (dsniff)                          ║
║  Yersinia   → Attaques L2 (DHCP, STP, VLAN...)             ║
║  arpspoof   → ARP Spoofing (dsniff)                          ║
║  Cain&Abel  → Windows, MITM + cracking                       ║
║                                                              ║
║  CONTRE-MESURES                                              ║
║  Port Security    → MAC Flooding                             ║
║  DHCP Snooping    → Rogue DHCP + Starvation                  ║
║  DAI              → ARP Poisoning (besoin DHCP Snooping)     ║
║  IP Source Guard  → IP Spoofing                              ║
║  DNSSEC / DoH     → DNS Poisoning                            ║
║  HSTS             → SSL Stripping                            ║
║  802.1X           → Accès réseau non autorisé                ║
║  Chiffrement      → Sniffing cleartext                       ║
║                                                              ║
║  DÉTECTION PROMISCUOUS                                        ║
║  Ping method → fausse MAC + IP correcte                      ║
║  ARP method  → unicast ARP invalide                          ║
║  DNS method  → volume PTR queries anormal                    ║
╚══════════════════════════════════════════════════════════════╝
```

---

## ❓ QUESTIONS D'EXAMEN TYPE CEH v13

---

**Q1.** Un attaquant inonde le réseau avec des trames contenant des adresses MAC aléatoires, forçant le switch à se comporter comme un hub. Quelle attaque est-ce ?

- A) ARP Flooding
- B) MAC Spoofing
- C) **MAC Flooding ✅**
- D) VLAN Hopping

> 💡 Le MAC Flooding sature la table CAM → switch bascule en mode hub → broadcast à tous les ports.

---

**Q2.** Quel outil est le plus approprié pour réaliser une attaque MAC Flooding ?

- A) Ettercap
- B) Yersinia
- C) arpspoof
- D) **macof ✅**

> 💡 `macof` (du package dsniff) génère des milliers de trames avec MACs aléatoires pour saturer la CAM.

---

**Q3.** Un attaquant envoie des milliers de requêtes DHCP DISCOVER avec des adresses MAC falsifiées, épuisant le pool d'adresses du serveur. Quelle attaque est-ce ?

- A) Rogue DHCP
- B) **DHCP Starvation ✅**
- C) DHCP Poisoning
- D) ARP Starvation

> 💡 DHCP Starvation = épuiser le pool IP → déni de service pour les clients légitimes.

---

**Q4.** Quelle fonctionnalité Cisco IOS empêche un serveur DHCP non autorisé d'envoyer des réponses aux clients ?

- A) Dynamic ARP Inspection
- B) Port Security
- C) IP Source Guard
- D) **DHCP Snooping ✅**

> 💡 DHCP Snooping classe les ports en trusted/untrusted → les réponses DHCP ne sont acceptées que sur les ports trusted.

---

**Q5.** Un attaquant envoie de fausses réponses ARP pour associer son adresse MAC à l'IP de la passerelle. Quel type d'attaque est-ce ?

- A) DNS Spoofing
- B) MAC Flooding
- C) **ARP Poisoning / ARP Spoofing ✅**
- D) DHCP Starvation

> 💡 L'ARP Spoofing empoisonne le cache ARP des victimes → MITM.

---

**Q6.** Quelle contre-mesure au niveau du switch permet de valider les paquets ARP en utilisant la DHCP Snooping Binding Table ?

- A) Port Security
- B) IP Source Guard
- C) **Dynamic ARP Inspection (DAI) ✅**
- D) BPDU Guard

> 💡 DAI utilise la binding table de DHCP Snooping pour valider l'association IP/MAC de chaque paquet ARP.

---

**Q7.** Dans Wireshark, quelle fonctionnalité permet de reconstituer et visualiser l'intégralité d'une session TCP (par exemple une session HTTP avec identifiants) ?

- A) Export Objects
- B) IO Graph
- C) Protocol Hierarchy
- D) **Follow TCP Stream ✅**

> 💡 Follow TCP Stream reconstruit la session complète → très utile pour voir credentials en clair.

---

**Q8.** Wireshark s'appelait autrefois :

- A) NetCapture
- B) **Ethereal ✅**
- C) EtherApe
- D) PacketShark

> 💡 Renommé Wireshark en 2006 suite à un problème de marque déposée.

---

**Q9.** Quel filtre Wireshark permet d'afficher uniquement les requêtes HTTP POST ?

- A) `tcp.port == 80 && post`
- B) `http.method == "POST"`
- C) **`http.request.method == "POST"` ✅**
- D) `http contains "POST"`

---

**Q10.** Un attaquant sur un réseau LAN switché veut intercepter le trafic entre deux hôtes. Quelle technique utilise-t-il pour forcer le trafic à transiter par lui ?

- A) Passive Sniffing
- B) Port Mirroring
- C) **ARP Spoofing (MITM) ✅**
- D) Wiretapping

> 💡 Sur un réseau switché, le trafic unicast n'est pas broadcasté → l'attaquant doit empoisonner le cache ARP pour créer un MITM.

---

**Q11.** Pour détecter une machine en mode promiscuous, on envoie un ping avec :

- A) Une IP invalide et une MAC valide
- B) Une IP valide et une MAC broadcast
- C) **Une IP valide et une fausse MAC ✅**
- D) Une IP et MAC invalides

> 💡 Seule une machine en mode promiscuous traite les trames avec une MAC destination invalide et répond au ping.

---

**Q12.** Quel protocole fournit des signatures cryptographiques pour authentifier les réponses DNS et contrer le DNS Cache Poisoning ?

- A) DoH (DNS over HTTPS)
- B) DNSTLS
- C) **DNSSEC ✅**
- D) DNS Guard

---

**Q13.** Un attaquant veut accéder au VLAN Comptabilité depuis le VLAN par défaut en envoyant une trame avec deux tags 802.1Q. Quelle attaque est-ce ?

- A) STP Attack
- B) **VLAN Hopping (Double Tagging) ✅**
- C) MAC Flooding
- D) DTP Spoofing

> 💡 Le Double Tagging exploite le fait que le switch retire le tag natif mais pas le second tag interne.

---

**Q14.** Quel outil est spécifiquement conçu pour les attaques sur les protocoles de couche 2 comme DHCP, STP et VLAN ?

- A) Ettercap
- B) **Yersinia ✅**
- C) Metasploit
- D) macof

> 💡 Yersinia est spécialisé dans les attaques de protocoles L2 : DHCP, STP, CDP, DTP, HSRP, etc.

---

**Q15.** Quel port UDP utilise le client DHCP pour recevoir les réponses du serveur DHCP ?

- A) UDP 67
- B) **UDP 68 ✅**
- C) UDP 69
- D) UDP 546

> 💡 Serveur DHCP = UDP 67 | Client DHCP = UDP 68. Pour IPv6 : serveur 547, client 546.

---

## 📋 CHECKLIST RÉVISION — Module 08

Avant l'examen, assurez-vous de maîtriser :

- [ ] Définition du sniffing et du mode promiscuous
- [ ] Différence sniffing passif vs actif
- [ ] Protocoles cleartext vulnérables (HTTP, FTP, Telnet, SMTP...)
- [ ] MAC Flooding : principe, outil (macof), contre-mesure (Port Security)
- [ ] Table CAM : fonctionnement et limite
- [ ] DHCP : processus DORA, ports UDP 67/68
- [ ] DHCP Starvation : principe, outils (Yersinia, dhcpstarv)
- [ ] Rogue DHCP : principe, combinaison avec Starvation
- [ ] DHCP Snooping : ports trusted/untrusted
- [ ] ARP Poisoning : principe, schéma MITM
- [ ] Outils ARP Spoofing : arpspoof, Ettercap, Bettercap
- [ ] IP Forwarding : pourquoi l'activer lors d'un MITM
- [ ] DAI : Dynamic ARP Inspection (nécessite DHCP Snooping)
- [ ] DNS Cache Poisoning : principe et techniques
- [ ] DNSSEC + DoH/DoT : contre-mesures DNS
- [ ] VLAN Hopping : Double Tagging, Switch Spoofing
- [ ] STP Attack : Root Bridge hijack avec Yersinia
- [ ] Wireshark : filtres essentiels, Follow TCP Stream
- [ ] tcpdump : commandes de base
- [ ] Ettercap : MITM + plugin dns_spoof
- [ ] Détection promiscuous : méthodes Ping et ARP
- [ ] ARPWatch : surveillance cache ARP

---

## 🔗 Navigation

| ← Précédent | Retour → |
|-------------|----------|
| [07 — Détection & Contre-mesures](./07_Detection_et_Contre-mesures.md) | [README — Index](./README.md) |

---

*✨ Good luck ! "Sniff the network, but first, understand what you're listening to."*
