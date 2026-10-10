# Cisco Networking Labs — Portfolio

Travaux pratiques réseaux réalisés pendant ma formation en **réseaux et administration systèmes (RSI3)**.
Chaque TP est simulé avec **Cisco Packet Tracer** (fichiers `.pkt`). Les labs sont basés sur le contenu **Cisco Networking Academy (CCNA)** et sur les sujets de mon établissement (ISET Nabeul).

## Liste des projets

### Routage

| # | Projet | Protocole | Fichier Packet Tracer |
|---|--------|-----------|-----------------------|
| TP5 | Configuration de base de RIPv2 | RIPv2 | [`TP05_RIPv2_Basic.pkt`](1-Routing/TP05-RIPv2-Basic/) |
| TP7 | Fonctionnalités avancées d'EIGRP pour IPv4 | EIGRP | [`TP07_EIGRP_Advanced.pkt`](1-Routing/TP07-EIGRP-Advanced/) |
| TP10 | OSPFv2 sur un réseau à accès multiple (DR/BDR) | OSPFv2 | [`TP10_OSPFv2_Multiaccess_DR_BDR.pkt`](1-Routing/TP10-OSPFv2-Multiaccess/) |
| TP11 | Fonctionnalités avancées d'OSPFv2 | OSPFv2 | [`TP11_OSPFv2_Advanced.pkt`](1-Routing/TP11-OSPFv2-Advanced/) |

### Commutation (Switching)

| # | Projet | Sujet | Fichier Packet Tracer |
|---|--------|-------|-----------------------|
| TP7 | Commutation de couche 3 et routage inter-VLAN | MLS, SVI, VLAN | [`TP07_L3Switch_InterVLAN.pkt`](2-Switching/TP07-L3Switch-InterVLAN/) |
| TP10 | Fonctions de sécurité des commutateurs | SSH, Port Security | [`TP10_Switch_Security_SSH_PortSecurity.pkt`](2-Switching/TP10-Switch-Security/) |
| Examen | Examen TP : VTP, trunks, routage inter-VLAN, EtherChannel | VTP, 802.1Q, PAgP/LACP | [`Exam_TP_VTP_Trunk_EtherChannel.pkt`](2-Switching/Exam-TP-VTP-EtherChannel/) |

> La numérotation des TP repart de zéro dans chaque module (routage / commutation).
>  D'autres TP seront ajoutés au fur et à mesure.

---

# Module Routage

## TP5 — RIPv2 : configuration de base

**Ce que j'ai fait :**

**Partie 1 — Construction du réseau et configuration de base**
- Câblage de la topologie : 3 routeurs (R1, R2, R3) reliés par des liens série, 2 switchs (S1, S3) et 3 PC
- Configuration de base sur chaque routeur et switch :
  - désactivation de la recherche DNS (`no ip domain-lookup`)
  - nom des équipements (hostname)
  - chiffrement des mots de passe en clair (`service password-encryption`)
  - mot de passe du mode privilégié (`class`) et mot de passe console/vty (`cisco`)
  - bannière MOTD d'avertissement contre les accès non autorisés
  - `logging synchronous` sur la ligne console
- Attribution des adresses IP sur toutes les interfaces, avec une **description** pour chaque interface adressée
- Configuration de la **fréquence d'horloge** (`clock rate`) sur les interfaces série DCE
- Sauvegarde de la configuration (`copy running-config startup-config`)
- Configuration IP des PC (adresse, masque, passerelle)
- Tests de connectivité de base : chaque PC vers sa passerelle et chaque routeur vers ses voisins directs

