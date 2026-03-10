# 🚧 06 — Scanning au-delà des IDS & Firewalls

> **Module 3 — CEH v13** | [← Retour au sommaire](./README.md)

---

## Vue d'ensemble

Même si les IDS et firewalls sont conçus pour bloquer les attaques, ils ont des **limitations de sécurité** que les attaquants exploitent.

```
┌─────────────────────────────────────────────────────────┐
│           TECHNIQUES D'ÉVASION IDS/FIREWALL             │
├───────────────────┬─────────────────────────────────────┤
│  Manipulation     │  Dissimulation                      │
│  de paquets       │  d'identité                         │
│                   │                                     │
│  • Fragmentation  │  • IP Address Decoy                 │
│  • Source Routing │  • IP Address Spoofing              │
│  • Source Port    │  • MAC Address Spoofing             │
│  • Bad Checksums  │  • Proxy Servers                    │
│  • Custom Packets │  • Anonymizers                      │
│  • Random Hosts   │                                     │
└───────────────────┴─────────────────────────────────────┘
```

---

## 1. Fragmentation de Paquets

Diviser les paquets probe en **plusieurs fragments plus petits** lors de l'envoi sur le réseau.

### Pourquoi ça fonctionne

La plupart des IDS sont configurés pour **ignorer les paquets fragmentés** lors des port scans car le traitement de chaque fragment consomme trop de ressources CPU et réseau.

```
Paquet original
     │
     ▼
┌───────────────────────────────┐
│  Fragment 1 │ Fragment 2 │ ...│  ← IDS souvent skip les fragments
└───────────────────────────────┘
     │
     ▼
Hôte cible : réassemble les fragments → paquet original reconstitué
```

### SYN/FIN Scanning via Fragmentation IP

Le header TCP est divisé en plusieurs fragments pour **contourner les packet filters**.

> ⚠️ La réassemblée côté serveur peut produire des résultats **imprévisibles** : crashes, reboots, dumps de monitoring réseau.

**Outil :** Nmap supporte la fragmentation de paquets.

---

## 2. Source Routing

Manipulation du **champ IP Options** pour définir un chemin spécifique que les paquets vont emprunter.

```
Normal routing :
Attaquant → Router A → [FIREWALL] → Router B → Cible

Source Routing :
Attaquant → Router A → Router C → Router B → Cible
(en contournant le FIREWALL via un chemin défini par l'attaquant)
```

### Deux modes

