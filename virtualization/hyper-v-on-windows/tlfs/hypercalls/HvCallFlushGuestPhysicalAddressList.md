---
title: HvCallFlushGuestPhysicalAddressList
description: HvCallFlushGuestPhysicalAddressList hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 990b89e1ba422fa169c3334e69de0f55dd609061
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120525"
---
# <a name="hvcallflushguestphysicaladdresslist"></a>HvCallFlushGuestPhysicalAddressList

L’hyperappel HvCallFlushGuestPhysicalAddressList invalide les mappages amp GVA/L2 de L2 mis en cache dans une partie d’un espace d’adressage de deuxième niveau.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallFlushGuestPhysicalAddressList(
    _In_ HV_SPA AddressSpace,
    _In_ UINT64 Flags,
    _In_reads_(RangeCount) PHV_GPA_PAGE_RANGE GpaRangeList
    );
 ```

Cet hyperappel peut uniquement être utilisé avec la virtualisation imbriquée est active. L’opération d’invalidation du TLB virtuel agit sur tous les processeurs.

Cet appel garantit que, lorsque le contrôle de temps retourne à l’appelant, les effets observables de tous les vidages se sont produits.
Si le TLB est actuellement « verrouillé », le processeur virtuel de l’appelant est suspendu.

Cet appel prend une liste de plages d’adresses GPA L2 à vider. Chaque plage possède un GPA L2 de base. Étant donné que les vidages sont effectués avec une granularité de page, les 12 bits inférieurs de l’GPA L2 peuvent être utilisés pour définir une longueur de plage. Ces bits encodent le nombre de pages supplémentaires (au-delà de la page initiale) dans la plage. Cela permet à chaque entrée d’encoder une plage de 1 à 4096 pages.

## <a name="call-code"></a>Appeler du code

`0x00B0` Attaché

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `AddressSpace`          | 0          | 8        | Spécifie un ID d’espace d’adresse (pointeur de table EPT PML4). |
| `Flags`                 | 8          | 8        | RsvdZ                                     |

## <a name="input-list-element"></a>Élément de liste d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `GpaRange`              | 0          | 8        | Plage GPA                                 |