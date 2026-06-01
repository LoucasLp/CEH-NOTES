# 🦠 01 — Introduction & Taxonomie des Malwares

> **CEH v13 | Module 07 : Malware Threats**  
> 📌 Section 1/8 — Concepts fondamentaux

---

## 🔑 Définition

> **Malware** (*Malicious Software*) = tout logiciel intentionnellement conçu pour **endommager, exploiter, perturber ou obtenir un accès non autorisé** à des systèmes informatiques.

Le mot est une contraction de **MAL**icious soft**WARE**.

---

## 📐 Pourquoi le CEH insiste sur les Malwares ?

| Raison | Explication |
|--------|-------------|
| 🎯 Outil principal des attaquants | La majorité des cyberattaques utilisent des malwares |
| 🔄 Chaîne d'attaque | Accès → Persistance → Dommage |
| 🔬 Connaissance défensive | Comprendre = mieux détecter et contrer |
| 📋 Poids à l'examen | ~10-15% des questions portent sur ce module |

---

## 🗺️ Taxonomie complète des Malwares

```
MALWARE
├── 🔴 INFECTEURS (nécessitent un hôte)
│   ├── Virus
│   │   ├── Boot Sector Virus
│   │   ├── File Infector Virus
│   │   ├── Macro Virus
│   │   ├── Polymorphic Virus
│   │   └── Metamorphic Virus
│   └── Trojan
│       ├── RAT (Remote Access Trojan)
│       ├── Banking Trojan
│       ├── Downloader Trojan
│       ├── Command Shell Trojan
│       └── Proxy Trojan
│
├── 🟠 AUTO-RÉPLICANTS (indépendants)
│   ├── Worms
│   └── Botnets / Zombies
│
├── 🟡 FURTIFS (persistance & évasion)
│   ├── Rootkits
│   │   ├── Kernel-level
│   │   ├── User-mode
│   │   ├── Firmware/BIOS
│   │   └── Bootkit (MBR)
│   ├── Backdoors
│   └── Fileless Malware
│
├── 💰 EXTORSION
│   ├── Ransomware (Crypto)
│   ├── Locker Ransomware
│   └── RaaS (Ransomware-as-a-Service)
│
└── 🕵️ ESPIONNAGE
    ├── Spyware
    ├── Keyloggers
    ├── Adware
    └── Stalkerware
```

---

## 🧩 Composants d'un malware

Un malware est généralement constitué de plusieurs modules :

| Composant | Rôle |
|-----------|------|
| **Crypter** | Chiffre le malware pour éviter la détection AV |
| **Dropper** | Dépose le payload sur le système cible |
| **Downloader** | Télécharge des composants supplémentaires |
| **Injector** | Injecte du code dans des processus légitimes |
| **Exploit** | Tire parti d'une vulnérabilité pour exécuter du code |
| **Obfuscator** | Cache la véritable intention du code |
| **Payload** | Le code malveillant effectif |
| **C2 Module** | Communication avec le serveur Command & Control |

---

## 🚪 Vecteurs de propagation (Comment le malware se propage)

### Vecteurs numériques
- 📧 **Pièces jointes email** (phishing / spear phishing)
- 🌐 **Drive-by downloads** (sites web malveillants)
- 💾 **Partage de fichiers** (P2P, FTP, SMB, NetBIOS)
- 🔗 **Liens malveillants** dans messages / réseaux sociaux
- 📦 **Logiciels craqués / freeware** de sources non fiables
- 🛡️ **Exploit kits** (Angler, Nuclear, Magnitude)

### Vecteurs physiques
- 🔌 **Clés USB / supports amovibles** (autorun)
- 📀 **CD/DVD** infectés
- 🖨️ **Imprimantes réseau** non sécurisées

### Vecteurs système
- ❌ **Mauvaise gestion des patches** (vulnérabilités non corrigées)
- 🔓 **Mauvaise configuration** des services
- 📱 **Applications mobiles** malveillantes
- ☁️ **Supply chain attacks** (dépendances compromises)

---

## 🌐 Techniques de distribution Web

Les attaquants utilisent des méthodes spécifiques pour piéger les victimes sur Internet :

```
Techniques Web de distribution
│
├── Black Hat SEO → Référencement malveillant pour attirer les victimes
├── Clickjacking social → Piège dans boutons/liens légitimes
├── Spear-Phishing sites → Sites imitant des portails légitimes
├── Malvertising → Publicités infectées sur sites légitimes
├── Compromised sites → Sites légitimes hackés
├── Drive-by Downloads → Téléchargement automatique sans clic
└── Spam emails → Campagnes massives avec pièces jointes
```

---

## 📌 Points clés examen

> ⚠️ **À retenir absolument :**

- Un **virus** requiert une **action humaine** pour se propager
- Un **worm** se propage **automatiquement** sans intervention
- Un **trojan** se **déguise** en programme légitime
- Un **rootkit** se **cache** lui-même et les autres malwares
- Un **ransomware** **chiffre** les données et demande une rançon
- Un **botnet** est un réseau de machines **zombies** contrôlées

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [README](./README.md) | [02 — Trojans & Backdoors](./02_Trojans_et_Backdoors.md) |
