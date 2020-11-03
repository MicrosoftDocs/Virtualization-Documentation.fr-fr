---
title: Prise en main du mode Swarm
description: Initialisation d’un cluster Swarm, création d’un réseau de superposition et association d’un service au réseau.
keywords: docker, conteneurs, swarm, orchestration
author: kallie-b
ms.author: jgerend
ms.date: 02/9/2017
ms.topic: how-to
ms.assetid: 5ceb9626-7c48-4d42-81f8-9c936595ad85
ms.openlocfilehash: 775de3b2a1b7d0379689ba7f813b662a0ab3959b
ms.sourcegitcommit: a5cd6c4477a5aee8d2a66c588ac5f799f6e8b035
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93044305"
---
# <a name="getting-started-with-swarm-mode"></a>Prise en main du mode Swarm

## <a name="what-is-swarm-mode"></a>Qu’est-ce que le « mode Swarm » ?
Le mode Swarm est une fonctionnalité de Docker qui fournit des fonctionnalités prédéfinies d’orchestration de conteneur, y compris le clustering natif des hôtes Docker et la planification des charges de travail de conteneur. Un groupe d’hôtes Docker constitue un cluster « Swarm » lorsque leurs moteurs Docker s’exécutent ensemble en « mode Swarm ». Pour un contexte supplémentaire sur le mode Swarm, reportez-vous au [site de documentation principale de Docker](https://docs.docker.com/engine/swarm/).

## <a name="manager-nodes-and-worker-nodes"></a>Nœuds de gestionnaire et nœuds de travail
Un cluster Swarm est composé de deux types d’hôte de conteneur : *nœuds de gestionnaire* et *nœuds de travail*. Chaque Swarm est initialisé via un nœud de gestionnaire, et toutes les commandes de l’interface de ligne de commande Docker pour contrôler et analyser un cluster Swarm doivent être exécutées à partir de l’un de ses nœuds de gestionnaire. Les nœuds de gestionnaire peuvent être comparés aux « gardiens » de l’état Swarm : ensemble, ils forment un groupe consensuel qui gère la reconnaissance de l’état des services exécutés dans le cluster Swarm. Leur tâche consiste à s’assurer que l’état réel du cluster Swarm correspond à son état souhaité, défini par le développeur ou l’administrateur.

>[!NOTE]
>Tout cluster Swarm peut compter plusieurs nœuds de gestionnaire, mais il doit toujours en comprendre *au moins un*.

Les nœuds de travail sont orchestrés par Docker Swarm via les nœuds de gestionnaire. Pour joindre un cluster Swarm, un nœud de travail doit utiliser un « jeton de jointure », qui aura été généré par le nœud de gestionnaire à l’initialisation du cluster Swarm. Les nœuds de travail reçoivent et exécutent simplement les tâches des nœuds de gestionnaire. En conséquence, ils ne nécessitent (et ne détiennent) pas de reconnaissance de l’état Swarm.

## <a name="swarm-mode-system-requirements"></a>Configuration requise du mode Swarm

Au moins un ordinateur physique ou un système virtuel (pour utiliser les fonctionnalités complètes du mode Swarm, il est conseillé de disposer d’au moins deux nœuds) exécutant soit **Windows 10 Creators Update** , soit **Windows Server 2016** *avec toutes les dernières mises à jour\** , configuré comme hôte de conteneur. Pour plus d’informations sur la prise en main des conteneurs Docker sur Windows 10, consultez la rubrique [Conteneurs Windows sur Windows 10](../quick-start/set-up-environment.md) ou [Conteneurs Windows sur Windows Server](../quick-start/set-up-environment.md).

\**_Remarque_*  : Docker Swarm sur Windows Server 2016 nécessitent la mise à jour [KB4015217](https://support.microsoft.com/help/4015217/windows-10-update-kb4015217).

**Moteur Docker v1.13.0 ou ultérieur**

Ports ouverts : les ports suivants doivent être disponibles sur chaque hôte. Sur certains systèmes, ces ports sont ouverts par défaut.
- Port TCP 2377 pour les communications de gestion de cluster
- Port TCP et UDP 7946 pour la communication entre les nœuds
- Port UDP 4789 pour le trafic du réseau de superposition

## <a name="initializing-a-swarm-cluster"></a>Initialisation d’un cluster Swarm

Pour initialiser un cluster Swarm, exécutez simplement la commande suivante à partir d’un de vos hôtes de conteneur (en remplaçant \<HOSTIPADDRESS\> par l’adresse IPv4 locale de votre ordinateur hôte) :

```
# Initialize a swarm
C:\> docker swarm init --advertise-addr=<HOSTIPADDRESS> --listen-addr <HOSTIPADDRESS>:2377
```
Lorsque cette commande est exécutée à partir d’un hôte de conteneur donné, le moteur Docker de cet hôte commence à s’exécuter en mode Swarm en tant que nœud de gestionnaire.

## <a name="adding-nodes-to-a-swarm"></a>Ajout de nœuds à un cluster Swarm

La disponibilité de plusieurs nœuds n’est *pas* indispensable pour tirer parti des fonctionnalités des mode Swarm et Mise en réseau de superposition. Toutes les fonctionnalités Swarm/superposition peuvent être utilisées avec un seul et même hôte s’exécutant en mode Swarm (autrement dit, un nœud de gestionnaire placé en mode Swarm avec la commande `docker swarm init`).

### <a name="adding-workers-to-a-swarm"></a>Ajout de nœuds de travail à un cluster Swarm

Une fois qu’un cluster Swarm a été initialisé à partir d’un nœud de gestionnaire, d’autres hôtes peuvent être ajoutés à ce cluster en tant que nœuds de travail à l’aide d’une simple autre commande :

```
C:\> docker swarm join --token <WORKERJOINTOKEN> <MANAGERIPADDRESS>
```

Ici, \<MANAGERIPADDRESS\> est l’adresse IP locale d’un nœud de gestionnaire Swarm et \<WORKERJOINTOKEN\> le jeton-jointure du nœud worker fourni en tant que sortie par la commande `docker swarm init` exécutée à partir du nœud de gestionnaire. Le jeton de jointure peut également être obtenu en exécutant l’une des commandes suivantes à partir du nœud de gestionnaire après l’initialisation du cluster Swarm :

```
# Get the full command required to join a worker node to the swarm
C:\> docker swarm join-token worker

# Get only the join-token needed to join a worker node to the swarm
C:\> docker swarm join-token worker -q
```

### <a name="adding-managers-to-a-swarm"></a>Ajout de nœuds de gestionnaire à un cluster Swarm
D’autres nœuds de gestionnaire peuvent être ajoutés à un cluster Swarm à l’aide de la commande suivante :

```
C:\> docker swarm join --token <MANAGERJOINTOKEN> <MANAGERIPADDRESS>
```

Là encore, \<MANAGERIPADDRESS\> est l’adresse IP locale d’un nœud de gestionnaire de cluster Swarm. Le jeton de jointure \<MANAGERJOINTOKEN\> est un jeton-jointure de *gestionnaire* , qui peut être obtenu en exécutant l’une des commandes suivantes à partir d’un nœud de gestionnaire existant :

```
# Get the full command required to join a **manager** node to the swarm
C:\> docker swarm join-token manager

# Get only the join-token needed to join a **manager** node to the swarm
C:\> docker swarm join-token manager -q
```

## <a name="creating-an-overlay-network"></a>Création d’un réseau de superposition

Une fois qu’un cluster Swarm a été configuré, des réseaux de superposition peuvent être créés dans le cluster. Vous pouvez créer un réseau de superposition en exécutant la commande suivante à partir d’un nœud de gestionnaire du cluster Swarm :

```
# Create an overlay network
C:\> docker network create --driver=overlay <NETWORKNAME>
```

Ici, \<NETWORKNAME\> est le nom que vous souhaitez donner à votre réseau.

## <a name="deploying-services-to-a-swarm"></a>Déploiement de services dans un cluster Swarm
Une fois qu’un réseau de superposition a été créé, des services peuvent être créés et associés au réseau. Un service est créé à l’aide de la syntaxe suivante :

```
# Deploy a service to the swarm
C:\> docker service create --name=<SERVICENAME> --endpoint-mode dnsrr --network=<NETWORKNAME> <CONTAINERIMAGE> [COMMAND] [ARGS…]
```

Ici, \<SERVICENAME\> est le nom que vous souhaitez donner au service. Il s’agit du nom que vous utiliserez pour référencer le service via la découverte de service (qui utilise un serveur DNS natif de Docker). \<NETWORKNAME\> est le nom du réseau auquel vous souhaitez connecter ce service (par exemple, « monRéseauSuperposition »). \<CONTAINERIMAGE\> est le nom de l’image conteneur qui est défini par le service.

>[!NOTE]
>Le deuxième argument de cette commande, `--endpoint-mode dnsrr`, est nécessaire pour indiquer au moteur Docker que la stratégie de tourniquet (round robin) DNS sera utilisée pour équilibrer le trafic réseau entre les points de terminaison du conteneur de service. Actuellement, l’usage d’un tourniquet (round robin) DNS est la seule stratégie d’équilibrage de charge prise en charge sur Windows Server 2016. Le [maillage de routage](https://docs.docker.com/engine/swarm/ingress/) pour les hôtes Windows Docker est pris en charge sur Windows Server 2019 (et versions ultérieures), mais pas sur Windows Server 2016. Les utilisateurs en quête d’une autre stratégie d’équilibrage de charge sur Windows Server 2016 dès aujourd’hui peuvent configurer un équilibreur de charge externe (par exemple, NGINX) et utiliser le [mode de publication de port](https://docs.docker.com/engine/reference/commandline/service_create/#/publish-service-ports-externally-to-the-swarm--p---publish) de Swarm pour exposer les ports d’hôte de conteneur sur lesquels équilibrer le trafic.

## <a name="scaling-a-service"></a>Évolutivité d’un service
Une fois qu’un service est déployé dans un cluster Swarm, les instances de conteneur composant ce service sont déployées dans le cluster. Par défaut, le nombre d’instances de conteneur soutenant un service, autrement dit le nombre de « réplicas » ou de « tâches » d’un service, est de une. Toutefois, un service peut être créé avec plusieurs tâches à l’aide de l’option `--replicas` pour la commande `docker service create` ou en mettant à l’échelle le service après sa création.

L’évolutivité de service est l’un des principaux avantages de Docker Swarm. Elle peut également être exploitée avec une seule commande Docker :

```
C:\> docker service scale <SERVICENAME>=<REPLICAS>
```

Ici, \<SERVICENAME\> est le nom du service mis à l’échelle, et \<REPLICAS\> est le nombre de tâches ou d’instances de conteneur, vers lesquelles le service est mis à l’échelle.


## <a name="viewing-the-swarm-state"></a>Affichage de l’état Swarm

Plusieurs commandes sont utiles pour afficher l’état d’un cluster Swarm et les services qui y sont exécutés.

### <a name="list-swarm-nodes"></a>Répertorier les nœuds Swarm
Utilisez la commande suivante pour afficher la liste des nœuds actuellement joints à un cluster Swarm, notamment des informations sur l’état de chaque nœud. Cette commande doit être exécutée à partir d’un **nœud de gestionnaire**.

```
C:\> docker node ls
```

Dans la sortie de cette commande, vous pouvez remarquer l’un des nœuds marqués d’un astérisque (*) ; l’astérisque indique simplement le nœud actuel, c.-à-d. le nœud à partir duquel la commande `docker node ls` a été exécutée.

### <a name="list-networks"></a>Dresser la liste des réseaux
Utilisez la commande suivante pour afficher la liste des réseaux qui existent sur un nœud donné. Pour afficher les réseaux de superposition, vous devez exécuter cette commande à partir d’un **nœud de gestionnaire** exécuté en mode Swarm.

```
C:\> docker network ls
```

### <a name="list-services"></a>Liste des services
Utilisez la commande suivante pour afficher la liste des services en cours d’exécution sur un cluster Swarm, y compris des informations sur leur état.

```
C:\> docker service ls
```

### <a name="list-the-container-instances-that-define-a-service"></a>Répertorier les instances de conteneur qui définissent un service
Utilisez la commande suivante pour afficher des détails sur les instances de conteneur exécutées pour un service donné. La sortie de cette commande inclut les ID et les nœuds sur lesquels chaque conteneur s’exécute, ainsi que des informations sur l’état des conteneurs.

```
C:\> docker service ps <SERVICENAME>
```
## <a name="linuxwindows-mixed-os-clusters"></a>Clusters avec systèmes d’exploitation mixtes Linux + Windows

Récemment, un membre de notre équipe a publié une courte démonstration en trois parties sur la façon de configurer une application pour systèmes d’exploitation mixtes Windows + Linux à l’aide de Docker Swarm. C'est un excellent point de départ si vous débutez avec Docker Swarm ou si vous souhaitez l'utiliser pour exécuter des applications pour systèmes d’exploitation mixtes. En savoir plus dès à présent :
- [Utiliser Docker Swarm pour exécuter une application Windows + Linux en conteneur (partie 1/3)](https://www.youtube.com/watch?v=ZfMV5JmkWCY&t=170s)
- [Utiliser Docker Swarm pour exécuter une application Windows + Linux en conteneur (partie 2/3)](https://www.youtube.com/watch?v=VbzwKbcC_Mg&t=406s)
- [Utiliser Docker Swarm pour exécuter une application Windows + Linux en conteneur (partie 3/3)](https://www.youtube.com/watch?v=I9oDD78E_1E&t=354s)

### <a name="initializing-a-linuxwindows-mixed-os-cluster"></a>Initialisation d’un cluster avec systèmes d’exploitation mixtes Linux + Windows
L’initialisation d’un cluster Swarm à systèmes d’exploitation mixtes est facile. Tant que vos règles de pare-feu sont correctement configurées et que vos ordinateurs hôtes ont accès l'un à l'autre, tout ce dont vous avez besoin pour ajouter un hôte Linux à un cluster Swarm est la commande `docker swarm join` standard :
```
C:\> docker swarm join --token <JOINTOKEN> <MANAGERIPADDRESS>
```
Vous pouvez également initialiser un cluster Swarm à partir d’un ordinateur hôte Linux à l’aide de la même commande que celle que vous exécuteriez pour initialiser ce cluster Swarm à partir d’un ordinateur hôte Windows :
```
# Initialize a swarm
C:\> docker swarm init --advertise-addr=<HOSTIPADDRESS> --listen-addr <HOSTIPADDRESS>:2377
```

### <a name="adding-labels-to-swarm-nodes"></a>Ajout d'étiquettes à des nœuds de cluster Swarm
Pour lancer un Service Docker vers un cluster Swarm à systèmes d’exploitation mixtes, il faut pouvoir distinguer ceux des nœuds qui exécutent le système d’exploitation pour lequel ce service est conçu, et ceux qui ne le sont pas. Les [étiquettes d’objet docker](https://docs.docker.com/engine/userguide/labels-custom-metadata/) sont un moyen utile d’étiqueter les nœuds. Les services peuvent ainsi être créés et configurés de manière à ne s’exécuter que sur les nœuds qui correspondent à leur système d’exploitation.

>[!NOTE]
>Les [étiquettes d’objet Docker](https://docs.docker.com/engine/userguide/labels-custom-metadata/) peuvent être utilisées pour appliquer des métadonnées à divers objets Docker (notamment des images de conteneur, des conteneurs, des volumes ou des réseaux) et à diverses fins. Par exemple, elles peuvent servir à séparer les composants « front-end » et « back-end » d’une application en autorisant la planification de microservices front-end uniquement sur des nœuds « front-end » et la planification de microservices back-end uniquement sur des nœuds « back-end ». Dans ce cas, nous utiliserons les étiquettes sur des nœuds afin de distinguer les nœuds du système d’exploitation Windows et ceux du système d’exploitation Linux.

Pour étiqueter vos nœuds de clusters Swarm existants, utilisez la syntaxe suivante :

```
C:\> docker node update --label-add <LABELNAME>=<LABELVALUE> <NODENAME>
```

Ici, `<LABELNAME>` est le nom de l’étiquette que vous créez. Dans ce cas, par exemple, puisque nous allons distinguer les nœuds selon leur système d’exploitation, il serait logique de nommer l’étiquette « système d’exploitation ». `<LABELVALUE>` est la valeur de l’étiquette (dans ce cas, vous pouvez choisir d’utiliser les valeurs « windows » et « linux »). (Bien entendu, vous pouvez nommer les étiquettes et leurs valeurs comme vous l'entendez, tant que vous restez cohérent). `<NODENAME>` est le nom du nœud que vous étiquetez. Vous pouvez vous retrouver les noms de vos nœuds en exécutant la commande `docker node ls`.

**Par exemple** , si vous avez quatre nœuds dans votre cluster Swarm, dont deux nœuds Windows et deux nœuds Linux, vos commandes de mise à jour de l’étiquette peuvent ressembler à ceci :

```
# Example -- labeling 2 Windows nodes and 2 Linux nodes in a cluster...
C:\> docker node update --label-add os=windows Windows-SwarmMaster
C:\> docker node update --label-add os=windows Windows-SwarmWorker1
C:\> docker node update --label-add os=linux Linux-SwarmNode1
C:\> docker node update --label-add os=linux Linux-SwarmNode2
```

### <a name="deploying-services-to-a-mixed-os-swarm"></a>Déploiement de services dans un cluster Swarm à systèmes d'exploitation mixtes
Avec des étiquettes pour vos nœuds de cluster Swarm, le déploiement des services dans votre cluster est simple. Il vous suffit d'utiliser l'option `--constraint` pour la commande [`docker service create`](https://docs.docker.com/engine/reference/commandline/service_create/) :

```
# Deploy a service with swarm node constraint
C:\> docker service create --name=<SERVICENAME> --endpoint-mode dnsrr --network=<NETWORKNAME> --constraint node.labels.<LABELNAME>=<LABELVALUE> <CONTAINERIMAGE> [COMMAND] [ARGS…]
```

Par exemple, à l’aide de l’étiquette et de la nomenclature des valeurs d’étiquette de l’exemple ci-dessus, un ensemble de commandes de création de service (l'une pour un service basé sur Windows et l’autre pour un service basé sur Linux) pourrait ressembler à ceci :

```
# Example -- using the 'os' label and 'windows'/'linux' label values, service creation commands might look like these...

# A Windows service
C:\> docker service create --name=win_s1 --endpoint-mode dnsrr --network testoverlay --constraint 'node.labels.os==windows' microsoft/nanoserver:latest powershell -command { sleep 3600 }

# A Linux service
C:\> docker service create --name=linux_s1 --endpoint-mode dnsrr --network testoverlay --constraint 'node.labels.os==linux' redis
```

## <a name="limitations"></a>Limitations
Actuellement, le mode Swarm sur Windows connaît les limitations suivantes :
- Chiffrement de plan-données non pris en charge (autrement dit, le trafic conteneur à conteneur à l’aide de l’option `--opt encrypted`)
- Le [maillage de routage](https://docs.docker.com/engine/swarm/ingress/) pour les hôtes Windows Docker n’est pas pris en charge sur Windows Server 2016, mais uniquement à partir de Windows Server 2019. Les utilisateurs recherchant une autre stratégie d’équilibrage de charge dès aujourd'hui peuvent configurer un équilibreur de charge externe (par exemple, NGINX) et utiliser le [mode de publication de port](https://docs.docker.com/engine/reference/commandline/service_create/#/publish-service-ports-externally-to-the-swarm--p---publish) de Swarm pour exposer les ports d’hôte de conteneur sur lesquels équilibrer la charge. Plus de détails sur ce point ci-dessous.

 >[!NOTE]
>Pour plus d’informations sur la configuration de la maille de routage Docker Swarm, consultez ce [billet de blog](/virtualization/community/team-blog/2017/20170926-docker-s-routing-mesh-available-with-windows-server-version-1709).

## <a name="publish-ports-for-service-endpoints"></a>Publication de ports pour les points de terminaison du service
 Les utilisateurs cherchant à publier des ports pour leurs points de terminaison de service peuvent aujourd’hui le faire à l’aide du mode de publication de port ou de la fonctionnalité de [maillage de routage](https://docs.docker.com/engine/swarm/ingress/) du Docker Swarm.

Pour que les ports hôtes soient publiés pour chacun des points de terminaison de tâches/conteneur qui définissent un service, utilisez l’argument `--publish mode=host,target=<CONTAINERPORT>` de la commande `docker service create` :

```
# Create a service for which tasks are exposed via host port
C:\ > docker service create --name=<SERVICENAME> --publish mode=host,target=<CONTAINERPORT> --endpoint-mode dnsrr --network=<NETWORKNAME> <CONTAINERIMAGE> [COMMAND] [ARGS…]
```

Par exemple, la commande suivante créera un service, « s1 », pour lequel chaque tâche sera exposée via le port 80 du conteneur et un port d’hôte sélectionné de façon aléatoire.

```
C:\ > docker service create --name=s1 --publish mode=host,target=80 --endpoint-mode dnsrr web_1 powershell -command {echo sleep; sleep 360000;}
```

Une fois un service créé à l’aide du mode de publication de port, ce service peut être interrogé pour afficher le mappage des ports pour chaque tâche du service :

```
C:\ > docker service ps <SERVICENAME>
```
La commande ci-dessus retournera les détails de chaque instance du conteneur en cours d’exécution pour votre service (sur tous vos ordinateurs hôtes de votre cluster Swarm). Une colonne de la sortie, la colonne « ports », comprendra des informations de port pour chaque hôte sous la forme \<HOSTPORT\>->\<CONTAINERPORT\>/tcp. Les valeurs de \<HOSTPORT\> seront différentes pour chaque instance du conteneur, car chaque conteneur est publié sur son propre port d’hôte.


## <a name="tips--insights"></a>Conseils et informations

#### <a name="existing-transparent-network-can-block-swarm-initializationoverlay-network-creation"></a>*Un réseau transparent existant peut bloquer l’initialisation d’un cluster Swarm ou la création d’un réseau de superposition*
Sous Windows, les pilotes de réseau de superposition ou transparent nécessitent la liaison d'un vSwitch externe à une carte réseau hôte (virtuelle). Lorsqu’un réseau de superposition est créé, un nouveau commutateur est créé, puis connecté à une carte réseau ouverte. Le mode de réseau transparent utilise également une carte réseau hôte. Dans le même temps, une carte réseau donnée ne peut être liée qu'à un seul commutateur à la fois. Si un ordinateur hôte n'a qu’une seule carte réseau, il ne peut la connecter qu'à un vSwitch externe à la fois, que ce soit celui d'un réseau de superposition ou d'un réseau transparent.

Si un hôte de conteneur n'a qu’une seule carte réseau, on risque donc de voir un réseau transparent bloquer la création d’un réseau de superposition (ou vice versa), car le réseau transparent occupe actuellement l’unique interface de réseau virtuel de l’ordinateur hôte.

Il existe deux façons de contourner ce problème :
- *Option 1 – Supprimer le réseau transparent existant :* avant d’initialiser un cluster Swarm, vérifiez qu’il n’existe aucun réseau transparent sur votre hôte de conteneur. Supprimez les réseaux transparents pour que votre hôte dispose d'une carte réseau virtuelle libre pouvant servir à créer un réseau de superposition.
- *Option 2 – Créer une carte réseau (virtuelle) supplémentaire sur votre ordinateur hôte :* Au lieu de supprimer un éventuel réseau transparent présent sur votre ordinateur hôte, vous pouvez créer une carte réseau supplémentaire sur cet hôte et l’utiliser pour créer un réseau de superposition. Pour ce faire, créez simplement une nouvelle carte réseau externe (à l’aide de PowerShell ou du Gestionnaire Hyper-V). Une fois la nouvelle interface en place, lorsque votre cluster Swarm sera initialisé, le service de réseau hôte (HNS) la reconnaîtra automatiquement sur votre ordinateur hôte et l’utilisera pour lier le vSwitch externe afin de créer un réseau de superposition.