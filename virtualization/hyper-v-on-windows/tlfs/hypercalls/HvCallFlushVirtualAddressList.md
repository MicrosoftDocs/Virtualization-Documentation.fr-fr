---
title: HvFlushVirtualAddressList
description: HvFlushVirtualAddressList hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: af8f736dbe6926c5712a26cf6978ae0fe020b89d
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120526"
---
# <a name="hvflushvirtualaddresslist"></a>HvFlushVirtualAddressList

L’hyperappel HvCallFlushVirtualAddressList invalide des parties du TLB virtuel qui appartiennent à un espace d’adressage spécifié.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallFlushVirtualAddressList(
    _In_ HV_ADDRESS_SPACE_ID AddressSpace,
    _In_ HV_FLUSH_FLAGS Flags,
    _In_ UINT64 ProcessorMask,
    _Inout_ PUINT32 GvaCount,
    _In_reads_(GvaCount) PCHV_GVA GvaRangeList
    );
 ```

L’opération d’invalidation du TLB virtuel agit sur un ou plusieurs processeurs.

Si l’invité a des connaissances sur les processeurs qui devront peut-être être vidés, il peut spécifier un masque de processeur. Chaque bit du masque correspond à un index de processeur virtuel. Par exemple, un masque de 0x0000000000000051 indique que l’hyperviseur doit vider uniquement le TLB des processeurs virtuels 0, 4 et 6.

Les indicateurs suivants peuvent être utilisés pour modifier le comportement du vidage :

- HV_FLUSH_ALL_PROCESSORS indique que l’opération doit s’appliquer à tous les processeurs virtuels au sein de la partition. Si cet indicateur est défini, le paramètre ProcessorMask est ignoré.
- HV_FLUSH_ALL_VIRTUAL_ADDRESS_SPACES indique que l’opération doit s’appliquer à tous les espaces d’adressage virtuels. Si cet indicateur est défini, le paramètre AddressSpace est ignoré.
- HV_FLUSH_NON_GLOBAL_MAPPINGS_ONLY n’a pas de sens pour cet appel et est traité comme une option non valide.

Tous les autres indicateurs sont réservés et doivent être définis sur zéro.

Cet appel prend une liste de plages GVA. Chaque plage a un GVA de base. Étant donné que les vidages sont effectués avec une granularité de page, les 12 bits inférieurs du GVA peuvent être utilisés pour définir une longueur de plage. Ces bits encodent le nombre de pages supplémentaires (au-delà de la page initiale) dans la plage. Cela permet à chaque entrée d’encoder une plage de 1 à 4096 pages.

Un GVA qui se trouve dans un mappage de « grande page » (2 Mo ou 4 Mo) entraîne le vidage de la grande page entière à partir du TLB virtuel.

Cet appel garantit que le contrôle de temps revient à l’appelant, les effets observables de tous les vidages sur les processeurs virtuels spécifiés se sont produits.

Les GVAs non valides (ceux qui spécifient des adresses au-delà de la fin de l’espace GVA de la partition) sont ignorés.

Si le TLB d’un processeur virtuel cible requiert un vidage et que ce processeur virtuel empêche les vidages TLB, le processeur virtuel de l’appelant est suspendu. Lorsque les vidages TLB ne sont plus bloqués, le processeur virtuel est « non suspendu » et l’hyperappel est émis à l’issue.

## <a name="call-code"></a>Appeler du code
`0x0003` Attaché

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `AddressSpace`          | 0          | 8        | Spécifie un ID d’espace d’adresse (valeur CR3). |
| `Flags`                 | 8          | 8        | Ensemble de bits d’indicateur qui modifient le fonctionnement du vidage. |
| `ProcessorMask`         | 16         | 8        | Masque de processeur indiquant les processeurs qui doivent être affectés par l’opération de vidage. |

## <a name="input-list-element"></a>Élément de liste d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `GvaRange`              | 0          | 8        | Plage GVA                                 |
