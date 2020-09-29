---
title: Créer et exécuter une application avec ou sans .NET Core 2.0 ou PowerShell Core 6
description: Créer et exécuter une application avec ou sans .NET Core 2.0 ou PowerShell Core 6.
keywords: docker, conteneurs
ms.topic: quickstart
author: cwilhit
ms.author: jgerend
ms.openlocfilehash: a438666c75671e935ce8e8999e92b9c828043d94
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990632"
---
# <a name="build-and-run-an-application-with-or-without-net-core-20-or-powershell-core-6"></a>Créer et exécuter une application avec ou sans .NET Core 2.0 ou PowerShell Core 6

Dans cette version, l’image de base du système d’exploitation du conteneur Nano Server a supprimé .NET Core et PowerShell, bien que .NET Core et PowerShell soient pris en charge en tant que conteneur en couche additionnel sur le conteneur Nano Server de base.

Si votre conteneur doit exécuter du code natif ou des infrastructures ouvertes telles que Node.js, Python, Ruby, etc., le conteneur Nano Server de base est suffisant.  Il convient toutefois de noter que certains codes natifs peuvent ne pas fonctionner en raison des [économies d’encombrement](/windows-server/get-started/nano-in-semi-annual-channel) introduites dans cette version par rapport à Windows Server 2016. Si vous remarquez des problèmes de régression, faites-le nous savoir dans les [forums](https://social.msdn.microsoft.com/Forums/home?forum=windowscontainers).

Pour créer votre conteneur à partir d’un fichier Dockerfile, utilisez la commande docker build, puis pour l’exécuter, utilisez la commande docker run.  La commande suivante télécharge l’image de base du système d’exploitation du conteneur Nano Server, ce qui peut prendre quelques minutes, et imprime un message « Hello World ! » sur la console de l’hôte.

```
docker run microsoft/nanoserver-insider cmd /c echo Hello World!
```

Vous pouvez générer des applications plus complexes à l’aide de [fichiers Dockerfile sur Windows](../manage-docker/manage-windows-dockerfile.md), avec la syntaxe de fichier Dockerfile, par exemple, FROM, RUN, COPY, ADD, CMD, etc.  S'il ne vous est pas possible d'exécuter certaines commandes à partir de cette image de base, vous pouvez cependant créer des images de conteneur comprenant uniquement les éléments requis pour le bon fonctionnement de votre application.

Étant donné que .NET Core et PowerShell ne sont pas disponibles dans l’image de base du système d’exploitation du conteneur Nano Server, le défi consiste à créer un conteneur dont le contenu utilise un format zip compressé. Grâce à la fonctionnalité de [création échelonnée](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) disponible dans Docker 17.05, vous pouvez exploiter PowerShell dans un autre conteneur pour décompresser le contenu et le copier dans le conteneur Nano. Vous pouvez utiliser cette approche pour créer un conteneur .NET Core et un conteneur PowerShell.

Vous pouvez extraire l’image du conteneur PowerShell à l’aide de cette commande :

```
docker pull microsoft/nanoserver-insider-powershell
```

Vous pouvez extraire l’image du conteneur .NET Core à l’aide de cette commande :

```
docker pull microsoft/nanoserver-insider-dotnet
```

Voici quelques exemples d’utilisation de créations échelonnées pour la création de ces images de conteneur.

## <a name="deploy-apps-based-on-net-core-20"></a>Déployer des applications basées sur .NET Core 2.0
Vous pouvez exploiter l’image de conteneur .NET Core 2.0 de la version Insider pour exécuter vos applications .NET Core si votre application .NET Core est créée à un autre emplacement et que vous souhaitez l’exécuter dans le conteneur.  Des informations supplémentaires sur l’exécution d’une application .NET Core avec les images de conteneur .NET Core sont disponibles à partir de [.NET Core GitHub](https://github.com/dotnet/dotnet-docker-nightly).  Si vous développez une application dans le conteneur, utilisez le Kit de développement logiciel (SDK) .NET Core à la place.  Les utilisateurs avancés peuvent créer leur propre conteneur .NET Core 2.0 avec la version .NET Core 2.0, un fichier Dockerfile et l’URL spécifiée dans [dotnet-docker-nightly](https://github.com/dotnet/dotnet-docker-nightly/tree/master/2.0). Pour ce faire, ils peuvent utiliser un conteneur Windows Server Core pour télécharger et décompresser les fichiers.  L’exemple de fichier Dockerfile est identique au [fichier Dockerfile .NET Core Runtime](https://github.com/dotnet/dotnet-docker-nightly/blob/master/2.0/runtime/nanoserver-insider/amd64/Dockerfile).


Avec ce fichier Dockerfile, un conteneur .NET Core 2.0 peut être créé à l’aide de la commande suivante.

```
docker build -t nanoserverdnc -f Dockerfile-dotnetRuntime .
```

## <a name="run-powershell-core-6-in-a-container"></a>Exécuter PowerShell Core 6 dans un conteneur
À l’aide de la même méthode de [création échelonnée](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), vous pouvez créer un conteneur PowerShell Core 6 avec [ce fichier Dockerfile PowerShell](https://github.com/PowerShell/PowerShell-Docker/blob/master/release/stable/nanoserver/docker/Dockerfile).


Ensuite, exécutez la commande docker build pour créer l’image de conteneur PowerShell.

```
docker build -t nanoserverPowerShell6 -f Dockerfile-PowerShell6 .
```

Pour plus d’informations, voir [PowerShell GitHub](https://github.com/PowerShell/PowerShell-Docker/tree/master/release).  Il est important de mentionner que le fichier zip PowerShell contient un sous-ensemble de .NET Core 2.0 qui est nécessaire pour créer PowerShell Core 6.  Si vos modules PowerShell dépendent de .NET Core 2.0, il est possible de créer le conteneur PowerShell sur le conteneur Nano .NET Core, au lieu du conteneur Nano de base, autrement dit à l’aide de FROM microsoft/nanoserver-insider-dotnet dans le fichier Dockerfile.

## <a name="next-steps"></a>Étapes suivantes
- Utilisez une des nouvelles images de conteneur basées sur Nano Server, disponibles dans le hub Docker, c’est-à-dire une image Nano Server de base, Nano avec .NET Core 2.0 et Nano avec PowerShell Core 6
- Créez votre propre image de conteneur basée sur la nouvelle image de base du système d’exploitation du conteneur Nano Server en utilisant l’exemple de contenu de fichier Dockerfile fourni dans ce guide