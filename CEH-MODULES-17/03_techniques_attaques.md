# 🔴 Module 17 — Techniques d'Attaques : Plateformes Mobiles

> **CEH v13 | EC-Council**  
> `#Malware` `#Smishing` `#ReverseEngineering` `#ADB` `#SS7` `#MaliciousApp`

---

## 1. Malware Mobile

```
Types de malware mobile :

Trojan bancaire :
  → Se déguise en application légitime
  → Vol des credentials bancaires
  → Interception des SMS (codes 2FA)
  Ex: Anubis, BankBot, Cerberus

Ransomware mobile :
  → Chiffre les fichiers sur /sdcard/
  → Demande de rançon
  Ex: Android/Filecoder, WannaLocker

Spyware :
  → Collecte silencieuse : SMS, appels, GPS, photos, contacts
  → Souvent dissimulé dans des apps populaires clonées
  
Adware :
  → Affiche des publicités intrusives
  → Peut générer des clics frauduleux

Distribution :
  → APK tiers (en dehors du Play Store)
  → Stores alternatifs
  → Phishing (lien vers APK malveillant)
  → Repackaging d'apps légitimes
```

## 2. Smishing (SMS Phishing)

```
Principe : Hameçonnage via SMS

Exemples typiques :
  "Votre colis est bloqué. Payez les frais : http://colissimo-fr.xyz"
  "Votre compte bancaire est suspendu. Vérifiez : http://bnp-secure.xyz"
  "Vous avez gagné un iPhone ! Cliquez ici"

Objectifs :
  - Vol de credentials (faux site de bank, opérateur)
  - Installation de malware (lien vers APK)
  - Fraude (paiement de faux frais)

Protection :
  - Ne jamais cliquer sur les liens SMS non sollicités
  - Vérifier l'URL (hover ou copier/coller)
  - Contacter directement l'entreprise via canal officiel
```

## 3. Attaques Wi-Fi et Réseau Mobile

```
Evil Twin sur mobile :
  → Créer un Rogue AP avec le nom d'un réseau connu
  → Le mobile s'y connecte automatiquement
  → Intercept du trafic (notamment HTTP)
  
Man-in-the-Mobile (MitMo) :
  → Malware sur le mobile intercepte le second facteur (SMS OTP)
  → Utilisé pour contourner la 2FA bancaire
  
Proxy HTTPS sur mobile (Burp Suite) :
  → Configurer le mobile pour passer par Burp
  → Installer le certificat Burp sur le mobile
  → Analyser tout le trafic HTTPS de l'app
```

## 4. Reverse Engineering APK

```bash
# ─── APKTOOL — Décompilation ressources ────────────────────────
apktool d app.apk -o app_decompiled/
# → Extrait : AndroidManifest.xml, res/, smali/ (bytecode)

# Modifier et repackager (repackaging attack) :
# 1. Décompiler
# 2. Modifier le code smali ou les ressources
# 3. Recompiler
apktool b app_decompiled/ -o app_modified.apk
# 4. Signer l'APK modifié :
keytool -genkey -v -keystore my.keystore -alias alias -keyalg RSA
jarsigner -keystore my.keystore app_modified.apk alias

# ─── JADX — Décompilation en Java lisible ──────────────────────
jadx app.apk -d output_dir/
jadx-gui app.apk    # Interface graphique
# → Code Java lisible (presque le code source original)

# Extraction de secrets :
strings app.apk | grep -i "api_key\|password\|secret\|token"
grep -r "http://" output_dir/   # URLs en HTTP
grep -r "jdbc:" output_dir/     # Connexions DB
```

## 5. ADB — Android Debug Bridge

```bash
# Vérifier les devices connectés :
adb devices

# Shell sur l'appareil :
adb shell

# Commandes utiles depuis adb shell :
pm list packages                    # Lister les applications
pm list packages -f | grep target   # Trouver une app
am start -n com.app/.MainActivity   # Lancer une activité
content query --uri content://sms   # Lire les SMS (si permissions)

# Extraction de données :
adb pull /data/data/com.app/databases/  # DB SQLite (root requis)
adb pull /sdcard/                        # External storage (toujours)
adb backup com.target.app                # Backup si allowBackup=true

# Installer un APK malveillant :
adb install malicious.apk

# Logcat (logs de l'app) :
adb logcat | grep com.target.app        # Logs de l'app cible
adb logcat -d > logs.txt                # Sauvegarder les logs
```

## 6. Attaques SS7

```
SS7 (Signaling System 7) = Protocole de télécommunication des opérateurs

Vulnérabilités :
  → Intercepter les SMS (contient les codes 2FA !)
  → Localiser un téléphone
  → Rediriger les appels

Impact :
  → Contournement de la 2FA par SMS
  → Espionnage ciblé (gouvernements, groupes criminels)
  
Protection :
  → Utiliser une app d'authentification (TOTP) plutôt que SMS
  → Éviter la 2FA par SMS pour les comptes critiques
  → Utiliser des clés hardware (YubiKey)
```

---

## 🧠 Points clés à retenir pour l'examen

- **Smishing** = phishing par SMS
- **ADB** = outil Android officiel pour les tests (connexion USB + debug)
- **Apktool** = décompiler les ressources APK (smali)
- **jadx** = décompiler en code Java lisible
- **MitMo** = malware interceptant le second facteur SMS
- **SS7** = faille des protocoles télécoms → interception SMS
- **allowBackup=true** = extraire les données via `adb backup`

---

*⬅️ [Vulnérabilités mobiles](./02_vulnerabilites_mobiles.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
