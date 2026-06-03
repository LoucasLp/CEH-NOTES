# 🔴 Module 19 — Concepts Fondamentaux : Cloud Computing

> **CEH v13 | EC-Council**  
> `#IaaS` `#PaaS` `#SaaS` `#SharedResponsibility` `#AWS` `#Azure` `#GCP`

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
│  Fournisseur gère : data center, hardware, hypervisor               │
│  Tu gères : OS, middleware, apps, données                           │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 2. Modèles de Déploiement

| Modèle | Description | Exemples |
|--------|-------------|---------|
| **Public** | Infrastructure partagée, multi-tenant | AWS, Azure, GCP |
| **Private** | Infrastructure dédiée (on-premise ou hébergée) | VMware Private Cloud |
| **Hybrid** | Mix public + private | AWS + datacenter interne |
| **Community** | Partagé entre organisations similaires | Clouds gouvernementaux |

---

## 3. Shared Responsibility Model

```
AWS Shared Responsibility Model :

FOURNISSEUR (AWS) gère :
  ├── Datacenters physiques (sécurité physique)
  ├── Hyperviseur et virtualisation
  ├── Réseau AWS (backbone)
  └── Matériel (serveurs, switches)

CLIENT (vous) gère :
  ├── Système d'exploitation et patches
  ├── Chiffrement des données
  ├── Configuration des groupes de sécurité
  ├── Gestion IAM (qui a accès à quoi)
  ├── Données clients
  └── Configuration des services (S3 public/privé)

Modèle change selon le service :
  EC2 (IaaS) : Client gère tout jusqu'à l'OS
  RDS (PaaS) : AWS gère l'OS et le moteur DB, client gère les données
  S3 (PaaS) : AWS gère l'infrastructure, client gère les permissions !
```

> ⚠️ **Rappel CEH** : Les **misconfigurations** (S3 public, IAM trop permissif) sont des erreurs du CLIENT, pas du fournisseur. C'est la menace cloud #1.

---

## 4. Services Cloud Importants (AWS)

```
Compute :
  EC2         : Machines virtuelles
  Lambda      : Fonctions serverless
  ECS/EKS     : Conteneurs (Docker/Kubernetes)

Stockage :
  S3          : Object storage (fichiers)
  EBS         : Block storage (disques EC2)
  Glacier     : Archivage

Base de données :
  RDS         : SQL managé (MySQL, PostgreSQL, MSSQL)
  DynamoDB    : NoSQL managé
  ElastiCache : Cache Redis/Memcached

Réseau :
  VPC         : Virtual Private Cloud (réseau isolé)
  Security Groups : Firewall par instance
  NACLs       : ACLs niveau subnet

Sécurité :
  IAM         : Identity and Access Management
  KMS         : Key Management Service
  CloudTrail  : Audit des actions AWS
  GuardDuty   : Détection des menaces
```

---

## 5. Virtualisation et Conteneurs

```
Machines virtuelles (VM) :
  Hyperviseur → VM1 (OS complet) | VM2 (OS complet) | VM3 (OS complet)
  → Isolation forte, OS complet, lent à démarrer
  
Conteneurs (Docker) :
  Docker Engine → Container1 (App+Libs) | Container2 | Container3
  → Partagent le kernel host, légers, rapides
  → Isolation moins forte que VM
  
Kubernetes (K8s) :
  → Orchestrateur de conteneurs
  → Cluster = plusieurs nœuds
  → Pods = unité de base (1+ conteneurs)
  
Serverless (Lambda) :
  → Pas de serveur à gérer
  → Facturation à l'exécution
  → Surface d'attaque différente (permissions IAM du rôle Lambda)
```

---

## 🧠 Points clés à retenir pour l'examen

- **IaaS** = tu gères l'OS (EC2) ; **PaaS** = tu gères le code ; **SaaS** = tu utilises l'app
- **Shared Responsibility** = AWS gère l'infra, le client gère la configuration
- Les misconfigurations S3 et IAM = responsabilité du CLIENT
- **CloudTrail** = logs de toutes les actions AWS (audit)
- **IAM** = gestion des identités et des droits cloud
- **VPC** = réseau virtuel isolé dans AWS

---

*⬅️ [README](./README.md) | ➡️ [Menaces Cloud](./02_menaces_cloud.md)*
