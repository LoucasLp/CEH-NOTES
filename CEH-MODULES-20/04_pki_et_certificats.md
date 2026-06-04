# 🔴 Module 20 — PKI et Certificats Numériques

> **CEH v13 | EC-Council**  
> `#PKI` `#X509` `#CA` `#TLS` `#CRL` `#OCSP` `#TLSHandshake`

---

## 📌 Table des matières

1. [Infrastructure PKI](#1-infrastructure-pki)
2. [Certificat X.509](#2-certificat-x509)
3. [Processus de Demande (CSR)](#3-processus-de-demande-csr)
4. [Révocation : CRL et OCSP](#4-révocation--crl-et-ocsp)
5. [TLS Handshake](#5-tls-handshake)
6. [Certificate Transparency](#6-certificate-transparency)

---

## 1. Infrastructure PKI

```
PKI (Public Key Infrastructure) = ensemble de technologies et processus
pour créer, gérer et révoquer des certificats numériques

Architecture hiérarchique :

ROOT CA (Certificate Authority)
  → Auto-signé, ultra-sécurisé, souvent hors ligne
  └── INTERMEDIATE CA
        → Signé par Root CA
        └── ISSUING CA / End Entity CA
              → Émet les certificats des serveurs, utilisateurs
              └── [Certificat site.com]
                  [Certificat user@company.com]
```

---

## 2. Certificat X.509

```
Structure d'un certificat X.509 :

Champs principaux :
  Version       : v3 (actuel)
  Serial Number : Unique par CA
  Issuer        : Qui a émis le certificat (CA)
  Subject       : Pour qui (CN=site.com, O=Company, C=FR)
  SAN           : Subject Alternative Names (noms DNS supplémentaires)
  Validity      : Not Before + Not After (dates de validité)
  Public Key    : Clé publique du serveur
  Signature     : Signature de la CA

Extensions importantes :
  KeyUsage         : Utilisation autorisée (signature, chiffrement)
  ExtendedKeyUsage : TLS Web Server Authentication, Code Signing
  SAN              : DNS:site.com, DNS:www.site.com, DNS:*.site.com
  
Visualiser un certificat :
  openssl x509 -in cert.pem -text -noout
```

---

## 3. Processus de Demande (CSR)

```
CSR = Certificate Signing Request

Étapes :
1. Serveur génère une paire de clés :
   openssl genrsa -out private.key 2048

2. Serveur crée un CSR :
   openssl req -new -key private.key -out request.csr
   → Contient : clé publique + informations (CN, O, C...)

3. CSR envoyé à la CA

4. CA vérifie l'identité (DV, OV, EV)
   DV (Domain Validation) : Vérifier le contrôle du domaine (email ou DNS)
   OV (Organization Validation) : Vérifier l'organisation
   EV (Extended Validation) : Vérification approfondie (barre verte)

5. CA signe le CSR avec sa clé privée → Certificat X.509

6. Certificat installé sur le serveur
```

---

## 4. Révocation : CRL et OCSP

```
CRL (Certificate Revocation List) :
  → Liste publiée par la CA des certificats révoqués
  → Téléchargée périodiquement par les clients
  → Problème : peut être obsolète (mis à jour toutes les 24h)
  
OCSP (Online Certificate Status Protocol) :
  → Vérification en temps réel du statut d'un certificat
  → Client interroge le serveur OCSP de la CA
  → Réponse : Good, Revoked, Unknown
  → OCSP Stapling : Le serveur inclut la réponse OCSP dans le TLS handshake
  
Raisons de révocation :
  - Clé privée compromise
  - CA compromise
  - Changement d'affiliation
  - Arrêt d'activité
```

---

## 5. TLS Handshake

```
TLS 1.2 Handshake (simplifié) :

Client                                    Serveur
  │──── Client Hello ────────────────────▶│
  │     (TLS version, cipher suites, random_C)
  │
  │◀─── Server Hello ────────────────────│
  │     (cipher suite choisi, random_S)
  │
  │◀─── Certificate ─────────────────────│
  │     (Certificat X.509 du serveur)
  │
  │◀─── Server Key Exchange (optionnel) ─│
  │     (Pour DHE/ECDHE)
  │
  │◀─── Server Hello Done ───────────────│
  │
  │──── Client Key Exchange ─────────────▶│
  │     (Pre-master secret chiffré avec pub key serveur)
  │
  [Les deux parties dérivent la session key = Master Secret]
  
  │──── Finished ────────────────────────▶│  (chiffré)
  │◀─── Finished ────────────────────────│  (chiffré)
  │
  ═══════════ Application Data (chiffré) ═══════════

TLS 1.3 (simplifié) :
  → 1 Round-Trip au lieu de 2 (plus rapide)
  → ECDHE obligatoire (Perfect Forward Secrecy toujours)
  → Moins de cipher suites (seulement les sécurisées)
```

---

## 6. Certificate Transparency

```
CT (Certificate Transparency) :
  → Journaux publics et immuables de tous les certificats émis
  → Permet de détecter les certificats frauduleux
  → Depuis 2018 : Chrome exige que tous les certificats soient dans les CT logs
  
Logs publics : crt.sh, Google Argon, Cloudflare Nimbus

Utilisation en reconnaissance :
  curl https://crt.sh/?q=%.example.com&output=json | jq '.[].name_value'
  → Découvrir tous les sous-domaines d'un domaine !
```

---

## 🧠 Points clés à retenir pour l'examen

- **PKI** = hiérarchie Root CA → Intermediate CA → Certificats
- **X.509** = format standard des certificats numériques
- **CSR** = demande de certificat (contient clé publique + infos)
- **CRL** = liste des certificats révoqués (peut être obsolète)
- **OCSP** = vérification en temps réel de la révocation
- **TLS 1.3** = 1 RTT, PFS obligatoire, cipher suites modernes uniquement
- **Certificate Transparency** = logs publics de tous les certificats (crt.sh)

---

*⬅️ [Attaques cryptographiques](./03_attaques_crypto.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
