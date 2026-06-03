# 🔴 Module 12 — Techniques d'Évasion des IDS

> **CEH v13 | EC-Council**  
> `#Fragmentation` `#TTL` `#SessionSplicing` `#Polymorphisme` `#Decoy`

---

## 📌 Table des matières

1. [Fragmentation des paquets IP](#1-fragmentation-ip)
2. [Session Splicing](#2-session-splicing)
3. [Manipulation du TTL](#3-manipulation-ttl)
4. [Encodage et obfuscation](#4-encodage-et-obfuscation)
5. [Insertion et Évasion (Ptacek & Newsham)](#5-insertion-et-évasion)
6. [Slow-Rate Attacks](#6-slow-rate-attacks)
7. [Scans furtifs avec Nmap](#7-scans-furtifs-nmap)

---

## 1. Fragmentation des paquets IP

L'IDS doit réassembler tous les fragments pour analyser le contenu. Si le réassemblage échoue ou diffère du comportement de la cible, l'attaque passe.

```
Paquet malveillant normal :
[IP Header][TCP Header][ATTAQUE_PAYLOAD_COMPLET]
→ IDS détecte la signature

Paquet fragmenté :
Fragment 1 : [IP Frag 1][ATTAQUE_PAY]
Fragment 2 : [IP Frag 2][LOAD_COMPLE]
Fragment 3 : [IP Frag 3][T]

→ IDS réassemble : ok, détecte
→ Si timeout IDS < timeout cible : IDS abandonne, cible réassemble → ATTAQUE PASSE
```

### Avec Nmap

```bash
# Fragmentation des paquets de scan
nmap -f target.com          # Fragmente en paquets de 8 bytes
nmap -ff target.com         # Fragmente en paquets de 16 bytes
nmap --mtu 24 target.com    # Fragment size personnalisé (multiple de 8)
```

---

## 2. Session Splicing

Diviser le payload HTTP sur plusieurs paquets TCP, espérant que l'IDS n'effectue pas le suivi d'état complet.

```
Requête normale :
Paquet TCP : "GET /cgi-bin/attack.sh HTTP/1.1"
→ IDS voit la signature complète → ALERTE

Session splicing :
Paquet 1 : "GE"
Paquet 2 : "T /"
Paquet 3 : "cgi"
Paquet 4 : "-bin/attack.sh HTTP/1.1"

→ IDS superficiel : ne reconnaît pas la signature fragmentée
→ IDS avec suivi d'état TCP : reconstitue et alerte
```

### Outil : Fragroute

```bash
# fragroute fragmente et réordonne les paquets
fragroute -f /etc/fragroute.conf target_ip

# Fichier de config fragroute.conf :
ip_frag 16        # Fragmentation IP 16 bytes
tcp_seg 8         # Segmentation TCP 8 bytes
order random      # Ordre aléatoire des fragments
dup first 10%     # Dupliquer 10% des paquets
```

---

## 3. Manipulation du TTL

Exploiter la différence de TTL entre l'IDS et la cible pour envoyer des données "vues" par l'IDS mais ignorées par la cible, ou vice-versa.

```
Réseau :
Attaquant ──(TTL=5)──▶ [Router] ──(TTL=4)──▶ [IDS: TTL=3]──▶ [Cible: TTL=2]

Technique :
1. Envoyer un faux paquet avec TTL assez grand pour atteindre l'IDS
   mais PAS assez pour atteindre la cible :
   
   Faux_Paquet(TTL=3, payload="TRAFIC LÉGITIME") → Atteint IDS, pas la cible
   
2. L'IDS voit "TRAFIC LÉGITIME" et construit une vue falsifiée du flux
3. Envoi du vrai paquet (TTL normal) → L'IDS a déjà une vue corrompue

Résultat : IDS "croit" voir du trafic normal, la cible reçoit le payload malveillant
```

---

## 4. Encodage et Obfuscation

### 4.1 URL Encoding

```
Payload non encodé : /cgi-bin/../../etc/passwd
IDS signature : "/../" → ALERTE

Payload encodé :
%2F%63%67%69%2D%62%69%6E%2F..%2F..%2F%65%74%63%2F%70%61%73%73%77%64
→ IDS ne reconnaît pas si signatures non normalisées
→ Serveur décode et exécute
```

### 4.2 Double URL Encoding

```
/ → %2F → %252F
. → %2E → %252E

Payload double encodé : %252F%252E%252E%252F%252E%252E%252Fetc%252Fpasswd
→ Certains IDS ne font qu'un seul niveau de décodage
```

### 4.3 Unicode Encoding

```
/ peut s'écrire :
%c0%af (overlong UTF-8)
%e0%80%af (sur-long UTF-8 3 bytes)
→ IIS historique acceptait ces encodages
→ IDS ne reconnaissait pas la signature
```

### 4.4 Case Variation

```
GET /index.php   → signature connue
GeT /InDeX.PhP   → contourne les IDS case-sensitive

SELECT → SeLeCt → selECT
<script> → <ScRiPt> → <SCRIPT>
```

### 4.5 Commentaires SQL / Insertion de caractères

```
SELECT * FROM users  → IDS détecte
SE/**/LECT/**/ * /**/FROM/**/users  → Injection de commentaires SQL
SE%00LECT  → Null byte entre caractères
```

---

## 5. Insertion et Évasion (Ptacek & Newsham 1998)

Deux techniques fondamentales décrites dans le papier fondateur de l'évasion IDS.

### 5.1 Insertion Attack

```
L'attaquant envoie des paquets que l'IDS accepte mais que la cible REJETTE.

Résultat : L'IDS construit une fausse représentation du flux.

Exemple :
IDS reçoit : "ATTACK" (avec TTL assez grand)
Cible reçoit : "ATTACK" SANS les paquets parasites (TTL trop faible)
→ IDS croit voir du trafic différent
```

### 5.2 Evasion Attack

```
L'attaquant envoie des paquets que la cible accepte mais que l'IDS IGNORE.

Exemple : Fragments reassemblés différemment par IDS vs OS cible
→ IDS voit "ABCD" ; cible reconstruit "A_TTACK"
```

---

## 6. Slow-Rate Attacks

Les IDS ont des fenêtres de corrélation temporelle. Envoyer les paquets très lentement peut contourner les seuils d'alerte.

```
Scan normal :
t=0s   : Port 80 → SYN
t=0.1s : Port 443 → SYN
t=0.2s : Port 22 → SYN
→ IDS : "Scan de port détecté" (100+ SYN en <1s)

Slow scan :
t=0s   : Port 80  → SYN
t=300s : Port 443 → SYN  (5 minutes plus tard)
t=600s : Port 22  → SYN
→ IDS : sous le seuil de détection (1 SYN / 5 min)
```

```bash
# Nmap slow scan :
nmap -T0 target.com    # Paranoid : 1 paquet toutes les 5 minutes
nmap -T1 target.com    # Sneaky   : 1 paquet toutes les 15 secondes
```

---

## 7. Scans Furtifs avec Nmap

```bash
# Décoy scan (masquer l'IP source avec leurres)
nmap -D RND:10 target.com        # 10 IPs aléatoires comme leurres
nmap -D decoy1,decoy2,ME target.com  # Leurres spécifiques

# Fragmentation
nmap -f target.com               # Paquets de 8 bytes

# Spoofer le port source (utiliser un port autorisé par le firewall)
nmap --source-port 53 target.com # Source port DNS

# Modifier le TTL
nmap --ttl 128 target.com

# Scan lent
nmap -T0 -p 80,443,22 target.com

# Combiné
nmap -f -T1 -D RND:5 --source-port 53 target.com
```

---

## 🧠 Points clés à retenir pour l'examen

- **Fragmentation** = diviser les paquets pour que l'IDS ne voit pas la signature complète
- **Session splicing** = même principe mais au niveau TCP
- **TTL manipulation** = exploiter la différence de topologie entre IDS et cible
- **Insertion attack** = envoyer des paquets acceptés par l'IDS, rejetés par la cible
- **Évasion attack** = envoyer des paquets rejetés par l'IDS, acceptés par la cible
- `nmap -D` = décoy scan (leurres)
- `nmap -f` = fragmentation
- `nmap -T0` = scan ultra-lent (IDS ne corrèle pas)

---

*⬅️ [Concepts IDS/Firewall](./01_concepts_ids_firewall.md) | ➡️ [Évasion Firewall & Honeypot](./03_evasion_firewall_honeypot.md)*
