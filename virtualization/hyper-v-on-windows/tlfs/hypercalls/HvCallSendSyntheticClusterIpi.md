---
title: HvCallSendSyntheticClusterIpi
description: HvCallSendSyntheticClusterIpi hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 21d7a7f5f43123b984e64d76008978e152e4a8a6
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120479"
---
# <a name="hvcallsendsyntheticclusteripi"></a>HvCallSendSyntheticClusterIpi

Cet hyperappel envoie une interruption fixe virtuelle à l’ensemble de processeurs virtuels spécifié. Elle ne prend pas en charge NMIs.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallSendSyntheticClusterIpi(
    _In_ UINT32 Vector,
    _In_ HV_INPUT_VTL TargetVtl,
    _In_ UINT64 ProcessorMask
    );
 ```

## <a name="call-code"></a>Appeler du code
`0x000b` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `Vector`                | 0          | 4        | A spécifié le vecteur déclaré. Doit être compris entre >= 0x10 et <= 0xFF.  |
| `TargetVtl`             | 4          | 1        | VTL cible                                |
| Remplissage                 | 5          | 3        |                                           |
| `ProcessorMask`         | 8          | 1        | Spécifie un masque représentant VPs à cibler|
