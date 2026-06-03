# 📝 Module 12 — Quiz & Révision : Évasion IDS/Firewalls/Honeypots

> **CEH v13 | EC-Council**  
> `#Quiz` `#Revision` `#ExamTips` `#IDS` `#Evasion`

---

## 🔍 Récapitulatif rapide

- Un IDS **détecte** ; un IPS **bloque** (mode inline)
- NIDS = réseau ; HIDS = hôte
- Techniques d'évasion IDS : fragmentation, session splicing, TTL manipulation, encodage, slow scan
- Techniques de bypass firewall : DNS tunneling, HTTP tunneling, port source spoofing, firewalking
- Honeypot low-interaction (Honeyd) vs high-interaction (Cowrie) — toute connexion = suspect
- DPI + normalisation = défenses principales contre l'évasion

---

## 🧠 Concepts clés

| Terme | Définition |
|-------|------------|
| **NIDS** | IDS basé sur le réseau (capture et analyse le trafic) |
| **HIDS** | IDS basé sur l'hôte (analyse logs, fichiers, processus) |
| **Signature-based** | Détection par correspondance avec des patterns connus |
| **Anomaly-based** | Détection par déviation d'une baseline de comportement |
| **Session Splicing** | Fragmentation TCP pour cacher une signature sur plusieurs paquets |
| **Firewalking** | Déterminer les règles firewall par TTL crafting |
| **DNS Tunneling** | Encoder du trafic C&C dans des requêtes DNS |
| **Insertion Attack** | Paquets acceptés par l'IDS mais rejetés par la cible |
| **Evasion Attack** | Paquets rejetés par l'IDS mais acceptés par la cible |
| **Honeypot** | Leurre attractif pour les attaquants, aucun trafic légitime attendu |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **IDS = détection** ; **IPS = prévention/blocage** (inline)
2. **NIDS** surveille le réseau ; **HIDS** surveille l'hôte
3. **Signature-based** = efficace mais aveugle aux 0-days
4. **Anomaly-based** = détecte 0-days mais faux positifs élevés
5. **Fragmentation** contournée par **réassemblage complet** côté défense
6. **DNS tunneling** → port 53, détecter via longueur requêtes + volume
7. `nmap -D` = décoy scan ; `nmap -T0` = paranoid scan ; `nmap -f` = fragmentation
8. **Honeypot low-interaction** = émulation légère ; **high-interaction** = vrai système
9. **Insertion/Evasion** (Ptacek & Newsham 1998) = concepts fondamentaux évasion IDS

---

## ❓ Questions de révision

1. Quelle est la différence fondamentale entre un IDS et un IPS ?
2. Comment la fragmentation IP peut-elle permettre de contourner un IDS ?
3. Qu'est-ce que le firewalking et comment fonctionne-t-il ?
4. Expliquer le principe du DNS tunneling.
5. Quelle est la différence entre une attaque d'insertion et une attaque d'évasion contre un IDS ?
6. Comment un attaquant peut-il détecter qu'une machine cible est un honeypot ?
7. Pourquoi le scan Nmap avec `-T0` est-il difficile à détecter par un IDS ?
8. Quelle contre-mesure rend la fragmentation IP inefficace comme technique d'évasion ?

---

## 📋 Quiz QCM

**Question 1.** Un IPS diffère d'un IDS principalement car il :
- a) Analyse uniquement le trafic entrant
- b) Peut bloquer activement le trafic suspect (mode inline)
- c) Utilise uniquement des signatures pour détecter
- d) Est installé sur chaque hôte

**Question 2.** Quelle technique d'évasion consiste à envoyer des paquets avec un TTL insuffisant pour atteindre la cible mais suffisant pour l'IDS ?
- a) Session splicing
- b) Insertion attack
- c) Slow scan
- d) URL encoding

**Question 3.** Le DNS tunneling exploite le fait que :
- a) DNS est toujours chiffré
- b) Le port UDP 53 est généralement autorisé par les firewalls
- c) DNS ne laisse pas de traces
- d) Les requêtes DNS ne peuvent pas être analysées

**Question 4.** Quel outil Nmap permet de masquer l'IP réelle avec des leurres ?
- a) `nmap -f`
- b) `nmap -T0`
- c) `nmap -D`
- d) `nmap -sS`

**Question 5.** Un honeypot low-interaction comme Honeyd :
- a) Utilise un vrai OS avec de vrais services
- b) Émule des services et répond aux connexions de façon superficielle
- c) Est totalement invisible sur le réseau
- d) Ne peut détecter que les attaques internes

**Question 6.** La normalisation des paquets est une contre-mesure contre :
- a) Les attaques DDoS volumétriques
- b) L'encodage URL et la fragmentation IP
- c) Le brute force de mots de passe
- d) Les attaques de déni de service

**Question 7.** Quelle commande Fragroute fragmente les paquets IP en chunks de 16 bytes ?
- a) `ip_frag 8`
- b) `tcp_seg 16`
- c) `ip_frag 16`
- d) `fragment 16`

**Question 8.** Le firewalking permet de :
- a) Sauter par-dessus un firewall physiquement
- b) Déterminer les règles d'un firewall par manipulation du TTL
- c) Mettre le feu aux configurations firewall
- d) Tester les performances d'un firewall

---

## ✅ Points de révision — Checklist

- [ ] Je comprends la différence IDS (détection) vs IPS (prévention)
- [ ] Je connais les différences NIDS / HIDS
- [ ] Je peux expliquer la fragmentation comme technique d'évasion
- [ ] Je comprends le session splicing au niveau TCP
- [ ] Je sais utiliser les options Nmap pour l'évasion (-f, -D, -T0, -g)
- [ ] Je peux expliquer le DNS tunneling et son principe
- [ ] Je connais la différence insertion attack / evasion attack
- [ ] Je comprends ce qu'est un honeypot et sa valeur défensive
- [ ] Je sais comment détecter un honeypot (indicateurs comportementaux)
- [ ] Je connais les contre-mesures : DPI, normalisation, SIEM

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | IPS = mode inline, peut bloquer le trafic |
| 2 | **b** | Insertion attack = TTL insuffisant pour la cible |
| 3 | **b** | Port 53 UDP généralement autorisé |
| 4 | **c** | `-D` = décoy scan avec leurres IP |
| 5 | **b** | Low-interaction = émulation légère des services |
| 6 | **b** | Normalisation traite l'encodage et la fragmentation |
| 7 | **c** | `ip_frag 16` fragmente en chunks de 16 bytes |
| 8 | **b** | Firewalking = TTL crafting pour sonder les règles |

---

*⬅️ [Contre-mesures](./05_contre-mesures.md) | ➡️ [README Module 12](./README.md)*
