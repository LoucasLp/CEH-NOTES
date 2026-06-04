# 🛡️ Module 16 — Contre-mesures : Sécurité Wi-Fi

> **CEH v13 | EC-Council**  
> `#WPA3` `#WPS` `#WIDS` `#VPN` `#IsolationClient` `#RADIUS`

---
## 📌 Table des matières

1. [Contre-mesures Essentielles](#contre-mesures-essentielles)
2. [Matrice des Contre-mesures](#matrice-des-contre-mesures)

---


## 📌 Contre-mesures Essentielles

### Protocoles

```
1. Utiliser WPA3 (ou WPA2-Enterprise si WPA3 non disponible)
   → WPA3 SAE résiste aux attaques dictionnaire
   → PMF (Protected Management Frames) obligatoire en WPA3
   
2. WPA2-Personal : n'accepter que des passphrases longues (20+ caractères)
   → Rend le brute force hors ligne impraticable
   
3. WPA2-Enterprise (802.1X + RADIUS) :
   → Chaque utilisateur a ses propres credentials
   → Compromission d'un utilisateur n'affecte pas les autres
   → Audit centralisé des connexions

4. Désactiver WEP définitivement
5. Désactiver TKIP (WPA), utiliser AES uniquement
```

### WPS

```
DÉSACTIVER WPS SUR TOUS LES ROUTEURS
→ C'est la contre-mesure la plus importante pour WPS
→ WPS PIN = brute forceable en quelques heures (Reaver)
→ WPS PBC = moins risqué mais accès physique requis

Vérification :
  wash -i wlan0mon   → Si l'AP n'apparaît pas = WPS désactivé ✓
```

### Réseau

```
Isolation des clients Wi-Fi :
  → Client1 ne peut pas communiquer avec Client2
  → Empêche les attaques MITM entre clients du même réseau
  
Segmentation VLAN :
  → Wi-Fi invité sur VLAN séparé (pas accès au réseau interne)
  → Entreprise : VLAN séparé par groupe d'utilisateurs
  
SSID unique par zone :
  → Ne pas diffuser le SSID (partiel, l'SSID est visible en Probe Request)
  → Utiliser des noms SSID non révélateurs
```

### VPN

```
Sur les réseaux Wi-Fi publics :
  → Toujours utiliser un VPN
  → Le VPN chiffre tout le trafic (même si le Wi-Fi est compromis)
  → Protège contre Evil Twin et sidejacking
  
En entreprise :
  → VPN split tunnel vs full tunnel
  → Full tunnel : tout le trafic passe par le VPN (plus sécurisé)
```

### WIDS — Wireless Intrusion Detection System

```
Détecte :
  → Déauth floods (attaque DoS Wi-Fi)
  → Rogue AP / Evil Twin (SSID identique)
  → Probe floods (scan Wi-Fi agressif)
  → Appareils non autorisés (liste blanche de BSSID)
  
Solutions :
  → Kismet (open-source)
  → Cisco Prime Infrastructure
  → Aruba RAPIDS
  
Configuration exemple (Kismet) :
  Alertes sur :
  - APSPOOF : AP avec SSID connu mais BSSID différent → Evil Twin !
  - BCASTDISCON : déauth broadcast → attaque DoS !
```

---

## Matrice des Contre-mesures

| Attaque | Contre-mesure |
|---------|---------------|
| WEP cracking | Migrer vers WPA2/WPA3 |
| WPA2 handshake cracking | Passphrase longue (20+ chars) + WPA3 |
| PMKID attack | Passphrase longue + WPA3 |
| WPS brute force | Désactiver WPS |
| Evil Twin | WIDS + VPN + 802.1X (certificats) |
| Déauthentification | WPA3 PMF |
| Bluetooth Bluesnarfing | Désactiver BT si inutile, non discoverable |
| Sidejacking Wi-Fi public | VPN obligatoire |

---

## 🧠 Points clés à retenir pour l'examen

- **WPA3 + PMF** = protection contre Evil Twin et déauth
- **Désactiver WPS** = contre-mesure principale contre Reaver
- **Passphrase longue** = rend le handshake cracking impraticable
- **VPN** = protection sur réseau Wi-Fi public
- **WIDS** = détection des Rogue APs, déauth floods
- **802.1X/RADIUS** = authentification par utilisateur, plus sécurisé que PSK

---

*⬅️ [Outils et Labs](./04_outils_et_labs.md) | ➡️ [Quiz et Révision](./06_quiz_et_revision.md)*
