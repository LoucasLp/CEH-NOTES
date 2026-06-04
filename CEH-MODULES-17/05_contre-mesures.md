# 🛡️ Module 17 — Contre-mesures : Sécurité Mobile

> **CEH v13 | EC-Council**  
> `#MDM` `#CertificatePinning` `#Chiffrement` `#RootDetection` `#SecureStorage`

---
## 📌 Table des matières

1. [Contre-mesures Essentielles](#contre-mesures-essentielles)
2. [Matrice des Contre-mesures](#matrice-des-contre-mesures)

---


## Contre-mesures Essentielles

### MDM — Mobile Device Management

```
MDM permet aux entreprises de gérer et sécuriser les appareils mobiles.

Fonctionnalités MDM :
  ├── Enforcement des politiques (PIN obligatoire, chiffrement)
  ├── Remote wipe (effacer l'appareil à distance)
  ├── App management (whitelisting, blacklisting)
  ├── VPN d'entreprise automatique
  ├── Séparation données pro/perso (BYOD)
  ├── Inventaire des appareils
  └── Détection de jailbreak/root

Solutions MDM populaires :
  Microsoft Intune, VMware Workspace ONE (AirWatch),
  Jamf (iOS/macOS), IBM MaaS360
```

### Certificate Pinning

```
Empêche les attaques MITM même si l'attaquant a un certificat valide.

Sans pinning :
  App → [Certificat Burp valide accepté] → Attaquant → Serveur
  → Attaquant voit tout !

Avec pinning :
  App compare le certificat reçu avec celui hardcodé
  → Certificat Burp ≠ certificat attendu → Connexion refusée !

Implémentation Android :
  // res/xml/network_security_config.xml :
  <network-security-config>
    <domain-config>
      <domain includeSubdomains="true">api.myapp.com</domain>
      <pin-set>
        <pin digest="SHA-256">base64EncodedSPKI==</pin>
      </pin-set>
    </domain-config>
  </network-security-config>
```

### Chiffrement et Stockage Sécurisé

```
Android Keystore :
  → Clés cryptographiques stockées dans le hardware (si disponible)
  → Inaccessibles depuis l'extérieur même si l'app est compromise
  
EncryptedSharedPreferences (Jetpack Security) :
  SharedPreferences sp = EncryptedSharedPreferences.create(
    "secure_prefs",
    MasterKeys.getOrCreate(MasterKeys.AES256_GCM_SPEC),
    context,
    EncryptedSharedPreferences.PrefKeyEncryptionScheme.AES256_SIV,
    EncryptedSharedPreferences.PrefValueEncryptionScheme.AES256_GCM
  );
  
iOS Keychain :
  → Stockage sécurisé des credentials
  → Protégé par le Secure Enclave
  → Persiste entre réinstallations
```

### Root/Jailbreak Detection

```
Android — RootBeer library :
  new RootBeer(context).isRooted()
  
Vérifications effectuées :
  - Présence de su, busybox
  - Propriétés de build (ro.build.tags = test-keys)
  - Présence de fichiers Magisk, SuperSU
  - Intégrité de la partition système
  
iOS — Jailbreak detection :
  - Présence de /Applications/Cydia.app
  - Écriture hors sandbox possible
  - fork() réussi
  - Présence de fichiers cydia, substrate
  
Limitations :
  → Frida peut bypasser ces détections
  → Utile en deterrence, pas en garantie absolue
```

### Sécurité du Code

```
AndroidManifest.xml sécurisé :
  android:debuggable="false"         ← Production
  android:allowBackup="false"        ← Empêche adb backup
  android:exported="false"           ← Composants privés
  
ProGuard / R8 (obfuscation) :
  → Renomme les classes et méthodes
  → Rend la décompilation moins lisible
  → build.gradle : minifyEnabled true
  
Anti-tampering :
  → Vérifier le hash du certificat de signing
  → Si certificat différent (app repackagée) → arrêter
  SafetyNet / Play Integrity API :
  → Vérifier l'intégrité de l'appareil et de l'app
```

---

## Matrice des Contre-mesures

| Attaque | Contre-mesure principale |
|---------|--------------------------|
| MITM via proxy | Certificate pinning |
| Reverse engineering | Obfuscation (ProGuard) + anti-tamper |
| Données en clair | Android Keystore + EncryptedSharedPreferences |
| App sur appareil rooté | Root/jailbreak detection |
| Malware mobile | MDM + source d'apps contrôlée |
| Backup non autorisé | `allowBackup="false"` |
| Composants exportés | `exported="false"` + permissions |
| SMS phishing | Formation utilisateurs |

---

## 🧠 Points clés à retenir pour l'examen

- **MDM** = gestion des appareils mobiles en entreprise (remote wipe)
- **Certificate pinning** = vérifier l'empreinte du certificat côté app
- **Android Keystore** = stockage sécurisé des clés cryptographiques
- **`allowBackup="false"`** = empêcher l'extraction de données via ADB
- **ProGuard** = obfuscation du code Android (rend la décompilation difficile)
- **Root/Jailbreak detection** = ne garantit pas la sécurité mais dissuade

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
