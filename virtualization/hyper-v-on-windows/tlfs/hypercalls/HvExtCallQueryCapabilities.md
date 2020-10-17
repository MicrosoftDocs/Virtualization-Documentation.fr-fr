---
title: HvExtCallQueryCapabilities
description: HvExtCallQueryCapabilities hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 5e1a896b22c5087d3cd9041224846b94557f5c10
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120600"
---
# <a name="hvextcallquerycapabilities"></a>HvExtCallQueryCapabilities

Cet hyperappel signale la disponibilité des hyperappels étendus.

La disponibilité de cet hyperappel doit être interrogée à l’aide de l’indicateur de privilège de partition EnableExtendedHypercalls.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvExtCallQueryCapabilities(
    _Out_ UINT64 Capabilities
    );
 ```

## <a name="call-code"></a>Appeler du code

`0x8001` Simple

## <a name="output-parameters"></a>Paramètres de sortie

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `Capabilities`          | 0          | 8        | Masque de masque des hyperappels étendus pris en charge                          |

Le masque de masque des hyperappels étendus pris en charge est au format suivant :

| bit     | Hypercall étendu                                          |
|---------|-------------------------------------------------------------|
| 0       | HvExtCallGetBootZeroedMemory                                |
| 1       | HvExtCallMemoryHeatHint                                     |
| 2       | HvExtCallEpfSetup                                           |
| 3       | HvExtCallSchedulerAssistSetup                               |
| 4       | HvExtCallMemoryHeatHintAsync                                |
| 63-5    | Réservé                                                    |
