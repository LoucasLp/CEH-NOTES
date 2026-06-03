# 🔴 Module 11 — Techniques Avancées de Session Hijacking

> **CEH v13 | EC-Council**  
> `#TCPHijacking` `#ARPPoisoning` `#SSLStripping` `#JWT` `#CookieTossing`

---

## 📌 Table des matières

1. [Prédiction de numéro de séquence TCP](#1-prédiction-de-séquence-tcp)
2. [ARP Poisoning pour MITM](#2-arp-poisoning)
3. [SSL Stripping](#3-ssl-stripping)
4. [Attaques JWT](#4-attaques-jwt)
5. [Cookie Tossing et Cookie Bomb](#5-cookie-tossing)
6. [Attaque par sous-domaine (Cookie Injection)](#6-sous-domaine-cookie-injection)

---

## 1. Prédiction de numéro de séquence TCP

### Fonctionnement du handshake TCP

```
Client                    Serveur
  │──SYN (SEQ=100)────────▶│
  │◀──SYN-ACK (SEQ=300, ACK=101)──│
  │──ACK (SEQ=101, ACK=301)▶│
  │                          │
  │◀── DATA ─────────────────│
```

### Attaque de prédiction SEQ

```
Contexte : attaquant peut observer le trafic mais pas interagir directement

1. Attaquant observe plusieurs connexions :
   SEQ #1 = 1000
   SEQ #2 = 2000
   SEQ #3 = 3000
   → Pattern : incréments de 1000
   
2. Attaquant prédit SEQ #4 ≈ 4000

3. Attaquant envoie un paquet RST ou DATA avec SEQ=4000
   → Serveur accepte le paquet comme légitime

4. Connexion détournée ou coupée (DoS/Hijacking)
```

> ⚠️ **Rappel CEH** : Les générateurs de nombres aléatoires modernes (RFC 6528) rendent la prédiction SEQ **quasi impossible**. C'est une attaque historique surtout vue en théorie à l'examen.

---

## 2. ARP Poisoning pour MITM

L'ARP Poisoning empoisonne les caches ARP pour devenir un Man-in-the-Middle, interceptant ensuite les sessions.

```
Réseau local :
  Alice  (192.168.1.10, MAC: AA:AA:AA)
  Bob    (192.168.1.20, MAC: BB:BB:BB)
  Routeur(192.168.1.1,  MAC: RR:RR:RR)

ÉTAT NORMAL :
  Cache ARP d'Alice : 192.168.1.1 → RR:RR:RR (correct)

APRÈS ARP POISONING (attaquant = CC:CC:CC) :
  Attaquant envoie à Alice : "192.168.1.1 est à CC:CC:CC"
  Attaquant envoie au Routeur : "192.168.1.10 est à CC:CC:CC"
  
  Alice → Routeur : passe par l'attaquant
  Routeur → Alice : passe par l'attaquant
  
  Cache ARP d'Alice : 192.168.1.1 → CC:CC:CC (empoisonné)
```

```bash
# Avec Ettercap (exemple démonstratif) :
ettercap -T -q -M arp:remote /192.168.1.10// /192.168.1.1//

# Avec arpspoof :
arpspoof -i eth0 -t 192.168.1.10 192.168.1.1
arpspoof -i eth0 -t 192.168.1.1 192.168.1.10
```

---

## 3. SSL Stripping

SSL Stripping (Moxie Marlinspike, 2009) dégrade une connexion HTTPS vers HTTP, exposant les cookies.

```
SANS SSL STRIPPING :
  Alice ──HTTPS──▶ bank.com  [Chiffré, cookies protégés]

AVEC SSL STRIPPING (attaquant en MITM) :
  
  Alice ──HTTP──▶ [Attaquant] ──HTTPS──▶ bank.com
  
  Alice croit être sur HTTP (pas de cadenas)
  L'attaquant voit tout en clair
  Les cookies de session transitent en clair côté Alice
```

### Protection : HSTS (HTTP Strict Transport Security)

```
# Header serveur :
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload

→ Le navigateur refuse de se connecter en HTTP
→ SSL Stripping impossible si HSTS est actif et en cache navigateur
```

---

## 4. Attaques JWT

### 4.1 Algorithme None Attack

```
Token légitime :
Header  : {"alg": "HS256", "typ": "JWT"}
Payload : {"user": "alice", "role": "user"}
Signature : HMACSHA256(header+payload, secret)

TOKEN FORGÉ (alg: none) :
Header  : {"alg": "none", "typ": "JWT"}
Payload : {"user": "alice", "role": "admin"}  ← Modifié !
Signature : (vide)

Si le serveur accepte alg:none → Bypass total !
```

### 4.2 Weak Secret Brute Force

```bash
# Avec jwt_tool :
python3 jwt_tool.py <token> -C -d /usr/share/wordlists/rockyou.txt

# Si le secret est faible ("secret123"), 
# l'attaquant peut signer ses propres tokens
```

### 4.3 Key Confusion (RS256 → HS256)

```
Serveur utilise RS256 (asymétrique : private key pour signer, public key pour vérifier)

Attaque :
1. Attaquant récupère la clé publique (souvent exposée dans /.well-known/jwks.json)
2. Forge un token signé avec RS256 en utilisant la clé publique comme secret HMAC
3. Change l'algorithme en HS256 dans le header
4. Serveur vulnérable vérifie HS256 avec la clé publique → Token accepté
```

---

## 5. Cookie Tossing et Cookie Bomb

### Cookie Tossing

```
Contexte : sous-domaine compromis (attacker.site.com)

L'attaquant peut définir des cookies pour le domaine parent .site.com :
Set-Cookie: session=EVIL; Domain=.site.com; Path=/

→ Quand la victime visite www.site.com, 
  son navigateur envoie le cookie malveillant
→ Session fixation via sous-domaine
```

### Cookie Bomb

```
L'attaquant crée des centaines de cookies pour un domaine :
Set-Cookie: c1=aaaaaa...a (4096 bytes)
Set-Cookie: c2=bbbbbb...b (4096 bytes)
... × 100

→ Le header Cookie devient énorme
→ Serveur refuse la requête (431 Request Header Fields Too Large)
→ Déni de service applicatif
```

---

## 6. Sous-domaine Cookie Injection

```
same-site.com utilise un cookie avec Domain=.same-site.com
Un sous-domaine compromis (admin.same-site.com) peut définir/écraser des cookies
pour tous les autres sous-domaines du même domaine parent.

Protection : SameSite=Strict empêche l'envoi cross-site mais pas cross-subdomain.
Pour une isolation totale : cookie sans attribut Domain (restreint au host exact).
```

---

## 🧠 Points clés à retenir pour l'examen

- **ARP Poisoning** est le prérequis courant pour une attaque MITM sur réseau local
- **SSL Stripping** dégrade HTTPS → HTTP ; contré par **HSTS**
- **JWT alg:none** = absence de signature = forgeage de token
- **Session fixation** peut venir d'un sous-domaine compromis (cookie tossing)
- Les attaques TCP SEQ sont historiques mais restent au programme CEH

---

*⬅️ [Types et mécanismes](./02_types_et_mecanismes.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
