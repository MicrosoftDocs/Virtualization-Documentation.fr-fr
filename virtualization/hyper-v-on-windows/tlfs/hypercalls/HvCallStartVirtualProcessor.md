---
title: HvCallStartVirtualProcessor
description: HvCallStartVirtualProcessor hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 262ba48156d7b73d5a7aa3f7b72a12aacb09a2a0
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120604"
---
# <a name="hvcallstartvirtualprocessor"></a>HvCallStartVirtualProcessor

HvCallStartVirtualProcessor est une méthode de démarrage d’un processeur virtuel. Elle est fonctionnellement équivalente aux méthodes traditionnelles basées sur INIT, à ceci près que le VP peut commencer par un état de Registre souhaité.

Il s’agit de la seule méthode pour démarrer un VP dans une VTL non nulle.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallStartVirtualProcessor(
    _In_ HV_PARTITION_ID PartitionId,
    _In_ HV_VP_INDEX VpIndex,
    _In_ HV_VTL TargetVtl,
    _In_ HV_INITIAL_VP_CONTEXT VpContext
    );
 ```

## <a name="call-code"></a>Appeler du code

`0x0099` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `PartitionId`           | 0          | 8        | Partition                                 |
| `VpIndex`               | 8          | 4        | VP d’index à démarrer. Pour récupérer l’index du VP à partir d’un ID APIC, utilisez HvGetVpIndexFromApicId. |
| `TargetVtl`             | 12         | 1        | VTL cible                                |
| `VpContext`             | 16         | 224      | Spécifie le contexte initial dans lequel le VP doit démarrer. |

## <a name="return-values"></a>Valeurs de retour

| Code d’état                         | Condition d'erreur                                       |
|-------------------------------------|-------------------------------------------------------|
| `HV_STATUS_ACCESS_DENIED`           | Accès refusé                                         |
| `HV_STATUS_INVALID_PARTITION_ID`    | L’ID de partition spécifié n’est pas valide.                |
| `HV_STATUS_INVALID_VP_INDEX`        | Le processeur virtuel spécifié par HV_VP_INDEX n’est pas valide. |
| `HV_STATUS_INVALID_REGISTER_VALUE`  | La valeur de Registre fournie n’est pas valide.               |
| `HV_STATUS_INVALID_VP_STATE`        | Un processeur virtuel n’est pas dans l’état correct pour les performances de l’opération indiquée. |
| `HV_STATUS_INVALID_PARTITION_STATE` | La partition spécifiée n’est pas dans l’état « actif ». |
| `HV_STATUS_INVALID_VTL_STATE`       | L’état de la VTL est en conflit avec l’opération demandée. |

## <a name="see-also"></a>Voir aussi

[HV_INITIAL_VP_CONTEXT](../datatypes/HV_INITIAL_VP_CONTEXT.md)