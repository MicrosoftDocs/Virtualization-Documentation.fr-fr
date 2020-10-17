---
title: HvCallModifyVtlProtectionMask
description: HvCallModifyVtlProtectionMask hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: a88dacf32a57eeb98c7971b5fd752dfe6562e1c8
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120475"
---
# <a name="hvcallmodifyvtlprotectionmask"></a>HvCallModifyVtlProtectionMask

L’hyperappel HvCallModifyVtlProtectionMask modifie les protections de VTL appliquées à un ensemble existant de pages d’amp.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvModifyVtlProtectionMask(
    _In_ HV_PARTITION_ID TargetPartitionId,
    _In_ HV_MAP_GPA_FLAGS MapFlags,
    _In_ HV_INPUT_VTL TargetVtl,
    _In_reads(PageCount) HV_GPA_PAGE_NUMBER GpaPageList
    );
 ```

Une VTL peut uniquement placer des protections sur une VTL inférieure.

Toute tentative d’appliquer des protections de VTL sur des plages non-RAM échouera avec HV_STATUS_INVALID_PARAMETER.

## <a name="call-code"></a>Appeler du code

`0x000C` Attaché

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `TargetPartitionId`     | 0          | 8        | Fournit l’ID de partition de la partition pour laquelle cette demande est destinée. |
| `MapFlags`              | 8          | 4        | Spécifie les nouveaux indicateurs de mappage à appliquer. |
| `TargetVtl`             | 12         | 1        | A spécifié la VTL cible.                 |
| RsvdZ                   | 13         | 3        |                                           |

## <a name="input-list-element"></a>Élément de liste d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `GpaPageList`           | 0          | 8        | Fournit les pages à protéger.       |