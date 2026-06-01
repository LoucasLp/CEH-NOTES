# 🐴 02 — Trojans & Backdoors

> **CEH v13 | Module 07 : Malware Threats**  
> 📌 Section 2/8 — Accès non autorisé & Contrôle distant

---

## 🔑 Définition — Trojan

> Un **Trojan** (cheval de Troie) est un programme qui **se déguise en logiciel légitime** tout en exécutant des actions malveillantes en arrière-plan, **à l'insu de l'utilisateur**.

**Origine** : Référence à la stratégie grecque du cheval de Troie — apparence inoffensive, but destructeur.

---

## 🎭 Comment un Trojan infecte-t-il un système ?

```
Étapes d'infection par Trojan
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[1] Création
    └─ Attaquant crée le Trojan (outil : msfvenom, RAT kits...)

[2] Distribution
    └─ Email, lien, logiciel craqué, faux update

[3] Exécution
    └─ Victime lance le fichier (pense que c'est légitime)

[4] Installation
    └─ Trojan s'installe, crée persistance (clé registre, service)

[5] Communication
    └─ Connexion au serveur C2 (Command & Control)

[6] Exploitation
    └─ Vol de données, backdoor, DDoS, installation autre malware
```

---

## 🗂️ Types de Trojans

### 🖥️ RAT — Remote Access Trojan
> Donne à l'attaquant un **contrôle total** sur le système infecté.

| Capacité | Description |
|---------|-------------|
| Accès desktop | Voir et contrôler l'écran |
| Gestion fichiers | Upload / Download / Suppression |
| Keylogging | Capture des frappes clavier |
| Webcam/Micro | Surveillance à distance |
| Shell distant | Exécution de commandes |
| Exfiltration | Vol de données sensibles |

**Exemples de RAT connus :**
- `DarkComet`, `njRAT`, `Quasar RAT`, `Remcos`, `AsyncRAT`

---

### 🏦 Banking Trojan
> Cible spécifiquement les **informations bancaires** et de paiement.

- Capture formulaires de connexion (form grabbing)
- Injecte du code dans les pages de banque (man-in-the-browser)
- Redirige les transactions

**Exemples :** `Zeus`, `Emotet`, `TrickBot`, `Dridex`

---

### 📥 Downloader Trojan
> Sa seule mission : **télécharger et installer** d'autres malwares.

```
Downloader Trojan
      │
      ├─→ Ransomware
      ├─→ Credential Stealer
      ├─→ Crypto Miner
      └─→ RAT
```

**Exemples :** `Emotet` (aussi utilisé comme downloader)

---

### 💻 Command Shell Trojan
> Ouvre un **shell distant** (cmd, PowerShell, bash) permettant d'exécuter des commandes.

---

### 🌐 Proxy Trojan
> Transforme la machine infectée en **proxy** pour que l'attaquant masque son trafic.

---

### 🔐 HTTP/HTTPS Trojan
> Utilise le port 80/443 pour **bypasser les firewalls** (trafic légitime en apparence).

---

### 💣 Destructive Trojan
> Conçu pour **détruire** des données, corrompre le système de fichiers.

---

### 🔔 Notification Trojan
> Alerte l'attaquant dès que la cible est connectée (online notifier).

---

## 🚪 Backdoors

> Une **backdoor** est un mécanisme d'accès **caché et persistant** qui contourne l'authentification normale.

### Différence Trojan vs Backdoor

| | Trojan | Backdoor |
|--|--------|----------|
| **Nature** | Programme déguisé | Point d'accès caché |
| **Objectif** | Déclencher une action | Maintenir l'accès |
| **Détection** | Plus visible | Très furtif |
| **Persistance** | Variable | Par définition persistant |

---

### Types de Backdoors

| Type | Description |
|------|-------------|
| **Shell Backdoor** | Accès shell distant (netcat, bash) |
| **Web Shell** | Script PHP/ASP sur serveur web compromis |
| **Hardware Backdoor** | Intégré dans firmware/matériel |
| **Cryptographic Backdoor** | Faiblesse intentionnelle dans les algos de chiffrement |
| **OS Backdoor** | Modifie le système d'exploitation |

---

### 🕸️ Web Shells courants

```bash
# Exemple simple de web shell PHP
<?php system($_GET['cmd']); ?>

# Accès via navigateur
http://victim.com/shell.php?cmd=whoami
```

**Web Shells connus :** `c99`, `r57`, `China Chopper`, `B374k`

---

## 🔧 Outils d'analyse et de création (à connaître pour l'examen)

### Outils légaux / défensifs
| Outil | Usage |
|-------|-------|
| `Autoruns` (Sysinternals) | Détecter persistances au démarrage |
| `Process Monitor` | Surveiller activité processus |
| `Wireshark` | Analyser trafic C2 |
| `NetStat` | Identifier connexions sortantes suspectes |

### Outils offensifs (contexte éducatif)
| Outil | Description |
|-------|-------------|
| `Metasploit msfvenom` | Génération de payloads Trojan |
| `njRAT` | RAT populaire ciblant Windows |
| `Cobalt Strike` | Framework C2 professionnel (red teams) |

---

## 🛡️ Contre-mesures

```
PRÉVENTION
├── Ne jamais ouvrir des pièces jointes inconnues
├── Télécharger uniquement depuis sources officielles
├── Activer UAC (User Account Control)
└── Désactiver l'autorun USB

DÉTECTION
├── Surveiller les connexions réseau sortantes (NetStat, Wireshark)
├── Inspecter les clés registre de démarrage (Run, RunOnce)
├── Analyser les processus actifs (Process Explorer)
└── IDS/IPS pour détecter trafic C2 anormal

RÉPONSE
├── Isoler la machine immédiatement
├── Analyser les logs système et réseau
├── Supprimer les entrées de persistance
└── Restaurer depuis une sauvegarde propre
```

---

## 📌 Points clés examen

> ⚠️ **Questions fréquentes :**

- Un RAT fournit un **accès complet à distance** (pas juste une backdoor)
- `Zeus` et `Emotet` sont des **Banking Trojans**
- Les Trojans se propagent principalement via **ingénierie sociale**
- Une backdoor n'est pas forcément un Trojan — elle peut être installée directement
- Port 80/443 utilisé par certains Trojans pour **bypasser les firewalls**

---

## 🔗 Navigation

| ← Précédent | Suivant → |
|-------------|-----------|
| [01 — Introduction](./01_Introduction_et_Types.md) | [03 — Virus & Worms](./03_Virus_et_Vers.md) |
