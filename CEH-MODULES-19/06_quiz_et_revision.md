# 📝 Module 19 — Quiz & Révision : Cloud Computing

> **CEH v13 | EC-Council**  
> `#Quiz` `#Cloud` `#AWS` `#SharedResponsibility` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- IaaS (EC2) > PaaS (Heroku) > SaaS (Gmail) — responsabilité croissante pour le fournisseur
- Shared Responsibility : AWS gère l'infra, le CLIENT gère la configuration !
- Misconfiguration = menace cloud #1 (S3 public, IAM trop large)
- SSRF → 169.254.169.254 → credentials IAM (IMDSv2 = protection)
- CloudTrail = activer partout pour l'audit
- ScoutSuite/Prowler = audit de configuration cloud

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **IaaS** | Infrastructure as a Service — VMs louées |
| **Shared Responsibility** | AWS gère l'infra, client gère la config/données |
| **IMDSv2** | Protection SSRF via token avant accès metadata |
| **CloudTrail** | Journal des actions AWS (qui a fait quoi, quand) |
| **GuardDuty** | Service AWS de détection des menaces |
| **ScoutSuite** | Outil d'audit de configuration multi-cloud |
| **Pacu** | Framework d'exploitation AWS |
| **TruffleHog** | Scanner de secrets dans les repos Git |
| **CSPM** | Cloud Security Posture Management |
| **IAM** | Identity and Access Management |

---

## 🎯 Exam Tips

1. **Shared Responsibility** = la misconfiguration S3/IAM est la faute du CLIENT
2. **SSRF → 169.254.169.254** = accès metadata AWS → credentials IAM
3. **IMDSv2** = protection SSRF (token PUT requis)
4. **CloudTrail** = logs AWS, activer dans toutes les régions
5. **S3 Block Public Access** = désactiver l'accès public au niveau compte
6. **ScoutSuite** = audit multi-cloud (AWS, Azure, GCP)
7. **Prowler** = audit des best practices AWS
8. **Pacu** = framework exploitation AWS (comme Metasploit pour AWS)
9. **Root account MFA** = obligatoire sur AWS
10. **Credentials dans GitHub** = fuite très fréquente → TruffleHog pour détecter

---

## ❓ Questions de révision

1. Expliquez le Shared Responsibility Model dans le contexte d'AWS EC2.
2. Comment exploiter un SSRF pour obtenir des credentials IAM AWS ?
3. Qu'est-ce qu'IMDSv2 et comment protège-t-il contre le SSRF ?
4. Pourquoi CloudTrail est-il essentiel pour la sécurité cloud ?
5. Quelle est la différence entre ScoutSuite et Prowler ?
6. Comment un attaquant utilise-t-il Pacu pour l'escalade de privilèges IAM ?
7. Expliquez la menace de cryptojacking dans le contexte cloud.
8. Quelles sont les 3 meilleures pratiques IAM pour sécuriser un compte AWS ?

---

## 📋 Quiz QCM

**Question 1.** Dans le Shared Responsibility Model AWS, qui est responsable de la configuration des Security Groups ?
- a) AWS
- b) Le client
- c) Les deux partagent la responsabilité
- d) Les auditeurs tiers

**Question 2.** Un bucket S3 public exposant des données d'entreprise est une faute de :
- a) AWS pour avoir rendu S3 accessible
- b) Le client pour avoir mal configuré les permissions
- c) L'ISP de l'entreprise
- d) Amazon Web Services Security Team

**Question 3.** L'adresse 169.254.169.254 dans un SSRF AWS permet d'accéder à :
- a) L'interface de gestion AWS Console
- b) Le service de métadonnées d'instance (dont les credentials IAM)
- c) Le service DNS interne AWS
- d) La configuration du VPC

**Question 4.** IMDSv2 protège contre le SSRF car il :
- a) Désactive complètement les métadonnées d'instance
- b) Nécessite un token PUT préalable, impossible via SSRF simple
- c) Chiffre les métadonnées avec AES-256
- d) Bloque toute requête depuis les EC2

**Question 5.** Quel outil d'audit de configuration est multi-cloud (AWS, Azure, GCP) ?
- a) Prowler
- b) Pacu
- c) ScoutSuite
- d) TruffleHog

**Question 6.** CloudTrail est utilisé pour :
- a) Détecter automatiquement les menaces
- b) Logger toutes les actions API dans AWS
- c) Scanner les buckets S3 publics
- d) Gérer les identités IAM

**Question 7.** Le cryptojacking cloud consiste à :
- a) Voler des données chiffrées
- b) Utiliser les ressources cloud de la victime pour miner des cryptomonnaies
- c) Chiffrer les buckets S3 de la victime
- d) Voler les clés KMS

**Question 8.** Quel outil est utilisé pour scanner les secrets dans les dépôts Git ?
- a) Prowler
- b) ScoutSuite
- c) TruffleHog
- d) Pacu

---

## ✅ Points de révision — Checklist

- [ ] Je connais les différences IaaS / PaaS / SaaS
- [ ] Je comprends le Shared Responsibility Model
- [ ] Je sais expliquer l'attaque SSRF → metadata credentials
- [ ] Je comprends IMDSv2 comme protection contre SSRF
- [ ] Je connais les outils d'audit cloud (ScoutSuite, Prowler)
- [ ] Je comprends l'importance de CloudTrail
- [ ] Je sais comment S3 peut être mal configuré
- [ ] Je connais les bonnes pratiques IAM (MFA, moindre privilège, rotation)

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | Security Groups = configuration client |
| 2 | **b** | Misconfiguration S3 = responsabilité client |
| 3 | **b** | 169.254.169.254 = metadata service avec credentials |
| 4 | **b** | IMDSv2 nécessite un PUT préalable impossible en SSRF |
| 5 | **c** | ScoutSuite = multi-cloud ; Prowler = AWS seulement |
| 6 | **b** | CloudTrail = audit logs des actions API |
| 7 | **b** | Cryptojacking = miner avec les ressources de la victime |
| 8 | **c** | TruffleHog = secrets dans Git |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 19](./README.md)*
