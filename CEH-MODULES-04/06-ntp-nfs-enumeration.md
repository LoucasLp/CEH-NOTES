# ⏱️ 06 — Énumération NTP & NFS

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

# PARTIE 1 — NTP Enumeration

## 📖 Présentation de NTP

**NTP** (Network Time Protocol) synchronise les horloges des ordinateurs en réseau.

| Attribut | Valeur |
|----------|--------|
| **Port** | UDP **123** |
| **Précision Internet** | ±10 ms |
| **Précision LAN** | ±200 µs (conditions idéales) |

> ⚠️ **Souvent négligé** par les admins lors des audits de sécurité, mais peut révéler des informations précieuses !

---

## 🎯 Informations extraites via NTP

Un attaquant peut obtenir :
- 📋 Liste des hôtes connectés au serveur NTP
- 🌐 Adresses IP des clients, noms de systèmes, OSs
- 🏠 IPs internes si le serveur NTP est en **DMZ**

---

## 🔧 Commandes d'énumération NTP

### ntpdate
Collecte des échantillons de temps de plusieurs sources.

```bash
ntpdate [-46bBdqsuv] [-a key] [-e authdelay] [-k keyfile] \
        [-o version] [-p samples] [-t timeout] [-U user_name] server [...]
```

**Paramètres clés :**

| Paramètre | Description |
|-----------|-------------|
| `-4` | Force la résolution DNS vers IPv4 |
| `-6` | Force la résolution DNS vers IPv6 |
| `-a key` | Active l'authentification avec une clé |
| `-B` | Force le décalage (slew) du temps |
| `-b` | Force le saut (step) du temps |
| `-d` | Active le mode débogage |
| `-e authdelay` | Spécifie le délai de traitement auth |
| `-k keyfile` | Chemin du fichier de clés auth (défaut: /etc/ntp/keys) |
| `-o version` | Version NTP pour les paquets sortants (1 ou 2, défaut: 4) |
| `-p samples` | Nombre d'échantillons par serveur (1-8, défaut: 4) |
| `-q` | Requête uniquement, ne pas ajuster l'horloge |
| `-s` | Redirige la sortie vers syslog |
| `-t timeout` | Temps max d'attente réponse serveur (défaut: 1s) |
| `-u` | Utilise un port non privilégié |
| `-v` | Verbeux — logs la chaîne de version ntpdate |

---

### ntptrace
Détermine la source de temps du serveur NTP en remontant la chaîne jusqu'à la source primaire.

```bash
ntptrace [-n] [-m maxhosts] [servername/IP_address]
```

| Paramètre | Description |
|-----------|-------------|
| `-n` | N'affiche pas les hostnames, seulement les IPs |
| `-m maxhosts` | Définit le nombre maximum de niveaux à remonter |

**Exemple de sortie :**
```
# ntptrace
localhost: stratum 4, offset 0.0019529, synch distance 0.143235
10.10.0.1: stratum 2, offset 0.0114273, synch distance 0.115554
10.10.1.1: stratum 1, offset 0.0017698, synch distance 0.011193
```

> 💡 Révèle la **topologie complète** des serveurs NTP jusqu'à la source primaire !

---

### ntpdc
Interroge le daemon ntpd sur son état actuel.

```bash
ntpdc [-46dilnps] [-c command] [hostname/IP_address]
```

| Paramètre | Description |
|-----------|-------------|
| `-4` | Force IPv4 |
| `-6` | Force IPv6 |
| `-d` | Active le débogage |
| `-c` | Commande au format interactif |
| `-i` | Mode interactif |
| `-l` | Liste des peers connus (équivalent -c listpeers) |
| `-n` | Format numérique pour les adresses |
| `-p` | Liste des peers + résumé de leurs états |
| `-s` | Liste des peers (format légèrement différent) |

---

### ntpq
Surveille les opérations du daemon NTP et ses performances.

```bash
ntpq [-46dinp] [-c command] [host/IP_address]
```

| Paramètre | Description |
|-----------|-------------|
| `-4` | Force IPv4 |
| `-6` | Force IPv6 |
| `-c` | Commande interactive |
| `-d` | Débogage |
| `-i` | Mode interactif |
| `-n` | Format numérique |
| `-p` | Liste des peers + résumé |

**Exemple :**
```
ntpq> version
ntpq 4.2.8p15@1.3728-o
ntpq> host
current host is localhost
```

