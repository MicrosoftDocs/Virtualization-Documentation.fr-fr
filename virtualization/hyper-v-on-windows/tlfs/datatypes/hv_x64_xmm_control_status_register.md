---
title: HV_X64_XMM_CONTROL_STATUS_REGISTER
description: HV_X64_XMM_CONTROL_STATUS_REGISTER
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: aabe46eadd52e57adc297f7ca54bb6e6785b0a7f
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120611"
---
# <a name="hv_x64_xmm_control_status_register"></a>HV_X64_XMM_CONTROL_STATUS_REGISTER

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    union
    {
        UINT64 LastFpRdp;
        struct
        {
            UINT32 LastFpDp;
            UINT16 LastFpDs;
        };
    };

    UINT32 XmmStatusControl;
    UINT32 XmmStatusControlMask;
} HV_X64_XMM_CONTROL_STATUS_REGISTER;
 ```
