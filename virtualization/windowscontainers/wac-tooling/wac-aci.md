---
title: Gérer les Azure Container Instances sur le centre d’administration Windows
description: Azure Container Instances sur le centre d’administration Windows
keywords: ancrage, conteneurs, Centre d’administration Windows
author: viniap
ms.author: viniap
ms.date: 12/24/2020
ms.topic: tutorial
ms.assetid: bb9bfbe0-5bdc-4984-912f-9c93ea67105f
ms.openlocfilehash: b4de665fdc9adb36281c17d0c0eb7e60e32e9459
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98186628"
---
# <a name="manage-azure-container-instances-using-windows-admin-center"></a>Gérer les Azure Container Instances à l’aide du centre d’administration Windows

Cette rubrique explique comment gérer Azure Container Instances (ACI) à l’aide du centre d’administration Windows. Azure Container Instances est une solution adaptée à tous les scénarios qui peut fonctionner dans des conteneurs isolés, sans orchestration.

>[!Note]
>Un abonnement Azure est requis pour exécuter les étapes de ce didacticiel. Pour plus d’informations sur la connexion de votre instance du centre d’administration Windows à Azure, consultez Configuration de l' [intégration d’Azure](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/azure-integration).

Le centre d’administration Windows vous permet d’effectuer une gestion de base de Azure Container Instances, qui comprend la liste des instances de conteneur existantes, le démarrage et l’arrêt d’une instance, la suppression d’instances et l’ouverture du portail Azure pour la gestion avancée.

![Azure Container instance](./media/WAC-ACI.png)

Avec l’instance de conteneur, vous pouvez effectuer les actions suivantes :

- **Démarrer**: pour démarrer une instance existante qui est actuellement arrêtée.
- **Fin**: pour arrêter une instance en cours d’exécution.
- **Supprimer**: pour supprimer une instance. Cela est irréversible et supprime toute configuration qui a été effectuée pour l’instance.
- **Gérer dans Azure**: ouvre le volet du **portail Azure** pour gérer l’instance de conteneur sélectionnée dans un nouvel onglet de navigateur.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les Azure Container Registry sur le centre d’administration Windows](./wac-acr.md)