---
title: HV_SYNIC_EVENT_FLAGS
description: HV_SYNIC_EVENT_FLAGS
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 741c01718890d453017fc22470b6b0e96298b05a
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120651"
---
# <a name="hv_synic_event_flags"></a>HV_SYNIC_EVENT_FLAGS

Les indicateurs d’événement SynIC sont des tableaux de bits de taille fixe. Elles sont numérotées de telle sorte que le premier octet du tableau contient les indicateurs 0 à 7 (0 étant le bit le moins significatif) et que le deuxième octet du tableau contient les indicateurs 8 à 15 (8 étant le bit le moins significatif), et ainsi de suite.

## <a name="syntax"></a>Syntaxe

```c
#define HV_EVENT_FLAGS_COUNT (256 * 8)
#define HV_EVENT_FLAGS_BYTE_COUNT 256

typedef struct
{
    UINT8 Flags[HV_EVENT_FLAGS_BYTE_COUNT];
} HV_SYNIC_EVENT_FLAGS;
 ```
