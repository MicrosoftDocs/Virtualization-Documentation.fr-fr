---
title: HvCallSendSyntheticClusterIpiEx
description: HvCallSendSyntheticClusterIpiEx hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: e6f2f46892a1a56bf13520ed94b81f6fce0a217b
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120608"
---
# <a name="hvcallsendsyntheticclusteripiex"></a>HvCallSendSyntheticClusterIpiEx

Cet hyperappel envoie une interruption fixe virtuelle à l’ensemble de processeurs virtuels spécifié. Elle ne prend pas en charge NMIs. Cette version diffère de [HvCallSendSyntheticClusterIpi](HvCallSendSyntheticClusterIpi.md) dans la mesure où un VP de taille variable peut être spécifié.

Les vérifications suivantes doivent être utilisées pour déduire la disponibilité de cet hyperappel :

- ExProcessorMasks doit être indiqué via le 0x40000004 de la feuille CPUID.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallSendSyntheticClusterIpiEx(
    _In_ UINT32 Vector,
    _In_ HV_INPUT_VTL TargetVtl,
    _In_ HV_VP_SET ProcessorSet
    );
 ```

## <a name="call-code"></a>Appeler du code
`0x0015` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `Vector`                | 0          | 4        | A spécifié le vecteur déclaré. Doit être compris entre >= 0x10 et <= 0xFF.  |
| `TargetVtl`             | 4          | 1        | VTL cible                                |
| Remplissage                 | 5          | 3        |                                           |
| `ProcessorSet`          | 8          | Variable | Spécifie un jeu de processeurs représentant VPs à cibler.|

## <a name="see-also"></a>Voir aussi

[HV_VP_SET](../datatypes/HV_VP_SET.md)
