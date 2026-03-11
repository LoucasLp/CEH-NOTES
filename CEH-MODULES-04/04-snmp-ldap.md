# 04 — Énumération SNMP & LDAP

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## 🟠 SNMP — Simple Network Management Protocol

### Fonctionnement
SNMP est le protocole standard de supervision réseau. Il gère : routeurs, switches, firewalls, imprimantes, serveurs.

```
┌──────────────────────────────────────────────┐
│                                              │
│   Manager (NMS)          Agent (équipement)  │
│       │                       │              │
│       │── GET (port 161) ────→│              │
│       │← Response (port 161) ─│              │
│       │                       │              │
│       │← TRAP (port 162) ─────│              │
│         (notification auto)                  │
└──────────────────────────────────────────────┘
```

### Versions SNMP & Sécurité

| Version | Sécurité | Community String | Chiffrement |
|---------|----------|-----------------|-------------|
| **SNMPv1** | ❌ Très faible | En clair | ❌ Non |
| **SNMPv2c** | ❌ Faible | En clair | ❌ Non |
| **SNMPv3** | ✅ Fort | Auth + Priv | ✅ AES/DES |

> 🎯 **Cible principale de l'attaquant :** SNMPv1 et SNMPv2c (community strings en clair)

### Community Strings par Défaut
```
READ-ONLY  : public   ← très souvent non changé
READ-WRITE : private  ← accès dangereux
             cisco    ← équipements Cisco
             manager
             admin
```

### Structure MIB (Management Information Base)
```
MIB = Base de données hiérarchique des objets gérables
      (Organisation en arbre OID)

.1.3.6.1.2.1.1.1.0  → sysDescr   (Description système)
.1.3.6.1.2.1.1.5.0  → sysName    (Nom du système)
.1.3.6.1.2.1.2.2    → ifTable    (Interfaces réseau)
.1.3.6.1.2.1.4.24   → ipRouteTable (Table de routage)
.1.3.6.1.2.1.6.13   → tcpConnTable (Connexions TCP)
```

---

### 🛠️ Outils SNMP

#### `snmpwalk` — Parcours de l'arbre MIB
```bash
# Parcours complet avec SNMPv1
snmpwalk -v1 -c public <IP_TARGET>

# Parcours complet avec SNMPv2c
snmpwalk -v2c -c public <IP_TARGET>

# OID spécifique (description système)
snmpwalk -v2c -c public <IP_TARGET> 1.3.6.1.2.1.1.1.0

# Lister les interfaces réseau
snmpwalk -v2c -c public <IP_TARGET> 1.3.6.1.2.1.2.2

# Lister les processus actifs
snmpwalk -v2c -c public <IP_TARGET> 1.3.6.1.2.1.25.4.2

# Lister les utilisateurs
snmpwalk -v2c -c public <IP_TARGET> 1.3.6.1.4.1.77.1.2.25
```

#### `snmpget` — Requête SNMP ciblée
```bash
# Obtenir la description système
snmpget -v2c -c public <IP_TARGET> sysDescr.0

# Obtenir le nom du système
snmpget -v2c -c public <IP_TARGET> sysName.0
```

#### `onesixtyone` — Brute Force Community Strings
```bash
# Tester une liste de community strings
onesixtyone -c /usr/share/wordlists/community_strings.txt <IP_TARGET>

# Scanner une plage réseau
onesixtyone -c public 192.168.1.0/24
```

#### `snmp-check` — Énumération SNMP complète
```bash
# Énumération complète de la cible
snmp-check <IP_TARGET>

# Avec community string spécifique
snmp-check -c private <IP_TARGET>

# Version SNMP spécifique
snmp-check -v 2c -c public <IP_TARGET>
```

**snmp-check fournit :**
- Informations système (OS, uptime)
- Interfaces réseau et IPs
- Processus actifs
- Services en cours
- Comptes utilisateurs
- Partages réseau
- Logiciels installés

#### Nmap Scripts SNMP
```bash
# Informations de base
nmap -sU -p 161 --script snmp-info <IP_TARGET>

# Lister les interfaces
nmap -sU -p 161 --script snmp-interfaces <IP_TARGET>

# Lister les processus
nmap -sU -p 161 --script snmp-processes <IP_TARGET>

# Brute force des community strings
nmap -sU -p 161 --script snmp-brute <IP_TARGET>

# Tout d'un coup
nmap -sU -p 161 --script snmp-* <IP_TARGET>
```

---

## 🔵 LDAP — Lightweight Directory Access Protocol

### Qu'est-ce que LDAP ?
LDAP est le protocole standard d'accès aux annuaires d'entreprise. Microsoft Active Directory, OpenLDAP l'utilisent comme protocole principal.

```
┌─────────────────────────────────────────────┐
│                                             │
│  Client LDAP      Serveur LDAP (AD, OpenLDAP)│
│      │                    │                 │
│      │── Bind (auth) ────→│  Port 389       │
│      │← Bind Response ────│                 │
│      │── Search ─────────→│                 │
│      │← Search Results ───│                 │
│      │── Unbind ─────────→│                 │
│                                             │
│  LDAPS (chiffré) : Port 636                 │
│  Global Catalog  : Port 3268 (non chiffré)  │
│  Global Catalog  : Port 3269 (chiffré)      │
└─────────────────────────────────────────────┘
```

