# 🛡️ Module 11 — Contre-mesures : Session Hijacking

> **CEH v13 | EC-Council**  
> `#HTTPS` `#CookieFlags` `#CSRF` `#MFA` `#SessionManagement`

---

## 📌 Table des matières

1. [HTTPS/TLS obligatoire](#1-httpstls-obligatoire)
2. [Configuration des cookies](#2-configuration-des-cookies)
3. [Gestion sécurisée des sessions](#3-gestion-des-sessions)
4. [Protection CSRF](#4-protection-csrf)
5. [Authentification forte (MFA)](#5-mfa)
6. [Détection et monitoring](#6-détection-et-monitoring)
7. [Matrice des contre-mesures](#7-matrice)

---

## 1. HTTPS/TLS Obligatoire

```
Sans HTTPS :
  Cookie en clair sur le réseau → Sidejacking possible

Avec HTTPS :
  Cookie chiffré dans le tunnel TLS → Sidejacking impossible

Configuration minimale serveur (Apache) :
  SSLProtocol TLSv1.2 TLSv1.3
  SSLCipherSuite HIGH:!aNULL:!MD5
  Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
```

### HSTS (HTTP Strict Transport Security)

```
Header : Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

Effet :
1. Premier accès : serveur envoie l'en-tête HSTS
2. Navigateur mémorise : "ce site DOIT utiliser HTTPS"
3. Tentatives futures HTTP → Navigateur refuse sans contacter le serveur
4. SSL Stripping impossible (navigateur force HTTPS)

Preload : inscription dans la liste HSTS préchargée du navigateur
→ Protection dès la toute première visite (avant même le header)
```

---

## 2. Configuration des Cookies

```
Set-Cookie: sessionid=<random>; 
            Secure;           ← HTTPS uniquement
            HttpOnly;         ← Inaccessible via JavaScript (anti-XSS)
            SameSite=Strict;  ← Pas d'envoi cross-site (anti-CSRF)
            Max-Age=1800;     ← Expiration 30 minutes
            Path=/;           ← Scope de l'application
```

### Comparatif des attributs SameSite

| SameSite | Navigation normale | Lien externe | Formulaire POST externe |
|----------|--------------------|--------------|------------------------|
| `Strict` | ✅ | ❌ | ❌ |
| `Lax` | ✅ | ✅ (GET) | ❌ |
| `None` | ✅ | ✅ | ✅ (+ Secure requis) |

> ⚠️ **Rappel CEH** : La question classique CEH porte sur quel attribut empêche le vol de cookie via JavaScript → **`HttpOnly`**.

---

## 3. Gestion Sécurisée des Sessions

### 3.1 Régénération de l'ID après authentification

```php
// PHP — contre-mesure session fixation
session_start();
// ... vérification du login/password ...
if ($login_ok) {
    session_regenerate_id(true);  // ← OBLIGATOIRE après auth !
    $_SESSION['user'] = $username;
}
```

### 3.2 Génération d'un ID fort

```
❌ Mauvais :
  session_id = str(user_id) + "_" + str(int(time.time()))
  → Prévisible, attaque par énumération

✅ Bon :
  import secrets
  session_id = secrets.token_hex(32)  # 256 bits d'entropie
  → Impossible à prédire ou brute-forcer
```

### 3.3 Expiration et invalidation

```
Bonnes pratiques :
1. Session timeout absolu : 4-8h max
2. Session timeout inactivité : 15-30 min
3. Logout = invalidation côté SERVEUR (supprimer la session)
4. Invalider toutes les sessions lors du changement de mot de passe
5. Invalider les sessions lors d'un changement d'IP (optionnel selon UX)
```

### 3.4 Binding de session

```
Lier la session à des attributs de la requête :
- Adresse IP (attention : mobiles changent d'IP)
- User-Agent
- Token matériel (plus sécurisé)

if session_ip != request_ip:
    invalidate_session()
    redirect_to_login()
```

---

## 4. Protection CSRF

### 4.1 Tokens CSRF

```html
<!-- Chaque formulaire inclut un token unique et secret -->
<form method="POST" action="/transfer">
  <input type="hidden" name="csrf_token" value="{{ csrf_token() }}">
  <input type="text" name="amount">
  <button type="submit">Transférer</button>
</form>

<!-- Côté serveur : vérifier que le token correspond à la session -->
if request.form.get('csrf_token') != session.get('csrf_token'):
    abort(403)
```

### 4.2 SameSite Strict

```
Set-Cookie: sessionid=...; SameSite=Strict

→ Le navigateur N'ENVOIE PAS le cookie si la requête vient d'un site externe
→ Rend les attaques CSRF inefficaces pour les formulaires cross-site
```

### 4.3 Double Submit Cookie Pattern

```
1. Serveur génère token CSRF aléatoire
2. Token envoyé dans un cookie ET dans un champ de formulaire caché
3. Serveur vérifie que cookie == valeur du formulaire
4. Un attaquant cross-site ne peut pas lire le cookie (SOP)
```

---

## 5. Authentification Forte (MFA)

```
MFA = quelque chose que vous SAVEZ + AVEZ + ÊTES

Facteurs :
├── Knowledge : mot de passe, PIN
├── Possession : TOTP (Google Auth), SMS OTP, hardware token (YubiKey)
└── Inherence : empreinte, facial, iris

Impact sur session hijacking :
→ Même avec le cookie volé, l'attaquant ne peut pas RE-authentifier
→ Si la session expire, pas de possibilité de renouvellement sans MFA

Limite : MitB peut intercepter le second facteur en temps réel
```

---

## 6. Détection et Monitoring

```
Indicateurs d'anomalie de session :
├── Changement soudain d'IP géographique
├── Changement de User-Agent en cours de session
├── Accès simultané depuis deux localisations
├── Volume d'accès anormal
└── Accès à des ressources inhabituelles

Actions recommandées :
└── Invalider la session + Alerter l'utilisateur + Logger l'événement

SIEM rules :
rule session_anomaly {
  condition: same_sessionid AND (
    different_ip_geolocation OR
    different_user_agent OR
    concurrent_sessions > 1
  )
  action: terminate_session, notify_user
}
```

---

## 7. Matrice des Contre-mesures

| Attaque | Contre-mesure principale |
|---------|--------------------------|
| Sidejacking | HTTPS + cookie `Secure` |
| XSS Cookie theft | Cookie `HttpOnly` |
| CSRF | Token CSRF + `SameSite=Strict` |
| Session Fixation | `session_regenerate_id()` après auth |
| SSL Stripping | HSTS + preload |
| JWT alg:none | Valider l'algorithme côté serveur |
| Brute force Session ID | Entropie 128+ bits + rate limiting |
| Session Replay | Nonce + expiration courte |

---

## 🧠 Points clés à retenir pour l'examen

- `HttpOnly` → protection contre XSS (JavaScript ne peut pas lire)
- `Secure` → transmission HTTPS uniquement
- `SameSite=Strict` → protection CSRF
- HSTS → protection contre SSL Stripping
- Régénérer l'ID de session après chaque authentification = contre-mesure session fixation
- MFA ne protège pas contre MitB (mais limite les dommages)

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
