# 🛡️ Module 18 — Contre-mesures : IoT & OT

> **CEH v13 | EC-Council**  
> `#Segmentation` `#Chiffrement` `#Patching` `#PhysicalSecurity` `#ICS-CERT`

---

## 📌 Table des matières

1. [Contre-mesures IoT](#1-contre-mesures-iot)
2. [Contre-mesures OT/SCADA](#2-contre-mesures-otscada)
3. [Matrice des Contre-mesures](#3-matrice-des-contre-mesures)

---

## 1. Contre-mesures IoT

```
1. Changer les credentials par défaut IMMÉDIATEMENT
   → Jamais laisser admin/admin, root/root, user/user
   → Mot de passe fort et unique par appareil
   → Inventaire des credentials de tous les appareils

2. Mise à jour du firmware
   → Activer les mises à jour automatiques si disponible
   → Surveiller les bulletins de sécurité du fabricant (CVE)
   → Remplacer les appareils abandonnés (End of Life, plus de patches)

3. Segmentation réseau
   → Réseau IoT sur VLAN séparé du réseau principal
   → Pas d'accès direct Internet aux appareils IoT
   → Firewall entre VLAN IoT et LAN corporate
   → Principe du moindre flux (ACL strictes)

4. Désactiver les services inutiles
   → Telnet désactivé → SSH uniquement
   → UPnP désactivé (ouverture automatique de ports)
   → Services debug/JTAG désactivés en production
   → Port série (UART) verrouillé physiquement

5. Chiffrement des communications
   → MQTT : utiliser le port 8883 (TLS) pas 1883
   → HTTPS pour les interfaces web d'administration
   → Désactiver les protocoles obsolètes (TLS 1.0/1.1)

6. Mises à jour sécurisées
   → Vérification de signature cryptographique du firmware
   → HTTPS pour le téléchargement des mises à jour
   → Secure Boot activé
```

---

## 2. Contre-mesures OT/SCADA

```
1. Segmentation et air gap réel
   Réseau IT ──[Firewall]──[DMZ]──[Firewall]── Réseau OT
                                    │
                                [Historian]    ← Seul pont autorisé
                                               (data diode one-way)

2. Monitoring réseau OT dédié
   → IDS/IPS spécialisé OT (Claroty, Dragos, Nozomi Networks)
   → Baseline du trafic normal (commandes Modbus habituelles)
   → Alertes sur : nouveaux appareils, nouvelles commandes, nouvelles connexions

3. Inventaire exhaustif des assets OT
   → Cartographier tous les PLCs, RTUs, HMIs, Engineering Workstations
   → Versions firmware et protocoles utilisés
   → Responsables et fournisseurs pour chaque équipement

4. Patching OT (avec précautions extrêmes)
   → Tester en environnement de simulation/jumeau numérique avant prod
   → Fenêtre de maintenance planifiée longtemps à l'avance
   → Snapshot/sauvegarde complète avant chaque patch
   → Procédure de rollback documentée

5. Sécurité physique
   → Accès JTAG/UART protégé physiquement (coffret verrouillé, scellés)
   → Politique de clés USB stricte (interdire les clés non autorisées et vérifiées)
   → Surveillance vidéo des salles de contrôle et des accès physiques aux PLCs
   → Contrôle d'accès strict aux locaux techniques

6. Politique des accès de maintenance
   → VPN à durée limitée pour les interventions fournisseurs
   → Revue et fermeture des accès après intervention
   → MFA pour tous les accès distants
```

---

## 3. Matrice des Contre-mesures

| Attaque/Vulnérabilité | Contre-mesure principale |
|----------------------|--------------------------|
| Default credentials | Changer immédiatement + inventaire des mots de passe |
| MQTT non chiffré | MQTT TLS (port 8883) + authentification username/password |
| Modbus sans auth | Segmentation réseau OT + firewall applicatif |
| Firmware vulnérable | Mise à jour régulière + monitoring CVE fabricant |
| Air gap contourné (USB) | Politique USB stricte + monitoring des ports USB |
| Stuxnet-like | Air gap réel + antivirus offline + monitoring OT |
| JTAG/UART exposé | Sécurité physique + désactiver debug en production |
| Accès maintenance oublié | Politique VPN temporaire + revue régulière des accès |

> 💡 **Concept clé** : La **data diode** (diode de données) est un dispositif hardware permettant une communication **unidirectionnelle uniquement** (OT → IT, jamais IT → OT). Elle est considérée comme la meilleure protection pour les réseaux OT critiques.

---

## 🧠 Points clés à retenir pour l'examen

- **Segmentation** = VLAN IoT séparé du LAN ; air gap OT réel avec firewall dédié
- **MQTT TLS** = port **8883** (au lieu de 1883 non chiffré)
- **ICS-CERT** = ressource officielle pour les vulnérabilités et alertes OT/SCADA
- **Data diode** = communication one-way (OT → IT uniquement, jamais l'inverse)
- **Clé USB** = principal vecteur d'infection des réseaux OT prétendument isolés (Stuxnet)
- **Claroty, Dragos, Nozomi** = solutions de monitoring de sécurité OT spécialisées
- **Principe OT** : ne jamais faire de tests de sécurité actifs sans environnement de simulation

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
