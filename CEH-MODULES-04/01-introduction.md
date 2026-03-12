# 🔍 01 — Introduction à l'Énumération

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

## 📖 Définition

L'**énumération** est le processus d'extraction active d'informations sur une cible :

- 👤 Noms d'utilisateurs
- 🖥️ Noms de machines
- 🌐 Ressources et partages réseau
- ⚙️ Services en cours d'exécution
- 🗺️ Tables de routage

> ⚠️ **Différence clé** : Contrairement au scanning, l'énumération établit des **connexions actives** avec le système cible et envoie des requêtes dirigées.

---

## 🎯 Informations collectées

```
┌─────────────────────────────────────────────────────┐
│              ÉNUMÉRATION — CIBLES                   │
├─────────────────────────────────────────────────────┤
│  • Ressources et partages réseau                    │
│  • Tables de routage                                │
│  • Paramètres d'audit et de service                 │
│  • Détails SNMP et FQDN                             │
│  • Noms de machines                                 │
│  • Utilisateurs et groupes                          │
│  • Applications et bannières                        │
└─────────────────────────────────────────────────────┘
```

---

## 🛠️ Techniques d'énumération

### 1️⃣ Extraction via les IDs email
Chaque adresse email `username@domainname` révèle un nom d'utilisateur valide.

### 2️⃣ Mots de passe par défaut
Les utilisateurs ignorent souvent les recommandations de changement des credentials par défaut du fabricant → vecteur d'attaque facile.

### 3️⃣ Brute-Force Active Directory
Microsoft Active Directory est vulnérable à l'énumération de noms d'utilisateurs lors de la vérification des entrées :

```
[Feature "logon hours" activée]
    ↓
Tentatives d'authentification → Messages d'erreur DIFFÉRENTS
    ↓
Permet d'identifier les usernames valides
    ↓
Brute-force des mots de passe correspondants
```

### 4️⃣ DNS Zone Transfer
Si le serveur DNS **mal configuré** autorise les transferts de zone :
- Liste complète des hôtes nommés
- Sous-zones et adresses IP associées
- Outils : `nslookup`, `dig`

### 5️⃣ Extraction de groupes Windows
Nécessite un compte enregistré dans l'Active Directory → extraction des groupes via interface Windows ou ligne de commande.

### 6️⃣ Extraction via SNMP
Deviner les community strings (lecture seule / lecture-écriture) via l'API SNMP pour extraire des noms d'utilisateurs.

### 7️⃣ Topologie réseau via SNMP
Interrogation méthodique de l'arbre SNMP pour obtenir des informations détaillées sur la topologie réseau.

---

## 🌐 Environnement d'énumération

```
┌─────────────────────────────────────────────────────┐
│                   INTRANET                          │
│                                                     │
│   Attaquant ──────────→ Connexion active            │
│              ←────────── Réponse avec infos         │
│                                                     │
│   ⚠️  Les techniques d'énumération fonctionnent    │
│      principalement en environnement INTRANET       │
└─────────────────────────────────────────────────────┘
```

---

## ⚖️ Aspect légal

> 🔴 **ATTENTION** : Les activités d'énumération peuvent être **illégales** selon les politiques de l'organisation et les lois en vigueur.

Un pentesteur éthique doit **toujours** :
1. Obtenir une **autorisation écrite** préalable
2. Définir clairement le **périmètre** de l'énumération
3. **Documenter** toutes les activités
4. Respecter les lois locales et internationales

---

## 🔄 Place dans la méthodologie CEH

```
Reconnaissance (passive)
        ↓
Scanning & Footprinting
        ↓
◄══ ÉNUMÉRATION (Module 4) ══►  ← Nous sommes ici
        ↓
Analyse des vulnérabilités
        ↓
Exploitation
```

---

## 🎯 Objectif final de l'énumération

```
Énumération → Identification des vulnérabilités → Attaques par mot de passe
                                                → Accès non autorisé
                                                → Exploitation du système
```

---

> 📝 **Note** : Les modules précédents couvrent la collecte d'informations **sans activité illégale**. L'énumération franchit la limite des connexions actives → autorisation obligatoire.

---

[→ Suivant : Ports & Services](./02-ports-services.md)
