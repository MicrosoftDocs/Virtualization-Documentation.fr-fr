---
title: HV_X64_FP_CONTROL_STATUS_REGISTER
description: HV_X64_FP_CONTROL_STATUS_REGISTER
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 0a8718588414147bf351aea7df42e9df95a6318e
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120636"
---
# <a name="hv_x64_fp_control_status_register"></a>HV_X64_FP_CONTROL_STATUS_REGISTER

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    UINT16 FpControl;
    UINT16 FpStatus;
    UINT8 FpTag;
    UINT8 Reserved:8;
    UINT16 LastFpOp;
    union {
        UINT64 LastFpRip;
        struct {
            UINT32 LastFpEip;
            UINT16 LastFpCs;
        };
     };
 } HV_X64_FP_CONTROL_STATUS_REGISTER;
 ```
