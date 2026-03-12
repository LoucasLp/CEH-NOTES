# 📡 04 — Énumération SNMP

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

## 📖 Présentation de SNMP

**SNMP** (Simple Network Management Protocol) est un protocole de **couche application** (UDP) permettant aux administrateurs réseau de gérer les équipements à distance.

| Attribut | Valeur |
|----------|--------|
| **Protocole** | UDP |
| **Port requêtes** | 161 |
| **Port traps** | 162 |
| **Équipements** | Routeurs, switchs, firewalls, imprimantes, serveurs |

---

## 🏗️ Architecture SNMP

```
┌──────────────────────────────────────────────────────────┐
│                    RÉSEAU GÉRÉ                           │
│                                                          │
│  ┌─────────────────┐         ┌─────────────────────┐    │
│  │  SNMP Manager   │◄───────►│   SNMP Agent        │    │
│  │  (Station de    │  UDP    │   (sur l'équipement) │    │
│  │   gestion)      │         │                     │    │
│  └─────────────────┘         └─────────────────────┘    │
│        Port 162                     Port 161             │
│       (réception traps)         (écoute requêtes)        │
└──────────────────────────────────────────────────────────┘
```

---

## 🔑 Community Strings (Mots de passe SNMP)

> 🔴 **Vulnérabilité majeure** : Les community strings par défaut sont **"public"** et **"private"**

| Type | Valeur défaut | Permissions |
|------|---------------|-------------|
| **Read Community String** | `public` | Lecture seule (vue de la config) |
| **Read/Write Community String** | `private` | Lecture + modification de la config |

---

## 🔄 Fonctionnement de SNMP (Flux de communication)

### 1. Initialisation
Le démon SNMP démarre, écoute sur **UDP 161**.

### 2. Découverte
Le Manager envoie des requêtes à l'adresse broadcast ou aux IPs connues.

### 3. Échange d'informations — PDUs (Protocol Data Units)

| Opération | Direction | Description |
|-----------|-----------|-------------|
| **Get Request** | Manager → Agent | Récupère la valeur d'une variable |
| **GetNext Request** | Manager → Agent | Récupère la prochaine variable dans l'arbre MIB |
| **Set Request** | Manager → Agent | Modifie une variable de configuration |
| **GetBulk Request** | Manager → Agent | Récupère un grand volume de données (SNMPv2+) |
| **Response** | Agent → Manager | Réponse aux requêtes Get/Set |
| **Inform Request** | Agent → Manager | Info non sollicitée sur événements importants |
| **Trap** | Agent → Manager | Alerte sur événement (reboot, panne d'interface) |

### 4. Monitoring continu
Le Manager poll régulièrement les agents et écoute les Traps.

---

## 📚 MIB — Management Information Base

```
MIB = Base de données virtuelle hiérarchique des objets SNMP
      (comme un annuaire des variables gérables)
```

### Structure
- Organisation **hiérarchique** (arbre OID)
- Chaque objet identifié par un **OID** (Object Identifier)
- OID = nom numérique unique ex: `1.3.6.1.2.1.1.1`

### Types d'objets MIB
| Type | Description |
|------|-------------|
| **Scalar** | Instance d'objet unique |
| **Tabular** | Groupe d'instances d'objets liés |

### Attributs d'un OID
- Type (compteur, chaîne, adresse…)
- Niveau d'accès (lecture / lecture-écriture)
- Restrictions de taille
- Informations de plage

### MIBs Windows principales

| MIB | Contenu |
|-----|---------|
| **DHCP.MIB** | Trafic entre serveurs DHCP et hôtes distants |
| **HOSTMIB.MIB** | Surveillance et gestion des ressources hôtes |
| **LNMIB2.MIB** | Types d'objets pour services workstation/serveur |
| **MIB_II.MIB** | Gestion TCP/IP (architecture simple) |
| **WINS.MIB** | Windows Internet Name Service |

### Accès à la MIB
```
http://<IP>/Lseries.mib
http://<library_name>/Lseries.mib
```

---

## 🔍 Énumération SNMP — SnmpWalk

> Source : https://ezfive.com

**SnmpWalk** scanne des nœuds SNMP et récupère les variables accessibles.

### Commandes principales

```bash
# SNMPv1 — Enumération de base
snmpwalk -v1 -c public <Target IP>

# SNMPv2c — Enumération avec community string "public"
snmpwalk -v2c -c public <Target IP>

# Lister les logiciels installés
snmpwalk -v2c -c public <Target IP> hrSWInstalledName

# Déterminer la RAM de l'hôte
snmpwalk -v2c -c public <Target IP> hrMemorySize

# Modifier un OID
snmpwalk -v2c -c public <Target IP> <OID> <New Value>

# Modifier le sysContact OID
snmpwalk -v2c -c public <Target IP> sysContact <New Value>
```

---

## 🔍 Énumération SNMP — Nmap

```bash
# Lister tous les processus SNMP en cours
nmap -sU -p 161 --script=snmp-processes <Target IP>

# Récupérer type et OS du serveur SNMP
nmap -sU -p 161 --script=snmp-sysdescr <Target IP>

# Lister les applications en cours d'exécution
nmap -sU -p 161 --script=snmp-win32-software <Target IP>
```

---

## 🛠️ Outils d'énumération SNMP

### snmp-check
> Source : https://www.nothink.org | Licence GPL

**Automatise** la collecte d'informations SNMP. Informations extraites :
- Contact, description, accès en écriture
- Équipements, domaine, informations hardware/stockage
- Hostname, statistiques IIS, IP forwarding
- Ports UDP en écoute, localisation, points de montage
- Interfaces réseau, services, informations de routage
- Composants logiciels, uptime, connexions TCP
- Mémoire totale, comptes utilisateurs

### SoftPerfect Network Scanner
> Source : https://www.softperfect.com

- Ping, scan de ports, découverte de dossiers partagés
- Récupère informations via **WMI, SNMP, HTTP, SSH, PowerShell**
- Scan services distants, registre, fichiers, compteurs de performance
- Résolution hostname, détection plages IP locales/externes
- Export XML, JSON
- Support **Wake-on-LAN** et arrêt à distance

### Autres outils SNMP

| Outil | URL |
|-------|-----|
| OpUtils | https://www.manageengine.com |
| Network Performance Monitor | https://www.solarwinds.com |
| PRTG Network Monitor | https://www.paessler.com |
| Engineer's Toolset | https://www.solarwinds.com |

---

## 🎯 Synthèse des informations extraites par SNMP

```
SNMP Enumeration → 
├── Hôtes, routeurs, équipements, partages
├── Tables ARP
├── Tables de routage
├── Informations spécifiques aux équipements
├── Statistiques de trafic
├── Noms d'utilisateurs
└── Topologie réseau
```

---

## ⚡ Flux d'attaque typique

```
1. Découverte SNMP
   nmap -sU -p 161 <réseau>
          ↓
2. Test des community strings par défaut
   snmpwalk -v2c -c public <IP>
          ↓
3. Si succès → Extraction complète
   snmpwalk -v2c -c public <IP> hrSWInstalledName
          ↓
4. Modification de config si R/W disponible
   snmpwalk -v2c -c private <IP> sysContact "hacker"
```

---

[← NetBIOS](./03-netbios-enumeration.md) | [→ LDAP Enumeration](./05-ldap-enumeration.md)
