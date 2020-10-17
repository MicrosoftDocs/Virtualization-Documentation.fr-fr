---
title: HvCallEnablePartitionVtl
description: HvCallEnablePartitionVtl hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: fde108fff5a7b7ca9867234b8e8fcceb5a98722f
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120531"
---
# <a name="hvcallenablepartitionvtl"></a>HvCallEnablePartitionVtl

L’hyperappel HvCallEnablePartitionVtl active un niveau de confiance virtuelle pour une partition spécifiée. Elle doit être utilisée conjointement avec HvCallEnableVpVtl pour initier et utiliser une nouvelle VTL.

## <a name="interface"></a>Interface

 ```c

typedef union
{
    UINT8 AsUINT8;
    struct {
        UINT8 EnableMbec:1;
        UINT8 Reserved:7;
    };
} HV_ENABLE_PARTITION_VTL_FLAGS;

HV_STATUS
HvCallEnablePartitionVtl(
    _In_ HV_PARTITION_ID TargetPartitionId,
    _In_ HV_VTL TargetVtl,
    _In_ HV_ENABLE_PARTITION_VTL_FLAGS Flags
    );
 ```

### <a name="restrictions"></a>Restrictions

- Une VTL de lancement peut toujours activer une VTL cible si la VTL cible est inférieure à la VTL de lancement.
- Une VTL de lancement peut activer une VTL cible plus élevée si la VTL de lancement est la VTL la plus élevée activée pour la partition qui est inférieure à la VTL cible.

## <a name="call-code"></a>Appeler du code

`0x000D` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `TargetPartitionId`     | 0          | 8        | Fournit l’ID de partition de la partition pour laquelle cette demande est destinée. |
| `TargetVtl`             | 8          | 1        | Spécifie la VTL à activer par cet hyperappel. |
| `Flags`                 | 9          | 1        | Spécifie un masque pour activer les fonctionnalités liées à VSM.|
| RsvdZ                   | 10         | 6        |                                           |