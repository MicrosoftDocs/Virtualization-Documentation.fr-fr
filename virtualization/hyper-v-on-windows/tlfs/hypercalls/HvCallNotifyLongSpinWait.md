---
title: HvCallNotifyLongSpinWait
description: HvCallNotifyLongSpinWait hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 468a1c941d547d3a1243ac67e6d63812341fe589
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120484"
---
# <a name="hvcallnotifylongspinwait"></a>HvCallNotifyLongSpinWait

L’hyperappel HvCallNotifyLongSpinWait est utilisé par un système d’exploitation invité pour notifier l’hyperviseur que le processeur virtuel appelant tente d’acquérir une ressource qui est potentiellement détenue par un autre processeur virtuel au sein de la même partition. Cet indicateur de planification améliore l’extensibilité des partitions avec plusieurs processeurs virtuels.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvNotifyLongSpinWait(
    _In_ UINT64 SpinCount
    );
 ```

## <a name="call-code"></a>Appeler du code

`0x0008` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `SpinCount`             | 0          | 4        | Spécifie le nombre cumulé de rotation de l’invité. |
| RsvdZ                   | 4          | 4        |                                           |

## <a name="return-values"></a>Valeurs de retour

Il n’y a pas d’état d’erreur pour cet hyperappel, seul HV_STATUS_SUCCESS est retourné, car il s’agit d’un hyperappel de Conseil.