---
title: HvCallEnableVpVtl
description: HvCallEnableVpVtl hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 0be265f26c7c0e3ee844505eee4e8743ec01faaf
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120711"
---
# <a name="hvcallenablevpvtl"></a>HvCallEnableVpVtl

HvCallEnableVpVtl permet à une VTL de s’exécuter sur un VP. Cet hyperappel doit être utilisé conjointement avec HvCallEnablePartitionVtl pour activer et utiliser une VTL. Pour activer une VTL sur un VP, vous devez d’abord l’activer pour la partition. Cet appel ne modifie pas la VTL active.

## <a name="interface"></a>Interface

 ```c

HV_STATUS
HvEnableVpVtl(
    _In_ HV_PARTITION_ID TargetPartitionId,
    _In_ HV_VP_INDEX VpIndex,
    _In_ HV_VTL TargetVtl,
    _In_ HV_INITIAL_VP_CONTEXT VpVtlContext
    );
 ```

### <a name="restrictions"></a>Restrictions

En général, une VTL ne peut être activée que par une VTL plus élevée. Il existe une exception à cette règle : la VTL la plus élevée activée pour une partition peut activer une VTL cible plus élevée.

Une fois que la VTL cible est activée sur un VP, tous les autres appels pour activer la VTL doivent provenir d’une VTL égale ou supérieure.
Cet hyperappel échouera s’il est appelé pour activer une VTL déjà activée pour un VP.

## <a name="call-code"></a>Appeler du code
`0x000F` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `TargetPartitionId`     | 0          | 8        | Fournit l’ID de partition de la partition pour laquelle cette demande est destinée. |
| `VpIndex`               | 8          | 4        | Spécifie l’index du processeur virtuel sur lequel activer la VTL. |
| `TargetVtl`             | 12         | 1        | Spécifie la VTL à activer par cet hyperappel. |
| RsvdZ                   | 13         | 3        |                                           |
| `VpVtlContext`          | 16         | 224      | Spécifie le contexte initial dans lequel le VP doit démarrer sur la première entrée de la VTL cible. |

## <a name="see-also"></a>Voir aussi

[HV_INITIAL_VP_CONTEXT](../datatypes/HV_INITIAL_VP_CONTEXT.md)