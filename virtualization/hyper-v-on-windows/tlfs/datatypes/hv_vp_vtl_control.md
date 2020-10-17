---
title: HV_VP_VTL_CONTROL
description: HV_VP_VTL_CONTROL
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 2d307b85e073cc0d8e29d45ef8fdf3f5371de05c
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120639"
---
# <a name="hv_vp_vtl_control"></a>HV_VP_VTL_CONTROL

L’hyperviseur utilise une partie de la page d’assistance du VP pour faciliter la communication avec le code qui s’exécute dans une VTL supérieure à VTL0. Chaque VTL possède sa propre structure de contrôle (à l’exception de VTL0).

Les informations suivantes sont communiquées à l’aide de la page de contrôle :

1. Motif de l’entrée de la VTL.
2. Indicateur qui spécifie que VINA est déclaré.
3. Valeurs des registres à charger sur une VTL.

## <a name="syntax"></a>Syntaxe

```c
typedef enum
{
    // This reason is reserved and is not used.
    HvVtlEntryReserved = 0,

    // Indicates entry due to a VTL call from a lower VTL.
    HvVtlEntryVtlCall = 1,

    // Indicates entry due to an interrupt targeted to the VTL.
    HvVtlEntryInterrupt = 2
} HV_VTL_ENTRY_REASON;

typedef struct
{
    // The hypervisor updates the entry reason with an indication as to why
    // the VTL was entered on the virtual processor.
    HV_VTL_ENTRY_REASON EntryReason;

    // This flag determines whether the VINA interrupt line is asserted.
    union
    {
        UINT8 AsUINT8;
        struct
        {
            UINT8 VinaAsserted :1;
            UINT8 VinaReservedZ :7;
        };
    } VinaStatus;

    UINT8 ReservedZ00;
    UINT16 ReservedZ01;

    // A guest updates the VtlReturn* fields to provide the register values
    // to restore on VTL return. The specific register values that are
    // restored will vary based on whether the VTL is 32-bit or 64-bit.
    union
    {
        struct
        {
            UINT64 VtlReturnX64Rax;
            UINT64 VtlReturnX64Rcx;
        };

        struct
        {
            UINT32 VtlReturnX86Eax;
            UINT32 VtlReturnX86Ecx;
            UINT32 VtlReturnX86Edx;
            UINT32 ReservedZ1;
        };
    };
} HV_VP_VTL_CONTROL;
 ```

## <a name="see-also"></a>Voir aussi

[HV_VP_ASSIST_PAGE](HV_VP_ASSIST_PAGE.md)
