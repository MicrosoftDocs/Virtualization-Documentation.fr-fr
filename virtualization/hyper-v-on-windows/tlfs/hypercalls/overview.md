---
title: Référence hypercall
description: Liste des hyperappels pris en charge
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: d40fc621374d7c3dacf616c01f1ea1efb3f08364
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120580"
---
# <a name="hypercall-reference"></a>Référence hypercall

Le tableau suivant répertorie les hyperappels pris en charge par l’appel de code.

| Appeler du code | Type    | Hypercall                                                                           |
|-----------|---------|-------------------------------------------------------------------------------------|
| 0x0001    | Simple  | HvCallSwitchVirtualAddressSpace                                                     |
| 0x0002    | Simple  | [HvCallFlushVirtualAddressSpace](HvCallFlushVirtualAddressSpace.md)                 |
| 0x0003    | Attaché     | [HvCallFlushVirtualAddressList](HvCallFlushVirtualAddressList.md)                   |
| 0x0008    | Simple  | [HvCallNotifyLongSpinWait](HvCallNotifyLongSpinWait.md)                             |
| 0x000b    | Simple  | [HvCallSendSyntheticClusterIpi](HvCallSendSyntheticClusterIpi.md)                   |
| 0x000c    | Attaché     | [HvCallModifyVtlProtectionMask](HvCallModifyVtlProtectionMask.md)                   |
| 0x000d    | Simple  | [HvCallEnablePartitionVtl](HvCallEnablePartitionVtl.md)                             |
| 0x000F    | Simple  | [HvCallEnableVpVtl](HvCallEnableVpVtl.md)                                           |
| 0x0011    | Simple  | [HvCallVtlCall](HvCallVtlCall.md)                                                   |
| 0x0012    | Simple  | [HvCallVtlReturn](HvCallVtlReturn.md)                                               |
| 0x0013    | Simple  | [HvCallFlushVirtualAddressSpaceEx](HvCallFlushVirtualAddressSpaceEx.md)             |
| 0x0014    | Attaché     | [HvCallFlushVirtualAddressListEx](HvCallFlushVirtualAddressListEx.md)               |
| 0x0015    | Simple  | [HvCallSendSyntheticClusterIpiEx](HvCallSendSyntheticClusterIpiEx.md)               |
| 0x0050    | Attaché     | [HvCallGetVpRegisters](HvCallGetVpRegisters.md)                                     |
| 0x0051    | Attaché     | [HvCallSetVpRegisters](HvCallSetVpRegisters.md)                                     |
| 0x005C    | Simple  | [HvCallPostMessage](HvCallPostMessage.md)                                           |
| 0x005D    | Simple  | [HvCallSignalEvent](HvCallSignalEvent.md)                                           |
| 0x007e    | Simple  | [HvCallRetargetDeviceInterrupt](HvCallRetargetDeviceInterrupt.md)                   |
| 0x0099    | Simple  | [HvCallStartVirtualProcessor](HvCallStartVirtualProcessor.md)                       |
| 0x009A    | Attaché     | [HvCallGetVpIndexFromApicId](HvCallGetVpIndexFromApicId.md)                         |
| 0x00AF    | Simple  | [HvCallFlushGuestPhysicalAddressSpace](HvCallFlushGuestPhysicalAddressSpace.md)     |
| 0x00B0    | Attaché     | [HvCallFlushGuestPhysicalAddressList](HvCallFlushGuestPhysicalAddressList.md)       |

Le tableau suivant répertorie les hyperappels étendus pris en charge par l’appel de code.

| Appeler du code | Type    | Hypercall                                                                           |
|-----------|---------|-------------------------------------------------------------------------------------|
| 0x8001    | Simple  | [HvExtCallQueryCapabilities](HvExtCallQueryCapabilities.md)                         |
| 0x8002    | Simple  | HvExtCallGetBootZeroedMemory                                                        |
| 0x8003    | Simple  | HvExtCallMemoryHeatHint                                                             |
| 0x8004    | Simple  | HvExtCallEpfSetup                                                                   |
| 0x8006    | Simple  | HvExtCallMemoryHeatHintAsync                                                        |
