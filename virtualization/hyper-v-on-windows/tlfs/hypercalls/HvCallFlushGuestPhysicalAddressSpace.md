---
title: HvCallFlushGuestPhysicalAddressSpace
description: HvCallFlushGuestPhysicalAddressSpace hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 7c37a08bea56cce7460c89b2625ca9cc3e4239aa
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120532"
---
# <a name="hvcallflushguestphysicaladdressspace"></a>HvCallFlushGuestPhysicalAddressSpace

L’hyperappel HvCallFlushGuestPhysicalAddressSpace invalide les mappages de niveau 2 de niveau 2 mis en cache dans un espace d’adressage de deuxième niveau.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallFlushGuestPhysicalAddressSpace(
    _In_ HV_SPA AddressSpace,
    _In_ UINT64 Flags
    );
 ```

Cet hyperappel peut uniquement être utilisé avec la virtualisation imbriquée est active. L’opération d’invalidation du TLB virtuel agit sur tous les processeurs.

Sur les plates-formes Intel, l’hyperappel HvCallFlushGuestPhysicalAddressSpace est similaire à l’exécution d’une instruction INVEPT de type « simple-Context » sur tous les processeurs.

Cet appel garantit que, lorsque le contrôle de temps retourne à l’appelant, les effets observables de tous les vidages se sont produits.
Si le TLB est actuellement « verrouillé », le processeur virtuel de l’appelant est suspendu.

## <a name="call-code"></a>Appeler du code

`0x00AF` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `AddressSpace`          | 0          | 8        | Spécifie un ID d’espace d’adresse (pointeur de table EPT PML4). |
| `Flags`                 | 8          | 8        | RsvdZ                                     |