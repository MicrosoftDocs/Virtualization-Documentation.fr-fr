---
title: HV_VP_SET
description: HV_VP_SET
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: af327161049650a95e39e9a658896d8e718cbd84
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120640"
---
# <a name="hv_vp_set"></a>HV_VP_SET

Un jeu de processeurs virtuels représente une collection de processeurs virtuels et peut être utilisé comme entrée pour certains hyperappels.

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    UINT64 Format;
    UINT64 ValidBanksMask;
    UINT64 BankContents[];
} HV_VP_SET;
 ```

Un jeu de processeurs a deux modes, qui sont spécifiés par le champ format. Les jeux de processeurs avec le format « 1 » représentent tous les processeurs virtuels pour la partition donnée. Les jeux de processeurs avec le format « 0 » décrivent un ensemble de processeurs virtuels épars.

| Valeur de format  | Définir le comportement                                                |
|---------------|-------------------------------------------------------------|
| 0             | Un sous-ensemble de VPs                                      |
| 1             | Toutes les VPs (appartenant à une partition)                          |

### <a name="sparse-virtual-processor-set"></a>Jeu de processeurs virtuels épars

La section suivante décrit comment construire un ensemble épars de processeurs virtuels.

Le jeu total de processeurs virtuels est divisé en segments de 64, appelé « Bank ». Par exemple, les processeurs 0-63 sont en banque 0, 64-127 sont dans la Banque 1, et ainsi de suite.

Pour décrire un processeur individuel, sa banque est spécifiée avec ValidBanksMask. Chaque bit de ValidBanksMask représente une banque particulière.

```
bank = VPindex / 64
```
Pour chaque bit défini avec ValidBanksMask, il doit y avoir un élément dans le tableau BanksContents. Cet élément est un masque décrivant la Banque.

Si un bit dans ValidBankMask a la valeur 0, il n’y a aucun élément correspondant dans BanksContents. En outre, pour un bit 1 dans ValidBankMask, il s’agit d’un état valide pour l’élément correspondant dans BanksContents peut être tous les 0, ce qui signifie qu’aucun processeur n’est spécifié dans cette banque.

#### <a name="processor-set-example"></a>Exemple de jeu de processeurs

Supposons qu’une partition comporte 200 VPs et que nous souhaitons spécifier l’ensemble suivant : {0, AMX5130}

Tout d’abord, le format est 0, puisqu’il s’agit d’un ensemble épars. Ensuite, les banques correspondantes (et par conséquent, le jeu de bits de ValidBanksMask) sont {0, 0, 2}. Par conséquent, ValidBanksMask est 0x05.

La Banque 0 définit les bits 0 et 5 pour spécifier les VPs au sein de cette banque. Par conséquent, l’élément correspondant dans le masque BankContents est 0x21.

Étant donné que le bit 1 n’est pas défini dans ValidBanksMask, il n’existe aucun élément correspondant dans BankContents. La Banque 2 représente les indices de VP 128-191. Pour décrire l’index 130, le bit 2 du masque correspondant est défini. Par conséquent, BankContents est : {0x21, 0x04}.