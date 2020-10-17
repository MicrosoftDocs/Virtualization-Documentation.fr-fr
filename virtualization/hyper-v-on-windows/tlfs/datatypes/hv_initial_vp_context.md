---
title: HV_INITIAL_VP_CONTEXT
description: HV_INITIAL_VP_CONTEXT
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 2e347d72d6f41544e2bc5d410ecfecb9dd809e80
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120688"
---
# <a name="hv_initial_vp_context"></a>HV_INITIAL_VP_CONTEXT

## <a name="syntax"></a>Syntaxe

 ```c
typedef struct
{
    UINT64 Rip;
    UINT64 Rsp;
    UINT64 Rflags;

    // Segment selector registers together with their hidden state.
    HV_X64_SEGMENT_REGISTER Cs;
    HV_X64_SEGMENT_REGISTER Ds;
    HV_X64_SEGMENT_REGISTER Es;
    HV_X64_SEGMENT_REGISTER Fs;
    HV_X64_SEGMENT_REGISTER Gs;
    HV_X64_SEGMENT_REGISTER Ss;
    HV_X64_SEGMENT_REGISTER Tr;
    HV_X64_SEGMENT_REGISTER Ldtr;

    // Global and Interrupt Descriptor tables
    HV_X64_TABLE_REGISTER Idtr;
    HV_X64_TABLE_REGISTER Gdtr;

    // Control registers and MSR's
    UINT64 Efer;
    UINT64 Cr0;
    UINT64 Cr3;
    UINT64 Cr4;
    UINT64 MsrCrPat;
} HV_INITIAL_VP_CONTEXT;
 ```

## <a name="see-also"></a>Voir aussi

[HV_X64_SEGMENT_REGISTER](HV_X64_SEGMENT_REGISTER.md)

[HV_X64_TABLE_REGISTER](HV_X64_TABLE_REGISTER.md)
