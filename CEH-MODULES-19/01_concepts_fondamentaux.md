# 🔴 Module 19 — Concepts Fondamentaux : Cloud Computing

> **CEH v13 | EC-Council**  
> `#IaaS` `#PaaS` `#SaaS` `#SharedResponsibility` `#AWS` `#Azure` `#GCP`

---

## 📌 Table des matières

1. [Modèles de Service Cloud](#1-modèles-de-service-cloud)
2. [Modèles de Déploiement](#2-modèles-de-déploiement)
3. [Shared Responsibility Model](#3-shared-responsibility-model)
4. [Services Cloud Importants (AWS)](#4-services-cloud-importants-aws)
5. [Virtualisation et Conteneurs](#5-virtualisation-et-conteneurs)

---

## 1. Modèles de Service Cloud

```
┌─────────────────────────────────────────────────────────────────────┐
│  SaaS ──────────────────────────────────────────────────────────    │
│  Tu utilises l'application. Ex: Gmail, Salesforce, Office 365       │
│  Fournisseur gère TOUT (infrastructure, OS, middleware, app)         │
├─────────────────────────────────────────────────────────────────────┤
│  PaaS ──────────────────────────────────────────────────────────    │
│  Tu déploies ton code. Ex: Heroku, AWS Elastic Beanstalk            │
│  Fournisseur gère : infra, OS, runtime ; Tu gères : code, data      │
├─────────────────────────────────────────────────────────────────────┤
│  IaaS ──────────────────────────────────────────────────────────    │
│  Tu loues des machines virtuelles. Ex: AWS EC2, Azure VMs           │
│  Fournisseur gère : data center, hardware, hyperviseur              │
│  Tu gères : OS, middleware, apps, données                           │
└─────────────────────────────────────────────────────────────────────┘
```

| Modèle | Géré par le fournisseur | Géré par le client | Exemples |
|--------|------------------------|-------------------|---------|
| **SaaS** | Tout | Rien (sauf les données utilisateur) | Gmail, Salesforce, Office 365 |
| **PaaS** | Infra + OS + Runtime | Code + Données | Heroku, AWS Beanstalk, Azure App Service |
| **IaaS** | Datacenter + Hardware + Hyperviseur | OS + Middleware + Apps + Données | AWS EC2, Azure VMs, GCP Compute |

> 💡 **Question type exam** : *Lequel offre le plus de contrôle au client ?* → **IaaS** (gère l'OS). *Lequel offre le moins de contrôle mais le plus de facilité ?* → **SaaS**

---

## 2. Modèles de Déploiement

| Modèle | Description | Exemples |
|--------|-------------|---------|
| **Public** | Infrastructure partagée, multi-tenant | AWS, Azure, GCP |
| **Private** | Infrastructure dédiée (on-premise ou hébergée) | VMware Private Cloud, OpenStack |
| **Hybrid** | Mix public + private (workloads répartis) | AWS + datacenter interne |
| **Community** | Partagé entre organisations similaires | Clouds gouvernementaux, secteur santé |

---

## 3. Shared Responsibility Model

```
AWS Shared Responsibility Model :

FOURNISSEUR (AWS) gère :
  ├── Datacenters physiques (sécurité physique, badges, caméras)
  ├── Hyperviseur et virtualisation
  ├── Réseau AWS (backbone, switches, routers physiques)
  └── Matériel (serveurs, stockage physique)

CLIENT (vous) gère :
  ├── Système d'exploitation et patches (pour EC2)
  ├── Chiffrement des données (at-rest et in-transit)
  ├── Configuration des Security Groups (firewalls)
  ├── Gestion IAM (qui a accès à quoi)
  ├── Données clients et leur classification
  └── Configuration des services (S3 public/privé !)

Modèle change selon le service :
  EC2 (IaaS) : Client gère tout jusqu'à l'OS inclus
  RDS (PaaS) : AWS gère l'OS et le moteur DB, client gère les données
  S3 (PaaS) : AWS gère l'infrastructure, client gère les permissions !
```

> ⚠️ **Rappel CEH** : Les **misconfigurations** (S3 public, IAM trop permissif) sont des erreurs du **CLIENT**, pas du fournisseur. C'est la menace cloud #1 selon la CSA.

---

## 4. Services Cloud Importants (AWS)

```
Compute :
  EC2         : Machines virtuelles (IaaS)
  Lambda      : Fonctions serverless (PaaS)
  ECS/EKS     : Conteneurs (Docker/Kubernetes managé)

Stockage :
  S3          : Object storage (fichiers, buckets)
  EBS         : Block storage (disques pour EC2)
  Glacier     : Archivage longue durée

Base de données :
  RDS         : SQL managé (MySQL, PostgreSQL, MSSQL)
  DynamoDB    : NoSQL managé
  ElastiCache : Cache Redis/Memcached

Réseau :
  VPC         : Virtual Private Cloud (réseau isolé logiquement)
  Security Groups : Firewall stateful par instance
  NACLs       : ACLs stateless au niveau subnet

Sécurité :
  IAM         : Identity and Access Management (utilisateurs, rôles, permissions)
  KMS         : Key Management Service (gestion des clés de chiffrement)
  CloudTrail  : Audit de toutes les actions AWS (qui a fait quoi, quand)
  GuardDuty   : Détection automatique des menaces (ML-based)
  Config      : Compliance et drift detection des configurations
  WAF         : Web Application Firewall pour les apps
```

---

## 5. Virtualisation et Conteneurs

```
Machines virtuelles (VM) :
  Hyperviseur → VM1 (OS complet) | VM2 (OS complet) | VM3 (OS complet)
  → Isolation forte (kernel séparé par VM)
  → OS complet → démarrage lent (minutes)
  → Lourdes (plusieurs GB par VM)

Conteneurs (Docker) :
  Docker Engine → Container1 (App+Libs) | Container2 | Container3
  → Partagent le kernel du host → légers, rapides (secondes)
  → Isolation moins forte que VM (kernel partagé → escape possible)
  → Images légères (quelques centaines de MB)

Kubernetes (K8s) :
  → Orchestrateur de conteneurs (scheduling, scaling, self-healing)
  → Cluster = plusieurs nœuds (masters + workers)
  → Pods = unité de base (1+ conteneurs partageant un réseau)
  → Namespaces = isolation logique des workloads

Serverless (Lambda) :
  → Pas de serveur à gérer
  → Facturation à l'exécution (millisecondes)
  → Surface d'attaque différente : permissions IAM du rôle Lambda, dépendances
  → Event-driven (déclenchement par S3, API Gateway, SQS...)
```

---

## 🧠 Points clés à retenir pour l'examen

- **IaaS** = tu gères l'OS (EC2) ; **PaaS** = tu gères le code ; **SaaS** = tu utilises l'app
- **Shared Responsibility** = AWS gère l'infra physique, le CLIENT gère la configuration
- Les misconfigurations S3 et IAM = responsabilité du **CLIENT** (menace cloud #1)
- **CloudTrail** = logs de toutes les actions AWS (audit et forensics)
- **IAM** = gestion des identités et des droits cloud (utilisateurs, rôles, policies)
- **VPC** = réseau virtuel isolé dans AWS
- **GuardDuty** = détection automatique des menaces cloud (ML)
- **Security Groups** = stateful (retour automatique) ; **NACLs** = stateless (retour manuel)

---

*⬅️ [README](./README.md) | ➡️ [Menaces Cloud](./02_menaces_cloud.md)*
