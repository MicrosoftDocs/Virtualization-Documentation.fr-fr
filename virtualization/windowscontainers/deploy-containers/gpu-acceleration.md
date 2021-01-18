---
title: Accélération GPU dans les conteneurs Windows
description: Quel est le niveau d’accélération GPU dans les conteneurs Windows
keywords: docker, conteneurs, appareils, matériel
author: v-susbo
ms.author: v-susbo
ms.date: 12/29/2020
ms.topic: how-to
ms.openlocfilehash: c6148f5703a3a50628ad81b506a7aac571781cd4
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98182026"
---
# <a name="gpu-acceleration-in-windows-containers"></a>Accélération GPU dans les conteneurs Windows

Pour de nombreuses charges de travail en conteneur, les ressources de calcul de l’UC offrent des performances suffisantes. Cependant, pour une certaine classe de charge de travail, la puissance de calcul massivement parallèle offerte par les GPU (unités de traitement graphique) peut accélérer les opérations par ordre de magnitude, ce qui a pour effet de réduire les coûts et d’améliorer considérablement le débit.

Les GPU sont déjà des outils couramment utilisés pour de nombreuses charges de travail populaires, allant du rendu et de la simulation traditionnels à l’apprentissage, la formation et l’inférence automatiques. Les conteneurs Windows prennent en charge l’accélération GPU pour les technologies DirectX et toutes les infrastructures basées sur celles-ci.

> [!NOTE]
> Cette fonctionnalité est disponible dans Docker Desktop, version 2.1 et Docker Engine - Enterprise, version 19.03 ou ultérieure.

## <a name="requirements"></a>Conditions requises

Pour que cette fonctionnalité soit opérationnelle, votre environnement doit satisfaire aux exigences suivantes :

- L’hôte de conteneur doit exécuter Windows Server 2019 ou Windows 10, version 1809 ou ultérieure.
- L’image de base du conteneur doit être [mcr.microsoft.com/windows:1809](https://hub.docker.com/_/microsoft-windows) ou une version plus récente. Les images de conteneur Windows Server Core et Nano Server ne sont pas prises en charge actuellement.
- L’hôte de conteneur doit exécuter Docker Engine, version 19.03 ou plus récente.
- L’hôte de conteneur doit avoir un GPU exécutant des pilotes d’affichage WDDM, version 2.5 ou ultérieure.

Pour vérifier la version de WDDM de vos pilotes d’affichage, exécutez l’outil de diagnostic DirectX (dxdiag.exe) sur l’hôte de votre conteneur. Sous l’onglet « Display » de l’outil, examinez la section « Drivers », comme indiqué ci-dessous.

![Dxdiag](media/dxdiag.png)

## <a name="run-a-container-with-gpu-acceleration"></a>Exécuter un conteneur avec une accélération GPU

Pour démarrer un conteneur avec une accélération GPU, exécutez la commande suivante :

```shell
docker run --isolation process --device class/5B45201D-F2F2-4F3B-85BB-30FF1F953599 mcr.microsoft.com/windows:1809
```

> [!IMPORTANT]
> Les technologies DirectX (et toutes les infrastructures basées dessus) sont les seules API qui peuvent être accélérées avec un GPU dès aujourd’hui. Les infrastructures tierces ne sont pas prises en charge.

## <a name="hyper-v-isolated-windows-container-support"></a>Prise en charge de conteneur Windows isolé par Hyper-V

L’accélération GPU pour les charges de travail dans des conteneurs Windows isolés par Hyper-V n’est pas prise en charge actuellement.

## <a name="hyper-v-isolated-linux-container-support"></a>Prise en charge de conteneur Linux isolé par Hyper-V

L’accélération GPU pour les charges de travail dans des conteneurs Linux isolés par Hyper-V n’est pas prise en charge actuellement.

## <a name="more-information"></a>Autres informations

Pour un exemple complet d’application DirectX en conteneur qui tire parti de l’accélération GPU, consultez [Exemple de conteneur DirectX](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-container-samples/directx).
