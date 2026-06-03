# 📝 Module 20 — Quiz & Révision : Cryptography

> **CEH v13 | EC-Council**  
> `#Quiz` `#Cryptography` `#AES` `#RSA` `#PKI` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- **Symétrique** (AES) = même clé, rapide ; **Asymétrique** (RSA, ECC) = paire clé pub/priv, lent
- **Hybride** = TLS utilise asymétrique pour échanger une clé AES symétrique
- **MD5/SHA-1** = cassés ; **SHA-256** = standard actuel pour le hachage
- **bcrypt/Argon2** = pour les mots de passe (lents par conception)
- **Rainbow tables** = contrecarrées par le salting
- **PKI** = Root CA → Intermediate CA → Certificats
- **TLS 1.3** = plus rapide, PFS obligatoire, cipher suites modernes

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **Symétrique** | Même clé pour chiffrer et déchiffrer |
| **Asymétrique** | Paire clé publique + clé privée |
| **IV/Nonce** | Valeur aléatoire évitant la répétition dans le chiffrement |
| **Salt** | Valeur aléatoire ajoutée au mot de passe avant hachage |
| **Birthday attack** | Exploiter la probabilité de collisions dans les hash |
| **POODLE** | Attaque sur SSLv3 CBC — désactiver SSLv3 |
| **HEARTBLEED** | CVE-2014-0160, fuite mémoire OpenSSL via Heartbeat |
| **PKI** | Infrastructure de gestion des certificats numériques |
| **OCSP** | Vérification en temps réel de la révocation d'un certificat |
| **PFS** | Perfect Forward Secrecy — DHE/ECDHE, compromission clé ≠ passé déchiffré |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **AES** = algorithme symétrique standard (pas DES/3DES qui sont obsolètes)
2. **RSA** = asymétrique, basé sur la factorisation
3. **Diffie-Hellman** = échange de clé (vulnérable au MITM sans authentification)
4. **MD5** = cassé (collisions), ne pas utiliser pour la sécurité
5. **SHA-1** = cassé depuis 2017 (SHAttered), utiliser SHA-256+
6. **bcrypt** = pour les mots de passe (lent, avec salt intégré)
7. **Rainbow tables** = contrecarrées par le **salting**
8. **POODLE** = SSLv3 → désactiver SSLv3
9. **HEARTBLEED** = OpenSSL 1.0.1 → patcher
10. **OCSP** vs **CRL** : OCSP = temps réel ; CRL = liste périodique
11. **TLS 1.3** = 1 RTT, ECDHE obligatoire, pas de RC4/DES

---

## ❓ Questions de révision

1. Quelle est la différence entre chiffrement symétrique et asymétrique ?
2. Expliquez le paradoxe des anniversaires et son application en cryptographie.
3. Pourquoi ne faut-il pas utiliser MD5 pour stocker des mots de passe ?
4. Qu'est-ce que le salting et pourquoi contrecarre-t-il les rainbow tables ?
5. Expliquez l'attaque POODLE et sa contre-mesure.
6. Quelle est la différence entre CRL et OCSP pour la révocation de certificats ?
7. Pourquoi TLS 1.3 est-il préférable à TLS 1.2 ?
8. Qu'est-ce que le Perfect Forward Secrecy (PFS) ?

---

## 📋 Quiz QCM

**Question 1.** L'algorithme AES appartient à quelle catégorie ?
- a) Asymétrique
- b) Symétrique
- c) Hachage
- d) Échange de clé

**Question 2.** Quel algorithme de hachage est cassé à cause de collisions démontrées en 2017 ?
- a) SHA-256
- b) SHA-512
- c) SHA-1
- d) SHA-3

**Question 3.** La principale protection contre les rainbow tables est :
- a) L'utilisation de SHA-256 au lieu de MD5
- b) Le salting (ajout d'une valeur aléatoire avant le hachage)
- c) L'utilisation d'AES pour hacher les mots de passe
- d) Un mot de passe plus long

**Question 4.** POODLE est une attaque ciblant :
- a) TLS 1.3
- b) SSLv3 avec mode CBC
- c) SHA-1
- d) RSA-1024

**Question 5.** OCSP est utilisé pour :
- a) Chiffrer les communications TLS
- b) Vérifier en temps réel si un certificat est révoqué
- c) Générer des paires de clés RSA
- d) Distribuer les certificats racine

**Question 6.** RSA est basé sur :
- a) La résolution du problème du logarithme discret
- b) La difficulté de factoriser le produit de deux grands nombres premiers
- c) Les propriétés des courbes elliptiques
- d) La permutation de S-boxes

**Question 7.** Quel mode AES assure à la fois la confidentialité ET l'intégrité ?
- a) ECB
- b) CBC
- c) CTR
- d) GCM

**Question 8.** Pour stocker des mots de passe en 2024, quel algorithme est le plus recommandé ?
- a) SHA-256 avec sel
- b) MD5 avec sel
- c) Argon2id
- d) bcrypt avec rounds=5

---

## ✅ Points de révision — Checklist

- [ ] Je comprends la différence symétrique / asymétrique / hybride
- [ ] Je connais les algorithmes modernes : AES, RSA, ECC, SHA-256
- [ ] Je sais pourquoi MD5 et SHA-1 sont cassés
- [ ] Je comprends le birthday attack et le salting
- [ ] Je connais les attaques TLS/SSL : POODLE, HEARTBLEED, BEAST
- [ ] Je comprends la PKI (Root CA, Intermediate CA, CSR)
- [ ] Je sais ce qu'est la révocation (CRL vs OCSP)
- [ ] Je comprends le TLS handshake et le PFS
- [ ] Je connais les bonnes pratiques (algorithmes recommandés 2024)
- [ ] Je comprends la menace quantique et les algorithmes post-quantiques

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | AES = chiffrement symétrique (même clé) |
| 2 | **c** | SHA-1 = SHAttered (collision 2017, Google/CWI) |
| 3 | **b** | Salting rend les rainbow tables pré-calculées inutiles |
| 4 | **b** | POODLE = SSLv3 + CBC padding oracle |
| 5 | **b** | OCSP = vérification temps réel révocation |
| 6 | **b** | RSA = factorisation de n=p×q |
| 7 | **d** | GCM = CTR + GMAC (confidentialité + intégrité) |
| 8 | **c** | Argon2id = NIST recommandé 2024, résistant GPU |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 20](./README.md)*