**Partie 2 — Configuration et vérification de RIPv2**
- Activation de **RIPv2** sur R1, R2 et R3 (`router rip`, `version 2`) et annonce des réseaux connectés
- Utilisation de `passive-interface` sur les interfaces LAN de R1 et R3 pour éviter l'envoi de mises à jour inutiles
- Sur R2, **non-annonce** du réseau 209.165.201.0 (réseau simulant Internet)
- Vérification de l'état des liens série avec `show ip interface brief`
- Test de connectivité entre PC avant et après le routage, et analyse des réponses
- Vérification du fonctionnement de RIPv2 avec `show ip protocols`, `debug ip rip` et `show run`
- **Analyse de la récapitulation automatique** : observation que R2 voit deux chemins de coût égal vers 172.30.0.0/16 (réseaux discontinus) et que R1 et R3 n'ont pas de route vers le LAN de l'autre
- Désactivation de la récapitulation automatique (`no auto-summary`) sur les 3 routeurs, puis `clear ip route *`
- Vérification des nouvelles tables de routage : les sous-réseaux 172.30.10.0/24 et 172.30.30.0/24 apparaissent partout
- Analyse des mises à jour RIP avec `debug ip rip` : routes reçues de R3 et masques de sous-réseau inclus
- **Route par défaut** sur R2 vers 209.165.201.2 (`ip route 0.0.0.0 0.0.0.0`) et propagation avec `default-information originate`
- Vérification de la route `R*` (route par défaut apprise par RIP) dans la table de R1
- Tests de bout en bout : ping de PC-A et PC-C vers « Internet » (209.165.201.2) et entre PC-A et PC-C

---

##  TP7 — EIGRP : fonctionnalités avancées

**Ce que j'ai fait :**

**Partie 1 — Construction du réseau et configuration de base**
- Câblage de la topologie en triangle (R1, R2, R3) avec 3 PC et un lien simulant Internet sur R2
- Configuration de base de chaque routeur : DNS désactivé, noms, mots de passe (`cisco` console/vty, `class` mode privilégié), `logging synchronous`, adressage IP des interfaces et sauvegarde de la configuration
- Configuration IP des PC

**Partie 2 — Configuration d'EIGRP et vérification de la connectivité**
- Activation d'**EIGRP (AS 1)** sur R1, R2 et R3 pour tous les réseaux directement connectés
- Désactivation de l'envoi des paquets Hello sur les interfaces LAN avec `passive-interface g0/0`
- Réglage de la **bande passante** des liens série (1024 Kbit/s entre R1 et R2, 64 Kbit/s entre R1 et R3) avec la commande `bandwidth`, en comprenant qu'elle n'agit que sur le calcul de la métrique EIGRP
- Test de connectivité : tous les PC peuvent se pinguer entre eux

**Partie 3 — Récapitulation automatique**
- Vérification de l'état par défaut de la récapitulation automatique avec `show ip protocols` (désactivée par défaut)
- Création d'interfaces **loopback** sur R1 (192.168.11.1/5/9/13) et sur R3 (192.168.33.1/5/9/13) et ajout des réseaux correspondants dans EIGRP (avec masque générique `0.0.0.3`)
- Observation de la table de routage de R2 : chaque loopback apparaît comme une route distincte
- Activation de `auto-summary` sur R1 et R3 et observation de la **réduction du nombre de routes** dans la table de R2 (routes récapitulées)

**Partie 4 — Route statique par défaut**
- Création de l'interface loopback de R2 simulant Internet
- Configuration d'une **route statique par défaut** avec interface de sortie Lo1
- Propagation de la route aux autres routeurs avec `redistribute static`
- Vérification avec `show ip protocols` sur R2 et `show ip route eigrp | include 0.0.0.0` sur R1 (route externe EIGRP, distance administrative 170)

**Partie 5 — Réglage fin d'EIGRP**
- Limitation de la bande passante utilisable par le trafic EIGRP : **75 %** sur le lien R1–R2 et **40 %** sur le lien R1–R3 (`ip bandwidth-percent eigrp 1`)
- Consultation des valeurs par défaut avec `show ip eigrp interfaces detail` : Hello 5 s et temps d'attente (Hold) 15 s
- Modification des temporisateurs sur toutes les interfaces série de R1, R2 et R3 : **Hello 60 s** et **Hold 180 s** (`ip hello-interval eigrp 1` puis `ip hold-time eigrp 1`)
- Compréhension de la raison pour laquelle le Hold doit rester supérieur ou égal au Hello, et des avantages de la récapitulation des routes

