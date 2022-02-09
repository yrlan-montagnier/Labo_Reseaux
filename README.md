# Projet de groupe Pôle-Réseau 
# Sommaire
- [I. Présentation du projet](#I-Présentation-du-projet)
- [II. Organisation du projet](#II-Organisation-du-projet)
- [III. Livrables](#III-Livrables)
    - [1. Schémas des réseaux](#1-Schémas-des-réseaux)
    - [2. Tableaux des réseaux](#2-Tableaux-des-réseaux)
    - [3. Plan d’adressage](#3-Plan-d’adressage)
    - [4. Tableaux des VLAN](#4-Tableaux-des-VLAN)
    - [5. Matrice des flux simplifiés](#5-Matrice-des-flux-simplifiés)
- [IV. Notions obligatoires](#IV-Notions-obligatoires)
- [V. Notions au choix](#V-Notions-au-choix)

# I. Présentation du projet
Pour ce projet, nous avions pour objectif de **mettre en place une architecture réseaux** utilisant plusieurs technologies comme : 
- Les **VLAN**
- Le **Routage OSPF**
- La mise en place d'un **FireWall**
- La mise en place d'un **serveur DHCP/DNS**
- Configurer le **NAT/PAT** 
- Le **VPN**

Sur ce repo, vous retrouverez les confs, livrables ainsi que notre projet GNS3.

# II. Organisation du projet
## Utilisation de Planner
* https://tasks.office.com/ynov.com/fr-FR/Home/Planner/#/plantaskboard?groupId=cf151af7-83e4-4274-96fb-f31bb484b50e&planId=uaQHRlK9z0qpkbb5R1LTp5YAGvj5
## Utilisation HackMD / Git
### HackMD
* https://hackmd.io/J6xMST0wR9aIFF5Rh60dtQ?both
### Git
* https://github.com/yrlan-montagnier/Labo_Reseaux
# III. Livrables
## 1. Schémas des réseaux
![](https://i.imgur.com/D9AhH7r.png)


## 2. Tableaux des réseaux

| Réseau   | Adresse IP + Masque | VLAN | Service                         |
| -------- | ------------------- | ---- | ------------------------------- |
| employés | 10.10.10.0          | 10   | Réseau privé pour les employés  |
| serveurs | 10.10.20.0          | 20   | Salle des serveurs              |
| admins   | 10.10.30.0          | 30   | Salle pour l'administration     |

## 3. Plan d’adressage

| Machine          | Adresse IP   | Réseau   |
| ---------------- | ------------ | -------- |
| `PC1`            | `10.10.10.1` | employés |
| `PC2`            | `10.10.10.2` | employés |
| `Srv1`(DHCP/DNS) | `10.10.20.1` | serveurs |
| `Srv2`(Ansible)  | `10.10.20.2` | serveurs |
| `Web1`           | `10.10.20.3` | serveurs |
| `PC3`            | `10.10.30.1` | admins   |
| `PC4`            | `10.10.30.2` | admins   |

## 4. Tableaux des VLAN

|          | VLAN 10      | VLAN 20                | VLAN 30      |
|:-------- |:------------ |:---------------------- |:------------ |
| Nom      | Employés     | Serveurs               | Admins       |
| Machines | `PC1`, `PC2` | `Web1`, `Srv1`, `Srv2` | `PC3`, `PC4` |

## 5. Matrice des flux simplifiés

- **Alias**

Cf. plan d'adressage

| Nom     | Adresse IP |
| ------- | ---------- |
| DNS     | 10.10.20.1 |
| DHCP    | 10.10.20.1 |
| Ansible | 10.10.20.2 |
| Google  |  8.8.8.8   |
	
- **Rules**


|     Nom     | Protocole | @IP src | Port src | @IP dest | Port dest |
|:-----------:|:---------:|:-------:|:--------:|:--------:|:---------:|
|    HTPP     |    TCP    |   any   |    *     |   any    |    80     |
|    HTTPS    |    TCP    |   any   |    *     |   any    |    443    |
|    Web1     |    TCP    | Admins  |  50000   |   Web1   |    443    |
|    Web1     |    TCP    | Admins  |  50000   |   Web1   |    443    |
|     NTP     |    UDP    |   any   |    -     | Firewall |    123    |
| Ansible SSH |    TCP    | Admins  |    -     |   any    |    22     |
|   DNS ext   |  TCP,UDP  |   DNS   |    -     |  Google  |    53     |
|   DNS int   |  TCP,UDP  |   any   |    -     |   DNS    |    53     |


# IV. Notions obligatoires

## Vlan
Un vlan est un réseau local virtuel, qui regroupe plusieurs machines et les isole du reste du réseau en obligeant la comunnication avec divers appareils via un routeur.

Sans VLAN configuré sur le switch, chaque élément du réseau peut communiquer avec l'ensemble du réseau sans passer par un routeur.

A l'aide du VLAN, on va pouvoir isoler certaines interfaces du switch dans un réseau virtuel.

Il permet donc d'améliorer la sécurité du réseau mais aussi d'optimiser la bande passante en séparant les flux de données
### Déclaration des VLAN sur les Switchs
- Sur tous les switchs
    ```
    enable 
    conf t
    vlan 10  
    name employes
    vlan 20  
    name serveurs
    vlan 30
    name admins

    do wr
    ```
- Sur `Sw1`
    ```

    ```

## Routage dynamique OSPF
## Pare-feu au choix (Sophos / pfSense / TNSR)
## Services réseaux (DHCP / DNS)
## Accès internet (NAT/PAT)
## Accès aux services internes (NAT/Port Forwarding)

# V. Notions au choix
## Authentification sécurisée sur les équipements réseaux
Dans un réseau il est important de sécuriser l'accès à la configuration des équipements. Pour ce faire, nous avons choisi de mettre en place une authentification par mot de passe sur les switches et routers : 
```
Switch#configure terminal
Comm1(config)#line con 0
Comm1(config-line)#password MOTDEPASSE
Comm1(config-line)#login
Comm1(config-line)#end
```
*Ces commandes sont les mêmes que ce soit pour un switch ou un router Cisco.*

Ainsi à chaque fois que l'on accède au terminal de nos équipements, un mot de passe nous est demandé pour passer en mode "enable".

![](https://i.imgur.com/LbQDDjf.png)

Il est donc très simple de mettre en place une sécurité via authentification sur des équipements réseaux ce qui pourrait éviter des manipulations malveillantes via une connexion Telnet.

## Port Security (filtrage par adresse MAC)
A faire par Tom (moi)
## DHCP snooping
- [:file_folder:`dhcpd.conf`](./conf/dhcp/dhcpd.conf)
```

```

## Déploiement de certains services via Ansible
## ( Protocol 802.1X (authentification par mot de passe) )
