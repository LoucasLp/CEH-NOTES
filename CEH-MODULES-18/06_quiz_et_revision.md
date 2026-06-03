# 📝 Module 18 — Quiz & Révision : IoT & OT Hacking

> **CEH v13 | EC-Council**  
> `#Quiz` `#IoT` `#SCADA` `#Shodan` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- IoT = couche Perception + Réseau + Application
- SCADA/ICS = systèmes de contrôle industriels (criticité haute)
- OWASP IoT I1 = credentials par défaut = vulnérabilité #1
- Modbus port 502 = aucune authentification ni chiffrement
- MQTT port 1883 = non chiffré ; port 8883 = TLS
- Shodan = moteur de recherche pour IoT/SCADA exposés
- Binwalk = extraction et analyse de firmware
- Stuxnet = premier cyberweapon OT (PLCs Siemens)

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **SCADA** | Supervision et contrôle des processus industriels |
| **PLC** | Automate programmable contrôlant des équipements physiques |
| **HMI** | Interface homme-machine pour les opérateurs SCADA |
| **Modbus** | Protocole industriel sans auth ni chiffrement (port 502) |
| **MQTT** | Protocole IoT pub/sub (1883 non chiffré, 8883 TLS) |
| **Binwalk** | Outil d'analyse et d'extraction de firmware |
| **Shodan** | Moteur de recherche pour appareils IoT/SCADA exposés |
| **Stuxnet** | Premier cyberweapon ciblant des PLCs (Iran 2010) |
| **Air gap** | Séparation physique du réseau OT d'Internet |
| **JTAG/UART** | Interfaces physiques pour accès bas-niveau au firmware |

---

## 🎯 Exam Tips

1. **I1 (Default Passwords)** = vulnérabilité IoT #1 selon OWASP
2. **Modbus** = pas d'authentification → n'importe qui peut envoyer des commandes
3. **MQTT `#`** = abonnement à tous les topics (si broker non sécurisé)
4. **Shodan** = `port:502`, `port:1883`, `product:SCADA`
5. **Binwalk** = outil de référence pour l'analyse firmware
6. **Stuxnet** = vecteur clé USB + 4 zero-days + PLCs Siemens
7. **OT priorité** = Disponibilité > Intégrité > Confidentialité
8. **Air gap** = souvent contourné via clé USB ou connexion de maintenance

---

## 📋 Quiz QCM

**Question 1.** Quelle est la vulnérabilité IoT #1 selon l'OWASP IoT Top 10 ?
- a) Firmware non chiffré
- b) Credentials faibles ou par défaut
- c) Manque de mises à jour
- d) Communications non sécurisées

**Question 2.** Shodan est principalement utilisé pour :
- a) Scanner des ports locaux
- b) Décompiler des firmwares
- c) Trouver des appareils IoT/SCADA exposés sur Internet
- d) Analyser des protocoles industriels

**Question 3.** Le protocole Modbus est vulnérable principalement car :
- a) Il utilise des clés de chiffrement faibles
- b) Il n'a pas de mécanisme d'authentification ni de chiffrement
- c) Il n'est compatible qu'avec les vieux équipements
- d) Il utilise UDP au lieu de TCP

**Question 4.** Stuxnet est célèbre pour avoir :
- a) Attaqué des serveurs cloud Amazon
- b) Ciblé les centrifugeuses iraniennes via des PLCs Siemens
- c) Créé le premier botnet IoT
- d) Compromis des réseaux MQTT non sécurisés

**Question 5.** Binwalk est principalement utilisé pour :
- a) Scanner des réseaux IoT
- b) Exploiter les protocoles Modbus
- c) Analyser et extraire le contenu de firmwares IoT
- d) Tester les brokers MQTT

**Question 6.** Le port MQTT par défaut non chiffré est :
- a) 80
- b) 8883
- c) 1883
- d) 502

**Question 7.** En OT/SCADA, quelle propriété de sécurité est prioritaire ?
- a) Confidentialité
- b) Intégrité
- c) Disponibilité
- d) Non-répudiation

**Question 8.** Quelle commande permet de s'abonner à tous les topics d'un broker MQTT ?
- a) `mosquitto_sub -t "all"`
- b) `mosquitto_sub -t "*"`
- c) `mosquitto_sub -t "#"`
- d) `mosquitto_sub -t "+"

---

## ✅ Points de révision — Checklist

- [ ] Je connais l'architecture IoT 3 couches
- [ ] Je comprends la différence SCADA / ICS / PLC
- [ ] Je connais l'OWASP IoT Top 10 (au moins I1, I2, I4, I7, I10)
- [ ] Je sais utiliser Shodan pour trouver des appareils IoT exposés
- [ ] Je comprends le fonctionnement de Binwalk
- [ ] Je sais pourquoi Modbus est dangereux
- [ ] Je connais les attaques MQTT (abonnement #, publication non autorisée)
- [ ] Je comprends Stuxnet et son vecteur d'infection (USB)
- [ ] Je connais les contre-mesures (segmentation, VLAN, MQTT TLS)

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | I1 = Weak/Guessable/Hardcoded Passwords |
| 2 | **c** | Shodan = moteur de recherche appareils connectés |
| 3 | **b** | Modbus = aucune auth ni chiffrement |
| 4 | **b** | Stuxnet = centrifugeuses + PLCs Siemens + Iran |
| 5 | **c** | Binwalk = analyse et extraction firmware |
| 6 | **c** | MQTT standard = port 1883 (non chiffré) |
| 7 | **c** | OT = Disponibilité avant tout |
| 8 | **c** | `#` = wildcard multi-level MQTT |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 18](./README.md)*
