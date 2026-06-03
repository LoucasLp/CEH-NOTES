# 🔴 Module 19 — Outils & Labs : Cloud Security

> **CEH v13 | EC-Council**  
> `#AWSCLI` `#ScoutSuite` `#Prowler` `#Pacu` `#TruffleHog`

---

## 1. AWS CLI — Commandes d'Audit

```bash
# Configuration :
aws configure
# Entrer : Access Key, Secret Key, Region, Output format

# Informations sur l'identité actuelle :
aws sts get-caller-identity

# ─── IAM ──────────────────────────────────────────────────────
aws iam list-users                          # Lister les utilisateurs
aws iam list-roles                          # Lister les rôles
aws iam list-groups                         # Lister les groupes
aws iam generate-credential-report          # Rapport des credentials
aws iam get-credential-report               # Télécharger le rapport
aws iam list-access-keys --user-name USER   # Clés d'accès d'un user

# ─── S3 ───────────────────────────────────────────────────────
aws s3 ls                                   # Lister les buckets
aws s3 ls s3://bucket-name                  # Contenu d'un bucket
aws s3api get-bucket-acl --bucket BUCKET    # ACL du bucket
aws s3api get-bucket-policy --bucket BUCKET # Policy du bucket

# ─── EC2 ──────────────────────────────────────────────────────
aws ec2 describe-instances                  # Toutes les instances
aws ec2 describe-security-groups            # Groupes de sécurité
aws ec2 describe-vpcs                       # VPCs
```

---

## 2. ScoutSuite — Audit Multi-Cloud

```bash
# Installation :
pip install scoutsuite

# Audit AWS :
scout aws                           # Avec credentials configurés
scout aws --profile production       # Avec un profil spécifique

# Rapport HTML généré dans ./scoutsuite-report/
# Ouvrir : scoutsuite-report/scoutsuite-results/scoutsuite_results_aws.html

# Audit Azure :
scout azure --cli                    # Via Azure CLI

# Audit GCP :
scout gcp                            # Via gcloud credentials
```

---

## 3. Prowler — AWS Security Best Practices

```bash
# Installation :
pip install prowler

# Audit complet AWS :
prowler aws

# Vérifications spécifiques :
prowler aws --checks s3_bucket_public_access_block_enabled
prowler aws --checks iam_root_mfa_enabled
prowler aws --service iam s3 cloudtrail

# Export CSV/HTML :
prowler aws -M csv html
```

---

## 4. Pacu — AWS Exploitation Framework

```bash
# Démarrer Pacu :
python3 pacu.py

# Dans Pacu :
set_keys                            # Configurer les Access Keys
run iam__enum_users_roles_policies  # Enumérer IAM
run iam__privesc_scan               # Scanner les possibilités d'escalade
run s3__enum                        # Enumérer les buckets S3
run ec2__enum                       # Enumérer les instances EC2
run lambda__enum                    # Enumérer les fonctions Lambda
run cloudtrail__download_event_history  # Télécharger les logs CloudTrail
```

---

## 5. TruffleHog & GitLeaks

```bash
# TruffleHog — Scanner les secrets dans Git :
trufflehog git https://github.com/target/repo
trufflehog git file:///path/to/local/repo
trufflehog s3 --bucket target-bucket      # Scanner un bucket S3

# GitLeaks :
gitleaks detect --source=. --verbose
gitleaks detect --config .gitleaks.toml --source=.

# Dans un pipeline CI/CD :
gitleaks protect --staged -v            # Avant chaque commit
```

---

## 🧠 Points clés à retenir pour l'examen

- **ScoutSuite** = audit de configuration multi-cloud (AWS, Azure, GCP)
- **Prowler** = audit des best practices AWS de sécurité
- **Pacu** = framework d'exploitation AWS (pentest cloud)
- **TruffleHog** = trouver des secrets dans les repos Git
- **`aws sts get-caller-identity`** = identifier qui on est dans AWS
- **`aws s3 ls --no-sign-request`** = tester l'accès anonyme S3

---

*⬅️ [Techniques d'attaques](./03_techniques_attaques.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
