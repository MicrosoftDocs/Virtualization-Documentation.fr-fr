---
title: HV_DEVICE_INTERRUPT_TARGET
description: HV_DEVICE_INTERRUPT_TARGET
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 44d31155b667a1e1f0114720150717318ab52451
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120571"
---
# <a name="hv_device_interrupt_target"></a>HV_DEVICE_INTERRUPT_TARGET

## <a name="syntax"></a>Syntaxe

 ```c

#define HV_DEVICE_INTERRUPT_TARGET_MULTICAST 1
#define HV_DEVICE_INTERRUPT_TARGET_PROCESSOR_SET 2

typedef struct
{
    HV_INTERRUPT_VECTOR Vector;
    UINT32 Flags;
    union
    {
        UINT64 ProcessorMask;
        UINT64 ProcessorSet[];
    };
} HV_DEVICE_INTERRUPT_TARGET;
 ```

« Flags » fournit des indicateurs facultatifs pour la cible d’interruption. « Multicast » indique que l’interruption est envoyée à tous les processeurs dans le jeu de cibles. Par défaut, l’interruption est envoyée à un seul processeur arbitraire dans le jeu de cibles.
