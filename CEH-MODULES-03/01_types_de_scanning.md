# 📡 01 — Types de Scanning & Flags TCP

> **Module 3 — CEH v13** | [← Retour au sommaire](./README.md)

---

## 1. Vue d'ensemble du Network Scanning

Le **network scanning** est un ensemble de procédures permettant d'**identifier les hôtes, les ports et les services** sur un réseau. C'est l'une des composantes clés de la **collecte d'informations** (information gathering).

```
Attaquant  ──── TCP/IP Probes ────►  Réseau cible
           ◄─── Network Info  ────
```

### Outils principaux utilisés
- **Nmap** — Scanner de réseau polyvalent
- **Hping3** — Outil de crafting de paquets
- **Metasploit** — Framework de pentesting
- **NetScanTools Pro** — Suite d'investigation réseau

### Objectifs du Network Scanning
1. Découvrir les hôtes actifs, adresses IP et ports ouverts
2. Identifier les OS et architectures système
3. Découvrir les services en cours d'exécution
4. Détecter les vulnérabilités des hôtes actifs

---

## 2. Les 3 Types de Scanning

### 🔵 Port Scanning
> *Liste les ports ouverts et les services associés*

Le port scanning consiste à envoyer une séquence de messages pour vérifier les services actifs sur un système cible. Il sonde les ports **TCP et UDP** pour déterminer si des services sont en écoute.

**Risque :** Les services en écoute peuvent permettre à des utilisateurs non autorisés de :
- Mal configurer des systèmes
- Exécuter des logiciels vulnérables

---

### 🟢 Network Scanning
> *Liste les hôtes actifs et les adresses IP*

Procédure d'identification des hôtes actifs sur un réseau, soit pour les attaquer, soit pour évaluer la sécurité du réseau.

---

### 🔴 Vulnerability Scanning
> *Révèle la présence de failles connues*

Méthode de vérification de l'exploitabilité d'un système en identifiant ses vulnérabilités.

**Composants d'un scanner de vulnérabilités :**

| Composant | Rôle |
|-----------|------|
| **Scanning Engine** | Logique de lecture des exploits et analyse des réponses |
| **Catalog** | Liste des fichiers communs vulnérables et des exploits connus |

---

## 3. Flags TCP — Référence complète

Les flags TCP sont des bits de contrôle dans l'en-tête TCP qui indiquent l'état ou des informations particulières sur un segment.

| Flag | Nom complet | Valeur | Description |
|------|------------|--------|-------------|
| **SYN** | Synchronize | `1` | Initie une connexion (3-way handshake). Notifie la transmission d'un nouveau numéro de séquence. |
| **ACK** | Acknowledgement | `1` | Confirme la réception et identifie le prochain numéro de séquence attendu. |
| **PSH** | Push | `1` | Demande au système de transmettre immédiatement les données en buffer à l'application réceptrice. Utilisé au début et à la fin d'un transfert. |
| **URG** | Urgent | `1` | Traitement prioritaire des données. Toutes les autres opérations sont suspendues. |
| **FIN** | Finish | `1` | Annonce la fin de la transmission. La connexion établie par SYN est terminée. |
| **RST** | Reset | `1` | En cas d'erreur dans la connexion courante. Utilisé par les attaquants pour scanner les hôtes et identifier les ports ouverts. |

---

## 4. Le Three-Way Handshake TCP

```
Client                          Serveur
  │                                │
  │──── SYN (seq=x) ──────────────►│
  │                                │
  │◄─── SYN+ACK (seq=y, ack=x+1) ─│
  │                                │
  │──── ACK (ack=y+1) ────────────►│
  │                                │
  │      [Connexion établie]       │
```

> 💡 **À retenir :** La connaissance des flags TCP est fondamentale pour comprendre les différentes techniques de scanning abordées dans ce module.

---

## 5. Résumé visuel

```
┌─────────────────────────────────────────────┐
│           TYPES DE SCANNING                 │
├──────────────┬──────────────┬───────────────┤
│ PORT         │ NETWORK      │ VULNERABILITY │
│ SCANNING     │ SCANNING     │ SCANNING      │
├──────────────┼──────────────┼───────────────┤
│ Ports TCP/UDP│ Hôtes actifs │ Failles        │
│ Services     │ Adresses IP  │ Exploits       │
│ Nmap, Hping3 │ Nmap, Angry  │ Nessus,        │
│              │ IP Scanner   │ OpenVAS        │
└──────────────┴──────────────┴───────────────┘
```

---

[← Retour au sommaire](./README.md) | [→ Outils de Scanning](./02_outils_de_scanning.md)
