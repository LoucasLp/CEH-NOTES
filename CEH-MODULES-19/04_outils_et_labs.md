# 🔴 Module 19 — Outils & Labs : Cloud Security

> **CEH v13 | EC-Council**  
> `#AWSCLI` `#ScoutSuite` `#Prowler` `#Pacu` `#TruffleHog`

---

## 📌 Table des matières

1. [AWS CLI — Commandes d'Audit](#1-aws-cli--commandes-daudit)
2. [ScoutSuite — Audit Multi-Cloud](#2-scoutsuite--audit-multi-cloud)
3. [Prowler — AWS Security Best Practices](#3-prowler--aws-security-best-practices)
4. [Pacu — AWS Exploitation Framework](#4-pacu--aws-exploitation-framework)
5. [TruffleHog & GitLeaks](#5-trufflehog--gitleaks)

---

## 1. AWS CLI — Commandes d'Audit

```bash
# Configuration initiale :
aws configure
# Entrer : Access Key, Secret Key, Region, Output format

# Informations sur l'identité actuelle :
aws sts get-caller-identity

# ─── IAM ──────────────────────────────────────────────────────
aws iam list-users                          # Lister les utilisateurs
aws iam list-roles                          # Lister les rôles
aws iam list-groups                         # Lister les groupes
aws iam generate-credential-report          # Générer rapport des credentials
aws iam get-credential-report               # Télécharger le rapport CSV
aws iam list-access-keys --user-name USER   # Clés d'accès d'un user

# ─── S3 ───────────────────────────────────────────────────────
aws s3 ls                                   # Lister les buckets
aws s3 ls s3://bucket-name                  # Contenu d'un bucket
aws s3api get-bucket-acl --bucket BUCKET    # ACL du bucket
aws s3api get-bucket-policy --bucket BUCKET # Policy du bucket

# ─── EC2 ──────────────────────────────────────────────────────
aws ec2 describe-instances                  # Toutes les instances
aws ec2 describe-security-groups            # Groupes de sécurité (firewalls)
aws ec2 describe-vpcs                       # VPCs
aws ec2 describe-network-interfaces         # Interfaces réseau

# ─── Sécurité ─────────────────────────────────────────────────
aws cloudtrail describe-trails              # Vérifier CloudTrail
aws guardduty list-detectors                # GuardDuty actif ?
aws securityhub describe-hub               # Security Hub actif ?
```

---

## 2. ScoutSuite — Audit Multi-Cloud

```bash
# Installation :
pip install scoutsuite

# Audit AWS (avec credentials configurés) :
scout aws                           # Profil par défaut
scout aws --profile production       # Avec un profil spécifique
scout aws --regions eu-west-1 us-east-1  # Régions spécifiques

# Rapport HTML généré dans ./scoutsuite-report/
# Ouvrir : scoutsuite-report/scoutsuite_results_aws.html

# Audit Azure :
scout azure --cli                    # Via Azure CLI (az login préalable)

# Audit GCP :
scout gcp --user-account              # Via gcloud credentials
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
prowler aws --checks cloudtrail_multi_region_enabled

# Audit par service :
prowler aws --service iam s3 cloudtrail ec2

# Export des résultats :
prowler aws -M csv html json
```

---

## 4. Pacu — AWS Exploitation Framework

```bash
# Démarrer Pacu :
python3 pacu.py

# Dans Pacu (interface interactive) :
set_keys                            # Configurer les Access Keys cibles
run iam__enum_users_roles_policies  # Enumérer IAM (users, roles, policies)
run iam__privesc_scan               # Scanner les possibilités d'escalade de privilèges
run iam__backdoor_users_keys        # Créer des backdoors IAM persistantes
run s3__enum                        # Enumérer les buckets S3 et leurs permissions
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
trufflehog --only-verified                 # Vérifier que les secrets sont actifs

# GitLeaks :
gitleaks detect --source=. --verbose
gitleaks detect --config .gitleaks.toml --source=.
gitleaks protect --staged -v            # Dans le CI/CD, avant chaque commit

# Recherche manuelle dans git :
git log --all --oneline | head -50
git grep "AKIA" -- "*.py" "*.js"       # Chercher dans le code actuel
git grep "AKIA" $(git log --all --pretty=format:"%H")  # Dans tout l'historique
```

---

## 🧠 Points clés à retenir pour l'examen

- **ScoutSuite** = audit de configuration multi-cloud (AWS, Azure, GCP) — rapport HTML
- **Prowler** = audit des best practices de sécurité AWS (conformité CIS)
- **Pacu** = framework d'exploitation AWS (pentest cloud, Metasploit du cloud AWS)
- **TruffleHog** = trouver des secrets dans les repos Git (historique complet)
- **`aws sts get-caller-identity`** = identifier qui on est dans AWS (première commande)
- **`aws s3 ls --no-sign-request`** = tester l'accès anonyme S3 sans credentials

---

*⬅️ [Techniques d'attaques](./03_techniques_attaques.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
