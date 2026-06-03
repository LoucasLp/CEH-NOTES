# 🛡️ Module 13 — Contre-mesures : Sécurisation des Serveurs Web

> **CEH v13 | EC-Council**  
> `#PatchManagement` `#WAF` `#HTTPS` `#Hardening` `#Headers`

---

## 📌 Table des matières

1. [Patch Management](#1-patch-management)
2. [Désactivation des fonctionnalités dangereuses](#2-désactivation)
3. [Configuration des headers de sécurité](#3-headers-sécurité)
4. [WAF — Web Application Firewall](#4-waf)
5. [HTTPS et configuration TLS](#5-https--tls)
6. [Segmentation réseau (DMZ)](#6-dmz)
7. [Matrice des contre-mesures](#7-matrice)

---

## 1. Patch Management

```
Stratégie de patch management :

1. INVENTAIRE
   → Lister tous les logiciels serveur (OS, web server, modules)
   → Identifier les versions installées

2. SURVEILLANCE DES CVE
   → S'abonner aux alertes CVE (NVD, vendor advisories)
   → Outils : Nessus, OpenVAS, Qualys

3. TEST
   → Tester les patches en staging avant production
   → Vérifier la non-régression

4. DÉPLOIEMENT
   → Déployer avec fenêtre de maintenance planifiée
   → Sauvegarder avant déploiement

5. VÉRIFICATION
   → Confirmer l'application du patch
   → Re-scanner post-patch

Cadence recommandée :
  Patches critiques (CVSS ≥ 9.0) : 24-48h
  Patches élevés   (CVSS 7-9)    : 7 jours
  Patches moyens   (CVSS 4-7)    : 30 jours
```

---

## 2. Désactivation des Fonctionnalités Dangereuses

### Apache

```apache
# /etc/apache2/apache2.conf ou httpd.conf

# Masquer la version du serveur
ServerTokens Prod
ServerSignature Off

# Désactiver les méthodes dangereuses
<LimitExcept GET POST HEAD>
    Deny from all
</LimitExcept>

# Désactiver le directory listing
Options -Indexes

# Désactiver le suivi des liens symboliques
Options -FollowSymLinks

# Désactiver WebDAV
# LoadModule dav_module modules/mod_dav.so  ← Commenter cette ligne

# Désactiver les modules inutiles
# mod_status, mod_info → Désactiver en production
```

### IIS

```xml
<!-- web.config -->
<system.webServer>
  <!-- Enlever les méthodes HTTP dangereuses -->
  <security>
    <requestFiltering>
      <verbs allowUnlisted="false">
        <add verb="GET" allowed="true"/>
        <add verb="POST" allowed="true"/>
        <add verb="HEAD" allowed="true"/>
      </verbs>
      <!-- Bloquer directory traversal -->
      <requestLimits maxAllowedContentLength="1048576"/>
    </requestFiltering>
  </security>
  <!-- Masquer la version IIS -->
  <httpProtocol>
    <customHeaders>
      <remove name="X-Powered-By"/>
    </customHeaders>
  </httpProtocol>
</system.webServer>
```

---

## 3. Headers de Sécurité

```apache
# Apache — Headers de sécurité (mod_headers requis)
Header always set X-Content-Type-Options "nosniff"
Header always set X-Frame-Options "SAMEORIGIN"
Header always set X-XSS-Protection "1; mode=block"
Header always set Referrer-Policy "strict-origin-when-cross-origin"
Header always set Content-Security-Policy "default-src 'self'; script-src 'self'"
Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
Header always set Permissions-Policy "geolocation=(), microphone=(), camera=()"

# Supprimer les headers révélateurs
Header unset Server
Header unset X-Powered-By
```

### Explication des headers

| Header | Protection contre |
|--------|------------------|
| `X-Content-Type-Options: nosniff` | MIME-type sniffing |
| `X-Frame-Options: SAMEORIGIN` | Clickjacking |
| `Content-Security-Policy` | XSS, injection |
| `HSTS` | SSL Stripping, MITM |
| `Referrer-Policy` | Fuite d'URL dans Referer |
| `Permissions-Policy` | Abus de fonctionnalités navigateur |

---

## 4. WAF — Web Application Firewall

```
Position du WAF :

INTERNET → [WAF] → [Load Balancer] → [Web Servers]
               ↑
         Filtre les requêtes malveillantes
         (SQLi, XSS, traversal, etc.)

Types de WAF :
  Cloud : Cloudflare WAF, AWS WAF, Akamai Kona
  Hardware : F5 BIG-IP ASM, Imperva SecureSphere
  Software : ModSecurity (open-source), NAXSI

ModSecurity (Apache/Nginx) :
  # Activer ModSecurity :
  SecRuleEngine On
  
  # Bloquer path traversal :
  SecRule REQUEST_URI "@rx \.\./" "id:1001,deny,status:403,msg:'Path traversal'"
  
  # Utiliser OWASP Core Rule Set (CRS) :
  Include /etc/modsecurity/crs/*.conf
```

---

## 5. HTTPS et Configuration TLS

```bash
# Générer un certificat SSL avec Let's Encrypt :
certbot --apache -d target.com -d www.target.com

# Configuration TLS sécurisée (Apache) :
SSLProtocol TLSv1.2 TLSv1.3        # Seulement TLS 1.2 et 1.3
SSLCipherSuite ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384
SSLHonorCipherOrder on
SSLCompression off                  # Désactiver CRIME/BREACH
SSLSessionTickets off               # Désactiver les tickets de session
```

---

## 6. Segmentation Réseau (DMZ)

```
Architecture DMZ recommandée :

INTERNET
    │
[FIREWALL EXTERNE]  ← Règles permissives (80, 443)
    │
   DMZ
    ├── Web Server    ← Accessible d'Internet
    ├── Mail Server
    └── DNS Server
    │
[FIREWALL INTERNE]  ← Règles très restrictives
    │
RÉSEAU INTERNE
    ├── DB Server     ← Jamais accessible directement d'Internet
    ├── AD Server
    └── Workstations

Règle : Le serveur web NE DOIT PAS être sur le même segment que la DB.
```

---

## 7. Matrice des Contre-mesures

| Attaque | Contre-mesure principale |
|---------|--------------------------|
| Directory traversal | Validation des entrées, `open_basedir` PHP |
| Directory listing | `Options -Indexes` Apache |
| Banner grabbing | `ServerTokens Prod`, supprimer headers |
| WebDAV upload | Désactiver WebDAV si non nécessaire |
| LFI/RFI | `allow_url_include = Off`, `open_basedir` |
| SSRF | Whitelist des URLs autorisées, IMDSv2 |
| Buffer overflow | Patching régulier, ASLR/DEP |
| Méthodes dangereuses | LimitExcept GET POST HEAD |
| Headers manquants | mod_headers avec headers de sécurité |

---

## 🧠 Points clés à retenir pour l'examen

- **Patch management** = priorité absolue contre les vulnérabilités serveur
- **`ServerTokens Prod`** = masque la version Apache
- **Désactiver WebDAV** si non nécessaire (empêche upload de webshells)
- **WAF** = filtre les requêtes malveillantes (SQLi, XSS, traversal)
- **DMZ** = isoler les serveurs web des systèmes internes critiques
- **Désactiver TRACE** empêche Cross-Site Tracing (XST)

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
