---
title: FAQ sur les conteneurs Windows
description: FAQ sur les conteneurs Windows Server
keywords: docker, conteneurs
author: PatrickLang
ms.author: PatrickLang
ms.date: 10/25/2019
ms.topic: overview
ms.assetid: 25de368c-5a10-40a4-b4aa-ac8c9a9ca022
ms.openlocfilehash: 9489b785a097340de0d4acba7720ac7ae297739b
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98182036"
---
# <a name="frequently-asked-questions-about-containers"></a>Questions fréquentes sur les conteneurs

Vous avez une question sur les conteneurs Windows Server ? Vérifiez s’il y a une réponse à celle-ci dans la liste ci-dessous.

## <a name="whats-the-difference-between-linux-and-windows-server-containers"></a>Quelle est la différence entre les conteneurs Linux et Windows Server ?

Linux et Windows Server implémentent tous deux des technologies similaires au sein de leurs noyau et système d’exploitation principal. La différence provient de la plateforme et des charges de travail qui s’exécutent dans les conteneurs.

Lorsqu'un client utilise des conteneurs Windows Server, ils peuvent être intégrés aux technologies Windows existantes, comme .NET, ASP.NET et PowerShell.

## <a name="what-are-the-prerequisites-for-running-containers-on-windows"></a>Quelles sont les conditions préalables à l’exécution de conteneurs sur Windows ?

Les conteneurs ont été introduits sur la plateforme avec Windows Server 2016. Pour utiliser des conteneurs, vous devez disposer de Windows Server 2016 ou de la Mise à jour anniversaire Windows 10 (version 1607) ou plus récente. Pour plus d’informations, consultez [Configuration requise](../deploy-containers/system-requirements.md).

## <a name="what-windows-operating-systems-are-supported"></a>Quels sont les systèmes d’exploitation Windows pris en charge ?

AKS sur Azure Stack HCI utilise Windows Server 2019 comme version du système d’exploitation hôte de conteneur et prend en charge seulement l’isolation des processus. Pour la compatibilité des versions des conteneurs Windows, consultez [Compatibilité des versions des conteneurs Windows](../deploy-containers/version-compatibility.md).

## <a name="how-do-i-patch-my-windows-nodes"></a>Comment corriger mes nœuds Windows ?

Les nœuds Windows Server dans AKS sur Azure Stack HCI doivent être mis à niveau pour recevoir les mises à jour et les correctifs les plus récents. Les mises à jour Windows ne sont pas activées sur les nœuds Windows dans AKS sur Azure Stack HCI. Cependant, AKS sur Azure Stack HCI rend les mises à jour Windows disponibles en mettant régulièrement à jour les images de nœud Windows Server.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Mes conteneurs Windows Server peuvent-ils utiliser gMSA ?

