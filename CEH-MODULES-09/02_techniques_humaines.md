# 👤 CEH v13 — Module 09 | Partie 2 : Techniques Basées sur l'Humain
> **Human-Based Social Engineering Techniques**

---

## 1. Vue d'ensemble

Les techniques humaines impliquent une **interaction directe** (physique ou vocale) entre l'attaquant et la victime. Elles ne nécessitent pas forcément d'outils informatiques.

```
┌─────────────────────────────────────────────────────────────┐
│           TECHNIQUES HUMAINES (Human-Based)                  │
├──────────────────────┬──────────────────────────────────────┤
│  INTERACTION DIRECTE │  OBSERVATION / PHYSIQUE              │
├──────────────────────┼──────────────────────────────────────┤
│  • Impersonation      │  • Shoulder Surfing                  │
│  • Vishing            │  • Dumpster Diving                   │
│  • Pretexting         │  • Tailgating / Piggybacking         │
│  • Elicitation        │  • Diversion Theft                   │
│  • Reverse SE         │  • Honey Trap                        │
└──────────────────────┴──────────────────────────────────────┘
```

---

## 2. Impersonation (Usurpation d'identité physique)

> **Définition** : L'attaquant se fait passer pour quelqu'un d'autre afin de gagner la confiance et d'accéder à des zones ou informations restreintes.

**Scénarios courants :**

| Rôle joué | Objectif |
|-----------|----------|
| Technicien IT | Accéder aux serveurs |
| Livreur / Coursier | Entrer dans les locaux |
| Auditeur / Consultant | Obtenir des documents internes |
| Employé d'un fournisseur | Accès aux systèmes de production |
| Agent de sécurité | Contourner les contrôles d'accès |

**Indicateurs visuels utilisés :**
- Badges falsifiés
- Uniformes ou tenues professionnelles
- Matériel crédible (ordinateur portable, clipboard)

---

## 3. Vishing (Voice Phishing)

> **Définition** : Attaque par appel téléphonique visant à tromper la victime pour qu'elle divulgue des informations sensibles.

**Cibles typiques :**
- Service Helpdesk (réinitialisation de mots de passe)
- Département RH (données employés)
- Comptabilité / Finance (virements frauduleux)

**Techniques associées :**
- **Caller ID Spoofing** : Afficher un numéro de confiance (banque, DSI)
- **Voicemail phishing** : Laisser un message urgent
- **AI Voice Cloning** *(CEH v13)* : Reproduire la voix d'un dirigeant

**Exemple de script type :**
```
"Bonjour, je suis [Prénom] du support IT. 
 Votre compte a été compromis. 
 Pour sécuriser votre accès, j'ai besoin 
 de votre mot de passe temporaire..."
```

> ⚠️ **CEH v13 — Nouveauté IA** : Le Voice Cloning permet de reproduire fidèlement la voix du DG ou du DSI pour autoriser des virements ou des accès.

---

## 4. Pretexting (Prétexte)

> **Définition** : Création d'un **scénario fictif crédible** (prétexte) pour manipuler la victime.

L'attaquant construit une identité et une histoire convaincante **avant** de contacter la cible.

**Exemples de prétextes :**
- "Je suis auditeur externe mandaté par la direction..."
- "Je rappelle suite à votre demande de support..."
- "Votre colis est bloqué en douane, confirmez vos infos..."

**Différence Pretexting vs Phishing :**

| | Pretexting | Phishing |
|-|-----------|---------|
| Canal | Téléphone / Physique | E-mail / Web |
| Personnalisation | Très élevée | Variable |
| Interaction | Active (dialogue) | Passive (lien à cliquer) |

---

## 5. Elicitation (Extraction d'infos par conversation)

> **Définition** : Technique subtile consistant à extraire des informations confidentielles **à travers une conversation naturelle**, sans éveiller les soupçons.

**Méthodes d'élicitation :**