---

## TP10 — OSPFv2 sur réseau à accès multiple

**Ce que j'ai fait :**

**Partie 1 — Construction du réseau et configuration de base**
- Câblage de 3 routeurs (R1, R2, R3) reliés au même switch sur le segment 192.168.1.0/24
- Configuration de base : DNS désactivé, noms, mots de passe (`class`, `cisco`), chiffrement des mots de passe, bannière MOTD, `logging synchronous`
- Adressage des interfaces physiques et des interfaces **loopback** (Lo0), vérification avec `show ip interface brief` et sauvegarde de la configuration

**Partie 2 — OSPFv2 avec DR, BDR et DROther**
- Configuration d'OSPFv2 (process 1, area 0) dans un **ordre précis** pour contrôler l'élection :
  - R3 en premier (ID de routeur le plus élevé, issu de sa loopback) pour qu'il devienne le **DR**
  - R2 ensuite pour qu'il devienne le **BDR**
  - R1 en dernier pour qu'il soit **DROther**
- Compréhension du fait qu'un routeur ajouté après l'élection ne remplace pas le DR/BDR existant, même avec une priorité ou un ID plus élevés
- Vérification des rôles avec `show ip ospf interface brief` et `show ip ospf neighbor` (états FULL/DR, FULL/BDR, priorité 1 par défaut)

**Partie 3 — Priorité d'interface OSPF**
- Modification de la priorité d'interface avec `ip ospf priority` :
  - R1 : **255** (priorité maximale)
  - R3 : **100**
  - R2 : **0** (ne peut jamais devenir DR ni BDR)
- Réinitialisation du processus OSPF (`clear ip ospf 1 process`) pour forcer une nouvelle élection
- Vérification avec `show ip ospf neighbor` et `show ip ospf interface` : R1 devient **DR**, R3 devient **BDR**, R2 passe en **DROther**
- Conclusion : la **priorité d'interface prime sur l'ID de routeur** dans l'élection DR/BDR

---

## TP11 — OSPFv2 : fonctionnalités avancées

**Ce que j'ai fait :**

**Partie 1 — Construction du réseau et configuration de base**
- Câblage de la topologie en triangle avec 2 PC et une interface loopback sur R2 simulant Internet
- Configuration de base de chaque routeur : DNS désactivé, noms, mots de passe (`class`, `cisco`), chiffrement des mots de passe, bannière MOTD, `logging synchronous`
- Adressage IP des interfaces et fréquence d'horloge **128000** sur toutes les interfaces série DCE
- Configuration IP des PC et tests de connectivité directe (voisins connectés et passerelles)

**Partie 2 — Configuration et vérification d'OSPF**
- Configuration du processus OSPF 1 avec des **ID de routeur manuels** : 1.1.1.1 (R1), 2.2.2.2 (R2), 3.3.3.3 (R3)
- Annonce des réseaux dans l'area 0 sur les trois routeurs
- Vérification des voisinages avec `show ip ospf neighbor` et des routes avec `show ip route ospf`
- Test de connectivité de bout en bout entre PC-A et PC-C

**Partie 3 — Modification des métriques OSPF**
- Observation des coûts par défaut avec `show ip ospf interface brief` (coût 64 sur les liens série)
- Réglage de `bandwidth 128` sur toutes les interfaces série : le coût passe à **781**
- Modification de la **bande passante de référence** avec `auto-cost reference-bandwidth 1000` sur tous les routeurs : le coût des liens série passe à **7812**
- Modification manuelle du coût avec `ip ospf cost 16000` sur l'interface S0/0/1 de R1, afin de rendre le chemin direct R1–R3 plus coûteux que celui passant par R2
- Vérification dans la table de routage : toutes les routes de R1 passent désormais par **R2**, et explication de ce changement par la somme des coûts
- Conclusion : la méthode la plus simple et recommandée est d'ajuster la bande passante de référence plutôt que de modifier le coût interface par interface

