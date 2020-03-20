---
title: Conteneurs Windows et Linux sur Windows 10
description: Démarrage rapide du déploiement de conteneurs
keywords: docker, conteneurs, LCOW
author: cwilhit
ms.author: crwilhit
ms.date: 11/12/2019
ms.topic: article
ms.prod: windows-containers
ms.service: windows-containers
ms.assetid: bb9bfbe0-5bdc-4984-912f-9c93ea67105f
ms.openlocfilehash: 551d405d836cfb16b587ef78bc2d5f5abbd8648f
ms.sourcegitcommit: ac923217ee2f74f08df2b71c2a4c57b694f0d7c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78853813"
---
# <a name="get-started-run-your-first-windows-container"></a>Bien démarrer : Exécuter votre premier conteneur Windows

Cette rubrique explique comment exécuter votre premier conteneur Windows, après avoir configuré votre environnement, comme décrit dans [Prise en main : Préparer Windows pour les conteneurs](./set-up-environment.md). Pour exécuter un conteneur, vous devez d’abord installer une image de base, qui fournit à votre conteneur une couche fondamentale de services de système d’exploitation. Ensuite, vous créez et exécutez une image de conteneur, qui repose sur l’image de base. Pour plus d’informations, consultez les références.

## <a name="install-a-container-base-image"></a>Installer une image de base de conteneur

Tous les conteneurs sont créés à partir d’images conteneur. Microsoft propose plusieurs images de démarrage, appelées images de base. Pour plus d’informations, consultez [Images de base de conteneur](../manage-containers/container-base-images.md). Cette procédure extrait (télécharge et installe) l’image de base Nano Server.

1. Ouvrez une fenêtre d’invite de commandes (telle que l’invite de commandes intégrée, PowerShell ou [Terminal Windows](https://www.microsoft.com/p/windows-terminal-preview/9n0dx20hk701?activetab=pivot:overviewtab)), puis exécutez la commande suivante pour télécharger et installer l’image de base :

   ```console
   docker pull mcr.microsoft.com/windows/nanoserver:1903
   ```

   > [!TIP]
   > Si un message d'erreur indiquant `no matching manifest for unknown in the manifest list entries` s'affiche, vérifiez que Docker n’est pas configuré pour exécuter des conteneurs Linux.

2. Une fois le téléchargement de l’image terminé, consultez le [CLUF](../images-eula.md). Vérifiez sa présence sur votre système en interrogeant votre référentiel d’images Docker local. L’exécution de la commande `docker images` retourne une liste d’images installées.

   Voici un exemple de sortie illustrant l’image Nano Server.

   ```console
   REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
   microsoft/nanoserver   latest              105d76d0f40e        4 days ago          652 MB
   ```

## <a name="run-a-windows-container"></a>Créer un conteneur Windows

Pour ce simple exemple, une image de conteneur « Hello World » est créée et déployée. Pour une expérience optimale, exécutez ces commandes dans une fenêtre d’invite de commandes avec élévation de privilèges (mais n’utilisez pas l'environnement d'écriture de scripts intégré de Windows PowerShell, celui-ci ne fonctionnant pas pour les sessions interactives avec des conteneurs, car les conteneurs semblent se bloquer).

1. Démarrez un conteneur avec une session interactive à partir de l’image `nanoserver` en entrant la commande suivante dans la fenêtre d’invite de commandes :

   ```console
   docker run -it mcr.microsoft.com/windows/nanoserver:1903 cmd.exe
   ```
2. Une fois le conteneur démarré, la fenêtre d’invite de commandes change de contexte dans le conteneur. À l’intérieur du conteneur, nous allons créer un fichier texte « Hello World » simple, puis quitter le conteneur en entrant les commandes suivantes :

   ```cmd
   echo "Hello World!" > Hello.txt
   exit
   ```   

3. Récupérez l’ID correspondant au conteneur que vous venez de quitter en exécutant la commande [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) :

   ```console
   docker ps -a
   ```

4. Créez une nouvelle image « HelloWorld » incluant les modifications apportées au premier conteneur que vous avez exécuté. Pour ce faire, exécutez la commande [docker commit](https://docs.docker.com/engine/reference/commandline/commit/), en remplaçant `<containerid>` par l’ID de votre conteneur :

   ```console
   docker commit <containerid> helloworld
   ```

   Quand vous avez terminé, vous disposez d’une image personnalisée qui contient le script « Hello World ». Pour l’afficher, utilisez la commande [docker images](https://docs.docker.com/engine/reference/commandline/images/).

   ```console
   docker images
   ```

   Voici un exemple de sortie :

   ```console
   REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
   helloworld                             latest              a1064f2ec798        10 seconds ago      258MB
   mcr.microsoft.com/windows/nanoserver   1903                2b9c381d0911        3 weeks ago         256MB
   ```

5. Enfin, exécutez le nouveau conteneur à l’aide de la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) avec le paramètre `--rm` qui supprime automatiquement le conteneur une fois la ligne de commande (cmd. exe) arrêtée.

   ```console
   docker run --rm helloworld cmd.exe /s /c type Hello.txt
   ```

   Résultat : un conteneur a été créé à partir de l’image « HelloWorld », une instance de cmd. exe a été démarrée dans le conteneur qui lit le fichier et génère son contenu dans le shell, puis le conteneur s’est arrêté et a été supprimé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Apprendre à conteneuriser un exemple d’application](./building-sample-app.md)
