# 📝 Module 06 — Quiz & Révision : System Hacking

> **CEH v13 | EC-Council**  
> `#Quiz` `#SystemHacking` `#NTLM` `#Kerberos` `#PrivEsc` `#ActiveDirectory`

---

## 🔍 Récapitulatif rapide

- Module 06 = le plus important de l'examen CEH (~15%)
- **CHMA** : Gaining Access → Escalating Privileges → Executing Apps → Hiding Files → Covering Tracks
- **Mimikatz** = outil clé : dump LSASS, Pass-the-Hash, Golden Ticket
- **NTLM** = challenge/response, vulnérable au Pass-the-Hash
- **Kerberos** = tickets TGT/TGS, vulnérable au Kerberoasting et Golden Ticket
- **NTFS ADS** = cacher des fichiers dans les flux alternatifs de données
- **Effacement logs** : `wevtutil cl` (Windows), `history -c` + shred (Linux)

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **SAM** | Security Account Manager — stocke les hashes de MDP Windows |
| **NTLM** | Protocole d'auth Windows par challenge/response, hash 128 bits |
| **Kerberos** | Protocole d'auth à tickets (TGT, TGS) via KDC |
| **Pass-the-Hash** | Utiliser le hash NTLM sans connaître le mot de passe clair |
| **Pass-the-Ticket** | Utiliser un ticket Kerberos TGT volé |
| **Golden Ticket** | Forger un TGT avec le hash KRBTGT → accès illimité 10 ans |
| **Kerberoasting** | Demander un TGS et le cracker hors-ligne (service accounts) |
| **DLL Hijacking** | Remplacer une DLL légitime par une DLL malveillante |
| **UAC Bypass** | Contourner le contrôle des comptes (User Account Control) |
| **NTFS ADS** | Alternate Data Streams — cacher du contenu dans les métadonnées NTFS |
| **DCSync** | Simuler un DC pour répliquer les hashes de MDP (Mimikatz) |
| **Covering Tracks** | Effacer logs, historique, timestamps pour éviter la détection |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH (15% des questions) :**

1. **Pass-the-Hash vs Pass-the-Ticket** : PtH = hash NTLM ; PtT = ticket Kerberos TGT
2. **Golden Ticket** = forger TGT avec hash KRBTGT → accès illimité 10 ans par défaut
3. **Mimikatz** = `sekurlsa::logonpasswords` (dump LSASS), `lsadump::sam`, `kerberos::golden`
4. **Hashcat** : `-m 1000` = NTLM, `-m 1800` = SHA-512 Unix, `-m 3200` = bcrypt
5. **NTFS ADS** : `type file.exe > legit.txt:hidden.exe` | `dir /r` pour lister
6. **Windows event logs** : `wevtutil cl System` et `wevtutil cl Security`
7. **Linux covering tracks** : `export HISTSIZE=0` + `shred ~/.bash_history`
8. **DLL Hijacking** : Windows cherche les DLLs dans l'ordre → remplacer celle en premier
9. **SUID Linux** : `find / -perm -4000 -type f 2>/dev/null` → fichiers SUID exécutables comme root
10. **BloodHound** = cartographier les chemins d'escalade dans Active Directory

---

## ❓ Questions de révision

1. Quelle est la différence entre Pass-the-Hash et Pass-the-Ticket ?
2. Comment fonctionne un Golden Ticket et pourquoi est-il si dangereux ?
3. Expliquez le Kerberoasting step by step.
4. Quelle est la différence entre NTLM et Kerberos en termes d'authentification ?
5. Comment détecter et lister les fichiers NTFS ADS sur un système Windows ?
6. Quelles commandes Windows permettent d'effacer les logs d'événements ?
7. Qu'est-ce que le DLL Hijacking et comment l'attaquant l'exploite-t-il ?
8. Comment `getsystem` dans Meterpreter tente-t-il d'escalader les privilèges ?

---

## 📋 Quiz QCM

**Question 1.** Le Pass-the-Hash (PtH) exploite quel mécanisme d'authentification ?
- a) Kerberos (ticket TGT)
- b) NTLM (hash Windows réutilisé sans mot de passe clair)
- c) LDAP (bind anonyme)
- d) SSH (clé publique)

