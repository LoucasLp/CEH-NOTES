# 🔴 Module 17 — Concepts Fondamentaux : Plateformes Mobiles

> **CEH v13 | EC-Council**  
> `#Android` `#iOS` `#Sandboxing` `#AppSigning` `#MobileSecurityModel`

---

## 📌 Table des matières

1. [Architecture Android](#1-architecture-android)
2. [Architecture iOS](#2-architecture-ios)
3. [Modèle de sécurité Android vs iOS](#3-comparatif-sécurité)
4. [Surface d'attaque mobile](#4-surface-dattaque)

---

## 1. Architecture Android

```
┌─────────────────────────────────────────────────────────────┐
│                   APPLICATIONS                               │
│  [App 1] [App 2] [App 3] [System Apps]                      │
├─────────────────────────────────────────────────────────────┤
│               ANDROID FRAMEWORK                             │
│  Activity Manager | Content Providers | Views | Telephony   │
├─────────────────────────────────────────────────────────────┤
│                ANDROID RUNTIME (ART)                        │
│  Dalvik VM → ART | Core Libraries                           │
├─────────────────────────────────────────────────────────────┤
│              NATIVE LIBRARIES (C/C++)                       │
│  WebKit | SQLite | OpenGL | Media Framework                 │
├─────────────────────────────────────────────────────────────┤
│          HARDWARE ABSTRACTION LAYER (HAL)                   │
├─────────────────────────────────────────────────────────────┤
│           LINUX KERNEL                                      │
│  Drivers | WiFi | Bluetooth | USB | Camera | Audio         │
└─────────────────────────────────────────────────────────────┘
```

### Sécurité Android

```
Isolation des applications :
  - Chaque app = UID Linux unique
  - Sandboxing via SELinux
  - Accès aux ressources via permissions explicites

Permissions Android :
  NORMAL    : Accordées automatiquement (INTERNET, ACCESS_WIFI_STATE)
  DANGEROUS : Demande à l'utilisateur (CAMERA, CONTACTS, LOCATION, SMS)
  SIGNATURE : Seulement apps du même développeur
  
Stockage :
  Internal Storage : /data/data/com.app.name/   ← Privé (UID)
  External Storage : /sdcard/                   ← Accessible à tous !
  
App Signing (APK) :
  → L'APK doit être signé avec un certificat développeur
  → Utilisé pour les mises à jour (même signature = même app)
  → Pas de PKI centralisée (auto-signé possible sur Android)
```

---

## 2. Architecture iOS

```
┌─────────────────────────────────────────────────────────────┐
│                   COCOA TOUCH                               │
│  UIKit | Foundation | CoreData | EventKit                   │
├─────────────────────────────────────────────────────────────┤
│                     MEDIA                                   │
│  CoreGraphics | CoreAudio | AVFoundation | CoreImage        │
├─────────────────────────────────────────────────────────────┤
│                  CORE SERVICES                              │
│  CFNetwork | CoreLocation | CoreBluetooth | iCloud          │
├─────────────────────────────────────────────────────────────┤
│                   CORE OS (Darwin)                          │
│  Security Framework | BSD Sockets | Accelerate              │
├─────────────────────────────────────────────────────────────┤
│                     XNU KERNEL                              │
│  (Mach + BSD hybrid) | Drivers | File System                │
└─────────────────────────────────────────────────────────────┘
```

### Sécurité iOS

```
Sécurité Apple :
  - Secure Enclave : processeur dédié aux clés cryptographiques
  - App Store Review : vérification des apps avant publication
  - Code Signing : TOUTES les apps doivent être signées par Apple
  - Sandboxing : chaque app isolée, accès limité au système
  
Keychain :
  → Stockage sécurisé des secrets (mots de passe, certificats)
  → Chiffré par le Secure Enclave
  → Persistant entre reinstallations
  
Data Protection :
  → Fichiers protégés par classes de chiffrement
  → Clés dérivées du code PIN de l'utilisateur
```

---

## 3. Comparatif Sécurité Android vs iOS

| Critère | Android | iOS |
|---------|---------|-----|
| **Sources d'apps** | Play Store + APK tiers | App Store uniquement |
| **Code signing** | Auto-signé possible | Signé par Apple obligatoire |
| **Jailbreak/Root** | Root Android courant | Jailbreak plus difficile |
| **Permissions** | Demandées par l'utilisateur | Demandées + revue Apple |
| **Isolation** | SELinux + UID | Sandbox + entitlements |
| **Chiffrement** | Android 6.0+ obligatoire | Depuis iPhone 3GS |
| **Mises à jour** | Fragmentées (constructeurs) | Centralisées (Apple) |
| **Surface d'attaque** | Plus large (sideloading) | Plus restreinte |

---

## 4. Surface d'Attaque Mobile

```
┌─────────────────────────────────────────────────────────────┐
│               SURFACE D'ATTAQUE MOBILE                      │
├──────────────────┬──────────────────────────────────────────┤
│ Communications   │ Wi-Fi, Bluetooth, NFC, SMS, appels       │
│ Applications     │ APK malveillants, vulnérabilités code     │
│ Stockage         │ External storage, fichiers non chiffrés  │
│ Réseau           │ SSL stripping, Evil Twin, Proxy           │
│ Matériel         │ Accès physique, extraction forensique     │
│ Système          │ Root/Jailbreak, patches manquants         │
└──────────────────┴──────────────────────────────────────────┘
```

---

## 🧠 Points clés à retenir pour l'examen

- Android = basé sur Linux, apps isolées par UID SELinux
- iOS = XNU kernel, Secure Enclave, apps signées par Apple obligatoire
- Android permet le **sideloading** (APK tiers) → surface d'attaque plus large
- iOS App Store Review = barrière supplémentaire de sécurité
- Jailbreak iOS / Root Android = suppriment les protections de sécurité

---

*⬅️ [README](./README.md) | ➡️ [Vulnérabilités mobiles](./02_vulnerabilites_mobiles.md)*
