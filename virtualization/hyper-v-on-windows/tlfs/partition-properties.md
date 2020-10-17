---
title: Partition
description: Interface de partition hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: c2a040603a5b0f3d354731ed3c2cc95f4c3da49b
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120520"
---
# <a name="partitions"></a>Partitions

L’hyperviseur prend en charge l’isolation en termes de partition. Une partition est une unité logique d’isolation, prise en charge par l’hyperviseur, dans laquelle des systèmes d’exploitation s'exécutent.

## <a name="partition-privilege-flags"></a>Indicateurs de privilège de partition

Chaque partition possède un ensemble de privilèges attribués par l’hyperviseur. Les privilèges contrôlent l’accès à MSRs synthétique ou aux hyperappels.

Une partition peut interroger ses privilèges par le biais de « l’identification de la fonctionnalité hyperviseur » (0x40000003). Consultez [HV_PARTITION_PRIVILEGE_MASK](datatypes/HV_PARTITION_PRIVILEGE_MASK.md) pour obtenir une description de tous les privilèges.

## <a name="partition-crash-enlightenment"></a>Inversion des pannes de partition

L’hyperviseur fournit des partitions invitées avec une fonctionnalité d’inversion des incidents. Cette interface permet au système d’exploitation en cours d’exécution dans une partition invitée de fournir à l’hyperviseur des informations légales sur les conditions du système d’exploitation fatal dans le cadre de sa procédure de vidage sur incident. Les options incluent la préservation du contenu du paramètre d’incident invité MSRs et la spécification d’un message d’incident. L’hyperviseur met ensuite ces informations à la disposition de la partition racine pour la journalisation. Grâce à ce mécanisme, l’administrateur de l’hôte de virtualisation peut collecter des informations sur l’événement d’incident du système d’exploitation invité sans avoir à inspecter le stockage persistant attaché à la partition invitée pour le vidage sur incident ou les informations de vidage principales qui peuvent être stockées ici par le système d’exploitation invité défaillant.

La disponibilité de ce mécanisme est indiquée via `CPUID.0x400003.EDX:10` , l’indicateur GuestCrashMsrsAvailable, reportez-vous à la [découverte des fonctionnalités](feature-discovery.md).

### <a name="guest-crash-enlightenment-interface"></a>Interface d’inversion des incidents invités

L’interface d’inversion des incidents invités est fournie par six MSRs synthétiques, comme indiqué ci-dessous.

 ```c
#define HV_X64_MSR_CRASH_P0 0x40000100
#define HV_X64_MSR_CRASH_P1 0x40000101
#define HV_X64_MSR_CRASH_P2 0x40000102
#define HV_X64_MSR_CRASH_P3 0x40000103
#define HV_X64_MSR_CRASH_P4 0x40000104
#define HV_X64_MSR_CRASH_CTL 0x40000105
 ```

#### <a name="guest-crash-control-msr"></a>Contrôle de panne invité MSR

La HV_X64_MSR_CRASH_CTL MSR de contrôle des incidents invités peut être utilisée par les partitions invitées pour déterminer les capacités de blocage invité de l’hyperviseur et pour appeler l’action spécifiée à entreprendre. La structure de données [HV_CRASH_CTL_REG_CONTENTS](datatypes/HV_CRASH_CTL_REG_CONTENTS.md) définit le contenu de la MSR.

##### <a name="determining-guest-crash-capabilities"></a>Détermination des capacités de blocage des invités

Pour déterminer les capacités de blocage des invités, les partitions invitées peuvent lire la HV_X64_MSR_CRASH_CTL Registre. L’ensemble d’actions et de fonctionnalités prises en charge par l’hyperviseur est signalé.

##### <a name="invoking-guest-crash-capabilities"></a>Appel des fonctionnalités de blocage des invités

Pour appeler une action de blocage d’un hyperviseur prise en charge, une partition invitée écrit dans le registre HV_X64_MSR_CRASH_CTL, en spécifiant l’action souhaitée. Deux variantes sont prises en charge : CrashNotify et CrashMessage en association avec CrashNotify. Pour chaque occurrence d’un incident invité, au plus une seule écriture dans la HV_X64_MSR_CRASH_CTL MSR doit être effectuée, en spécifiant l’une des deux variations.

| Action d’incident invité  | Description                                                 |
|---------------------|-------------------------------------------------------------|
| CrashMessage        | Cette action est utilisée en association avec CrashNotify pour spécifier un message d’incident à l’hyperviseur. Lorsque cette option est sélectionnée, les valeurs de P3 et P4 sont traitées comme l’emplacement et la taille du message. HV_X64_MSR_CRASH_P3 est l’adresse physique de l’invité du message et HV_X64_MSR_CRASH_P4 la longueur en octets du message (4096 octets au maximum). |
| CrashNotify         | Cette action indique à l’hyperviseur que la partition invitée a terminé d’écrire les données souhaitées dans le paramètre d’incident invité MSRs (c.-à-d., P0 à P4), et l’hyperviseur doit procéder à la journalisation du contenu de ces MSRs. |