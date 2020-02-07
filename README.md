
# Projet virtualisation sur VMware - 4IRC

Membres : 
* Maxence Bohard
* Lucas Philippe

## Contexte

Ce projet consiste en un Lab d'une semaine sur VMware pour appréhender la virtualisation ainsi que la solution.

le projet se découpe en plusieurs briques : 

* **Update manager :**


* **DRS :**


* **VMotion :**

La fonction Vmotion permet la migration de VM à chaud sans impact utilisateur. Pour le mettre en place, il suffit de créer sur les hotes un adapteur réseau NIC Vmkernel sur le vlan Vmotion et de cocher l'option Vmotion. Pour faire l'essai, il suffit de se connecter sur Vcenter et de se rendre sur une Vm --> faire clic droit puis migrer --> migrer la ressource de calcul et le stockage --> puis de choisir l'hote.

* **Stockage partagé :**


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

