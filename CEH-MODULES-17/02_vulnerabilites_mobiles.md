# 🔴 Module 17 — Vulnérabilités Mobiles : OWASP Mobile Top 10

> **CEH v13 | EC-Council**  
> `#OWASPMobile` `#InsecureStorage` `#WeakAuth` `#InsecureCommunication`

---
## 📌 Table des matières

1. [OWASP Mobile Top 10 (2024)](#owasp-mobile-top-10-2024)
2. [M1 — Improper Credential Usage](#m1--improper-credential-usage)
3. [M2 — Inadequate Supply Chain Security](#m2--inadequate-supply-chain-security)
4. [M3 — Insecure Authentication/Authorization](#m3--insecure-authenticationauthorization)
5. [M4 — Insufficient Input/Output Validation](#m4--insufficient-inputoutput-validation)
6. [M5 — Insecure Communication](#m5--insecure-communication)
7. [M6 — Inadequate Privacy Controls](#m6--inadequate-privacy-controls)
8. [M7 — Insufficient Binary Protections](#m7--insufficient-binary-protections)
9. [M8 — Security Misconfiguration](#m8--security-misconfiguration)
10. [M9 — Insecure Data Storage](#m9--insecure-data-storage)
11. [M10 — Insufficient Cryptography](#m10--insufficient-cryptography)

---


## OWASP Mobile Top 10 (2024)

---

## M1 — Improper Credential Usage

```
Description : Hardcoding de credentials, stockage non sécurisé des credentials

Exemples :
  - API key dans le code source (strings.xml, code Java)
  - Mot de passe en clair dans SharedPreferences
  - Token d'authentification dans les logs
  
Détection :
  grep -r "password" /app/src/           # Recherche dans le code
  strings app.apk | grep -i "api_key"    # Extraction de strings
  
Contre-mesure :
  - Android Keystore pour les credentials
  - Variables d'environnement (pas de hardcode)
  - Chiffrement des SharedPreferences
```

## M2 — Inadequate Supply Chain Security

```
Description : Composants tiers vulnérables, libraries compromises

Exemples :
  - SDK publicitaire avec spyware intégré
  - Bibliothèques avec CVEs connues
  - Outils de build compromis (CI/CD)
  
Contre-mesure :
  - Audit des dépendances (OWASP Dependency-Check)
  - Vérifier les hashs des bibliothèques
  - SBOM (Software Bill of Materials)
```

## M3 — Insecure Authentication/Authorization

```
Description : Authentification faible, bypass de contrôles d'accès

Exemples :
  - PIN/pattern facile à deviner
  - Token d'authentification prévisible
  - Vérification des droits côté client seulement
  
Test :
  adb shell am start -n com.app/.AdminActivity  # Bypass d'intention
  # Si l'activité admin s'ouvre sans auth = vulnérable !
  
Contre-mesure :
  - Authentification forte (biométrie + PIN)
  - Vérification côté serveur obligatoire
  - Token aléatoire 128+ bits
```

## M4 — Insufficient Input/Output Validation

```
Description : SQLi côté mobile, XSS dans WebView, injection de commandes

Exemples :
  - SQLi dans la base de données locale (SQLite)
  - XSS dans un WebView Android
  - Injection via Intents
  
WebView vulnérable :
  webView.loadUrl("javascript:" + userInput);  // XSS !
  
Contre-mesure :
  - Prepared statements pour SQLite
  - Désactiver JavaScript dans WebView si non nécessaire
  - Validation des Intents
```

## M5 — Insecure Communication

```
Description : HTTP non chiffré, SSL non validé, Man-in-the-Middle

Exemples :
  - API calls en HTTP (pas HTTPS)
  - Trust ALL certificates (code qui accepte tous les certificats)
  - TLS 1.0/1.1 encore utilisé
  
Code vulnérable (Android) :
  // Accepter TOUS les certificats = MITM trivial !
  TrustManager[] trustAllCerts = new TrustManager[]{
    new X509TrustManager() {
      public void checkClientTrusted(X509Certificate[] chain, String authType) {}
      public void checkServerTrusted(X509Certificate[] chain, String authType) {}
      ...
    }
  };
  
Contre-mesure :
  - HTTPS obligatoire
  - Certificate pinning
  - Network Security Config (Android)
  - App Transport Security (iOS)
```

## M6 — Inadequate Privacy Controls

```
Description : Collecte excessive de données, permissions inutiles

Exemples :
  - App lampe torche demandant l'accès aux contacts
  - Géolocalisation envoyée à des serveurs tiers
  - Logs contenant des données personnelles (PII)
  
Contre-mesure :
  - Principe de minimisation des données (RGPD)
  - Auditer les permissions demandées
  - Privacy by Design
```

## M7 — Insufficient Binary Protections

```
Description : App facilement décompilée, pas d'obfuscation, debug actif

Exemples :
  - APK décompilable avec Apktool → code lisible
  - Pas d'obfuscation (ProGuard) → noms de classes clairs
  - Debug mode laissé actif en production
  - Pas de root/jailbreak detection
  
Test :
  apktool d app.apk -o decompiled/
  jadx-gui app.apk          # Décompilation complète
  
Contre-mesure :
  - ProGuard / R8 (obfuscation Android)
  - Anti-tampering checks
  - Root/jailbreak detection
  - Android SafetyNet / Play Integrity API
```

## M8 — Security Misconfiguration

```
Description : Debug mode, permissions excessives, configurations par défaut

Exemples :
  - android:debuggable="true" en production
  - android:allowBackup="true" → backup extractable via ADB
  - Exported activities sans permissions
  
Test :
  adb backup com.target.app   # Si allowBackup=true → extraction données !
  
Contre-mesure :
  - Désactiver debuggable et allowBackup en production
  - Restreindre les composants exportés
```

## M9 — Insecure Data Storage

```
Description : Données sensibles stockées non chiffrées

Lieux de stockage risqués :
  - SharedPreferences (XML en clair)
  - SQLite databases (souvent non chiffrées)
  - External storage (/sdcard/) accessible à toutes les apps
  - Logs (logcat lisible si USB debug activé)
  - Clipboard (partagé entre apps)
  
Test :
  adb pull /data/data/com.app/shared_prefs/  # Si root
  adb pull /sdcard/com.app/                 # Toujours accessible
  adb logcat | grep -i password             # Chercher dans les logs
  
Contre-mesure :
  - EncryptedSharedPreferences (Android Jetpack Security)
  - SQLCipher pour SQLite chiffré
  - Ne jamais stocker sur External Storage
```

## M10 — Insufficient Cryptography

```
Description : Algorithmes faibles, mauvaise implémentation

Exemples :
  - MD5 ou SHA-1 pour les mots de passe
  - AES en mode ECB (révèle les patterns)
  - Clés hardcodées dans le code
  - Nombre aléatoire non cryptographique
  
Contre-mesure :
  - AES-256-GCM pour le chiffrement
  - SHA-256+ pour le hachage
  - Android Keystore pour les clés
  - SecureRandom pour les nombres aléatoires
```

---

## 🧠 Points clés à retenir pour l'examen

| # | Catégorie Mobile | Exemple clé |
|---|-----------------|-------------|
| M1 | Credential Usage | API key hardcodée |
| M5 | Insecure Communication | Trust All Certs = MITM |
| M7 | Binary Protections | APK décompilable |
| M8 | Misconfiguration | allowBackup=true, debuggable=true |
| M9 | Insecure Storage | Données en clair sur /sdcard/ |
| M10 | Cryptography | MD5 pour mots de passe |

---

*⬅️ [Concepts fondamentaux](./01_concepts_fondamentaux.md) | ➡️ [Techniques d'attaques](./03_techniques_attaques.md)*
