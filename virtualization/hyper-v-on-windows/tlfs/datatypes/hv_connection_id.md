---
title: HV_CONNECTION_ID
description: HV_CONNECTION_ID
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: a3b1238c23c017dfb2fe756269cc2d59de1af5ff
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120576"
---
# <a name="hv_connection_id"></a>HV_CONNECTION_ID

Les connexions sont identifiées par des ID 32 bits. Les 8 bits de poids fort sont réservés et doivent être nuls. Tous les ID de connexion sont uniques au sein d’une partition.

## <a name="syntax"></a>Syntaxe

 ```c
typedef union
{
    UINT32 AsUInt32;
    struct
    {
        UINT32 Id:24;
        UINT32 Reserved:8;
    };
} HV_CONNECTION_ID;
 ```
