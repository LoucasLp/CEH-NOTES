# 📝 Module 17 — Quiz & Révision : Hacking Mobile Platforms

> **CEH v13 | EC-Council**  
> `#Quiz` `#Mobile` `#OWASPMobile` `#ADB` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- Android = basé sur Linux, sandboxing SELinux, APK sideloading possible
- iOS = Secure Enclave, signature Apple obligatoire, plus restrictif
- OWASP Mobile Top 10 : M1 (credentials), M5 (communication), M7 (binary), M9 (storage)
- ADB = outil officiel Android pour les tests et l'exploitation
- MDM = solution de gestion des appareils mobiles en entreprise
- Certificate pinning = protection contre les proxy HTTPS (Burp Suite)

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **ADB** | Android Debug Bridge — interface USB pour interagir avec Android |
| **Apktool** | Décompilation des ressources APK (smali, XML) |
| **jadx** | Décompilation APK en Java lisible |
| **Frida** | Framework d'instrumentation dynamique |
| **MobSF** | Analyse statique et dynamique d'applications mobiles |
| **MDM** | Mobile Device Management — gestion et sécurité d'appareils |
| **Certificate Pinning** | Vérifier l'empreinte du certificat côté application |
| **Smishing** | Phishing par SMS |
| **MitMo** | Man-in-the-Mobile — malware interceptant les SMS 2FA |
| **SS7** | Protocole télécoms vulnérable → interception SMS |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **ADB** = outil de référence pour les tests Android
2. **`adb shell`** = accès shell sur l'appareil
3. **`android:allowBackup="false"`** = empêche l'extraction via ADB
4. **`android:debuggable="false"`** = désactiver en production
5. **Apktool** = décompiler les ressources ; **jadx** = décompiler en Java
6. **MDM** = remote wipe, enforcement de politiques
7. **Certificate pinning** = contre-mesure contre les proxy HTTPS
8. **M9 Insecure Data Storage** = données sensibles sur /sdcard/ ou SharedPreferences
9. **Smishing** = phishing SMS ; **MitMo** = interception SMS par malware
10. **SS7** = faille telcos permettant l'interception des SMS (2FA)

---

## ❓ Questions de révision

1. Quelle est la différence fondamentale entre Android et iOS en termes de sources d'applications ?
2. Comment ADB peut-il être utilisé pour extraire des données d'un appareil Android ?
3. Expliquez la vulnérabilité M9 de l'OWASP Mobile Top 10.
4. Qu'est-ce que le certificate pinning et contre quelle attaque protège-t-il ?
5. Comment Frida est-il utilisé pour bypasser la détection de root ?
6. Pourquoi `allowBackup="true"` est-il dangereux ?
7. Quelle est la différence entre Smishing et MitMo ?
8. Qu'est-ce que le MDM et quelles fonctionnalités apporte-t-il ?

---

## 📋 Quiz QCM

**Question 1.** Quel outil permet de décompiler un APK Android en code Java lisible ?
- a) Apktool
- b) jadx
- c) Frida
- d) ADB

**Question 2.** La catégorie M9 de l'OWASP Mobile Top 10 correspond à :
- a) Authentification insuffisante
- b) Stockage insécurisé des données
- c) Communications non sécurisées
- d) Utilisation inappropriée des credentials

**Question 3.** `adb pull /sdcard/` permet de :
- a) Installer une APK sur l'appareil
- b) Récupérer les fichiers du stockage externe
- c) Accéder au shell de l'appareil
- d) Capturer les logs de l'application

**Question 4.** Le certificate pinning protège principalement contre :
- a) Les attaques XSS mobiles
- b) Le vol de cookies de session
- c) Les attaques Man-in-the-Middle via proxy (Burp Suite)
- d) Les malwares bancaires

**Question 5.** La directive `android:allowBackup="false"` empêche :
- a) L'installation d'apps tierces
- b) L'extraction de données via `adb backup`
- c) L'accès aux permissions dangereuses
- d) Le root de l'appareil

**Question 6.** MobSF est principalement utilisé pour :
- a) Le brute force de PIN Android
- b) L'analyse statique et dynamique d'applications mobiles
- c) La capture de trafic réseau mobile
- d) L'exploitation de vulnérabilités SS7

**Question 7.** Un attaquant capture les SMS d'OTP de sa victime sans accès physique à l'appareil. Quelle technique utilise-t-il probablement ?
- a) Smishing
- b) Bluesnarfing
- c) SS7 attack
- d) Evil Twin

**Question 8.** Le MDM (Mobile Device Management) permet principalement :
- a) De scanner les vulnérabilités des apps mobiles
- b) De gérer les appareils mobiles d'entreprise (remote wipe, politiques)
- c) De décompiler les APK Android
- d) D'intercepter le trafic HTTPS mobile

---

## ✅ Points de révision — Checklist

- [ ] Je connais la différence Android (sideloading possible) vs iOS (App Store only)
- [ ] Je comprends les 10 catégories OWASP Mobile Top 10
- [ ] Je sais utiliser ADB (shell, pull, logcat, backup)
- [ ] Je connais la différence Apktool (ressources) vs jadx (Java)
- [ ] Je comprends ce qu'est le certificate pinning et son utilité
- [ ] Je sais ce qu'est le MDM et ses fonctionnalités
- [ ] Je comprends les attaques SS7 et MitMo
- [ ] Je connais les directives de sécurité AndroidManifest.xml

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | jadx = décompilation Java ; Apktool = smali + ressources |
| 2 | **b** | M9 = Insecure Data Storage |
| 3 | **b** | /sdcard/ = stockage externe accessible |
| 4 | **c** | Certificate pinning bloque les proxies non autorisés |
| 5 | **b** | allowBackup=false empêche `adb backup` |
| 6 | **b** | MobSF = analyse statique + dynamique d'apps |
| 7 | **c** | SS7 = interception SMS via réseau télécoms |
| 8 | **b** | MDM = gestion d'appareils mobiles d'entreprise |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 17](./README.md)*
