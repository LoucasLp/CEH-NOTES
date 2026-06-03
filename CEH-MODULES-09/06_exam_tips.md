# 🎯 CEH v13 — Module 09 | Partie 6 : Astuces Examen
> **Exercices Pratiques, Questions Types & Mémo Final**


## 1. Questions Types d'Examen CEH

### Questions sur les Définitions

**Q1 :** *Qu'est-ce que le Social Engineering ?*
> ✅ L'art de manipuler des individus pour les amener à divulguer des informations confidentielles en exploitant la psychologie humaine plutôt que les failles techniques.

---

**Q2 :** *Quelle technique consiste à suivre une personne autorisée dans une zone sécurisée sans qu'elle le sache ?*
> ✅ **Tailgating**
> (Piggybacking = la personne autorisée laisse intentionnellement entrer l'attaquant)

---

**Q3 :** *Quel est le terme pour un e-mail de phishing ciblant spécifiquement un PDG ou directeur général ?*
> ✅ **Whaling**

---

**Q4 :** *Un attaquant appelle la hotline IT en se faisant passer pour un employé et demande la réinitialisation de son mot de passe. Quelle technique utilise-t-il ?*
> ✅ **Vishing** (Voice Phishing) avec **Impersonation**

---

**Q5 :** *Quelle technique de social engineering consiste à laisser une clé USB infectée dans un parking d'entreprise ?*
> ✅ **Baiting**

---

**Q6 :** *Quel protocole DNS permet de définir une politique sur la gestion des emails échouant SPF/DKIM ?*
> ✅ **DMARC** (Domain-based Message Authentication, Reporting and Conformance)

---

**Q7 :** *SET (Social-Engineer Toolkit) a été développé par qui ?*
> ✅ **TrustedSec** (David Kennedy)

---

**Q8 :** *Quelle méthode SET permet de cloner un site web existant pour du credential harvesting ?*
> ✅ **Site Cloner**

---

**Q9 :** *La technique "Reverse Social Engineering" implique que :*
> ✅ La victime est manipulée pour **prendre contact elle-même** avec l'attaquant, qui se présente comme un expert pouvant l'aider.

---

**Q10 :** *Netcraft affiche quel message si un site est identifié comme phishing ?*
> ✅ **"This site is a known phishing site"**

---

### Questions Scénarios

**Q11 :** *Maria reçoit un email de sa "banque" avec le bouton "Vérifier votre compte". En survolant le lien, elle voit une URL différente de celle de sa banque. Quel type d'attaque s'agit-il ?*
> ✅ **Phishing** (indicateur : URL différente du texte affiché)

---

**Q12 :** *Un attaquant envoie un email à un employé en se faisant passer pour le PDG et demande un virement urgent de 50 000€. Quelle attaque est-ce ?*
> ✅ **Business Email Compromise (BEC)** / **Whaling**

---

**Q13 :** *Quel type de menace interne décrit un employé qui cause accidentellement une fuite de données en cliquant sur un lien malveillant ?*
> ✅ **Negligent Insider** (Insider involontaire / négligent)

---

**Q14 :** *Un attaquant crée un faux profil LinkedIn imitant un recruteur pour collecter des informations sur des employés. Quelle technique est utilisée ?*
> ✅ **Impersonation sur les réseaux sociaux** / **Honey Trap**

---

## 3. Mémo Final — Cheat Sheet Module 09

```
╔══════════════════════════════════════════════════════════════╗
║           CEH v13 — MODULE 09 CHEAT SHEET                    ║
╠══════════════════════════════════════════════════════════════╣
║ TECHNIQUES HUMAINES                                          ║
║  • Impersonation     → Se faire passer pour quelqu'un        ║
║  • Vishing           → Phishing par téléphone                ║
║  • Pretexting        → Faux scénario pour extraire infos     ║
║  • Shoulder Surfing  → Observer l'écran/clavier              ║
║  • Dumpster Diving   → Fouiller les poubelles                ║
║  • Tailgating        → Suivre sans que la cible le sache     ║
║  • Piggybacking      → La cible laisse passer intentionnel.  ║
║  • Reverse SE        → La victime vient chercher l'aide      ║
║  • Elicitation       → Extraire infos par conversation       ║
║  • Honey Trap        → Attirer par séduction / intérêt       ║
╠══════════════════════════════════════════════════════════════╣
║ TECHNIQUES INFORMATIQUES                                     ║
║  • Phishing          → Email frauduleux générique            ║
║  • Spear Phishing    → Email frauduleux ciblé                ║
║  • Whaling           → Spear Phishing visant les dirigeants  ║
║  • Smishing          → Phishing par SMS                      ║
║  • Pharming          → Redirection DNS vers faux site        ║
║  • Clone Phishing    → Copie d'un email légitime             ║
║  • BEC               → Compromission email professionnel     ║
║  • Watering Hole     → Compromission site fréquenté par cible║
║  • Quid Pro Quo      → Service contre information            ║
╠══════════════════════════════════════════════════════════════╣
║ PHASES D'ATTAQUE SE                                          ║
║  1. Recherche (OSINT)                                        ║
║  2. Développement relation (Hook)                            ║
║  3. Exploitation                                             ║
║  4. Sortie (Exit)                                            ║
╠══════════════════════════════════════════════════════════════╣
║ OUTILS OFFENSIFS                                             ║
║  • SET (setoolkit)   → Framework social engineering          ║
║  • Shellphish        → Pages phishing automatisées           ║
║  • Gophish           → Campagnes phishing simulées           ║
╠══════════════════════════════════════════════════════════════╣
║ OUTILS DÉFENSIFS                                             ║
║  • Netcraft          → Détection phishing (navigateur)       ║
║  • PhishTank         → Base URLs phishing                    ║
║  • VirusTotal        → Analyse URLs/fichiers                 ║
╠══════════════════════════════════════════════════════════════╣
║ CONTREMESURES                                                ║
║  • Formation & sensibilisation = DÉFENSE #1                  ║
║  • MFA sur tous les comptes critiques                        ║
║  • SPF + DKIM + DMARC sur le domaine email                   ║
║  • Politique de moindre privilège                            ║
║  • Procédure de vérification d'identité indépendante         ║
║  • DLP + UEBA pour la détection des insiders                 ║
╠══════════════════════════════════════════════════════════════╣
║ CEH v13 — SPÉCIFICITÉS IA                                    ║
║  • Voice Cloning     → Usurpation vocale par IA              ║
║  • Deepfakes         → Fausses vidéos convaincantes          ║
║  • AI Spear Phishing → Personnalisation massive automatisée  ║
╚══════════════════════════════════════════════════════════════╝
```




---

*← [Contremesures & Outils](./05_contremesures_outils.md) | ← [Retour au README](./README.md)*

---

> 📝 **Notes rédigées à des fins éducatives pour la préparation au CEH v13 (312-50)**  
> *Toutes les techniques décrites sont présentées dans un contexte légal de pentest éthique.*
