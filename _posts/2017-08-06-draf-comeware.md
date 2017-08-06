---
ID: 663
post_title: Draf comeware
author: kral2
post_excerpt: ""
layout: post
permalink: http://www.kral2.fr/draf-comeware/
published: true
post_date: 2017-08-06 17:19:08
---
<h2>TL;DR</h2>
J'ai récemment eu l'occasion de travailler avec un switch HPE5700. Ce modèle est issu du catalogue H3C et tourne sous Comware OS.

Assez déroutant lorsque l'on a pris l'habitude de la cli Cisco. Ce billet traite de la mise en oeuvre initiale et des opérations de base pour un switch fonctionnant sous Comware OS7.

N'ayant qu'un seul switch, je n'ai malheureusement pas la possibilité de tester les fonctions avancées telles que IRF[^1].
<h2>Caractéristiques physiques du HPE 5700-48</h2>
Le 5700 48G (modèle JG894A) est un switch datacentre composé des ports suivants :
- face avant :
- 48 ports fixes GbE base-T,
- 4 ports 10GbE SFP+,
- 2 ports 40GbE QSFP+
- face arrière :
- 1 port GbE base-T pour le management (OOBM),
- 1 port console.

Voici un lien vers le document [quickspec HP5700](https://www.hpe.com/h20195/v2/getpdf.aspx/c04347352.pdf?ver=10 "quickspec HPE5700")
<h2>Basculer entre les modes view/config, sauvegarder config</h2>
On démarre en mode visualisation lors de la première connexion, appelé *display mode*. Cela correspond au mode *show* et on ne peut que afficher les paramètres actuels. Il faut exécuter la commande «system-view» pour basculer en mode configuration. Le prompt passe alors du signe ***\&gt;*** au signe ***\\#***.

```
hpswitch&gt;system-view
hpswitch#
```

La sauvegarde de la configuration s'effectuer à l'aide de la commande «*save*».
```
hpswitch# save force
```

Le paramètre «*force*» évite d'avoir à confirmer l'action et spécifier un nom de fichier.
<h2>Configuring remote out of band management</h2>
Cette partie est composée de 5 tâches :
1. Configurer l'IP de l'interface de management,
2. Définir la passerelle par défaut,
3. Créer un utilisateur local de niveau administrateur,
4. Activer un protocole de gestion à distance (ssh de préférence),
5. Configurer les lignes de connexion vty (Virtual Teletype).
<h3>Configuration de l’IP sur l’interface de management</h3>
Après avoir basculé en mode *system-view*, il faut sélectionner l'interface de management puis définir adresse IP et masque de sous-réseau.
```
interface M-GigabitEthernet0/0/0
ip address 172.30.1.77 24
```
<h3>Ajout de la route par défaut pour l’interface de management</h3>
```
ip route-static 0.0.0.0 0 M-GigabitEthernet 0/0/0 172.30.1.250 permanent
```
<h3>création d’un utilisateur local, affectation du rôle « network-admin » (full feature) &amp; autorisation d’accès via SSH</h3>
```
local-user admin
password simple  service-type ssh
authorization-attribute user-role network-admin
```
<h3>Configuration SSH</h3>
Tout d'abord, on active le service SSH :
```
ssh server enable
```

Puis on génère les clés cryptographiques :
```
Public-key local create rss
```
<h3>Configuration des 15 premiers vty &amp; autorisation SSH</h3>
```
line vty 0 15
authentication-mode scheme
protocol inbound ssh
```
<h2>Quelques commandes utiles</h2>
<h3>Configurer un uplink en mode trunk et véhiculer tous les VLANs</h3>
```
Interface GigabitEthernet 1/0/48
port link-type trunk
port trunk permit vlan all
```
<h3>Créer une SVI pour un vlan</h3>
```
interface Vlan-interface
ip address 10.0.0.252 24
```
<h3>Split des interfaces 40GbE en 10GbE</h3>
Les interfaces 40GbE peuvent être divisées en 4 interfaces 10GbE. Cela nécessite un redémarrage du switch ainsi qu'un câble pieuvre (break-out cable).

Sélectionner une interface 40GbE et exécuter la commande suivante :
```
using tengige
```

Au reboot, les interfaces 10GbE sont présentes. Les 4 interfaces 10GbE sont numérotées à la manière d'une sous-interface (XX:X).

```
[HPE-5700]display interface brief
…
XGE1/0/53:1          UP   10G(a)  F(a)   A    1
XGE1/0/53:2          UP   10G(a)  F(a)   A    1
XGE1/0/53:3          UP   10G(a)  F(a)   A    1
XGE1/0/53:4          UP   10G(a)  F(a)   A    1
XGE1/0/54:1          UP   10G(a)  F(a)   A    1
XGE1/0/54:2          UP   10G(a)  F(a)   A    1
XGE1/0/54:3          UP   10G(a)  F(a)   A    1
XGE1/0/54:4          UP   10G(a)  F(a)   A    1
```
<h3>Configurer un port trunk avec un vlan natif</h3>
La configuration d'un vlan natif sur un port trunk nécessite de placer le port en mode hybrid (requiert d'abord le mode access).

```
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port link-type hybrid
Please set the link type of the port to access first.
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port link-type access
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port link-type hybrid
```
<h4>Configuration du native vlan</h4>
```
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port hybrid vlan 500 untagged
```
<h4>Configuration du trunked vlan</h4>
```
[HPE-5700-Ten-GigabitEthernet1/0/53:1]port hybrid vlan 502 tagged
```
<h4>Résumé des commandes nécessaires pour la configuration d'un port Trunk avec un vlan natif.</h4>
```
port link-type access
port link-type hybrid
port hybrid vlan 500 untagged
port hybrid vlan 502 tagged
```

[^1]: IRF : Intelligent Resilient Framework, la technologie mLAG d'HPE (comparable au vPC Cisco).