# 🛡️ Module 19 — Contre-mesures : Cloud Security

> **CEH v13 | EC-Council**  
> `#IAMBestPractices` `#CloudTrail` `#S3Security` `#CSPM` `#ZeroTrust`

---

## IAM Best Practices

```
1. Moindre privilège
   → Chaque utilisateur/rôle n'a que les droits nécessaires
   → Auditer régulièrement avec Prowler/ScoutSuite

2. MFA obligatoire
   → Sur tous les comptes, surtout root et admins
   → Hardware MFA (YubiKey) pour les accès critiques

3. Désactiver le compte root AWS
   → Ne jamais utiliser root pour les opérations courantes
   → Créer des comptes IAM individuels

4. Rotation des Access Keys
   → Rotation tous les 90 jours minimum
   → Supprimer les clés inutilisées
   → aws iam generate-credential-report → analyser

5. Pas de credentials dans le code
   → Utiliser les rôles IAM sur les EC2 (Instance Profiles)
   → Secrets Manager pour les secrets applicatifs
   → GitLeaks/TruffleHog dans le CI/CD
```

## S3 Security

```
# Bloquer tout accès public au niveau du compte :
aws s3control put-public-access-block \
  --account-id 123456789012 \
  --public-access-block-configuration BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true

# Chiffrement des objets S3 :
aws s3api put-bucket-encryption --bucket my-bucket \
  --server-side-encryption-configuration '{"Rules":[{"ApplyServerSideEncryptionByDefault":{"SSEAlgorithm":"AES256"}}]}'

# Activer le versioning :
aws s3api put-bucket-versioning --bucket my-bucket \
  --versioning-configuration Status=Enabled
```

## CloudTrail + Monitoring

```
# CloudTrail = journal de toutes les actions AWS

# Activer CloudTrail dans toutes les régions :
aws cloudtrail create-trail --name GlobalTrail \
  --s3-bucket-name my-cloudtrail-bucket \
  --is-multi-region-trail

# Alertes importantes à configurer :
  - Root account login
  - IAM user creation
  - Security group modification
  - S3 bucket policy change
  - CloudTrail disabled
  
# Outils de détection :
  GuardDuty  : Détection automatique des menaces AWS
  Security Hub : Tableau de bord de conformité
  Config Rules : Vérification de conformité des ressources
```

## Protection contre SSRF (IMDSv2)

```bash
# Forcer IMDSv2 sur toutes les nouvelles instances :
aws ec2 modify-instance-metadata-options --instance-id i-xxx \
  --http-tokens required \
  --http-endpoint enabled

# IMDSv2 nécessite un token :
TOKEN=$(curl -X PUT "http://169.254.169.254/latest/api/token" \
  -H "X-aws-ec2-metadata-token-ttl-seconds: 21600")

curl -H "X-aws-ec2-metadata-token: $TOKEN" \
  http://169.254.169.254/latest/meta-data/

# Un SSRF simple ne peut plus obtenir les credentials (pas de PUT possible)
```

---

## Matrice des Contre-mesures

| Menace | Contre-mesure |
|--------|---------------|
| S3 misconfiguration | Block Public Access + S3 Bucket Policy |
| IAM trop permissif | Moindre privilège + audit Prowler |
| Credentials dans le code | GitLeaks + Secrets Manager |
| SSRF → metadata | IMDSv2 obligatoire |
| No logging | CloudTrail activé (toutes régions) |
| Account hijacking | MFA obligatoire + alertes GuardDuty |
| Cryptojacking | Alertes coût AWS + GuardDuty |

---

## 🧠 Points clés à retenir pour l'examen

- **Shared Responsibility** = AWS gère l'infra, le client gère la config
- **CloudTrail** = activer dans TOUTES les régions (pas seulement une)
- **IMDSv2** = protection contre SSRF → metadata credentials
- **Block Public Access** = S3 protection au niveau compte
- **GuardDuty** = détection automatique des menaces AWS
- **MFA obligatoire** = protection du compte root et des admins

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
