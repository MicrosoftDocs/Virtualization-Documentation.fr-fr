---
title: HV_CRASH_CTL_REG_CONTENTS
description: HV_CRASH_CTL_REG_CONTENTS
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: fe106c7998d5851c813e3a3a7c493c9c5aec550a
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120575"
---
# <a name="hv_crash_ctl_reg_contents"></a>HV_CRASH_CTL_REG_CONTENTS

La structure de données suivante est utilisée pour définir le contenu du registre de contrôle d’inversion des incidents invités (HV_X64_MSR_CRASH_CTL).

## <a name="syntax"></a>Syntaxe

 ```c
typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 Reserved : 62;         // Reserved bits
        UINT64 CrashMessage : 1;      // P3 is the PA of the message
                                      // P4 is the length in bytes
        UINT64 CrashNotify : 1;       // Log contents of crash parameter
    };
} HV_CRASH_CTL_REG_CONTENTS;
 ```

## <a name="see-also"></a>Voir aussi

 [Inversion des pannes de partition](../partition-properties.md#partition-crash-enlightenment)