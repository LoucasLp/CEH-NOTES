# 🔴 Module 19 — Menaces Cloud : CSA Top Threats

> **CEH v13 | EC-Council**  
> `#Misconfiguration` `#IAM` `#AccountHijacking` `#SSRF` `#Cryptojacking`

---

## CSA (Cloud Security Alliance) Top Threats

### Menace #1 — Misconfiguration (Erreur de Configuration)

```
La menace cloud la plus répandue

Exemples AWS :
  ✗ S3 Bucket en accès public
    → Des millions de fichiers d'entreprises exposés en clair
    → Capital One breach (2019) : 100M clients exposés
    
  ✗ Security Group avec 0.0.0.0/0 (ouvert à Internet)
    → Serveur RDS accessible depuis Internet !
    
  ✗ IAM policy avec AdministratorAccess pour tous
    → N'importe qui peut tout faire
    
  ✗ CloudTrail désactivé
    → Pas de logs = pas de détection d'incident
    
Test de misconfiguration S3 :
  aws s3 ls s3://bucket-name --no-sign-request  ← Accès anonyme ?
  curl https://bucket-name.s3.amazonaws.com/    ← Liste des objets ?
```

### Menace #2 — Inadequate Identity and Access Management

```
Excessive permissions :
  IAM user avec AdministratorAccess
  Rôle EC2 avec FullS3Access (toujours)
  
Long-lived credentials :
  Access Keys jamais rotées (depuis 3 ans)
  Access Keys dans le code source (GitHub leak !)
  
Test :
  git grep "AKIA" .         # Chercher des Access Keys AWS dans le code
  trufflehog git file:///path/to/repo  # Scanner les secrets dans git

Contre-mesure :
  Moindre privilège : chaque entité a seulement les droits nécessaires
  Rotation régulière des clés
  MFA sur tous les comptes
  AWS Organizations + SCPs pour limiter au niveau organisationnel
```

### Menace #3 — Insecure APIs

```
APIs cloud exposées sans authentification ou avec auth faible

Exemples :
  API Gateway sans authentification (API Key absente)
  Endpoint REST exposé avec données sensibles
  GraphQL introspection activée → schéma complet exposé
  
Test :
  # Découvrir des endpoints non documentés :
  ffuf -u https://api.site.com/FUZZ -w api_wordlist.txt
```

### Menace #4 — Account Hijacking

```
Voler les credentials d'un compte cloud

Vecteurs :
  Phishing ciblé (spear-phishing) contre admin cloud
  Credentials dans le code source (GitHub, GitLab public)
  Credentials dans les logs (Lambda logs lisibles)
  SSRF → Metadata service → IAM credentials
  
Impact :
  → Accès total si compte admin
  → Exfiltration de données
  → Cryptojacking (miner de la crypto)
  → Pivot vers d'autres ressources
```

### Menace Spéciale — SSRF → Metadata Service

```
Exploitation SSRF pour obtenir des credentials IAM via le metadata service AWS

URL metadata : http://169.254.169.254/latest/meta-data/

Via SSRF vulnérable :
  https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/

Réponse :
  {
    "AccessKeyId": "ASIA...",
    "SecretAccessKey": "...",
    "Token": "...",
    "Expiration": "2024-01-01T12:00:00Z"
  }
  
→ Credentials temporaires de l'instance EC2 !
→ Utiliser pour accéder à tous les services AWS autorisés par le rôle

Protection : IMDSv2 (Instance Metadata Service v2) :
  Nécessite un token préalable (PUT request) avant d'accéder aux métadonnées
  → SSRF simple ne peut plus obtenir les credentials
```

### Autres Menaces

```
Cryptojacking :
  Attaquant compromet un compte cloud → déploie des instances pour miner de la crypto
  → Factures AWS astronomiques pour la victime

Shadow IT :
  Employés créent des ressources cloud hors du contrôle IT
  → Ressources non sécurisées, non auditées

Side-channel attacks :
  VM co-résidence sur le même hôte physique
  → Flush+Reload, Rowhammer (rare, théorique)
```

---

## 🧠 Points clés à retenir pour l'examen

- **Misconfiguration** = menace cloud #1 (S3 public, IAM trop large)
- **SSRF → 169.254.169.254** = voler les credentials IAM d'une EC2
- **IMDSv2** = protection contre SSRF via metadata service
- **CloudTrail** = obligatoire pour détecter les incidents cloud
- **Access Keys dans GitHub** = fuite de credentials très fréquente
- **Cryptojacking** = utiliser les ressources cloud de la victime pour miner

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques d'attaques](./03_techniques_attaques.md)*
