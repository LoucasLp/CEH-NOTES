# 📝 Module 15 — Quiz & Révision : SQL Injection

> **CEH v13 | EC-Council**  
> `#Quiz` `#SQLi` `#sqlmap` `#PreparedStatements` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- SQLi = injecter du SQL via des entrées non validées pour manipuler les requêtes
- **In-band** (error-based, union-based) vs **Blind** (boolean, time-based) vs **OOB**
- **sqlmap** = outil d'exploitation automatisée de SQLi
- **Prepared statements** = contre-mesure principale (#1)
- `information_schema` = base de métadonnées pour extraire la structure DB
- `xp_cmdshell` = RCE sur MSSQL via SQLi

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **Error-based** | Extraction de données via messages d'erreur SQL |
| **Union-based** | Extraction via UNION SELECT sur colonnes visibles |
| **Boolean blind** | Déduction caractère par caractère via true/false |
| **Time-based blind** | Déduction par délai SLEEP/WAITFOR |
| **OOB** | Exfiltration via canal DNS ou HTTP externe |
| **Second-order** | Payload stocké puis exécuté dans un second contexte |
| **xp_cmdshell** | Procédure MSSQL permettant l'exécution OS |
| **LOAD_FILE** | Fonction MySQL pour lire des fichiers (nécessite FILE privilege) |
| **INTO OUTFILE** | Écriture de fichier depuis MySQL (nécessite FILE privilege) |
| **Prepared statement** | Requête avec paramètres séparés → anti-SQLi |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **Prepared statements** = LA contre-mesure principale contre SQLi
2. **sqlmap** = outil CEH officiel pour automatiser les tests SQLi
3. **`--dbs`** = lister les bases ; **`--tables`** = lister les tables
4. `'` (apostrophe) = caractère test #1 pour détecter SQLi
5. **Boolean blind** = comportement différent selon `1=1` vs `1=2`
6. **Time-based blind** = `SLEEP(5)` MySQL, `WAITFOR DELAY` MSSQL
7. **UNION SELECT** nécessite le même nombre de colonnes
8. **xp_cmdshell** = MSSQL uniquement, exécution de commandes OS
9. **Masquer les erreurs SQL** est important mais pas suffisant
10. `information_schema` = base de métadonnées standard (MySQL, PostgreSQL)

---

## ❓ Questions de révision

1. Quelle est la différence entre SQLi error-based et SQLi blind ?
2. Comment déterminer le nombre de colonnes pour une injection UNION SELECT ?
3. Expliquez le fonctionnement d'une SQLi time-based avec un exemple.
4. Pourquoi les prepared statements sont-ils plus efficaces que la validation des entrées contre SQLi ?
5. Quelles conditions sont nécessaires pour utiliser `INTO OUTFILE` en MySQL ?
6. Qu'est-ce que la second-order injection et pourquoi est-elle difficile à détecter ?
7. Comment sqlmap gère-t-il le bypass de WAF ?
8. Quel est le rôle de `information_schema` dans l'exploitation SQLi ?

---

## 📋 Quiz QCM

**Question 1.** Quelle est la contre-mesure la plus efficace contre l'injection SQL ?
- a) Validation des entrées côté client
- b) Utilisation d'un WAF
- c) Requêtes paramétrées (Prepared Statements)
- d) Masquer les messages d'erreur SQL

**Question 2.** `' OR 1=1 --` est typiquement utilisé pour :
- a) Un scan de port
- b) Un bypass d'authentification via SQLi
- c) Une attaque XSS
- d) Un test de path traversal

**Question 3.** Quelle technique SQLi utilise les délais de réponse pour extraire les données ?
- a) Union-based
- b) Error-based
- c) Boolean blind
- d) Time-based blind

**Question 4.** La commande sqlmap `--dbs` permet de :
- a) Télécharger toutes les bases de données
- b) Lister les bases de données disponibles
- c) Détecter le type de SGBD
- d) Créer une nouvelle base de données

**Question 5.** `xp_cmdshell` est une procédure présente dans :
- a) MySQL
- b) Oracle
- c) Microsoft SQL Server
- d) PostgreSQL

**Question 6.** Pour utiliser `INTO OUTFILE` en MySQL, l'utilisateur DB doit avoir :
- a) Le privilège SUPER
- b) Le privilège FILE
- c) Le privilège ALL
- d) Le privilège INSERT

**Question 7.** Le tamper script sqlmap `space2comment` remplace :
- a) Les guillemets par des commentaires
- b) Les espaces par des commentaires SQL `/**/`
- c) Les unions par des sous-requêtes
- d) Les caractères spéciaux par leur URL encoding

**Question 8.** Une injection de second ordre se produit quand :
- a) L'injection est exécutée deux fois de suite
- b) Le payload est stocké puis exécuté dans un autre contexte
- c) L'attaquant utilise deux types d'injection simultanément
- d) Le WAF est contourné deux fois

---

## ✅ Points de révision — Checklist

- [ ] Je connais les 5 types de SQLi (error, union, boolean blind, time blind, OOB)
- [ ] Je peux expliquer comment fonctionne UNION SELECT step by step
- [ ] Je comprends la différence boolean blind vs time-based blind
- [ ] Je sais utiliser sqlmap (--dbs, --tables, --dump, -r, --tamper)
- [ ] Je comprends pourquoi les prepared statements empêchent SQLi
- [ ] Je connais le rôle de information_schema dans l'exploitation
- [ ] Je sais ce qu'est second-order injection
- [ ] Je connais xp_cmdshell et ses conditions d'exploitation

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **c** | Prepared statements séparent code et données |
| 2 | **b** | 1=1 toujours vrai → retourne tous les enregistrements |
| 3 | **d** | SLEEP(5)/WAITFOR = délai = time-based blind |
| 4 | **b** | `--dbs` = lister les databases disponibles |
| 5 | **c** | xp_cmdshell = procédure MSSQL uniquement |
| 6 | **b** | FILE privilege requis pour LOAD_FILE et INTO OUTFILE |
| 7 | **b** | space2comment remplace espaces par `/**/` |
| 8 | **b** | Second-order = stocké proprement, exécuté plus tard |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 15](./README.md)*
