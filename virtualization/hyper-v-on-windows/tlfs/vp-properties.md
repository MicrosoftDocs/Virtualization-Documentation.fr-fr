---
title: Processeur virtuel
description: Interface du processeur virtuel de l’hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: a27d41b5d459e75af0fcbaf80de02b8e07d66391
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120563"
---
# <a name="virtual-processors"></a>Processeurs virtuels

Chaque partition peut avoir zéro ou plusieurs processeurs virtuels.

## <a name="virtual-processor-indices"></a>Index du processeur virtuel

Un processeur virtuel est identifié par un tuple composé de son ID de partition et de son index de processeur. L’index du processeur est affecté au processeur virtuel lors de sa création, et il n’est pas modifié pendant la durée de vie du processeur virtuel.

Une valeur spéciale HV_ANY_VP peut être utilisée dans certaines situations pour spécifier « n’importe quel processeur virtuel ». La valeur HV_VP_INDEX_SELF peut être utilisée pour spécifier l’index de vice-président de l’un.

```c
typedef UINT32 HV_VP_INDEX;

#define HV_ANY_VP ((HV_VP_INDEX)-1)

#define HV_VP_INDEX_SELF ((HV_VP_INDEX)-2)
 ```

L’ID d’un processeur virtuel peut être récupéré par l’invité par le biais d’un HV_X64_MSR_VP_INDEX MSR (Registre spécifique au modèle) défini par un hyperviseur.

```c
#define HV_X64_MSR_VP_INDEX 0x40000002
 ```

## <a name="virtual-processor-idle-sleep-state"></a>État de veille de l’inactivité du processeur virtuel

Les processeurs virtuels peuvent être placés dans un état d’alimentation de processeur inactif virtuel ou dans un état de veille du processeur. Cet état d’inactivité virtuelle améliorée permet à un processeur virtuel mis en état d’inactivité faible d’être réveillé avec l’arrivée d’une interruption même lorsque l’interruption est masquée sur le processeur virtuel. En d’autres termes, l’état inactif virtuel permet au système d’exploitation de la partition invitée de tirer parti des techniques d’économie d’énergie du processeur dans le système d’exploitation qui, sans cela, seraient indisponibles lors de l’exécution dans une partition invitée.

Une partition qui possède le privilège AccessGuestIdleMsr peut déclencher l’entrée dans l’état de veille de l’inactivité du processeur virtuel via une lecture sur la MSR définie par l’hyperviseur `HV_X64_MSR_GUEST_IDLE` . Le processeur virtuel est réveillée lorsqu’une interruption arrive, que l’interruption soit activée ou non sur le processeur virtuel.

## <a name="virtual-processor-assist-page"></a>Page d’assistance du processeur virtuel

L’hyperviseur fournit une page par processeur virtuel qui est superposée sur l’espace de l’amp invité. Cette page peut être utilisée pour une communication bidirectionnelle entre un VP invité et l’hyperviseur. Le système d’exploitation invité dispose d’un accès en lecture/écriture à cette page d’aide du VP virtuel.

Un invité spécifie l’emplacement de la page de superposition (dans l’espace GPA) en écrivant sur le VP virtuel d’aide MSR (0x40000073). Le format de la page MSR de l’aide du VP virtuel est le suivant :

| Bits      | Champ           | Description                                                                 |
|-----------|-----------------|-----------------------------------------------------------------------------|
| 0         | Activer          | Active la page d’assistance du VP                                                  |
| 11:1      | RsvdP           | Réservé                                                                    |
| 63:12     | Page PFN        | Page d’assistance du VP virtuel PFN                                                  |

## <a name="virtual-processor-run-time-register"></a>Registre de la durée d’exécution du processeur virtuel

Le planificateur de l’hyperviseur effectue en interne le suivi du temps que chaque processeur virtuel consomme dans l’exécution du code. L’heure suivie est une combinaison de la durée pendant laquelle le processeur virtuel consomme du code invité en cours d’exécution, et du temps que le processeur logique associé passe à exécuter le code de l’hyperviseur pour le compte de cet invité. Cette heure cumulative est accessible via le HV_X64_MSR_VP_RUNTIME en lecture seule 64 bits MSR. La quantité de temps est mesurée en unités de 100 ns.

## <a name="non-privileged-instruction-execution-prevention-npiep"></a>Prévention de l’exécution d’instructions non privilégiées (NPIEP)

L’exécution d’instructions non privilégiées (NPIEP) est une fonctionnalité qui limite l’utilisation de certaines instructions par code en mode utilisateur. Plus précisément, lorsqu’elle est activée, cette fonctionnalité peut bloquer l’exécution des instructions SIDT, SGDT, SLDT et STR. L’exécution de ces instructions génère une erreur de #GP.

Cette fonctionnalité doit être configurée par VP à l’aide de [HV_X64_MSR_NPIEP_CONFIG_CONTENTS](datatypes/HV_X64_MSR_NPIEP_CONFIG_CONTENTS.md).

## <a name="guest-spinlocks"></a>Verrouillages spinlock invités

Un système d’exploitation standard multiprocesseur prend en charge les verrous pour l’application de l’atomicité de certaines opérations. Lors de l’exécution d’un système d’exploitation de ce type dans un ordinateur virtuel contrôlé par l’hyperviseur, ces sections critiques protégées par des verrous peuvent être étendues par les interceptions générées par le code de la section critique. Le code de la section critique peut également être devancé par le planificateur de l’hyperviseur. Bien que l’hyperviseur tente d’empêcher ces préemptions, il peut se produire. Par conséquent, d’autres contendeurs de verrous pourraient finir par tourner jusqu’à ce que le détenteur de verrou soit de nouveau planifié et, par conséquent, étendre considérablement le temps d’acquisition du verrouillage SpinLock.

L’hyperviseur indique au système d’exploitation invité le nombre de fois qu’une acquisition de SpinLock doit être tentée avant d’indiquer une situation de spin excessive à l’hyperviseur. Ce nombre est retourné dans le 0x40000004 de la feuille CPUID.

L’hypercall [HvCallNotifyLongSpinWait](hypercalls/HvCallNotifyLongSpinWait.md) fournit une interface pour les invités compatibles afin d’améliorer la propriété d’équité statistique d’un verrou pour les machines virtuelles multiprocesseurs. L’invité doit effectuer cette notification sur chaque multiple du nombre recommandé retourné par le 0x40000004 de la feuille CPUID. Grâce à cet hyperappel, un invité notifie l’hyperviseur d’une longue acquisition de SpinLock. Cela permet à l’hyperviseur de prendre de meilleures décisions de planification.