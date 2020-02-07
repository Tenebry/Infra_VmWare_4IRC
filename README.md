# Projet virtualisation sur VMware - 4IRC

Membres : 
* Maxence Bohard
* Lucas Philippe

## Contexte

Ce projet consiste en un Lab d'une semaine sur VMware pour appréhender la virtualisation ainsi que la solution.

le projet se découpe en plusieurs briques : 

* **Update manager :**
Update manager permet de vérifier et de patcher automatiquement les hôtes ESXI d'un cluster et/ou d'un datacenter. Cela inclus les extensions tel que VMware-tools, les patch et les mises à niveaux.

	* Pour le configurer c'est assez simple. Il faut se rendre sur l'onglet Update Manager du menu depuis Vcenter :
	* Puis créer une "baseline" qui match ce qu'on souhaite faire, dans notre cas les patch seulement :
  * Une fois la baseline crée il faut l'attacher à un chaque hôtes ESXI du cluster :

* **DRS :**


* **VMotion :**

La fonction Vmotion permet la migration de VM à chaud sans impact utilisateur. Pour le mettre en place, il suffit de créer sur les hotes un adapteur réseau NIC Vmkernel sur le vlan Vmotion et de cocher l'option Vmotion. Pour faire l'essai, il suffit de se connecter sur Vcenter et de se rendre sur une Vm --> faire clic droit puis migrer --> migrer la ressource de calcul et le stockage --> puis de choisir l'hote.

![vmotion](https://user-images.githubusercontent.com/47632799/74013834-a2470080-498d-11ea-9168-eaceee4fc4a8.png)
![migration](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/migration.PNG?raw=true)


* **Stockage partagé :**

Afin d'avoir un accès aux fonctions de hautes disponibilités, drs, fault tolérance etc.. il est nécessaire de posséder un espace de stockage partagé dans lequel nous déplaçerons nos VM afin que tous les hotes du cluster puisse y accéder. Dans notre infrastructure, nous utiliserons Freenas pour créer nos LUN que nous connecterons à notre cluster. Bien entendu, si l'hote qui heberge notre stockage partagé tombe, nous perdons les VM qui sont à l'intérieur, si cela était possible, il aurait fallu redonder notre serveur freenas, mais nous n'avons pas assez d'hotes pour ça.

![partage](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/stockage_partag%C3%A9.png?raw=true)

Il suffit de Migrer les VM voulues dans l'espace partagé LUN :

![lun](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/lun.png?raw=true)

* **Datacenter & Cluster:**

* **ESXI & VCenter:**












## Le  réseau Virtuel

### Fonctionnement

Les technologies réseau mis en oeuvre sur les routeurs :
  - **configuration IP** : configuration basique des équipements réseau via leurs adresses IP uniques et le masques permettant de définir les différent sous-réseau.
  - **OSPF** : Open Shortest Path First, un protocol de routage mis en place dans la backbone et ne servant qu'a annoncer les différents sous-réseau de l'Autonomous System BGP. Les interfaces étant connectés à un autre AS sont passé en passive et ne participerons pas à l'OSPF.
  - **BGP** : Border Gateway Protocol est un protocol de routage permettant une forte redondance 
    - **IBGP**: les voisins IBGP sont les routeur du même AS, on utilise l'attribut next-hop self et update-source loopback
    - **EBGP**: les voisins EBGP sont les routeurs d'un autre AS, dans notre cas le ou les routeur des casernes. On utilise l'attribut multihop et remote-private AS pour la balance de charge sur des interconnexion avec multiple lien et pour palier au problème d'un AS diviser sur plusieurs LAN.
    - **peer-group**: un "objet" servant à factorier le code BGP via un template, j'ai définis un template EBGP et IBGP contennant les commandes habituels.
    - **route reflector**: route reflector est une technologie permetant de mettre en place un "serveur" BGP et donc de ne pas recourir à une topologie full mesh (en IBGP une route IBGP n'est pas retranmise pour éviter les boucle), on factorise le code correspondant à BGP.
  - **ACL**: Filtrage du traffic entrant et sortant sur internet et sur le LAN datacenter via des règles définis
  - **routage**: 
  - **VRRP**: Virtual Rouder Redondancy Protocol, mis en place sur le LAN 2, il permet d'assurer une redondance des routeurs via une adresse IP virtuel unique connus des hôtes.
 
 Les technologies mis en oeuvre sur l'infrastructure virtuel : 
  - **Serveurs** : L'intégralité des serveurs de production sont externalisés dans le cloud Azure : simulation & emergency_manager
  - **DHCP** : Seul serveur local, présent dans la caserne "data-center", et servant à octroyer des IP dynamiquement aux hôtes dans tout les LAN des casernes.
  - **Client** : VM MXLinux sous VirtualBox, elle peux joindre les autres casernes et sortir sur internet pour accèder a l'emergency manager.

 Les technologies de templating des LAN de casernes :
  - **Template JINJA**: Fichier de template faisant appel aux variables définis dans le fichier data.yaml. 
  - **Fichier Python**: Fichier de compilation et de "rendu" du template.
  - **Fichier DATA YAML**: Fichier dictionnaire de données à remplir par l'administrateur réseau avec les informations nécessaire au déploiement.

![schéma_réseau](https://github.com/Tenebry/Network_On_Fire/blob/master/Capture.PNG)

