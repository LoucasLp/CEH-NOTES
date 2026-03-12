# 🛡️ 09 — Contre-mesures d'Énumération

> **CEH v13 | Module 4** | [← Retour au sommaire](./README.md)

---

## 📊 Vue d'ensemble

```
┌─────────────────────────────────────────────────────────┐
│               CONTRE-MESURES PAR PROTOCOLE              │
├──────────────┬──────────────────────────────────────────┤
│  SNMP        │  Désactiver, changer strings, SNMPv3     │
│  LDAP        │  SSL/TLS, désactiver anon bind, MFA      │
│  NFS         │  Permissions, firewall port 2049         │
│  SMTP        │  Désactiver VRFY/EXPN, rate limiting     │
│  DNS         │  Restreindre zone transfer, DNSSEC       │
│  SMB         │  Désactiver SMBv1, bloquer 139/445       │
└──────────────┴──────────────────────────────────────────┘
```

---

## 📡 SNMP — Contre-mesures

### Actions immédiates

| Priorité | Action |
|----------|--------|
| 🔴 Haute | Supprimer l'agent SNMP ou désactiver le service si inutile |
| 🔴 Haute | **Changer les community strings par défaut** (public/private) |
| 🔴 Haute | **Migrer vers SNMPv3** (chiffrement des mots de passe et messages) |
| 🟡 Moyenne | Bloquer TCP/UDP port **161** |
| 🟡 Moyenne | Configurer des ACLs restrictives pour les connexions SNMP |

### Configuration avancée
```
✅ Implémenter la politique "Additional restrictions for anonymous connections"
✅ Restreindre l'accès aux null session pipes/shares et filtrage IPsec
✅ Ne pas installer les composants Windows de gestion/monitoring si non requis
✅ Chiffrer/authentifier via IPsec
✅ Ne pas configurer SNMP avec autorisation read-write inutilement
✅ Limiter l'accès SNMP aux seules IPs légitimes
✅ Utiliser le mode "AuthNoPriv" (MD5/SHA) pour chiffrer les credentials
✅ Modifier le registre pour restreindre l'accès au nom de communauté SNMP
✅ Changer le mot de passe par défaut et le changer périodiquement
✅ Identifier les équipements R/W et leur donner un accès lecture seule si possible
✅ Éviter le mode "NoAuthNoPriv" (aucun chiffrement)
✅ Implémenter les politiques RBAC (Role-Based Access Control)
✅ Configurer SNMPv3 avec chiffrement et authentification
✅ Pour SNMPv1/v2c : changer les community strings et restreindre l'écriture
✅ Isoler le trafic de gestion sur un VLAN sécurisé séparé
✅ Appliquer régulièrement les mises à jour des implémentations SNMP
✅ Implémenter la détection d'anomalies sur le trafic SNMP
✅ Logger les accès SNMP et auditer régulièrement
```

---

## 📂 LDAP — Contre-mesures

### Actions immédiates

| Priorité | Action |
|----------|--------|
| 🔴 Haute | **Chiffrer le trafic LDAP avec SSL ou STARTTLS** |
| 🔴 Haute | **Désactiver les liaisons anonymes** (anonymous binds) |
| 🔴 Haute | Activer le verrouillage de compte |

### Configuration avancée
```
✅ Utiliser un nom d'utilisateur différent de l'email
✅ Restreindre l'accès AD via des logiciels comme Citrix
✅ Utiliser NTLM, Kerberos ou un mécanisme d'authentification basique
✅ Logger les accès aux services Active Directory
✅ Modifier les permissions sur les objets/attributs AD sensibles
✅ Déployer des comptes "canary" pour détecter les attaquants
✅ Créer des groupes leurres contenant "Admin" pour tromper les attaquants
✅ Activer l'authentification multi-facteurs (MFA) pour les annuaires LDAP
✅ Configurer des ACLs pour limiter ce que les utilisateurs peuvent voir/faire
✅ Logger et auditer toutes les requêtes et modifications LDAP
✅ Déployer des outils de monitoring pour détecter les patterns anormaux
✅ Placer les serveurs LDAP dans un segment réseau sécurisé
✅ Configurer les pare-feux pour restreindre l'accès LDAP
✅ Appliquer des politiques de mots de passe forts
```

---

## 📁 NFS — Contre-mesures

### Actions immédiates

