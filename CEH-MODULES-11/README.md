# 📚 CEH v13 — Module 11 : Session Hijacking

> **Certified Ethical Hacker v13 — AI Powered**  
> EC-Council | Formation officielle

---

## 🗂️ Structure des notes

| Fichier | Contenu |
|--------|---------|
| [`01_concepts_fondamentaux.md`](./01_concepts_fondamentaux.md) | Cycle de vie des sessions, cookies, tokens, TCP vs app-level |
| [`02_types_et_mecanismes.md`](./02_types_et_mecanismes.md) | Session fixation, sidejacking, CSRF, XSS, replay |
| [`03_techniques_avancees.md`](./03_techniques_avancees.md) | Prédiction de séquence TCP, ARP poisoning, SSL Stripping, JWT |
| [`04_outils_et_labs.md`](./04_outils_et_labs.md) | Wireshark, Burp Suite, BeEF, Scapy, Hamster/Ferret |
| [`05_contre-mesures.md`](./05_contre-mesures.md) | HTTPS, cookie flags, régénération ID, MFA, CSRF tokens |
| [`06_quiz_et_revision.md`](./06_quiz_et_revision.md) | Questions, QCM, cas d'exam et rappels |

---

## 🎯 Objectifs du module

À l'issue de ce module, vous serez capable de :

- ✅ Comprendre le cycle de vie d'une session et les mécanismes d'identification
- ✅ Distinguer le hijacking TCP du hijacking applicatif
- ✅ Identifier les vecteurs de vol de session (cookie theft, sidejacking, XSS)
- ✅ Utiliser des outils d'audit de session (Burp Suite, Wireshark, Scapy)
- ✅ Déployer des contre-mesures efficaces (TLS, flags cookies, CSRF tokens)

---

## 📊 Poids dans l'examen CEH (312-50)

```
Module 11 — Session Hijacking
Poids estimé : ~3-5% des questions de l'examen
Nb de labs officiels : ~4 labs dans la plateforme iLabs/CyberQ
Thèmes récurrents : session fixation vs hijacking, HttpOnly/Secure/SameSite
```

---

## 🔗 Ressources complémentaires

- [EC-Council Official CEHv13](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/)
- [CyberQ Platform (Labs)](https://cyberq.eccouncil.org)
- [OWASP Session Management Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)
- [OWASP Testing Guide — Session Hijacking](https://owasp.org/www-project-web-security-testing-guide/)

---

*Notes rédigées dans le cadre de la préparation à la certification CEH v13 — EC-Council*  
*Format : Markdown — Compatible GitHub Pages*