### Structure de l'Annuaire LDAP

```
DC=company,DC=com                    ← Domain Component (racine)
├── OU=Users                         ← Organizational Unit
│   ├── CN=John Doe                  ← Common Name (utilisateur)
│   └── CN=Jane Smith
├── OU=Computers
│   ├── CN=WORKSTATION01
│   └── CN=SERVER01
├── OU=Groups
│   └── CN=Domain Admins
└── CN=Administrator
```

### Syntaxe LDAP (DN — Distinguished Name)
```
CN=John Doe,OU=Users,DC=company,DC=com
│           │         │
└─ Prénom   └─ OU     └─ Domaine
```

### Requêtes LDAP Importantes
| Attribut | Description |
|----------|-------------|
| `cn` | Common Name |
| `sn` | Surname (nom de famille) |
| `uid` | User ID |
| `mail` | Adresse email |
| `memberOf` | Groupes d'appartenance |
| `userAccountControl` | Statut du compte |
| `pwdLastSet` | Dernière modification MdP |
| `adminCount` | Compte administrateur |

---

### 🛠️ Outils LDAP

#### `ldapsearch` — Requêtes LDAP depuis Linux
```bash
# Connexion anonyme (si autorisé)
ldapsearch -x -H ldap://<IP_TARGET> -b "DC=company,DC=com"

# Lister tous les utilisateurs
ldapsearch -x -H ldap://<IP_TARGET> \
  -b "DC=company,DC=com" \
  "(objectClass=user)" \
  cn sAMAccountName mail

# Lister les groupes
ldapsearch -x -H ldap://<IP_TARGET> \
  -b "DC=company,DC=com" \
  "(objectClass=group)" \
  cn member

# Avec authentification
ldapsearch -x -H ldap://<IP_TARGET> \
  -D "CN=user,DC=company,DC=com" \
  -w "password" \
  -b "DC=company,DC=com" \
  "(objectClass=*)"

# Trouver les administrateurs du domaine
ldapsearch -x -H ldap://<IP_TARGET> \
  -b "DC=company,DC=com" \
  "(adminCount=1)" \
  cn sAMAccountName
```

#### `JXplorer` — GUI LDAP Browser
Outil graphique pour naviguer dans l'annuaire LDAP :
- Connexion avec ou sans credentials
- Navigation arborescente de l'annuaire
- Export des données en CSV/LDIF

#### Nmap Scripts LDAP
```bash
# Informations basiques LDAP
nmap -p 389 --script ldap-rootdse <IP_TARGET>

# Brute force LDAP
nmap -p 389 --script ldap-brute \
  --script-args ldap.base='"cn=users,dc=company,dc=com"' <IP_TARGET>

# Recherche LDAP
nmap -p 389 --script ldap-search \
  --script-args ldap.base='"dc=company,dc=com"' <IP_TARGET>
```

#### PowerShell — Énumération AD/LDAP
```powershell
# Lister tous les utilisateurs AD
Get-ADUser -Filter * | Select-Object Name, SamAccountName, Enabled

# Lister les membres du groupe "Domain Admins"
Get-ADGroupMember "Domain Admins" | Select-Object Name, SamAccountName

# Lister tous les groupes
Get-ADGroup -Filter * | Select-Object Name

# Lister les ordinateurs du domaine
Get-ADComputer -Filter * | Select-Object Name, DNSHostName

# Chercher des utilisateurs avec MdP n'expirant pas
Get-ADUser -Filter {PasswordNeverExpires -eq $true} | Select Name
```

---

## 📊 Comparatif SNMP vs LDAP

| Critère | SNMP | LDAP |
|---------|------|------|
| **Port** | 161/162 UDP | 389 TCP/UDP, 636 SSL |
| **Usage principal** | Supervision équipements réseau | Gestion d'identités/annuaire |
| **Données** | Config réseau, performances | Users, groupes, policies |
| **Auth v1/v2** | Community string (texte clair) | Bind anonyme ou simple |
| **Auth sécurisée** | SNMPv3 | LDAPS (SSL) |
| **Outils CEH** | snmpwalk, snmp-check, onesixtyone | ldapsearch, JXplorer |

---

## 🛡️ Contre-mesures

### SNMP
- Passer à **SNMPv3** (chiffrement + authentification)
- Changer les community strings par défaut
- Restreindre l'accès SNMP par ACL/IP
- Désactiver SNMP si non utilisé
- Utiliser des firewalls pour bloquer UDP 161/162

### LDAP
- Désactiver les **liaisons anonymes** (Anonymous Bind)
- Utiliser **LDAPS** (port 636) au lieu de LDAP
- Implémenter des ACL granulaires sur l'annuaire
- Activer l'audit des connexions LDAP
- Utiliser le **Global Catalog SSL** (port 3269)

---

[← NetBIOS & SMB](./03-netbios-smb.md) | [DNS & SMTP →](./05-dns-smtp.md)
