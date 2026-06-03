# 🛡️ Module 18 — Contre-mesures : IoT & OT

> **CEH v13 | EC-Council**  
> `#Segmentation` `#Chiffrement` `#Patching` `#PhysicalSecurity` `#ICS-CERT`

---

## Contre-mesures IoT

```
1. Changer les credentials par défaut IMMÉDIATEMENT
   → Jamais laisser admin/admin, root/root
   → Mot de passe fort et unique par appareil
   
2. Mise à jour du firmware
   → Activer les mises à jour automatiques si disponible
   → Surveiller les bulletins de sécurité du fabricant
   → Remplacer les appareils abandonnés (End of Life)
   
3. Segmentation réseau
   → Réseau IoT sur VLAN séparé du réseau principal
   → Pas d'accès direct Internet aux appareils IoT
   → Firewall entre VLAN IoT et LAN
   
4. Désactiver les services inutiles
   → Telnet → SSH uniquement
   → UPnP désactivé
   → Services debug désactivés en production
   
5. Chiffrement des communications
   → MQTT : utiliser le port 8883 (TLS) pas 1883
   → HTTPS pour les interfaces web
```

## Contre-mesures OT/SCADA

```
1. Segmentation et air gap réel
   Réseau IT ──[Firewall]──[DMZ]──[Firewall]── Réseau OT
                                    │
                                [Historian]    ← Seul pont autorisé
                                               (one-way data diode)
   
2. Monitoring réseau OT
   → IDS/IPS dédié OT (Claroty, Dragos, Nozomi)
   → Baseline du trafic normal (Modbus commands habituels)
   → Alerter sur les nouveaux appareils, nouvelles commandes
   
3. Inventaire des assets
   → Cartographier tous les PLCs, RTUs, HMIs
   → Versions firmware, protocoles utilisés
   
4. Patching OT (avec précautions)
   → Tester en environnement de simulation avant prod
   → Fenêtre de maintenance planifiée
   → Snapshot/backup avant patch
   
5. Physical security
   → Accès JTAG/UART protégé physiquement (coffret verrouillé)
   → Politique de clés USB (interdire les clés non autorisées)
   → Surveillance vidéo des salles de contrôle
```

---

## Matrice des Contre-mesures

| Attaque/Vulnérabilité | Contre-mesure |
|----------------------|---------------|
| Default credentials | Changer immédiatement + inventaire |
| MQTT non chiffré | MQTT TLS (port 8883) + authentification |
| Modbus sans auth | Segmentation réseau + firewall OT |
| Firmware vulnérable | Mise à jour + monitoring CVE fabricant |
| Air gap contourné (USB) | Politique USB stricte + monitoring |
| Stuxnet-like | Air gap réel + antivirus offline + monitoring |
| JTAG/UART exposé | Sécurité physique + désactiver debug |

---

## 🧠 Points clés à retenir pour l'examen

- **Segmentation** = VLAN IoT séparé, air gap OT réel
- **MQTT TLS** = port 8883 (au lieu de 1883)
- **ICS-CERT** = ressource officielle pour les vulnérabilités OT
- **Data diode** = communication one-way (OT → IT, jamais l'inverse)
- **Clé USB** = principal vecteur d'infection des réseaux OT "isolés" (Stuxnet)

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
