---
title: Utiliser des conteneurs avec le programme Windows Insider
description: Découvrez comment prendre en main l’utilisation de conteneurs Windows avec le programme Windows Insider.
keywords: docker, conteneurs, insider, windows
author: v-susbo
ms.author: v-susbo
ms.date: 12/29/2020
ms.topic: how-to
ms.openlocfilehash: f2eb6bce8ab482f8c1d34bdc9b7c5916ba0e708b
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98181986"
---
# <a name="use-containers-with-the-windows-insider-program"></a>Utiliser des conteneurs avec le programme Windows Insider

Cet exercice va vous guider tout au long du déploiement et de l’utilisation de la fonctionnalité de conteneur Windows de la dernière build Insider de Windows Server du programme Windows Insider Preview. Au cours de cet exercice, vous allez installer le rôle de conteneur et déployer une version d’évaluation des images de système d’exploitation de base. Si vous voulez vous familiariser avec les conteneurs, vous trouverez des informations dans la rubrique [À propos des conteneurs](../about/index.md).

## <a name="join-the-windows-insider-program"></a>Rejoignez le Programme Windows Insider

Pour exécuter la version Insider des conteneurs Windows, vous devez disposer d’un hôte exécutant la dernière build de Windows Server et/ou de Windows 10 disponible dans le cadre du programme Windows Insider. Rejoigne le [Programme Windows Insider](https://insider.windows.com/GettingStarted) et consultez les conditions d’utilisation.

> [!IMPORTANT]
> Pour pouvoir utiliser l’image de base décrite ci-dessous, vous devez utiliser une build de Windows Server ou de Windows 10 disponible dans le cadre du Programme Windows Server Insider en préversion. Si vous n’utilisez pas l’une de ces builds, la création d’un conteneur échouera lors de l’utilisation de ces images de base.

## <a name="install-docker"></a>Installer Docker

Si vous n’avez pas encore installé Docker, suivez le Guide [Prise en main](../quick-start/set-up-environment.md) pour l’installer.

## <a name="pull-an-insider-container-image"></a>Extraire une image de conteneur Insider

En tant que participant au Programme Windows Insider, vous pouvez également utiliser les images de base de nos builds les plus récentes.

Pour extraire l’image de base Insider Nano Server, exécutez la commande suivante :

```console
docker pull mcr.microsoft.com/nanoserver/insider
```

Exécutez la commande suivante pour extraire l’image de base Insider Windows Server Core :

```console
docker pull mcr.microsoft.com/windows/servercore/insider
```

Les images de base « Windows » et « IoTCore » disposent également d’une version Insider disponible pour extraction. Pour en savoir plus sur les images de base Insider disponibles, lisez la documentation [Images de base de conteneur](../manage-containers/container-base-images.md).

> [!IMPORTANT]
> Veuillez lire le [CLUF](../images-eula.md ) applicable à l’image de système d’exploitation de conteneurs Windows et les [Conditions d’utilisation](https://www.microsoft.com/software-download/windowsinsiderpreviewserver) du programme Windows Insider.
