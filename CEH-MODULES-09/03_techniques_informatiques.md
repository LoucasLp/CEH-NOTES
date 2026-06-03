# 💻 CEH v13 — Module 09 | Partie 3 : Techniques Informatiques
> **Computer-Based Social Engineering — Phishing, SET, Outils**

---

## 1. Vue d'ensemble

Les attaques informatiques de social engineering utilisent des **technologies numériques** comme vecteur de manipulation. Elles permettent d'atteindre des milliers de victimes simultanément.

```
┌────────────────────────────────────────────────────────────────┐
│           TECHNIQUES INFORMATIQUES (Computer-Based)            │
├────────────────────┬───────────────────────────────────────────┤
│  EMAIL / WEB       │  MOBILE / VOIX / AUTRES                   │
├────────────────────┼───────────────────────────────────────────┤
│  • Phishing        │  • Smishing (SMS)                         │
│  • Spear Phishing  │  • Vishing (Voix)                         │
│  • Whaling         │  • Spam                                   │
│  • Pharming        │  • Pop-up malveillants                    │
│  • Clone Phishing  │  • Watering Hole Attack                   │
│  • BEC / EAC       │  • Quid Pro Quo                           │
└────────────────────┴───────────────────────────────────────────┘
```

---

## 2. Phishing — La Menace #1

> **Définition** : Envoi d'e-mails frauduleux imitant des organisations légitimes pour tromper les victimes et voler des identifiants ou installer des malwares.

**Statistique clé** : ~**3,4 milliards** d'e-mails de phishing envoyés chaque jour.

### Anatomie d'un e-mail de phishing

```
De:      support@paypa1.com  ← Domaine falsifié (1 au lieu de l)
À:       victime@entreprise.fr
Objet:   ⚠️ URGENT : Votre compte sera suspendu sous 24h

[Logo PayPal falsifié]

Cher client,

Nous avons détecté une activité suspecte sur votre compte.
Veuillez confirmer votre identité immédiatement :

        [ SÉCURISER MON COMPTE → ]
        
   ← Lien pointant vers : http://paypa1-secure.xyz/login

Cordialement,
L'équipe de sécurité PayPal
```

**Indicateurs d'un phishing :**
- ❌ Domaine légèrement différent (typosquatting)
- ❌ Urgence artificielle ("Dans les 24h")
- ❌ Lien différent du texte affiché (hover le lien)
- ❌ Mauvaise grammaire / orthographe
- ❌ Pièce jointe non sollicitée (.exe, .zip, .docm)
- ❌ Demande d'informations sensibles

---

## 3. Types de Phishing

### 3.1 Spear Phishing
> E-mail **ciblé et personnalisé** pour une personne ou organisation spécifique.

- Utilise des informations OSINT (nom, poste, projets en cours)
- Taux de succès beaucoup plus élevé que le phishing générique
- **Exemple** : "Bonjour Jean, suite à notre réunion de lundi sur le projet Orion..."

### 3.2 Whaling
> Spear Phishing visant les **hauts dirigeants** (CEO, CFO, CISO).

- Objectif : Autorisations de virements, accès à des données stratégiques
- Souvent combiné avec du BEC (Business Email Compromise)
- **Exemple** : Faux e-mail du PDG demandant un virement urgent

### 3.3 Clone Phishing
> Copie exacte d'un e-mail **légitime déjà reçu**, avec un lien ou pièce jointe remplacés par des éléments malveillants.

### 3.4 Pharming
> Redirection des utilisateurs vers un **faux site web** sans qu'ils cliquent sur un lien.
- Modifie le fichier `hosts` ou empoisonne le DNS
- La victime tape la bonne URL mais arrive sur un site malveillant

### 3.5 Business Email Compromise (BEC)
> Compromission ou usurpation d'une adresse e-mail professionnelle légitime.

| Type | Description |
|------|-------------|
| **BEC** | Compromission d'un compte email d'entreprise |
| **EAC** | Compromission d'un compte email personnel |

---

## 4. Smishing (SMS Phishing)

> **Définition** : Phishing via **messages SMS** ou MMS.

**Exemples de messages smishing :**
```
📱 "Votre colis UPS n'a pu être livré. 
    Planifiez la relivraison : https://ups-delivery.xyz"

📱 "BANQUE SOCIÉTÉ GÉNÉRALE : Transaction suspecte 
    détectée. Confirmez : http://sg-secure.net"

📱 "Vous avez gagné un iPhone ! Réclamez-le ici : 
    http://promo-iphone.win"
```

**Caractéristiques :**
- Numéros rotatifs utilisés pour contourner les filtres
- Liens vers des sites malveillants
- Souvent doublé d'un appel (Vishing) pour renforcer la crédibilité

---

## 5. Watering Hole Attack (Attaque par point d'eau)

> **Définition** : Compromettre un **site web fréquemment visité** par la cible pour y injecter des malwares.

```
Attaquant identifie les sites fréquentés par la cible
            ↓
Compromet l'un de ces sites légitimes
            ↓
Injecte un exploit ou malware
            ↓
La cible visite le site et est infectée
```

