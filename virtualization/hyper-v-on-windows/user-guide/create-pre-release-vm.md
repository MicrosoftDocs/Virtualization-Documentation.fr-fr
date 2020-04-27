---
title: Essayer les fonctionnalités de la préversion pour Hyper-V
description: Essayer les fonctionnalités de la préversion pour Hyper-V
keywords: Windows 10, Hyper-V
author: scooley
ms.date: 05/02/2016
ms.topic: article
ms.prod: windows-10-hyperv
ms.service: windows-10-hyperv
ms.assetid: 426c87cc-fa50-4b8d-934e-0b653d7dea7d
ms.openlocfilehash: 725466f657ae8fc4f14813822e90657e12d26fa6
ms.sourcegitcommit: 16ebc4f00773d809fae84845208bd1dcf08a889c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "77439556"
---
# <a name="try-pre-release-features-for-hyper-v"></a>Essayer les fonctionnalités de la préversion pour Hyper-V

> Il s’agit d’un contenu préliminaire qui peut faire l’objet de modifications.  
  Les machines virtuelles en préversion sont destinées à des environnements de développement ou de test uniquement, car Microsoft n’en assure pas le support.

Accédez en avant-première aux fonctionnalités de la préversion pour Hyper-V sur Windows Server 2016 Technical Preview pour les tester dans vos environnements de développement ou de test. Vous pouvez être le premier à découvrir les dernières fonctionnalités Hyper-V et à participer à l’élaboration du produit en fournissant vos commentaires.

Les machines virtuelles que vous créez en préversion n’ont aucune compatibilité d’une build à l’autre, ni aucun support.  Ne les utilisez pas dans un environnement de production.

Voici d’autres raisons pour lesquelles elles sont réservées à des environnements de non-production :

* Il n’existe aucune compatibilité ascendante pour les machines virtuelles en préversion. Vous ne pouvez pas mettre à niveau ces machines virtuelles vers une nouvelle version de configuration.
* Les machines virtuelles en préversion n’ont pas de définition cohérente entre les builds. Si vous mettez à jour le système d’exploitation hôte, les machines virtuelles en préversion existantes peuvent être incompatibles avec l’hôte. Ces machines virtuelles peuvent ne pas démarrer ou peuvent sembler fonctionner au début pour ensuite rencontrer des problèmes de compatibilité importants.
* Si vous importez une machine virtuelle en préversion vers un hôte avec une build différente, les résultats sont imprévisibles. Vous pouvez déplacer une machine virtuelle en préversion vers un autre hôte. Toutefois, ce scénario ne fonctionne vraisemblablement que si les deux hôtes exécutent la même build.

## <a name="create-a-pre-release-virtual-machine"></a>Créer une machine virtuelle en préversion

Vous pouvez créer une machine virtuelle en préversion sur des hôtes Hyper-V qui exécutent Windows Server 2016 Technical Preview.

1. Sur le Bureau Windows, cliquez sur le bouton Démarrer et tapez une partie du nom **Windows PowerShell**.
2. Cliquez avec le bouton droit sur **Windows PowerShell**, puis sélectionnez **Exécuter en tant qu'administrateur**.
3. Utilisez l’applet de commande [New-VM](https://docs.microsoft.com/powershell/module/hyper-v/new-vm?view=win10-ps) avec l’indicateur -Prerelease pour créer la machine virtuelle en préversion. Par exemple, exécutez la commande suivante, où VM Name est le nom de la machine virtuelle que vous souhaitez créer.

``` PowerShell
New-VM -Name <VM Name> -Prerelease
```
Autres exemples d’utilisation de l’indicateur -Prerelease :
 - Pour créer une machine virtuelle qui utilise un disque dur virtuel existant ou un nouveau disque dur, consultez les exemples PowerShell dans [Créer une machine virtuelle dans Hyper-V sur Windows Server 2016 Technical Preview](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/Create-a-virtual-machine-in-Hyper-V#BKMK_PowerShell).
 - Pour créer un disque dur virtuel qui démarre sur une image du système d’exploitation, consultez l’exemple PowerShell dans [Déployer une machine virtuelle Windows dans Hyper-V sur Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/create-virtual-machine).

 Les exemples présentés dans ces articles fonctionnent pour les hôtes Hyper-V qui exécutent Windows 10 ou Windows Server 2016 Technical Preview. Pour l’instant, vous ne pouvez utiliser l’indicateur -Prerelease que pour créer une machine virtuelle en préversion sur des hôtes Hyper-V qui exécutent Windows Server 2016 Technical Preview.

## <a name="see-also"></a>Voir également
-  [Blog de virtualisation](https://techcommunity.microsoft.com/t5/Virtualization/bg-p/Virtualization) : Découvrez les fonctionnalités de la préversion qui sont disponibles et comment les tester.
- [Versions de configuration de machines virtuelles prises en charge](https://docs.microsoft.com/windows-server/virtualization/hyper-v/deploy/Upgrade-virtual-machine-version-in-Hyper-V-on-Windows-or-Windows-Server#BKMK_SupportedConfigVersions) : Apprenez à vérifier la version de configuration de la machine virtuelle et les versions prises en charge par Microsoft.
