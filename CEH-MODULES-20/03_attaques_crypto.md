# 🔴 Module 20 — Attaques Cryptographiques

> **CEH v13 | EC-Council**  
> `#BruteForce` `#RainbowTables` `#Birthday` `#POODLE` `#HEARTBLEED` `#BEAST`

---

## 1. Brute Force et Dictionnaire

```
Brute Force :
  Essayer toutes les combinaisons possibles de la clé/mot de passe
  
  AES-256 brute force : 2^256 = 10^77 opérations → infeasible avec technologie actuelle
  MD5 d'un MDP de 8 chars [a-z0-9] : 36^8 ≈ 2.8 × 10^12 → quelques heures GPU !
  
Dictionary Attack :
  Utiliser des listes de mots de passe communs
  Rockyou.txt : 14 millions de MDP réels volés en 2009
  
  Hashcat (GPU) :
  hashcat -m 0 hash.txt rockyou.txt      # MD5
  hashcat -m 1800 hash.txt rockyou.txt   # SHA-512 Unix
  hashcat -m 3200 hash.txt rockyou.txt   # bcrypt → très lent !
```

---

## 2. Rainbow Tables

```
Problème : Brute force mot de passe = lent car recalcul à chaque fois

Solution Rainbow Tables :
  Pré-calculer : hash(password) pour des millions de mots de passe
  Stocker dans une table : hash → password
  
  Lors de l'attaque : Chercher le hash dans la table → résultat immédiat !
  
Protection : SALTING
  hash = SHA256(password + random_salt)
  Exemple :
    Salt = "X7k2mN"
    Hash = SHA256("P@ssw0rd" + "X7k2mN") = différent pour chaque user !
  
  Avec salt : Les rainbow tables pré-calculées sont inutiles
              (il faudrait une table pour chaque salt possible)
```

---

## 3. Birthday Attack

```
Paradoxe des anniversaires :
  Dans un groupe de 23 personnes : 50% de chance que 2 aient le même anniversaire
  (Malgré 365 jours possibles)
  
Application à la cryptographie :
  Hash de N bits → Collision attendue après ~2^(N/2) tentatives
  
  SHA-1 (160 bits) → Collision attendue après ~2^80 = 10^24 tentatives
  MD5 (128 bits)   → Collision attendue après ~2^64 = 10^19 tentatives
  
Impact : Si deux messages différents donnent le même hash (collision) :
  → Substitution de document signé numériquement
  → MD5 collisions démontrées en 2004
  → SHA-1 collision démontrée en 2017 (SHAttered attack, Google/CWI)
```

---

## 4. Attaques SSL/TLS

### POODLE (Padding Oracle On Downgraded Legacy Encryption) — 2014

```
Cible : SSLv3 avec CBC mode
Condition : L'attaquant peut forcer un downgrade vers SSLv3
Mécanisme : Padding oracle sur le chiffrement CBC en SSLv3

Protection :
  → Désactiver SSLv3 complètement
  → TLS_FALLBACK_SCSV header (empêche le downgrade forcé)
```

### BEAST (Browser Exploit Against SSL/TLS) — 2011

```
Cible : TLS 1.0 avec CBC mode
Mécanisme : Exploite la prévisibilité de l'IV en TLS 1.0
Protection :
  → Utiliser TLS 1.1+ (IV aléatoire)
  → Prioriser les RC4 cipher suites (contournement temporaire)
  → Aujourd'hui : Désactiver TLS 1.0 tout simplement
```

### HEARTBLEED (CVE-2014-0160) — 2014

```
Cible : OpenSSL 1.0.1 - 1.0.1f (extension Heartbeat de TLS)
Mécanisme :
  Client envoie : "Lis 64KB de mémoire et renvoie-les"
  OpenSSL ne vérifie pas la taille réelle → lit la mémoire serveur !
  → Leak de clé privée, sessions TLS actives, credentials
  
Impact : 500 000+ serveurs vulnérables en 2014
Protection : Mettre à jour OpenSSL vers 1.0.1g+
```

### CRIME et BREACH

```
CRIME (2012) : Exploite la compression TLS (DEFLATE)
  → Inférer les cookies de session via l'oracle de compression
  Protection : Désactiver la compression TLS
  
BREACH (2013) : Version HTTP de CRIME
  → Exploite la compression HTTP gzip
  Protection : Désactiver gzip pour les pages avec données sensibles
  Ou : CSRF token unique par requête
```

### FREAK et LOGJAM

```
FREAK (2015) :
  Forcer l'utilisation de chiffrement "export" (40/56 bits) → cassable
  Protection : Désactiver les cipher suites "export"

LOGJAM (2015) :
  Downgrade Diffie-Hellman vers 512 bits → cassable
  Protection : Clés DH de 2048 bits minimum
```

---

## 5. Side Channel Attacks

```
Attaques qui exploitent les informations physiques de l'implémentation :

Timing Attack :
  Mesurer le temps de réponse pour déduire la clé
  Ex: Comparaison de chaînes non-constante en temps
  Protection : Comparaisons en temps constant

Power Analysis (SPA/DPA) :
  Mesurer la consommation électrique pendant le chiffrement
  Déduire les opérations effectuées → Déduire la clé

Cache timing :
  Accès au cache CPU révèlent des informations sur la clé
  → Flush+Reload, Spectre/Meltdown (2018)
```

---

## 🧠 Points clés à retenir pour l'examen

| Attaque | Cible | Protection |
|---------|-------|------------|
| Brute force | Clés faibles | Clés longues, AES-256 |
| Dictionary attack | Mots de passe faibles | bcrypt/Argon2 + complexité |
| Rainbow tables | Hashes sans salt | SALTING obligatoire |
| Birthday attack | MD5, SHA-1 | SHA-256+ |
| POODLE | SSLv3 CBC | Désactiver SSLv3 |
| HEARTBLEED | OpenSSL 1.0.1 | Patcher OpenSSL |
| BEAST | TLS 1.0 CBC | TLS 1.2+ |
| CRIME | Compression TLS | Désactiver compression |

---

*⬅️ [Algorithmes](./02_algorithmes.md) | ➡️ [PKI et Certificats](./04_pki_et_certificats.md)*
