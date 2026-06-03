# 🔴 Module 10 — Concepts Fondamentaux : DoS & DDoS

> **CEH v13 | EC-Council**  
> `#DoS` `#DDoS` `#Disponibilité` `#CIA-Triad`

---

## 📌 Table des matières

1. [Qu'est-ce qu'une attaque DoS ?](#1-quest-ce-quune-attaque-dos)
2. [Qu'est-ce qu'une attaque DDoS ?](#2-quest-ce-quune-attaque-ddos)
3. [Comparatif DoS vs DDoS](#3-comparatif-dos-vs-ddos)
4. [Objectifs et motivations d'un attaquant](#4-objectifs-et-motivations)
5. [Impact sur les systèmes cibles](#5-impact-sur-les-systèmes-cibles)
6. [Cas réels notables](#6-cas-réels-notables)

---

## 1. Qu'est-ce qu'une attaque DoS ?

Une attaque **Denial-of-Service (DoS)** consiste à rendre un service, une machine ou un réseau **indisponible** pour ses utilisateurs légitimes en submergeant ses ressources depuis **une seule source**.

```
   [Attaquant]
       │
       │ Flood massif de requêtes
       ▼
   [Serveur Cible]  ←── Ressources épuisées (CPU, RAM, Bande passante)
       │
       ✗ Utilisateurs légitimes bloqués
```

### Mécanismes d'une attaque DoS

| Mécanisme | Description |
|-----------|-------------|
| **Traffic Flooding** | Envoi massif de paquets pour saturer la bande passante |
| **Service Overload** | Surcharge d'un service spécifique (ex: IRC, HTTP) |
| **Corrupt Packets** | Envoi de paquets malformés exploitant des vulnérabilités TCP/IP |
| **Unexpected Interactions** | Interactions non prévues exploitant des bugs logiciels |
| **Infinite Loop** | Commandes forçant le système dans une boucle infinie |

> ⚠️ **Rappel CEH** : Une attaque DoS cible la **Disponibilité** (le "A" du triptyque CIA — Confidentiality, Integrity, **Availability**).

---

## 2. Qu'est-ce qu'une attaque DDoS ?

Une attaque **Distributed Denial-of-Service (DDoS)** est une version amplifiée du DoS utilisant **des milliers de machines compromises** (botnets) pour submerger simultanément la cible.

```
  [Attaquant / Handler]
         │
    ┌────┴────┐
    ▼         ▼
[Bot 1]   [Bot 2]   [Bot 3] ... [Bot N]     ← Réseau de zombies (Botnet)
    │         │         │           │
    └────┬────┴─────────┘           │
         └──────────────────────────┘
                      │
                      │  Flood coordonné
                      ▼
              [Serveur Cible]  ✗
```

### Architecture d'un réseau DDoS

```
Attaquant
   └── C&C Server (Command & Control)
          ├── Handler 1
          │     ├── Zombie 1.1
          │     ├── Zombie 1.2
          │     └── Zombie 1.3
          └── Handler 2
                ├── Zombie 2.1
                └── Zombie 2.2
```

- **C&C (Command & Control)** : Serveur central contrôlant les bots
- **Handlers** : Machines intermédiaires relayant les ordres
- **Zombies/Bots** : Machines compromises à l'insu de leurs propriétaires

---

## 3. Comparatif DoS vs DDoS

| Critère | DoS | DDoS |
|---------|-----|------|
| **Sources** | Une seule | Multiples (milliers) |
| **Facilité de blocage** | Facile (bloquer 1 IP) | Difficile (IPs multiples) |
| **Puissance** | Limitée | Massive |
| **Détection** | Plus simple | Complexe |
| **Infrastructure nécessaire** | Minimale | Botnet requis |
| **Exemple d'outil** | Hping3 | LOIC, HOIC |

---

## 4. Objectifs et motivations

Les attaquants lancent des attaques DoS/DDoS pour des raisons variées :

```
┌─────────────────────────────────────────────┐
│           MOTIVATIONS D'UN ATTAQUANT        │
├─────────────────┬───────────────────────────┤
│ 💰 Financier    │ Extorsion (ransom DDoS)    │
│ 🏆 Compétitif   │ Sabotage d'un concurrent  │
│ 🎯 Idéologique  │ Hacktivisme               │
│ 🔐 Camouflage   │ Distraction pendant        │
│                 │ une autre attaque          │
│ 😠 Vengeance    │ Représailles personnelles  │
│ 🧪 Test         │ Démonstration de capacité  │
└─────────────────┴───────────────────────────┘
```

---

## 5. Impact sur les systèmes cibles

### Ressources ciblées

```
CPU ████████████████████ Épuisement des cycles processeur
RAM ████████████████░░░░ Saturation de la mémoire
BW  ████████████████████ Saturation de la bande passante
CON █████████████░░░░░░░ Tables de connexions pleines
```

### Conséquences business

| Impact | Description |
|--------|-------------|
| **Perte de revenus** | Service indisponible = transactions impossibles |
| **Atteinte à la réputation** | Perte de confiance des clients |
| **Coûts de récupération** | Infrastructure, personnel, forensics |
| **Pénalités légales** | SLA non respectés (contrats) |
| **Opportunité pour d'autres attaques** | Distraction des équipes sécurité |

---

## 6. Cas réels notables

### 🔴 Attaque Dyn DNS — Octobre 2016

```
Type    : DDoS via Botnet IoT (Mirai)
Cibles  : Dyn (DNS provider)
Impact  : Twitter, Spotify, Netflix, GitHub hors ligne
Trafic  : ~1,2 Tbps
Vecteur : Caméras IP et routeurs compromis
```

### 🔴 Attaque GitHub — Février 2018

```
Type    : DDoS par amplification Memcached
Cible   : GitHub.com
Impact  : Indisponibilité ~10 minutes
Trafic  : 1,35 Tbps (record à l'époque)
Facteur : Amplification x51 000
```

### 🔴 Attaque AWS — Février 2020

```
Type    : DDoS volumétrique
Cible   : Client AWS Shield
Trafic  : 2,3 Tbps (record mondial)
Durée   : 3 jours
Méthode : Amplification CLDAP
```

---

## 🧠 Points clés à retenir pour l'examen

> 📝 **À mémoriser absolument :**

- DoS = **1 source**, DDoS = **sources multiples**
- Les attaques DoS/DDoS exploitent les vulnérabilités du **modèle TCP/IP**
- Objectif principal : altérer la **disponibilité** (Availability)
- Les **botnets** sont contrôlés via des serveurs **C&C (Command & Control)**
- La mesure des attaques : **bps** (volumétrique), **pps** (protocole), **rps** (applicative)

---

*⬅️ [README](./README.md) | ➡️ [Catégories d'attaques](./02_categories_attaques.md)*