**Partie 4 — Route statique par défaut**
- Configuration d'une **route statique par défaut** sur R2 vers l'interface loopback (simulation d'un FAI)
- Propagation de la route avec `default-information originate` dans le processus OSPF
- Vérification avec `show ip route static` sur R2 et `show ip route` sur R1 (route `O*E2`)
- Test de bout en bout : ping de PC-A vers l'adresse de l'interface « FAI » (209.165.200.225)

---

# Module Commutation (Switching)

## TP7 — Commutation de couche 3 et routage inter-VLAN

**Ce que j'ai fait :**

**Partie 1 — Commutation de couche 3**
- Configuration du port **Fa0/2 du switch multicouche (MLS)** en **port routé** (`no switchport`) avec l'adresse 200.1.1.1/24
- Attribution de l'adresse 200.1.1.2/24 à l'interface G0/1 du routeur R1
- Configuration d'une **route par défaut** sur le MLS vers R1 (`ip route 0.0.0.0 0.0.0.0 200.1.1.2`)
- Vérification de la connectivité avec un ping du MLS vers R1

**Partie 2 — Routage inter-VLAN**
- Création des **VLAN 10 (Staff), 20 (Student), 30 (Faculty) et 99 (Management)** sur le MLS et sur les switchs S1, S2 et S3
- Configuration des **ports d'accès** sur S2 et S3 pour affecter chaque PC à son VLAN (PC0/PC5 en VLAN 10, PC1/PC4 en VLAN 20, PC2/PC3 en VLAN 30)
- Configuration des **ports trunk 802.1Q** entre MLS, S1, S2 et S3 (`switchport trunk encapsulation dot1q`, `switchport mode trunk`)
- Création et activation des **interfaces SVI** sur le MLS pour les VLAN 10, 20, 30 et 99 (passerelles des différents réseaux)
- Attribution des adresses IP de gestion (VLAN 99) aux switchs S1, S2 et S3
- Configuration IP des 6 PC (adresse, masque, passerelle = SVI du VLAN correspondant)
- Observation de la table de routage avant et après l'activation du routage avec `show ip route`, puis activation avec **`ip routing`** : apparition des réseaux connectés (VLAN 10, 20, 30, 99 et 200.1.1.0/24) et de la route par défaut
- Tests de connectivité :
  - au sein de chaque VLAN (VLAN 10, 20 et 30)
  - entre les switchs dans le VLAN 99
  - entre VLAN différents grâce au routage inter-VLAN du MLS

---

##  TP10 — Sécurité des commutateurs (SSH & Port Security)

**Ce que j'ai fait :**

**Partie 1 et 2 — Construction du réseau et configuration de base**
- Câblage de la topologie : R1 (G0/1) → S1 (F0/5), PC-A → S1 (F0/6)
- Configuration IP de PC-A (172.16.99.3) et de l'interface G0/1 de R1 (172.16.99.1)
- Configuration de base de S1 : mot de passe d'accès (`cisco`) et mot de passe du mode privilégié (`class`)
- Création du **VLAN 99 « Management »**, configuration de l'interface de gestion VLAN 99 (172.16.99.11/24) et activation avec `no shutdown`
- Vérification de l'état avec `show vlan` et `show ip interface brief` ; compréhension du fait que le protocole de l'interface VLAN 99 reste *down* tant qu'aucun port actif n'est affecté à ce VLAN
- Affectation des ports F0/5 et F0/6 au VLAN 99 en mode accès
- Tests de connectivité : PC-A vers R1, PC-A vers S1, S1 vers R1, et essai de connexion **Telnet** vers S1

**Partie 3 — Accès SSH sur S1**
- Activation de **SSH** : nom de domaine (`ip domain-name CCNA-Lab.com`)
- Création d'un utilisateur local avec privilège 15 (`username admin privilege 15 secret ...`)
- Restriction des lignes vty à **SSH uniquement** (`transport input ssh`) avec authentification locale (`login local`)
- Génération d'une **clé RSA** de 1024 bits (`crypto key generate rsa`)
- Vérification avec `show ip ssh` : version SSH, délai d'attente par défaut et nombre de tentatives d'authentification
- Modification des paramètres SSH : `ip ssh time-out 75` et `ip ssh authentication-retries 2`
- Test de connexion SSH depuis PC-A (`ssh -l admin 172.16.99.11`)

**Partie 4 — Fonctions de sécurité sur S1**
- Configuration d'une **bannière MOTD** d'avertissement
- **Arrêt des ports inutilisés** avec `interface range` (F0/1–4, F0/7–24, G0/1–2) et vérification de leur état
- Configuration de la **sécurité des ports** (Port Security) sur F0/5 :
  - relevé de l'adresse MAC de G0/1 sur R1 et de la table MAC du switch (`show mac-address-table`)
  - activation de `switchport port-security` (maximum 1 adresse MAC, violation en mode `shutdown`)
  - ajout d'une **adresse MAC statique** (celle de R1)
- Vérification avec `show port-security interface f0/5`
- **Simulation d'une violation** : changement de l'adresse MAC de R1 (`mac-address aaaa.bbbb.cccc`) → le port F0/5 passe en état **err-disabled**
- Analyse avec `show port-security`, `show interface f0/5` et `show port-security address`
- **Récupération** : retrait de l'adresse MAC modifiée sur R1, puis réactivation du port (`shutdown` / `no shutdown`) et ping réussi vers PC-A
- Compréhension de l'intérêt de la sécurité des ports et de la désactivation des ports inutilisés

---

## Examen TP — VTP, trunks, inter-VLAN et EtherChannel

**Ce que j'ai fait :**

- **Configuration de base des 3 switchs** (SW1, SW2, SW3) : nom d'hôte et passerelle par défaut (172.20.99.1)
- Configuration de l'**interface de gestion VLAN 99** sur chaque switch (172.20.99.11, .12 et .13)
- Configuration de **VTP** : SW1 en mode **serveur**, SW2 et SW3 en mode **client**, avec le même domaine VTP et un mot de passe VTP
- Configuration des **ports trunk 802.1Q** sur les 3 switchs, avec le **VLAN 99 comme VLAN natif**
- Création des VLAN sur le serveur VTP (SW1) : **99 (administration), 10 (Ingénierie), 20 (Marketing), 30 (Production)**, puis vérification de leur propagation automatique vers les clients
- **Affectation des ports de SW2** aux VLAN : Fa0/5–10 en VLAN 30, Fa0/11–17 en VLAN 10, Fa0/18–24 en VLAN 20
- Configuration de l'**interface trunk sur R1** (router-on-a-stick) : une **sous-interface par VLAN** (VLAN 1, 10, 20, 30, 99), chacune avec son encapsulation 802.1Q et son adresse IP de passerelle
- Configuration IP de PC1, PC2 et PC3 selon le tableau d'adressage
- **Tests de connectivité** entre PC1, PC2 et PC3 (routage inter-VLAN via R1)
- Configuration des **EtherChannels** :
  - **PAgP** entre SW1 et SW3
  - **LACP** entre SW2 et SW3
  - **PAgP** entre SW1 et SW2
- Vérification des agrégations (`show etherchannel summary`) et des trunks

---

## Outils & compétences

- **Cisco Packet Tracer**, Cisco IOS (CLI)
- Routage dynamique : **RIPv2, EIGRP, OSPFv2** ; routage statique et route par défaut
- Optimisation des protocoles : récapitulation de routes, métriques, coûts OSPF, temporisateurs, élection DR/BDR
- Commutation : **VLAN, trunking 802.1Q, VTP, EtherChannel (PAgP/LACP)**
- Routage inter-VLAN (**SVI** sur switch L3, **router-on-a-stick**)
- Sécurité des équipements : **SSH, Port Security**, bannières, durcissement des ports
- Adressage IPv4, sous-réseaux, VLSM
- Dépannage : `show`, `debug`, `ping`

##  Structure du dépôt

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

## Auteure

**Mariem** — Étudiante en réseaux et administration systèmes (RSI31)
