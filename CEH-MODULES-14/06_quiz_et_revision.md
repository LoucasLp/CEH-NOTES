# 📝 Module 14 — Quiz & Révision : Hacking Web Applications

> **CEH v13 | EC-Council**  
> `#Quiz` `#OWASP` `#XSS` `#CSRF` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- Module 14 = l'un des plus importants de l'examen CEH (~10-12%)
- OWASP Top 10 2021 est à connaître par cœur (A01 à A10)
- **XSS** : Reflected (URL) / Stored (DB) / DOM-based (côté client)
- **CSRF** : forger une requête du navigateur authentifié
- **IDOR** = A01, manipulation d'IDs pour accéder à des ressources non autorisées
- **Burp Suite** = outil principal pour les tests web

---

## 🧠 OWASP Top 10 — Synthèse

| # | Catégorie | Exemple concret |
|---|-----------|-----------------|
| A01 | Broken Access Control | IDOR, force browsing vers /admin |
| A02 | Cryptographic Failures | Mots de passe en MD5, HTTP |
| A03 | Injection | SQLi, XSS, Command injection |
| A04 | Insecure Design | Workflow sans threat modeling |
| A05 | Security Misconfiguration | XXE, verbose errors, defaults |
| A06 | Vulnerable Components | Log4Shell, plugins outdated |
| A07 | Auth Failures | Brute force, sessions faibles |
| A08 | Integrity Failures | Désérialisation, supply chain |
| A09 | Logging Failures | Pas de monitoring |
| A10 | SSRF | Accès metadata cloud interne |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **XSS Stored** est plus dangereux que **XSS Reflected** (persistant, touche tous les visiteurs)
2. **XSS DOM-based** = côté client, jamais vu par le serveur
3. **CSRF** nécessite que la victime soit authentifiée
4. **`X-Frame-Options: SAMEORIGIN`** = protection clickjacking
5. **CSP** est la défense la plus efficace contre XSS
6. **Burp Suite Intruder** = brute force et fuzzing
7. **Burp Suite Repeater** = modifier et rejouer des requêtes
8. **IDOR** = A01 OWASP = changer l'ID dans l'URL pour accéder à d'autres données
9. **Désérialisation non sécurisée** = A08 OWASP
10. **SSRF** = A10 OWASP, cible les services internes ou metadata cloud

---

## ❓ Questions de révision

1. Quelle est la différence entre XSS Reflected, Stored et DOM-based ?
2. Pourquoi le XSS Stored est-il considéré comme plus dangereux ?
3. Comment fonctionne une attaque CSRF et quelle en est la contre-mesure principale ?
4. Expliquer l'IDOR avec un exemple concret et sa contre-mesure.
5. Qu'est-ce que le Clickjacking et comment le Content-Security-Policy peut-il le contrer ?
6. Pourquoi l'upload d'un fichier PHP dans un webroot est-il critique ?
7. Qu'est-ce que l'SSRF et quel service cloud expose des risques critiques via SSRF ?
8. Quelle est la différence entre SAST et DAST ?

---

## 📋 Quiz QCM

**Question 1.** Quel type de XSS stocke le payload dans la base de données ?
- a) Reflected XSS
- b) DOM-based XSS
- c) Stored XSS
- d) Blind XSS

**Question 2.** La protection principale contre le Clickjacking est :
- a) CSRF token
- b) HttpOnly cookie flag
- c) X-Frame-Options: SAMEORIGIN
- d) HSTS

**Question 3.** IDOR correspond à quelle catégorie OWASP Top 10 2021 ?
- a) A03 — Injection
- b) A01 — Broken Access Control
- c) A07 — Authentication Failures
- d) A05 — Security Misconfiguration

**Question 4.** Un attaquant découvre que `https://site.com/order?id=1001` retourne sa commande. En changeant id=1002 il voit la commande d'un autre utilisateur. Quelle est cette vulnérabilité ?
- a) SQL Injection
- b) CSRF
- c) IDOR
- d) XSS Stored

**Question 5.** Quel header HTTP empêche l'exécution de scripts inline non autorisés ?
- a) X-Content-Type-Options
- b) X-Frame-Options
- c) Content-Security-Policy
- d) HSTS

**Question 6.** La catégorie A10 de l'OWASP Top 10 2021 correspond à :
- a) SQL Injection
- b) SSRF
- c) Broken Access Control
- d) XSS

**Question 7.** Un fichier uploadé avec l'extension `.php` dans `/uploads/` accessible via HTTP permet :
- a) Vol de cookie de session
- b) Exécution de code côté serveur (WebShell)
- c) Injection SQL dans la DB
- d) Déni de service du serveur

**Question 8.** Quel outil Burp Suite est utilisé pour modifier et rejouer une requête individuelle ?
- a) Scanner
- b) Intruder
- c) Repeater
- d) Decoder

---

## ✅ Points de révision — Checklist

- [ ] Je connais les 10 catégories OWASP Top 10 2021 et leurs exemples
- [ ] Je comprends les 3 types de XSS (Reflected, Stored, DOM)
- [ ] Je sais expliquer CSRF et ses contre-mesures (token + SameSite)
- [ ] Je peux décrire l'IDOR et pourquoi la vérification serveur est obligatoire
- [ ] Je comprends le Clickjacking et X-Frame-Options
- [ ] Je sais pourquoi les fichiers uploadés doivent être stockés hors du webroot
- [ ] Je connais les modules Burp Suite (Proxy, Repeater, Intruder, Scanner)
- [ ] Je comprends SSRF et le risque metadata cloud (169.254.169.254)
- [ ] Je sais ce que fait CSP contre XSS
- [ ] Je comprends la différence SAST vs DAST

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **c** | Stored XSS = stocké en DB, persistant |
| 2 | **c** | X-Frame-Options empêche l'iframe invisible |
| 3 | **b** | IDOR = Broken Access Control = A01 |
| 4 | **c** | Manipulation d'ID = IDOR classique |
| 5 | **c** | CSP contrôle les sources de scripts autorisées |
| 6 | **b** | A10 OWASP 2021 = SSRF |
| 7 | **b** | Webshell PHP = RCE via upload |
| 8 | **c** | Repeater = rejouer/modifier une requête |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 14](./README.md)*
