# 📌 06 — Réseau, Email & Réseaux Sociaux

> **Module 2 — Footprinting & Reconnaissance** | CEH v13

---

## 🌐 Network Footprinting

### Localisation de la Plage Réseau

```
Objectif : Déterminer la plage d'adresses IP de la cible
      ↓
Ressources ARIN/RIR (whois.arin.net)
      ↓
Informations obtenues :
  ├── Structure du réseau
  ├── Machines actives
  ├── Topologie réseau
  ├── Dispositifs de contrôle d'accès
  └── OS utilisé dans le réseau cible
```

### Plages IP Privées (RFC 1918)

| Plage | Préfixe CIDR |
|-------|-------------|
| `10.0.0.0 – 10.255.255.255` | 10/8 |
| `172.16.0.0 – 172.31.255.255` | 172.16/12 |
| `192.168.0.0 – 192.168.255.255` | 192.168/16 |

> 💡 Obtenir des adresses IP privées d'un réseau cible peut aider à comprendre la topologie interne.

---

## 🗺️ Traceroute

### Principe de fonctionnement

Le **Traceroute** exploite le champ **TTL (Time to Live)** de l'en-tête IP pour tracer le chemin des paquets réseau.

```
┌────────┐   TTL=1    ┌─────────┐   TTL=2    ┌─────────┐        ┌──────────┐
│ Source │ ─────────► │ Router1 │ ─────────► │ Router2 │ ──...──► │  Target  │
└────────┘            └─────────┘            └─────────┘        └──────────┘
                      ICMP Error              ICMP Error         ICMP Reply
                      Time Exceeded           Time Exceeded
```

**Mécanisme :**
1. Paquet envoyé avec TTL=1 → Router 1 répond avec "Time Exceeded"
2. Paquet envoyé avec TTL=2 → Router 2 répond avec "Time Exceeded"
3. Continue jusqu'à atteindre la destination → Réponse ICMP normale

**Informations collectées :**
- Adresses IP des routeurs intermédiaires
- Noms DNS des routeurs
- Round-trip time (RTT) à chaque saut
- Localisation géographique des hops
- Affiliations réseau

---

### 3 Types de Traceroute

#### 1. ICMP Traceroute (Windows — défaut)
```cmd
C:\> tracert 216.239.36.10
C:\> tracert www.google.com
```

#### 2. TCP Traceroute (contourne les blocages ICMP)
```bash
sudo tcptraceroute www.google.com
```
> Utilisé car de nombreux équipements **bloquent les messages ICMP** — aussi appelé "Layer 4 traceroute"

#### 3. UDP Traceroute (Linux — défaut)
```bash
traceroute www.google.com
```

---

### Analyse de Traceroute — Exemple Pratique

```
Résultats :
  traceroute 1.10.10.20    → avant-dernier hop : 1.10.10.1
  traceroute 1.10.20.10    → 3ème avant-dernier : 1.10.10.1
  traceroute 1.10.20.10    → avant-dernier hop : 1.10.10.50
  traceroute 1.10.20.15    → 3ème avant-dernier : 1.10.10.1
  traceroute 1.10.20.15    → avant-dernier hop : 1.10.10.50
```

**Topologie déduite :**
```
Hacker ──► Internet ──► 1.10.10.1 (Router) ──► 1.10.10.50 (Firewall)
                                                ├──► 1.10.10.20 (Bastion Host)  DMZ ZONE
                                                ├──► 1.10.20.10 (Web Server)
                                                └──► 1.10.20.15 (Mail Server)
```

---

### Outils Traceroute

| Outil | Source | Fonctionnalités |
|-------|--------|-----------------|
| **NetScanTools Pro** | netscantools.com | ICMP/UDP/TCP, localisation IPv4, analyse de réseau |
| **PingPlotter** | pingplotter.com | Collecte de données, graphiques, latence/pertes |
| **Traceroute NG** | – | Traceroute avancé, détection de problèmes |
| **tracert** | Windows built-in | Traceroute ICMP standard Windows |

**Fonctionnalités communes :**
- Traceroute hop-by-hop
- Tracé inverse (reverse tracing)
- Analyse historique
- Rapport de perte de paquets
- Reverse DNS
- Ping plotting
- Sondage de ports
- Détection de problèmes réseau

---

## 📧 Email Footprinting & Tracking

