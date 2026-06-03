# 📚 CEH v13 — Module 07 : Malware Threats

> **Certified Ethical Hacker v13 — AI Powered**  
> EC-Council | Formation officielle

---

## 🗂️ Structure des notes

| Fichier | Contenu |
|--------|---------|
| [`01_Introduction_et_Types.md`](./01_Introduction_et_Types.md) | Taxonomie des malwares, définitions, propagation |
| [`02_Trojans_et_Backdoors.md`](./02_Trojans_et_Backdoors.md) | Trojans, RAT, Backdoors, ports communs |
| [`03_Virus_et_Vers.md`](./03_Virus_et_Vers.md) | Virus, Worms, Macro, types de propagation |
| [`04_Ransomware_et_Rootkits.md`](./04_Ransomware_et_Rootkits.md) | Ransomware, Rootkits (kernel/user), Bootkits, Botnets |
| [`05_Techniques_Avancees.md`](./05_Techniques_Avancees.md) | Fileless, Polymorphique, Métamorphique, AI-Based, APT |
| [`06_Analyse_Malware.md`](./06_Analyse_Malware.md) | Analyse statique, dynamique, sandboxing, memory forensics |
| [`07_Detection_et_Contre-mesures.md`](./07_Detection_et_Contre-mesures.md) | AV, EDR, sandbox, yara rules, contre-mesures |
| [`08_Cas_Reels_et_Quiz.md`](./08_Cas_Reels_et_Quiz.md) | Incidents réels (WannaCry, Emotet...) & quiz examen |

---

## 🎯 Objectifs du module

À l'issue de ce module, vous serez capable de :

- ✅ Classifier les malwares (Trojan, Virus, Worm, Ransomware, Rootkit, Spyware)
- ✅ Distinguer les caractéristiques de chaque type (réplication, vecteur, payload)
- ✅ Comprendre les APT (Advanced Persistent Threats) et leur cycle de vie
- ✅ Analyser un malware statiquement (strings, PE header, imports) et dynamiquement
- ✅ Expliquer les techniques d'évasion (fileless, polymorphisme, obfuscation)
- ✅ Mettre en place des contre-mesures (AV, EDR, sandboxing)

---

## 📊 Poids dans l'examen CEH (312-50)

```
Module 07 — Malware Threats
Poids estimé : ~5-7% des questions de l'examen
Nb de labs officiels : ~6 labs dans la plateforme iLabs/CyberQ
Thèmes récurrents : Virus vs Worm vs Trojan (différences !),
                    Trojan ports (2, 20, 21, 23, 1234, 5555...),
                    WannaCry / EternalBlue, fileless malware,
                    analyse statique vs dynamique
```

---

## 🔗 Ressources complémentaires

- [EC-Council Official CEHv13](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/)
- [CyberQ Platform (Labs)](https://cyberq.eccouncil.org)
- [VirusTotal](https://www.virustotal.com/)
- [Any.run (sandbox)](https://any.run/)
- [YARA Rules](https://yara.readthedocs.io/)
- [MalwareBazaar](https://bazaar.abuse.ch/)

---

## 🧠 Mémo — Différences clés

```
VIRUS   → Nécessite une action humaine pour se propager
          S'attache à un fichier hôte

WORM    → Auto-réplication SANS action humaine
          Se propage par le réseau (TCP/IP, email)
          Exemple : WannaCry (SMB exploit EternalBlue)

TROJAN  → Se déguise en programme légitime
          Pas d'auto-réplication
          Ouvre une backdoor (RAT = Remote Access Trojan)

ROOTKIT → Furtivité : se cache sous l'OS (ring 0)
          Difficile à détecter depuis l'OS infecté

RANSOMWARE → Chiffre les fichiers + demande rançon
             Exemple : WannaCry, CryptoLocker, Ryuk
```

---

*Notes rédigées dans le cadre de la préparation à la certification CEH v13 — EC-Council*  
*Format : Markdown — Compatible GitHub Pages*
