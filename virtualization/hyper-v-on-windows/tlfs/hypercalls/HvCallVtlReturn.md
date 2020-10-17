---
title: HvCallVtlReturn
description: HvCallVtlReturn hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 7070443a4e41c7059980196f47fc8d31d6f5ced3
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120603"
---
# <a name="hvcallvtlreturn"></a>HvCallVtlReturn

HvCallVtlReturn lance un «[retour VTL](../vsm.md#vtl-return)» et bascule vers la VTL la plus basse suivante activée sur le VP.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallVtlReturn(
    VOID
    );
 ```

## <a name="call-code"></a>Appeler du code

`0x0012` Simple