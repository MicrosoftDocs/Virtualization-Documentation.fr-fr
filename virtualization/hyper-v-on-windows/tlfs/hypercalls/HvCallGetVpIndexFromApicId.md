---
title: HvCallGetVpIndexFromApicId
description: HvCallGetVpIndexFromApicId hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: d7dc0d9314e1a51a693ca041039e0d6386f24c0f
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120480"
---
# <a name="hvcallgetvpindexfromapicid"></a>HvCallGetVpIndexFromApicId

Le HvCallGetVpIndexFromApicId permet à l’appelant de récupérer un index VP pour le VP avec l’ID de APID spécifié.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallGetVpIndexFromApicId(
    _In_ HV_PARTITION_ID PartitionId,
    _In_ HV_VTL TargetVtl,
    _Inout_ PUINT32 ApicIdCoount,
    _In_reads_(ApicIdCount) PHV_APIC_ID ApicIdList,
    _Out_writes(ApicIdCount) PHV_VP_INDEX VpIndexList
    );

 ```

## <a name="call-code"></a>Appeler du code

`0x009A` Attaché

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `PartitionId`           | 0          | 8        | Partition                                 |
| `TargetVtl`             | 8          | 1        | VTL cible                                |
| Remplissage                 | 9          | 7        |                                           |

## <a name="input-list-element"></a>Élément de liste d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `ApicId`                | 0          | 4        | ID APIC du VP                         |
| Remplissage                 | 4          | 4        |                                           |

## <a name="output-list-element"></a>Élément de liste de sortie

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `VpIndex`               | 0          | 4        | Index du VP avec l’ID APIC spécifié|
| Remplissage                 | 4          | 4        |                                           |

## <a name="return-values"></a>Valeurs de retour

| Code d’état                         | Condition d'erreur                                       |
|-------------------------------------|-------------------------------------------------------|
| `HV_STATUS_ACCESS_DENIED`           | Accès refusé                                         |
| `HV_STATUS_INVALID_PARAMETER`       | Un paramètre non valide a été spécifié                    |
| `HV_STATUS_INVALID_PARTITION_ID`    | L’ID de partition spécifié n’est pas valide.                |
| `HV_STATUS_INVALID_REGISTER_VALUE`  | La valeur de Registre fournie n’est pas valide.               |
| `HV_STATUS_INVALID_VP_STATE`        | Un processeur virtuel n’est pas dans l’état correct pour les performances de l’opération indiquée. |
| `HV_STATUS_INVALID_PARTITION_STATE` | La partition spécifiée n’est pas dans l’état « actif ». |
| `HV_STATUS_INVALID_VTL_STATE`       | L’état de la VTL est en conflit avec l’opération demandée. |