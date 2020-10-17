---
title: Contrôleur d’interruptions virtuelles
description: Interface du contrôleur d’interruptions virtuelles hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 4311ac0698bfda86c4a840c2cc7052828f808e59
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120537"
---
# <a name="virtual-interrupt-controller"></a>Contrôleur d’interruptions virtuelles

L’hyperviseur virtualise la remise d’interruption aux processeurs virtuels. Cela s’effectue via l’utilisation d’un contrôleur d’interruptions synthétiques (SynIC) qui est une extension d’une APIC locale virtualisée ; autrement dit, chaque processeur virtuel possède une instance APIC locale avec les extensions SynIC. Ces extensions fournissent un mécanisme de communication entre les partitions simple, qui est décrit dans le chapitre suivant.
Les interruptions transmises à une partition se répartissent en deux catégories : externe et interne. Les interruptions externes proviennent d’autres partitions ou périphériques, et les interruptions internes proviennent de la partition elle-même.

Les interruptions externes sont générées dans les situations suivantes :

- Un périphérique matériel physique génère une interruption matérielle.
- Une partition parente déclare une interruption virtuelle (en général, en cours d’émulation d’un périphérique matériel).
- L’hyperviseur remet un message (par exemple, en raison d’une interception) à une partition.
- Une autre partition publie un message.
- Une autre partition signale un événement.

Les interruptions internes sont générées dans les situations suivantes :

- Un processeur virtuel accède au registre de commande d’interruption APIC (ICR).
- Un minuteur synthétique expire.

## <a name="local-apic"></a>APIC local

Le SynIC est un sur-ensemble d’un APIC local. L’interface à cet APIC est fournie par un ensemble de registres mappés en mémoire 32 bits. Cet APIC local (y compris le comportement des registres mappés en mémoire) est généralement compatible avec l’APIC local sur les systèmes P4/Xeon, comme décrit dans la documentation d’Intel et d’AMD.

La virtualisation APIC locale de l’hyperviseur peut s’écarter de l’opération APIC physique des manières suivantes :

- Sur les systèmes physiques, le IA32_APIC_BASE MSR peut être différent pour chaque processeur du système. L’hyperviseur peut exiger que ce MSR contienne la même valeur pour tous les processeurs virtuels au sein d’une partition. Par conséquent, ce MSR peut être traité comme une valeur à l’ensemble de la partition. Si un processeur virtuel modifie ce registre, la valeur peut se propager à tous les processeurs virtuels au sein de la partition.
- La IA32_APIC_BASE MSR définit un bit « global Enable » pour l’activation ou la désactivation de l’APIC. L’APIC virtualisé peut toujours être activé. Si c’est le cas, ce bit est toujours défini sur 1.
- L’APIC local de l’hyperviseur n’est peut-être pas en mesure de générer des SMIs virtuels (interruptions de gestion du système).
- Si plusieurs processeurs virtuels au sein d’une partition reçoivent des ID APIC identiques, le comportement de la remise d’interruption ciblée est illimité. Autrement dit, l’hyperviseur est libre de remettre l’interruption à un seul processeur virtuel, à tous les processeurs virtuels avec l’ID APIC spécifié, ou à aucun processeur virtuel. Cette situation est considérée comme une erreur de programmation invitée.
- Certains registres APIC mappés en mémoire sont accessibles par le biais d’une solution MSRs virtuelle.
- L’hyperviseur ne peut pas autoriser un invité à modifier ses ID APIC.

Les parties restantes de cette section décrivent uniquement les aspects des fonctionnalités SynIC qui sont des extensions de l’APIC local.

### <a name="local-apic-msr-accesses"></a>Accès au MSR APIC local

L’hyperviseur fournit un accès MSR accéléré aux registres APIC mappés en mémoire à forte utilisation. Il s’agit des registres TPR, EOI et ICR. Les registres haute et ICR à haute disponibilité sont combinés en un seul MSR. Pour des raisons de performances, le système d’exploitation invité doit suivre la recommandation d’hyperviseur pour l’utilisation de MSRs APIC.

| Adresse MSR      | Nom du Registre       | Description                                                                 |
|------------------|---------------------|-----------------------------------------------------------------------------|
| 0x40000070       | HV_X64_MSR_EOI      | Accède à l’APIC EOI                                                       |
| 0x40000071       | HV_X64_MSR_ICR      | Accède à APIC ICR-High et ICR-Low                                      |
| 0x40000072       | HV_X64_MSR_TPR      | Accéder à l’APIC TPR                                                         |

#### <a name="hv_x64_msr_eoi"></a>HV_X64_MSR_EOI

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 63:32         | RsvdZ (réservé, doit être égal à zéro)    | Write                                                       |
| 31:0          | Valeur EOI                           | Write                                                       |

