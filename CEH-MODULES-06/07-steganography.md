# 🖼️ 07 — Stéganographie & Stéganalyse

> **CEH v13 | Module 6** | [← Retour au sommaire](./README.md)

---

## 📖 Définition

La **stéganographie** est l'art de dissimuler un message secret à l'intérieur d'un autre message ou fichier ordinaire, de sorte que seul l'expéditeur et le destinataire savent que le message caché existe.

> 🔑 **Distinction importante** :
> - **Cryptographie** : cache le **contenu** du message (visible mais illisible)
> - **Stéganographie** : cache **l'existence** du message (invisible)

---

## 🆚 Stéganographie vs Cryptographie

| Critère | Stéganographie | Cryptographie |
|---------|----------------|---------------|
| **Objectif** | Cacher l'existence | Chiffrer le contenu |
| **Détection** | Difficile à détecter | Message visible mais illisible |
| **Si découvert** | Tout est révélé | Reste illisible (si bon chiffrement) |
| **Usage CEH** | Cacher malware, données volées | Chiffrer les communications C2 |

---

## 🗂️ Classification de la Stéganographie

### 1. Stéganographie Technique

```
PHYSIQUE / TECHNIQUE
├─ Encre invisible
├─ Microdots (réduire le message à un point)
├─ Modifications physiques imperceptibles
└─ Méthodes laser/magnétiques
```

### 2. Stéganographie Numérique — Par support

```
SUPPORT IMAGE
├─ LSB (Least Significant Bit) — le plus courant
├─ Masquage et filtrage
└─ Transformations (DCT, DWT)

SUPPORT AUDIO
├─ LSB Audio
├─ Echo hiding
└─ Phase coding

SUPPORT VIDÉO
├─ Modification des frames
└─ LSB vidéo

SUPPORT TEXTE / DOCUMENT
├─ Espaces blancs supplémentaires
├─ Ponctuation cachée
└─ Acrostiche (1ère lettre de chaque mot)
```

---

## 🖼️ Technique LSB (Least Significant Bit)

La technique la plus répandue pour les images.

```
PRINCIPE :
Chaque pixel d'une image = combinaison de valeurs R, G, B (0-255)
                         = 8 bits chacun

Exemple — Pixel rouge = 11001010 (202)
Modifier le BIT LE MOINS SIGNIFICATIF (LSB) :
  Original : 11001010  → 202 (rouge vif)
  Modifié  : 11001011  → 203 (différence INVISIBLE à l'œil)

CAPACITÉ :
Image 800×600 pixels, 24 bits = 1,440,000 bits disponibles
Capacité LSB = 180,000 octets = ~175 KB de données cachées

DÉTECTION :
Sans analyse statistique → IMPOSSIBLE à détecter visuellement
```

---

### Masquage et Filtrage

```
Technique similaire aux filigranes sur papier.
Appliquée principalement aux images en niveaux de gris.
Détectable par analyse statistique simple.
```

---

## 🎵 Stéganographie Audio

### Echo Hiding
```
Intégre des données dans l'écho d'un signal audio.
Deux types d'écho : court délai = bit 0, long délai = bit 1
Inaudible dans un enregistrement normal.
```

### Phase Coding
```
Modifie les phases des segments audio.
Le message est encodé dans la différence de phase.
```

---

## 🛠️ Outils de Stéganographie

### OpenStego
> https://www.openstego.com

- Logiciel open-source multi-plateforme
- Cache des données dans des images
- Supporte la protection par mot de passe
- Peut créer des **filigranes numériques** invisibles

```
Usage : Attacker cache un payload dans une image
        → Envoie l'image à la victime
        → La victime (ou un autre outil) extrait le payload
```

---

### Steghide
```bash
# Cacher un fichier dans une image JPG
steghide embed -cf cover.jpg -sf secret.txt -p "motdepasse"

# Extraire le message caché
steghide extract -sf stego.jpg -p "motdepasse"

# Info sur une image (sans extraction)
steghide info stego.jpg
```

---

### QuickStego
- Interface graphique simple pour Windows
- Supporte BMP, GIF, JPG
- Peut cacher du texte ou des fichiers

---

### DeepSound (Audio)
```
Dissimule des données dans des fichiers audio
Formats : WAV, FLAC, APE, WMA, MP3
Chiffrement AES optionnel
Interface graphique Windows
```

