# 08 — Contre-mesures & Défense

> **CEH v13 · Module 04**  
> [← Retour au README](./README.md)

---

## 🛡️ Principe Général

> La meilleure défense contre l'énumération est de **minimiser la surface d'exposition** :  
> moins d'informations accessibles = moins d'opportunités pour l'attaquant.

```
┌─────────────────────────────────────────────────┐
│           STRATÉGIE DE DÉFENSE                  │
│                                                 │
│  1. Réduire la surface d'exposition             │
│  2. Authentifier avant de répondre              │
│  3. Monitorer les accès suspects                │
│  4. Chiffrer les communications                 │
│  5. Segmenter le réseau                         │
└─────────────────────────────────────────────────┘
```

---

## 🔵 NetBIOS & SMB

### Désactiver les Sessions Nulles

```powershell
# Regedit - Désactiver l'accès anonyme
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\LSA
  RestrictAnonymous      = 2   # Aucun accès anonyme
  RestrictAnonymousSAM   = 1   # Pas d'énumération SAM anonyme
  EveryoneIncludesAnonymous = 0
```

```powershell
# Via PowerShell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\LSA" `
  -Name "RestrictAnonymous" -Value 2
```

### Désactiver NetBIOS
```powershell
# Via les propriétés réseau
# Connexion Réseau → Propriétés TCP/IP → Avancé → WINS
# → Désactiver NetBIOS sur TCP/IP

# Via PowerShell (désactiver sur toutes les interfaces)
$adapters = Get-WmiObject Win32_NetworkAdapterConfiguration
foreach ($adapter in $adapters) {
    $adapter.SetTcpipNetbios(2)  # 2 = Désactivé
}
```

### Filtrage des Ports SMB/NetBIOS
```
Bloquer en entrée/sortie :
  TCP  137  (NetBIOS Name)
  UDP  137  (NetBIOS Name)
  TCP  138  (NetBIOS Datagram)
  UDP  139  (NetBIOS Session)
  TCP  445  (SMB Direct)
  
→ Surtout sur les interfaces périmètre (Internet)
→ Segmenter les VLAN pour limiter la propagation interne
```

### Sécuriser SMB
- Désactiver **SMBv1** (vulnérable à EternalBlue/WannaCry)
  ```powershell
  Set-SmbServerConfiguration -EnableSMB1Protocol $false
  ```
- Activer le **chiffrement SMB** (SMBv3)
  ```powershell
  Set-SmbServerConfiguration -EncryptData $true
  ```
- Activer la **signature SMB**
  ```powershell
  Set-SmbServerConfiguration -RequireSecuritySignature $true
  ```

---

## 🟠 SNMP

### Mesures Essentielles
| Action | Impact |
|--------|--------|
| Mettre à jour vers **SNMPv3** | Chiffrement + Authentification |
| Changer les community strings | Éliminer `public`/`private` |
| Restreindre par ACL | Limiter aux seuls gestionnaires |
| Désactiver si inutilisé | Supprimer la surface d'attaque |
| Auditer régulièrement | Détecter les accès anormaux |

### Configuration SNMPv3
```bash
# Cisco IOS - SNMPv3 avec auth et chiffrement
snmp-server group SecureGroup v3 priv
snmp-server user SecureUser SecureGroup v3 auth sha AuthPass priv aes 128 PrivPass
no snmp-server community public ro     # Supprimer community publique
no snmp-server community private rw    # Supprimer community privée
```

```bash
# Linux / Net-SNMP (/etc/snmp/snmpd.conf)
# Supprimer les accès par community string
#rocommunity public  default    ← Commenter cette ligne

# Ajouter SNMPv3
createUser secureUser SHA "authPassword" AES "privPassword"
rouser secureUser priv
```

---

## 🔵 LDAP & Active Directory

### Mesures Active Directory
```powershell
# Désactiver les liaisons LDAP anonymes
# Éditeur d'objets de stratégie de groupe (GPMC)
# Computer Configuration → Windows Settings → Security Settings
# → Local Policies → Security Options
# → "Network access: Do not allow anonymous enumeration of SAM accounts"
#    → Enabled

# PowerShell
Set-GPRegistryValue -Name "Default Domain Policy" `
  -Key "HKLM\SYSTEM\CurrentControlSet\Control\LSA" `
  -ValueName "RestrictAnonymousSAM" `
  -Type DWord -Value 1
```

### Forcer LDAPS (LDAP over SSL)
```
1. Installer un certificat SSL sur les contrôleurs de domaine
2. Activer LDAP Channel Binding
3. Activer LDAP Signing (signature des communications)

Via GPO :
Computer Config → Windows Settings → Security Settings
→ Local Policies → Security Options
→ "Domain controller: LDAP server signing requirements" → Require signing
```

