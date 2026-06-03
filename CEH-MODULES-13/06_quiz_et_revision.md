# 📝 Module 13 — Quiz & Révision : Hacking Web Servers

> **CEH v13 | EC-Council**  
> `#Quiz` `#WebServer` `#Nikto` `#DirectoryTraversal` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- Les serveurs web (Apache, Nginx, IIS) sont vulnérables aux mauvaises configurations et aux patches manquants
- **Directory traversal** = `../` pour accéder à des fichiers hors du webroot
- **Nikto** = scanner de vulnérabilités web polyvalent (CEH tool essentiel)
- **Banner grabbing** = identifier le serveur et sa version via les headers HTTP
- **WebDAV + PUT** = upload de webshell possible
- Contre-mesures clés : patch management, désactiver méthodes inutiles, WAF, headers sécurité

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **Directory Traversal** | Accéder à des fichiers hors du webroot via `../` |
| **Banner Grabbing** | Récupérer les informations serveur depuis les headers HTTP |
| **WebDAV** | Extension HTTP pour gestion de fichiers ; dangereux si PUT activé |
| **LFI** | Local File Inclusion — inclure des fichiers locaux dans PHP |
| **RFI** | Remote File Inclusion — inclure des fichiers distants |
| **SSRF** | Forcer le serveur à faire des requêtes vers l'intérieur du réseau |
| **Nikto** | Scanner de vulnérabilités web (open-source) |
| **Gobuster** | Outil de brute-force de répertoires et fichiers web |
| **DMZ** | Zone démilitarisée réseau pour isoler les serveurs exposés |
| **ServerTokens Prod** | Directive Apache masquant la version dans les headers |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **Nikto** = outil CEH officiel pour scanner les serveurs web
2. **Banner grabbing** via `telnet`, `netcat` ou `curl -I`
3. **Directory traversal** = `../../../etc/passwd`
4. **WebDAV** + PUT = upload de fichiers (potentiellement webshell)
5. **`ServerTokens Prod`** = masque la version Apache dans les headers
6. **Désactiver TRACE** = contre-mesure XST (Cross-Site Tracing)
7. **robots.txt** peut révéler des chemins sensibles
8. **LFI + log poisoning** = élever LFI vers exécution de code

---

## ❓ Questions de révision

1. Quelle est la différence entre un serveur web et une application web en termes de sécurité ?
2. Comment le banner grabbing permet-il de cibler les exploits ?
3. Expliquez l'attaque de directory traversal et deux méthodes de bypass de filtre.
4. Pourquoi WebDAV activé avec la méthode PUT est-il dangereux ?
5. Quelle est la différence entre LFI et RFI, et quelle configuration PHP permet RFI ?
6. Comment fonctionne le log poisoning pour transformer une LFI en RCE ?
7. Quel est l'impact d'avoir `Options Indexes` activé sur Apache ?
8. Pourquoi la directive `ServerTokens Prod` est-elle une bonne pratique ?

---

## 📋 Quiz QCM

**Question 1.** Quel outil est principalement utilisé pour scanner les vulnérabilités d'un serveur web dans le contexte CEH ?
- a) Nmap
- b) Nikto
- c) Metasploit
- d) Wireshark

**Question 2.** La directive Apache `Options -Indexes` permet de :
- a) Désactiver SSL
- b) Empêcher l'affichage du contenu des répertoires
- c) Bloquer les méthodes HTTP dangereuses
- d) Masquer la version du serveur

**Question 3.** Un attaquant utilise l'URL `http://site.com/page?file=../../../../etc/passwd`. Quelle attaque est-ce ?
- a) SQL Injection
- b) XSS
- c) Directory/Path Traversal
- d) SSRF

**Question 4.** WebDAV est dangereux principalement parce qu'il permet :
- a) Le déni de service du serveur
- b) L'upload de fichiers arbitraires via la méthode PUT
- c) L'interception des cookies
- d) La modification des headers HTTP

**Question 5.** `ServerTokens Prod` dans la configuration Apache :
- a) Active les tokens d'authentification
- b) Masque la version détaillée du serveur dans les headers
- c) Génère des tokens CSRF
- d) Active le module de production

**Question 6.** La méthode HTTP TRACE est dangereuse car elle :
- a) Permet l'upload de fichiers
- b) Peut être exploitée pour le Cross-Site Tracing (XST) et le vol de cookies
- c) Crée une connexion persistante
- d) Expose le code source PHP

**Question 7.** Pour exploiter un RFI, la configuration PHP suivante doit être activée :
- a) `register_globals = On`
- b) `allow_url_include = On`
- c) `safe_mode = Off`
- d) `display_errors = On`

**Question 8.** `gobuster dir` est utilisé pour :
- a) Scanner les ports d'un serveur
- b) Capturer des paquets réseau
- c) Brute-forcer les répertoires et fichiers d'un serveur web
- d) Tester les injections SQL

---

## ✅ Points de révision — Checklist

- [ ] Je peux expliquer le directory traversal et ses variantes d'encodage
- [ ] Je sais utiliser Nikto pour scanner un serveur web
- [ ] Je connais les méthodes HTTP dangereuses (TRACE, PUT, DELETE)
- [ ] Je comprends la différence LFI vs RFI
- [ ] Je sais ce qu'est le log poisoning et comment il élève LFI en RCE
- [ ] Je connais les directives Apache de sécurisation (ServerTokens, Options, LimitExcept)
- [ ] Je comprends ce qu'est le SSRF et ses cibles typiques (metadata cloud)
- [ ] Je sais utiliser gobuster pour énumérer les répertoires

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | Nikto est l'outil CEH de référence pour les serveurs web |
| 2 | **b** | `-Indexes` désactive le listing de répertoires |
| 3 | **c** | `../../../../etc/passwd` = traversal de répertoire |
| 4 | **b** | PUT WebDAV permet l'upload de fichiers (webshells) |
| 5 | **b** | `Prod` = affiche seulement "Apache" sans version |
| 6 | **b** | TRACE renvoie la requête complète → XST possible |
| 7 | **b** | `allow_url_include = On` permet les includes distants |
| 8 | **c** | gobuster brute-force les chemins web |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 13](./README.md)*
