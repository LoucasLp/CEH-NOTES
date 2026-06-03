# 📚 CEH v13 — Module 04 : Énumération (Enumeration)

> **Certified Ethical Hacker v13 — AI Powered**  
> EC-Council | Formation officielle

---

## 🗂️ Structure des notes

| Fichier | Contenu |
|--------|---------|
| [`01-introduction.md`](./01-introduction.md) | Définition, objectifs, techniques d'énumération, différence scanning/enum |
| [`02-ports-services.md`](./02-ports-services.md) | TCP/UDP, protocoles et ports clés à connaître |
| [`03-netbios-enumeration.md`](./03-netbios-enumeration.md) | NetBIOS, Nbtstat, enum4linux, outils |
| [`04-snmp-enumeration.md`](./04-snmp-enumeration.md) | SNMP, MIB, community strings, snmpwalk, outils |
| [`05-ldap-enumeration.md`](./05-ldap-enumeration.md) | LDAP, Active Directory, ldapsearch, outils |
| [`06-ntp-nfs-enumeration.md`](./06-ntp-nfs-enumeration.md) | NTP commands, NFS, RPCScan |
| [`07-smtp-dns-enumeration.md`](./07-smtp-dns-enumeration.md) | SMTP user enum, DNS zone transfer, DNSSEC, cache snooping |
| [`08-other-enumeration.md`](./08-other-enumeration.md) | IPsec/IKE, VoIP/SIP, RPC, SMB, Unix/Linux enum |
| [`09-countermeasures.md`](./09-countermeasures.md) | Contre-mesures par protocole (NetBIOS, SNMP, LDAP, DNS...) |
| [`10-Quiz-et-Revision.md`](./10-Quiz-et-Revision.md) | Questions, rappels et exercices rapides |

---

## 🎯 Objectifs du module

À l'issue de ce module, vous serez capable de :

- ✅ Définir l'énumération et la différencier du scanning
- ✅ Identifier les services et ports vulnérables à l'énumération
- ✅ Effectuer une énumération NetBIOS, SNMP, LDAP, NTP, SMTP, DNS
- ✅ Utiliser les outils : Nbtstat, snmpwalk, ldapsearch, enum4linux, dig
- ✅ Réaliser un transfert de zone DNS (zone transfer)
- ✅ Appliquer les contre-mesures appropriées par protocole

---

## 📊 Poids dans l'examen CEH (312-50)

```
Module 04 — Enumeration
Poids estimé : ~3-5% des questions de l'examen
Nb de labs officiels : ~6 labs dans la plateforme iLabs/CyberQ
Thèmes récurrents : SNMP community strings, NetBIOS Nbtstat,
                    DNS zone transfer (AXFR), LDAP ldapsearch,
                    ports à connaître (137/139/161/389/445)
```

---

## 🔗 Ressources complémentaires

- [EC-Council Official CEHv13](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/)
- [CyberQ Platform (Labs)](https://cyberq.eccouncil.org)
- [Nmap NSE Scripts](https://nmap.org/nsedoc/)
- [dnsrecon (GitHub)](https://github.com/darkoperator/dnsrecon)
- [enum4linux-ng (GitHub)](https://github.com/cddmp/enum4linux-ng)

---

## 🧠 Mémo rapide — Ports clés à l'énumération

```
NetBIOS   : 137/UDP (Name), 138/UDP (Datagram), 139/TCP (Session)
SMB       : 445/TCP (Direct)
SNMP      : 161/UDP (queries), 162/UDP (traps)
LDAP      : 389/TCP, 3268/TCP (Global Catalog)
SMTP      : 25/TCP
DNS       : 53/TCP/UDP
NTP       : 123/UDP
NFS/RPC   : 2049/TCP, 111/TCP
SIP/VoIP  : 5060/UDP, 5061/TCP
```

---

*Notes rédigées dans le cadre de la préparation à la certification CEH v13 — EC-Council*  
*Format : Markdown — Compatible GitHub Pages*