**Exemple** : Compromettre le forum d'une association professionnelle fréquentée par des employés d'une entreprise cible.

---

## 6. Pop-up Attacks & Scareware

> Fenêtres pop-up trompeuses affichant des **alertes de fausse sécurité**.

```
┌────────────────────────────────────────────┐
│  ⚠️  ALERTE VIRUS CRITIQUE                 │
│                                            │
│  Votre PC est infecté par 5 virus !        │
│  Votre carte bancaire est en danger.        │
│                                            │
│  [ NETTOYER MAINTENANT → ]                 │
└────────────────────────────────────────────┘
```

---

## 7. Quid Pro Quo

> **Définition** : Offrir un **service ou avantage** en échange d'informations.

**Exemple classique** : Un attaquant appelle en se présentant comme le "support IT" et propose d'améliorer les performances du PC. En échange, il demande l'accès à distance ou le mot de passe.

---

## 8. Social Engineer Toolkit (SET)

> **SET** est un framework open-source Python dédié aux tests de pénétration via social engineering. Développé par **TrustedSec**.

### Interface SET

```
[SET] Social-Engineer Toolkit - TrustedSec

Select from the menu:
 1) Social-Engineering Attacks
 2) Penetration Testing (Fast-Track)
 3) Third Party Modules
 4) Update the Social-Engineer Toolkit
 5) Update SET configuration
 6) Help, Credits, and About
 99) Exit the Social-Engineer Toolkit
```

### Vecteurs d'attaque disponibles dans SET

```
Social Engineering Attacks:
 1) Spear-Phishing Attack Vectors
 2) Website Attack Vectors
    ├── Java Applet Attack Method
    ├── Metasploit Browser Exploit
    ├── Credential Harvester Attack Method
    │   ├── Web Templates
    │   ├── Site Cloner         ← Clonage de site web
    │   └── Custom Import
    └── Tabnabbing Attack Method
 3) Infectious Media Generator
 4) Create a Payload and Listener
 5) Mass Mailer Attack
 6) Arduino-Based Attack Vector
 7) SMS Spoofing Attack Vector
 8) Wireless Access Point Attack Vector
```

### Lab SET — Credential Harvesting (Scénario type)

**Objectif** : Cloner un site de login et capturer les identifiants

```bash
# 1. Lancer SET
sudo setoolkit

# 2. Navigation dans les menus
# → 1 (Social-Engineering Attacks)
# → 2 (Website Attack Vectors)
# → 3 (Credential Harvester Attack Method)
# → 2 (Site Cloner)

# 3. Entrer l'IP de la machine attaquante
SET: Enter your IP address for the POST back: 192.168.1.100

# 4. URL à cloner
SET: Enter the url to clone: https://www.facebook.com

# → SET clone le site et démarre un serveur HTTP
# → Les identifiants capturés apparaissent dans le terminal
```

**Résultat visible :**
```
[*] Credential Harvester Running on 192.168.1.100
[*] Captured Credentials:
    Username: jean.dupont@email.com
    Password: MonMotDePasse123
```

---

## 9. Netcraft — Détection Anti-Phishing

> **Netcraft** est un outil/extension permettant de détecter les sites de phishing.

**Fonctionnement :**
- Vérifie l'âge du domaine
- Analyse l'hébergement et le pays d'origine
- Compare avec sa base de données de sites malveillants

**Message affiché si site de phishing détecté :**
```
⛔ "This site is a known phishing site"
   Netcraft Anti-Phishing Community Toolbar
```

**Outil complémentaire** : **PhishTank** — base de données collaborative des URLs de phishing.

---

## 10. Shellphish — Phishing via Kali Linux

> Outil automatisé pour créer des pages de phishing de sites populaires sous Kali Linux.

**Sites supportés :** Facebook, Instagram, Twitter, LinkedIn, Google, Netflix, etc.

```bash
# Installation
git clone https://github.com/suljot/shellphish

# Utilisation
cd shellphish && bash shellphish.sh

# Sélectionner le site à cloner → génère un lien de phishing
```

---

## 📌 Récapitulatif — Techniques Informatiques

| Technique | Canal | Détection difficile |
|-----------|-------|---------------------|
| Phishing | Email | ⭐⭐ |
| Spear Phishing | Email ciblé | ⭐⭐⭐⭐ |
| Whaling | Email (dirigeants) | ⭐⭐⭐⭐ |
| Smishing | SMS | ⭐⭐⭐ |
| Pharming | DNS/Hosts | ⭐⭐⭐⭐⭐ |
| Watering Hole | Site web légitime | ⭐⭐⭐⭐⭐ |
| BEC | Email professionnel | ⭐⭐⭐⭐⭐ |
| Quid Pro Quo | Téléphone/Email | ⭐⭐ |

---

*← [Techniques Humaines](./02_techniques_humaines.md) | Suite → [Menaces Internes & Usurpation](./04_menaces_internes_usurpation.md)*
