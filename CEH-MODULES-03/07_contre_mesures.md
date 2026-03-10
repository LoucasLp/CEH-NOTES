# 🛡️ 07 — Contre-mesures (Countermeasures)

> **Module 3 — CEH v13** | [← Retour au sommaire](./README.md)

---

## Vue d'ensemble

En tant qu'**Ethical Hacker / Pen Tester**, identifier les vulnérabilités ne suffit pas. Il faut aussi mettre en place des **contre-mesures** pour protéger le réseau contre les attaquants réels.

```
┌─────────────────────────────────────────────────────────┐
│              CONTRE-MESURES MODULE 3                    │
├──────────────┬──────────────┬───────────────────────────┤
│  Ping Sweep  │  Port Scan   │  Banner Grabbing          │
│              │              │                           │
│  IP Spoofing │  Scan        │  Détection/Prévention     │
│  Detection   │  Detection   │  Outils                   │
└──────────────┴──────────────┴───────────────────────────┘
```

---

## 1. Contre-mesures Ping Sweep

| Contre-mesure | Description |
|--------------|-------------|
| **Bloquer ICMP echo entrant** | Configurer le firewall pour bloquer les requêtes ICMP ECHO provenant de sources inconnues |
| **IDS/IPS** | Utiliser Snort pour détecter et prévenir les ping sweeps |
| **Audit trafic ICMP** | Évaluer soigneusement le type de trafic ICMP traversant le réseau |
| **Limiter connexions ICMP** | Terminer la connexion avec tout hôte envoyant > 10 ICMP ECHO requests |
| **DMZ** | N'autoriser que ICMP ECHO REPLY, HOST UNREACHABLE et TIME EXCEEDED dans la DMZ |
| **ACLs** | Limiter le trafic ICMP aux adresses IP spécifiques du FAI |
| **Rate limiting** | Implémenter des limites de débit pour les paquets ICMP |
| **Segmentation réseau** | Diviser en sous-réseaux isolés pour limiter la portée d'un ping sweep |
| **NAT** | Utiliser des plages d'IP privées + NAT pour masquer les IPs internes |

---

## 2. Contre-mesures Port Scanning

### Configuration Firewall & IDS

```
✅  Configurer rules Firewall/IDS pour détecter et bloquer les probes
✅  Le firewall doit inspecter les DONNÉES des paquets (pas seulement l'en-tête TCP)
✅  Tester régulièrement avec des outils de port scanning
✅  Mettre à jour le firmware : routeur, IDS, firewall
✅  Configurer firewall contre fast port scans et SYN floods
✅  Déployer Snort pour détection OS (signatures fréquemment mises à jour)
```

### Gestion des ports

```
✅  Garder le MINIMUM de ports ouverts
✅  Filtrer les ports inutiles au niveau du firewall
✅  Bloquer ces ports spécifiques : 135-159, 256-258, 389, 445, 1080, 1745, 3268
✅  Désactiver les services inutiles sur les ports
✅  S'assurer que les versions de services sont non-vulnérables
```

### Contre-mesures avancées

| Technique | Description |
|-----------|-------------|
| **Bloquer ICMP entrant** | Bloquer les messages ICMP type-3 unreachable en sortie sur les routeurs |
| **Anti source-routing** | S'assurer que firewall/routeur bloquent les techniques de source routing |
| **Anti-scanning rules** | Configurer les règles anti-scanning et anti-spoofing |
| **TCP Wrappers** | Limiter l'accès basé sur noms de domaine ou IP |
| **Proxy servers** | Bloquer les paquets fragmentés ou malformés |
| **Honeypots** | Rediriger les port scans vers des honeypots |
| **IPS** | Identifier les tentatives de port scan et blacklister les IPs |
| **Port Knocking** | Cacher les ports ouverts |
| **NAT** | Masquer les IPs des systèmes internes |
| **Egress Filtering** | Contrôler le trafic sortant pour identifier les hôtes malveillants internes |
| **VLANs** | Isoler différents types de trafic |

### Checklist Firewall Commercial

```
□  Patché avec les dernières mises à jour
□  Règles anti-spoofing correctement définies
□  Services fast-mode inutilisables
```

---

## 3. Contre-mesures Banner Grabbing

### 3.1 Désactiver ou Modifier les Banners

| Action | Méthode |
|--------|---------|
| **Afficher de faux banners** | Tromper/décevoir les attaquants avec des informations incorrectes |
| **Désactiver services inutiles** | Limiter la divulgation d'informations |
| **Masquer les détails vendeur/version** | Supprimer ces infos des banners |
| **Modifier Apache** | Changer l'en-tête banner dans `httpd.conf` → `New Server Name` |
| **Désactiver ServerSignature** | `ServerSignature Off` dans `httpd.conf` |
| **ServerTokens minimal** | Modifier `ServerTokens` de `Full` à `Prod` dans Apache |
| **UrlScan.ini** | Modifier `RemoveServerHeader` de `0` à `1` |
| **AlternateServerName** | Tromper avec des valeurs comme `xyz` ou `myserver` |
| **Supprimer en-têtes HTTP** | Retirer `x-Powered-By` via `customHeaders` dans `web.config` |
| **Désactiver méthodes HTTP** | Bloquer Connect, Put, Delete, Options |

### 3.2 Masquer les Extensions de Fichiers

| Action | Description |
|--------|-------------|
| **Remplacer .asp par .htm** | Masquer la technologie serveur IIS |
| **Utiliser mod_negotiation** | Pour les serveurs Apache |
| **Éviter les extensions** | Préférable de ne pas utiliser d'extensions du tout |

### 3.3 Autres contre-mesures