Oui, gMSA est pris en charge avec les nœuds worker Windows joints à un domaine. Pour découvrir comment utiliser gMSA avec des conteneurs Windows, consultez [Préparer les nœuds Windows pour gMSA](https://docs.microsoft.com/azure-stack/aks-hci/prepare-windows-nodes-gmsa).

## <a name="what-are-wcow-and-lcow"></a>Que signifient WCOW et LCOW ?

WCOW est l'abréviation de « Windows Containers on Windows », Conteneurs Windows sur Windows. LCOW est l'abréviation de « Linux Containers on Windows », Conteneurs Linux sur Windows.

## <a name="how-are-containers-licensed-is-there-a-limit-to-the-number-of-containers-i-can-run"></a>Comment les conteneurs sont-ils sous licence ? Le nombre de conteneurs que je peux exécuter est-il limité ?

L’image de conteneur Windows [CLUF](../images-eula.md) décrit une utilisation reposant sur un utilisateur qui dispose d’un système d’exploitation hôte sous licence valide. Le nombre de conteneurs qu’un utilisateur est autorisé à exécuter dépend de l’édition du système d’exploitation hôte et du [mode d'isolation](../manage-containers/hyperv-container.md) avec lequel un conteneur est exécuté, ainsi que de l’exécution de ces conteneurs à des fins de développement/test ou de production.

|Système d’exploitation hôte                                                         |Limite de conteneurs isolés par processus                   |Limite de conteneurs isolés par Hyper-V                   |
|----------------------------------------------------------------|---------------------------------------------------|---------------------------------------------------|
|Windows Server Standard                                         |Illimité                                          |2                                                  |
|Windows Server Datacenter                                       |Illimité                                          |Illimité                                          |
|Windows 10 Professionnel et Entreprise                                   |Illimité  *(à des fins de test ou de développement uniquement)*|Illimité  *(à des fins de test ou de développement uniquement)*|
|Windows 10 IoT Core et Entreprise                             |Illimité*                                         |Illimité*                                          |

L’utilisation des images de conteneur Windows Server est déterminée par la lecture du nombre d'invités de virtualisation pris en charge pour cette [édition](/windows-server/get-started-19/editions-comparison-19.md). <br/>

>[!NOTE]
>\*L’utilisation en production de conteneurs sur une édition IoT de Windows dépend de votre acceptation des Conditions d’utilisation commerciales de Microsoft pour les images du runtime Windows 10 Core ou de la licence d’appareil Windows 10 IoT Enterprise (« Contrat commercial Windows IoT »). Des conditions et restrictions supplémentaires liées aux Contrats commerciaux Windows IoT s’appliquent à votre utilisation d'image de conteneur dans un environnement de production. Veuillez consulter le [CLUF relatif à l'image de conteneur](../images-eula.md) pour comprendre précisément ce qui est autorisé et ce qui ne l’est pas.

## <a name="as-a-developer-do-i-have-to-rewrite-my-app-for-each-type-of-container"></a>En tant que développeur, dois-je réécrire mon application pour chaque type de conteneur ?

Non. Les images des conteneurs Windows sont communes aux conteneurs Windows Server et à l'isolation Hyper-V. Le choix du type de conteneur est effectué quand vous démarrez le conteneur. Du point de vue d’un développeur, les conteneurs Windows Server et l'isolation Hyper-V sont deux versions du même élément. Ils offrent la même expérience de développement, de programmation et de gestion, sont ouverts et extensibles, et présentent le même niveau d’intégration et de prise en charge avec Docker.

Un développeur peut créer une image de conteneur à l’aide d’un conteneur Windows Server et la déployer dans l'isolation Hyper-V ou inversement, sans modification autre que la spécification de l’indicateur d’exécution approprié.

Les conteneurs Windows Server fournissent une densité et des performances supérieures lorsque la vitesse est primordiale, notamment, une durée de rotation inférieure, des performances d’exécution plus rapides par rapport aux configurations imbriquées. Comme son nom l'indique, l'isolation Hyper-V offre une isolation supérieure en garantissant que le code s’exécutant dans un conteneur ne peut pas compromettre ni affecter le système d’exploitation hôte ni d’autres conteneurs exécutés sur le même hôte. Cela est utile pour les scénarios avec plusieurs clients (où l’hébergement de code non fiable est requis), y compris les applications SaaS et l’hébergement d’ordinateurs.

## <a name="can-i-run-windows-containers-in-process-isolated-mode-on-windows-10"></a>Puis-je exécuter des conteneurs Windows en mode isolé par processus sur Windows 10 ?

À partir de la mise à jour d'octobre 2018 de Windows 10, vous pouvez exécuter un conteneur Windows avec isolation de processus mais devez, dans un premier temps, demander l’isolation de processus à l’aide de l’indicateur `--isolation=process` lors de l’exécution de vos conteneurs avec `docker run`. L’isolation de processus est compatible sur Windows 10 Professionnel, Windows 10 Entreprise, Windows 10 IoT Core et Windows 10 IoT Enterprise.

Si vous souhaitez exécuter vos conteneurs Windows de cette manière, vous devez vous assurer que votre hôte exécute Windows 10 Build 17763+ et que vous disposez d’une version Docker dotée du moteur 18.09 ou plus récent.

> [!WARNING]
> En dehors des hôtes IoT Core et IoT Entreprise (après acceptation des conditions générales supplémentaires), cette fonctionnalité est réservée au développement et au test. Vous devez continuer d'utiliser Windows Server en tant qu'hôte pour les déploiements de production. En utilisant cette fonctionnalité, vous devez également vous assurer que les balises de version de l’hôte et du conteneur correspondent, à défaut de quoi le conteneur peut ne démarrer ou ne pas se comporter comme prévu.

## <a name="how-do-i-make-my-container-images-available-on-air-gapped-machines"></a>Comment faire en sorte que mes images de conteneur soient disponibles sur des machines isolées ?

Les images de base de conteneur Windows contiennent des artefacts dont la distribution est limitée par licence. Lorsque vous créez en utilisant ces images et les transmettez (push) à un registre public ou privé, vous pouvez constater que la couche de base n’est jamais envoyée. En effet, nous utilisons le concept de couche étrangère qui pointe vers la couche de base réelle résidant dans le stockage cloud Azure.

Cela peut compliquer la situation lorsque vous disposez d'une machine isolée capable uniquement d'extraire des images à partir de l'adresse de votre registre de conteneurs privé. Dans ce cas, les tentatives de suivi de la couche étrangère visant à récupérer l’image de base échoueront. Pour contourner le comportement de la couche étrangère, vous pouvez utiliser l’indicateur `--allow-nondistributable-artifacts` dans le démon Docker.

> [!IMPORTANT]
> L’utilisation de cet indicateur ne modifie en rien votre obligation de vous conformer aux termes de la licence d’image de base du conteneur Windows ; vous ne devez pas publier de contenu Windows à des fins de redistribution publique ou tierce. Une utilisation au sein de votre environnement est autorisée.

## <a name="additional-feedback"></a>Commentaires supplémentaires

Vous souhaitez ajouter des éléments au Forum aux questions ? Ouvrez un nouveau problème dans la section des commentaires ou configurez une requête de tirage pour cette page avec GitHub.
