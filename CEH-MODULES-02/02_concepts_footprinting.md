# 📌 02 — Concepts du Footprinting

> **Module 2 — Footprinting & Reconnaissance** | CEH v13

---

## 🔍 Qu'est-ce que le Footprinting ?

Le footprinting (ou **empreinte numérique**) est la **phase préparatoire** du hacking. L'attaquant collecte le maximum d'informations sur la cible **avant** de lancer une attaque.

```
┌─────────────────────────────────────────────────┐
│  OBJECTIF : Construire un profil complet         │
│  de l'organisation cible                         │
│                                                  │
│  → Plage IP, espaces de noms, employés           │
│  → Infrastructure réseau                         │
│  → Technologies utilisées                        │
└─────────────────────────────────────────────────┘
```

---

## 🔄 Les 2 Types de Footprinting

### 🟡 Footprinting Passif

> **Aucune interaction directe** avec la cible

- Techniquement plus difficile à réaliser
- Indétectable car aucun trafic n'est envoyé vers la cible
- Utilise uniquement des **informations publiques archivées**

**Méthodes :**
- 🔎 Open-Source Intelligence (OSINT)
- 💾 Bases de données propriétaires / services payants
- 🤝 Partage de renseignements avec des partenaires / groupes industriels
- 🌐 Moteurs de recherche, réseaux sociaux

---

### 🔴 Footprinting Actif

> **Interaction directe** avec la cible — peut être détecté

- Laisse des traces dans les logs de la cible
- Nécessite plus de préparation
- Fournit des informations plus précises et récentes

**Méthodes :**
- 🔬 Interrogation DNS
- 🎭 Social engineering
- 🔌 Scan réseau / ports
- 👤 Énumération des utilisateurs et services

---

## 📊 Informations Collectées lors du Footprinting

### 🏢 Informations sur l'Organisation

| Type | Exemples |
|------|---------|
| Employés | Noms, contacts, fonctions, expérience |
| Coordonnées | Adresses, téléphones, mobile |
| Structure | Agences, partenaires, filiales |
| Historique | Background de l'organisation |
| Technique | Technologies web utilisées |
| Documents | Communiqués, brevets, marques, documents légaux |

> ⚠️ Ces informations permettent d'identifier des cibles pour des **attaques d'ingénierie sociale**

---

### 🌐 Informations Réseau

| Type | Exemples |
|------|---------|
| Domaines | Domaines et sous-domaines |
| Infrastructure | Blocs réseau, topologie, routeurs, pare-feux |
| Connectivité | Adresses IP des systèmes accessibles |
| DNS | Enregistrements DNS, données Whois |

---

### 💻 Informations Système

| Type | Exemples |
|------|---------|
| Serveur Web | Système d'exploitation, localisation |
| Contacts | Adresses email publiques |
| Accès | Noms d'utilisateurs, mots de passe (si exposés) |

---

## 🗺️ Carte des Techniques de Footprinting

```
                    ┌─────────────────────────────────────┐
                    │     FOOTPRINTING TECHNIQUES          │
                    └──────────────┬──────────────────────┘
                                   │
       ┌───────────────────────────┼───────────────────────────┐
       ▼                           ▼                           ▼
┌──────────────┐          ┌──────────────────┐        ┌──────────────┐
│  Moteurs de  │          │  Réseaux Sociaux │        │    WHOIS     │
│  Recherche   │          │  & People Search │        │  Footprint   │
└──────┬───────┘          └────────┬─────────┘        └──────┬───────┘
       │                           │                          │
  Google Dorks              LinkedIn, Twitter           IP Geolocation
  SHODAN, Censys            Sherlock, BuzzSumo          RIRs (ARIN...)
       │                                                      │
┌──────▼───────┐          ┌──────────────────┐        ┌──────▼───────┐
│ DNS / Réseau │          │   Dark Web       │        │  Email       │
│ Footprinting │          │   Footprinting   │        │  Tracking    │
└──────────────┘          └──────────────────┘        └──────────────┘
    DNSRecon                   Tor Browser              eMailTrackerPro
    Traceroute                 OnionLand                MxToolbox
```

---

## 🎯 Phases du Footprinting dans le cycle CEH

```
Phase 1 : RECONNAISSANCE
│
├── Footprinting Passif
│   ├── Moteurs de recherche (Google, Bing, SHODAN)
│   ├── Réseaux sociaux (LinkedIn, Twitter, Facebook)
│   ├── WHOIS & DNS (informations publiques)
│   ├── Job sites (LinkedIn, Glassdoor)
│   └── Dark Web (Tor Browser)
│
└── Footprinting Actif
    ├── DNS Interrogation (Fierce, SecurityTrails)
    ├── Traceroute (NetScanTools Pro, PingPlotter)
    ├── Social Engineering
    └── Scan réseau/ports
```

---

> ⬅️ [01 - Introduction & Phases](./01_introduction_phases.md) | ➡️ [03 - Search Engines](./03_search_engines.md)
