---
title: À propos des conteneurs Windows
description: Les conteneurs constituent une technologie d’empaquetage et d’exécution d’applications, notamment les applications Windows, dans le cloud et divers environnements locaux. Cette rubrique explique comment Microsoft, Windows et Azure vous aident à développer et déployer des applications dans des conteneurs, notamment avec Docker et Azure Kubernetes Service.
keywords: docker, conteneurs
author: taylorb-microsoft
ms.author: jgerend
ms.date: 10/22/2019
ms.topic: overview
ms.assetid: 8e273856-3620-4e58-9d1a-d1e06550448
ms.openlocfilehash: 25221fdfac5507d169adcaf95d050bcb06c7baeb
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990862"
---
# <a name="windows-and-containers"></a>Windows et conteneurs

Les conteneurs constituent une technologie d’empaquetage et d’exécution d’applications Windows et Linux, dans le cloud et divers environnements locaux. Les conteneurs offrent un environnement léger et isolé qui simplifie le développement, le déploiement et la gestion des applications. Les conteneurs démarrent et s’arrêtent rapidement, ce qui les rend idéaux pour les applications qui doivent s’adapter promptement à l’évolution de la demande. La nature légère du conteneur en fait également un outil pratique qui permet d’accroître la densité et l’utilisation de votre infrastructure.

![Graphique illustrant la façon dont les conteneurs peuvent s’exécuter dans le cloud ou localement, en prenant en charge des applications monolithiques ou des microservices écrits dans pratiquement n’importe quel langage.](media/about-3-box.png)

## <a name="the-microsoft-container-ecosystem"></a>L’écosystème des conteneurs Microsoft

Microsoft fournit un certain nombre d’outils et de plateformes pour vous aider à développer et déployer des applications dans des conteneurs :

