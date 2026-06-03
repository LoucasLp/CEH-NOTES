# 🛡️ Module 14 — Contre-mesures : Sécurisation des Applications Web

> **CEH v13 | EC-Council**  
> `#InputValidation` `#CSP` `#CSRFToken` `#DevSecOps` `#WAF`

---

## 📌 Table des matières

1. [Validation et encodage des entrées](#1-validation-et-encodage)
2. [Content Security Policy (CSP)](#2-csp)
3. [Protection CSRF](#3-csrf)
4. [Authentification et gestion des sessions](#4-auth--sessions)
5. [Contrôle d'accès sécurisé](#5-contrôle-daccès)
6. [Gestion sécurisée des fichiers uploadés](#6-file-upload-sécurisé)
7. [DevSecOps et SDL](#7-devsecops)

---

## 1. Validation et Encodage des Entrées

```
Règle d'or : Valider côté serveur (TOUJOURS), côté client (en complément seulement)

Validation des entrées :
  → Whitelist (accepter seulement ce qui est valide)
  → Vérifier le type, la longueur, le format
  → Rejeter ou échapper les caractères spéciaux

# Python Flask — Validation robuste :
from bleach import clean
import re

def validate_username(username):
    if not re.match(r'^[a-zA-Z0-9_]{3,30}$', username):
        raise ValueError("Username invalide")
    return username

def safe_output(user_input):
    return clean(user_input)  # Échapper HTML
```

### Encodage en sortie contre XSS

```python
# Python/Jinja2 — Auto-escape activé :
{{ user_input }}           ← Encodé automatiquement ✓
{{ user_input | safe }}    ← NON encodé, dangereux !

# PHP — Échapper l'output :
echo htmlspecialchars($input, ENT_QUOTES, 'UTF-8');  ✓

# JavaScript — Utiliser textContent plutôt que innerHTML :
element.textContent = userInput;    ✓
element.innerHTML = userInput;      ✗ (XSS possible)
```

---

## 2. Content Security Policy (CSP)

```
CSP restreint les sources de ressources autorisées dans une page.

Header CSP strict :
Content-Security-Policy:
  default-src 'self';             ← Par défaut : même origine
  script-src 'self' cdn.site.com; ← Scripts : soi-même + CDN
  style-src 'self' 'unsafe-inline';
  img-src 'self' data:;
  font-src 'self';
  connect-src 'self' api.site.com;
  frame-ancestors 'none';         ← Anti-clickjacking
  form-action 'self';
  report-uri /csp-violation-report

Impact sur XSS :
→ Même si XSS injecté, le navigateur REFUSE d'exécuter le script
  s'il ne vient pas d'une source autorisée
→ 'unsafe-inline' DÉSACTIVE cette protection (à éviter absolument)
```

---

## 3. Protection CSRF

### Token CSRF (Synchronizer Token Pattern)

```python
# Django — Protection CSRF intégrée :
# Template :
<form method="POST">
    {% csrf_token %}    ← Token unique par session
    ...
</form>

# Flask-WTF :
from flask_wtf.csrf import CSRFProtect
csrf = CSRFProtect(app)

# Côté serveur (vérification) :
def transfer():
    token = request.form.get('csrf_token')
    if not validate_csrf(token):
        abort(403)
```

### SameSite Cookie + Double Submit

```
SameSite=Strict → Le navigateur n'envoie pas le cookie cross-site
→ CSRF impossible si SameSite=Strict ou Lax sur le cookie de session

Double Submit Cookie :
1. Serveur génère token aléatoire
2. Envoie dans cookie ET dans champ de formulaire
3. Vérifie que cookie == formulaire
→ Un attaquant cross-site ne peut pas lire le cookie (SOP)
→ Donc ne peut pas mettre la bonne valeur dans le formulaire
```

---

## 4. Authentification et Gestion des Sessions

```
Bonnes pratiques :

1. MFA (Multi-Factor Authentication)
   → TOTP (Google Authenticator), SMS OTP, clé hardware

2. Politique de mots de passe solide
   → Min 12 chars, complexité, vérification contre listes connues (HaveIBeenPwned)

3. Limitations des tentatives de login
   → Lockout après N échecs
   → Rate limiting progressif
   → CAPTCHA après X tentatives

4. Stockage des mots de passe
   bcrypt (coût 12+) :
   import bcrypt
   hashed = bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))
   
5. Session management
   → ID long et aléatoire (128+ bits)
   → Régénérer après login
   → Invalider côté serveur au logout
   → Timeout absolu et inactivité
```

---

## 5. Contrôle d'Accès Sécurisé

```
Principe du moindre privilège :
→ Chaque utilisateur n'a accès qu'à ce dont il a besoin

Vérification côté serveur à chaque requête :
# Python — Vérification systématique :
@app.route('/admin/users')
@login_required
@require_role('admin')           ← TOUJOURS vérifier côté serveur
def admin_users():
    return render_template('admin_users.html')

# NE PAS se fier aux contrôles côté client :
if (user.isAdmin) {              ← JavaScript côté client : contournable !
    document.getElementById('adminPanel').style.display = 'block';
}

Pattern Deny-by-Default :
def get_resource(resource_id, current_user):
    resource = db.get(resource_id)
    if resource.owner_id != current_user.id and not current_user.is_admin:
        raise Unauthorized()     ← REFUSER par défaut
    return resource
```

---

## 6. File Upload Sécurisé

```python
ALLOWED_EXTENSIONS = {'png', 'jpg', 'jpeg', 'gif'}
MAX_CONTENT_LENGTH = 5 * 1024 * 1024  # 5MB max

def secure_upload(file):
    # 1. Vérifier l'extension (whitelist)
    ext = file.filename.rsplit('.', 1)[1].lower()
    if ext not in ALLOWED_EXTENSIONS:
        raise ValueError("Extension non autorisée")
    
    # 2. Vérifier le contenu (magic bytes, pas juste l'extension)
    content = file.read(16)
    if not is_valid_image(content):  # Vérifier les bytes du fichier
        raise ValueError("Fichier invalide")
    
    # 3. Renommer le fichier (éviter les traversal via nom)
    import uuid
    filename = str(uuid.uuid4()) + '.' + ext
    
    # 4. Stocker HORS du webroot
    upload_path = '/var/uploads/secure/'  # Pas dans /var/www/html/
    file.save(os.path.join(upload_path, filename))
    
    # 5. Pas d'exécution directe (servir via API, pas URL directe)
    return {'file_id': filename}
```

---

## 7. DevSecOps et SDL

```
Shift Left Security — Intégrer la sécurité tôt :

CI/CD Pipeline sécurisé :
  commit → [SAST: SonarQube] → [SCA: Snyk] → [Build] → [DAST: ZAP] → [Deploy]
                ↑                    ↑                        ↑
           Analyse code          Dépendances vuln.      Scan dynamique

Outils DevSecOps :
  SAST   : SonarQube, Semgrep, Checkmarx
  SCA    : Snyk, OWASP Dependency-Check, npm audit
  DAST   : OWASP ZAP (en CI), Burp Suite Enterprise
  Secrets: GitLeaks, TruffleHog (empêcher commit de secrets)
  IaC    : Checkov (scan Terraform/CloudFormation)
```

---

## 🧠 Points clés à retenir pour l'examen

- **Validation côté serveur** = toujours requise (client-side = facilement bypassée)
- **htmlspecialchars()** (PHP) / `textContent` (JS) = protection XSS output
- **CSP** = politique de sécurité du contenu → empêche XSS même si injecté
- **Token CSRF** + **SameSite=Strict** = protection CSRF
- **bcrypt/Argon2** = stockage sécurisé des mots de passe
- **Deny-by-default** = refuser si non explicitement autorisé (contre IDOR)
- **Fichiers uploadés** : whitelist ext + vérif magic bytes + stockage hors webroot

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
