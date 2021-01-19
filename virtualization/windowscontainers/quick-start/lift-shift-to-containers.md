---
title: Lift-and-shift sur des conteneurs
description: Découvrez comment migrer des applications existantes vers des conteneurs
keywords: conteneurs, migrer
author: v-susbo
ms.author: v-susbo
ms.date: 12/11/2020
ms.topic: quickstart
ms.openlocfilehash: 6185290fa34621c10305b3c5acff138a624c74b1
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98186820"
---
# <a name="lift-and-shift-to-containers"></a>Lift-and-shift sur des conteneurs
Cette rubrique fournit des conseils pour la modernisation des applications traditionnelles et héritées en effectuant leur migration lift-and-shift vers conteneurs. Vous allez découvrir comment identifier les types d’applications qui sont appropriés pour des conteneurs, puis comment effectuer leur migration lift-and-shift avec un minimum de modifications de votre code. Certaines applications Windows Server traditionnelles qui s’exécutent actuellement sur des machines physiques ou sur des machines virtuelles peuvent être facilement déplacées vers des conteneurs. 

Pour voir une feuille de route des fonctionnalités actuellement disponibles et planifiées, consultez la [feuille de route des conteneurs Windows Server](https://github.com/microsoft/Windows-Containers/projects/1).

## <a name="benefits-of-using-containers"></a>Avantages de l’utilisation des conteneurs
Comme les conteneurs sont un moyen d’isoler une application dans son propre package, où elle n’est pas affectée par des applications ou des processus qui existent en dehors du conteneur, il est facile de voir comment cela réduit les coûts, et permet la portabilité et le contrôle.
L’utilisation de conteneurs pour créer des applications offre un accroissement significatif de l’agilité pour la création et l’exécution d’applications dans n’importe quelle infrastructure. Avec les conteneurs, vous pouvez faire passer n’importe quelle application du développement à la production avec peu ou pas de modifications du code, grâce à l’intégration de Docker avec les outils de développement et les systèmes d’exploitation de Microsoft. Pour obtenir la liste complète des avantages liés à l’utilisation des conteneurs, consultez [Déployer des applications .NET existantes en tant que conteneurs Windows](https://docs.microsoft.com/dotnet/architecture/modernize-with-azure-containers/modernize-existing-apps-to-cloud-optimized/deploy-existing-net-apps-as-windows-containers).

## <a name="supported-applications-for-containers"></a>Applications prises en charge pour les conteneurs

Consultez le tableau suivant avant de choisir l’application que vous voulez conteneuriser : 

| Application | Considérations |
|-------------|----------------|
| les applications web | Les applications web sont de bonnes candidates à la conteneurisation. Pour les applications héritées créées avec ASP.NET, la meilleure image à utiliser est l’image ASP.NET provisionnée pour .NET 3.5, qui peut également exécuter des applications ASP.NET 2.0 et 1.X. |
| Services WCF (Windows Communication Foundation) | Ces applications orientées service créées avec WCF sont souvent hébergées sur IIS, mais elles peuvent être hébergées dans d’autres applications et services, comme une autre application web ou un autre service Windows. |
| Windows Services | Ces applications s’exécutent en tant que services d’arrière-plan dans Windows et peuvent être conteneurisées. Cependant, comme les services s’exécutent en arrière-plan, vous devez créer un processus de premier plan pour que le conteneur reste en cours d’exécution. |
|  Applications console | Il s’agit d’applications qui sont exécutées à partir de la ligne de commande et qui sont de bonnes candidates pour les conteneurs. |

Vous devez également consulter la liste des [applications non prises en charge pour les conteneurs](#applications-not-supported-by-containers).

## <a name="lift-and-shift-windows-applications"></a>Migration lift-and-shift d’applications Windows

### <a name="plan-how-to-lift-and-shift-legacy-applications"></a>Planifier la migration lift-and-shift d’applications héritées 

Utilisez les étapes suivantes pour planifier la façon dont vous allez déplacer des applications traditionnelles et héritées vers des conteneurs.

1. Déterminez l’image de base du système d’exploitation Windows dont vous avez besoin : [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore) ou [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver).
2. Déterminez le type de conteneur, par exemple s’il doit être isolé des processus ou isolé de l’hyperviseur. Pour plus d’informations, consultez [Modes d’isolation](../manage-containers/hyperv-container.md). Actuellement, AKS et Azure Kubernetes Service sur Azure Stack HCI prennent en charge seulement les conteneurs isolés des processus, et Windows Server 2019 Datacenter est le seul hôte de conteneur pris en charge. Dans une version ultérieure, AKS et Azure Kubernetes Service sur Azure Stack HCI prendront en charge les conteneurs isolés de l’hyperviseur.
3. Configurez la sécurité pour vos conteneurs. Pour configurer un conteneur Windows pour qu’il s’exécute avec un compte de service administré de groupe (gMSA), consultez [Créer des comptes de service administré de groupe pour des conteneurs Windows](../manage-containers/manage-serviceaccounts.md).
4. Conteneurisez l’application. Avant de pouvoir le faire, vous devez [installer l’extension **Conteneurs**](../wac-tooling/wac-extension.md) dans Windows Admin Center. Ensuite, vous pouvez utiliser Windows Admin Center pour [créer des images conteneur](../wac-tooling/wac-images.md) et les [exécuter](../wac-tooling/wac-containers.md). 
5. Envoyez (push) l’application héritée en [créant le registre de conteneurs](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-powershell) et en [chargeant l’image](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-powershell#push-image-to-registry) dans le registre.
6. Déployez sur un environnement Kubernetes pris en charge par Windows. Vous pouvez déployer sur [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) ou sur Azure Kubernetes Service sur Azure Stack HCI en utilisant [Windows Admin Center](https://docs.microsoft.com/azure-stack/aks-hci/setup) ou [PowerShell](https://docs.microsoft.com/azure-stack/aks-hci/setup-powershell).

Pour vous aider à choisir les applications pour lesquelles effectuer une migration lift-and-shift et à développer un plan pour la façon de le faire, consultez l’organigramme suivant :

![Graphique montrant un organigramme sur la façon d’effectuer une migration lift-and-shift d’applications Windows.](./media/DecisionTreeflowchartv2.jpg#lightbox)    

## <a name="applications-not-supported-by-containers"></a>Applications non prises en charge par les conteneurs

À compter de mai 2018, les applications suivantes ne sont pas prises en charge pour les conteneurs Windows :
-  Pour en savoir plus sur les scénarios pris en charge pour l’utilisation de MSMQ (Microsoft Message Queuing) et des conteneurs Windows, consultez l’article de Microsoft Tech Community [MSMQ et les conteneurs Windows](https://techcommunity.microsoft.com/t5/containers/msmq-and-windows-containers/ba-p/1981414). Pour découvrir des informations supplémentaires, consultez le [Forum de discussion Windows Server](https://windowsserver.uservoice.com/forums/304624-containers/suggestions/15719031-create-base-container-image-with-msmq-server) et le [Forum Microsoft Developer Network](https://social.msdn.microsoft.com/Forums/bce99a7d-aa60-44fa-a348-450855650810/msmqserver-is-it-supported?forum=windowscontainers). 
- MSDTC (Microsoft Distributed Transaction Coordinator) n’est actuellement pas pris en charge dans les conteneurs Windows. Pour plus d’informations, consultez la page [des problèmes de GitHub](https://github.com/MicrosoftDocs/Virtualization-Documentation/issues/494).    
- Microsoft Office ne prend actuellement pas en charge les conteneurs. Pour plus d’informations, consultez le [Forum des demandes UserVoice](https://windowsserver.uservoice.com/forums/304624-containers/suggestions/19686220-provide-office-support-for-containers).  
- Les applications clientes avec une interface utilisateur visuelle ne sont pas prises en charge.  
- Les rôles d’infrastructure Windows ne sont pas pris en charge. Ceci inclut DNS, DHCP, DC, NTP, les services d’impression, le serveur de fichiers, et la Gestion des identités et des accès (IAM).  
  
Utilisez le [dépôt GitHub](https://github.com/microsoft/Windows-Containers/issues) des conteneurs Windows pour découvrir des informations supplémentaires sur les scénarios non pris en charge et les demandes de la communauté.