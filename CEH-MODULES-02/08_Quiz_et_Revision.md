# 📝 Module 02 — Quiz & Révision : Footprinting & Reconnaissance

> **CEH v13 | EC-Council**  
> `#Quiz` `#Footprinting` `#OSINT` `#GoogleDork` `#SHODAN` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- Footprinting = **première phase** du hacking éthique — collecte d'infos sur la cible
- **Passif** = sans contact direct (légal sans autorisation) — **Actif** = interaction directe (autorisation requise)
- Sources clés : DNS/WHOIS, moteurs de recherche, SHODAN, réseaux sociaux, Maltego
- Objectif : construire une carte de la surface d'attaque de la cible

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **Footprinting passif** | Collecte sans contact direct (WHOIS, Google, LinkedIn) |
| **Footprinting actif** | Interaction directe (DNS queries, traceroute, banner grabbing) |
| **Google Dork** | Opérateurs de recherche avancée pour trouver données sensibles |
| **SHODAN** | Moteur de recherche des équipements connectés (IoT, SCADA...) |
| **WHOIS** | Infos d'enregistrement d'un domaine (owner, registrar, contacts) |
| **DNS Zone Transfer** | Copie complète des enregistrements DNS — fuite d'infrastructure |
| **theHarvester** | Collecte d'emails, sous-domaines, IPs depuis sources publiques |
| **Maltego** | Outil OSINT graphique — visualisation des liens entre entités |
| **MX Record** | Enregistrement DNS pointant vers le serveur de mail |
| **NS Record** | Enregistrement DNS pointant vers les serveurs de noms |
| **FOCA** | Analyse des métadonnées des documents publics |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **Footprinting passif** = Google, WHOIS, DNS public — aucune interaction directe
2. **SHODAN** = scanner Internet des appareils connectés (IoT, SCADA, ICS)
3. **Zone Transfer** (`dig @ns domain AXFR`) = révèle tous les sous-domaines
4. **Google Dork** `site:target.com filetype:xls` = chercher fichiers Excel exposés
5. **theHarvester** = collecter emails + domaines + IPs depuis sources multiples
6. **Maltego** = outil graphique de visualisation OSINT (transforms)
7. **FOCA** = extraire métadonnées (auteur, chemin réseau, logiciel) depuis PDFs/DOCX
8. **Enregistrements DNS à connaître** : A, AAAA, MX, NS, TXT, CNAME, SOA, PTR
9. **Contre-mesure principale** : minimiser l'information publique, protection WHOIS

---

## ❓ Questions de révision

1. Quelle est la différence entre footprinting passif et actif ?
2. Expliquez ce qu'est un Google Dork et donnez un exemple concret.
3. Comment fonctionne un transfert de zone DNS et pourquoi est-il dangereux ?
4. Quelles informations peut-on obtenir via WHOIS sur un domaine ?
5. Quels enregistrements DNS sont les plus importants à recenser en reconnaissance ?
6. Quel est l'avantage de SHODAN par rapport aux moteurs de recherche classiques ?
7. Comment Maltego est-il différent de theHarvester ?
8. Pourquoi les métadonnées des documents peuvent-elles être une source d'information pour un attaquant ?

---

## 📋 Quiz QCM

**Question 1.** Le footprinting passif inclut laquelle des activités suivantes ?
- a) Scan de ports Nmap sur la cible
- b) Recherche Google et consultation du WHOIS
- c) Envoi de requêtes DNS directes au serveur cible
- d) Test d'injection SQL sur le site web cible

**Question 2.** SHODAN est principalement utilisé pour :
- a) Scanner les vulnérabilités d'un réseau interne
- b) Trouver des appareils connectés exposés sur Internet (caméras, routeurs, SCADA)
- c) Gérer les certificats SSL d'un site web
- d) Analyser le trafic réseau capturé

**Question 3.** La commande `dig @ns1.target.com target.com AXFR` tente de :
- a) Scanner les ports ouverts du serveur ns1
- b) Réaliser un transfert de zone DNS pour obtenir tous les enregistrements
- c) Vérifier la disponibilité du serveur DNS
- d) Changer les enregistrements DNS de target.com

**Question 4.** Quel enregistrement DNS indique le ou les serveurs de messagerie d'un domaine ?
- a) A
- b) NS
- c) MX
- d) TXT

**Question 5.** Google Dork `site:target.com filetype:pdf` permet de :
- a) Trouver les sous-domaines de target.com
- b) Lister tous les fichiers PDF indexés sur target.com
- c) Vérifier les certificats SSL de target.com
- d) Télécharger le code source de target.com

**Question 6.** Quel outil permet d'extraire les métadonnées de fichiers Office et PDF publics ?
- a) theHarvester
- b) Maltego
- c) FOCA
- d) SHODAN

**Question 7.** theHarvester est principalement utilisé pour :
- a) Visualiser graphiquement les relations entre entités
- b) Collecter des emails, sous-domaines et noms de personnes depuis sources publiques
- c) Scanner les ports d'une cible
- d) Analyser les vulnérabilités SCADA

**Question 8.** Quelle contre-mesure empêche la fuite d'informations sur le propriétaire d'un domaine via WHOIS ?
- a) Désactiver le DNS
- b) Utiliser un enregistrement privé / protection WHOIS
- c) Passer le site en HTTPS
- d) Configurer des règles de firewall

---

## ✅ Points de révision — Checklist

- [ ] Je distingue footprinting passif (sans contact) vs actif (avec contact)
- [ ] Je connais les Google Dorks essentiels (`site:`, `filetype:`, `intitle:`, `inurl:`)
- [ ] Je comprends ce qu'est un transfert de zone DNS et pourquoi c'est dangereux
- [ ] Je connais les enregistrements DNS : A, AAAA, MX, NS, TXT, CNAME, SOA
- [ ] Je sais utiliser SHODAN pour trouver des équipements exposés
- [ ] Je connais theHarvester (emails, sous-domaines) vs Maltego (graphique)
- [ ] Je comprends ce que FOCA extrait des documents (métadonnées)

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | Google/WHOIS = sans contact direct = passif |
| 2 | **b** | Shodan = moteur des équipements connectés |
| 3 | **b** | AXFR = zone transfer request |
| 4 | **c** | MX = Mail eXchanger |
| 5 | **b** | `filetype:pdf` liste les PDFs indexés |
| 6 | **c** | FOCA = analyse métadonnées documents |
| 7 | **b** | theHarvester = collecte emails, sub-domaines |
| 8 | **b** | Protection WHOIS = données propriétaire masquées |

---

*⬅️ [Outils & Contre-mesures](./07_tools_countermeasures.md) | ➡️ [README Module 02](./README.md)*
