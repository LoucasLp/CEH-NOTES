# 🛡️ Module 15 — Contre-mesures : SQL Injection

> **CEH v13 | EC-Council**  
> `#PreparedStatements` `#ORM` `#WAF` `#MoindrePrivilège` `#InputValidation`

---

## 📌 Table des matières

1. [Requêtes paramétrées (Prepared Statements)](#1-prepared-statements)
2. [Stored Procedures sécurisées](#2-stored-procedures)
3. [Validation et whitelist des entrées](#3-validation-entrées)
4. [Principe du moindre privilège DB](#4-moindre-privilège)
5. [WAF — Règles anti-SQLi](#5-waf)
6. [Gestion des erreurs](#6-gestion-des-erreurs)
7. [Matrice des contre-mesures](#7-matrice)

---

## 1. Requêtes Paramétrées (Prepared Statements)

**La contre-mesure #1 contre SQLi.**

```php
// PHP — PDO avec prepared statement :
// ✗ DANGEREUX (vulnérable) :
$query = "SELECT * FROM users WHERE username = '$username'";
$result = $pdo->query($query);

// ✓ SÉCURISÉ (prepared statement) :
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$username]);
$result = $stmt->fetchAll();
// L'entrée utilisateur ne peut jamais modifier la structure SQL

// ✓ Avec paramètres nommés :
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = :user AND password = :pass");
$stmt->execute([':user' => $username, ':pass' => $hash]);
```

```python
# Python — avec psycopg2 (PostgreSQL) :
# ✗ DANGEREUX :
cursor.execute(f"SELECT * FROM users WHERE name = '{username}'")

# ✓ SÉCURISÉ :
cursor.execute("SELECT * FROM users WHERE name = %s", (username,))
```

```java
// Java — PreparedStatement :
// ✗ DANGEREUX :
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT * FROM users WHERE user='" + username + "'");

// ✓ SÉCURISÉ :
PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM users WHERE user = ?");
pstmt.setString(1, username);
ResultSet rs = pstmt.executeQuery();
```

> ⚠️ **Rappel CEH** : Les **prepared statements** (requêtes paramétrées) sont **la contre-mesure principale** contre SQLi. L'examen demande fréquemment quelle est la meilleure protection → c'est ça.

---

## 2. Stored Procedures Sécurisées

```sql
-- Stored procedure sécurisée (MySQL) :
DELIMITER //
CREATE PROCEDURE GetUser(IN p_username VARCHAR(50))
BEGIN
    -- L'input est traité comme une valeur, pas du SQL
    SELECT user_id, username, email 
    FROM users 
    WHERE username = p_username;  -- Paramétré dans la procédure
END //
DELIMITER ;

-- Appel sécurisé :
CALL GetUser('alice');  -- 'alice' est une valeur, pas du SQL
CALL GetUser("' OR '1'='1");  -- Inoffensif si la procédure est paramétrique
```

---

## 3. Validation et Whitelist des Entrées

```python
import re

# Whitelist pour un ID numérique :
def validate_id(user_id):
    if not re.match(r'^\d+$', str(user_id)):
        raise ValueError("ID invalide")
    return int(user_id)

# Whitelist pour un nom d'utilisateur :
def validate_username(username):
    if not re.match(r'^[a-zA-Z0-9_]{3,30}$', username):
        raise ValueError("Username invalide : seulement lettres, chiffres, underscore")
    return username

# Validation de longueur :
def validate_input(data, max_length=100):
    if len(data) > max_length:
        raise ValueError("Entrée trop longue")
    return data
```

---

## 4. Principe du Moindre Privilège DB

```sql
-- Créer un utilisateur dédié à l'application avec droits minimaux :

-- Mauvais : utiliser root ou un user avec tous les droits
-- Bon : créer un user spécifique

CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'StrongPass123!';

-- Accorder seulement les droits nécessaires :
GRANT SELECT, INSERT, UPDATE ON app_database.* TO 'app_user'@'localhost';

-- NE PAS accorder :
-- GRANT ALL PRIVILEGES       ← Trop permissif
-- GRANT FILE                 ← Permet LOAD_FILE et INTO OUTFILE
-- GRANT SUPER                ← Accès privilégié
-- Accès à mysql.user         ← Permet de lire les passwords

-- Vérifier les droits accordés :
SHOW GRANTS FOR 'app_user'@'localhost';
```

---

## 5. WAF — Règles Anti-SQLi

```
ModSecurity — OWASP Core Rule Set (CRS) inclut des règles SQLi :

Règles actives automatiquement :
- Détection des mots-clés SQL : UNION, SELECT, INSERT, DROP...
- Détection des caractères spéciaux : ' " ; --
- Détection des fonctions SQL : SLEEP(), WAITFOR, LOAD_FILE...
- Score d'anomalie : chaque pattern correspond à un score

Configuration ModSecurity :
SecRuleEngine On
Include /usr/share/modsecurity-crs/crs-setup.conf
Include /usr/share/modsecurity-crs/rules/*.conf

Limite du WAF :
→ Peut être bypassé (obfuscation, encodage)
→ Ne remplace pas les prepared statements
→ Doit être utilisé EN COMPLÉMENT
```

---

## 6. Gestion des Erreurs

```php
// ✗ MAUVAIS : Afficher les erreurs SQL à l'utilisateur
mysqli_connect_errno() → "Access denied for user 'root'@'localhost'"
                       ← Révèle l'utilisateur DB, l'hôte !

// ✓ BON : Logger en interne, afficher une erreur générique
try {
    $result = $pdo->query($sql);
} catch (PDOException $e) {
    error_log("DB Error: " . $e->getMessage());  // Log serveur
    echo "Une erreur est survenue. Veuillez réessayer.";  // Message générique
}
```

---

## 7. Matrice des Contre-mesures

| Vecteur | Contre-mesure | Efficacité |
|---------|---------------|------------|
| Injection via paramètre | Prepared statements | ★★★★★ (primaire) |
| Contournement de filtres | Whitelist + validation | ★★★★ |
| RCE via xp_cmdshell | Désactiver xp_cmdshell | ★★★★ |
| LOAD_FILE/OUTFILE | Révoquer privilège FILE | ★★★★ |
| Error-based | Masquer les erreurs SQL | ★★★ |
| Volume / automatisation | WAF + rate limiting | ★★★ |
| Escalade de privilège | Moindre privilège DB | ★★★★ |

---

## 🧠 Points clés à retenir pour l'examen

- **Prepared statements** = LA contre-mesure principale contre SQLi
- **Moindre privilège DB** = pas de FILE privilege pour l'utilisateur app
- **Masquer les erreurs SQL** = ne pas afficher les messages d'erreur à l'utilisateur
- **WAF** = complément utile mais pas suffisant seul
- **ORM** (Hibernate, SQLAlchemy) = utilise les prepared statements automatiquement
- Révoquer **FILE privilege** empêche LOAD_FILE et INTO OUTFILE

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
