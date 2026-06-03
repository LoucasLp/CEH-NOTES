# 📝 Module 03 — Quiz & Révision : Network Scanning

> **CEH v13 | EC-Council**  
> `#Quiz` `#Nmap` `#PortScanning` `#OSFingerprinting` `#IDS_Evasion` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- Scanning = **deuxième phase** du hacking — identifier hôtes, ports ouverts, services, OS
- **Nmap** = outil de référence pour tout type de scan réseau
- **-sS** (SYN/Stealth) = le scan CEH le plus demandé à l'examen
- Scans furtifs : SYN, NULL, FIN, Xmas, IDLE — pas de connexion complète
- Évasion IDS : fragmentation (`-f`), décoy (`-D`), scan lent (`-T0`), source port (`-g`)

---

## 🧠 Concepts clés

| Option Nmap | Technique | Comportement |
|-------------|-----------|-------------|
| `-sT` | TCP Connect | Connexion complète (SYN→SYN-ACK→ACK) — visible dans les logs |
| `-sS` | SYN/Stealth | Demi-connexion (SYN→SYN-ACK→RST) — plus discret |
| `-sU` | UDP Scan | Requête UDP → ICMP Port Unreachable = fermé / timeout = ouvert |
| `-sN/-sF/-sX` | NULL/FIN/Xmas | Pas de réponse = ouvert (Unix) ; RST = fermé (Windows ne respecte pas) |
| `-sA` | ACK Probe | Déterminer les règles de firewall (RST = non filtré) |
| `-sI` | IDLE Scan | Scan via un "zombie" — IP source totalement masquée |
| `-O` | OS Detection | TTL + window size → fingerprinting système |
| `-sV` | Version | Banner grabbing des services |
| `-f` | Fragmentation | Fragmenter paquets → contourner IDS |
| `-D RND:5` | Décoy | 5 IPs leurres pour masquer la vraie source |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **`-sS`** = SYN Scan = stealth/half-open = le plus courant en pentest
2. **`-sT`** = TCP Connect = visible dans les logs du serveur (connexion complète)
3. **NULL / FIN / Xmas scans** → Pas de réponse = ouvert **sur Unix** (Windows renvoie toujours RST)
4. **IDLE Scan (`-sI`)** = scan totalement anonyme via machine zombie (IPID)
5. **ACK Scan (`-sA`)** = ne détecte pas les ports ouverts, mais détecte les règles firewall
6. **`-O`** = OS fingerprinting (TTL : Windows=128, Linux=64, Cisco=255)
7. **`-f`** = fragmentation des paquets contre IDS
8. **`-D RND:10`** = 10 IPs décoy pour masquer la source
9. **`-T0`** à **`-T5`** : Paranoid (0) → Insane (5) — T0/T1 pour évasion IDS
10. **TTL Windows = 128**, **Linux = 64**, **Cisco = 255** (OS fingerprinting passif)

---

## ❓ Questions de révision

1. Quelle est la différence fondamentale entre un TCP Connect Scan et un SYN Scan ?
2. Pourquoi les scans NULL, FIN et Xmas sont-ils inefficaces sur les systèmes Windows ?
3. Comment fonctionne l'IDLE Scan et quel avantage apporte-t-il ?
4. Quelle est la différence entre `-O` et `-sV` dans Nmap ?
5. Comment le banner grabbing permet-il d'identifier les vulnérabilités d'un service ?
6. Expliquez deux techniques d'évasion d'IDS utilisables avec Nmap.
7. Quelle est la signification des TTL 64, 128 et 255 en OS fingerprinting ?
8. Qu'est-ce que le scan ACK (`-sA`) permet de détecter ?

---

## 📋 Quiz QCM

**Question 1.** L'option Nmap `-sS` réalise quel type de scan ?
- a) UDP Scan
- b) TCP Connect Scan (Full Open)
- c) TCP SYN Scan (Stealth / Half-Open)
- d) ACK Scan

**Question 2.** Un scan Xmas envoie des paquets avec quels flags TCP activés ?
- a) SYN + ACK
- b) FIN + URG + PSH
- c) SYN + FIN
- d) RST + ACK

**Question 3.** Un paquet TCP avec TTL = 64 indique probablement quel système d'exploitation ?
- a) Windows
- b) Cisco IOS
- c) Linux / Unix
- d) macOS Retina

**Question 4.** L'IDLE Scan (nmap -sI) présente quel avantage principal ?
- a) Il est plus rapide qu'un SYN scan
- b) Il masque totalement l'IP de l'attaquant en utilisant une machine zombie
- c) Il peut détecter les systèmes derrière un NAT
- d) Il contourne les firewalls applicatifs

**Question 5.** L'option `nmap -D RND:5 target` permet :
- a) D'effectuer 5 scans successifs
- b) D'envoyer des paquets fragmentés en 5 morceaux
- c) D'utiliser 5 adresses IP décoy pour masquer la source réelle
- d) De scanner 5 ports simultanément

**Question 6.** Un scan ACK (`-sA`) sur un port qui retourne RST signifie que ce port est :
- a) Ouvert
- b) Fermé
- c) Non filtré (le firewall laisse passer les ACK)
- d) Filtré (le firewall bloque les ACK)

**Question 7.** Pour contourner un IDS avec des paquets fragmentés, on utilise :
- a) `nmap -sI`
- b) `nmap -f`
- c) `nmap -T0`
- d) `nmap -D`

**Question 8.** Quel outil autre que Nmap est spécialisé dans l'envoi de paquets TCP/UDP personnalisés ?
- a) Wireshark
- b) Nikto
- c) Hping3
- d) sqlmap

---

## ✅ Points de révision — Checklist

- [ ] Je connais les options Nmap essentielles (-sS, -sT, -sU, -O, -sV, -sA, -sI, -f, -D)
- [ ] Je comprends la différence TCP Connect vs SYN Scan
- [ ] Je sais pourquoi NULL/FIN/Xmas ne fonctionnent pas sur Windows
- [ ] Je comprends le principe de l'IDLE Scan (machine zombie + IPID)
- [ ] Je connais les TTL d'OS fingerprinting (64=Linux, 128=Windows, 255=Cisco)
- [ ] Je peux citer 3 techniques d'évasion IDS
- [ ] Je sais ce que détecte un scan ACK (règles firewall, pas les ports ouverts)

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **c** | `-sS` = SYN (stealth / half-open) |
| 2 | **b** | Xmas = FIN + URG + PSH activés (comme un "sapin de Noël") |
| 3 | **c** | TTL 64 = Linux/Unix |
| 4 | **b** | IDLE = zombie machine = IP source totalement masquée |
| 5 | **c** | `-D RND:5` = 5 IPs décoy aléatoires |
| 6 | **c** | RST en ACK scan = non filtré (firewall laisse passer) |
| 7 | **b** | `-f` = fragmentation des paquets |
| 8 | **c** | Hping3 = outil de crafting de paquets TCP/UDP |

---

*⬅️ [Contre-mesures](./07_contre_mesures.md) | ➡️ [README Module 03](./README.md)*
