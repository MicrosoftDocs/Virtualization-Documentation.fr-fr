---
title: Vue d’ensemble du stockage de conteneurs
description: Comment les conteneurs Windows Server peuvent-ils utiliser les hôtes et les autres types de stockage
keywords: conteneurs, volume, stockage, montage, montage lié
author: cwilhit
ms.author: jgerend
ms.topic: overview
ms.openlocfilehash: e5cc4770cfa014ca307db1bb7ec4a3bb81c2dd32
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990922"
---
# <a name="container-storage-overview"></a>Vue d’ensemble du stockage de conteneurs

<!-- Great diagram would be great! -->

Cette rubrique fournit une vue d’ensemble des différentes manières dont les conteneurs utilisent le stockage sur Windows. Quand il s’agit de stockage, les conteneurs se comportent différemment des machines virtuelles. Par nature, les conteneurs sont conçus pour empêcher une application s’exécutant dans ceux-ci d’écrire un état dans le système de fichiers de l’hôte. Les conteneurs utilisent un espace « de travail » par défaut, mais Windows fournit également un moyen de conserver le stockage.

## <a name="scratch-space"></a>Espace de travail

Les conteneurs Windows utilisent par défaut un stockage éphémère. Toutes les E/S de conteneur se produisent dans un « espace de travail » et chaque conteneur reçois son propre travail. La création de fichier et les écritures de fichier sont capturées dans l’espace de travail et n’échappent pas à l’hôte. Lors de l’arrêt d’une instance de conteneur, toutes les modifications apportées à l’espace de travail sont écartées. Lors du démarrage d’une nouvelle instance de conteneur, un nouvel espace de travail est fourni pour l’instance.

## <a name="layer-storage"></a>Stockage par niveaux

Comme décrit dans la [Vue d’ensemble des conteneurs](../about/index.md), les images de conteneur sont un ensemble de fichiers qui se présente sous la forme d’une série de couches. Le stockage en couches englobe tous les fichiers intégrés dans le conteneur. Chaque fois que vous effectuez un `docker pull`, puis un `docker run` de ce conteneur, vous obtenez un résultat identique.

### <a name="where-layers-are-stored-and-how-to-change-it"></a>Où sont stockées les niveaux et comment modifier cette configuration

Dans une installation par défaut, les niveaux sont stockés dans `C:\ProgramData\docker` et répartis dans les répertoires « image » et « windowsfilter ». Vous pouvez changer l’emplacement de stockage des niveaux à l’aide de la configuration `docker-root`, comme illustré dans la documentation [Moteur Docker sur Windows](../manage-docker/configure-docker-daemon.md).

> [!NOTE]
> Seul NTFS est pris en charge pour le stockage de niveaux. ReFS n’est pas pris en charge.

Vous ne devez pas modifier les fichiers contenus dans les répertoires des niveaux : ils sont gérés avec soin à l’aide de commandes telles que :

- [docker images](https://docs.docker.com/engine/reference/commandline/images/)
- [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/)
- [docker pull](https://docs.docker.com/engine/reference/commandline/pull/)
- [docker load](https://docs.docker.com/engine/reference/commandline/load/)
- [docker save](https://docs.docker.com/engine/reference/commandline/save/)

### <a name="supported-operations-in-layer-storage"></a>Opérations prises en charge dans le stockage de niveaux

Les conteneurs en cours d’exécution peuvent utiliser la plupart des opérations de NTFS à l’exception des transactions. Cela implique de paramétrer des ACL et toutes les listes ACL sont vérifiées à l’intérieur du conteneur. Si vous souhaitez exécuter des processus en tant qu’utilisateurs multiples au sein d’un conteneur, vous pouvez créer des utilisateurs dans votre `Dockerfile` avec `RUN net user /create ...`, définir des ACL de fichier, puis configurer les processus à exécuter associés à chaque utilisateur à l’aide de la [directive Dockerfile USER](https://docs.docker.com/engine/reference/builder/#user).

## <a name="persistent-storage"></a>Stockage persistant

Les conteneurs Windows prennent en charge des mécanismes permettant de fournir un stockage persistant via des montages et volumes liés. Pour plus d’informations, consultez [Stockage persistant dans des conteneurs](./persistent-storage.md).

## <a name="storage-limits"></a>Limites de stockage

Il est courant dans les applications Windows de lancer une requête sur la quantité d’espace disque disponible avant d’installer ou de créer de nouveaux fichiers, ou en tant que déclencheur pour le nettoyage des fichiers temporaires.  Afin d’optimiser la compatibilité des applications, le disque C: d’un conteneur Windows représente un volume virtuel libre de 20 Go.

Si des utilisateurs le souhaitent, ils peuvent remplacer ce paramétrage par défaut pour configurer l’espace libre sur une valeur inférieure ou supérieure. À cette fin, ils peuvent utiliser l’option « size » dans la configuration « storage-opt ».

### <a name="examples"></a>Exemples

Ligne de commande : `docker run --storage-opt "size=50GB" mcr.microsoft.com/windows/servercore:ltsc2019 cmd`

Vous pouvez également modifier directement le fichier de configuration de Docker :

```Docker Configuration File
"storage-opts": [
    "size=50GB"
  ]
```

> [!TIP]
> Cette méthode fonctionne également pour la build de Docker. Consultez le [document Configure docker](../manage-docker/configure-docker-daemon.md#configure-docker-with-a-configuration-file) pour plus d’informations sur la modification du fichier de configuration docker.