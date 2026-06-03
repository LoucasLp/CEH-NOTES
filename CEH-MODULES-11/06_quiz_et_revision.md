# 📝 Module 11 — Quiz & Révision : Session Hijacking

> **CEH v13 | EC-Council**  
> `#Quiz` `#Revision` `#ExamTips` `#SessionHijacking`

---

## 🔍 Récapitulatif rapide

- Le session hijacking vise à prendre le contrôle d'une session active **sans connaître le mot de passe**
- Deux grandes familles : TCP-level (réseau) et Application-level (web/cookie)
- Les cookies sans `HttpOnly` sont volables via XSS
- La session fixation impose un ID à la victime ; la régénération d'ID la contrecarrer
- HTTPS + HSTS empêchent le sidejacking et le SSL Stripping

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **Session Fixation** | Forcer la victime à utiliser un ID de session contrôlé par l'attaquant |
| **Sidejacking** | Capture de cookies HTTP non chiffrés sur réseau partagé |
| **CSRF** | Forger une requête du navigateur authentifié de la victime |
| **SSL Stripping** | Dégrader HTTPS → HTTP pour exposer les cookies |
| **XSS + Cookie Theft** | Voler document.cookie via injection JavaScript |
| **Session Replay** | Réutiliser un token capturé pour se réauthentifier |
| **MitB** | Malware navigateur modifiant les transactions en temps réel |
| **HSTS** | Forcer HTTPS via header, protection contre SSL Stripping |
| **HttpOnly** | Attribut cookie interdisant la lecture JavaScript |
| **SameSite=Strict** | Bloque l'envoi du cookie lors de requêtes cross-site |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **Session fixation vs session hijacking** : fixation = imposer l'ID *avant* l'auth ; hijacking = voler l'ID *après* l'auth
2. **HttpOnly** = protection contre XSS (JavaScript)
3. **Secure** = transmission HTTPS seulement
4. **SameSite=Strict** = protection CSRF
5. **Régénérer l'ID** après login = contre-mesure session fixation
6. **Hamster/Ferret** = outils de sidejacking (Wi-Fi non chiffré)
7. **BeEF** = exploitation navigateur via XSS
8. **JWT alg:none** = forgeage de token si le serveur accepte l'absence de signature

---

## ❓ Questions de révision

1. Quelle est la différence fondamentale entre session fixation et session hijacking ?
2. Pourquoi le cookie avec l'attribut `HttpOnly` ne peut-il pas être volé via XSS ?
3. Dans quel contexte réseau le sidejacking est-il possible ?
4. Comment l'attribut `SameSite=Strict` protège-t-il contre CSRF ?
5. Qu'est-ce que le SSL Stripping et quelle contre-mesure est la plus efficace ?
6. Pourquoi faut-il régénérer l'ID de session après l'authentification ?
7. Un JWT avec l'algorithme "none" est-il valide ? Quel risque cela représente-t-il ?
8. Quelle différence entre un Man-in-the-Middle et un Man-in-the-Browser ?

---

## 📋 Quiz QCM

**Question 1.** Un attaquant capture un cookie de session sur un réseau Wi-Fi public. Quelle attaque utilise-t-il ?
- a) Session Fixation
- b) Sidejacking
- c) CSRF
- d) SQL Injection

**Question 2.** Quel attribut de cookie empêche son accès via JavaScript ?
- a) `Secure`
- b) `SameSite`
- c) `HttpOnly`
- d) `Path`

**Question 3.** La session fixation est contre-carrée principalement par :
- a) L'utilisation de HTTPS
- b) La régénération de l'ID de session après authentification
- c) Le cookie SameSite=Strict
- d) Un timeout de session court

**Question 4.** Quel outil est utilisé pour le "sidejacking" de sessions HTTP ?
- a) Metasploit
- b) Nmap
- c) Hamster & Ferret
- d) sqlmap

**Question 5.** HSTS protège principalement contre :
- a) Les injections SQL
- b) Le vol de session par XSS
- c) Le SSL Stripping
- d) Les attaques CSRF

**Question 6.** Quel attribut de cookie oblige sa transmission uniquement via HTTPS ?
- a) `HttpOnly`
- b) `SameSite`
- c) `Secure`
- d) `Encrypted`

**Question 7.** Un JWT avec `"alg": "none"` représente quel risque ?
- a) Le token ne peut pas être déchiffré
- b) Le token peut être forgé sans connaître le secret
- c) Le token expire immédiatement
- d) Le token est illisible par le serveur

**Question 8.** BeEF est principalement utilisé pour :
- a) Scanner des ports réseau
- b) Exploiter des navigateurs via XSS
- c) Cracker des mots de passe Wi-Fi
- d) Analyser des paquets TCP

---

## ✅ Points de révision — Checklist

- [ ] Je peux expliquer la différence entre session fixation et session hijacking
- [ ] Je connais les flags de cookies et leur rôle (Secure, HttpOnly, SameSite)
- [ ] Je comprends le mécanisme du sidejacking et ses prérequis
- [ ] Je sais décrire une attaque CSRF et ses protections
- [ ] Je peux expliquer SSL Stripping et comment HSTS le bloque
- [ ] Je connais les attaques JWT (alg:none, key confusion, weak secret)
- [ ] Je sais quels outils CEH sont associés au session hijacking (Wireshark, Burp, BeEF, Scapy)
- [ ] Je comprends pourquoi la régénération d'ID est essentielle après login

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | Sidejacking = capture de cookie HTTP sur réseau partagé |
| 2 | **c** | HttpOnly = inaccessible via JavaScript |
| 3 | **b** | Régénérer l'ID invalide l'ID fixé par l'attaquant |
| 4 | **c** | Hamster/Ferret = outils dédiés au sidejacking |
| 5 | **c** | HSTS force HTTPS, empêchant le downgrade SSL |
| 6 | **c** | `Secure` = HTTPS uniquement |
| 7 | **b** | Sans signature, n'importe qui peut modifier le payload |
| 8 | **b** | BeEF hook les navigateurs via XSS |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 11](./README.md)*
