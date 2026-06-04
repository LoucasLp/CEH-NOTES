# 🛡️ Module 19 — Contre-mesures : Cloud Security

> **CEH v13 | EC-Council**  
> `#IAMBestPractices` `#CloudTrail` `#S3Security` `#CSPM` `#ZeroTrust`

---

## 📌 Table des matières

1. [IAM Best Practices](#1-iam-best-practices)
2. [S3 Security](#2-s3-security)
3. [CloudTrail + Monitoring](#3-cloudtrail--monitoring)
4. [Protection contre SSRF (IMDSv2)](#4-protection-contre-ssrf-imdsv2)
5. [Matrice des Contre-mesures](#5-matrice-des-contre-mesures)

---

## 1. IAM Best Practices

```
1. Principe du moindre privilège
   → Chaque utilisateur/rôle n'a que les droits strictement nécessaires
   → Auditer régulièrement avec Prowler/ScoutSuite
   → Supprimer les policies inutilisées

2. MFA obligatoire
   → Sur tous les comptes, surtout root et administrateurs
   → Hardware MFA (YubiKey) pour les accès critiques
   → Virtual MFA (Authenticator app) pour les utilisateurs standards

3. Désactiver le compte root AWS
   → Ne jamais utiliser root pour les opérations courantes
   → Créer des comptes IAM individuels avec permissions limitées
   → Sécuriser le root avec MFA hardware et ne pas créer d'Access Keys

4. Rotation des Access Keys
   → Rotation tous les 90 jours minimum
   → Supprimer les clés inutilisées (>90j sans utilisation)
   → aws iam generate-credential-report → analyser et corriger

5. Pas de credentials dans le code
   → Utiliser les rôles IAM sur les EC2 (Instance Profiles)
   → AWS Secrets Manager pour les secrets applicatifs
   → GitLeaks/TruffleHog dans le CI/CD pour prévenir les fuites
```

---

## 2. S3 Security

```bash
# Bloquer tout accès public au niveau du compte AWS :
aws s3control put-public-access-block \
  --account-id 123456789012 \
  --public-access-block-configuration \
    BlockPublicAcls=true,IgnorePublicAcls=true,\
    BlockPublicPolicy=true,RestrictPublicBuckets=true

# Chiffrement automatique des objets S3 (SSE-S3) :
aws s3api put-bucket-encryption --bucket my-bucket \
  --server-side-encryption-configuration \
  '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'

# Activer le versioning (protection contre suppression/ransomware) :
aws s3api put-bucket-versioning --bucket my-bucket \
  --versioning-configuration Status=Enabled

# Activer l'accès logging :
aws s3api put-bucket-logging --bucket my-bucket \
  --bucket-logging-status '{"LoggingEnabled":{"TargetBucket":"my-logs","TargetPrefix":"s3/"}}'
```

---

## 3. CloudTrail + Monitoring

```bash
# CloudTrail = journal de toutes les actions AWS (qui a fait quoi, quand, depuis où)

# Activer CloudTrail dans toutes les régions :
aws cloudtrail create-trail --name GlobalTrail \
  --s3-bucket-name my-cloudtrail-bucket \
  --is-multi-region-trail \
  --enable-log-file-validation

# Démarrer le logging :
aws cloudtrail start-logging --name GlobalTrail
```

```
Alertes critiques à configurer (via CloudWatch + SNS) :
  ├── Root account login           → Toujours suspect
  ├── IAM user creation            → Nouveau compte ?
  ├── Security Group modification  → Ouverture de ports ?
  ├── S3 bucket policy change      → Exposition de données ?
  ├── CloudTrail disabled          → Attaquant couvrant ses traces !
  └── MFA désactivé sur root       → Compromission imminente

Outils de détection complémentaires :
  GuardDuty  : Détection automatique des menaces AWS (ML)
  Security Hub : Tableau de bord de conformité centralisé
  Config Rules : Vérification continue de conformité des ressources
```

---

## 4. Protection contre SSRF (IMDSv2)

```bash
# IMDSv2 = Instance Metadata Service v2 (protection contre SSRF)

# Forcer IMDSv2 sur une instance existante :
aws ec2 modify-instance-metadata-options --instance-id i-xxx \
  --http-tokens required \
  --http-endpoint enabled

# Forcer IMDSv2 sur toutes les nouvelles instances (via Account Settings) :
aws ec2 modify-instance-metadata-defaults \
  --http-tokens required

# Comment IMDSv2 fonctionne (sans SSRF possible) :
# Étape 1 : Obtenir un token (PUT request avec TTL)
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

# Étape 2 : Utiliser le token pour accéder aux metadata
curl -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/

# Pourquoi SSRF ne fonctionne plus avec IMDSv2 :
# → Un SSRF ne peut faire que des GET (requête simple)
# → Le PUT préalable est impossible via SSRF simple
# → Sans token valide → 401 Unauthorized → credentials inaccessibles
```

---

## 5. Matrice des Contre-mesures

| Menace | Contre-mesure principale |
|--------|--------------------------|
| S3 misconfiguration | Block Public Access au niveau compte + bucket policy |
| IAM trop permissif | Principe du moindre privilège + audit Prowler |
| Credentials dans le code | GitLeaks dans CI/CD + Secrets Manager |
| SSRF → metadata | IMDSv2 obligatoire sur toutes les instances |
| Pas de logging | CloudTrail multi-région + S3 access logging |
| Account hijacking | MFA obligatoire + alertes GuardDuty |
| Cryptojacking | Alertes budget AWS + GuardDuty (détecte les mineurs) |
| Container escape | Jamais de conteneurs privileged + Pod Security Standards |

---

## 🧠 Points clés à retenir pour l'examen

- **Shared Responsibility** = AWS gère l'infra physique, le client gère la configuration
- **CloudTrail** = activer dans **TOUTES les régions** et valider l'intégrité des logs
- **IMDSv2** = protection contre SSRF → metadata credentials (token PUT préalable requis)
- **Block Public Access** = protection S3 au niveau du compte entier
- **GuardDuty** = détection automatique des menaces AWS (cryptojacking, credential theft...)
- **MFA obligatoire** = protection du compte root et des administrateurs
- **Secrets Manager** = alternative sécurisée aux credentials hardcodés dans le code

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
