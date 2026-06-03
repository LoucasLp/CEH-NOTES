# 📚 CEH v13 — Module 03 : Network Scanning

> **Certified Ethical Hacker v13 — AI Powered**  
> EC-Council | Formation officielle

---

## 🗂️ Structure des notes

| Fichier | Contenu |
|--------|---------|
| [`01_types_de_scanning.md`](./01_types_de_scanning.md) | Port / Network / Vulnerability Scanning, flags TCP |
| [`02_outils_de_scanning.md`](./02_outils_de_scanning.md) | Nmap, Hping3, Metasploit, NetScanTools Pro |
| [`03_decouverte_hotes.md`](./03_decouverte_hotes.md) | ARP, UDP, ICMP, TCP Ping Scans, Ping Sweep |
| [`04_techniques_port_scanning.md`](./04_techniques_port_scanning.md) | TCP Full-Open, Stealth, Xmas, NULL, IDLE, UDP, SCTP |
| [`05_os_discovery_banner_grabbing.md`](./05_os_discovery_banner_grabbing.md) | Fingerprinting actif/passif, TTL, Nmap -O, Wireshark |
| [`06_evasion_ids_firewall.md`](./06_evasion_ids_firewall.md) | Fragmentation, Spoofing, Décoy (-D), Proxy, Anonymizer |
| [`07_contre_mesures.md`](./07_contre_mesures.md) | Protection ping sweep, port scan, banner grabbing, spoofing |
| [`08_Quiz_et_Revision.md`](./08_Quiz_et_Revision.md) | Questions, rappels, points d'examen |

---

## 🎯 Objectifs du module

À l'issue de ce module, vous serez capable de :

- ✅ Distinguer les types de scanning (port, réseau, vulnérabilités)
- ✅ Maîtriser les options Nmap essentielles (-sS, -sT, -sU, -O, -sV, -A)
- ✅ Identifier les hôtes actifs sur un réseau (ARP, ICMP, TCP pings)
- ✅ Réaliser des scans furtifs (SYN, NULL, FIN, Xmas, IDLE)
- ✅ Effectuer du banner grabbing et de l'OS fingerprinting
- ✅ Contourner les IDS et firewalls lors du scanning
- ✅ Mettre en place des contre-mesures efficaces

---

## 📊 Poids dans l'examen CEH (312-50)

```
Module 03 — Network Scanning
Poids estimé : ~3-5% des questions de l'examen
Nb de labs officiels : ~8 labs dans la plateforme iLabs/CyberQ
Thèmes récurrents : types de scan Nmap (-sS vs -sT), flags TCP (SYN/ACK/FIN...),
                    techniques d'évasion (fragmentation, décoy), TTL/OS fingerprinting
```

---

## 🔗 Ressources complémentaires

- [EC-Council Official CEHv13](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/)
- [CyberQ Platform (Labs)](https://cyberq.eccouncil.org)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Hping3 Documentation](https://linux.die.net/man/8/hping3)
- [Wireshark](https://www.wireshark.org/)

---

## 🧠 Mémo rapide — Nmap options clés

| Option | Technique | Comportement |
|--------|-----------|-------------|
| `-sT` | TCP Connect Scan | Connexion complète (SYN → SYN-ACK → ACK) |
| `-sS` | TCP SYN (Stealth) | Demi-connexion (SYN → SYN-ACK → RST) |
| `-sU` | UDP Scan | Requête UDP, timeout = fermé |
| `-sF` / `-sX` / `-sN` | FIN / Xmas / NULL | Pas de réponse = ouvert (Unix) |
| `-sA` | ACK Probe | Déterminer les règles de firewall |
| `-sI` | IDLE Scan | Scan via un zombie (IP source usurpée) |
| `-O` | OS Detection | TTL + Window size fingerprinting |
| `-sV` | Version Detection | Banner grabbing des services |
| `-D RND:5` | Décoy Scan | Masquer la source parmi des leurres |
| `-f` | Fragmentation | Contournement IDS/firewall |

---

*Notes rédigées dans le cadre de la préparation à la certification CEH v13 — EC-Council*  
*Format : Markdown — Compatible GitHub Pages*
