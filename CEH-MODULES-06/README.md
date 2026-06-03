# 📚 CEH v13 — Module 06 : System Hacking

> **Certified Ethical Hacker v13 — AI Powered**  
> EC-Council | Formation officielle

---

## 🗂️ Structure des notes

| Fichier | Contenu |
|--------|---------|
| [`01-introduction-methodology.md`](./01-introduction-methodology.md) | CEH Hacking Methodology (CHMA), objectifs, phases |
| [`02-password-cracking.md`](./02-password-cracking.md) | Types d'attaques, SAM, NTLM, Kerberos, Mimikatz, Hashcat |
| [`03-vulnerability-exploitation.md`](./03-vulnerability-exploitation.md) | Metasploit, Buffer Overflow, AI-powered tools |
| [`04-privilege-escalation.md`](./04-privilege-escalation.md) | DLL Hijacking, UAC Bypass, Named Pipe, Spectre/Meltdown |
| [`05-maintaining-acces.md`](./05-maintaining-acces.md) | Backdoors, Keyloggers, Spyware, Persistence Registry |
| [`06-rootkits-hidden-files.md`](./06-rootkits-hidden-files.md) | Types de rootkits, NTFS ADS, Bootkit, détection |
| [`07-steganography.md`](./07-steganography.md) | Techniques, outils (Steghide, OpenStego), stéganalyse |
| [`08-covering-tracks.md`](./08-covering-tracks.md) | Effacement logs Windows/Linux, anti-forensics |
| [`09-active-directory-attacks.md`](./09-active-directory-attacks.md) | Pass-the-Hash, Kerberoasting, Golden Ticket, BloodHound |
| [`10-Quiz-et-Revision.md`](./10-Quiz-et-Revision.md) | Questions d'examen, mémo et checklist |

---

## 🎯 Objectifs du module

À l'issue de ce module, vous serez capable de :

- ✅ Appliquer la méthodologie CEH Hacking (CHMA) en 5 étapes
- ✅ Effectuer du cracking de mots de passe (brute-force, dictionnaire, rainbow tables)
- ✅ Exploiter des vulnérabilités via Metasploit et buffer overflow
- ✅ Escalader les privilèges sur Windows (DLL Hijacking, UAC) et Linux (SUID)
- ✅ Maintenir un accès persistant (backdoors, keyloggers, registry run keys)
- ✅ Cacher des fichiers via rootkits et NTFS ADS (Alternate Data Streams)
- ✅ Effacer les traces (logs, historique, timestamps)
- ✅ Conduire des attaques Active Directory (Pass-the-Hash, Golden Ticket)

---

## 📊 Poids dans l'examen CEH (312-50)

```
Module 06 — System Hacking
Poids estimé : ~15-17% des questions — LE PLUS IMPORTANT !
Nb de labs officiels : ~10 labs dans la plateforme iLabs/CyberQ
Thèmes récurrents : NTLM/Kerberos/SAM, Mimikatz, Pass-the-Hash,
                    Golden Ticket, NTFS ADS, DLL Hijacking,
                    wevtutil, Metasploit sessions
```

---

## 🔗 Ressources complémentaires

- [EC-Council Official CEHv13](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/)
- [CyberQ Platform (Labs)](https://cyberq.eccouncil.org)
- [Mimikatz (GitHub)](https://github.com/gentilkiwi/mimikatz)
- [Metasploit Framework](https://www.metasploit.com/)
- [BloodHound (AD attacks)](https://github.com/BloodHoundAD/BloodHound)
- [PEASS-ng (PrivEsc)](https://github.com/carlospolop/PEASS-ng)

---

## 🧠 Méthodologie CEH System Hacking (CHMA)

```
┌──────────────────────────────────────────────────────────────┐
│              CEH HACKING METHODOLOGY (CHMA)                  │
├──────────────────────────────────────────────────────────────┤
│  1. GAINING ACCESS        → Password cracking, exploits      │
│  2. ESCALATING PRIVILEGES → DLL Hijacking, UAC Bypass, SUID  │
│  3. EXECUTING APPS        → Keyloggers, backdoors, trojans   │
│  4. HIDING FILES          → Rootkits, NTFS ADS, Stéganog.    │
│  5. COVERING TRACKS       → Effacement logs, anti-forensics  │
└──────────────────────────────────────────────────────────────┘

Commandes essentielles :
  Mimikatz  : sekurlsa::logonpasswords, lsadump::sam
  Hashcat   : hashcat -m 1000 -a 0 hashes.txt rockyou.txt
  Meterpreter : getsystem, run post/multi/recon/local_exploit_suggester
  Windows logs : wevtutil cl System && wevtutil cl Security
  NTFS ADS  : type malware.exe > legit.txt:malware.exe
  Linux PrivEsc : find / -perm -4000 -type f 2>/dev/null
```

---

> ⚠️ **Rappel éthique** : Toutes ces techniques nécessitent une **autorisation écrite préalable**. Leur usage sans permission est illégal.

---

*Notes rédigées dans le cadre de la préparation à la certification CEH v13 — EC-Council*  
*Format : Markdown — Compatible GitHub Pages*
