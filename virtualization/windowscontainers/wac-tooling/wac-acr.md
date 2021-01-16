---
title: Gérer les Azure Container Registry sur le centre d’administration Windows
description: Azure Container Registry sur le centre d’administration Windows
keywords: ancrage, conteneurs, Centre d’administration Windows
author: vrapolinario
ms.author: viniap
ms.date: 12/24/2020
ms.topic: tutorial
ms.assetid: bb9bfbe0-5bdc-4984-912f-9c93ea67105f
ms.openlocfilehash: cba3af82164c157b416b8ed8c63998f90308187d
ms.sourcegitcommit: 0fed672793b8b0c07253c498ac6f7c98ca5fe2b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98241660"
---
# <a name="manage-azure-container-registry-using-windows-admin-center"></a>Gérer les Azure Container Registry à l’aide du centre d’administration Windows

Cette rubrique explique comment gérer les Azure Container Registry (ACR) à l’aide du centre d’administration Windows. Azure Container Registry vous permet de créer, de stocker et de gérer des images conteneur et des artefacts dans un conteneur privé pour tous les types de déploiement de conteneur. 

>[!Note]
>Un abonnement Azure est requis pour exécuter les étapes de ce didacticiel. Pour plus d’informations sur la connexion de votre instance du centre d’administration Windows à Azure, consultez Configuration de l' [intégration d’Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).

Le centre d’administration Windows vous permet d’effectuer une gestion de base des Azure Container Registry, par exemple :
  
- Répertorier les registres et les images 
- Créer des registres 
- Supprimer des images 
- Extraire les images vers votre hôte de conteneur
- Démarrer de nouveaux conteneurs sur Azure Container Instances à partir d’images stockées dans Azure Container Registry

À l’aide du centre d’administration Windows pour gérer ACR, vous pouvez préparer l’emplacement Azure vers lequel vous pouvez envoyer des images directement à partir de l’onglet **images** sous **hôte de conteneur**. Pour commencer, suivez les étapes ci-dessous :  

1. Ouvrez votre instance du centre d’administration Windows et sélectionnez l’hôte de conteneur. 
2. Sous **Outils** dans le volet gauche, faites défiler la liste et sélectionnez l’extension **conteneurs** .
3. Ouvrez l’onglet **Azure Container Registry** sous **Azure**.

    ![Onglet Azure Container Registry](./media/WAC-ACR.png)

## <a name="manage-registries-and-images"></a>Gérer les registres et les images

Pour créer un nouveau registre, sous **Azure Container Registry**, sélectionnez **créer un nouveau registre**.

![Créer un registre](./media/WAC-ACRNew.png)

Sous **créer un registre**, sélectionnez l’abonnement que vous souhaitez utiliser pour créer un nouveau registre et le groupe de ressources pour lequel vous souhaitez allouer le registre. Indiquez ensuite un nom de Registre, un emplacement et une référence (SKU). Pour plus d’informations sur la tarification et les fonctionnalités de chaque référence (SKU), consultez [la documentation ACR](https://docs.microsoft.com/azure/container-registry/). Cliquez sur **créer** pour créer le nouveau registre. Une fois l’opération terminée, le centre d’administration Windows vous avertit si l’opération s’est terminée avec succès, et le nouveau registre s’affiche.

Une fois les registres et les images répertoriés, vous pouvez supprimer une image existante ou l’extraire vers l’hôte de conteneur pour une utilisation locale. Pour extraire une image, sélectionnez l’image que vous souhaitez extraire, puis cliquez sur **extraire l’image**:

![Extraire (pull) l’image](./media/WAC-ACRPull.png)

Une fois le processus d’extraction terminé, le centre d’administration Windows vous en informe, et l’image est disponible pour utilisation sous l’onglet **images** sous **hôte de conteneur**.

Enfin, vous pouvez exécuter un nouveau conteneur basé sur une image hébergée sur ACR. Pour commencer, sélectionnez l’image que vous souhaitez exécuter, puis cliquez sur **exécuter l’instance**:

![Exécuter l’instance](./media/WAC-ACRRun.png)

Sous **exécuter l’instance**, vous devez fournir un nom de conteneur, l’abonnement à utiliser, ainsi que le groupe de ressources et l’emplacement où vous souhaitez exécuter cette instance.

Ensuite, fournissez l’allocation de mémoire et d’UC pour cette instance, ainsi que le port que vous souhaitez ouvrir, si nécessaire. Cliquez sur **créer** et le centre d’administration Windows envoie la commande à Azure pour exécuter l’instance. Vous pouvez vérifier l’état de l’instance de conteneur sous l’onglet **Azure Container instance** .

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer Azure Container instance sur le centre d’administration Windows](./wac-aci.md)