# 01 — Introduction & Concepts de l'Énumération

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## 📖 Définition

L'**énumération** est le processus d'extraction active d'informations sur un système ou un réseau cible :

- Noms d'utilisateurs & groupes
- Noms de machines
- Ressources et partages réseau
- Services actifs
- Détails SNMP et FQDN
- Tables de routage
- Paramètres d'audit

> ⚠️ Contrairement au scanning, l'énumération établit des **connexions directes** avec la cible — elle est donc détectable et potentiellement illégale sans autorisation.

---

## 🔄 Position dans le Cycle d'Attaque

```
┌─────────────────────────────────────────────────────┐
│  PHASE 1       PHASE 2       PHASE 3       PHASE 4  │
│                                                     │
│ Footprinting → Scanning → Énumération → Exploitation│
│   (passif)     (semi-     (ACTIF)       (exploit)   │
│                actif)                               │
└─────────────────────────────────────────────────────┘
```

---

## 🎯 Informations Collectées

| Catégorie | Exemples |
|-----------|----------|
| **Utilisateurs** | Comptes, groupes, politiques de MdP |
| **Réseau** | Ressources partagées, tables de routage |
| **Services** | Bannières, versions, configurations |
| **Système** | Noms de machines, OS, applications |
| **Infrastructure** | SNMP, FQDN, DNS |

---

## 🛠️ Techniques d'Énumération

### 1️⃣ Extraction via les adresses email
Chaque email contient `username@domainname`. Ce format permet de déduire la convention de nommage des comptes utilisateurs de l'organisation.

```
john.doe@company.com  →  login probable : john.doe ou jdoe
```

### 2️⃣ Exploitation des mots de passe par défaut
De nombreux équipements (routeurs, switches, caméras IP) conservent leurs credentials d'usine.  
Ressources utiles :
- [DefaultPassword.com](https://www.defaultpassword.com)
- [CIRT.net Default Passwords](https://www.cirt.net/passwords)

### 3️⃣ Brute Force Active Directory
Microsoft Active Directory est vulnérable à l'énumération des noms d'utilisateurs lors de la phase de vérification :

```
✅ Compte valide   → "Mot de passe incorrect"
❌ Compte invalide → "Utilisateur inexistant"
```

> 💡 Si la fonctionnalité **"heures de connexion"** est activée, les messages d'erreur diffèrent selon le statut du compte — permettant l'énumération.

### 4️⃣ DNS Zone Transfer
```bash
# Via nslookup
nslookup
> server dns.target.com
> set type=any
> ls -d target.com

# Via dig
dig axfr @dns.target.com target.com
```

Un serveur DNS mal configuré peut retourner :
- Tous les enregistrements DNS
- Liste des sous-domaines
- Adresses IP associées

### 5️⃣ Extraction des groupes Windows
Un attaquant avec un compte AD valide peut extraire les informations de groupe :

```powershell
# Méthode CLI
net group /domain
net localgroup administrators

# Méthode SNMP
# Requête de l'arbre SNMP pour cartographier les ressources
```

### 6️⃣ Extraction SNMP
Les community strings par défaut (`public`, `private`) permettent souvent :
- Lister les utilisateurs
- Cartographier la topologie réseau
- Obtenir les configurations d'équipements

---

## 🔍 IPC$ — Partage Inter-Processus Windows

```
IPC$ (Inter-Process Communication Share)
         ↓
  Session Nulle (Null Session)
         ↓
  Connexion anonyme possible
         ↓
  Énumération des partages, users, policies
```

Une session nulle sur `IPC$` permet de :
- Lister les utilisateurs et groupes
- Énumérer les partages
- Récupérer les politiques de sécurité

```bash
# Établir une session nulle
net use \\TARGET\IPC$ "" /user:""
```

---

## ⚖️ Aspects Légaux

| Contexte | Statut |
|----------|--------|
| Environnement de test autorisé | ✅ Légal |
| Bug bounty avec scope défini | ✅ Légal |
| Sans autorisation écrite | ❌ Illégal |
| Environnement de production non autorisé | ❌ Illégal |

> 🔐 **Toujours obtenir une autorisation écrite** avant toute phase d'énumération.

---

[Suivant → Ports & Services →](./02-ports-services.md)
