---
title: HV_X64_FP_REGISTER
description: HV_X64_FP_REGISTER
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 10a6c03c9ba9d3d985b2980f8d893556f76f9cae
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120632"
---
# <a name="hv_x64_fp_register"></a>HV_X64_FP_REGISTER

Les registres à virgule flottante sont encodés en tant que valeurs 80 bits.

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    UINT64 Mantissa;
    UINT64 BiasedExponent:15;
    UINT64 Sign:1;
    UINT64 Reserved:48;
} HV_X64_FP_REGISTER;
 ```
