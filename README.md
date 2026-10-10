# 🌐 Cisco Networking Labs — Portfolio

Travaux pratiques réseaux réalisés pendant ma formation en **réseaux et administration systèmes (RSI3)**.
Chaque TP est simulé avec **Cisco Packet Tracer** (fichiers `.pkt`). Les labs sont basés sur le contenu **Cisco Networking Academy (CCNA)** et sur les sujets de mon établissement (ISET Nabeul).

## 📚 Liste des projets

### 1️⃣ Routage

| # | Projet | Protocole | Fichier Packet Tracer |
|---|--------|-----------|-----------------------|
| TP5 | Configuration de base de RIPv2 | RIPv2 | [`TP05_RIPv2_Basic.pkt`](1-Routing/TP05-RIPv2-Basic/) |
| TP7 | Fonctionnalités avancées d'EIGRP pour IPv4 | EIGRP | [`TP07_EIGRP_Advanced.pkt`](1-Routing/TP07-EIGRP-Advanced/) |
| TP10 | OSPFv2 sur un réseau à accès multiple (DR/BDR) | OSPFv2 | [`TP10_OSPFv2_Multiaccess_DR_BDR.pkt`](1-Routing/TP10-OSPFv2-Multiaccess/) |
| TP11 | Fonctionnalités avancées d'OSPFv2 | OSPFv2 | [`TP11_OSPFv2_Advanced.pkt`](1-Routing/TP11-OSPFv2-Advanced/) |

### 2️⃣ Commutation (Switching)

| # | Projet | Sujet | Fichier Packet Tracer |
|---|--------|-------|-----------------------|
| TP7 | Commutation de couche 3 et routage inter-VLAN | MLS, SVI, VLAN | [`TP07_L3Switch_InterVLAN.pkt`](2-Switching/TP07-L3Switch-InterVLAN/) |
| TP10 | Fonctions de sécurité des commutateurs | SSH, Port Security | [`TP10_Switch_Security_SSH_PortSecurity.pkt`](2-Switching/TP10-Switch-Security/) |
| Examen | Examen TP : VTP, trunks, routage inter-VLAN, EtherChannel | VTP, 802.1Q, PAgP/LACP | [`Exam_TP_VTP_Trunk_EtherChannel.pkt`](2-Switching/Exam-TP-VTP-EtherChannel/) |

> ℹ️ La numérotation des TP repart de zéro dans chaque module (routage / commutation).
> 🚧 D'autres TP seront ajoutés au fur et à mesure.

---

# 1️⃣ Module Routage

## 🔹 TP5 — RIPv2 : configuration de base

**Topologie :** 3 routeurs (R1, R2, R3) en série + 2 switchs (S1, S3) + 3 PC.

```
PC-A -- S1 -- R1 ==== R2 ==== R3 -- S3 -- PC-C
                       |
                     PC-B
```

