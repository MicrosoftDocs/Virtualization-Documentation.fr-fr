---
title: HvFlushVirtualAddressListEx
description: HvFlushVirtualAddressListEx hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 60d56bdc4735c9b16534d3c7ad813e69734254af
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120519"
---
# <a name="hvflushvirtualaddresslistex"></a>HvFlushVirtualAddressListEx

L’hyperappel HvFlushVirtualAddressListEx est similaire à [HvCallFlushVirtualAddressList](HvCallFlushVirtualAddressList.md), mais peut prendre un VP fragmenté à taille variable défini en tant qu’entrée.
Les vérifications suivantes doivent être utilisées pour déduire la disponibilité de cet hyperappel :

- ExProcessorMasks doit être indiqué via le 0x40000004 de la feuille CPUID.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallFlushVirtualAddressListEx(
    _In_ HV_ADDRESS_SPACE_ID AddressSpace,
    _In_ HV_FLUSH_FLAGS Flags,
    _In_ HV_VP_SET ProcessorSet,,
    _Inout_ PUINT32 GvaCount,
    _In_reads_(GvaCount) PCHV_GVA GvaRangeList
    );
 ```

## <a name="call-code"></a>Appeler du code
`0x0014` Attaché

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `AddressSpace`          | 0          | 8        | Spécifie un ID d’espace d’adresse (valeur CR3). |
| `Flags`                 | 8          | 8        | Ensemble de bits d’indicateur qui modifient le fonctionnement du vidage. |
| `ProcessorSet`          | 16         | Variable | Jeu de processeurs indiquant les processeurs qui doivent être affectés par l’opération de vidage. |

## <a name="input-list-element"></a>Élément de liste d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `GvaRange`              | 0          | 8        | Plage GVA                                 |

## <a name="see-also"></a>Voir aussi

[HV_VP_SET](../datatypes/HV_VP_SET.md)
