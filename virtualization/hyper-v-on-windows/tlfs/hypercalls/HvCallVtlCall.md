---
title: HvCallVtlCall
description: HvCallVtlCall hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: c2481cc0da0b185c45a0285f2cd0394dff7af9c5
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120472"
---
# <a name="hvcallvtlcall"></a>HvCallVtlCall

HvCallVtlCall lance un «[appel de VTL](../vsm.md#vtl-call)» et bascule vers la VTL la plus élevée suivante activée sur le VP.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallVtlCall(
    VOID
    );
 ```

## <a name="call-code"></a>Appeler du code

`0x0011` Simple