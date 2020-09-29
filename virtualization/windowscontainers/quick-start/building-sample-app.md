---
title: Conteneuriser une application .NET Core
description: Apprendre à créer un exemple d’application .NET Core avec des conteneurs
keywords: docker, conteneurs
author: cwilhit
ms.author: crwilhit
ms.date: 11/12/2019
ms.topic: quickstart
ms.openlocfilehash: e04e4ee4c38409dfd20e4426d586839e1741651e
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990652"
---
# <a name="containerize-a-net-core-app"></a>Conteneuriser une application .NET Core

Cette rubrique explique comment empaqueter un exemple d’application .NET à des fins de déploiement en tant que conteneur Windows, après avoir configuré votre environnement, comme décrit dans [Prise en main : Préparer Windows pour les conteneurs](set-up-environment.md), et exécuté votre premier conteneur, comme décrit dans [Exécuter votre premier conteneur Windows](run-your-first-container.md).

Le système de contrôle de code source Git doit également être installé sur votre ordinateur. Pour ce faire, rendez-vous sur [Git](https://git-scm.com/download).

## <a name="clone-the-sample-code-from-github"></a>Cloner l’exemple de code à partir de GitHub

Tout le code source de l’exemple de conteneur est conservé sous le référentiel Git [Virtualization-Documentation](https://github.com/MicrosoftDocs/Virtualization-Documentation) dans un dossier nommé `windows-container-samples`.

1. Ouvrez une session PowerShell et modifiez les répertoires en fonction du dossier dans lequel vous souhaitez stocker ce référentiel. (D'autres types de fenêtre d’invite de commandes sont également possibles, mais nos exemples de commandes utilisent PowerShell.)
2. Clonez le référentiel vers votre répertoire de travail actuel :

   ```PowerShell
   git clone https://github.com/MicrosoftDocs/Virtualization-Documentation.git
   ```

3. Accédez à l’exemple de répertoire situé sous `Virtualization-Documentation\windows-container-samples\asp-net-getting-started` et créez un fichier Dockerfile à l’aide des commandes suivantes.

   Un fichier [Dockerfile](https://docs.docker.com/engine/reference/builder/) s'apparente à un makefile ; il répertorie les instructions indiquant au moteur de conteneur comment créer l’image de conteneur.

   ```Powershell
   # Navigate into the sample directory
   Set-Location -Path Virtualization-Documentation\windows-container-samples\asp-net-getting-started

   # Create the Dockerfile for our project
   New-Item -Name Dockerfile -ItemType file
   ```

## <a name="write-the-dockerfile"></a>Écrire le fichier Docker

Ouvrez le fichier Dockerfile que vous venez de créer avec l’éditeur de texte de votre choix, puis ajoutez le contenu suivant :

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.1 AS build-env
WORKDIR /app

COPY *.csproj ./
RUN dotnet restore

COPY . ./
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.1
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "asp-net-getting-started.dll"]
```

Examinons-le ligne par ligne et expliquons l'objectif de chaque instruction.

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.1 AS build-env
WORKDIR /app
```

Le premier groupe de lignes déclare à partir de quelle image de base nous allons créer le conteneur. Si cette image ne se trouve pas sur le système local, Docker essaie automatiquement de la récupérer. Le `mcr.microsoft.com/dotnet/core/sdk:2.1` est fourni avec le kit de développement logiciel (SDK) .NET Core 2.1 installé, et convient à la tâche de création de projets ASP .NET Core ciblant la version 2.1. L’instruction suivante modifie le répertoire de travail dans notre conteneur pour qu'il corresponde à `/app` et donc, toutes les commandes suivantes s’exécutent dans ce contexte.

```Dockerfile
COPY *.csproj ./
RUN dotnet restore
```

Ces instructions copient ensuite les fichiers. csproj dans le répertoire `/app` du conteneur `build-env`. Une fois ce fichier copié, .NET le lit, puis récupère toutes les dépendances et les outils requis par notre projet.

```Dockerfile
COPY . ./
RUN dotnet publish -c Release -o out
```

Une fois que .NET a extrait toutes les dépendances dans le conteneur `build-env`, l’instruction suivante copie tous les fichiers sources du projet dans le conteneur. Ensuite, nous demandons à .NET de publier notre application avec une version finale et nous spécifions le chemin de sortie.

La compilation doit aboutir. Nous devons à présent créer l’image finale.

> [!TIP]
> Ce guide de démarrage rapide crée un projet .NET Core à partir de la source. Lors de la génération d’images de conteneur, il est conseillé d’inclure _uniquement_ la charge utile de production et ses dépendances dans l’image de conteneur. Nous ne souhaitons pas inclure le kit de développement logiciel (SDK) .NET Core dans notre image finale, car nous avons uniquement besoin du runtime .NET Core. Dès lors, le fichier Dockerfile est écrit pour utiliser un conteneur temporaire empaqueté avec le kit de développement logiciel (SDK) appelé `build-env` pour générer l’application.

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.1
WORKDIR /app
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "asp-net-getting-started.dll"]
```

Dans la mesure où il s’agit d’une application ASP.NET, nous spécifions une image incluant ce runtime. Ensuite, nous copions tous les fichiers du répertoire de sortie de notre conteneur temporaire dans notre conteneur final. Nous configurons notre conteneur pour qu’il s’exécute avec notre nouvelle application en tant que point d’entrée au démarrage du conteneur.

Nous avons écrit le fichier Dockerfile pour permettre une _génération échelonnée_. Lorsque le fichier Dockerfile est exécuté, il utilise le conteneur temporaire, `build-env`, avec le kit de développement logiciel (SDK) .NET Core 2.1 pour générer l’exemple d’application, puis copie les fichiers binaires générés dans un autre conteneur comprenant uniquement le runtime .NET Core 2.1 et ce, afin de réduire la taille du conteneur final.

## <a name="build-and-run-the-app"></a>Générer et exécuter l’application

Une fois le fichier Dockerfile écrit, nous pouvons pointer Docker sur notre fichier Dockerfile pour lui demander de générer et d’exécuter l’image :

1. Dans une fenêtre d’invite de commandes, accédez au répertoire où se trouve le fichier Dockerfile, puis exécutez la commande [docker build](https://docs.docker.com/engine/reference/commandline/build/) pour générer le conteneur à partir du fichier Dockerfile.

   ```Powershell
   docker build -t my-asp-app .
   ```

2. Pour exécuter le nouveau conteneur généré, exécutez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/).

   ```Powershell
   docker run -d -p 5000:80 --name myapp my-asp-app
   ```

   Examinons cette commande :

   * `-d` indique à Docker d'exécuter le conteneur « detached », ce qui signifie qu’aucune console n’est reliée à la console à l’intérieur du conteneur. Le conteneur s’exécute en arrière-plan.
   * `-p 5000:80` indique à Docker de mapper le port 5000 de l’hôte au port 80 du conteneur. Chaque conteneur obtient sa propre adresse IP. Par défaut, ASP .NET écoute sur le port 80. Le mappage de ports nous permet d’accéder à l’adresse IP de l’hôte au niveau du port mappé et Docker transmet tout le trafic vers le port de destination à l’intérieur du conteneur.
   * `--name myapp` indique à Docker d’attribuer à ce conteneur un nom convivial à interroger (plutôt que de devoir rechercher l’ID de conteneur attribué au moment de l’exécution par Docker).
   * `my-asp-app` correspond à l’image que nous souhaitons que Docker exécute. Il s’agit de l’image de conteneur générée au terme du processus `docker build`.

3. Ouvrez un navigateur web et accédez à `http://localhost:5000` pour voir votre application en conteneur, comme illustré dans cette capture d’écran :

   >![Page web ASP.NET Core s'exécutant à partir de localhost dans un conteneur](media/SampleAppScreenshot.png)

## <a name="next-steps"></a>Étapes suivantes

1. L’étape suivante consiste à publier votre application web ASP.NET en conteneur dans un registre privé à l’aide d'Azure Container Registry. Vous pouvez ainsi la déployer au sein de votre organisation.

   > [!div class="nextstepaction"]
   > [Créer un Registre de conteneurs privé](/azure/container-registry/container-registry-get-started-powershell)

   Lorsque vous accédez à la section où vous [transmettez (push) votre image de conteneur au registre](/azure/container-registry/container-registry-get-started-powershell#push-image-to-registry), spécifiez le nom de l’application ASP.NET que vous venez d'empaqueter (`my-asp-app`), ainsi que votre registre de conteneurs (par exemple : `contoso-container-registry`) :

   ```PowerShell
   docker tag my-asp-app contoso-container-registry.azurecr.io/my-asp-app:v1
   ```

   Pour consulter d’autres exemples d’applications et de fichiers Dockerfile connexes, consultez [Exemples de conteneurs supplémentaires](../samples.md).

2. Après avoir publié votre application dans le registre de conteneurs, l’étape suivante consiste à déployer l’application dans un cluster Kubernetes que vous créez avec Azure Kubernetes Service.

   > [!div class="nextstepaction"]
   > [Créer un cluster Kubernetes](/azure/aks/windows-container-cli)