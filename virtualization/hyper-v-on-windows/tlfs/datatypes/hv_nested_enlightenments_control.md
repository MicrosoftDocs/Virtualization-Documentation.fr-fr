---
title: HV_NESTED_ENLIGHTENMENTS_CONTROL
description: HV_NESTED_ENLIGHTENMENTS_CONTROL
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 71e4871aaa3cd50fac9e4b5e212ca029b180f157
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120676"
---
# <a name="hv_nested_enlightenments_control"></a>HV_NESTED_ENLIGHTENMENTS_CONTROL

Structure de contrôle qui permet à un hyperviseur d’indiquer à son hyperviseur parent quels privilèges d’autorisation imbriqués doivent être accordés au contexte d’invité imbriqué actuel.

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    union {
        UINT32 AsUINT32;
        struct
        {
            UINT32 DirectHypercall:1;
            UINT32 VirtualizationException:1;
            UINT32 Reserved:30;
        };
    } Features;

    union
    {
        UINT32 AsUINT32;
        struct
        {
            UINT32 InterPartitionCommunication:1;
            UINT32 Reserved:31;
        };
     } HypercallControls;
} HV_NESTED_ENLIGHTENMENTS_CONTROL, *PHV_NESTED_ENLIGHTENMENTS_CONTROL;
 ```

## <a name="see-also"></a>Voir aussi

[HV_VP_ASSIST_PAGE](HV_VP_ASSIST_PAGE.md)