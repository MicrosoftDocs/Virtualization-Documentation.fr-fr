---
title: HV_X64_MSR_NPIEP_CONFIG_CONTENTS
description: HV_X64_MSR_NPIEP_CONFIG_CONTENTS
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 3bbf267f7480387bd64a70a1227afc456c82c33b
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120635"
---
# <a name="hv_x64_msr_npiep_config_contents"></a>HV_X64_MSR_NPIEP_CONFIG_CONTENTS

## <a name="syntax"></a>Syntaxe

```c
union
{
    UINT64 AsUINT64;
    struct
    {
        // These bits enable instruction execution prevention for specific
        // instructions.

        UINT64 PreventSgdt:1;
        UINT64 PreventSidt:1;
        UINT64 PreventSldt:1;
        UINT64 PreventStr:1;
        UINT64 Reserved:60;
    };
} HV_X64_MSR_NPIEP_CONFIG_CONTENTS;
 ```