> 📝 **Note** : Dans de nombreuses distributions Linux, le daemon `ntpd` a été remplacé par **Chrony/chronyd**.

---

## 🛠️ Outils NTP

| Outil | URL | Usage |
|-------|-----|-------|
| **PRTG Network Monitor** | https://www.paessler.com | Surveillance SNTP/NTP, temps de réponse, synchronisation |
| **Nmap** | https://nmap.org | Scan NTP |
| **Wireshark** | https://www.wireshark.org | Analyse du trafic NTP |
| **udp-proto-scanner** | https://labs.portcullis.co.uk | Scanner UDP |
| **NTP Server Scanner** | http://www.bytefusion.com | Scan serveurs NTP |

---

---

# PARTIE 2 — NFS Enumeration

## 📖 Présentation de NFS

**NFS** (Network File System) permet aux utilisateurs d'accéder, afficher, stocker et mettre à jour des fichiers sur un serveur distant comme s'ils étaient locaux.

| Attribut | Valeur |
|----------|--------|
| **Port** | TCP **2049** |
| **Protocole** | RPC (Remote Procedure Call) |
| **Config** | `/etc/exports` sur le serveur NFS |
| **Authentification** | Adresse IP du client uniquement (NFS < v4) |

---

## 🔄 Fonctionnement NFS

```
┌─────────────────────────────────────────────────────────┐
│                    RÉSEAU NFS                           │
│                                                         │
│  Client                    Serveur NFS                  │
│    │                           │                        │
│    ├─1─► "mounting" process ──►│ (/etc/exports)         │
│    │                           │  → vérifie IP client   │
│    ├─2─► Accès accordé        │                        │
│    │                           │                        │
│    └─3─► Lecture/Écriture ◄──►│ (si permissions OK)    │
└─────────────────────────────────────────────────────────┘
```

- **Exporting** : processus de partage des fichiers/répertoires
- **Mounting** : processus côté client pour rendre le partage accessible
- `/etc/exports` : liste des clients autorisés à partager des fichiers

---

## 🎯 Informations extraites via NFS

- 📁 Répertoires exportés
- 🌐 Liste des clients connectés + leurs IPs
- 📊 Données partagées associées aux IPs

> 🔴 **Attaque** : Après énumération, l'attaquant peut **usurper une IP** pour accéder aux fichiers partagés !

---

## 💻 Commandes d'énumération NFS

### Scanner les ports NFS ouverts
```bash
rpcinfo -p <Target IP>
```

**Sortie typique :**
```
program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100005    1   udp   2049  mountd
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
```

---

### Lister les partages disponibles
```bash
showmount -e <Target IP>
```

**Sortie typique :**
```
Export list for 192.168.1.100:
/data        *
/home/shared 192.168.1.0/24
/backup      192.168.1.50
```

---

## 🛠️ Outils d'énumération NFS

### RPCScan
> Source : https://github.com

Communique avec les services RPC et vérifie les mauvaises configurations sur les partages NFS.

```bash
python3 rpc-scan.py <Target IP> --rpc
```

---

### SuperEnum
> Source : https://github.com

Effectue l'énumération de base de n'importe quel port ouvert.

```bash
# Lancer le script
./superenum

# Entrer le fichier cible
> Target.txt
```

Affiche tous les ports ouverts, y compris le **port 2049** avec le service NFS.

---

## ⚡ Flux d'attaque NFS typique

```
1. Scanner les services RPC/NFS
   rpcinfo -p <IP>
          ↓
2. Lister les partages exportés
   showmount -e <IP>
          ↓
3. Identifier les permissions (lecture/écriture)
          ↓
4. Usurpation d'IP cliente autorisée
          ↓
5. Montage du partage et accès aux fichiers
   mount -t nfs <IP>:/exported/path /mnt/local
          ↓
6. Upload de malware / backdoor
```

---

## 🎯 Synthèse NTP & NFS

| Protocole | Port | Risque principal | Outil clé |
|-----------|------|-----------------|-----------|
| **NTP** | UDP 123 | Fuite infos réseau, IPs internes | ntptrace, ntpq |
| **NFS** | TCP 2049 | Accès fichiers via IP spoofing | showmount, rpcinfo |

---

[← LDAP](./05-ldap-enumeration.md) | [→ SMTP & DNS](./07-smtp-dns-enumeration.md)
