---
title: HV_VP_ASSIST_PAGE
description: HV_VP_ASSIST_PAGE
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 69a936c21d24cd88225c4ee958e72f2f872443c4
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120643"
---
# <a name="hv_vp_assist_page"></a>HV_VP_ASSIST_PAGE

 Cette structure définit le format de la [page d’assistance du processeur virtuel](../vp-properties.md#virtual-processor-assist-page).

## <a name="syntax"></a>Syntaxe

```c
typedef union
{
    struct
    {
        //
        // APIC assist for optimized EOI processing.
        //
        HV_VIRTUAL_APIC_ASSIST ApicAssist;
        UINT32 ReservedZ0;

        //
        // VP-VTL control information
        //
        HV_VP_VTL_CONTROL VtlControl;

        HV_NESTED_ENLIGHTENMENTS_CONTROL NestedEnlightenmentsControl;
        BOOLEAN EnlightenVmEntry;
        UINT8 ReservedZ1[7];
        HV_GPA CurrentNestedVmcs;

        BOOLEAN SyntheticTimeUnhaltedTimerExpired;
        UINT8 ReservedZ2[7];

        //
        // VirtualizationFaultInformation must be 16 byte aligned.
        //
        HV_VIRTUALIZATION_FAULT_INFORMATION VirtualizationFaultInformation;
    };

    UINT8 ReservedZBytePadding[HV_PAGE_SIZE];

} HV_VP_ASSIST_PAGE, *PHV_VP_ASSIST_PAGE;
 ```

## <a name="see-also"></a>Voir aussi

[HV_NESTED_ENLIGHTENMENTS_CONTROL](HV_NESTED_ENLIGHTENMENTS_CONTROL.md)

[HV_VIRTUALIZATION_FAULT_INFORMATION](HV_VIRTUALIZATION_FAULT_INFORMATION.md)

[HV_VP_VTL_CONTROL](HV_VP_VTL_CONTROL.md)