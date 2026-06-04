# 🛡️ Module 20 — Contre-mesures : Bonnes Pratiques Cryptographiques

> **CEH v13 | EC-Council**  
> `#AlgorithmesRecommandés` `#GestionClés` `#TLS13` `#PostQuantum` `#HSTS`

---

## 📌 Table des matières

1. [Algorithmes Recommandés (2024)](#1-algorithmes-recommandés-2024)
2. [Configuration TLS Sécurisée](#2-configuration-tls-sécurisée)
3. [Gestion des Clés](#3-gestion-des-clés)
4. [HSTS et Headers TLS](#4-hsts-et-headers-tls)
5. [Post-Quantum Cryptography](#5-post-quantum-cryptography)

---

## 1. Algorithmes Recommandés (2024)

```
CHIFFREMENT SYMÉTRIQUE :
  ✓ AES-256-GCM         (confidentialité + intégrité)
  ✓ AES-128-GCM         (acceptable)
  ✓ ChaCha20-Poly1305   (mobile, IoT)
  ✗ DES, 3DES           (obsolètes)
  ✗ RC4                 (cassé)
  ✗ AES-ECB             (patterns visibles)
  
CHIFFREMENT ASYMÉTRIQUE :
  ✓ RSA-2048+           (minimum acceptable)
  ✓ RSA-4096            (recommandé pour secrets long terme)
  ✓ ECDSA P-256 / P-384 (signatures TLS)
  ✓ Curve25519 / Ed25519 (moderne, performances)
  ✗ RSA-1024            (cassé)
  
FONCTIONS DE HACHAGE :
  ✓ SHA-256, SHA-384, SHA-512  (intégrité, signatures)
  ✓ SHA-3                       (alternative Keccak)
  ✗ MD5, SHA-1                  (cassés)
  
MOTS DE PASSE :
  ✓ Argon2id             (RECOMMANDÉ 2024)
  ✓ bcrypt (rounds ≥ 12) (acceptable)
  ✓ scrypt               (résistant GPU)
  ✗ MD5, SHA-1/256       (trop rapides pour MDP)
```

---

## 2. Configuration TLS Sécurisée

```
TLS versions :
  ✓ TLS 1.3 (recommandé)
  ✓ TLS 1.2 (acceptable)
  ✗ TLS 1.1 (déprécié)
  ✗ TLS 1.0 (déprécié)
  ✗ SSLv3   (cassé - POODLE)
  ✗ SSLv2   (cassé)

Cipher Suites recommandées (TLS 1.2) :
  TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
  TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
  TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
  
  Doivent inclure : ECDHE (PFS) + AES-GCM + SHA-256/384

TLS 1.3 cipher suites (réduites, toutes sécurisées) :
  TLS_AES_256_GCM_SHA384
  TLS_CHACHA20_POLY1305_SHA256
  TLS_AES_128_GCM_SHA256

Configuration Apache :
  SSLProtocol TLSv1.2 TLSv1.3
  SSLCipherSuite HIGH:!aNULL:!MD5:!RC4:!DHE
  SSLHonorCipherOrder On
  SSLCompression Off        ← Désactiver (CRIME)
  SSLSessionTickets Off     ← Pour PFS strict
```

---

## 3. Gestion des Clés

```
Principes fondamentaux :

1. Ne jamais implémenter sa propre cryptographie
   → Utiliser des bibliothèques validées : OpenSSL, libsodium, BouncyCastle
   → "Don't roll your own crypto"

2. Séparation des clés et des données
   → Stocker les clés dans un HSM ou un Key Vault
   → AWS KMS, Azure Key Vault, HashiCorp Vault

3. Rotation des clés
   → Clés de chiffrement : rotation annuelle ou après incident
   → Certificats TLS : expiration 1 an (Let's Encrypt : 90 jours)
   
4. Destruction sécurisée
   → Écrasement sécurisé (multiples passes) ou destruction physique
   
5. Accès aux clés
   → Moindre privilège
   → Audit de tous les accès
   → HSM : les clés ne quittent jamais le matériel
```

---

## 4. HSTS et Headers TLS

```
HSTS (HTTP Strict Transport Security) :
  Header : Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
  
  Effet :
  1. Navigateur mémorise que le site DOIT utiliser HTTPS
  2. Refus silencieux des connexions HTTP pour 1 an (31536000 secondes)
  3. Protection contre SSL Stripping

Certificate Pinning (pour les apps, pas les navigateurs) :
  → Hardcoder l'empreinte SHA-256 du certificat dans l'app
  → Refuser toute connexion avec un certificat différent
  → Utilisé en iOS/Android
  
Certificate Transparency :
  Activer dans nginx/Apache :
  add_header Expect-CT "max-age=86400, enforce";
```

---

## 5. Post-Quantum Cryptography

```
Menace quantique :
  Algorithme de Shor (1994) : Factoriser RSA et résoudre DH/ECC en temps polynomial
  → Sur un ordinateur quantique suffisamment grand : RSA-4096 cassé en heures !
  
AES résiste mieux :
  AES-128 → Sécurité ~64 bits contre ordinateur quantique
  AES-256 → Sécurité ~128 bits → Toujours sécurisé
  
Algorithmes Post-Quantiques (NIST standardisation 2024) :
  CRYSTALS-Kyber : Chiffrement et échange de clé
  CRYSTALS-Dilithium : Signatures numériques
  FALCON : Signatures numériques (plus compact)
  SPHINCS+ : Signatures basées sur hash (conservative)
  
Migration :
  → Hybride : Utiliser à la fois RSA/ECC et algorithmes PQ
  → TLS 1.3 avec extensions PQ (en cours de standardisation)
```

---

## Matrice Résumée

| Besoin | Algorithme recommandé 2024 |
|--------|---------------------------|
| Chiffrer des données | AES-256-GCM |
| Hacher des données | SHA-256 |
| Stocker un MDP | Argon2id |
| Signature numérique | ECDSA P-256 / Ed25519 |
| Échange de clé | ECDH / ECDHE |
| Protocole transport | TLS 1.3 |
| Stocker des clés | HSM / AWS KMS |

---


## 🧠 Points clés à retenir pour l'examen

- **AES-256-GCM** = standard actuel recommandé (chiffrement + authentification)
- **TLS 1.3** = version TLS recommandée (TLS 1.0/1.1 dépréciés, SSLv3 cassé)
- **ECB** = mode dangereux (patterns visibles dans le ciphertext)
- **Argon2id** = algorithme recommandé pour le stockage des mots de passe (2024)
- **"Don't roll your own crypto"** = ne jamais implémenter sa propre cryptographie
- **HSM** = Hardware Security Module (les clés ne quittent jamais le matériel)
- **HSTS** = force HTTPS via header HTTP, protège contre SSL Stripping
- **CRYSTALS-Kyber/Dilithium** = algorithmes post-quantiques standardisés NIST 2024
- **DHE/ECDHE** = algorithmes avec **Perfect Forward Secrecy** (PFS)

*⬅️ [PKI et Certificats](./04_pki_et_certificats.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
