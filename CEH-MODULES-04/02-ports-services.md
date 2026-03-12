# 🔌 02 — Services et Ports à Énumérer

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

## 📡 TCP vs UDP — Rappel

| Critère | TCP | UDP |
|---------|-----|-----|
| **Type** | Orienté connexion | Sans connexion |
| **Fiabilité** | ✅ Fiable (ACK, retransmission) | ❌ Non garanti |
| **Vitesse** | Plus lent | Plus rapide |
| **Usage** | Email, web, transferts fichiers | Streaming, VoIP, DNS |
| **Contrôle flux** | ✅ Oui (fenêtre glissante) | ❌ Non |

---

## 🗺️ Carte des ports clés

```
PORT    PROTO     SERVICE                   RISQUE
─────────────────────────────────────────────────────────
20/21   TCP       FTP (données/contrôle)    🟡 Moyen
22      TCP       SSH / SFTP               🟡 Brute-force
23      TCP       Telnet                   🔴 CRITIQUE (clair)
25      TCP       SMTP                     🟡 User enum
53      TCP/UDP   DNS                      🔴 Zone Transfer
69      UDP       TFTP                     🔴 Malware upload
111     TCP/UDP   RPC Portmapper           🟡 Moyen
123     UDP       NTP                      🟡 Info leak
135     TCP/UDP   RPC Endpoint Mapper      🔴 DoS possible
137     UDP       NetBIOS Name Service     🟡 Moyen
138     UDP       NetBIOS Datagram         🟡 Moyen
139     TCP       NetBIOS Session / SMB    🔴 CRITIQUE
161     UDP       SNMP                     🔴 Config leak
162     TCP/UDP   SNMP Trap                🟡 Moyen
179     TCP       BGP                      🔴 Hijacking
389     TCP/UDP   LDAP                     🔴 AD enum
445     TCP/UDP   SMB Direct               🔴 CRITIQUE
500     UDP       IKE / ISAKMP             🟡 VPN enum
2049    TCP       NFS                      🔴 File access
3268    TCP/UDP   Global Catalog (LDAP)    🟡 AD enum
5060    TCP/UDP   SIP (VoIP)               🟡 VoIP enum
5061    TCP/UDP   SIP over TLS             🟡 VoIP enum
```

---

## 📋 Détail des ports critiques

### 🔵 TCP/UDP 53 — DNS Zone Transfer
Le DNS utilise **UDP 53** par défaut. Si la réponse dépasse 512 octets, bascule sur **TCP 53**.

> 🦠 Malware connus exploitant le port 53 : **ADM worm**, **Bonk Trojan**

```bash
# Test zone transfer
dig @<nameserver> <domain> axfr
nslookup> set querytype=soa <domain>
```

---

### 🔵 TCP/UDP 135 — RPC Endpoint Mapper
- Permet aux clients RPC de trouver le port assigné à un service RPC spécifique
- **Vulnérabilité** : mauvaise gestion des messages malformés → attaque **DoS**

---

### 🔵 UDP 137 — NetBIOS Name Service (NBNS / WINS)
- Résolution de noms NetBIOS ↔ adresses IP
- Aussi connu sous **Windows Internet Name Service (WINS)**
- TCP 137 possible mais rare en pratique

---

### 🔵 TCP 139 — NetBIOS Session (SMB over NetBIOS)
> 🔴 **Port prioritaire à restreindre sur les systèmes Windows**

- Transfert de fichiers sur le réseau
- Sessions null + partage fichiers/imprimantes
- Accès non autorisé possible → vol de données

---

### 🔵 TCP/UDP 445 — SMB over TCP (Direct Host)
- SMB directement sur TCP (sans NetBIOS)
- Remplace progressivement le port 139
- Supporte les versions modernes de SMB (SMBv2, SMBv3)

---

### 🔵 UDP 161/162 — SNMP
```
Manager  ──→ port 161 (requêtes)  ──→ Agent
Manager  ←── port 162 (traps)     ←── Agent
```
- **Port 161** : réception des requêtes SNMP
- **Port 162** : envoi des notifications (traps)

---

### 🔵 TCP/UDP 389 — LDAP
- Accès aux annuaires distribués (Active Directory)
- Permet l'énumération anonyme si mal configuré

---

### 🔵 TCP 2049 — NFS
- Montage de systèmes de fichiers distants
- Si mal configuré → contrôle du système, escalade de privilèges, backdoors

---

### 🔵 TCP 25 — SMTP

| Commande | Syntaxe | Usage |
|----------|---------|-------|
| **Hello** | `HELO <host>` | Identification |
| **From** | `MAIL FROM:<addr>` | Expéditeur |
| **Recipient** | `RCPT TO:<addr>` | Destinataire |
| **Verify** | `VRFY <string>` | ⚠️ Valide un user |
| **Expand** | `EXPN <string>` | ⚠️ Liste les aliases |
| **Data** | `DATA` | Corps du message |
| **Quit** | `QUIT` | Fermeture |

---

### 🔵 TCP 22 — SSH / SFTP
- SSH : alternative sécurisée à Telnet
- SFTP : utilise **le même port 22** (plus simple que FTP/S)
- **Attaque** : brute-force des credentials SSH

---

### 🔵 TCP 23 — Telnet
> 🔴 **PROTOCOLE NON SÉCURISÉ** — Transmet les credentials **en clair**

- Principalement utilisé sur réseaux privés
- Vecteur : banner grabbing, brute-force, port-forwarding

---

### 🔵 UDP 69 — TFTP
- Protocole **sans connexion** basé sur UDP
- Pas de garantie de transmission
- Utilisé pour mises à jour firmware
- **Risque** : installation de malware/firmware malveillant

---

### 🔵 TCP 179 — BGP
- Utilisé par les ISP pour les grandes tables de routage
- **Mauvaise config** → dictionnary attacks, resource exhaustion, flooding, hijacking

---

### 🔵 TCP/UDP 500 — ISAKMP/IKE
- Protocole d'établissement des associations de sécurité pour **IPsec/VPN**
- Négociation, modification et suppression des SAs et clés cryptographiques

---

### 🔵 TCP/UDP 3268 — Global Catalog Service
- Serveur Microsoft stockant les infos de **tout l'annuaire** (pas seulement un domaine)
- LDAP sur le Global Catalog → port **3268**
- Utilisé pour localiser des objets dans n'importe quel domaine

---

### 🔵 TCP/UDP 5060/5061 — SIP (VoIP)
- 5060 : trafic **non chiffré**
- 5061 : trafic **chiffré TLS**

---

## 🎯 Résumé mnémotechnique

```
"Super Sécurisé, Ne Pas Toucher Léger Négligemment Sans Big Surprise"
  SSH(22) SMTP(25) NFS(2049) TFTP(69) LDAP(389) NetBIOS(137-139) SMB(445) BGP(179) SNMP(161)
```

---

[← Introduction](./01-introduction.md) | [→ NetBIOS Enumeration](./03-netbios-enumeration.md)