#### <a name="hv_x64_msr_icr"></a>HV_X64_MSR_ICR

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 63:32         | Haute valeur ICR                      | Lecture/écriture                                                |
| 31:0          | Valeur basse ICR                       | Lecture/écriture                                                |

#### <a name="hv_x64_msr_tpr"></a>HV_X64_MSR_TPR

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 63:8          | RsvdZ (réservé, doit être égal à zéro)    | Lecture/écriture                                                |
| 7:0           | Valeur TPR                           | Lecture/écriture                                                |

Ce MSR est destiné à accélérer l’accès au TPR dans les partitions invitées en mode 32 bits. les partitions invitées en mode 64 doivent définir le TPR par le biais de CR8.

### <a name="synthetic-cluster-ipi"></a>IPI de cluster synthétique

Un hyperviseur prend en charge les hyperappels qui permettent à d’envoyer des interruptions fixes virtuelles à un ensemble arbitraire de processeurs virtuels.

| Hypercall                                                                           | Description                                     |
|-------------------------------------------------------------------------------------|-------------------------------------------------|
| [HvCallSendSyntheticClusterIpi](hypercalls/HvCallSendSyntheticClusterIpi.md)      | Envoie une interruption fixe virtuelle à l’ensemble de processeurs virtuels spécifié. |
| [HvCallSendSyntheticClusterIpiEx](hypercalls/HvCallSendSyntheticClusterIpiEx.md)  | Semblable à HvCallSendSyntheticClusterIpi, prend un VP épars défini comme entrée.    |

### <a name="eoi-assist"></a>Assistance EOI

Un champ de la [page d’assistance du processeur virtuel](vp-properties.md#virtual-processor-assist-page) est le champ EOI Assist. Le champ assistance EOI se trouve au décalage 0 de la page de superposition et sa taille est de 32 bits. Le format du champ EOI Assist est le suivant :

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 31:1          | RsvdZ                               | Lecture/écriture                                                |
| 0             | Aucun EOI requis                     | Lecture/écriture                                                |

Le système d’exploitation invité effectue un EOI en écrivant atomiquement zéro dans le champ assistance EOI de la page d’assistance du VP virtuel et en vérifiant si le champ « aucun EOI requis » était précédemment nul. Si c’est le cas, le système d’exploitation doit écrire sur le HV_X64_APIC_EOI MSR, ce qui déclenche une interception dans l’hyperviseur. Le code suivant est recommandé pour effectuer un EOI :

```
lea rcx, [VirtualApicAssistVa]
btr [rcx], 0
jc NoEoiRequired

mov ecx, HV_X64_APIC_EOI
wrmsr

NoEoiRequired:
```

L’hyperviseur définit le bit « no EOI required » lorsqu’il injecte une interruption virtuelle si les conditions suivantes sont satisfaites :

- L’interruption virtuelle est déclenchée par le bord, et
- Aucune interruption de priorité inférieure n’est en attente

Si, ultérieurement, une interruption de priorité inférieure est demandée, l’hyperviseur efface le « no EOI required », de sorte qu’un EOI suivant provoque une interception.

En cas d’interruptions imbriquées, l’interception EOI est évitée uniquement pour l’interruption de priorité la plus élevée. Cela est nécessaire, car aucun nombre n’est maintenu pour le nombre de EOIs effectuées par le système d’exploitation. Par conséquent, seul le premier EOI peut être évité et puisque le premier EOI efface le bit « no EOI required », le EOI suivant génère une interception. Toutefois, les interruptions imbriquées sont rares, ce qui n’est pas un problème dans le cas courant.

Notez que les appareils et/ou l’APIC d’e/s (physique ou synthétique) n’ont pas besoin d’être avertis d’un EOI pour une interruption déclenchée par le bord : l’hyperviseur intercepte ces EOIs uniquement pour mettre à jour l’État APIC virtuel. Dans certains cas, l’État APIC virtuel peut être mis à jour tardivement. dans ce cas, le bit « NoEoiRequired » est défini par l’hyperviseur, indiquant à l’invité qu’une interception EOI n’est pas nécessaire. Plus tard, l’hyperviseur peut dériver l’état de l’APIC local en fonction de la valeur actuelle du bit « NoEoiRequired ».

L’activation et la désactivation de cette mise en œuvre peuvent être effectuées à tout moment indépendamment de l’activité d’interruption et de l’État APIC à ce moment-là. Tandis que l’activation est activée, les EOIs conventionnels peuvent toujours être exécutés, quelle que soit la valeur « aucune EOI requise », mais elles ne profiteront pas des avantages en matière de performances de l’ingestion.