### Qu'est-ce que le Email Tracking ?

> Surveillance des emails d'un utilisateur via des **enregistrements horodatés** révélant date/heure d'ouverture

**Informations collectables :**

| Information | Description |
|-------------|-------------|
| **IP de destination** | Adresse IP du système du destinataire |
| **Géolocalisation** | Localisation estimée sur carte |
| **Email reçu/lu** | Notification de réception et lecture |
| **Durée de lecture** | Temps passé à lire le mail |
| **Proxy Detection** | Type de serveur utilisé par le destinataire |
| **Liens cliqués** | Vérification des liens dans l'email |
| **OS & Navigateur** | Système et browser du destinataire |
| **Forwarding** | Si l'email a été transféré à quelqu'un d'autre |
| **Type d'appareil** | Desktop, mobile, tablet |
| **Chemin de routage** | Route email via les MTA (Mail Transfer Agents) |

---

### 📋 En-tête Email — Structure

L'en-tête d'un email contient :

```
From:           → Adresse de l'expéditeur + serveur mail
Return-Path:    → Adresse IP de l'expéditeur
Received:       → Serveurs de messagerie traversés
Date/Time:      → Horodatage d'envoi et réception
Authentication: → DKIM, SPF, ARC — système d'authentification
Message-ID:     → Identifiant unique du message (mx.google.com)
```

**Informations extraites :**
- Serveur mail de l'expéditeur
- Date/heure de réception par les serveurs
- Système d'authentification du serveur expéditeur
- Date/heure d'envoi
- Identifiant unique du message
- Nom complet de l'expéditeur
- Adresse IP et serveur d'envoi

---

### Outils Email Tracking

| Outil | Source | Fonctionnalités |
|-------|--------|-----------------|
| **eMailTrackerPro** | emailtrackerpro.com | Localisation géographique, IP, sauvegarde des traces |
| **IP2LOCATION Email Header Tracer** | ip2location.com | Analyse en-têtes, traçage du chemin réseau |
| **MxToolbox** | mxtoolbox.com | Analyse headers, blacklists, DNS |
| **DNS Checker Email Header Analyzer** | – | Analyse complète des en-têtes |
| **Social Catfish** | – | Identification de l'expéditeur |
| **Holehe** | – | Vérification si email lié à des comptes |

---

### Clients Email courants (référencés CEH)

```
eM Client         SmarterMail Webmail
Mailbird          Outlook
Hiri              Apple Mail
Mozilla Thunderbird   ProtonMail
Spike             AOL Mail
Claws Mail        Tuta
```

---

## 🎭 Techniques d'Ingénierie Sociale

### Eavesdropping (Écoute clandestine)

> Interception de communications (audio, vidéo, texte) **sans consentement**

- Écoute téléphonique
- Interception de messagerie instantanée / fax
- Capture de trafic réseau

---

### Shoulder Surfing

> L'attaquant **observe physiquement** la victime à son insu

```
Cibles :
  ├── Mots de passe saisis au clavier
  ├── Codes PIN aux distributeurs
  ├── Numéros de carte bancaire
  └── Codes de sécurité / accès
```

> ⚠️ Très efficace dans les espaces publics bondés (cafés, transports, open spaces)

---

### Dumpster Diving (Fouille des poubelles)

> Aussi appelé **"Trashing"** — récupération d'informations dans les déchets

**Sources :**
- Corbeilles à papier de l'entreprise
- Bacs de déchets d'imprimantes
- Post-its sur les bureaux
- Distributeurs automatiques (ATM trash bins)

**Informations trouvables :**
- Factures téléphoniques, coordonnées
- Informations financières
- Codes source imprimés
- Documents opérationnels sensibles

---

### Impersonation (Usurpation d'identité)

> Se faire passer pour une **personne légitime** pour soutirer des informations

**Rôles joués :**
- Livreur / coursier
- Agent de maintenance / technicien
- Visiteur / client
- Agent de nettoyage

**Actions menées :**
- Scanner les terminaux à la recherche de mots de passe
- Rechercher des documents sur les bureaux
- Écouter des conversations confidentielles
- Shoulder surfing

---

> ⬅️ [05 - WHOIS, DNS & Géolocalisation](./05_whois_dns_geo.md) | ➡️ [07 - Outils & Contre-mesures](./07_tools_countermeasures.md)