| Priorité | Action |
|----------|--------|
| 🔴 Haute | **Bloquer le port NFS 2049 via firewall** |
| 🔴 Haute | Implémenter des permissions correctes (R/W restreint aux utilisateurs spécifiques) |

### Configuration des fichiers
```bash
# Revoir et mettre à jour /etc/exports
# Autoriser uniquement les hôtes légitimes

# Utiliser /etc/hosts.allow et /etc/hosts.deny
# pour contrôler l'accès aux services NFS

# Garder l'option root_squash ACTIVÉE dans /etc/exports
# → Les requêtes root client ne sont PAS de confiance
```

### Configuration avancée
```
✅ Assurer la configuration correcte de /etc/smb.conf, /etc/exports, /etc/hosts.allow
✅ Logger les demandes d'accès aux fichiers système NFS
✅ Garder root_squash ON dans /etc/exports
✅ Implémenter le tunneling NFS via SSH (chiffrement du trafic)
✅ Appliquer le principe du moindre privilège
✅ Ne pas exécuter suid et sgid sur le système de fichiers exporté
✅ S'assurer que NIS netgroup a un hostname pleinement défini
✅ Configurer un pare-feu DPI pour monitorer tout le trafic NFS
✅ Implémenter l'authentification Kerberos pour NFS
✅ Migrer vers NFSv4 (support Kerberos, chiffrement renforcé)
✅ Segmenter les serveurs NFS dans la partie sécurisée du réseau
✅ Monitorer les logs d'accès NFS pour les patterns inhabituels
✅ Utiliser des outils d'audit du système de fichiers
✅ Mettre à jour et patcher régulièrement les logiciels NFS
```

---

## 📧 SMTP — Contre-mesures

### Actions immédiates

| Priorité | Action |
|----------|--------|
| 🔴 Haute | **Désactiver EXPN, VRFY et RCPT TO** (ou restreindre aux utilisateurs authentifiés) |
| 🔴 Haute | Désactiver le **relais ouvert** (open relay) |
| 🔴 Haute | Ignorer les emails adressés aux destinataires inconnus |

### Configuration avancée
```
✅ Ignorer les messages email aux destinataires inconnus
✅ Exclure les infos sensibles des réponses des serveurs mail
✅ Limiter le nombre de connexions acceptées d'une source (anti brute-force)
✅ Ne pas partager les infos d'IP internes ou de relay system
✅ Implémenter SPF (Sender Policy Framework)
✅ Implémenter DKIM (DomainKeys Identified Mail)
✅ Implémenter DMARC (Domain-based Message Authentication)
✅ Configurer le serveur pour fournir un minimum d'infos dans les erreurs
✅ Utiliser des ACLs pour restreindre les commandes SMTP
✅ Exiger l'authentification avant l'accès aux informations
✅ Utiliser TLS pour chiffrer les communications SMTP
✅ Logger les tentatives d'accès et les commandes
✅ Analyser les logs pour détecter les comportements suspects (connexions en masse)
✅ Utiliser des pare-feux pour contrôler l'accès au serveur SMTP
✅ Appliquer le rate limiting par adresse IP
✅ Identifier les spammeurs via des solutions de Machine Learning
```

---

## 🌐 DNS — Contre-mesures

### Actions immédiates

| Priorité | Action |
|----------|--------|
| 🔴 Haute | **Restreindre les transferts de zone DNS** aux IPs de serveurs secondaires légitimes |
| 🔴 Haute | Désactiver les transferts de zone vers les hôtes non fiables |
| 🔴 Haute | **Désactiver la récursion DNS** si non nécessaire |