**Ce que j'ai fait :**
- Configuration de base (hostname, mots de passe, bannière MOTD, `logging synchronous`, descriptions d'interfaces, `clock rate` sur les DCE)
- Routage **RIPv2** (`version 2`) et annonce des réseaux
- `passive-interface` sur les interfaces LAN
- Observation du problème des **réseaux discontinus** avec la récapitulation automatique, puis `no auto-summary`
- Route par défaut vers « Internet » + `default-information originate`
- Vérification avec `show ip route`, `show ip protocols`, `debug ip rip`

**Commandes clés :** `router rip`, `version 2`, `passive-interface`, `no auto-summary`, `default-information originate`

---

## 🔹 TP7 — EIGRP : fonctionnalités avancées

**Topologie :** 3 routeurs en triangle (R1, R2, R3), interfaces loopback, 3 PC, simulation d'Internet sur R2.

```
        Internet
           |
          R2 (PC-B)
         /    \
   1024k/      \1544k
       /        \
 (PC-A) R1 ---- R3 (PC-C)
          64k
```

**Ce que j'ai fait :**
- EIGRP avec AS 1 sur tous les routeurs, `passive-interface` sur les LAN
- Réglage de la bande passante des liens série (`bandwidth`)
- **Récapitulation automatique** (`auto-summary`) et impact sur la table de routage de R2
- Route statique par défaut + propagation avec `redistribute static`
- Réglage fin : `ip bandwidth-percent eigrp`, `ip hello-interval eigrp`, `ip hold-time eigrp`

**Commandes clés :** `router eigrp 1`, `network`, `auto-summary`, `redistribute static`, `ip bandwidth-percent eigrp 1`, `ip hello-interval eigrp 1`

---

## 🔹 TP10 — OSPFv2 sur réseau à accès multiple

**Topologie :** 3 routeurs connectés au même switch (même segment 192.168.1.0/24).

```
 R1 ---- [Switch] ---- R3
            |
           R2
```

**Ce que j'ai fait :**
- Configuration d'OSPFv2 (process 1, area 0) sur DR, BDR et DROther
- Compréhension de l'**élection DR/BDR** (ordre de configuration, router-ID)
- Modification de la **priorité d'interface** (`ip ospf priority` : 255 / 100 / 0) pour forcer l'élection
- Vérification avec `show ip ospf neighbor`, `show ip ospf interface (brief)`

**Commandes clés :** `router ospf 1`, `network ... area 0`, `ip ospf priority`, `clear ip ospf process`

---

## 🔹 TP11 — OSPFv2 : fonctionnalités avancées

**Topologie :** 3 routeurs en triangle (liens série), R2 relié à « Internet » via Lo0, 2 PC.

**Ce que j'ai fait :**
- Router-ID manuels (1.1.1.1, 2.2.2.2, 3.3.3.3)
- Modification des **métriques OSPF** : `bandwidth`, `auto-cost reference-bandwidth`, `ip ospf cost`
- Forcer le trafic à passer par R2 en augmentant le coût du lien R1–R3
- Route statique par défaut sur R2 + `default-information originate`
- Vérification de bout en bout (ping PC-A → interface « FAI »)

**Commandes clés :** `router-id`, `auto-cost reference-bandwidth`, `ip ospf cost`, `default-information originate`

---

# 2️⃣ Module Commutation (Switching)

## 🔹 TP7 — Commutation de couche 3 et routage inter-VLAN

**Topologie :** 1 switch multicouche (MLS 3560), 3 switchs (S1, S2, S3), 6 PC répartis sur 3 VLAN, 1 routeur R1 (sortie « Internet »).

```
 PC0/PC1/PC2 -- S2 --- S1 --- MLS ---- R1
                        |
 PC3/PC4/PC5 -- S3 -----+
```

| VLAN | Nom | Réseau |
|------|-----|--------|
| 10 | Staff | 192.168.10.0/24 |
| 20 | Student | 192.168.20.0/24 |
| 30 | Faculty | 192.168.30.0/24 |
| 99 | Management | 192.168.99.0/24 |

**Ce que j'ai fait :**
- Port routé sur le MLS (`no switchport`) + route par défaut vers R1
- Création des **VLAN**, ports d'accès (S2, S3) et **trunks 802.1Q**
- **Routage inter-VLAN** avec des interfaces **SVI** sur le MLS (`ip routing`)
- Adressage de gestion des switchs (VLAN 99) et tests de connectivité de bout en bout

**Commandes clés :** `no switchport`, `switchport mode trunk`, `interface vlan X`, `ip routing`, `ip route 0.0.0.0 0.0.0.0`

---

## 🔹 TP10 — Sécurité des commutateurs (SSH & Port Security)

**Topologie :** 1 routeur (R1), 1 switch (S1), 1 PC (PC-A), réseau de gestion 172.16.99.0/24.

```
R1 (G0/1) ---- (F0/5) S1 (F0/6) ---- PC-A
```

**Ce que j'ai fait :**
- Configuration de base du switch + **VLAN 99 de gestion**
- Accès **SSH uniquement** : nom de domaine, utilisateur local, clé RSA, `transport input ssh`, `login local`
- Ajustement des paramètres SSH (`ip ssh time-out`, `ip ssh authentication-retries`)
- Sécurité générale : bannière MOTD, **arrêt des ports inutilisés** (`interface range`)
- **Port Security** : adresse MAC statique, violation en mode `shutdown`, simulation d'une attaque par usurpation de MAC, puis récupération du port `err-disabled`

**Commandes clés :** `crypto key generate rsa`, `username ... secret`, `switchport port-security`, `switchport port-security mac-address`, `show port-security`

---

## 🔹 Examen TP — VTP, trunks, inter-VLAN et EtherChannel

**Topologie :** 3 switchs (SW1, SW2, SW3) interconnectés par des EtherChannels, 1 routeur R1 (router-on-a-stick), 3 PC dans 3 VLAN.

```
 R1 ---- SW1 ===== PAgP ===== SW3
          \                    /
         PAgP               LACP
            \              /
               \          /
                  SW2
               /   |   \
            PC1   PC2   PC3
```

**Ce que j'ai fait :**
- Configuration de base (hostname, passerelle par défaut, interface de gestion VLAN 99)
- **VTP** : SW1 en serveur, SW2 et SW3 en clients (domaine + mot de passe)
- **Trunks 802.1Q** avec VLAN 99 en VLAN natif
- Création des VLAN (10, 20, 30, 99) sur le serveur VTP et affectation des ports sur SW2
- **Router-on-a-stick** : sous-interfaces sur R1, une par VLAN
- **EtherChannel** : PAgP (SW1–SW3 et SW1–SW2) et LACP (SW2–SW3)
- Tests de connectivité entre PC1, PC2 et PC3

**Commandes clés :** `vtp mode`, `vtp domain`, `switchport trunk native vlan`, `interface g0/1.X` + `encapsulation dot1Q X`, `channel-group X mode desirable/active`

---

## 🛠️ Outils & compétences

- **Cisco Packet Tracer**, Cisco IOS (CLI)
- Routage dynamique : **RIPv2, EIGRP, OSPFv2** ; routage statique et route par défaut
- Commutation : **VLAN, trunking 802.1Q, VTP, EtherChannel (PAgP/LACP)**
- Routage inter-VLAN (**SVI** sur switch L3, **router-on-a-stick**)
- Sécurité des équipements : **SSH, Port Security**, durcissement des ports
- Adressage IPv4, sous-réseaux, VLSM
- Dépannage : `show`, `debug`, `ping`

## 📁 Structure du dépôt

```
.
├── README.md
├── 1-Routing/
│   ├── TP05-RIPv2-Basic/
│   ├── TP07-EIGRP-Advanced/
│   ├── TP10-OSPFv2-Multiaccess/
│   └── TP11-OSPFv2-Advanced/
└── 2-Switching/
    ├── TP07-L3Switch-InterVLAN/
    ├── TP10-Switch-Security/
    └── Exam-TP-VTP-EtherChannel/
```

## 👩‍💻 Auteure

**Mariem** — Étudiante en réseaux et administration systèmes (RSI3)
