---
title: HV_X64_SEGMENT_REGISTER
description: HV_X64_SEGMENT_REGISTER
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 6c661e3ce8314d6aeaf94286b0bba190a78f629a
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120615"
---
# <a name="hv_x64_segment_register"></a>HV_X64_SEGMENT_REGISTER

L’état du registre de segment est encodé comme suit :

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    UINT64 Base;
    UINT32 Limit;
    UINT16 Selector;
    union
    {
        struct
        {
            UINT16 SegmentType:4;
            UINT16 NonSystemSegment:1;
            UINT16 DescriptorPrivilegeLevel:2;
            UINT16 Present:1;
            UINT16 Reserved:4;
            UINT16 Available:1;
            UINT16 Long:1;
            UINT16 Default:1;
            UINT16 Granularity:1;
        };

        UINT16 Attributes;
    };
} HV_X64_SEGMENT_REGISTER;
 ```

La limite est encodée sous la forme d’une valeur 32 bits. Pour les segments x64 en mode long, la limite est ignorée. Pour les segments x86 hérités, la limite doit être exprimable dans les limites de l’architecture du processeur x64. Par exemple, si le bit « G » (granularité) est défini dans les attributs d’un segment de code ou de données, les 12 bits de poids faible de la limite doivent être des 1s.

Le bit « present » contrôle si le segment agit comme un segment null (autrement dit, si un accès mémoire effectué via ce segment génère une erreur #GP).

Le IA32_FS_BASE et les IA32_GS_BASE MSRs ne sont pas définis dans la liste de registres, car ils sont des alias de l’élément de base de la structure du registre des segments. Utilisez HvX64RegisterFs et HvX64RegisterGs et la structure ci-dessus à la place.