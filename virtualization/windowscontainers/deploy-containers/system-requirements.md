---
title: Configuration requise pour un conteneur Windows
description: Configuration requise pour un conteneur Windows.
keywords: métadonnées, conteneurs
author: taylorb-microsoft
ms.author: taylorb
ms.date: 10/22/2019
ms.topic: deployment
ms.assetid: 3c3d4c69-503d-40e8-973b-ecc4e1f523ed
ms.openlocfilehash: 6e6007d2b953f07d9b5b5d0cf1bec58dcbcd2fd7
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98182106"
---
# <a name="windows-container-requirements"></a>Configuration requise pour un conteneur Windows

Ces guides fournissent la configuration requise pour un hôte de conteneur Windows.

## <a name="operating-system-requirements"></a>Configuration système requise

- La fonctionnalité de conteneur Windows est disponible sur Windows Server (Canal semi-annuel), Windows Server 2019, Windows Server 2016 et Windows 10 Professionnel et Entreprise (versions 1607 et ultérieures).
- Le rôle Hyper-V doit être installé avant l’activation d’une isolation Hyper-V.
- Sur les hôtes de conteneur Windows Server, Windows doit être installé sur C:\. Cette restriction ne s’applique pas si seuls des conteneurs isolés par Hyper-V sont déployés.

## <a name="virtualized-container-hosts"></a>Hôtes de conteneurs virtualisés

Si un hôte de conteneur Windows doit être exécuté sur une machine virtuelle Hyper-V et héberger une isolation Hyper-V, la virtualisation imbriquée doit être activée. La configuration requise pour la virtualisation imbriquée est la suivante :

- Au moins 4 Go de RAM disponible pour l’hôte Hyper-V virtualisé.
- Windows Server (Canal semi-annuel), Windows Server 2019, Windows Server 2016 ou Windows 10 sur le système hôte, et Windows Server (installation complète ou Server Core) sur la machine virtuelle.
- Un processeur Intel VT-x (cette fonctionnalité est actuellement disponible pour les processeurs Intel uniquement).
- La machine virtuelle de l’hôte du conteneur a également besoin d’au moins deux processeurs virtuels.

### <a name="memory-requirements"></a>Mémoire requise

Les restrictions applicables à la mémoire disponible pour les conteneurs peuvent être configurées via [les contrôles de ressources](../manage-containers/resource-controls.md) ou la surcharge d’un hôte de conteneur.  La quantité minimale de mémoire requise pour lancer un conteneur et exécuter des commandes de base (ipconfig dir, etc.) est indiquée ci-dessous.

>[!NOTE]
>Ces valeurs ne tiennent pas compte du partage de ressources entre les conteneurs ou de la configuration requise par l’application en cours d’exécution dans le conteneur.  Par exemple, un hôte avec 512 Mo de mémoire disponible peut exécuter plusieurs conteneurs Server Core sous isolation Hyper-V, car ces conteneurs partagent des ressources.

#### <a name="windows-server-2016"></a>Windows Server 2016

| Base image  | Conteneur Windows Server | Isolation Hyper-V    |
| ----------- | ------------------------ | -------------------- |
| Nano Server | 40 Mo                     | 130 Mo + fichier d’échange de 1 Go |
| Server Core | 50 Mo                     | 325 Mo + fichier d’échange de 1 Go |

#### <a name="windows-server-semi-annual-channel"></a>Windows Server (canal semi-annuel)

| Base image  | Conteneur Windows Server | Isolation Hyper-V    |
| ----------- | ------------------------ | -------------------- |
| Nano Server | 30 Mo                     | 110 Mo + fichier d’échange de 1 Go |
| Server Core | 45 Mo                     | 360 Mo + fichier d’échange de 1 Go |

## <a name="see-also"></a>Voir aussi

[Stratégie de support pour les conteneurs Windows et Docker dans des scénarios locaux](https://support.microsoft.com/help/4489234/support-policy-for-windows-containers-and-docker-on-premises)