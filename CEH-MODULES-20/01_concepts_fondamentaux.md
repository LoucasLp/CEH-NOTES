# 🔴 Module 20 — Concepts Fondamentaux : Cryptographie

> **CEH v13 | EC-Council**  
> `#Cryptographie` `#Symétrique` `#Asymétrique` `#CIA` `#NonRepudiation`

---

## 📌 Table des matières

1. [Définitions Clés](#1-définitions-clés)
2. [Objectifs de la Cryptographie](#2-objectifs-de-la-cryptographie)
3. [Symétrique vs Asymétrique](#3-symétrique-vs-asymétrique)
4. [Chiffrement par Blocs vs Flux](#4-chiffrement-par-blocs-vs-flux)
5. [Entropie et Longueur de Clé](#5-entropie-et-longueur-de-clé)

---

## 1. Définitions Clés

```
Cryptographie   : Science de la protection de l'information par le chiffrement
Cryptanalyse    : Science du déchiffrement sans la clé (casser les algorithmes)
Cryptologie     : Ensemble (cryptographie + cryptanalyse)

Terminologie :
  Plaintext (clair)    : Message original lisible
  Ciphertext (chiffré) : Message transformé, illisible sans clé
  Key (clé)            : Paramètre secret de l'algorithme
  IV/Nonce             : Valeur aléatoire pour l'initialisation (évite répétition)
  Salt                 : Valeur aléatoire ajoutée avant hachage (anti rainbow tables)
  Cipher               : Algorithme de chiffrement
```

---

## 2. Objectifs de la Cryptographie

```
CIA + Non-répudiation :

CONFIDENTIALITÉ  → Chiffrement symétrique/asymétrique
                   "Seul le destinataire peut lire le message"

INTÉGRITÉ        → Fonctions de hachage (SHA-256)
                   "Le message n'a pas été modifié"

AUTHENTIFICATION → Signatures numériques, certificats
                   "L'expéditeur est bien celui qu'il prétend être"

NON-RÉPUDIATION  → Signature numérique (clé privée unique)
                   "L'expéditeur ne peut pas nier avoir envoyé le message"
```

| Objectif | Mécanisme | Exemples |
|----------|-----------|---------|
| **Confidentialité** | Chiffrement | AES, RSA |
| **Intégrité** | Hachage | SHA-256, SHA-3 |
| **Authentification** | Certificats, MFA | X.509, TOTP |
| **Non-répudiation** | Signature numérique | RSA signature, ECDSA |

---

## 3. Symétrique vs Asymétrique

```
SYMÉTRIQUE :
  Même clé pour chiffrer ET déchiffrer

  Alice ──chiffre avec K──▶ Ciphertext ──déchiffre avec K──▶ Bob

  Avantages : Rapide, efficace pour gros volumes de données
  Inconvénients : Problème d'échange de clé (comment partager K de façon sécurisée ?)
  Exemples : AES, DES, 3DES, RC4, Blowfish, ChaCha20

ASYMÉTRIQUE :
  Paire de clés : Clé Publique + Clé Privée
  Ce qui est chiffré avec Pub ne peut être déchiffré qu'avec Priv (et vice versa)

  Chiffrement (pour Bob) :
  Alice ──chiffre avec clé Pub de Bob──▶ Ciphertext ──déchiffre avec clé Priv de Bob──▶ Bob

  Signature numérique (par Alice) :
  Alice ──signe avec clé Priv d'Alice──▶ Signature ──vérifie avec clé Pub d'Alice──▶ Bob

  Avantages : Pas de problème d'échange de clé
  Inconvénients : Lent (100-1000x plus lent qu'AES)
  Exemples : RSA, ECC, DSA, ElGamal, Diffie-Hellman

HYBRIDE (pratique réelle — TLS, PGP) :
  RSA/DH pour échanger une clé de session AES (quelques Ko)
  AES pour chiffrer les données (rapidement)
  → TLS utilise cette approche !
```

| Critère | Symétrique | Asymétrique |
|---------|-----------|-------------|
| **Clés** | 1 clé partagée | Paire publique/privée |
| **Vitesse** | Très rapide | 100-1000x plus lent |
| **Usage typique** | Chiffrer les données | Échange de clé, signatures |
| **Exemples** | AES, DES, ChaCha20 | RSA, ECC, DSA |

> 💡 **Question type exam** : *Quel algorithme est utilisé en pratique pour chiffrer de gros volumes de données ?* → **AES (symétrique)** car RSA est trop lent pour les données volumineuses.

---

## 4. Chiffrement par Blocs vs Flux

```
BLOCK CIPHER (par blocs) :
  → Divise le message en blocs de taille fixe (64 ou 128 bits)
  → Chiffre chaque bloc selon un mode d'opération
  → Exemples : AES (blocs 128 bits), DES (blocs 64 bits)

  Modes importants :
  ECB (Electronic Codebook) : Chaque bloc indépendant → DANGEREUX (patterns visibles)
  CBC (Cipher Block Chaining): XOR avec bloc précédent → meilleur mais vulnérable POODLE
  CTR (Counter) : Transforme en stream cipher → parallélisable, rapide
  GCM (Galois/Counter) : CTR + authentification (AEAD) → RECOMMANDÉ

STREAM CIPHER (flux) :
  → Chiffre bit à bit / octet à octet avec un keystream pseudo-aléatoire
  → Exemples : RC4 (obsolète), ChaCha20 (moderne)
  → Rapide sur les petits appareils (IoT, mobile)
```

---

## 5. Entropie et Longueur de Clé

```
Sécurité en bits = log2(nombre de clés possibles)

AES-128 : 2^128 combinaisons ≈ 10^38 → infeasible avec technologie actuelle
AES-256 : 2^256 combinaisons ≈ 10^77 → résiste même aux ordinateurs quantiques
RSA-2048 : Sécurité équivalente ≈ 112 bits (factorisation plus facile)
ECC-256  : Sécurité équivalente ≈ 128 bits (logarithme discret sur courbe elliptique)

Recommandations actuelles (NIST 2024) :
  Symétrique : AES-128 minimum, AES-256 recommandé
  Asymétrique RSA : 2048 bits minimum, 4096 recommandé
  Asymétrique ECC : 256 bits minimum (P-256 ou Curve25519)
  Hash : SHA-256 minimum
```

---

## 🧠 Points clés à retenir pour l'examen

- **Symétrique** = même clé pour chiffrer et déchiffrer, rapide (AES, DES, ChaCha20)
- **Asymétrique** = clé publique + privée, lent (RSA, ECC, DSA)
- **Hybride** = TLS, PGP : RSA pour l'échange de clé + AES pour les données
- **AES-256** = standard actuel recommandé pour le chiffrement symétrique
- **ECB** = mode de chiffrement dangereux (patterns identiques → ciphertext identique)
- **GCM** = mode AES recommandé (chiffrement + authentification intégrée = AEAD)
- **Non-répudiation** = seule la signature numérique (clé privée) garantit cet objectif

---

*⬅️ [README](./README.md) | ➡️ [Algorithmes](./02_algorithmes.md)*