| Mode | Description |
|------|-------------|
| **Loose Source Routing** | Spécifie une liste de routeurs par lesquels le paquet doit passer (peut passer par d'autres aussi) |
| **Strict Source Routing** | Spécifie exactement chaque routeur sur le chemin |

---

## 3. Source Port Manipulation

**Option Zenmap :** `-g` ou `--source-port`

Remplacer le **numéro de port source réel** par un port bien connu (HTTP, DNS, FTP...).

```bash
nmap -g 80 <target>   # Simule du trafic HTTP
nmap -g 53 <target>   # Simule du trafic DNS
```

### Pourquoi ça fonctionne

Les administrateurs configurent souvent les firewalls pour **autoriser le trafic entrant** depuis des ports bien connus (80, 443, 53, 21...).

> ⚠️ **Mauvaise pratique de sécurité :** Faire confiance aveuglément au numéro de port source.

---

## 4. IP Address Decoy

**Option Nmap :** `-D RND:10` ou `-D decoy1,decoy2,...`

Générer des adresses IP "leurres" pour faire croire à la cible que plusieurs hôtes scannent en simultané → difficile pour l'IDS/firewall de déterminer l'IP réelle.

### Méthode 1 — Décoys aléatoires automatiques
```bash
nmap -D RND:10 10.10.10.10
# Nmap génère 10 IPs aléatoires + position l'IP réelle aléatoirement
```

### Méthode 2 — Décoys manuels
```bash
nmap -D 192.168.0.1,172.120.2.8,192.168.2.8,10.10.1.19,10.10.1.5 10.10.1.11
#                                                         ^IP réelle  ^Cible
```

> Utiliser **ME** pour positionner votre IP réelle à un endroit précis dans la liste.

```bash
nmap -D 192.168.0.1,192.168.0.2,ME,192.168.0.3 <target>
# Votre IP sera en 3ème position
```

> ⚠️ **Limites :** Inefficace si la cible utilise le path tracing ou le response dropping. Trop de décoys peut **ralentir** le scan.

---

## 5. IP Address Spoofing

Technique de hijacking où l'attaquant forge les en-têtes de paquets pour se faire passer pour un hôte légitime.

```
Attaquant (IP: 1.2.3.4)
    │
    │── Paquet avec IP source = 5.6.7.8 ──►  Cible
    │                                          │
    │                                          │── Réponse vers 5.6.7.8 (IP spoofée)
    │                                          │   (ne revient pas à l'attaquant)
```

### IP Spoofing avec Hping3
```bash
hping3 www.certifiedhacker.com -a 7.7.7.7
# Envoie des paquets TCP/IP arbitraires avec l'IP source spoofée 7.7.7.7
```

> ⚠️ **Limitation :** Impossible de **compléter le three-way handshake** avec une IP spoofée → pas de connexion TCP établie.

**Usage principal :** Attaques **DoS** (Denial of Service).

---

## 6. MAC Address Spoofing

Les firewalls filtrent aussi via les **adresses MAC**. L'attaquant utilise une fausse MAC pour se faire passer pour un hôte légitime.

### Commandes Nmap pour MAC Spoofing

#### MAC aléatoire
```bash
nmap -sT -Pn --spoof-mac 0 10.10.1.11
# --spoof-mac 0 → génère une MAC aléatoire
```

#### MAC basée sur un vendeur
```bash
nmap -sT -Pn --spoof-mac Dell 10.10.1.11
# Génère une MAC dans la plage du fabricant Dell
```

#### MAC manuelle
```bash
nmap -sT -Pn --spoof-mac 00:01:02:25:56:AE 10.10.1.11
# Définit manuellement l'adresse MAC
```

> 💡 **Avantage :** La MAC originale n'est **pas enregistrée** dans les logs firewall.

---

## 7. Création de Paquets Personnalisés

### Colasoft Packet Builder
> 🌐 Source : [https://www.colasoft.com](https://www.colasoft.com)

Outil de création de paquets réseau personnalisés. **3 vues principales :**

| Vue | Description |
|-----|-------------|
| **Packet List** | Affiche tous les paquets construits |
| **Decode Editor** | Modification sans mémoriser longueurs/offsets |
| **Hex Editor** | Édition en hexadécimal et ASCII |

**Capacités :**
- Création de paquets fragmentés pour bypasser firewalls/IDS
- Flood de victimes avec un grand nombre de paquets (DoS)
- Envoi avec contrôle de l'intervalle, nombre de loops, délai

---

## 8. Randomisation de l'Ordre des Hôtes

**Option Nmap :** `--randomize-hosts`

```bash
nmap --randomize-hosts <target range>
```

Nmap **mélange chaque groupe de 16384 hôtes** avant de scanner → moins détectable par les systèmes de monitoring.

**Alternative :**
```bash
nmap -sL -n -oN liste.txt <range>  # Générer liste
# Puis randomiser avec script Perl
nmap -iL liste_randomisée.txt       # Scanner depuis la liste
```

---

## 9. Envoi de Mauvaises Checksums

**Option Nmap :** `--badsum`

```bash
nmap --badsum <target>
```

Envoie des paquets avec des checksums TCP/UDP **invalides**.

| Réponse | Signification |
|---------|--------------|
| **Réponse reçue** | IDS/Firewall ne vérifie **pas** les checksums → mal configuré |
| **Pas de réponse** | Système correctement configuré |

---

## 10. Proxy Servers

Un proxy server sert d'**intermédiaire** entre l'attaquant et la cible.

```
Attaquant → Proxy → Cible
              │
              └── Les logs de la cible enregistrent l'IP du PROXY
                  (pas celle de l'attaquant)
```

### Usages des proxies par les attaquants
- Masquer l'IP source réelle
- Éviter la détection IDS/firewall
- Accéder à des intranets normalement inaccessibles
- Chaîner plusieurs proxies pour maximiser l'anonymat

### Proxy Chaining

```
Attaquant → Proxy 1 → Proxy 2 → Proxy 3 → ... → Cible
              │          │          │
              └── Supprime les infos d'identification à chaque étape
```

### Outils Proxy

| Outil | URL | Description |
|-------|-----|-------------|
| **Proxy Switcher** | proxyswitcher.com | Surf anonyme, accès sites bloqués |
| **CyberGhost VPN** | cyberghostvpn.com | Cache IP, chiffre connexion, no logs |
| **Burp Suite** | portswigger.net | Proxy d'interception web |
| **Tor** | torproject.org | Réseau de routage en oignon |
| **Hotspot Shield** | hotspotshield.com | VPN grand public |
| **Proxifier** | proxifier.com | Routage d'applications via proxy |

---

## 11. Anonymizers

Un anonymizer est un **serveur intermédiaire** qui accède aux sites web à la place de l'utilisateur, rendant la navigation web **intraçable**.

### Fonctionnement

```
Utilisateur ──► Anonymizer ──► Site cible
              (cache IP, chiffre données)
```

### Pourquoi utiliser un anonymizer

| Raison | Description |
|--------|-------------|
| **Privacy** | Navigation intraçable |
| **Contournement censure** | Accès aux sites géo-bloqués ou censurés |
| **Protection DoS** | Routage via DNS protégé |
| **Bypass Firewall/IDS** | Le firewall voit seulement la connexion à l'anonymizer |

### Deux types d'anonymizers

#### Networked Anonymizers
```
Requête → Ordinateur A → Ordinateur B → Ordinateur C → Site cible
```
✅ Analyse de trafic très complexe  
❌ Risque de compromission à chaque nœud

#### Single-Point Anonymizers
```
Requête → Site Anonymizer → Site cible
```
✅ Cache IP efficacement  
❌ Moins résistant à l'analyse de trafic sophistiquée

### Outils Anonymizers

| Outil | URL | Type |
|-------|-----|------|
| **Whonix** | whonix.org | OS basé Debian + Tor (VM) |
| **AstrillVPN** | astrill.com | VPN + bypass censure |
| **Tails** | tails.net | Live OS (USB) + Tor + chiffrement |
| **Psiphon** | psiphon.ca | Circumvention de censure |
| **TunnelBear** | tunnelbear.com | VPN simple d'utilisation |
| **I2P** | geti2p.net | Réseau anonyme distribué |

---

## 12. Tableau récapitulatif des techniques d'évasion

| Technique | Option Nmap | Objectif | Contre |
|-----------|------------|---------|--------|
| Fragmentation | `-f` | Bypasser IDS par fragments | Port scanning IDS |
| Source Routing | — | Chemin personnalisé | Firewalls sur le chemin |
| Source Port Manip. | `-g / --source-port` | Simuler trafic légitime | Règles basées sur port src |
| IP Decoy | `-D RND:10` | Masquer IP réelle | Logging IDS |
| IP Spoofing | `hping3 -a` | Fausse identité IP | Filtrage par IP |
| MAC Spoofing | `--spoof-mac` | Fausse identité MAC | Filtrage par MAC |
| Random Host Order | `--randomize-hosts` | Éviter détection pattern | Monitoring réseau |
| Bad Checksums | `--badsum` | Tester config IDS/FW | Vérification checksums |
| Proxy/Anonymizer | — | Cacher identité réelle | Tracing IP |

---

[← OS Discovery](./05_os_discovery_banner_grabbing.md) | [→ Contre-mesures](./07_contre_mesures.md)
