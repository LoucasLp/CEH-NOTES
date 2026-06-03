# 🔴 Module 14 — Concepts Fondamentaux : Hacking Web Applications

> **CEH v13 | EC-Council**  
> `#WebApp` `#Architecture` `#SOP` `#CORS` `#SAST` `#DAST`

---

## 📌 Table des matières

1. [Architecture web 3-tiers](#1-architecture-3-tiers)
2. [Same-Origin Policy (SOP) et CORS](#2-sop-et-cors)
3. [Types de tests de sécurité](#3-types-de-tests)
4. [SDLC sécurisé](#4-sdlc-sécurisé)
5. [Surfaces d'attaque applicatives](#5-surfaces-dattaque)

---

## 1. Architecture Web 3-Tiers

```
TIER 1 — PRÉSENTATION (Client)
  ├── Navigateur (HTML, CSS, JavaScript)
  ├── Application mobile
  └── API client (curl, Postman)
  
TIER 2 — LOGIQUE APPLICATIVE (Serveur)
  ├── Web Server (Apache, Nginx, IIS)
  ├── Application Server (PHP, Node.js, Java, Python)
  ├── Business Logic
  └── API REST / GraphQL / SOAP
  
TIER 3 — DONNÉES (Base de données)
  ├── SGBD Relationnel (MySQL, PostgreSQL, MSSQL)
  ├── NoSQL (MongoDB, Redis, Elasticsearch)
  └── Fichiers (stockage blob, file system)
```

### Flux d'une requête web

```
Navigateur → DNS → CDN → Load Balancer → WAF → Web Server → App Server → DB
                                                    ↑
                                              ATTAQUANT CIBLE
                                              chaque composant
```

---

## 2. Same-Origin Policy (SOP) et CORS

### Same-Origin Policy

```
Une page web NE PEUT PAS lire les données d'une autre origine.

Origine = Schéma + Domaine + Port
  https://site.com:443    ← Origine de référence
  
  https://site.com/admin  ✓ Même origine (même schéma/domaine/port)
  http://site.com:80      ✗ Schéma et port différents
  https://evil.com:443    ✗ Domaine différent
  https://sub.site.com    ✗ Sous-domaine différent

But : empêcher le vol de données cross-site (JavaScript ne peut pas lire
les réponses de requêtes vers d'autres origines)
```

### CORS (Cross-Origin Resource Sharing)

```
CORS permet à un serveur d'autoriser explicitement d'autres origines.

Requête cross-origin :
GET /api/user HTTP/1.1
Host: api.site.com
Origin: https://app.site.com    ← Navigateur ajoute ce header

Réponse avec CORS permissif (dangereux) :
Access-Control-Allow-Origin: *                          ← Tout le monde !
Access-Control-Allow-Credentials: true                  ← + Cookies !
→ N'importe quel site peut lire les données de l'API

Réponse CORS sécurisée :
Access-Control-Allow-Origin: https://app.site.com       ← Whitelist uniquement
```

---

## 3. Types de Tests de Sécurité

| Type | Description | Quand | Outils |
|------|-------------|-------|--------|
| **SAST** | Analyse statique du code source | Pendant le développement | SonarQube, Checkmarx |
| **DAST** | Tests dynamiques sur l'app en exécution | Staging/production | Burp Suite, OWASP ZAP |
| **IAST** | Instrumentation de l'app en temps réel | QA / staging | Contrast Security |
| **SCA** | Analyse des dépendances open-source | Build pipeline | Snyk, OWASP Dependency-Check |
| **Pentest manuel** | Test humain approfondi | Avant mise en production | Burp Suite + expertise |

```
Shift Left Security :
Développement → Tests → Staging → Production
     ↑
     SAST ici (plus tôt = moins cher à corriger)
     
     DAST + Pentest ici
```

---

## 4. SDLC Sécurisé

```
Microsoft SDL / OWASP SAMM :

1. FORMATION
   → Former les développeurs à la sécurité (OWASP, SANS)

2. EXIGENCES
   → Définir les exigences de sécurité et de confidentialité

3. DESIGN
   → Threat modeling (identifier les menaces dès la conception)
   → Architecture sécurisée (moindre privilège, séparation des rôles)

4. IMPLÉMENTATION
   → Coding standards sécurisés
   → Analyse statique automatisée (SAST)

5. VÉRIFICATION
   → Tests de sécurité (DAST, pentest)
   → Code review

6. DÉPLOIEMENT
   → Configuration sécurisée du serveur
   → Gestion des secrets

7. RÉPONSE
   → Processus de gestion des vulnérabilités
   → Bug bounty
```

---

## 5. Surfaces d'Attaque Applicatives

```
┌─────────────────────────────────────────────────────────────────┐
│              SURFACES D'ATTAQUE — APPLICATION WEB               │
├──────────────────────┬──────────────────────────────────────────┤
│ ENTRÉES UTILISATEUR  │ Formulaires, URL params, headers, cookies│
│                      │ JSON body, fichiers uploadés             │
├──────────────────────┼──────────────────────────────────────────┤
│ AUTHENTIFICATION     │ Login, reset MDP, sessions, tokens OAuth │
├──────────────────────┼──────────────────────────────────────────┤
│ AUTORISATION         │ Contrôle d'accès aux ressources, RBAC   │
├──────────────────────┼──────────────────────────────────────────┤
│ LOGIQUE MÉTIER       │ Workflow, conditions, validation côté    │
│                      │ client seulement, race conditions        │
├──────────────────────┼──────────────────────────────────────────┤
│ APIS                 │ REST, GraphQL, SOAP, OAuth, JWT          │
├──────────────────────┼──────────────────────────────────────────┤
│ COMPOSANTS TIERS     │ Bibliothèques, CMS plugins, CDN          │
└──────────────────────┴──────────────────────────────────────────┘
```

> ⚠️ **Rappel CEH** : La majorité des attaques web exploitent des **entrées utilisateur non validées** côté serveur. La validation côté client (JavaScript) est facilement contournée.

---

## 🧠 Points clés à retenir pour l'examen

- Architecture 3-tiers : Présentation → Logique → Données
- **SOP** = une page ne peut pas lire des données d'une autre origine
- **CORS** mal configuré (`*` + credentials) = bypass de la SOP
- **SAST** = analyse du code source (statique) ; **DAST** = tests sur l'app en exécution
- **SDLC sécurisé** = intégrer la sécurité à chaque phase du développement
- La validation **côté client** est facilement contournée (Burp Suite)

---

*⬅️ [README](./README.md) | ➡️ [OWASP Top 10](./02_owasp_top10.md)*