### Liste complète
```
✅ Restreindre l'accès au resolver aux hôtes internes uniquement
✅ Randomiser les ports sources (plutôt que UDP 53 fixe)
✅ Randomiser les query IDs et la casse des noms de domaine (anti cache poisoning)
✅ Auditer les zones DNS régulièrement
✅ Mettre à jour et patcher les serveurs de noms (BIND, Microsoft DNS)
✅ Monitorer le comportement des serveurs de noms
✅ Utiliser des serveurs différents pour les fonctions autoritatives et résolvantes
✅ Utiliser des serveurs DNS isolés (ne pas héberger avec le serveur applicatif)
✅ Durcir l'OS (fermer les ports inutilisés)
✅ Utiliser un VPN pour les communications sécurisées
✅ Implémenter l'authentification à deux facteurs
✅ Utiliser DNS change lock / client lock
✅ Implémenter DNSSEC (signatures numériques)
✅ Utiliser des services DNS premium qui cachent les infos sensibles (HINFO)
✅ Utiliser DNS-over-HTTPS (DoH) ou DNS-over-TLS (DoT)
✅ Activer le logging DNS et monitorer régulièrement
✅ Implémenter la détection d'anomalies sur les requêtes DNS
✅ Configurer le rate limiting sur les requêtes DNS
✅ Architecture DNS split (interne/externe séparés)
✅ Minimiser les informations DNS exposées publiquement
✅ Ne pas publier les IPs des hôtes privés dans les fichiers de zone publics
✅ Utiliser des contacts admin réseau standard pour les enregistrements DNS
✅ Supprimer les enregistrements DNS obsolètes
✅ Maintenir des serveurs DNS internes et externes indépendants
✅ Déployer des pare-feux DNS (DNS Firewalls)
✅ Réviser et auditer périodiquement les configurations DNS
```

---

## 🖥️ SMB — Contre-mesures

### Actions immédiates

| Priorité | Action |
|----------|--------|
| 🔴 Haute | **Désactiver SMBv1** (vulnérable, EternalBlue) |
| 🔴 Haute | **Bloquer TCP 139, TCP/UDP 445** sur les serveurs accessibles depuis Internet |
| 🔴 Haute | Activer Windows Firewall / endpoint protection |

### Désactiver SMB
```
Méthode 1 : Désactiver dans "Connexions réseau"
  → Désactiver "Client for Microsoft Networks"
  → Désactiver "File and Printer Sharing for Microsoft Networks"

Méthode 2 : Bloquer les ports TCP 139 et 445 (bastion hosts)
```

### Configuration du Registre Windows
```
Restreindre les sessions null (accès anonyme) :
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters
  → RestrictNullSessAccess = 1 (activé / restreint)
  → RestrictNullSessAccess = 0 (désactivé)
```

### Configuration avancée
```
✅ Installer les derniers patches de sécurité Windows et tiers
✅ Implémenter une authentification forte avec politique de mots de passe robuste
✅ Implémenter des permissions fortes pour les données stockées
✅ Effectuer des audits réguliers des logs système
✅ Monitoring actif pour les incidents malveillants
✅ Implémenter des VPNs sécurisés pour l'accès distant
✅ Utiliser des systèmes d'analyse comportementale (NGFW) pour le trafic SMB
✅ Utiliser des systèmes de monitoring robustes (global threat sensors) pour les données sensibles
✅ Implémenter la transmission numérique signée pour les ressources SMB
✅ Bloquer/désactiver TCP 88, 139, 445 et UDP 88, 137, 138
✅ Activer les paramètres de profil public dans le pare-feu
✅ Bloquer/désactiver SMB pour les serveurs exposés sur Internet
✅ S'assurer que tous les systèmes utilisent SMBv3 ou supérieur
✅ Configurer des ACLs pour restreindre l'accès aux partages SMB
✅ Appliquer le principe du moindre privilège
✅ Logger les tentatives d'accès et changements aux ressources partagées
```

---

## 🎯 Tableau récapitulatif final

| Protocole | Port(s) | Action prioritaire | Version sécurisée |
|-----------|---------|-------------------|-------------------|
| **SNMP** | 161/162 | Migrer vers SNMPv3, changer strings | SNMPv3 |
| **LDAP** | 389/636 | SSL/TLS + désactiver anon bind | LDAPS (636) |
| **NFS** | 2049 | Bloquer firewall + Kerberos | NFSv4 + Kerberos |
| **SMTP** | 25/587 | Désactiver VRFY/EXPN + TLS | SMTP+TLS (587) |
| **DNS** | 53 | Restreindre zone transfer + DNSSEC | DoH/DoT |
| **SMB** | 445/139 | Désactiver SMBv1 + bloquer ports | SMBv3 + signatures |

---

> 💡 **Principe fondamental** : La **défense en profondeur** (layered security) est la meilleure approche — aucune contre-mesure unique ne suffit. Combiner : pare-feu, ACLs, chiffrement, authentification forte, monitoring et patching régulier.

---

[← Autres Enumerations](./08-other-enumeration.md) | [↑ Retour au sommaire](./README.md)
