# 🛡️ CEH v13 — Module 3 : Network Scanning

> **Certified Ethical Hacker v13** | Notes de cours complètes  
> *Basées sur le cours officiel EC-Council*

---

## 📚 Table des matières

| Fichier | Contenu |
|--------|---------|
| [01 — Types de Scanning](./01_types_de_scanning.md) | Port, Network, Vulnerability Scanning + Flags TCP |
| [02 — Outils de Scanning](./02_outils_de_scanning.md) | Nmap, Hping3, Metasploit, NetScanTools Pro |
| [03 — Découverte d'Hôtes](./03_decouverte_hotes.md) | ARP, UDP, ICMP, TCP Ping Scans, Ping Sweep |
| [04 — Techniques de Port Scanning](./04_techniques_port_scanning.md) | TCP Full-Open, Stealth, Xmas, UDP, SCTP, IDLE... |
| [05 — Découverte OS & Banner Grabbing](./05_os_discovery_banner_grabbing.md) | Active/Passive fingerprinting, TTL, Nmap, Wireshark |
| [06 — Évasion IDS & Firewall](./06_evasion_ids_firewall.md) | Fragmentation, Spoofing, Decoy, Proxy, Anonymizer |
| [07 — Contre-mesures](./07_contre_mesures.md) | Ping Sweep, Port Scan, Banner Grabbing, IP Spoofing |

---

## 🎯 Objectifs du Module

```
✔  Comprendre les différents types de scanning réseau
✔  Maîtriser les outils : Nmap, Hping3, Metasploit
✔  Identifier les hôtes actifs sur un réseau
✔  Reconnaître les techniques de port scanning avancées
✔  Effectuer la découverte d'OS (Banner Grabbing)
✔  Contourner IDS et Firewalls
✔  Mettre en place des contre-mesures efficaces
```

---

## 🧠 Rappel rapide — Commandes Nmap essentielles

| Option | Description |
|--------|-------------|
| `-sT` | TCP Connect Scan (Full-Open) |
| `-sS` | TCP SYN Scan (Stealth / Half-Open) |
| `-sU` | UDP Scan |
| `-sF` | FIN Scan |
| `-sX` | Xmas Scan |
| `-sN` | NULL Scan |
| `-sA` | ACK Flag Probe Scan |
| `-sI` | IDLE / IPID Scan |
| `-O`  | OS Detection |
| `-sV` | Version Detection |
| `-sC` | Script Scan (NSE) |
| `-PR` | ARP Ping Scan |
| `-PE` | ICMP ECHO Ping |
| `-PP` | ICMP Timestamp Ping |
| `-PM` | ICMP Address Mask Ping |
| `-PS` | TCP SYN Ping |
| `-PA` | TCP ACK Ping |
| `-6`  | IPv6 Scan |
| `-D RND:10` | Decoy Scan |
| `--spoof-mac 0` | MAC Spoofing (aléatoire) |
| `--badsum` | Bad Checksum |

---

## ⚠️ Disclaimer

> Ces notes sont réalisées dans un cadre **éducatif et légal**, dans le contexte de la préparation à la certification CEH.  
> Toute utilisation des techniques présentées doit se faire **uniquement sur des systèmes autorisés**.

---

*Dernière mise à jour : Mars 2026*