---

### ShellGPT (CEH v13 — Nouveauté IA)

> Section nouvelle dans le CEH v13 — utilisation de l'IA pour la stéganographie

```bash
# Utiliser ShellGPT pour générer une commande de stéganographie
sgpt "Create a steganography command to hide the file secret.txt in cover.jpg"

# Stéganographie assistée par IA pour créer des images steganographiques
# avec des techniques avancées de distribution des bits
```

---

### Autres outils

| Outil | Type | Spécialité |
|-------|------|-----------|
| **SilentEye** | Image/Audio | Interface graphique, chiffrement AES |
| **OpenPuff** | Multi-format | Haute capacité, multi-carrier |
| **Stegosuite** | Image | Java, plusieurs algorithmes |
| **S-Tools** | Image/Audio | BMP, GIF, WAV |
| **MP3Stego** | Audio | Cache dans la compression MP3 |
| **ImageHide** | Image | Simple, texte dans BMP |

---

## 🔎 Stéganalyse (Steganalysis)

L'art de **détecter et rendre covert les messages** dans les fichiers stéganographiés.

```
Stéganalyse ← Attaque sur la stéganographie
```

### Types de stéganalyse

| Type | Méthode |
|------|---------|
| **Visuelle** | Chercher des artefacts visuels, bords anormaux, patterns |
| **Statistique** | Analyse Chi-square, RS Analysis, histogrammes |
| **Structurelle** | Analyser la structure du fichier (header, metadata) |
| **De signature** | Base de données des outils connus et leurs patterns |

---

### Détection par analyse visuelle

```
Indices visuels d'une image stéganographiée :
├─ Bords anormalement nets ou flous
├─ Zones de couleur inhabituelle
├─ Patterns répétitifs dans le bruit
└─ Taille de fichier anormalement grande
```

---

### Outils de Stéganalyse

| Outil | Description |
|-------|-------------|
| **StegSpy** | Détecte la stéganographie dans les images |
| **StegExpose** | Analyse statistique de fichiers images |
| **zsteg** | Détecte LSB dans PNG et BMP |
| **stegcracker** | Brute-force des mots de passe steghide |
| **Aperisolve** | Outil en ligne d'analyse stéganographique |
| **Binwalk** | Analyse les fichiers binaires, détecte les données embarquées |
| **ExifTool** | Analyse les métadonnées des fichiers |
| **Forensically** | Analyse d'images en ligne (clone detection, error level) |

```bash
# zsteg — détecter LSB dans une image
zsteg image.png
zsteg -a image.png   # Tous les canaux

# stegcracker — brute-force
stegcracker secret.jpg wordlist.txt

# binwalk — chercher des fichiers cachés
binwalk image.jpg
binwalk -e image.jpg   # Extraire

# ExifTool — métadonnées
exiftool image.jpg

# steghide — tenter d'extraire sans mot de passe
steghide extract -sf image.jpg -p ""
```

---

## 🎯 Stéganographie dans le contexte du System Hacking

### Scénarios d'utilisation par les attaquants

```
1. EXFILTRATION DE DONNÉES
   Données volées → cachées dans une image
   → Envoyée par email / HTTP → Semble innocente

2. COMMUNICATION C2 (Command & Control)
   Commandes du C2 → cachées dans des images
   → Postées sur un forum public / réseau social
   → Le malware les récupère et les décode

3. PERSISTANCE
   Malware configuré pour cacher ses payloads dans des fichiers légitimes
   → AV ne détecte rien d'anormal dans l'image

4. ÉVASION AV
   Payload chiffré + caché dans une image
   → Pas de signature malware détectable directement
```

---

## 🛡️ Défense contre la Stéganographie

| Mesure | Description |
|--------|-------------|
| **Inspection des images sortantes** | Scanner les images avant l'envoi (DLP) |
| **Outils de stéganalyse sur le réseau** | Détecter automatiquement les fichiers suspects |
| **Politique d'usage des images** | Interdire les images de sources inconnues |
| **Analyse des métadonnées** | ExifTool pour détecter les anomalies |
| **Steganalysis dans le SOC** | Intégrer la détection au flux d'analyse |

---

[← Rootkits](./06-rootkits-hidden-files.md) | [→ Covering Tracks](./08-covering-tracks.md)
