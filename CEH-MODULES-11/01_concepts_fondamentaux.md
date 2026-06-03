# 🔴 Module 11 — Concepts Fondamentaux : Session Hijacking

> **CEH v13 | EC-Council**  
> `#Session` `#Hijacking` `#Cookie` `#TCP` `#Auth`

---

## 📌 Table des matières

1. [Qu'est-ce qu'une session ?](#1-quest-ce-quune-session)
2. [Cycle de vie d'une session web](#2-cycle-de-vie-dune-session-web)
3. [Mécanismes d'identification de session](#3-mécanismes-didentification)
4. [TCP vs Application-level Hijacking](#4-tcp-vs-application-level)
5. [Surface d'attaque du session hijacking](#5-surface-dattaque)
6. [Impacts et risques](#6-impacts-et-risques)

---

## 1. Qu'est-ce qu'une session ?

Une **session** est un état temporaire et partagé entre un client et un serveur, permettant de maintenir un contexte d'identification au-delà du protocole HTTP stateless.

```
Problème HTTP : STATELESS
┌────────┐   GET /page   ┌────────┐
│ Client │ ─────────────▶│Serveur │  "Qui êtes-vous ?"
└────────┘◀───────────── └────────┘
           200 OK
           
Solution : SESSION
┌────────┐  GET /page + Cookie:SESS=abc123  ┌────────┐
│ Client │ ─────────────────────────────────▶│Serveur │  "Ah, vous êtes Alice !"
└────────┘◀─────────────────────────────── └────────┘
           200 OK (contenu personnalisé)
```

---

## 2. Cycle de vie d'une session web

```
1. AUTHENTIFICATION
   [Client] ──POST /login (user+pass)──▶ [Serveur]
   
2. CRÉATION DE SESSION
   [Serveur] génère Session ID (ex: sid=A3f9x2...)
   Stocke en mémoire/DB : sid → {user: Alice, role: admin, exp: +30min}

3. TRANSMISSION DU TOKEN
   [Serveur] ──Set-Cookie: sid=A3f9x2; Secure; HttpOnly──▶ [Client]
   
4. UTILISATION
   [Client] ──GET /dashboard + Cookie: sid=A3f9x2──▶ [Serveur]
   [Serveur] vérifie sid → retourne la page

5. EXPIRATION / LOGOUT
   [Client] ──POST /logout──▶ [Serveur] invalide sid
```

### Caractéristiques d'un bon Session ID

| Propriété | Exigence | Exemple mauvais | Exemple bon |
|-----------|----------|-----------------|-------------|
| Longueur | 128+ bits | `sid=12345` | `sid=a3f2c9...` (32 hex) |
| Entropie | Aléatoire cryptographique | `sid=user_1_time_1690000000` | UUID v4 |
| Unicité | Jamais réutilisé | Session incrémentale | Random + timestamp |
| Durée de vie | Session ou courte | Permanent | 30 min inactivité |

---

## 3. Mécanismes d'identification de session

### 3.1 Cookies HTTP

```
Set-Cookie: sessionid=abc123; Path=/; Secure; HttpOnly; SameSite=Strict; Max-Age=1800

Attributs importants :
├── Secure     : transmis uniquement via HTTPS
├── HttpOnly   : inaccessible en JavaScript (anti-XSS)
├── SameSite   : Strict | Lax | None (anti-CSRF)
├── Path       : scope du cookie (URL)
├── Domain     : domaine valide
└── Max-Age    : durée de vie en secondes
```

### 3.2 URL Parameters (insecure)

```
https://site.com/dashboard?sessid=abc123
⚠️ Visible dans les logs serveur, historique navigateur, Referer header
```

### 3.3 Hidden Form Fields (insecure)

```html
<input type="hidden" name="sessid" value="abc123">
⚠️ Visible dans le source HTML, transmis en POST
```

### 3.4 JWT (JSON Web Token)

```
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VyIjoiYWxpY2UiLCJyb2xlIjoiYWRtaW4ifQ.SIGNATURE

Structure : Header.Payload.Signature
- Header  : {"alg": "HS256", "typ": "JWT"}
- Payload : {"user": "alice", "role": "admin", "exp": 1700000000}
- Signature: HMACSHA256(header.payload, secret)
```

> ⚠️ **Rappel CEH** : JWT avec `alg: none` est une vulnérabilité classique — sans signature, n'importe qui peut forger un token valide.

---

## 4. TCP vs Application-Level Hijacking

| Critère | TCP Session Hijacking | Application-Level Hijacking |
|---------|----------------------|-----------------------------|
| **Couche OSI** | Transport (L4) | Application (L7) |
| **Cible** | Paquets TCP en transit | Cookie / Token HTTP |
| **Condition** | Accès réseau (MITM) | Souvent à distance |
| **Méthode** | Prédiction SEQ/ACK | Vol de cookie, XSS, CSRF |
| **Complexité** | Élevée (réseau) | Moyenne (web) |
| **Exemple** | RST injection | Cookie theft via XSS |
| **Détection** | IDS réseau | WAF / logs applicatifs |

```
TCP Hijacking :
  Client ──SYN──▶ Serveur
  Client ◀──SYN-ACK── Serveur
  [Attaquant intercepte et prédit le numéro SEQ]
  Attaquant ──ACK (SEQ prédit)──▶ Serveur  ← Connexion détournée

App-Level Hijacking :
  Alice s'authentifie → obtient cookie sid=abc
  [Attaquant vole le cookie via XSS ou sniffing]
  Attaquant ──GET /profile + Cookie: sid=abc──▶ Serveur  ← Usurpation identité
```

---

## 5. Surface d'attaque

```
┌─────────────────────────────────────────────────────────────┐
│               SURFACE D'ATTAQUE SESSION HIJACKING           │
├──────────────────┬──────────────────────────────────────────┤
│ Réseau           │ Sniffing HTTP, ARP poisoning, MITM       │
│ Navigateur       │ XSS → document.cookie, LocalStorage      │
│ Serveur          │ Session fixation, prédiction ID          │
│ Transport        │ SSL Stripping, downgrade TLS             │
│ Protocole TCP    │ SEQ prediction, RST injection            │
│ Token/JWT        │ alg:none, weak secret, token theft       │
└──────────────────┴──────────────────────────────────────────┘
```

---

## 6. Impacts et risques

| Impact | Description |
|--------|-------------|
| **Usurpation d'identité** | L'attaquant agit en tant que la victime |
| **Accès aux données** | Consultation des informations personnelles |
| **Actions non autorisées** | Transactions, suppressions, modifications |
| **Escalade de privilèges** | Si la session volée est admin |
| **Persistance** | Accès maintenu tant que la session vit |
| **Violation RGPD** | Fuite de données personnelles |

> ⚠️ **Rappel CEH** : Le session hijacking vise principalement à contourner l'authentification **après** une connexion légitime, sans avoir besoin du mot de passe.

---

## 🧠 Points clés à retenir pour l'examen

- Session ID doit être : long (128+ bits), aléatoire, unique, temporaire
- HTTP est **stateless** → les sessions compensent ce manque
- Différence TCP hijacking (réseau) vs App-level (web)
- Les cookies `HttpOnly` empêchent le vol via JavaScript
- JWT sans signature (`alg: none`) = vulnérabilité critique

---

*⬅️ [README](./README.md) | ➡️ [Types et mécanismes](./02_types_et_mecanismes.md)*