### Sécuriser les Comptes Sensibles
```powershell
# Protéger les comptes administrateurs
# Activer Protected Users Security Group
Add-ADGroupMember "Protected Users" -Members "AdminAccount"

# Interdire la délégation Kerberos pour les admins
Set-ADUser adminUser -AccountNotDelegated $true

# Activer l'audit des connexions LDAP
# Activer dans les paramètres d'audit avancés AD
```

---

## 🟣 DNS

### Restreindre le Zone Transfer
```bash
# === BIND (/etc/named.conf) ===
zone "target.com" IN {
    type master;
    file "/var/named/target.com.zone";
    allow-transfer { 
        192.168.1.2;    # Seulement le DNS secondaire
        192.168.1.3;    # Si 2ème DNS secondaire
    };
    notify yes;
};

# Désactiver globalement par défaut
options {
    allow-transfer { none; };     # Bloquer par défaut
};
```

```powershell
# === Windows DNS Server ===
# DNS Manager → Zone → Propriétés → Zone Transfers
# → "Allow zone transfers" → Désactivé ou limité aux serveurs autorisés
```

### Split-DNS (Horizon divisé)
```
DNS Interne → Résolutions internes + externes
DNS Externe → Uniquement les services publics nécessaires
              Pas d'IP internes, pas d'infrastructure sensible
```

---

## 🟢 SMTP

### Désactiver VRFY et EXPN
```bash
# Postfix (/etc/postfix/main.cf)
disable_vrfy_command = yes
smtpd_helo_required = yes

# Sendmail (/etc/mail/sendmail.mc)
O PrivacyOptions=goaway,restrictmailq,restrictqrun

# Exchange - via PowerShell
Set-ReceiveConnector -Identity "Default" -SuppressXAnonymousTLS $true
```

### Rate Limiting SMTP
```bash
# Postfix - Limiter les connexions
smtpd_client_connection_rate_limit = 30
smtpd_client_message_rate_limit = 100
smtpd_error_sleep_time = 10s
smtpd_soft_error_limit = 3
smtpd_hard_error_limit = 10
```

---

## 🔴 NFS

### Sécuriser /etc/exports
```bash
# MAUVAISE configuration
/home  *                           # ← Tout le monde !
/etc   *(ro,sync)                  # ← Dangereux !

# BONNE configuration
/home/partage  192.168.1.0/24(rw,sync,no_root_squash)
/data/public   192.168.1.50(ro,sync,all_squash,anonuid=65534)

# Options importantes
no_root_squash  → root distant = root local (DANGEREUX)
root_squash     → root distant = nobody (SÉCURISÉ)
all_squash      → Tous les users → nobody
sync            → Écriture synchrone
```

### NFSv4 avec Kerberos
```bash
# /etc/exports avec Kerberos
/secure/share  *(sec=krb5p)   # Chiffrement + Auth Kerberos
```

---

## 🟡 FTP

| Mesure | Description |
|--------|-------------|
| Désactiver l'accès anonyme | Supprimer le login anonyme |
| Migrer vers SFTP | SSH File Transfer Protocol |
| Migrer vers FTPS | FTP over SSL/TLS |
| Chiffrer les credentials | FTPS (Explicite ou Implicite) |
| Limiter les plages IP | ACL sur le serveur FTP |
| Bannir après N tentatives | fail2ban / limites de taux |

---

## 📊 Tableau de Sécurité Global

| Service | Port | Contre-mesure Prioritaire | Niveau Urgence |
|---------|------|--------------------------|----------------|
| NetBIOS/SMB | 137-139,445 | Désactiver SMBv1, sessions nulles | 🔴 Critique |
| SNMP | 161/162 | Passer à SNMPv3 | 🔴 Critique |
| LDAP | 389 | Désactiver liaison anonyme, LDAPS | 🟠 Haute |
| DNS | 53 | Restreindre zone transfer | 🟠 Haute |
| SMTP | 25 | Désactiver VRFY/EXPN | 🟡 Moyenne |
| FTP | 21 | Désactiver anonyme, migrer SFTP | 🟠 Haute |
| NFS | 2049 | Restreindre exports, NFSv4+Kerberos | 🟠 Haute |
| NTP | 123 | Désactiver monlist | 🟡 Moyenne |

---

## 🔍 Audit & Surveillance

```bash
# Monitorer les tentatives d'énumération NetBIOS/SMB
# Windows Event Logs :
# Event ID 4625 : Échec de connexion
# Event ID 4776 : Validation credentials NTLM
# Event ID 4771 : Échec pre-auth Kerberos

# SIEM - Règles de détection
# - Multiples requêtes LDAP depuis une seule IP
# - Zone transfers DNS non autorisés
# - Scans de ports massifs
# - Requêtes SNMP depuis IPs inconnues
```

---

[← Outils & Commandes](./07-outils-commandes.md) | [IA & Énumération →](./09-ia-enumeration.md)
