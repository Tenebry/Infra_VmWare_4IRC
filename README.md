# Projet virtualisation sur VMware - 4IRC

Membres : 
* Maxence Bohard
* Lucas Philippe

## Contexte

Ce projet consiste en un Lab d'une semaine sur VMware pour appréhender la virtualisation ainsi que la solution.
Le schéma de notre infrastructure est identique à celui du sujet.
le projet se découpe en plusieurs briques : 

* **Update manager :**
Update manager permet de vérifier et de patcher automatiquement les hôtes ESXI d'un cluster et/ou d'un datacenter. Cela inclus les extensions tel que VMware-tools, les patchs et les mises à niveaux.

	* Pour le configurer c'est assez simple. Il faut se rendre sur l'onglet Update Manager du menu depuis Vcenter :
	![menu_update_manager](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/Menu.png?raw=true)
	* Puis créer une "baseline" qui match ce que l'on souhaite faire, dans notre cas les patchs seulement :
	![menu_baseline](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/UpdateManagerPNG.PNG?raw=true)
	![baseline](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/Baseline.png?raw=true)
	* Une fois la baseline crée il faut l'attacher à un chaque hôtes ESXI du cluster via le menu "mise à jour" de ceux-çi :
	![attached_baseline](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/BaselineAttached.PNG?raw=true)
	* Enfin on peux vérifier la conformité et lancer les mises à jours depuis l'onglet dédier dans le cluster. On peux aussi creer une tâche automatique de vérification via l'onglet mise à jour du cluster:
	![MAJ & planifier](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/Planifier.PNG?raw=true)


	
* **DRS :**
DRS permet un bascule de charge automatique sur les différents hôtes ESXI d'un même cluster. 

	* Il suffit d'activer la fonctionnalité et de la configuré pour que celle çi soit en marche :
	![DRS](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/DRS.PNG?raw=true)
	* De la même façon on peux mettre en place une tâche automatique dé vérification DRS : 
	![DRS setting](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/SettingDRS.PNG?raw=true)
	![DRS setting 2](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/SettingDRS-2.PNG?raw=true)
	

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
Afin de pouvoir bénéficier de toutes les fonctionnalités, il faut installer sur l'un des hotes Vcenter afin de pouvoir gérer notre infra et nos clusters. Nous lui attribuons une adresse IP (10.0.0.10) sur le LAN Management afin de pouvoir accéder à l'interface web.
Ainsi, nous pouvons ajouter nos hotes (10.0.0.1 & 10.0.0.2) avec leurs adresses IP et créer notre cluster avec les options désirées.
Ici c'est DRS qui nous intéresse car nous ne pouvons tester HA.
![cluster](https://github.com/Tenebry/Infra_VmWare_4IRC/blob/master/cluster.png?raw=true)

* **ESXI & VCenter:**




