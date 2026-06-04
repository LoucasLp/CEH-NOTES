# 🔴 Module 17 — Outils & Labs : Mobile Hacking

> **CEH v13 | EC-Council**  
> `#MobSF` `#ADB` `#Apktool` `#Frida` `#Drozer` `#BurpMobile`

---
## 📌 Table des matières

1. [MobSF — Mobile Security Framework](#1-mobsf--mobile-security-framework)
2. [ADB — Android Debug Bridge](#2-adb--android-debug-bridge)
3. [Apktool & jadx](#3-apktool--jadx)
4. [Frida — Dynamic Instrumentation](#4-frida--dynamic-instrumentation)
5. [Drozer — Exploitation Android](#5-drozer--exploitation-android)

---


## 1. MobSF — Mobile Security Framework

```bash
# Installation via Docker :
docker pull opensecurity/mobile-security-framework-mobsf
docker run -it --rm -p 8000:8000 opensecurity/mobile-security-framework-mobsf

# Interface web : http://localhost:8000
# Upload APK → Analyse statique automatique

# Ce que MobSF analyse :
# - Permissions déclarées dans AndroidManifest.xml
# - Composants exportés (Activities, Services, Receivers)
# - Hardcoded secrets dans le code
# - API dangereuses utilisées
# - Bibliothèques vulnérables
# - Score de sécurité global

# Analyse dynamique :
# → Connecter un émulateur ou appareil physique
# → MobSF instrumentalise l'app et capture le trafic réseau
```

---

## 2. ADB — Android Debug Bridge

```bash
# Prérequis : USB Debug activé sur l'appareil (Paramètres → Options développeur)

# ─── CONNEXION ────────────────────────────────────────────────
adb devices                           # Lister appareils
adb connect 192.168.1.100:5555        # Via réseau (si ADB over TCP)

# ─── FICHIERS ─────────────────────────────────────────────────
adb push local_file.apk /sdcard/      # Envoyer un fichier
adb pull /sdcard/DCIM/ /tmp/photos/   # Récupérer des fichiers
adb install app.apk                   # Installer une APK
adb uninstall com.app.package         # Désinstaller

# ─── SHELL ────────────────────────────────────────────────────
adb shell                             # Shell interactif
adb shell pm list packages            # Lister les packages
adb shell am start -a android.intent.action.VIEW  # Lancer intention
adb shell dumpsys                     # Informations système
adb shell getprop ro.build.version.release  # Version Android
adb shell cat /proc/version           # Kernel

# ─── LOGS ─────────────────────────────────────────────────────
adb logcat                            # Tous les logs
adb logcat -s "MyApp"                 # Logs filtrés par tag
adb logcat | grep -i "password\|token\|key"  # Chercher des secrets

# ─── BACKUP ───────────────────────────────────────────────────
adb backup -apk -shared -all -f backup.ab  # Backup complet
dd if=backup.ab bs=1 skip=24 | python -c "import zlib,sys; sys.stdout.buffer.write(zlib.decompress(sys.stdin.buffer.read()))" > backup.tar
```

---

## 3. Apktool & jadx

```bash
# ─── APKTOOL ──────────────────────────────────────────────────
# Décompiler :
apktool d app.apk -o ./decompiled/
# Contenu extrait :
# - AndroidManifest.xml (permissions, composants)
# - res/ (ressources, strings.xml)
# - smali/ (bytecode Dalvik lisible)

# Recompiler (après modification) :
apktool b ./decompiled/ -o modified.apk

# Aligner et signer :
zipalign -v 4 modified.apk aligned.apk
apksigner sign --ks my.keystore aligned.apk

# ─── JADX ─────────────────────────────────────────────────────
# Décompilation en Java :
jadx -d output/ app.apk

# Chercher des secrets :
grep -r "api_key\|password\|secret" output/
grep -r "http://" output/      # URLs HTTP non sécurisées

# Interface graphique :
jadx-gui app.apk
```

---

## 4. Frida — Dynamic Instrumentation

```javascript
// Frida permet de modifier le comportement de l'app en temps réel

// Hooker la fonction de vérification de root :
Java.perform(function() {
  var RootBeer = Java.use("com.scottyab.rootbeer.RootBeer");
  RootBeer.isRooted.implementation = function() {
    console.log("[+] isRooted() appelé, retourne false");
    return false;  // Bypass de la détection root !
  };
});

// Bypass du certificate pinning :
// (hooker javax.net.ssl.X509TrustManager)
Java.perform(function() {
  var SSLContext = Java.use("javax.net.ssl.SSLContext");
  SSLContext.init.overload(...).implementation = function(km, tm, sr) {
    // Remplacer par un TrustManager qui accepte tout
  };
});
```

```bash
# Utilisation Frida :
frida-ps -U                              # Lister les processus (USB)
frida -U -l script.js com.target.app     # Injecter un script
frida -U -l objection.js --pause com.target.app

# objection (framework basé sur Frida) :
objection -g com.target.app explore
# Dans objection :
android sslpinning disable          # Bypass certificate pinning
android root disable                # Bypass root detection
android clipboard monitor           # Surveiller le presse-papier
```

---

## 5. Drozer — Exploitation Android

```bash
# Installer l'agent Drozer sur l'appareil :
adb install drozer-agent.apk

# Connexion :
adb forward tcp:31415 tcp:31415
drozer console connect

# Dans la console Drozer :
dz> list                             # Modules disponibles
dz> run app.package.list             # Lister les packages
dz> run app.package.info -a com.target.app   # Infos sur l'app

# Activités exportées (bypass auth) :
dz> run app.activity.info -a com.target.app
dz> run app.activity.start --component com.target.app com.target.app.AdminActivity

# Content Providers (bases de données exposées) :
dz> run app.provider.info -a com.target.app
dz> run app.provider.query content://com.target.app.provider/users

# Services exportés :
dz> run app.service.info -a com.target.app
```

---

## 🧠 Points clés à retenir pour l'examen

- **MobSF** = analyse statique et dynamique d'APK (interface web)
- **ADB** = outil officiel Android pour tests (shell, logs, files)
- **Apktool** = décompiler ressources + smali
- **jadx** = décompiler en Java lisible
- **Frida** = instrumentation dynamique (bypass root detection, SSL pinning)
- **Drozer** = exploitation des composants Android exportés

---

*⬅️ [Techniques d'attaques](./03_techniques_attaques.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
