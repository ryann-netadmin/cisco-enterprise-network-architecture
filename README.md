# Infrastructure Réseau d'Entreprise Sécurisée avec Interconnexion WAN (Cisco Packet Tracer)

<p align="center">
  <img src="./screenshot_topology.png" alt="Topologie Réseau Complète" width="90%">
</p>

## 📌 Présentation du Projet
Ce projet consiste en la conception, la configuration et la sécurisation d'une infrastructure réseau d'entreprise complète et simulée sous **Cisco Packet Tracer**. 

L'architecture met en place une segmentation par VLANs, un routage inter-VLAN géré par un commutateur de niveau 3 (Multilayer Switch), de la redondance de liens (EtherChannel), des services réseau centralisés (DHCP), ainsi qu'une interconnexion WAN sécurisée vers un Fournisseur d'Accès Internet (FAI) pour simuler l'accès à un serveur web public.

## 🗺️ Topologie Réseau & Segmentation
Le réseau de l'entreprise est segmenté en plusieurs zones logiques afin d'assurer l'étanchéité et la sécurité des flux :

* **VLAN 10 - Admin / Staff :** Destiné au personnel administratif et aux postes internes (`192.168.10.0/26`).
* **VLAN 11 - Comptabilité :** Zone dédiée au service comptable (`192.168.10.64/27`).
* **VLAN 20 - Guest (Wi-Fi) :** Réseau isolé pour les invités accédant via les bornes Wi-Fi (`192.168.20.0/27`).
* **VLAN 99 - Management :** Dédié à l'administration sécurisée (SSH) des équipements réseaux (`192.168.99.0/28`).
* **VLAN 999 - Native VLAN :** Utilisé pour sécuriser les liaisons trunks et éviter les attaques de type *VLAN Hopping*.

## 🛠️ Technologies & Protocoles Implémentés

### 1. Commutation & Haute Disponibilité (Couche 2)
* **EtherChannel (LACP) :** Agrégation de liens physiques en mode `active` (Port-channel 1 et 2) entre le commutateur central L3 et les commutateurs d'accès pour augmenter la bande passante et offrir une tolérance aux pannes.
* **Trunking Dot1Q :** Configuration des interconnexions avec élagage (*VLAN pruning*) et assignation d'un VLAN natif non utilisé (VLAN 999).
* **Spanning-Tree (PVST+) :** Optimisation de la convergence réseau.

### 2. Routage & Services Réseau (Couche 3)
* **Routage Inter-VLAN :** Configuré directement sur le switch `Central-L3` via des interfaces virtuelles de commutateur (SVI).
* **Routage Statique :** * Configuration d'une route par défaut (`0.0.0.0/0`) sur le réseau d'entreprise pointant vers le routeur de bordure (`R1`), puis vers le `Router_FAI`.
  * Configuration de routes statiques de retour sur le routeur FAI pour joindre les sous-réseaux internes de l'entreprise.
* **DHCP Centralisé :** Le switch L3 distribue automatiquement les adresses IP aux clients des VLANs 10, 11 et 20, avec une exclusion stricte des premières adresses réservées aux passerelles.

### 3. Sécurité de l'Infrastructure (Hardening)
* **Port-Security (Sticky MAC) :** Activé sur les commutateurs d'accès pour mémoriser dynamiquement l'adresse MAC des postes légitimes. En cas de branchement d'un équipement inconnu, la violation est définie sur `restrict`.
* **STP PortFast & BPDU Guard :** Appliqués sur tous les ports d'accès utilisateurs afin d'accélérer la connexion des machines et de bloquer l'introduction de commutateurs pirates.
* **Administration Sécurisée (SSH v2) :** Désactivation du protocole non chiffré Telnet. L'accès en CLI se fait exclusivement via **SSHv2** avec authentification locale sur le VLAN 99.
* **Chiffrement des Mots de Passe :** Application de `service password-encryption` et utilisation de mots de passe de type *secret* (MD5).
* **Sécurité Wi-Fi :** Configuration des points d'accès sans fil avec le protocole de chiffrement **WPA2-PSK (AES)**.

---

## 📸 Tests de Validation & Preuves de Fonctionnement

### 1. Connectivité de bout en bout (Ping)
Voici la validation des communications montrant qu'un PC du réseau interne de l'entreprise (ex: VLAN 10) arrive à joindre avec succès le serveur du FAI (`8.8.8.2`) :

<img src="./images/screenshot_ping_test.png" alt="Test de validation Ping" width="70%">

### 2. Sécurisation des accès à distance (SSH)
Démonstration de la connexion à l'un des commutateurs ou routeurs via SSHv2 depuis le VLAN de management 99, prouvant que Telnet est rejeté et que l'authentification locale fonctionne :

<img src="./images/screenshot_ssh_access.png" alt="Accès SSH Équipement" width="70%">

### 3. Attribution des adresses IP par le DHCP
Preuve du bon fonctionnement du serveur DHCP configuré sur le commutateur de niveau 3 (Central-L3) sur les machines clientes :

<img src="./images/screenshot_dhcp_success.png" alt="Attribution IP DHCP" width="70%">

---

## 📊
