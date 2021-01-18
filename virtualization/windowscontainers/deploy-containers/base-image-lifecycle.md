---
title: Cycles de vie de la maintenance des images de base
description: Informations sur le cycle de vie des images de base de conteneur Windows.
keywords: conteneurs windows, conteneurs, cycle de vie, informations sur la publication, image de base, image de base de conteneur
author: v-susbo
ms.author: helohr
ms.date: 10/20/2020
ms.topic: reference
ms.openlocfilehash: ec466de78f55ba752a7f7747fbe6ee53ccf5f86a
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98182016"
---
# <a name="base-image-servicing-lifecycles"></a>Cycles de vie de la maintenance des images de base

> [!Note]
> Microsoft a retardé les dates planifiées de fin du support et de maintenance pour un certain nombre de produits afin d’aider les personnes et les organisations à concentrer leur attention sur le maintien de la continuité des activités. Pour plus d’informations, consultez l’article [Changements du cycle de vie aux dates de fin de support et de maintenance](https://support.microsoft.com/help/4557164/lifecycle-changes-to-end-of-support-and-servicing-dates) du 14 avril 2020.

Les images de base de conteneur Windows reposent sur des publications du Canal semi-annuel ou du Canal de maintenance à long terme de Windows Server. Cet article vous indique la durée du support pour les différentes versions des images de base des deux canaux.

Le Canal semi-annuel est une publication de mise à jour de fonctionnalités qui planifie, deux fois par an, des chronologies de maintenance de 18 mois. Il permet aux clients de tirer parti des nouvelles fonctionnalités du système d’exploitation à un rythme plus soutenu, tant dans les applications (en particulier, celles basées sur des conteneurs et microservices), que dans le centre de données hybride à définition logicielle. Pour plus d’informations, consultez la [Présentation du canal semi-annuel de Windows Server](/windows-server/get-started/semi-annual-channel-overview).

Pour les images de Server Core, les clients peuvent également utiliser le Canal de maintenance à long terme qui publie une nouvelle version majeure de Windows Server tous les deux à trois ans. Les publications effectuées via la Canal de maintenance à long terme bénéficient de cinq ans de supports standard et étendu. Ce canal fonctionne avec des systèmes qui nécessitent une option de maintenance et une stabilité fonctionnelle plus longues.

Le tableau suivant répertorie chaque type d’image de base, son canal de maintenance et la durée de support associée.

|Base image                       |Canal de maintenance|Version|Build du système d’exploitation|Disponibilité|Date de fin du support standard|Date de support étendue|
|---------------------------------|-----------------|-------|--------|------------|---------------------------|---------------------|
|Server Core, Nano Server, Windows|Semi-annuel      |20H2   |19042   |20/10/2020  |10/05/2022                 |NON APPLICABLE                  |
|Server Core, Nano Server, Windows|Semi-annuel      |2004   |19041   |27/05/2020  |14/12/2021                 |NON APPLICABLE                  |
|Server Core, Nano Server, Windows|Semi-annuel      |1909   |18363   |12/11/2019  |11/05/2021                 |NON APPLICABLE                  |
|Server Core, Nano Server, Windows|Semi-annuel      |1903   |18362   |21/05/2019  |08/12/2020                 |NON APPLICABLE                  |
|Server Core                      |À long terme        |2019   |17763   |13/11/2018  |09/01/2024                 |09/01/2029           |
|Nano Server                      |Semi-annuel      |1809   |17763   |13/11/2018  |09/01/2024                 |NON APPLICABLE                  |
|Server Core, Windows             |Semi-annuel      |1809   |17763   |13/11/2018  |10/11/2020                 |NON APPLICABLE                  |
|Server Core, Nano Server         |Semi-annuel      |1803   |17134   |30/04/2018  |12/11/2019                 |NON APPLICABLE                  |
|Server Core, Nano Server         |Semi-annuel      |1709   |16299   |17/10/2017  |09/04/2019                 |NON APPLICABLE                  |
|Server Core                      |À long terme        |2016   |14393   |15/10/2016  |11/01/2022                 |11/01/2027           |
|Nano Server                      |Semi-annuel      |1607   |14393   |15/10/2016  |10/09/2018                 |NON APPLICABLE                  |

Pour plus d’informations sur les conditions requises pour la maintenance et autres, consultez les pages [FAQ sur le cycle de vie - Produits Windows](https://support.microsoft.com/help/18581/lifecycle-faq-windows-products) et [Informations de publication de Windows Server](/windows-server/get-started/windows-server-release-info), ainsi que le [dépôt du Docker Hub Windows base OS images](https://hub.docker.com/_/microsoft-windows-base-os-images).
