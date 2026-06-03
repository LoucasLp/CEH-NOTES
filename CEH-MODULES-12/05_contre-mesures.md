# 🛡️ Module 12 — Contre-mesures : Évasion IDS/Firewalls

> **CEH v13 | EC-Council**  
> `#DPI` `#SIEM` `#Normalisation` `#ThreatIntel` `#SOC`

---

## 📌 Table des matières

1. [Durcissement de l'IDS](#1-durcissement-ids)
2. [Normalisation des paquets](#2-normalisation)
3. [Deep Packet Inspection (DPI)](#3-dpi)
4. [Mise à jour des signatures](#4-signatures)
5. [SIEM et corrélation d'alertes](#5-siem)
6. [Protection contre le tunneling](#6-anti-tunneling)
7. [Matrice des contre-mesures](#7-matrice)

---

## 1. Durcissement de l'IDS

```
Mesures essentielles :

1. Mode inline (IPS) plutôt que passif quand possible
2. Réassemblage complet des paquets IP fragmentés
3. Suivi d'état TCP (stateful inspection)
4. Analyse des deux sens du trafic
5. Logs centralisés et protégés (SIEM)
6. Hardening de l'OS de l'IDS/IPS lui-même
7. Redondance : deux IDS en parallèle (comparison)
```

---

## 2. Normalisation des Paquets

La normalisation consiste à "reconstruire" les paquets dans leur forme canonique avant l'analyse.

```
Sans normalisation :
  %53%45%4C%45%43%54 → Signature "SELECT" non détectée !

Avec normalisation :
  %53%45%4C%45%43%54 → [décode URL] → SELECT → Signature détectée ✓

Niveaux de normalisation :
  IP  : réassemblage de fragments, suppression des duplicats
  TCP : ordonnancement des segments, fenêtre de suivi
  HTTP: décodage URL, gzip, chunked encoding, Unicode
  DNS : décodage des labels DNS, détection d'anomalies longueur
```

### Exemple : mod_security (WAF Apache)

```
# Règle de normalisation HTML dans mod_security :
SecRule ARGS "@rx (?i:<script)" "id:1001,deny,status:403,msg:'XSS attempt'"
SecAction "id:1002,phase:1,nolog,pass,t:htmlEntityDecode,t:lowercase"
```

---

## 3. Deep Packet Inspection (DPI)

Le DPI analyse le contenu complet du payload, pas seulement les headers.

```
Packet Filtering classique :
IP: 1.2.3.4 → 5.6.7.8
Port: 53
→ Autorisé (DNS)

Avec DPI :
IP: 1.2.3.4 → 5.6.7.8
Port: 53
Payload: A1B2C3D4... (pattern de tunneling DNS, pas une requête DNS normale)
→ BLOQUÉ (DNS Tunneling détecté)
```

### Signatures de tunneling à détecter

| Protocole | Indicateur de tunneling |
|-----------|------------------------|
| DNS | Requêtes très longues (>50 chars), volume élevé, sous-domaines aléatoires |
| ICMP | Payload inhabituel (non-ASCII), taille variable |
| HTTP | User-Agent anormal, headers manquants, POST fréquents |
| HTTPS | Certificats auto-signés, SNI absent |

---

## 4. Mise à Jour des Signatures IDS

```
Cadence recommandée :
- Signatures IDS : quotidienne (auto-update)
- Threat intelligence feeds : horaire
- Firmware IPS : mensuelle + urgences
- Règles custom : basée sur les incidents internes

Sources de signatures :
- Snort.org (Sourcefire/Cisco) : règles communautaires
- Emerging Threats (proofpoint.com/us/threat-intelligence)
- Règles commerciales (Talos Intelligence)
- YARA rules (malware hunting)
```

---

## 5. SIEM et Corrélation d'Alertes

Un IDS seul génère trop d'alertes. Le SIEM corrèle pour réduire le bruit.

```
Collecte de logs :
├── IDS/IPS alertes
├── Firewall logs
├── Serveurs (auth.log, syslog)
├── DNS queries
└── Proxy logs

Corrélation SIEM — Exemple de règle :
Si :
  IDS : "Port scan depuis 1.2.3.4"
  AND Firewall : "Connexion refusée depuis 1.2.3.4" × 50
  AND DNS : "Requêtes DNS suspectes depuis 1.2.3.4"
Alors :
  ALERTE PRIORITÉ HAUTE : "Activité de reconnaissance hostile"
  ACTION : Bloquer 1.2.3.4 sur tous les équipements

Outils SIEM :
  Splunk, IBM QRadar, Microsoft Sentinel, Elastic SIEM
```

---

## 6. Protection contre le Tunneling

### Anti-DNS Tunneling

```
1. Limiter les résolveurs DNS autorisés (pas de résolution externe directe)
2. Monitorer le volume de requêtes DNS par client
3. Analyser la longueur des sous-domaines (>30 chars → suspect)
4. Bloquer les types de requêtes inhabituel (TXT, NULL)
5. Solutions DNS sécurisées : Cisco Umbrella, Cloudflare Gateway

Seuils typiques :
  > 100 requêtes/min par IP → Investigate
  Sous-domaine > 50 chars → Alerte
  TTL = 0 sur réponses → Suspect
```

### Anti-HTTP Tunneling

```
1. Proxy web avec SSL inspection (déchiffre le HTTPS)
2. Filtrage par catégorie de domaine
3. Analyse comportementale du trafic HTTP
4. Détection de connexions persistantes anormales
5. Limitation de bande passante par type MIME
```

---

## 7. Matrice des Contre-mesures

| Technique d'évasion | Contre-mesure |
|--------------------|---------------|
| Fragmentation IP | Réassemblage avant inspection |
| Session splicing | Suivi d'état TCP complet |
| Manipulation TTL | Normalisation TTL |
| Encodage URL | Décodage avant analyse (DPI) |
| Scan lent (T0) | Corrélation longue durée (SIEM) |
| Décoy scan | Attribution multi-sources |
| DNS tunneling | DPI DNS + monitoring volume |
| HTTP tunneling | Proxy SSL inspection |
| ICMP tunneling | Blocage ICMP / DPI ICMP |
| Polymorphic payload | Analyse comportementale (sandbox) |

---

## 🧠 Points clés à retenir pour l'examen

- La **normalisation** est la contre-mesure principale à la fragmentation et à l'encodage
- Le **DPI** inspecte le payload complet, pas seulement les headers
- Le **SIEM** corrèle les alertes de plusieurs sources pour réduire les faux positifs
- La **mise à jour des signatures** est critique pour les IDS signature-based
- Un **honeypot** côté défense permet d'observer et d'analyser les TTPs des attaquants
- **Snort** en mode IPS (inline) peut bloquer ; en mode IDS (passif) il alerte seulement

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
