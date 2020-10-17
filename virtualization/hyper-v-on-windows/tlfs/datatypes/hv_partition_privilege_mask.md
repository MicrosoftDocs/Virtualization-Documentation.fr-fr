---
title: HV_PARTITION_PRIVILEGE_MASK
description: HV_PARTITION_PRIVILEGE_MASK
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 2efabf3eedba77d9b3a51e8ee130ba3a0b975ccd
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120672"
---
# <a name="hv_partition_privilege_mask"></a>HV_PARTITION_PRIVILEGE_MASK

Une partition peut interroger son masque de privilèges par le biais de « l’identification de la fonctionnalité hyperviseur » (0x40000003).

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    // Access to virtual MSRs
    UINT64 AccessVpRunTimeReg:1;
    UINT64 AccessPartitionReferenceCounter:1;
    UINT64 AccessSynicRegs:1;
    UINT64 AccessSyntheticTimerRegs:1;
    UINT64 AccessIntrCtrlRegs:1;
    UINT64 AccessHypercallMsrs:1;
    UINT64 AccessVpIndex:1;
    UINT64 AccessResetReg:1;
    UINT64 AccessStatsReg:1;
    UINT64 AccessPartitionReferenceTsc:1;
    UINT64 AccessGuestIdleReg:1;
    UINT64 AccessFrequencyRegs:1;
    UINT64 AccessDebugRegs:1;
    UINT64 AccessReenlightenmentControls:1
    UINT64 Reserved1:18;

    // Access to hypercalls
    UINT64 CreatePartitions:1;
    UINT64 AccessPartitionId:1;
    UINT64 AccessMemoryPool:1;
    UINT64 Reserved:1;
    UINT64 PostMessages:1;
    UINT64 SignalEvents:1;
    UINT64 CreatePort:1;
    UINT64 ConnectPort:1;
    UINT64 AccessStats:1;
    UINT64 Reserved2:2;
    UINT64 Debugging:1;
    UINT64 CpuManagement:1;
    UINT64 Reserved:1
    UINT64 Reserved:1;
    UINT64 Reserved:1;
    UINT64 AccessVSM:1;
    UINT64 AccessVpRegisters:1;
    UINT64 Reserved:1;
    UINT64 Reserved:1;
    UINT64 EnableExtendedHypercalls:1;
    UINT64 StartVirtualProcessor:1;
    UINT64 Reserved3:10;
} HV_PARTITION_PRIVILEGE_MASK;
 ```

Chaque privilège contrôle l’accès à un ensemble de MSRs ou d’hyperappels synthétiques.

| Indicateur de privilège                        | Signification                                       |
|---------------------------------------|-----------------------------------------------|
|`AccessVpRunTimeReg`                   | La partition a accès au HV_X64_MSR_VP_RUNTIME MSR synthétique. |
|`AccessPartitionReferenceCounter`      | La partition a accès au compte de référence à l’ensemble de la partition MSR, HV_X64_MSR_TIME_REF_COUNT. |
|`AccessSynicRegs`                      | La partition a accès au MSRs synthétique associé à SynIC (HV_X64_MSR_SCONTROL via HV_X64_MSR_EOM et HV_X64_MSR_SINT0 par HV_X64_MSR_SINT15).|
|`AccessSyntheticTimerMsrs`             | La partition a accès au MSRs synthétique associé à SynIC (HV_X64_MSR_STIMER0_CONFIG via HV_X64_MSR_STIMER3_COUNT). |
|`AccessIntrCtrlRegs`                   | La partition a accès au MSRs synthétique associé à l’APIC (HV_X64_MSR_EOI, HV_X64_MSR_ICR et HV_X64_MSR_TPR). |
|`AccessHypercallMsrs`                  | La partition a accès à la MSRs synthétique liée à l’interface hypercall (HV_X64_MSR_GUEST_OS_ID et HV_X64_MSR_HYPERCALL). |
|`AccessVpIndex`                        | La partition a accès à la MSR synthétique qui retourne l’index du processeur virtuel. |
|`AccessResetReg`                       | Cette partition a accès à la MSR synthétique qui réinitialise le système. |
|`AccessStatsReg`                       | Cette partition a accès au MSRs synthétique qui autorise l’invité à mapper et à annuler ses propres pages de statistiques. |
|`AccessPartitionReferenceTsc`          | La partition a accès au TSC de référence. |
|`AccessGuestIdleReg`                   | La partition a accès à la MSR synthétique qui permet à l’invité d’entrer dans l’état d’inactivité de l’invité. |
|`AccessFrequencyRegs`                  | La partition a accès au MSRs synthétique qui fournit les fréquences TSC et APIC, si elles sont prises en charge. |
|`AccessDebugRegs`                      | La partition a accès au MSRs synthétique utilisé pour certaines formes de débogage invité. |
|`AccessReenlightenmentControls`        | La partition a accès aux contrôles de réversion. |
|`CreatePartitions`                     | La partition peut appeler le HvCallCreatePartition hypercall. La partition peut également effectuer tout autre hyperappel qui est limité à l’utilisation des enfants. |
|`AccessPartitionId`                    | La partition peut appeler l’hypercall HvCallGetPartitionId pour obtenir son propre ID de partition. |
|`AccessMemoryPool`                     | La partition peut appeler les hyperappels HvCallDepositMemory, HvCallWithdrawMemory et HvCallGetMemoryBalance. |
|`PostMessages`                         | La partition peut appeler le [HvCallPostMessage](../hypercalls/HvCallPostMessage.md)hypercall. |
|`SignalEvents`                         | La partition peut appeler le [HvCallSignalEvent](../hypercalls/HvCallSignalEvent.md)hypercall. |
|`CreatePort`                           | La partition peut appeler le HvCallCreatePort hypercall.  |
|`PostMessages`                         | La partition peut appeler le HvCallPostMessage hypercall. |
|`ConnectPort`                          | La partition peut appeler le HvCallConnectPort hypercall. |
|`AccessStats`                          | La partition peut appeler les hyperappels HvCallMapStatsPage et HvCallUnmapStatsPage. |
|`Debugging`                            | La partition peut appeler les hyperappels HvCallPostDebugData, HvCallRetrieveDebugData et HvCallResetDebugSession. |
|`CpuManagement`                        | La partition peut appeler plusieurs hyperappels pour la gestion de l’UC. |
|`AccessVSM`                            | La partition peut utiliser [VSM](../vsm.md). |
|`AccessVpRegisters`                    | La partition peut appeler les hyperappels HvCallSetVpRegisters et HvCallGetVpRegisters. |
|`EnableExtendedHypercalls`             | La partition peut utiliser l' [interface hypercall étendue](../hypercall-interface.md#extended-hypercall-interface). |
|`StartVirtualProcessor`                | La partition peut utiliser [HvCallStartVirtualProcessor](../hypercalls/HvCallStartVirtualProcessor.md) pour initialiser des processeurs virtuels. |