**Question 2.** Un Golden Ticket Kerberos est forgé à partir du hash de quel compte ?
- a) Administrator
- b) Domain Admin
- c) KRBTGT
- d) Guest

**Question 3.** `hashcat -m 1000` est utilisé pour cracker quel type de hash ?
- a) SHA-256
- b) bcrypt
- c) NTLM
- d) MD5

**Question 4.** La commande Mimikatz `sekurlsa::logonpasswords` permet de :
- a) Lister les partages réseau
- b) Extraire les credentials depuis la mémoire LSASS
- c) Modifier le mot de passe administrateur
- d) Scanner les ports du réseau

**Question 5.** Pour cacher un exécutable malveillant dans un fichier texte NTFS, on utilise :
- a) `attrib +h malware.exe`
- b) `type malware.exe > legit.txt:malware.exe`
- c) `icacls legit.txt /deny everyone:f`
- d) `xcopy /h malware.exe legit.txt`

**Question 6.** Le Kerberoasting cible principalement :
- a) Les comptes administrateurs locaux
- b) Les comptes de service ayant un SPN (Service Principal Name) enregistré
- c) Les contrôleurs de domaine
- d) Les comptes invités

**Question 7.** La commande Linux `find / -perm -4000 -type f 2>/dev/null` cherche :
- a) Les fichiers avec le bit sticky
- b) Les fichiers SUID (exécutés avec les droits du propriétaire)
- c) Les fichiers world-writable
- d) Les fichiers cachés dans les répertoires système

**Question 8.** Pour effacer les logs d'événements Windows depuis la ligne de commande, on utilise :
- a) `del C:\Windows\System32\logs\*.evtx`
- b) `wevtutil cl System && wevtutil cl Security`
- c) `taskkill /f /im eventlog.exe`
- d) `net stop eventlog`

**Question 9.** Le DLL Hijacking exploite le fait que Windows :
- a) Vérifie la signature des DLLs avant de les charger
- b) Cherche les DLLs dans un ordre de répertoires prédéfini (DLL search order)
- c) Charge toujours les DLLs depuis System32 uniquement
- d) N'utilise pas de liste blanche pour les DLLs

**Question 10.** BloodHound est utilisé pour :
- a) Scanner les vulnérabilités web d'une application
- b) Analyser et visualiser les chemins d'attaque dans Active Directory
- c) Détecter les rootkits sur les postes Windows
- d) Intercepter les communications TLS/HTTPS

---

## ✅ Points de révision — Checklist

- [ ] Je connais les 5 étapes CHMA dans l'ordre exact
- [ ] Je comprends la différence NTLM vs Kerberos
- [ ] Je peux expliquer Pass-the-Hash vs Pass-the-Ticket vs Golden Ticket
- [ ] Je sais ce que fait `mimikatz` (logonpasswords, sam, golden)
- [ ] Je connais les modes Hashcat importants (1000=NTLM, 1800=SHA512, 3200=bcrypt)
- [ ] Je comprends NTFS ADS (comment cacher + comment détecter avec `dir /r`)
- [ ] Je sais les commandes d'effacement de logs (wevtutil, shred, HISTSIZE)
- [ ] Je comprends le DLL Hijacking et comment le détecter
- [ ] Je sais rechercher les SUID sur Linux (`find / -perm -4000`)
- [ ] Je connais BloodHound et son utilisation dans les attaques AD

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | PtH = réutilisation du hash NTLM sans connaître le mot de passe |
| 2 | **c** | Golden Ticket forgé avec le hash du compte KRBTGT |
| 3 | **c** | `-m 1000` = NTLM dans Hashcat |
| 4 | **b** | sekurlsa::logonpasswords dumpe les credentials de LSASS |
| 5 | **b** | `type file > legit.txt:hidden` = création d'un ADS |
| 6 | **b** | Kerberoasting cible les comptes avec SPN (service accounts) |
| 7 | **b** | `-perm -4000` = bit SUID (execute as owner) |
| 8 | **b** | `wevtutil cl` = clear log Windows |
| 9 | **b** | Windows utilise un ordre de recherche DLL (search order hijacking) |
| 10 | **b** | BloodHound = cartographie des chemins d'escalade AD |

---

*⬅️ [Active Directory Attacks](./09-active-directory-attacks.md) | ➡️ [README Module 06](./README.md)*
