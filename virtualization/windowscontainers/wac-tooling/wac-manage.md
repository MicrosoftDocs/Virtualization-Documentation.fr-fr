---
title: Gérer les images de conteneur sur le centre d’administration Windows
description: Gérer les images de conteneur sur WAC
keywords: ancrage, conteneurs, Centre d’administration Windows
author: vrapolinario
ms.author: viniap
ms.date: 12/23/2020
ms.topic: quickstart
ms.assetid: bb9bfbe0-5bdc-4984-912f-9c93ea67105f
ms.openlocfilehash: 7256298007f3af7252cc5d9e2c09a93ab43acf1c
ms.sourcegitcommit: 0fed672793b8b0c07253c498ac6f7c98ca5fe2b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98241650"
---
# <a name="manage-container-images-on-windows-admin-center"></a>Gérer les images de conteneur sur le centre d’administration Windows

Cette rubrique explique comment gérer des images de conteneur dans le centre d’administration Windows. Les images de conteneur sont utilisées pour créer des conteneurs sur des ordinateurs Windows ou d’autres services Cloud, tels que le service Azure Kubernetes. Pour plus d’informations sur les images Windows, consultez [vue d’ensemble des images conteneur](https://docs.microsoft.com/virtualization/windowscontainers/about/#container-images).

## <a name="pull-container-images"></a>Extraire les images de conteneur

Après le déploiement d’un hôte de conteneur, l’étape suivante consiste à extraire (ou télécharger) des images de conteneur afin que de nouveaux conteneurs puissent être créés à partir des images. Vous pouvez utiliser le centre d’administration Windows pour extraire de nouvelles images de conteneur en sélectionnant l’extension conteneurs sur votre hôte de conteneur ciblé. Ensuite, sélectionnez l’onglet **images** dans l’extension de **conteneur** sous **hôte du conteneur** et sélectionnez l’option d' **extraction** .

![Extraire les images de conteneur](./media/WAC-Pull.png)

Dans les paramètres d' **image de conteneur pull** , indiquez l’URL de l’image et la balise de l’image que vous souhaitez extraire. Vous pouvez également sélectionner l’option permettant d’extraire toutes les images avec balises sur ce dépôt.

Si l’image que vous souhaitez extraire se trouve sur un dépôt privé, indiquez le nom d’utilisateur et le mot de passe pour vous authentifier auprès de ce référentiel. Si votre référentiel est hébergé sur Azure Container Registry, utilisez l’authentification Azure native sur le centre d’administration Windows pour accéder à l’image. Pour cela, l’instance du centre d’administration Windows doit être connectée à Azure et s’authentifier avec votre compte Azure. Pour plus d’informations sur la connexion d’une instance du centre d’administration Windows à Azure, consultez Configuration de l' [intégration d’Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).

Si vous n’êtes pas certain de l’image à extraire, le centre d’administration Windows fournit une liste d’images courantes de Microsoft. Sélectionnez la liste déroulante **images Windows communes** pour afficher la liste des images de base qui sont souvent extraites. Sélectionnez l’image que vous souhaitez extraire, et le centre d’administration Windows remplira les champs de dépôt et de balise.

## <a name="push-container-images"></a>Envoyer des images de conteneur

Une fois que vous avez créé votre image conteneur, il est recommandé d’envoyer cette image vers un référentiel centralisé pour permettre à d’autres hôtes de conteneur ou services Cloud d’extraire l’image.

Sous l’onglet **images** de l’extension **conteneurs** du centre d’administration Windows, sélectionnez l’image que vous souhaitez envoyer sur un réseau étendu, puis cliquez sur **pousser**.

![Envoyer des images de conteneur](./media/WAC-Push.png)

Dans les paramètres d' **image de conteneur Push** , vous pouvez modifier le nom et la balise de l’image avant de la pousser (charger). Vous pouvez également choisir de l’envoyer vers un référentiel générique ou un référentiel sur Azure Container Registry. Pour un référentiel générique, vous devez fournir un nom d’utilisateur et un mot de passe. Pour Azure Container Registry, vous pouvez utiliser l’authentification intégrée sur le centre d’administration Windows. Pour Azure, vous pouvez également sélectionner l’abonnement et le registre vers lesquels vous souhaitez envoyer l’image.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des conteneurs dans le centre d’administration Windows](./wac-images.md) 