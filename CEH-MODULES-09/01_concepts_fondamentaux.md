# 🧠 CEH v13 — Module 09 | Partie 1 : Concepts Fondamentaux
> **Social Engineering — Fondements & Psychologie**

---

## 1. Définition

> **Le Social Engineering** est l'art de manipuler des individus pour les amener à divulguer des informations confidentielles ou à effectuer des actions compromettant la sécurité d'un système.

Contrairement aux attaques techniques, le social engineering **exploite les failles humaines** plutôt que les failles logicielles. L'attaquant joue sur :
- La **confiance** naturelle des individus
- La **peur** et l'urgence
- La **curiosité** humaine
- Le **désir d'être utile**

---

## 2. Pourquoi le Social Engineering est-il efficace ?

```
┌─────────────────────────────────────────────────────┐
│           FACTEURS DE VULNÉRABILITÉ                  │
├─────────────────────────────────────────────────────┤
│  🔴 Absence de formation / sensibilisation           │
│  🔴 Systèmes de sécurité obsolètes                  │
│  🔴 Politiques de sécurité insuffisantes             │
│  🔴 Confiance excessive envers les collègues         │
│  🔴 Manque de procédures de vérification             │
│  🔴 Accès trop larges aux informations sensibles     │
└─────────────────────────────────────────────────────┘
```

---

## 3. Cibles Fréquentes

| Cible | Raison |
|-------|--------|
| **Personnel RH** | Accès aux données employés |
| **Helpdesk / IT Support** | Réinitialisation de mots de passe |
| **Réceptionnistes** | Accès physique aux locaux |
| **Cadres dirigeants** | Autorisations financières (Whaling) |
| **Nouveaux employés** | Méconnaissent les procédures |
| **Personnes âgées** | Moins habituées aux technologies modernes |

---

## 4. Principes Psychologiques Exploités

| Principe | Description | Exemple d'attaque |
|----------|-------------|-------------------|
| **Autorité** | Obéissance à une figure d'autorité | Se faire passer pour le DSI |
| **Réciprocité** | Rendre service en échange | "Je t'aide, aide-moi en retour" |
| **Urgence / Peur** | Pression temporelle | "Votre compte sera bloqué dans 1h" |
| **Sympathie** | Exploiter la bienveillance | Créer un lien de confiance |
| **Preuve sociale** | "Tout le monde le fait" | Faux témoignages |
| **Engagement** | Cohérence avec les engagements passés | Escalade progressive des demandes |
| **Rareté** | Sentiment de manque | "Offre limitée" |

---

## 5. Comportements Vulnérables

> Ces comportements rendent les individus susceptibles d'être victimes :

- 🔓 Partager des mots de passe avec des collègues
- 🔓 Cliquer sur des liens sans vérification
- 🔓 Ouvrir des pièces jointes inconnues
- 🔓 Laisser des documents sensibles sur son bureau
- 🔓 Permettre l'accès physique à des inconnus
- 🔓 Ne pas vérifier l'identité d'un appelant
- 🔓 Utiliser des mots de passe faibles ou réutilisés

---

## 6. Les 4 Phases d'une Attaque de Social Engineering

```
Phase 1: RECHERCHE
━━━━━━━━━━━━━━━━━
 Collecte d'informations sur la cible
 → OSINT, réseaux sociaux, LinkedIn, site web de l'entreprise
 → Organigrammes, noms des employés, fournisseurs

         ⬇️

Phase 2: DÉVELOPPEMENT DE LA RELATION (Hook)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 Établir la confiance avec la cible
 → Se faire passer pour un collègue, fournisseur, technicien
 → Créer un prétexte crédible

         ⬇️

Phase 3: EXPLOITATION
━━━━━━━━━━━━━━━━━━━━━
 Utiliser la confiance établie pour extraire des infos
 → Demander des mots de passe, codes d'accès
 → Faire exécuter une action malveillante

         ⬇️

Phase 4: SORTIE (Exit)
━━━━━━━━━━━━━━━━━━━━━
 Effacer les traces et maintenir la crédibilité
 → Éviter de déclencher des soupçons
 → Maintenir la relation pour de futures attaques
```

---

## 7. Pourquoi c'est Critique en CEH v13 ?

> ⚠️ **Nouveau dans CEH v13** : L'IA amplifie les attaques de social engineering

- **Voice Cloning / Deepfakes** : Usurpation vocale ou visuelle convaincante
- **Spear Phishing automatisé par IA** : E-mails ultra-personnalisés à grande échelle
- **Chatbots malveillants** : Simulation de conversations naturelles
- **AI-generated content** : Faux documents, fausses preuves

---

## 📌 Points Clés à Mémoriser

> 💡 Le Social Engineering est la **manipulation psychologique** d'individus dans le but d'obtenir des informations ou des accès.

| ✅ À retenir |
|------------|
| Cible la **faiblesse humaine**, pas les systèmes |
| Phase 1 = **Recherche** (OSINT) |
| Principes : **Autorité, Urgence, Réciprocité, Sympathie** |
| L'IA (v13) → **deepfakes, voice cloning, spear phishing automatisé** |
| Seule defense efficace = **Formation et sensibilisation** |

---

*← Retour au [README](./README.md) | Suite → [Techniques Humaines](./02_techniques_humaines.md)*