- <strong>Exécutez des conteneurs Windows ou Linux sur Windows 10</strong> à des fins de développement et de test avec [Docker Desktop](https://store.docker.com/editions/community/docker-ce-desktop-windows), ce qui vous permet d’utiliser les fonctionnalités de conteneurs intégrées à Windows. Vous pouvez également [exécuter des conteneurs en mode natif sur Windows Server](../quick-start/set-up-environment.md?tabs=Windows-Server).
- <strong>Développez, testez, publiez et déployez des conteneurs Windows</strong> avec une [prise en charge solide des conteneurs dans Visual Studio](/visualstudio/containers/overview) et [Visual Studio Code](https://code.visualstudio.com/docs/azure/docker), ce qui inclut la prise en charge de Docker, Docker Compose, Kubernetes, Helm et d’autres technologies avantageuses.
- <strong>Publiez vos applications sous forme d’images conteneur</strong> sur l’instance DockerHub publique afin que d’autres personnes puissent les utiliser, ou sur une instance [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) privée pour le développement et déploiement de votre propre organisation, en procédant à l’envoi (push) et au tirage (pull) directement à partir de Visual Studio et de Visual Studio Code.
- <strong>Déployez des conteneurs à grande échelle sur Azure</strong> ou d’autres clouds :

  - Tirez (pull) votre application (image conteneur) d’un registre de conteneurs comme Azure Container Registry, puis déployez-la et gérez-la à grande échelle avec un orchestrateur comme [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) ou [Azure Fabric Service](/azure/service-fabric/).
  - Azure Fabric Service déploie des conteneurs sur des machines virtuelles Azure et les gère à grande échelle, qu’il s’agisse de douzaines, de centaines, voire de milliers de conteneurs. Les machines virtuelles Azure exécutent une image Windows Server personnalisée (si vous déployez une application Windows) ou une image Ubuntu Linux personnalisée (si vous déployez une application Linux).
- <strong>Déployez des conteneurs locaux</strong> au moyen d’[Azure Stack avec le moteur AKS](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) (en préversion pour des conteneurs Linux) ou d’[Azure Stack avec OpenShift](/azure/virtual-machines/linux/openshift-azure-stack). Vous pouvez également configurer Kubernetes vous-même sur Windows Server (voir [Kubernetes sur Windows](../kubernetes/getting-started-kubernetes-windows.md)) ; nous travaillons également sur la prise en charge de l’exécution des [conteneurs Windows sur RedHat OpenShift Container Platform](https://techcommunity.microsoft.com/t5/Networking-Blog/Managing-Windows-containers-with-Red-Hat-OpenShift-Container/ba-p/339821).

## <a name="how-containers-work"></a>Fonctionnement des conteneurs

Un conteneur est un silo léger et isolé qui permet l’exécution d’une application sur le système d’exploitation hôte. Les conteneurs s’appuient sur le noyau du système d’exploitation hôte (qui peut être considéré comme la plomberie enfouie du système d’exploitation), tel qu’illustré dans ce schéma.

![Diagramme d’architecture montrant comment les conteneurs s’exécutent au-dessus du noyau](media/container-diagram.svg)

Bien qu’un conteneur partage le noyau du système d’exploitation hôte, il n’obtient pas un accès sans limites à celui-ci. Le conteneur récupère plutôt une vue isolée, et virtualisée dans certains cas, du système. Par exemple, un conteneur peut accéder à une version virtualisée du système de fichiers et du Registre, mais les modifications effectuées n’affectent que le conteneur et sont ignorées lorsqu’il s’arrête. Pour enregistrer des données, le conteneur peut monter un stockage persistant, comme un [disque Azure](https://azure.microsoft.com/services/storage/disks/) ou un partage de fichiers (notamment [Azure Files](https://azure.microsoft.com/services/storage/files/)).

Un conteneur s’appuie sur le noyau, mais le noyau ne fournit pas tous les services et les API qui sont nécessaires à une application pour s’exécuter : la plupart de ces besoins sont pourvus par les fichiers système (bibliothèques) qui s’exécutent au-dessus du noyau en mode utilisateur. Étant donné qu’un conteneur est isolé de l’environnement du mode utilisateur de l’hôte, il a besoin de sa propre copie des fichiers système en mode utilisateur ; ceux-ci sont empaquetés dans un fichier appelé image de base. L’image de base sert de couche primordiale sur laquelle est construit votre conteneur, en lui fournissant des services de système d’exploitation non pourvus par le noyau. Nous aborderons les images conteneur plus en détail ultérieurement.

## <a name="containers-vs-virtual-machines"></a>Conteneurs ou machines virtuelles

Contrairement à un conteneur, une machine virtuelle exécute un système d’exploitation complet, y compris son propre noyau, comme illustré dans ce schéma.

![Diagramme d’architecture montrant comment les machines virtuelles exécutent un système d’exploitation complet à côté du système d’exploitation hôte](media/virtual-machine-diagram.svg)

Les conteneurs et les machines virtuelles ont chacun leur utilisation propre : en fait, beaucoup de déploiements de conteneurs utilisent les machines virtuelles comme système d’exploitation hôte au lieu de s’exécuter directement sur le matériel, surtout lors de l’exécution de conteneurs dans le cloud.

Pour de plus amples informations sur les similitudes et les différences entre ces technologies complémentaires, consultez [Conteneurs ou machines virtuelles](containers-vs-vm.md).

## <a name="container-images"></a>Images de conteneur

Tous les conteneurs sont créés à partir d’images conteneur. Une image conteneur est un ensemble de fichiers organisés en une pile de couches résidant sur votre machine locale, ou dans un registre de conteneurs distant. L’image conteneur se compose des fichiers du système d’exploitation en mode utilisateur indispensables à la prise en charge de votre application, de votre application, des runtimes ou dépendances de votre application, ainsi que de tout autre fichier de configuration diverse dont votre application a besoin pour s’exécuter correctement.

Microsoft propose plusieurs images (appelées images de base) que vous pouvez utiliser comme point de départ pour créer votre propre image conteneur :

* <strong>Windows</strong> : l’ensemble complet des services système (moins les rôles serveur) et API Windows.
* <strong>Windows Server Core</strong> : une image plus petite qui contient un sous-ensemble des API Windows Server, c’est-à-dire le .NET Framework complet. Il comprend également la plupart des rôles de serveur, bien que malheureusement trop peu nombreux, et pas Serveur de télécopie.
* <strong>Nano Server</strong> : la plus petite image Windows Server, avec la prise en charge des API .NET Core et de certains rôles de serveur.
* <strong>Windows 10 IoT Standard</strong> : une version de Windows utilisée par les fabricants de matériel pour les petits appareils Internet des objets qui exécutent des processeurs ARM ou x86/x64.

Comme cela a été mentionné précédemment, les images conteneur sont composées d’une série de couches. Chaque couche contient un ensemble de fichiers qui, lorsqu’ils sont superposés, représentent votre image conteneur. Du fait de la nature en couches des conteneurs, vous n’êtes pas obligé de toujours cibler une image de base pour créer un conteneur Windows. Vous pouvez également cibler une autre image contenant déjà l’infrastructure de votre choix. Par exemple, l’équipe .NET publie une [image .NET Core](https://hub.docker.com/_/microsoft-dotnet-core) qui comporte le runtime .NET Core. Elle évite aux utilisateurs de devoir dupliquer le processus d’installation de .NET Core : ils peuvent, à la place, réutiliser les couches de cette image conteneur. L’image .NET Core elle-même est basée sur Nano Server.

Pour plus d’informations, consultez [Images de base de conteneur](../manage-containers/container-base-images.md).

## <a name="container-users"></a>Utilisateurs de conteneur

### <a name="containers-for-developers"></a>Conteneurs pour les développeurs

Les conteneurs aident les développeurs à générer et à livrer des applications de meilleure qualité, plus rapidement. Avec les conteneurs, les développeurs peuvent créer une image conteneur qui se déploie en quelques secondes, de la même manière dans plusieurs environnements. Les conteneurs jouent le rôle de mécanisme simple permettant de partager du code entre des équipes, et de démarrer un environnement de développement sans impacter le système de fichiers de votre hôte.

Les conteneurs sont portables et polyvalents, ils peuvent exécuter des applications écrites dans n’importe quel langage et sont compatibles avec toute machine exécutant Windows 10, version 1607 ou ultérieure, et Windows Server 2016 ou ultérieur. Les développeurs peuvent créer et tester localement un conteneur sur leur ordinateur portable ou leur appareil de bureau, puis déployer cette même image conteneur sur le cloud privé de leur société, cloud public ou fournisseur de services. De nature agile, les conteneurs prennent en charge les modèles de développement d’applications modernes dans des environnements cloud, virtualisés et à grande échelle.

### <a name="containers-for-it-professionals"></a>Conteneurs pour les professionnels de l’informatique

Les conteneurs permettent aux administrateurs de créer une infrastructure plus facile à mettre à jour et à gérer, et qui tire pleinement parti des ressources matérielles. Les professionnels de l’informatique peuvent utiliser des conteneurs pour fournir des environnements standardisés à leurs équipes de développement, d’assurance qualité et de production. En utilisant des conteneurs, les administrateurs système font abstraction des différences dans les installations des systèmes d’exploitation et l’infrastructure sous-jacente.

## <a name="container-orchestration"></a>Orchestration de conteneurs

Les orchestrateurs constituent un élément essentiel de l’infrastructure lors de la configuration d’un environnement basé sur conteneur. Même si vous pouvez gérer quelques conteneurs manuellement par l’intermédiaire de Docker et de Windows, les applications utilisent souvent cinq, dix, voire des centaines de conteneurs, et c’est là que les orchestrateurs entrent en jeu.

Les orchestrateurs de conteneurs ont été créés pour faciliter la gestion des conteneurs, à grande échelle et en production. Les orchestrateurs offrent les fonctionnalités suivantes :

- Déploiement à grande échelle
- Planification de la charge de travail
- Analyse de l'intégrité
- Basculement en cas de défaillance d’un nœud
- Possibilité d’effectuer des scale-up ou scale-down
- Mise en réseau
- Détection du service
- Coordination des mises à niveau d’applications
- Affinité de nœud de cluster

Les différents orchestrateurs que vous pouvez utiliser avec les conteneurs Windows sont nombreux. Voici les options que Microsoft fournit :
- [Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) : utilisation d’un service Azure Kubernetes managé
- [Azure Service Fabric](/azure/service-fabric/) : utilisation d’un service managé
- [Azure Stack avec le moteur AKS](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) : utilisation d’Azure Kubernetes Service en local
- [Kubernetes sur Windows](../kubernetes/getting-started-kubernetes-windows.md) : configuration par vous-même de Kubernetes sur Windows

## <a name="try-containers-on-windows"></a>Essayer les conteneurs sur Windows

Pour vous familiariser avec les conteneurs sur Windows Server ou Windows 10, consultez :
> [!div class="nextstepaction"]
> [Prise en main : Configurer votre environnement pour les conteneurs](../quick-start/set-up-environment.md)

Pour vous aider à choisir les services Azure les plus appropriés pour votre scénario, consultez [Azure Container Services](https://azure.microsoft.com/product-categories/containers/) et [Choisir les services Azure à utiliser pour héberger votre application](/azure/architecture/guide/technology-choices/compute-decision-tree).