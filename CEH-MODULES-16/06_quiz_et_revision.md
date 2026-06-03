# 📝 Module 16 — Quiz & Révision : Hacking Wireless Networks

> **CEH v13 | EC-Council**  
> `#Quiz` `#Wireless` `#WEP` `#WPA2` `#Aircrack` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- WEP = obsolète, cracké en minutes (IV 24 bits)
- WPA2-PSK = crackable si passphrase faible (handshake ou PMKID)
- WPA3 = résiste aux attaques dictionnaire (SAE + PMF)
- WPS PIN = 20000 combinaisons max, Reaver ~4-10h
- Suite Aircrack-ng = outil CEH officiel pour l'audit Wi-Fi
- Evil Twin = faux AP même SSID → MITM

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **WEP IV** | Vecteur d'initialisation 24 bits → réutilisation rapide |
| **4-way handshake** | Échange WPA2 lors de la connexion → capturable |
| **PMKID** | Identifiant de clé permettant le cracking sans handshake |
| **Evil Twin** | AP malveillant avec le même SSID que la cible |
| **KARMA** | AP qui répond à tous les Probe Requests |
| **Déauth** | Trame de déconnexion forgée (non chiffrée en WPA2) |
| **SAE** | Simultaneous Authentication of Equals (WPA3) |
| **PMF** | Protected Management Frames — WPA3, anti-déauth |
| **WIDS** | Wireless Intrusion Detection System |
| **Airmon-ng** | Outil pour passer l'interface en mode monitor |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **WEP** = RC4 + IV 24 bits = cassé, ne jamais utiliser
2. **airmon-ng start wlan0** = première commande avant tout audit
3. **aireplay-ng --deauth** = forcer la déconnexion (puis capturer le handshake)
4. **airodump-ng -w** = capturer et sauvegarder les paquets
5. **aircrack-ng -w rockyou.txt** = cracker WPA2 avec dictionnaire
6. **WPS PIN** = 20000 tentatives max (8 chiffres en 2 × 4 séparés)
7. **Reaver** = outil de brute force WPS
8. **Désactiver WPS** = contre-mesure principale
9. **WPA3 PMF** = protège contre les attaques de déauth
10. **PMKID** (2018) = cracking WPA2 sans capturer un handshake

---

## ❓ Questions de révision

1. Pourquoi WEP est-il considéré comme totalement cassé ?
2. Comment fonctionne une attaque de déauthentification et à quoi sert-elle ?
3. Quelle est la différence entre une attaque PMKID et une attaque sur le 4-way handshake ?
4. Comment fonctionne le brute force WPS PIN de Reaver ? Pourquoi est-il limité à ~20000 tentatives ?
5. Expliquez une attaque Evil Twin et comment s'en protéger.
6. Quelle commande Aircrack-ng permet de passer en mode monitor ?
7. Qu'est-ce que la KARMA attack ?
8. Pourquoi WPA3 résiste-t-il mieux aux attaques dictionnaire que WPA2 ?

---

## 📋 Quiz QCM

**Question 1.** La faiblesse principale de WEP est :
- a) L'utilisation d'AES comme algorithme de chiffrement
- b) Le vecteur d'initialisation (IV) de seulement 24 bits
- c) L'absence d'authentification
- d) L'utilisation d'un canal unique

**Question 2.** Quelle est la première commande à exécuter pour les audits Wi-Fi avec Aircrack-ng ?
- a) `airodump-ng wlan0`
- b) `aircrack-ng wlan0`
- c) `airmon-ng start wlan0`
- d) `aireplay-ng -3 wlan0`

**Question 3.** L'attaque PMKID permet de :
- a) Cracker WPA2 sans avoir besoin de capturer un handshake complet
- b) Bypasser le WPS sur n'importe quel routeur
- c) Déchiffrer le trafic WEP sans IV
- d) Créer un Evil Twin automatiquement

**Question 4.** Pourquoi Reaver peut-il cracker WPS en maximum ~20000 tentatives ?
- a) Car WPS utilise seulement 20000 caractères ASCII
- b) Car le PIN de 8 chiffres est vérifié en deux moitiés de 4 chiffres
- c) Car Reaver utilise un algorithme mathématique spécial
- d) Car les routeurs limitent les tentatives à 20000

**Question 5.** WPA3 résiste aux attaques dictionnaire grâce à :
- a) AES 256 bits
- b) L'algorithme SAE (Dragonfly)
- c) Des clés dynamiques par paquet
- d) L'utilisation de certificats numériques

**Question 6.** Pour capturer un handshake WPA2, on utilise :
- a) `aireplay-ng -3` suivi de `aircrack-ng`
- b) `airodump-ng` pour capturer + `aireplay-ng --deauth` pour forcer la reconnexion
- c) `airmon-ng stop` puis scan manuel
- d) `aircrack-ng --wpa` directement sur l'AP

**Question 7.** La protection contre les attaques de déauthentification en WPA3 est :
- a) WPA3-SAE
- b) PMF (Protected Management Frames)
- c) OWE
- d) CCMP

**Question 8.** Quel outil est utilisé pour détecter les Rogue APs sur un réseau Wi-Fi ?
- a) Reaver
- b) Hashcat
- c) WIDS (ex: Kismet)
- d) sqlmap

---

## ✅ Points de révision — Checklist

- [ ] Je comprends pourquoi WEP est cassé (IV 24 bits)
- [ ] Je connais les étapes du cracking WPA2 (airmon, airodump, deauth, aircrack)
- [ ] Je comprends la différence WPA2 handshake vs PMKID
- [ ] Je sais comment fonctionne WPS et pourquoi il est vulnérable
- [ ] Je connais les commandes clés de la suite Aircrack-ng
- [ ] Je comprends l'attaque Evil Twin et la KARMA attack
- [ ] Je sais pourquoi WPA3 est plus sécurisé (SAE, PMF)
- [ ] Je connais les contre-mesures (désactiver WPS, WPA3, VPN, WIDS)

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | IV 24 bits = réutilisation rapide → analyse statistique |
| 2 | **c** | airmon-ng start = mode monitor obligatoire |
| 3 | **a** | PMKID = cracking sans handshake |
| 4 | **b** | PIN vérifié en 2 × 4 chiffres = 10^4 + 10^4 = 20000 |
| 5 | **b** | SAE/Dragonfly = résistant aux attaques offline |
| 6 | **b** | airodump capture + deauth force le re-handshake |
| 7 | **b** | PMF protège les management frames |
| 8 | **c** | WIDS (Kismet, etc.) détecte les Rogue APs |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 16](./README.md)*
