# 🔴 Module 20 — Algorithmes Cryptographiques

> **CEH v13 | EC-Council**  
> `#AES` `#RSA` `#ECC` `#DiffieHellman` `#SHA` `#bcrypt` `#MD5`

---

## 📌 Table des matières

1. [Algorithmes Symétriques](#1-algorithmes-symétriques)
2. [Algorithmes Asymétriques](#2-algorithmes-asymétriques)
3. [Fonctions de Hachage](#3-fonctions-de-hachage)

---

## 1. Algorithmes Symétriques

| Algorithme | Taille clé | Taille bloc | Statut |
|------------|-----------|-------------|--------|
| **DES** | 56 bits | 64 bits | Obsolète (cassé en 22h) |
| **3DES** | 112/168 bits | 64 bits | Déprécié (lent, legacy) |
| **AES** | 128/192/256 bits | 128 bits | Standard actuel ✓ |
| **RC4** | 40-2048 bits | Stream | Obsolète (WEP, TLS ancien) |
| **Blowfish** | 32-448 bits | 64 bits | Valide mais remplacé par AES |
| **ChaCha20** | 256 bits | Stream | Moderne, rapide sur mobile |

### AES — Modes d'Opération

```
ECB (Electronic Codebook) :
  ❌ DANGEREUX
  Blocs identiques → Ciphertext identiques (patterns visibles)
  Exemple : Image chiffrée ECB encore reconnaissable !

CBC (Cipher Block Chaining) :
  ✓ Utilisé couramment
  XOR avec ciphertext précédent → chaque bloc dépend du précédent
  Nécessite un IV aléatoire
  Vulnérable : POODLE (padding oracle)

CTR (Counter) :
  ✓ Performant, parallélisable
  Transform en stream cipher : AES(IV+counter) XOR plaintext

GCM (Galois/Counter Mode) :
  ✓✓ RECOMMANDÉ
  CTR + GMAC (authentification)
  Assure confidentialité ET intégrité
  Utilisé dans TLS 1.3
```

---

## 2. Algorithmes Asymétriques

### RSA

```
Principe : Basé sur la difficulté de factoriser le produit de deux grands nombres premiers

Génération :
  1. Choisir p et q (grands premiers)
  2. n = p × q (module)
  3. φ(n) = (p-1)(q-1)
  4. Clé publique : (e, n) où gcd(e, φ(n)) = 1
  5. Clé privée : (d, n) où e × d ≡ 1 (mod φ(n))

Chiffrement : C = M^e mod n
Déchiffrement : M = C^d mod n

Tailles recommandées :
  2048 bits : acceptable
  4096 bits : recommandé pour les secrets long terme
  
Usages : Chiffrement de clés de session, signatures numériques, TLS
```

### ECC (Elliptic Curve Cryptography)

```
Principe : Basé sur le problème du logarithme discret sur les courbes elliptiques

Avantage vs RSA :
  ECC-256 ≈ RSA-3072 en terme de sécurité
  Clés plus petites → Performances meilleures (mobile, IoT)
  
Courbes importantes :
  P-256 (NIST) : Standard, TLS
  Curve25519 : Moderne, plus rapide, Signal, WireGuard
  P-384 : Haute sécurité
  
Algorithmes ECC :
  ECDSA : Digital Signature Algorithm sur ECC
  ECDH  : Diffie-Hellman sur ECC (échange de clé)
```

### Diffie-Hellman

```
Objectif : Permettre à deux parties d'établir un secret partagé sur un canal public

Principe :
  1. Alice et Bob conviennent de g et p (publics)
  2. Alice choisit a (secret), calcule A = g^a mod p, envoie A à Bob
  3. Bob choisit b (secret), calcule B = g^b mod p, envoie B à Alice
  4. Alice calcule secret = B^a mod p
  5. Bob calcule secret = A^b mod p
  6. secret = g^(ab) mod p (même valeur !)
  
Vulnérabilité : Sans authentification → Man-in-the-Middle !
DHE/ECDHE : version éphémère (nouvelle clé par session) → Perfect Forward Secrecy
```

---

## 3. Fonctions de Hachage

```
Propriétés d'un bon hash :
  1. Déterministe : même entrée → même hash
  2. À sens unique : impossible de retrouver l'entrée depuis le hash
  3. Résistance aux collisions : difficile de trouver deux entrées → même hash
  4. Effet avalanche : 1 bit changé → hash complètement différent
```

| Algorithme | Taille | Statut |
|------------|--------|--------|
| **MD5** | 128 bits | Cassé (collisions), ne pas utiliser pour sécurité |
| **SHA-1** | 160 bits | Cassé (collision 2017), obsolète |
| **SHA-256** | 256 bits | Sécurisé ✓, recommandé |
| **SHA-512** | 512 bits | Sécurisé ✓, plus lent |
| **SHA-3** | 224-512 bits | Keccak, sécurisé, alternative SHA-2 |
| **BLAKE2** | 256-512 bits | Rapide, sécurisé |

### Hachage des Mots de Passe

```
NE PAS utiliser : MD5, SHA-1, SHA-256 directement pour les mots de passe !
→ Trop rapides → Brute force en millisecondes avec GPU

UTILISER :
  bcrypt    : Fonction lente + salt intégré, facteur de coût ajustable
  Argon2    : Moderne, résistant GPU (mémoire intensive), RECOMMANDÉ 2024
  PBKDF2    : Compatible FIPS, utilisé dans WPA2
  scrypt    : Résistant GPU (mémoire intensive)

bcrypt Python :
  import bcrypt
  hash = bcrypt.hashpw(password.encode(), bcrypt.gensalt(rounds=12))
  # Rounds=12 : ~400ms sur PC moderne → Brute force impraticable
```

---

## 🧠 Points clés à retenir pour l'examen

| Algorithme | Catégorie | Statut | Usage |
|------------|-----------|--------|-------|
| DES | Symétrique | Obsolète | — |
| AES | Symétrique | Standard | Chiffrement données |
| RSA | Asymétrique | Standard | TLS, signatures |
| ECC | Asymétrique | Standard | TLS, mobile |
| DH/DHE | Échange clé | Standard | TLS Perfect Forward Secrecy |
| MD5 | Hash | Cassé | Sommes de contrôle non-sécurisées |
| SHA-1 | Hash | Cassé (2017) | — |
| SHA-256 | Hash | Standard | Intégrité, certificats |
| bcrypt | Hash MDP | Standard | Stockage mots de passe |

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Attaques cryptographiques](./03_attaques_crypto.md)*
