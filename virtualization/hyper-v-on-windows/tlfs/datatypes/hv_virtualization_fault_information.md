---
title: HV_VIRTUALIZATION_FAULT_INFORMATION
description: HV_VIRTUALIZATION_FAULT_INFORMATION
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 503b11383bf02f541e253f673a412254c9cbbf72
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120644"
---
# <a name="hv_virtualization_fault_information"></a>HV_VIRTUALIZATION_FAULT_INFORMATION

La zone d’informations sur les erreurs de virtualisation contient le code d’erreur et les paramètres d’erreur actuels pour le VP. Elle est alignée sur 16 octets.

## <a name="syntax"></a>Syntaxe

```c
typedef union
{
    struct
    {
        UINT16 Parameter0;
        UINT16 Reserved0;
        UINT32 Code;
        UINT64 Parameter1;
    };
} HV_VIRTUALIZATION_FAULT_INFORMATION, *PHV_VIRTUALIZATION_FAULT_INFORMATION;
 ```

## <a name="see-also"></a>Voir aussi

 [HV_VP_ASSIST_PAGE](HV_VP_ASSIST_PAGE.md)