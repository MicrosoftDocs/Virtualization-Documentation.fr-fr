---
title: HvCallFlushVirtualAddressSpace
description: HvCallFlushVirtualAddressSpace hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: ce0c0a4f0f5879fb43cf173c51882c63e733c747
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120514"
---
# <a name="hvcallflushvirtualaddressspace"></a>HvCallFlushVirtualAddressSpace

L’hyperappel HvCallFlushVirtualAddressSpace invalide toutes les entrées TLB virtuelles qui appartiennent à un espace d’adressage spécifié.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallFlushVirtualAddressSpace(
    _In_ HV_ADDRESS_SPACE_ID AddressSpace,
    _In_ HV_FLUSH_FLAGS Flags,
    _In_ UINT64 ProcessorMask
    );
 ```

L’opération d’invalidation du TLB virtuel agit sur un ou plusieurs processeurs.

Si l’invité a des connaissances sur les processeurs qui devront peut-être être vidés, il peut spécifier un masque de processeur. Chaque bit du masque correspond à un index de processeur virtuel. Par exemple, un masque de 0x0000000000000051 indique que l’hyperviseur doit vider uniquement le TLB des processeurs virtuels 0, 4 et 6. Un processeur virtuel peut déterminer son index en lisant à partir de la HV_X64_MSR_VP_INDEX MSR.

Les indicateurs suivants peuvent être utilisés pour modifier le comportement du vidage :

- HV_FLUSH_ALL_PROCESSORS indique que l’opération doit s’appliquer à tous les processeurs virtuels au sein de la partition. Si cet indicateur est défini, le paramètre ProcessorMask est ignoré.
- HV_FLUSH_ALL_VIRTUAL_ADDRESS_SPACES indique que l’opération doit s’appliquer à tous les espaces d’adressage virtuels. Si cet indicateur est défini, le paramètre AddressSpace est ignoré.
- HV_FLUSH_NON_GLOBAL_MAPPINGS_ONLY indique que l’hyperviseur est requis uniquement pour vider les mappages de page qui n’ont pas été mappés en tant que « global » (autrement dit, le bit « G » x64 a été défini dans l’entrée de la table de pages). Les entrées globales peuvent être (mais n’ont pas besoin d’être) laissées non vidées par l’hyperviseur.

Tous les autres indicateurs sont réservés et doivent être définis sur zéro.

Cet appel garantit que le contrôle de temps revient à l’appelant, les effets observables de tous les vidages sur les processeurs virtuels spécifiés se sont produits.

Si le TLB d’un processeur virtuel cible requiert un vidage et que le TLB de ce processeur virtuel est actuellement « verrouillé », le processeur virtuel de l’appelant est suspendu. Lorsque le processeur virtuel de l’appelant est « Unsuspended », l’hyperappel est réémis.

## <a name="call-code"></a>Appeler du code
`0x0002` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `AddressSpace`          | 0          | 8        | Spécifie un ID d’espace d’adresse (valeur CR3). |
| `Flags`                 | 8          | 8        | Ensemble de bits d’indicateur qui modifient le fonctionnement du vidage. |
| `ProcessorMask`         | 16         | 8        | Masque de processeur indiquant les processeurs qui doivent être affectés par l’opération de vidage. |
