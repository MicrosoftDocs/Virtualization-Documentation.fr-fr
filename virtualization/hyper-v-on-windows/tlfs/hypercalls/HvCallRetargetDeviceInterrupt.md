---
title: HvCallRetargetDeviceInterrupt
description: HvCallRetargetDeviceInterrupt hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: df12f74a0eb34947e20afb61264c2434689b052f
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120468"
---
# <a name="hvcallretargetdeviceinterrupt"></a>HvCallRetargetDeviceInterrupt

Cet hyperappel recible une interruption d’appareil, ce qui peut être utile pour rééquilibrer les IRQ au sein d’un invité.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvRetargetDeviceInterrupt(
    _In_ HV_PARTITION_ID PartitionId,
    _In_ UINT64 DeviceId,
    _In_ HV_INTERRUPT_ENTRY InterruptEntry,
    _In_ UINT64 Reserved,
    _In_ HV_DEVICE_INTERRUPT_TARGET InterruptTarget
    );
 ```

## <a name="call-code"></a>Appeler du code
`0x007e` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `PartitionId`           | 0          | 8        | ID de partition (peut être HV_PARTITION_SELF)   |
| `DeviceId`              | 8          | 6        | Fournit l’ID d’appareil logique unique (dans un invité) qui est affecté par l’hôte.   |
| RsvdZ                   | 32         | 8        | Réservé                                  |
| `InterruptEntry`        | 16         | 16       | Fournit l’adresse MSI et les données qui identifient l’interruption. |
| `InterruptTarget`       | 40         | 16       | Spécifie un masque représentant VPs à cibler|

## <a name="see-also"></a>Voir aussi

[HV_INTERRUPT_ENTRY](../datatypes/HV_INTERRUPT_ENTRY.md)

[HV_DEVICE_INTERRUPT_TARGET](../datatypes/HV_DEVICE_INTERRUPT_TARGET.md)
