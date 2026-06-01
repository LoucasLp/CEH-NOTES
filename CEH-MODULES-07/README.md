# 🛡️ CEH v13 — Module 07 : Malware Threats

> **Certified Ethical Hacker v13** | EC-Council | Formation officielle  
> 📅 Mis à jour : 2025 | ✍️ Notes de cours personnelles

---

## 📋 Table des matières

| Fichier | Contenu | Thème |
|--------|---------|-------|
| [01_Introduction_et_Types.md](./01_Introduction_et_Types.md) | Introduction & Taxonomie des malwares | 🦠 Concepts fondamentaux |
| [02_Trojans_et_Backdoors.md](./02_Trojans_et_Backdoors.md) | Trojans, RAT, Backdoors | 🐴 Accès non autorisé |
| [03_Virus_et_Vers.md](./03_Virus_et_Vers.md) | Virus, Worms, Propagation | 🔬 Réplication & Infection |
| [04_Ransomware_et_Rootkits.md](./04_Ransomware_et_Rootkits.md) | Ransomware, Rootkits, Botnets | 💰 Extorsion & Persistance |
| [05_Techniques_Avancees.md](./05_Techniques_Avancees.md) | Fileless, Polymorphique, AI-Based, APT | 🔬 Évasion avancée |
| [06_Analyse_Malware.md](./06_Analyse_Malware.md) | Static, Dynamic, Memory Forensics | 🔍 Investigation |
| [07_Detection_et_Contre-mesures.md](./07_Detection_et_Contre-mesures.md) | Outils, EDR, Countermeasures | 🛡️ Défense |
| [08_Cas_Reels_et_Quiz.md](./08_Cas_Reels_et_Quiz.md) | Incidents réels & Questions d'examen | 📚 Révision |

---

## 🎯 Objectifs du module

À la fin de ce module, vous serez capable de :

- ✅ Décrire les concepts de malware et leurs méthodes de propagation
- ✅ Identifier et distinguer les types de malwares (Trojan, Virus, Worm, Ransomware...)
- ✅ Comprendre les APT (Advanced Persistent Threats) et leur cycle de vie
- ✅ Expliquer le fonctionnement des malwares fileless et basés sur l'IA
- ✅ Effectuer une analyse statique et dynamique de malwares
- ✅ Appliquer des contre-mesures adaptées
- ✅ Utiliser les outils de détection (AV, EDR, Sandbox)

---

## 🧠 Mémo d'examen rapide

```
"Very Wild Tigers Really Run Swiftly Backwards"
 ↓      ↓      ↓       ↓      ↓      ↓        ↓
Virus  Worms  Trojans Rootkit Ransomware Spyware Botnets
```

> 💡 **Règle d'or** :  
> - **Virus** = nécessite action humaine  
> - **Worm** = auto-réplication sans interaction  
> - **Trojan** = se déguise en programme légitime  

---

## 📊 Vue d'ensemble des types de malware

```
                    MALWARE
                      │
     ┌────────────────┼────────────────┐
     │                │                │
  Infecteurs      Furtifs          Destructeurs
     │                │                │
  Virus           Rootkit          Ransomware
  Worm            Bootkit          Wiper
  Macro           Fileless         Logic Bomb
     │                │
  Trojan          Spyware
  RAT             Keylogger
  Backdoor        Adware
```

---

## ⚡ Statistiques clés (contexte examen)

| Incident | Année | Impact |
|---------|-------|--------|
| ILOVEYOU Worm | 2000 | ~$10 milliards de dégâts |
| Stuxnet | 2010 | Ciblage infrastructure SCADA iranienne |
| WannaCry | 2017 | 230 000 victimes en 24h, exploit SMB |
| Emotet | 2014-2021 | Trojan bancaire devenu plateforme distribution |
| Mirai Botnet | 2016 | DDoS via objets IoT compromis |

---

*Notes préparées pour la certification CEH v13 — Usage personnel et GitHub*
