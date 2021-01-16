---
title: Exécuter des conteneurs sur le centre d’administration Windows
description: Exécuter un conteneur sur le centre d’administration Windows
keywords: ancrage, conteneurs, Centre d’administration Windows
author: vrapolinario
ms.author: viniap
ms.date: 12/23/2020
ms.topic: quickstart
ms.assetid: bb9bfbe0-5bdc-4984-912f-9c93ea67105f
ms.openlocfilehash: d7786caa70d51270e685bf4264e92a3b84df7f1f
ms.sourcegitcommit: 0fed672793b8b0c07253c498ac6f7c98ca5fe2b2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98241610"
---
# <a name="run-new-containers-using-windows-admin-center"></a>Exécuter de nouveaux conteneurs à l’aide du centre d’administration Windows

Le centre d’administration Windows vous permet d’exécuter des conteneurs localement sur votre hôte de conteneur. 

1. À l’aide du centre d’administration Windows avec l’extension containers installée, ouvrez l’hôte de conteneur que vous souhaitez gérer.
2. Sous **Outils** sur le côté gauche, sélectionnez l’extension **conteneurs** . 
3. Sélectionnez l’onglet **images** sous hôte du conteneur.

    ![OngletImages](./media/WAC-Images.png)

4. Sélectionnez l’image que vous souhaitez exécuter, puis cliquez sur **exécuter**.

    ![Exécuter l’image](./media/WAC-RunContainers.png)

5. Sous **image d’exécution**, vous pouvez spécifier la configuration liée au conteneur :

    - Nom du conteneur : il s’agit d’une entrée facultative. Vous pouvez fournir un nom de conteneur pour vous aider à suivre le conteneur que vous avez créé. dans le cas contraire, l’arrimeur lui fournira un nom générique.
    - Type d’isolation : vous pouvez choisir d’utiliser l’hyperviseur au lieu de l’isolation de processus par défaut. Pour plus d’informations sur les modes d’isolation pour les conteneurs Windows, consultez [modes d’isolation](../manage-containers/hyperv-container.md).
    - Ports de publication : par défaut, les conteneurs Windows utilisent NAT pour le mode de mise en réseau. Cela signifie qu’un port sur l’hôte de conteneur sera mappé au conteneur. Cette option vous permet de spécifier ce mappage.
    - Allocation de mémoire et nombre de PROCESSEURs : vous pouvez spécifier la quantité de mémoire et le nombre de processeurs qu’un conteneur peut utiliser. Cette option n’alloue pas la mémoire ou l’UC affecté au conteneur. Au lieu de cela, cette option spécifie la quantité maximale pouvant être allouée par un conteneur.
    - Ajouter : vous pouvez également ajouter des paramètres d’exécution de l’Ancreur qui ne sont pas dans l’interface utilisateur, tels que-v pour le volume persistant. Pour plus d’informations sur les paramètres d’exécution de l’amarrage disponibles, consultez documentation de l' [ancrage](https://docs.docker.com/engine/reference/commandline/run/).

6. Une fois que vous avez terminé de configurer le conteneur, sélectionnez **exécuter**. Vous pouvez afficher l’état des conteneurs en cours d’exécution sous l’onglet **conteneurs** :

    ![Onglet Conteneurs](./media/WAC-Containers.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les Azure Container Registry sur le centre d’administration Windows](./wac-acr.md)