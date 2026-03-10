# 📌 01 — Introduction & Phases du Hacking

> **Module 2 — Footprinting & Reconnaissance** | CEH v13

---

## 🔄 Les 5 Phases du Hacking (Framework CEH)

| Phase | Nom | Description |
|-------|-----|-------------|
| 1️⃣ | **Reconnaissance** | Collecte d'informations sur la cible (passive & active) |
| 2️⃣ | **Vulnerability Scanning** | Analyse des failles : systèmes, réseaux, services |
| 3️⃣ | **Gaining Access** | Exploitation des vulnérabilités, élévation de privilèges |
| 4️⃣ | **Maintaining Access** | Persistance, backdoors, contrôle à distance |
| 5️⃣ | **Clearing Tracks** | Effacement des logs, suppression des preuves |

---

## 🤖 Outils IA utilisés en Hacking

L'intelligence artificielle transforme le paysage de la cybersécurité offensive et défensive. Voici les principaux outils GPT-based référencés dans le CEH :

### 🔴 Outils offensifs / ambivalents

| Outil | Usage |
|-------|-------|
| **WormGPT** | Génération automatisée de scripts malveillants (worms, payloads) |
| **FraudGPT** | Détection ET génération de contenus frauduleux |
| **FreedomGPT** | Contourne les filtres de contenu, accès non restreint |
| **ChaosGPT** | Simulation de comportements chaotiques/imprévisibles |
| **PoisonGPT** | Injection de modèles malveillants dans des systèmes IA de confiance |
| **ChatGPT + DAN** | Version modifiée via prompt "Do Anything Now" — contournement des restrictions |

### 🟢 Outils défensifs / pentest éthique

| Outil | Source | Usage |
|-------|--------|-------|
| **HackerGPT** | chat.hackerai.co | Identification de vulnérabilités en temps réel |
| **BurpGPT** | burpgpt.app | Intégration Burp Suite + analyse IA |
| **BugBountyGPT** | chatgpt.com | Bug bounty hunting automatisé |
| **PentestGPT** | github.com/GreyDGL | Automatisation des tests de pénétration |
| **GPT White Hack** | chatgpt.com | Ethical hacking, assessment des risques |
| **CybGPT** | github.com/Coinnect-SA | Threat intelligence + incident response |
| **h4ckGPT** | chatgpt.com | Assistant polyvalent pour ethical hackers |
| **Ethical Hacker GPT** | chatgpt.com | Évaluations de vulnérabilités IA |
| **GP(en)T(ester)** | chatgpt.com | Red teaming automatisé |
| **PentestGPT** | chatgpt.com | Rapports détaillés de pentest |

---

## 🧠 Behavioral Identification (Adversary Behavior)

> Comprendre le comportement d'un adversaire permet d'améliorer les capacités de détection.

| Comportement | Description | Détection |
|---|---|---|
| **Internal Reconnaissance** | Enumération des systèmes, processus, IPs, configs locales | Surveiller les scripts Batch/PowerShell inhabituels |
| **Use of PowerShell** | Exfiltration de données, lancement d'attaques | Transcript logs PowerShell, Windows Event Logs |
| **Unspecified Proxy Activities** | Multiples domaines → même hôte pour éviter la détection | Analyser les data feeds des domaines |
| **Use of CLI** | Parcours de fichiers, création de comptes, téléchargement de malware | Logs process ID, noms de fichiers suspects |
| **HTTP User Agent** | Modification du champ User Agent pour communiquer avec C2 | Inspecter le champ User Agent dans les logs HTTP |
| **Command & Control Server** | Communication chiffrée avec des systèmes compromis | Trafic sortant, ports ouverts inhabituels |
| **DNS Tunneling** | Trafic malveillant camouflé dans le trafic DNS légitime | Analyser requêtes DNS, payload, domaines suspects |
| **Web Shell** | Shell dans un site web pour accès distant au serveur | Logs serveur, strings d'encodage suspects |
| **Data Staging** | Collecte maximale de données post-intrusion | Monitoring trafic réseau, file integrity monitoring |

---

## 📐 MITRE ATT&CK

```
┌──────────┬────────────┬─────────┬─────────┬─────────┬─────────┬────────────┐
│  Recon   │ Weaponize  │ Deliver │ Exploit │ Control │ Execute │  Maintain  │
├──────────┴────────────┤         └─────────┴─────────┴─────────┴────────────┘
│     PRE-ATT&CK        │                  Enterprise ATT&CK
└───────────────────────┘
```

- **PRE-ATT&CK** : Phases de reconnaissance et armement
- **Enterprise ATT&CK** : 14 catégories de tactiques (Exploit → Maintain)
- **Mobile ATT&CK** : Spécifique aux appareils mobiles

---

## 📋 Standards ISO/IEC référencés

| Standard | Sujet |
|----------|-------|
| **ISO/IEC 27001:2022** | ISMS — Système de Management de la Sécurité de l'Information |
| **ISO/IEC 27002:2022** | Contrôles de sécurité (accès, cryptographie, personnel) |
| **ISO/IEC 27005:2022** | Gestion des risques de sécurité de l'information |
| **ISO/IEC 27701:2019** | Extension ISMS → gestion de la vie privée (PII/PIMS) |
| **ISO/IEC 27018:2019** | PII dans les environnements cloud publics |
| **ISO/IEC 27032:2023** | Cybersécurité Internet — parties prenantes et rôles |
| **ISO/IEC 27033-7:2023** | Sécurité de la virtualisation réseau |
| **ISO/IEC 27036-3:2023** | Sécurité de la chaîne d'approvisionnement hardware/software |
| **ISO/IEC 27040:2024** | Sécurité du stockage de données |

---

> ⬅️ [README](./README.md) | ➡️ [02 - Concepts du Footprinting](./02_concepts_footprinting.md)