| Méthode | Exemple |
|---------|---------|
| **Flattery** | "Vous semblez très compétent en sécurité, comment gérez-vous les accès ?" |
| **Feinte d'ignorance** | "Je ne comprends pas comment fonctionne votre VPN..." |
| **Fausse déclaration** | "J'ai entendu dire que votre pare-feu est un Cisco ASA ?" |
| **Intérêt commun** | Créer un rapport pour ouvrir la discussion |

---

## 6. Shoulder Surfing

> **Définition** : Observer physiquement ou via caméra ce qu'une personne tape ou regarde sur son écran.

**Contextes :**
- Transports en commun (métro, train, avion)
- Espace de co-working / open space
- Caisses automatiques, distributeurs
- Accès via caméras de surveillance détournées

**Contremesure** : Filtre de confidentialité d'écran (*privacy screen*)

---

## 7. Dumpster Diving (Fouille des poubelles)

> **Définition** : Chercher dans les poubelles (physiques ou numériques) des informations sensibles jetées sans être détruites.

**Ce qu'on peut y trouver :**
- Relevés bancaires, factures
- Organigrammes internes
- Mots de passe notés sur papier
- Anciens badges d'accès
- Supports de stockage (CD, clés USB, disques durs)
- Contrats, plans stratégiques

**Contremesure** : Politique de **destruction systématique** des documents (broyeur, déchiquetage).

---

## 8. Tailgating & Piggybacking

> **Tailgating** : Suivre discrètement une personne autorisée pour entrer dans une zone sécurisée **sans que celle-ci le sache**.

> **Piggybacking** : Identique, mais la personne autorisée **laisse passer** l'attaquant (souvent par politesse).

```
[Zone Publique] → [Porte d'accès] → [Zone Sécurisée]
                        ↑
               Employé scan badge
                        ↑
              Attaquant suit juste derrière
              (ou se fait tenir la porte)
```

**Déguisements courants :**
- Agent de maintenance
- Livreur avec colis volumineux (les bras "pleins")
- Nouveau collaborateur perdu

---

## 9. Reverse Social Engineering

> **Définition** : L'attaquant crée une situation où **c'est la victime qui vient chercher de l'aide**, et l'attaquant se positionne comme l'expert de confiance.

**Étapes :**
1. **Sabotage** : L'attaquant crée un problème (virus, panne réseau)
2. **Publicité** : Il se fait connaître comme "l'expert qui peut aider"
3. **Aide** : La victime fait appel à lui, l'attaquant exploite la situation

---

## 10. Honey Trap (Piège à miel)

> **Définition** : Utilisation d'une personne séduisante ou d'une relation fictive pour manipuler la cible et extraire des informations.

- Très utilisé dans les contextes **d'espionnage industriel**
- Peut être réalisé en ligne (faux profil LinkedIn, réseau social)
- Exploitation sur la durée (relation de confiance établie progressivement)

---

## 11. Diversion Theft (Vol par diversion)

> **Définition** : Créer une distraction pour détourner l'attention et voler des informations ou accès.

**Exemple** : Pendant qu'un complice retient l'attention du réceptionniste, l'attaquant accède à un poste déverrouillé.

---

## 📌 Récapitulatif — Techniques Humaines

| Technique | Vecteur | Niveau de sophistication |
|-----------|---------|--------------------------|
| Impersonation | Physique | ⭐⭐⭐ |
| Vishing | Téléphone | ⭐⭐ |
| Pretexting | Téléphone / Physique | ⭐⭐⭐⭐ |
| Elicitation | Conversation | ⭐⭐⭐⭐ |
| Shoulder Surfing | Physique / Visuel | ⭐ |
| Dumpster Diving | Physique | ⭐ |
| Tailgating | Physique | ⭐⭐ |
| Piggybacking | Physique | ⭐ |
| Reverse SE | Psychologique | ⭐⭐⭐⭐⭐ |
| Honey Trap | Relationnel | ⭐⭐⭐⭐⭐ |

---

*← [Concepts Fondamentaux](./01_concepts_fondamentaux.md) | Suite → [Techniques Informatiques](./03_techniques_informatiques.md)*
