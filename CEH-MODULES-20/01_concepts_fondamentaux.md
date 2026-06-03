# 🔴 Module 20 — Concepts Fondamentaux : Cryptographie

> **CEH v13 | EC-Council**  
> `#Cryptographie` `#Symétrique` `#Asymétrique` `#CIA` `#NonRepudiation`

---

## 1. Définitions Clés

```
Cryptographie   : Science de la protection de l'information par le chiffrement
Cryptanalyse    : Science de la déchiffrement sans la clé
Cryptologie     : Ensemble (cryptographie + cryptanalyse)

Terminologie :
  Plaintext (clair)    : Message original lisible
  Ciphertext (chiffré) : Message transformé, illisible sans clé
  Key (clé)            : Paramètre secret de l'algorithme
  IV/Nonce             : Valeur aléatoire pour l'initialisation (évite répétition)
  Salt                 : Valeur aléatoire ajoutée avant hachage (anti rainbow tables)
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

---

## 3. Symétrique vs Asymétrique

```
SYMÉTRIQUE :
  Même clé pour chiffrer ET déchiffrer
  
  Alice ──chiffre avec K──▶ Ciphertext ──déchiffre avec K──▶ Bob
  
  Avantages : Rapide, efficace pour gros volumes
  Inconvénients : Problème d'échange de clé (comment partager K en sécurité ?)
  Exemples : AES, DES, 3DES, RC4, Blowfish

ASYMÉTRIQUE :
  Paire de clés : Clé Publique + Clé Privée
  Ce qui est chiffré avec Pub ne peut être déchiffré qu'avec Priv (et vice versa)
  
  Chiffrement :
  Alice ──chiffre avec clé Pub de Bob──▶ Ciphertext ──déchiffre avec clé Priv de Bob──▶ Bob
  
  Signature :
  Alice ──signe avec clé Priv d'Alice──▶ Signature ──vérifie avec clé Pub d'Alice──▶ Bob
  
  Avantages : Pas de problème d'échange de clé
  Inconvénients : Lent (100-1000x plus lent qu'AES)
  Exemples : RSA, ECC, DSA, ElGamal, Diffie-Hellman

HYBRIDE (pratique réelle) :
  RSA/DH pour échanger une clé de session AES
  AES pour chiffrer les données (rapide)
  → TLS utilise cette approche !
```

---

## 4. Chiffrement par Blocs vs Flux

```
BLOCK CIPHER (par blocs) :
  → Divise le message en blocs de taille fixe (64 ou 128 bits)
  → Chiffre chaque bloc
  → Exemples : AES (128 bits), DES (64 bits)
  
  Modes importants :
  ECB (Electronic Codebook) : Chaque bloc indépendant → DANGEREUX (patterns visibles)
  CBC (Cipher Block Chaining): XOR avec bloc précédent → meilleur
  CTR (Counter) : Transforme en stream cipher → parallélisable
  GCM (Galois/Counter) : CTR + authentification → recommandé
  
STREAM CIPHER (flux) :
  → Chiffre bit à bit / octet à octet avec un keystream
  → Exemples : RC4 (obsolète), ChaCha20 (moderne)
  → Rapide sur les petits appareils
```

---

## 5. Entropie et Longueur de Clé

```
Sécurité en bits = log2(nombre de clés possibles)

AES-128 : 2^128 combinaisons ≈ 10^38 → infeasible
AES-256 : 2^256 combinaisons ≈ 10^77 → résiste même aux ordinateurs quantiques
RSA-2048 : Sécurité équivalente ≈ 112 bits (factorisation plus facile)
ECC-256  : Sécurité équivalente ≈ 128 bits (logarithme discret)

Recommandations actuelles (NIST 2024) :
  Symétrique : AES-128 minimum, AES-256 recommandé
  Asymétrique RSA : 2048 bits minimum, 4096 recommandé
  Asymétrique ECC : 256 bits minimum (P-256 ou Curve25519)
  Hash : SHA-256 minimum
```

---

## 🧠 Points clés à retenir pour l'examen

- **Symétrique** = même clé, rapide (AES, DES)
- **Asymétrique** = clé publique + privée, lent (RSA, ECC)
- **Hybride** = TLS, PGP utilisent les deux
- **AES-256** = standard actuel recommandé
- **ECB** = mode dangereux (patterns visibles)
- **GCM** = mode AES recommandé (chiffrement + authentification)

---

*⬅️ [README](./README.md) | ➡️ [Algorithmes](./02_algorithmes.md)*
