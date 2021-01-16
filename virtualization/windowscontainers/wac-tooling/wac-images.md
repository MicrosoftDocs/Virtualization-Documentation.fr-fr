---
title: Créer des images de conteneur sur le centre d’administration Windows
description: Images de conteneur sur le centre d’administration Windows
keywords: ancrage, conteneurs, Centre d’administration Windows
author: vrapolinario
ms.author: viniap
ms.date: 12/23/2020
ms.topic: quickstart
ms.assetid: bb9bfbe0-5bdc-4984-912f-9c93ea67105f
ms.openlocfilehash: f11e73f1865b8f41ec16ec8d9e4880b2d5d669b5
ms.sourcegitcommit: 0fed672793b8b0c07253c498ac6f7c98ca5fe2b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98241640"
---
# <a name="create-new-container-images-on-windows-admin-center"></a>Créer des images de conteneur dans le centre d’administration Windows

Cette rubrique explique comment créer des images de conteneur à l’aide du centre d’administration Windows. Les images de conteneur sont utilisées pour créer des conteneurs sur des ordinateurs Windows ou d’autres services Cloud, tels que le service Azure Kubernetes. Pour plus d’informations sur les images Windows, consultez [vue d’ensemble des images conteneur](https://docs.microsoft.com/virtualization/windowscontainers/about/#container-images).

## <a name="create-new-container-images"></a>Créer des images conteneur

Lorsque vous travaillez avec des conteneurs, vous écrivez des instructions pour l’ancrage sur le fonctionnement de votre image conteneur, puis l’Ancreur crée une image de conteneur en fonction de ces instructions. Ces instructions sont enregistrées dans un fichier appelé « fichier dockerfile » qui est enregistré dans le même dossier que celui dans lequel votre application réside. 

Le centre d’administration Windows peut réduire considérablement la surcharge liée à l’écriture de fichiers dockerfile, voire supprimer la nécessité d’écrire ces fichiers entièrement. Pour commencer, dans l’extension **conteneurs** , sélectionnez l’option **créer** sous l’onglet **images** .

![Créer un conteneur](./media/WAC-CreateNew.png)

Lorsque vous créez une image de conteneur, vous avez le choix entre différentes options :

- **Utiliser un fichier dockerfile existant**: cette option vous permet de reconstruire une nouvelle image de conteneur basée sur un fichier dockerfile existant. Cela est utile lorsque vous devez apporter des modifications mineures à un fichier dockerfile existant ou lorsque vous devez recréer le conteneur pour intercepter une mise à jour d’application.
- **Application Web IIS/dossier d’application Web statique**: utilisez cette option pour créer une image de conteneur à l’aide de l’image de base IIS. Le contenu du dossier est copié dans l’image conteneur pour l’ajouter en tant que site Web. Aucune infrastructure n’est ajoutée avec cette option.
- **Application Web IIS/solution Visual Studio (ASP.net)**: utilisez cette option pour créer une image de conteneur basée sur une solution Visual Studio existante. Cette option utilise une approche de la phase multi-image pour mettre en place l’application, compiler les binaires nécessaires et stocker uniquement les ressources nécessaires sur l’image finale. L’image de conteneur ASP.NET est utilisée comme image de base. Cette option demande également le dossier dans lequel Visual Studio réside. Cela vous permet de voir une liste des projets existants, et vous pouvez sélectionner celui que vous souhaitez déconteneurr.
- **Application Web IIS/Web Deploy (fichier zip exporté)**: utilisez cette option pour créer une image conteneur à partir des artefacts exportés à partir d’un serveur en cours d’exécution. Vous pouvez utiliser Web Deploy pour exporter l’application dans un fichier zip, puis utiliser le centre d’administration Windows pour créer une image de conteneur basée sur le fichier zip exporté. L’image de conteneur ASP.NET est utilisée comme image de base.

Une fois que vous avez sélectionné le type d’application que vous souhaitez placer dans une conteneur, vous pouvez sélectionner des options communes pour finaliser la création de votre image :

- **Version du Framework**: les options solution et Web Deploy de Visual Studio utilisent l’image ASP.net comme base pour votre image conteneur. Toutefois, vous pouvez sélectionner la version du .NET Framework que vous souhaitez utiliser pour prendre en charge votre application.
- **Scripts supplémentaires à exécuter**: cette option vous permet de sélectionner un script PowerShell à utiliser au moment de la génération. Le centre d’administration Windows ajoute une instruction au fichier dockerfile pour copier le. Fichier PS1 à l’image de conteneur, puis exécutez ce script lors de la création de l’image de conteneur. Cela peut être utile si votre application vous demande d’exécuter des étapes supplémentaires qui ne sont pas terminées dans l’application elle-même.
- **Nom** de l’image : nom de l’image finale à utiliser. Vous pouvez modifier le nom ultérieurement lorsque vous envoyez l’image vers un registre de conteneurs.
- **Balise d’image**: la balise est utilisée pour différencier plusieurs versions de la même image. Fournissez un identificateur, afin que votre image soit correctement marquée.

Une fois que vous avez sélectionné toutes les options pour votre image de conteneur, vous pouvez passer en revue le fichier dockerfile. Si nécessaire, vous pouvez également modifier manuellement le fichier dockerfile. Ce fichier dockerfile est enregistré à l’emplacement de l’application que vous avez spécifié à l’étape précédente. 

>[!Note]
>Si un fichier dockerfile existe déjà à l’emplacement de l’application que vous essayez de délimiter, le centre d’administration Windows remplace ce fichier par le nouveau qui vient d’être créé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Exécuter des conteneurs sur le centre d’administration Windows](./wac-containers.md)