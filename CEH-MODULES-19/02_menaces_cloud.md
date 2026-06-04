# 🔴 Module 19 — Menaces Cloud : CSA Top Threats

> **CEH v13 | EC-Council**  
> `#Misconfiguration` `#IAM` `#AccountHijacking` `#SSRF` `#Cryptojacking`

---

## 📌 Table des matières

1. [Menace #1 — Misconfiguration](#1-menace-1--misconfiguration)
2. [Menace #2 — IAM Inadéquat](#2-menace-2--iam-inadéquat)
3. [Menace #3 — APIs Insécurisées](#3-menace-3--apis-insécurisées)
4. [Menace #4 — Account Hijacking](#4-menace-4--account-hijacking)
5. [Menace Spéciale — SSRF → Metadata Service](#5-menace-spéciale--ssrf--metadata-service)
6. [Autres Menaces Cloud](#6-autres-menaces-cloud)

---

## 1. Menace #1 — Misconfiguration

```
La menace cloud la plus répandue (CSA Top Threat #1)

Exemples AWS :
  ✗ S3 Bucket en accès public
    → Des millions de fichiers d'entreprises exposés en clair
    → Capital One breach (2019) : 100M clients exposés via SSRF + S3 mal configuré

  ✗ Security Group avec 0.0.0.0/0 (ouvert à tout Internet)
    → Serveur RDS (base de données) accessible depuis Internet !

  ✗ IAM policy avec AdministratorAccess pour tous les utilisateurs
    → N'importe qui dans l'organisation peut tout faire

  ✗ CloudTrail désactivé
    → Pas de logs = pas de détection d'incident = forensics impossible

Test de misconfiguration S3 :
  aws s3 ls s3://bucket-name --no-sign-request  ← Accès anonyme autorisé ?
  curl https://bucket-name.s3.amazonaws.com/    ← Liste des objets exposée ?
```

> ⚠️ **Rappel CEH** : Les misconfigurations sont la responsabilité du **CLIENT** dans le Shared Responsibility Model. AWS ne configure pas les permissions à ta place.

---

## 2. Menace #2 — IAM Inadéquat

```
Excessive permissions (violation du moindre privilège) :
  IAM user avec AdministratorAccess alors qu'il ne fait que du S3
  Rôle EC2 avec FullS3Access permanent (au lieu de permissions temporaires)

Long-lived credentials :
  Access Keys jamais rotées (depuis 3 ans, 5 ans...)
  Access Keys dans le code source (GitHub leak !)
  Access Keys dans les logs CloudWatch lisibles par tous

Test :
  git grep "AKIA" .         # Chercher des Access Keys AWS dans le code source
  trufflehog git file:///path/to/repo  # Scanner les secrets dans l'historique git
  gitleaks detect --source .           # Autre scanner de secrets

Contre-mesure :
  Moindre privilège : chaque entité a seulement les droits nécessaires
  Rotation régulière des clés (90 jours max)
  MFA obligatoire sur tous les comptes (surtout root)
  AWS Organizations + SCPs pour limiter au niveau organisationnel
```

---

## 3. Menace #3 — APIs Insécurisées

```
APIs cloud exposées sans authentification ou avec auth faible

Exemples :
  API Gateway sans authentification (API Key absente ou exposée)
  Endpoint REST retournant des données sensibles sans vérification d'identité
  GraphQL introspection activée → schéma complet de l'API exposé
  Swagger/OpenAPI doc exposée publiquement → carte de l'API pour un attaquant

Test :
  # Découvrir des endpoints non documentés :
  ffuf -u https://api.site.com/FUZZ -w api_wordlist.txt

  # Tester GraphQL :
  curl -X POST https://api.site.com/graphql \
    -d '{"query":"{ __schema { types { name } } }"}'
  # → Si répond → introspection activée → cartographier toute l'API
```

---

## 4. Menace #4 — Account Hijacking

```
Voler les credentials d'un compte cloud pour prendre le contrôle

Vecteurs d'attaque :
  Phishing ciblé (spear-phishing) contre admin cloud
  Credentials dans le code source (GitHub, GitLab public)
  Credentials dans les logs (Lambda logs lisibles par tous)
  SSRF → Metadata service → IAM credentials temporaires
  Password spraying sur portail AWS (si MFA absent)

Impact selon le compte compromis :
  → Compte admin : accès total à TOUTE l'infrastructure
  → Compte lambda : accès aux services via le rôle IAM attaché
  → Compte S3 : exfiltration de données
  → Cryptojacking : miner de la crypto avec les ressources de la victime
  → Ransom : chiffrer les S3 buckets ou supprimer les sauvegardes
```

---

## 5. Menace Spéciale — SSRF → Metadata Service

```
Exploitation SSRF pour obtenir des credentials IAM via le metadata service AWS

URL metadata AWS : http://169.254.169.254/latest/meta-data/
URL metadata Azure: http://169.254.169.254/metadata/instance

Via SSRF vulnérable :
  https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/

Réponse (credentials temporaires du rôle IAM de l'instance) :
  {
    "AccessKeyId": "ASIA...",
    "SecretAccessKey": "...",
    "Token": "...",
    "Expiration": "2024-01-01T12:00:00Z"
  }

→ Credentials temporaires de l'instance EC2 avec toutes ses permissions !
→ Utiliser pour pivoter vers tous les services AWS autorisés par le rôle

Protection — IMDSv2 (Instance Metadata Service v2) :
  Nécessite un token préalable (PUT request avec TTL) avant d'accéder aux métadonnées
  → SSRF simple (GET seul) ne peut plus obtenir les credentials
  → Défense en profondeur contre Capital One-style attacks
```

> 💡 **Question type exam** : *Quelle URL est utilisée pour accéder au metadata service AWS depuis une instance EC2 ?* → `http://169.254.169.254/latest/meta-data/`

---

## 6. Autres Menaces Cloud

```
Cryptojacking :
  Attaquant compromet un compte cloud → déploie des instances EC2/Lambda
  → Mine de la crypto (Monero, Bitcoin) avec les ressources de la victime
  → Factures AWS astronomiques pour la victime (des milliers d'€ par jour)
  → Détection : pic inhabituel de CPU, nouvelles instances non autorisées

Shadow IT :
  Employés créent des ressources cloud hors du contrôle IT/Sécurité
  → Ressources non sécurisées, non auditées, non patchées
  → Découverte via CloudTrail + audit régulier des ressources

Side-channel attacks (théoriques) :
  VM co-résidence sur le même hôte physique chez le fournisseur
  → Flush+Reload, Rowhammer (rare, très difficile à exploiter en pratique)
  → Protégé par l'isolation hardware des hyperviseurs modernes

Insider Threat Cloud :
  Employé malveillant avec accès IAM → exfiltration via S3, snapshot EBS
  → Importance du principe du moindre privilège + logging CloudTrail
```

---

## 🧠 Points clés à retenir pour l'examen

- **Misconfiguration** = menace cloud #1 CSA (S3 public, IAM trop large)
- **SSRF → 169.254.169.254** = voler les credentials IAM temporaires d'une EC2
- **IMDSv2** = protection contre SSRF via metadata service (token préalable requis)
- **CloudTrail** = obligatoire pour détecter et analyser les incidents cloud
- **Access Keys dans GitHub** = fuite de credentials très fréquente (TruffleHog, GitLeaks)
- **Cryptojacking** = utiliser les ressources cloud de la victime pour miner de la crypto
- **Shared Responsibility** = misconfigs = responsabilité du CLIENT, pas d'AWS

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques d'attaques](./03_techniques_attaques.md)*
