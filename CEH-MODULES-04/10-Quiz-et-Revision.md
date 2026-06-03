# 📝 Module 04 — Quiz & Révision : Énumération

> **CEH v13 | EC-Council**  
> `#Quiz` `#Enumeration` `#NetBIOS` `#SNMP` `#LDAP` `#DNS` `#ExamTips`

---

## 🔍 Récapitulatif rapide

- Énumération = **connexions ACTIVES** avec la cible pour extraire des informations précises
- Différence avec scanning : le scanning identifie les hôtes/ports ; l'enum extrait les **données**
- Protocoles clés : NetBIOS (137-139), SNMP (161), LDAP (389), SMTP (25), DNS (53/AXFR), SMB (445)
- **SNMP community strings** = équivalent des mots de passe SNMP ("public", "private")
- **DNS zone transfer** = récupérer tous les enregistrements DNS internes d'une organisation

---

## 🧠 Concepts clés

| Protocole | Port | Outil clé | Risque |
|-----------|------|-----------|--------|
| **NetBIOS** | 137-139/TCP-UDP | `nbtstat -a` | Noms d'hôtes, shares |
| **SMB** | 445/TCP | `enum4linux`, `smbclient` | Shares, users, policies |
| **SNMP** | 161/UDP | `snmpwalk` | Config réseau, users, routes |
| **LDAP** | 389/TCP | `ldapsearch` | Utilisateurs AD, structure OU |
| **SMTP** | 25/TCP | `smtp-user-enum` | Valider les noms d'utilisateurs |
| **DNS (Zone Transfer)** | 53/TCP | `dig AXFR` | Toute l'infrastructure interne |
| **NFS** | 2049/TCP | `showmount` | Partages NFS montables |
| **RPC** | 111/TCP | `rpcinfo` | Services RPC exposés |

---

## 🎯 Exam Tips

> 📌 **Points récurrents à l'examen CEH :**

1. **Énumération ≠ Scanning** : Enum = connexion active, extraction de données ; Scan = découverte
2. **SNMP community strings** : "public" (lecture) et "private" (écriture) — à changer !
3. **DNS Zone Transfer** : `dig @nameserver domain AXFR` — très dangereux si non sécurisé
4. **NetBIOS `nbtstat -a IP`** = obtenir le nom NetBIOS, le domaine, l'adresse MAC
5. **LDAP `ldapsearch`** = énumérer les utilisateurs et OUs d'un Active Directory
6. **`smtp-user-enum -M VRFY`** = vérifier si un utilisateur existe sur le serveur SMTP
7. **Null Session** (Windows): connexion anonyme aux partages IPC$ → extract utilisateurs
8. **enum4linux** = outil tout-en-un pour SMB/NetBIOS/SNMP enumération Linux
9. **MIB** (Management Information Base) = base de données des objets SNMP gérables

---

## ❓ Questions de révision

1. Quelle est la différence entre scanning et énumération ?
2. Expliquez ce qu'est une SNMP community string et pourquoi "public" est dangereux.
3. Comment réaliser un transfert de zone DNS et qu'est-ce qu'on peut y trouver ?
4. Qu'est-ce qu'une null session et pourquoi est-elle dangereuse ?
5. Quelles informations peut-on extraire via `nbtstat -a <IP>` ?
6. Comment `ldapsearch` est-il utilisé pour énumérer un Active Directory ?
7. Pourquoi le port SNMP utilise-t-il UDP 161 et non TCP ?
8. Quelle contre-mesure simple empêche l'énumération SNMP ?

---

## 📋 Quiz QCM

**Question 1.** Quelle commande réalise une énumération NetBIOS d'une machine Windows distante ?
- a) `nmap -sN target`
- b) `nbtstat -a target_IP`
- c) `ldapsearch -h target`
- d) `snmpwalk -c public target`

**Question 2.** Le port UDP 161 est associé à quel protocole d'énumération ?
- a) NetBIOS
- b) LDAP
- c) SNMP
- d) DNS

**Question 3.** La commande `dig @ns.target.com target.com AXFR` tente de :
- a) Pinger le serveur DNS
- b) Résoudre le nom d'hôte target.com
- c) Effectuer un transfert de zone DNS complet
- d) Changer les NS records de target.com

**Question 4.** La SNMP community string "public" permet par défaut :
- a) L'accès en lecture seule aux informations SNMP
- b) L'accès en lecture/écriture aux informations SNMP
- c) L'authentification SSH sur le device
- d) La configuration VLAN du switch

**Question 5.** Quel outil Linux est le plus adapté pour une énumération complète SMB/NetBIOS ?
- a) theHarvester
- b) nikto
- c) enum4linux
- d) hping3

**Question 6.** Le port LDAP est le :
- a) 25/TCP
- b) 161/UDP
- c) 389/TCP
- d) 445/TCP

**Question 7.** Une "null session" NetBIOS permet à un attaquant de :
- a) Cracker les mots de passe des partages
- b) Établir une connexion anonyme à IPC$ et extraire des informations sur les users/shares
- c) Injecter du code malveillant dans les partages NetBIOS
- d) Effectuer un ARP spoofing sur le réseau local

**Question 8.** La commande `smtp-user-enum -M VRFY -U users.txt -t target.com` vise à :
- a) Envoyer des spams au serveur cible
- b) Vérifier quels noms d'utilisateurs existent sur le serveur SMTP
- c) Brute-forcer les mots de passe SMTP
- d) Lister les domaines acceptés par le serveur

---

## ✅ Points de révision — Checklist

- [ ] Je comprends la différence entre scanning et énumération
- [ ] Je connais les ports clés : NetBIOS (137-139), SMB (445), SNMP (161), LDAP (389)
- [ ] Je sais utiliser `nbtstat -a` pour l'énumération NetBIOS
- [ ] Je comprends le risque des SNMP community strings par défaut
- [ ] Je sais effectuer un zone transfer DNS (`dig AXFR`)
- [ ] Je connais la commande `ldapsearch` pour l'Active Directory
- [ ] Je comprends ce qu'est une null session et ses risques
- [ ] Je sais ce que fait `enum4linux`

---

## 🏆 Réponses QCM

| Q | Réponse | Justification |
|---|---------|---------------|
| 1 | **b** | `nbtstat -a IP` = enum NetBIOS distante |
| 2 | **c** | SNMP = UDP 161 |
| 3 | **c** | AXFR = Zone Transfer DNS |
| 4 | **a** | "public" = lecture seule (read-only community) |
| 5 | **c** | enum4linux = outil SMB/NetBIOS complet |
| 6 | **c** | LDAP = TCP 389 |
| 7 | **b** | Null session IPC$ = connexion anonyme pour extraire infos |
| 8 | **b** | VRFY = vérifier si l'utilisateur existe |

---

*⬅️ [Countermeasures](./09-countermeasures.md) | ➡️ [README Module 04](./README.md)*
