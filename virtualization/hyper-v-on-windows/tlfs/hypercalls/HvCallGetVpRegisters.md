---
title: HvCallGetVpRegisters
description: HvCallGetVpRegisters hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: fbddc59feefb9025a4abf27f8ae55524f0adb30a
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120476"
---
# <a name="hvcallgetvpregisters"></a>HvCallGetVpRegisters

L’hyperappel HvCallGetVpRegisters lit l’état d’un processeur virtuel.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallGetVpRegisters(
    _In_ HV_PARTITION_ID PartitionId,
    _In_ HV_VP_INDEX VpIndex,
    _In_ HV_INPUT_VTL InputVtl,
    _Inout_ PUINT32 RegisterCount,
    _In_reads(RegisterCount) PCHV_REGISTER_NAME RegisterNameList,
    _In_writes(RegisterCount) PHV_REGISTER_VALUE RegisterValueList
    );
 ```

L’État est retourné sous la forme d’une série de valeurs de Registre, chacune correspondant à un nom de registre fourni comme entrée.

### <a name="restrictions"></a>Restrictions

- L’appelant doit être le parent de la partition spécifiée par PartitionId ou la partition spécifiée doit avoir la valeur « Self » et la partition doit avoir le privilège AccessVpRegisters.

## <a name="call-code"></a>Appeler du code

`0x0050` Attaché

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `PartitionId`           | 0          | 8        | Spécifie l’ID de partition.               |
| `VpIndex`               | 8          | 4        | Spécifie l’index du processeur virtuel. |
| `TargetVtl`             | 12         | 1        | spécifie la VTL cible.                 |
| RsvdZ                   | 13         | 3        |                                           |

## <a name="input-list-element"></a>Élément de liste d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `RegisterName`          | 0          | 4        | Spécifie le nom d’un registre à lire. |

## <a name="output-list-element"></a>Élément de liste de sortie

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `RegisterValue`         | 0          | 16       | Retourne la valeur du Registre spécifié. |

## <a name="see-also"></a>Voir aussi

[HV_REGISTER_NAME](../datatypes/HV_REGISTER_NAME.md)

[HV_REGISTER_VALUE](../datatypes/HV_REGISTER_VALUE.md)
