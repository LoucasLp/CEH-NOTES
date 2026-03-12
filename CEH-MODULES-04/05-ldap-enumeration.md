# 📂 05 — Énumération LDAP

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

## 📖 Présentation de LDAP

**LDAP** (Lightweight Directory Access Protocol) est un protocole Internet pour accéder aux services d'annuaire distribués.

| Attribut | Valeur |
|----------|--------|
| **Port standard** | TCP/UDP **389** |
| **Port sécurisé** | TCP **636** (LDAPS) |
| **Transport** | TCP ou UDP |
| **Format de transmission** | BER (Basic Encoding Rules) |
| **DNS** | Utilisé pour les lookups rapides |

---

## 🏗️ Structure LDAP

```
LDAP = Annuaire hiérarchique (comme un organigramme d'entreprise)

Exemple de hiérarchie :
└── DC=company,DC=com          (Domain Component)
    ├── OU=IT                  (Organizational Unit)
    │   ├── CN=John Doe        (Common Name)
    │   └── CN=Jane Smith
    ├── OU=HR
    │   └── CN=Bob Johnson
    └── OU=Servers
        ├── CN=WebServer01
        └── CN=DBServer01
```

---

## 🔄 Processus de connexion LDAP

```
Client
  │
  ├─1─► Connexion au DSA (Directory System Agent) sur port 389
  │
  ├─2─► Envoi d'une requête d'opération
  │
  ├─3─► Transmission via format BER
  │
  └─4─► Réception de la réponse
```

---

## 🎯 Informations extraites via LDAP

Un attaquant peut effectuer des **requêtes anonymes** pour obtenir :
- 👤 Noms d'utilisateurs valides
- 📧 Adresses email
- 🏢 Détails départementaux
- 🖥️ Noms de serveurs

---

## 💻 Énumération LDAP manuelle — Python

### Étapes d'énumération

**1. Vérification du port LDAP avec Nmap**
```bash
# Vérifier LDAP (389) et LDAPS (636)
nmap -p 389,636 <Target IP>
```

**2. Installation de ldap3**
```bash
pip3 install ldap3
```

**3. Création de l'objet serveur**
```python
import ldap3

# Connexion standard LDAP
server = ldap3.Server('TARGET_IP', get_info=ldap3.ALL, port=389)

# Connexion LDAPS (port 636)
server = ldap3.Server('TARGET_IP', get_info=ldap3.ALL, port=636, use_ssl=True)
```

**4. Établissement de la connexion**
```python
connection = ldap3.Connection(server)
connection.bind()
# → True si succès
```

**5. Récupération des informations de domaine**
```python
# Obtenir le nom de domaine et contextes de nommage
>>> server.info
```

**6. Récupération de tous les objets**
```python
connection.search(
    search_base='DC=DOMAIN,DC=DOMAIN',
    search_filter='(&(objectClass=*))',
    search_scope='SUBTREE',
    attributes='*'
)
>>> connection.entries
```

**7. Dump complet de l'annuaire LDAP**
```python
connection.search(
    search_base='DC=DOMAIN,DC=DOMAIN',
    search_filter='(&(objectClass=person))',
    search_scope='SUBTREE',
    attributes='userPassword'
)
>>> connection.entries
```

---

## 💻 Énumération LDAP automatisée — Nmap

```bash
# Brute-force LDAP avec Nmap NSE
nmap -p 389 \
  --script ldap-brute \
  --script-args 'ldap.base="cn=users,dc=CEH,dc=com"' \
  <Target IP>
```

---

## 🔧 ldapsearch — Outil ligne de commande

> Source : https://linux.die.net

**ldapsearch** ouvre une connexion LDAP, la lie et effectue une recherche avec les paramètres spécifiés.

### Commandes essentielles

```bash
# Connexion de base (authentification simple)
ldapsearch -h <Target IP> -x

# Obtenir les contextes de nommage
ldapsearch -h <Target IP> -x -s base namingcontexts

# Requête sur le domaine principal
ldapsearch -h <Target IP> -x -b "DC=htb,DC=local"

# Récupérer les objets de classe Employee
ldapsearch -h <Target IP> -x -b "DC=htb,DC=local" '(objectClass=Employee)'

# Récupérer TOUS les objets de l'arbre
ldapsearch -x -h <Target IP> -b "DC=htb,DC=local" "objectclass=*"

# Lister les utilisateurs d'une classe spécifique
ldapsearch -h <Target IP> -x -b "DC=htb,DC=local" \
  '(objectClass=Employee)' sAMAccountName sAMAccountType
```

### Options importantes
| Option | Rôle |
|--------|------|
| `-h` | Hôte cible |
| `-x` | Authentification simple (sans SASL) |
| `-b` | Base de recherche |
| `-s` | Étendue (base, one, sub) |
| `*` | Tous les attributs utilisateur |
| `+` | Tous les attributs opérationnels |
| `1.1` | Aucun attribut retourné |

---

## 🛠️ Outils LDAP

### Softerra LDAP Administrator
> Source : https://www.ldapadministrator.com

- Compatible : **Active Directory**, Novell Directory Services, Netscape/iPlanet
- Navigue et gère les annuaires LDAP
- Énumère : noms d'utilisateurs, emails, départements

### Autres outils

| Outil | URL |
|-------|-----|
| AD Explorer | https://docs.microsoft.com |
| LDAP Admin Tool | https://www.ldapsoft.com |
| LDAP Account Manager | https://www.ldap-account-manager.org |
| LDAP Search | https://securityxploded.com |

---

## 🎯 Scénario d'attaque LDAP typique

```
1. Scanner le port 389
   nmap -p 389 <IP>
          ↓
2. Tenter une liaison anonyme
   ldapsearch -h <IP> -x
          ↓
3. Découvrir les contextes de nommage
   ldapsearch -h <IP> -x -s base namingcontexts
          ↓
4. Extraire les utilisateurs du domaine
   ldapsearch -h <IP> -x -b "DC=domain,DC=local" 
          ↓
5. Brute-force si authentification requise
   nmap -p 389 --script ldap-brute <IP>
```

---

## ⚡ Synthèse des informations extraites

```
LDAP Enumeration →
├── Utilisateurs valides (sAMAccountName)
├── Adresses email
├── Détails départementaux
├── Noms de serveurs
├── Structure organisationnelle
├── Politiques de sécurité
└── Appartenance aux groupes
```

---

[← SNMP](./04-snmp-enumeration.md) | [→ NTP & NFS](./06-ntp-nfs-enumeration.md)