```
✅  Packet filtering → bloquer accès aux ports révélant des banners
✅  IDS/IPS → monitorer et alerter sur les activités de scanning
✅  Remplacer HTTP/FTP/Telnet par HTTPS/SFTP/SSH (chiffrement)
✅  TLS pour les services → chiffre les banners lors du handshake
```

---

## 4. Détection du Spoofing IP

### 4.1 Direct TTL Probes

**Principe :** Envoyer un ping à l'hôte légitime et comparer le TTL de la réponse.

```
TTL des paquets légitimes (TCP/UDP) : 64 ou 128
TTL des paquets légitimes (ICMP)    : 128 ou 255

Si TTL réponse ≠ TTL attendu → Paquet potentiellement spoofé

Hop count = TTL initial - TTL observé
```

> ⚠️ **Faux positif possible** si l'attaquant connaît le hop count entre source et hôte.

---

### 4.2 IP Identification Number (IPID)

**Principe :** L'IPID augmente de 1 à chaque paquet envoyé.

```
1. Envoyer une probe vers l'IP source suspecte
2. Observer l'IPID dans la réponse
3. L'IPID doit être proche (légèrement supérieur) à l'IPID de la probe

Si IPID réponse ≠ proche de IPID probe → Adresse IP spoofée
```

> ✅ Efficace même quand attaquant et cible sont sur le **même subnet**.

---

### 4.3 TCP Flow Control Method

**Principe :** Utiliser la taille de fenêtre TCP pour détecter les paquets spoofés.

```
Attaquant réel     → connaît l'ACK avec window size → s'arrête quand window = 0
Attaquant spoofed  → ne reçoit pas l'ACK            → continue d'envoyer au-delà de window size

Si données reçues > window size → Paquets probablement spoofés
```

**Technique handshake :**
```
1. Définir SYN-ACK = 0
2. Si l'expéditeur envoie ACK avec données → SPOOFÉ
   (réponse correcte = ACK seul, sans données quand window = 0)
```

---

## 5. Contre-mesures IP Spoofing

| Contre-mesure | Description |
|--------------|-------------|
| **Éviter les trust relationships** | Ne pas se baser sur l'authentification IP seule → ajouter password auth |
| **Firewalls + ACLs** | Filtrer paquets entrants ET sortants |
| **ISN aléatoires** | Utiliser des Initial Sequence Numbers aléatoires (pas prévisibles) |
| **Ingress Filtering** | ACLs sur routeurs pour rejeter paquets avec IP source hors plage définie |
| **Egress Filtering** | Bloquer les paquets sortants avec une adresse source extérieure |
| **Chiffrement** | IPSec pour authentification, intégrité et confidentialité des données |
| **SYN Flood Countermeasures** | S'appliquent aussi pour le spoofing IP |

### Contre-mesures avancées

```
□  Migration IPv4 → IPv6 (améliore intégrité et confidentialité)
□  Authentification par certificats digitaux (domain + 2-way auth)
□  VPN sécurisé sur WiFi public et hotspots
□  Dispositifs de mitigation : scrubbers Behemoth (100M paquets/s)
□  Variation dynamique adresses IPv6 (random address generator)
□  Configurer routeurs pour encoder infos paquets fragmentés
□  Configurer routeurs pour vérifier paquets via signatures (digests)
□  NAT pour masquer hôtes intranet
□  DHCP static address tables sur switchs internes
□  Protocoles sécurisés : HTTPS, SFTP, SSH
```

---

## 6. Outils de Détection et Prévention de Scanning

### ExtraHop
> 🌐 Source : [https://www.extrahop.com](https://www.extrahop.com)

Fournit **visibilité complète, détection en temps réel et réponse intelligente** aux scans réseau malveillants.

**Capacités :**
- Auto-découverte et identification de chaque device (y compris IoT non-managés)
- Analyse de toutes les interactions réseau en temps réel
- Inclut transactions cloud et trafic SSL/TLS chiffré
- Auto-classification des devices pour analyse des communications

### Autres outils de détection

| Outil | URL | Description |
|-------|-----|-------------|
| **Splunk Enterprise Security** | splunk.com | SIEM avec détection de scans |
| **Scanlogd** | github.com | Détecteur de port scans |
| **Vectra Detect** | vectra.ai | Détection d'attaques réseau par IA |
| **IBM Security QRadar XDR** | ibm.com | Plateforme XDR complète |
| **Cynet 360 AutoXDR™** | cynet.com | Détection et réponse automatisée |

---

## 7. Récapitulatif — Quick Reference

### Tableau de correspondance Attaque → Contre-mesure

| Technique d'attaque | Contre-mesure principale |
|--------------------|--------------------------|
| Ping Sweep | Bloquer ICMP entrant + Rate limiting ICMP |
| Port Scanning | IDS/IPS + Firewall rules + Port knocking |
| Banner Grabbing | Faux banners + Masquer extensions + TLS |
| IP Spoofing | Ingress/Egress filtering + IPSec |
| MAC Spoofing | 802.1X authentication + DHCP snooping |
| Proxy/Anonymizer | Deep packet inspection + Blacklists |
| Fragmentation | Défragmentation avant inspection (stateful FW) |
| Source Routing | Désactiver source routing sur routeurs |
| IDLE/IPID Scan | Désactiver IPID séquentiel (IP randomization) |

---

## 8. Snort — IDS de référence

> 🌐 Source : [https://www.snort.org](https://www.snort.org)

Snort est un **système de détection et prévention d'intrusions** (IDS/IPS) particulièrement utile car :
- Signatures **fréquemment mises à jour** par des auteurs publics
- Détecte les scans OS (Nmap), port scans, SYN floods
- Mode IPS : **bloque** activement les tentatives

---

[← Évasion IDS & Firewall](./06_evasion_ids_firewall.md) | [↑ Retour au sommaire](./README.md)
