---
title: HV_X64_TABLE_REGISTER
description: HV_X64_TABLE_REGISTER
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 1fddba94270b7c1591ac6f7aa92d3ce5e926fe1e
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120612"
---
# <a name="hv_x64_table_register"></a>HV_X64_TABLE_REGISTER

Les registres de table sont similaires aux registres de segment, mais ils n’ont aucun sélecteur ou attribut, et la limite est limitée à 16 bits.

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    UINT16 Pad[3];
    UINT16 Limit;
    UINT64 Base;
} HV_X64_TABLE_REGISTER;
 ```
