# 09 — Énumération Assistée par l'IA (CEH v13)

> **CEH v13 · Module 04 — Nouveauté IA**  
> [← Retour au README](./README.md)

---

## 🤖 L'IA dans CEH v13

CEH v13 intègre l'intelligence artificielle dans les techniques d'énumération.  
L'outil principal utilisé est **ShellGPT** (`sgpt`), un LLM en ligne de commande.

```
ShellGPT = ChatGPT/LLM intégré dans le terminal
           → Génère des commandes automatiquement
           → Explique les résultats
           → Suggère les prochaines étapes
```

---

## 🔧 ShellGPT — Installation & Configuration

```bash
# Installation
pip install shell-gpt

# Configuration (nécessite une clé API OpenAI)
export OPENAI_API_KEY="sk-..."

# Test rapide
sgpt "List nmap commands for NetBIOS enumeration"
```

---

## 🎯 Énumération NetBIOS avec l'IA

### Demander les commandes appropriées
```bash
# ShellGPT génère automatiquement les commandes
sgpt "Generate nbtstat commands to enumerate NetBIOS on target 192.168.1.100"

# Réponse de l'IA :
# nbtstat -A 192.168.1.100   → Table distante
# nbtstat -n                 → Table locale
# nbtscan 192.168.1.0/24     → Scan du réseau

# Exécuter directement avec --shell
sgpt --shell "Show NetBIOS information for 192.168.1.100"
```

### Interpréter les résultats
```bash
# Passer les résultats à l'IA pour analyse
nbtstat -A 192.168.1.100 | sgpt "Analyze this NetBIOS output and identify security risks"
```

---

## 🌐 Énumération SNMP avec l'IA

```bash
# Générer les commandes SNMP
sgpt "Create snmpwalk commands to enumerate all information from 192.168.1.100"

# Analyser la sortie SNMP
snmpwalk -v2c -c public 192.168.1.100 | sgpt \
  "Analyze this SNMP walk output and identify sensitive information"

# Générer une liste de community strings
sgpt "Generate a list of common SNMP community strings for brute force testing"
```

---

## 📧 Énumération SMTP avec l'IA

```bash
# Commandes d'énumération SMTP
sgpt "Generate smtp-user-enum commands to enumerate users on mail.target.com port 25"

# Script d'énumération automatisé
sgpt --code "Write a Python script to enumerate SMTP users using VRFY command" \
  > smtp_enum.py

# Analyser les résultats
cat smtp_results.txt | sgpt "Analyze these SMTP enumeration results and list valid accounts"
```

---

## 🔍 Énumération DNS avec l'IA

```bash
# Zone Transfer automatisé
sgpt "Generate dig and dnsrecon commands for DNS enumeration of target.com"

# Analyser les zones DNS
dig axfr @ns1.target.com target.com | sgpt \
  "Analyze this DNS zone transfer and highlight sensitive records"

# Sous-domaines
sgpt "Generate subfinder and fierce commands for subdomain enumeration of target.com"
```

---

## 💡 Cas d'Usage Avancés

### Créer un Script d'Énumération Complet
```bash
sgpt --code "Write a bash script that performs complete network enumeration:
1. NetBIOS scan with nbtstat
2. SNMP enumeration with snmpwalk
3. LDAP enumeration with ldapsearch
4. SMB enumeration with enum4linux
Input: target IP as argument" > full_enum.sh

chmod +x full_enum.sh
./full_enum.sh 192.168.1.100
```

### Analyse de Vulnérabilités
```bash
# Analyser les résultats et prioriser les vulnérabilités
cat enum_results.txt | sgpt \
  "As a security analyst, analyze these enumeration results,
   identify the top 5 vulnerabilities and suggest attack vectors"
```

### Rapport Automatisé
```bash
# Générer un rapport de findings
sgpt "Generate a professional security report based on these enumeration findings:
$(cat all_results.txt)" > security_report.md
```

---

## 📋 Commandes ShellGPT Utiles

| Commande | Description |
|----------|-------------|
| `sgpt "question"` | Réponse texte |
| `sgpt --shell "action"` | Génère + exécute commande shell |
| `sgpt --code "task"` | Génère du code |
| `sgpt --repl` | Mode conversation interactive |
| `cmd \| sgpt "analyze"` | Analyser la sortie d'une commande |

---

## ⚠️ Limites et Précautions

| Aspect | Considération |
|--------|---------------|
| **Précision** | L'IA peut générer des commandes incorrectes — toujours valider |
| **Confidentialité** | Ne jamais envoyer de données sensibles réelles à l'API |
| **Abonnement** | ShellGPT nécessite un abonnement OpenAI (payant) |
| **Légalité** | Même avec l'IA, autorisation préalable obligatoire |
| **Audit** | Les requêtes IA peuvent être loguées par le fournisseur |

---

## 🔄 Workflow IA Recommandé (CEH v13)

```
┌─────────────────────────────────────────────────────┐
│                                                     │
│  1. DÉCOUVERTE                                      │
│     sgpt "scan and enumerate target <IP>"           │
│              ↓                                      │
│  2. EXÉCUTION                                       │
│     Lancer les commandes générées par l'IA          │
│              ↓                                      │
│  3. ANALYSE                                         │
│     output | sgpt "analyze and identify risks"      │
│              ↓                                      │
│  4. REPORTING                                       │
│     sgpt "generate report from findings"            │
│              ↓                                      │
│  5. RECOMMANDATIONS                                 │
│     sgpt "suggest countermeasures for these issues" │
│                                                     │
└─────────────────────────────────────────────────────┘
```

---

## 📚 Ressources CEH v13 IA

- **EC-Council iLabs** : Labs pratiques avec ShellGPT intégré
- **Documentation ShellGPT** : `sgpt --help`
- **Alternative libre** : `ollama` avec modèles locaux (pas de clé API)

```bash
# Ollama - Alternative gratuite et locale
curl https://ollama.ai/install.sh | sh
ollama run llama2
# Puis utiliser comme ShellGPT mais en local
```

---

[← Contre-mesures](./08-contre-mesures.md) | [🏠 Retour README](./README.md)
