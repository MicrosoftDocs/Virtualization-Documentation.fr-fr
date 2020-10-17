---
title: HvCallSetVpRegisters
description: HvCallSetVpRegisters hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 82b5aa39c1cf2d9e1372e9fba6c86579b11e15bf
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120708"
---
# <a name="hvcallsetvpregisters"></a>HvCallSetVpRegisters

L’hyperappel HvCallSetVpRegisters écrit l’état d’un processeur virtuel.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallSetVpRegisters(
    _In_ HV_PARTITION_ID PartitionId,
    _In_ HV_VP_INDEX VpIndex,
    _In_ HV_INPUT_VTL InputVtl,
    _Inout_ PUINT32 RegisterCount,
    _In_reads(RegisterCount) PCHV_REGISTER_NAME RegisterNameList,
    _In_reads(RegisterCount) PCHV_REGISTER_VALUE RegisterValueList
    );
 ```

L’État est écrit sous la forme d’une série de valeurs de Registre, chacune correspondant à un nom de registre fourni comme entrée.

Une vérification minimale des erreurs est effectuée quand une valeur de Registre est modifiée. En particulier, l’hyperviseur valide que les bits réservés d’un registre sont définis sur zéro, les bits qui sont définis de manière architecturale comme contenant toujours un zéro ou un qui est correctement défini, et les bits spécifiés au-delà de la taille architecturale du Registre sont mis à zéro.

Cet appel ne peut pas être utilisé pour modifier la valeur d’un registre en lecture seule.

Les effets secondaires de la modification d’un registre ne sont pas effectués. Cela comprend la génération d’exceptions, les synchronisations de pipeline, les vidages TLB, etc.

### <a name="restrictions"></a>Restrictions

- L’appelant doit être le parent de la partition spécifiée par PartitionId ou la partition spécifiée doit avoir la valeur « Self » et la partition doit avoir le privilège AccessVpRegisters.

## <a name="call-code"></a>Appeler du code

`0x0051` Attaché

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
| `RegisterName`          | 0          | 4        | Spécifie le nom d’un registre à modifier. |
| RsvdZ                   | 4          | 12       |                                           |
| `RegisterValue`         | 16         | 16       | Spécifie la nouvelle valeur pour le registre spécifié. |

## <a name="see-also"></a>Voir aussi

[HV_REGISTER_NAME](../datatypes/HV_REGISTER_NAME.md)

[HV_REGISTER_VALUE](../datatypes/HV_REGISTER_VALUE.md)
