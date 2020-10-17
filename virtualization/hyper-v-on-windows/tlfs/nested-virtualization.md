---
title: Virtualisation imbriquée
description: Virtualisation imbriquée
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 36fb58759f7236f5fac0a66a2b3621d35b382c28
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120712"
---
# <a name="nested-virtualization"></a>Virtualisation imbriquée

La virtualisation imbriquée fait référence à l’hyperviseur Hyper-V qui émule les extensions de virtualisation matérielle. Ces extensions émulées peuvent être utilisées par d’autres logiciels de virtualisation (par exemple, un hyperviseur imbriqué) pour s’exécuter sur la plateforme Hyper-V.

Cette fonctionnalité est uniquement disponible pour les partitions invitées. Elle doit être activée pour chaque machine virtuelle. La virtualisation imbriquée n’est pas prise en charge dans une partition racine Windows.

La terminologie suivante permet de définir différents niveaux de virtualisation imbriquée :

| Terme                   | Définition                                                              |
|------------------------|-------------------------------------------------------------------------|
| **Hyperviseur n0**      | Hyperviseur Hyper-V exécuté sur le matériel physique.                    |
| **Racine L1**            | Le système d’exploitation racine Windows.                                      |
| **Invité L1**           | Ordinateur virtuel Hyper-V sans hyperviseur imbriqué.                  |
| **Hyperviseur L1**      | Hyperviseur imbriqué s’exécutant au sein d’un ordinateur virtuel Hyper-V.           |
| **Racine L2**            | Un système d’exploitation Windows racine, exécuté dans le contexte d’une machine virtuelle Hyper-V.|
| **Invité L2**           | Un ordinateur virtuel imbriqué, exécuté dans le contexte d’un ordinateur virtuel Hyper-V. |

Par rapport au système nu, les hyperviseurs peuvent entraîner une régression significative des performances lors de l’exécution dans une machine virtuelle. Les hyperviseurs L1 peuvent être optimisés pour s’exécuter sur une machine virtuelle Hyper-V à l’aide d’interfaces compatibles fournies par l’hyperviseur N0.

## <a name="enlightened-vmcs"></a>VMCS

Sur les plateformes Intel, le logiciel de virtualisation utilise des structures de données de contrôle de machine virtuelle (VMCSs) pour configurer le comportement du processeur lié à la virtualisation. VMCSs doit être activé à l’aide d’une instruction VMPTRLD et modifié à l’aide des instructions VMREAD et VMWRITE. Ces instructions représentent souvent un goulot d’étranglement important pour la virtualisation imbriquée, car elles doivent être émulées.

L’hyperviseur expose une fonctionnalité « VMCSe » qui peut être utilisée pour contrôler le comportement du processeur lié à la virtualisation à l’aide d’une structure de données dans la mémoire physique invitée. Cette structure de données peut être modifiée à l’aide d’instructions d’accès mémoire normales, de sorte qu’il n’est pas nécessaire que l’hyperviseur L1 exécute des instructions VMREAD ou VMWRITE ou VMPTRLD.

L’hyperviseur L1 peut choisir d’utiliser des VMCSss compatibles en écrivant 1 dans le champ correspondant de la [page d’assistance du processeur virtuel](vp-properties.md#virtual-processor-assist-page). Un autre champ de la page aide du VP contrôle le VMCS actuellement activé. Chaque VMCS de contrôle d’un point de contrôle est d’une taille d’une page (4 Ko) et doit être initialement zéro. Aucune instruction VMPTRLD ne doit être exécutée pour rendre un VMCS activé ou actif.

Une fois que l’hyperviseur L1 a effectué une entrée de machine virtuelle avec un VMCS activé, le VMCS est considéré comme actif sur le processeur. Un VMCS qui a été activé ne peut être actif que sur un seul processeur à la fois. L’hyperviseur L1 peut exécuter une instruction VMCLEAR pour faire passer un VMCS activé à l’état non actif. Toute instruction VMREAD ou VMWRITE alors qu’un VMCS de compatibilité est actif n’est pas prise en charge et peut entraîner un comportement inattendu.

La structure [HV_VMX_ENLIGHTENED_VMCS](datatypes/HV_VMX_ENLIGHTENED_VMCS.md) définit la disposition du VMCS. Tous les champs non synthétiques sont mappés à un encodage VMCS physique Intel.

### <a name="feature-discovery"></a>Détection des fonctionnalités

La prise en charge d’une interface VMCS avec assistance est signalée avec le 0x40000004 de la feuille CPUID.

La structure VMCS a été gérée pour tenir compte des futures modifications. Chaque structure VMCS signalée contient un champ de version, qui est signalé par l’hyperviseur N0.

La seule version VMCS actuellement prise en charge est 1.

### <a name="clean-fields"></a>Champs de nettoyage

L’hyperviseur n0 peut choisir de mettre en cache des parties du VMCS. Les champs de nettoyage VMCS activés déterminent les parties du VMCS qui ont été rechargées à partir de la mémoire invité sur une entrée de machine virtuelle imbriquée. L’hyperviseur L1 doit effacer les champs de nettoyage VMCS correspondants chaque fois qu’il modifie le VMCS, sinon l’hyperviseur n0 peut utiliser une version obsolète.

L’inversion des champs de nettoyage est contrôlée par le biais du champ « CleanFields » synthétique du VMCS. Par défaut, tous les bits sont définis de façon à ce que l’hyperviseur n0 doive recharger les champs VMCS correspondants pour chaque entrée de machine virtuelle imbriquée.

## <a name="enlightened-msr-bitmap"></a>Bitmap MSR

Sur les plateformes Intel, l’hyperviseur n0 émule les contrôles « MSR-bitmap Address » VMX qui permettent au logiciel de virtualisation de contrôler les accès aux interceptions de la MSR.

L’hyperviseur L1 peut collaborer avec l’hyperviseur n0 pour améliorer l’efficacité des accès à la MSR. Il peut activer les bitmaps MSR compatibles en affectant la valeur 1 au champ correspondant dans le VMCS. Quand cette option est activée, l’hyperviseur n0 n’analyse pas les bitmaps MSR pour les modifications. Au lieu de cela, l’hyperviseur L1 doit invalider le champ Clean correspondant après avoir apporté des modifications à l’une des bitmaps MSR.

## <a name="compatibility-with-live-migration"></a>Compatibilité avec Migration dynamique

Hyper-V a la possibilité de migrer dynamiquement une partition enfant d’un hôte vers un autre. Les migrations dynamiques sont généralement transparentes à la partition enfant. Toutefois, dans le cas d’une virtualisation imbriquée, l’hyperviseur L1 devra peut-être être conscient des migrations.

### <a name="live-migration-notification"></a>Notification de Migration dynamique

Un hyperviseur L1 peut demander à être averti lorsque sa partition est migrée. Cette fonctionnalité est énumérée dans le « CPUID » en tant que privilège « AccessReenlightenmentControls ». L’hyperviseur n0 expose un LBM synthétique (HV_X64_MSR_REENLIGHTENMENT_CONTROL) qui peut être utilisé par l’hyperviseur L1 pour configurer un vecteur d’interruption et un processeur cible. L’hyperviseur n0 injecte une interruption avec le vecteur spécifié après chaque migration.

```c
#define HV_X64_MSR_REENLIGHTENMENT_CONTROL (0x40000106)

typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 Vector :8;
        UINT64 RsvdZ1 :8;
        UINT64 Enabled :1;
        UINT64 RsvdZ2 :15;
        UINT64 TargetVp :32;
    };
} HV_REENLIGHTENMENT_CONTROL;
```

Le vecteur spécifié doit correspondre à une interruption APIC fixe. TargetVp spécifie l’index de processeur virtuel.

### <a name="tsc-emulation"></a>Émulation TSC

Une partition invitée peut être migrée dynamiquement entre deux machines avec des fréquences TSC différentes. Dans ces cas-là, la valeur TscScale de la [page TSC de référence](timers.md#partition-reference-time-enlightenment) devra peut-être être recalculée.

L’hyperviseur n0 émule éventuellement tous les accès TSC après une migration jusqu’à ce que l’hyperviseur L1 ait la possibilité de recalculer la valeur TscScale. L’hyperviseur L1 peut s’abonner à l’émulation TSC en écrivant sur la HV_X64_MSR_TSC_EMULATION_CONTROL MSR. S’il est activé, l’hyperviseur n0 émule les accès TSC une fois la migration effectuée.

L’hyperviseur L1 peut interroger si des accès TSC sont actuellement émulés à l’aide de la HV_X64_MSR_TSC_EMULATION_STATUS MSR. Par exemple, l’hyperviseur L1 peut s’abonner à [migration dynamique notifications](#live-migration-notification) et interroger l’État TSC après avoir reçu l’interruption de migration. Il peut également désactiver l’émulation TSC (après avoir mis à jour la valeur TscScale) à l’aide de ce MSR.

```c
#define HV_X64_MSR_TSC_EMULATION_CONTROL (0x40000107)
#define HV_X64_MSR_TSC_EMULATION_STATUS (0x40000108)

typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 Enabled :1;
        UINT64 RsvdZ :63;
    };
 } HV_TSC_EMULATION_CONTROL;

typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 InProgress : 1;
        UINT64 RsvdP1 : 63;
    };
} HV_TSC_EMULATION_STATUS;
```

## <a name="virtual-tlb"></a>TLB virtuel

Le TLB virtuel exposé par l’hyperviseur peut être étendu pour mettre en cache les traductions de L2 GPAs vers GPAs. Comme avec le TLB sur un processeur logique, le TLB virtuel est un cache non cohérent, et cette non-cohérence est visible pour les invités. L’hyperviseur expose des opérations pour gérer le TLB.
Sur les plateformes Intel, l’hyperviseur n0 virtualise les méthodes supplémentaires suivantes pour gérer le TLB :

- L’instruction INVVPID peut être utilisée pour invalider des GVA mis en cache sur des mappages d’amp ou SPA
- L’instruction INVEPT peut être utilisée pour invalider les mappages de la couche de couche des connexions GPA en cache GPA

### <a name="direct-virtual-flush"></a>Vidage virtuel direct

L’hyperviseur expose des hyperappels ([HvCallFlushVirtualAddressSpace](hypercalls/HvCallFlushVirtualAddressSpace.md), [HvCallFlushVirtualAddressSpaceEx](hypercalls/HvCallFlushVirtualAddressSpaceEx.md), [HvCallFlushVirtualAddressList](hypercalls/HvCallFlushVirtualAddressList.md)et [HvCallFlushVirtualAddressListEx](hypercalls/HvCallFlushVirtualAddressListEx.md)) qui permettent aux systèmes d’exploitation de gérer plus efficacement le TLB virtuel. L’hyperviseur L1 peut choisir d’autoriser son invité à utiliser ces hyperappels et de déléguer la responsabilité de les gérer à l’hyperviseur N0. Cela nécessite l’utilisation d’un VMCSs et d’une page d’assistance de partition.

En cas d’utilisation d’VMCSss, le TLB virtuel marque tous les mappages mis en cache avec un identificateur du VMCS qui les a créés. En réponse à un hyperappel de vidage virtuel direct à partir d’un invité L2, l’hyperviseur n0 invalide tous les mappages mis en cache créés par VMCSs

- Le VmId est le même que le VmId de l’appelant
- Le VpId est contenu dans le ProcessorMask ou HV_FLUSH_ALL_PROCESSORS spécifié.

#### <a name="configuration"></a>Configuration

La gestion directe des hyperappels de vidage virtuel est activée par :

1. Définition du champ NestedEnlightenmentsControl. Features. DirectHypercall de la [page d’assistance du processeur virtuel](vp-properties.md#virtual-processor-assist-page) sur 1.
2. Affectation de la valeur 1 au champ EnlightenmentsControl. NestedFlushVirtualHypercall d’un VMCS à l'.

Avant de l’activer, l’hyperviseur L1 doit configurer les champs supplémentaires suivants de l’VMCS d’informations d’être activé :

- VpId : ID du processeur virtuel que l’VMCS a pu contrôler.
- VmId : ID de la machine virtuelle à laquelle appartient le VMCS d’identification.
- PartitionAssistPage : adresse physique de l’invité de la page d’assistance à la partition.

L’hyperviseur L1 doit également exposer les fonctionnalités suivantes à ses invités via CPUID.

- UseHypercallForLocalFlush
- UseHypercallForRemoteFlush

#### <a name="partition-assist-page"></a>Page d’assistance à la partition

La page d’aide sur la partition est une zone de taille de page alignée sur la taille de la page, que l’hyperviseur L1 doit allouer et zéro avant de pouvoir utiliser des hyperappels de vidage direct. Son amp doit être écrite dans le champ correspondant dans le VMCS.

```c
struct
{
    UINT32 TlbLockCount;
} VM_PARTITION_ASSIST_PAGE;
```

#### <a name="synthetic-vm-exit"></a>VM-Exit synthétique

Si le TlbLockCount de la page d’assistance à la partition de l’appelant est différent de zéro, l’hyperviseur n0 remet un VM-Exit avec une raison de sortie synthétique à l’hyperviseur L1 après avoir géré un hyperappel de vidage virtuel direct.

```c
#define HV_VMX_SYNTHETIC_EXIT_REASON_TRAP_AFTER_FLUSH 0x10000031
```

### <a name="second-level-address-translation"></a>Traduction d’adresse de second niveau

Lorsque la virtualisation imbriquée est activée pour une partition invitée, l’unité de gestion de la mémoire (MMU) exposée par la partition prend en charge la traduction d’adresses de second niveau. La traduction d’adresses de second niveau est une fonctionnalité qui peut être utilisée par l’hyperviseur L1 pour virtualiser la mémoire physique. En cas d’utilisation, certaines adresses qui seraient traitées comme des adresses physiques invitées (GPAs) sont traitées comme des adresses physiques d’invité L2 (L2 GPAs) et converties en GPAs en parcourant un ensemble de structures de pagination.

L’hyperviseur L1 peut décider comment et où utiliser les espaces d’adressage de deuxième niveau. Chaque espace d’adressage de second niveau est identifié par une valeur d’ID 64 bits définie par l’invité. Sur les plateformes Intel, cette valeur est identique à l’adresse de la table EPT PML4.

#### <a name="compatibility"></a>Compatibilité

Sur les plateformes Intel, la fonctionnalité de traduction d’adresse de second niveau exposée par l’hyperviseur est généralement compatible avec la prise en charge de VMX pour la traduction d’adresses. Toutefois, les différences observables suivantes existent :

- En interne, l’hyperviseur peut utiliser des tables de pages fictives qui convertissent les GPAs L2 en SPAs. Dans de telles implémentations, ces tables de pages fictives s’affichent comme étant des TLBss de grande taille. Toutefois, plusieurs différences peuvent être observables. Tout d’abord, les tables de pages fictives peuvent être partagées entre deux processeurs virtuels, tandis que les TLBs classiques sont des structures par processeur et sont indépendantes. Ce partage peut être visible, car un accès de page par un processeur virtuel peut remplir une entrée de table de pages fictives qui est ensuite utilisée par un autre processeur virtuel.
- Certaines implémentations d’hyperviseur peuvent utiliser la protection en écriture interne des tables de pages invitées pour vider les mappages MMU des structures de données internes (par exemple, les tables de pages fictives). Cela est invisible pour l’invité, car les écritures dans ces tables seront gérées de manière transparente par l’hyperviseur. Toutefois, les écritures effectuées sur les pages GPA sous-jacentes par d’autres partitions ou par des périphériques peuvent ne pas déclencher le vidage TLB approprié.
- Sur certaines implémentations d’hyperviseur, une erreur de page de deuxième niveau (« violation EPT ») peut ne pas invalider les mappages mis en cache.

#### <a name="enlightened-second-level-tlb-flushes"></a>Vidages du TLB de second niveau

L’hyperviseur prend également en charge un ensemble d’améliorations qui permettent à un invité de gérer plus efficacement l’TLB de deuxième niveau. Ces opérations améliorées peuvent être utilisées de façon interchangeable avec les opérations de gestion TLB héritées.

L’hyperviseur prend en charge les hyperappels suivants pour invalider TLBs :

| Hypercall                                                                           | Description                                     |
|-------------------------------------------------------------------------------------|-------------------------------------------------|
| [HvCallFlushGuestPhysicalAddressSpace](hypercalls/HvCallFlushGuestPhysicalAddressSpace.md)      | invalide les mappages de niveau de plan de couche des connexions GPA dans un espace d’adressage de deuxième niveau.   |
| [HvCallFlushGuestPhysicalAddressList](hypercalls/HvCallFlushGuestPhysicalAddressList.md)  | invalide les mappages amp GVA/L2 de L2 mis en cache dans une partie d’un espace d’adressage de deuxième niveau.    |

## <a name="nested-virtualization-exceptions"></a>Exceptions de virtualisation imbriquée

L’hyperviseur L1 peut s’abonner à la combinaison d’exceptions de virtualisation dans la classe d’exception de défaut de page. L’hyperviseur n0 annonce la prise en charge de cette clarté dans les fonctionnalités de virtualisation imbriquée de l’hyperviseur.

Cette activation peut être activée en affectant la valeur « 1 » à VirtualizationException dans [HV_NESTED_ENLIGHTENMENTS_CONTROL](datatypes/HV_NESTED_ENLIGHTENMENTS_CONTROL.md) structure de données de la page d’assistance du processeur virtuel.

## <a name="nested-virtualization-msrs"></a>Virtualisation imbriquée MSRs

### <a name="nested-vp-index-register"></a>Registre de l’index VP imbriqué

L’hyperviseur L1 expose un MSR qui signale l’index du VP sous-jacent du processeur actuel.

| Adresse MSR      | Nom du Registre              | Description                                                                 |
|------------------|----------------------------|-----------------------------------------------------------------------------|
| 0x40001002       | HV_X64_MSR_NESTED_VP_INDEX | Dans une partition racine imbriquée, signale l’index du VP sous-jacent du processeur en cours. |

### <a name="nested-synic-msrs"></a>MSRs SynIC imbriqué

Dans une partition racine imbriquée, le MSRs suivant autorise l’accès au [MSRS SynIC](inter-partition-communication.md#synic-msrs) correspondant de l’hyperviseur de base.

Pour Rechercher l’index du processeur sous-jacent, les appelants doivent d’abord utiliser HV_X64_MSR_NESTED_VP_INDEX.

| Adresse MSR      | Nom du Registre                 | MSR sous-jacent                                              |
|------------------|-------------------------------|--------------------------------------------------------------|
| 0x40001080       | HV_X64_MSR_NESTED_SCONTROL    | HV_X64_MSR_SCONTROL                                          |
| 0x40001081       | HV_X64_MSR_NESTED_SVERSION    | HV_X64_MSR_SVERSION                                          |
| 0x40001082       | HV_X64_MSR_NESTED_SIEFP       | HV_X64_MSR_SIEFP                                             |
| 0x40001083       | HV_X64_MSR_NESTED_SIMP        | HV_X64_MSR_SIMP                                              |
| 0x40001084       | HV_X64_MSR_NESTED_EOM         | HV_X64_MSR_EOM                                               |
| 0x40001090       | HV_X64_MSR_NESTED_SINT0       | HV_X64_MSR_SINT0                                             |
| 0x40001091       | HV_X64_MSR_NESTED_SINT1       | HV_X64_MSR_SINT1                                             |
| 0x40001092       | HV_X64_MSR_NESTED_SINT2       | HV_X64_MSR_SINT2                                             |
| 0x40001093       | HV_X64_MSR_NESTED_SINT3       | HV_X64_MSR_SINT3                                             |
| 0x40001094       | HV_X64_MSR_NESTED_SINT4       | HV_X64_MSR_SINT4                                             |
| 0x40001095       | HV_X64_MSR_NESTED_SINT5       | HV_X64_MSR_SINT5                                             |
| 0x40001096       | HV_X64_MSR_NESTED_SINT6       | HV_X64_MSR_SINT6                                             |
| 0x40001097       | HV_X64_MSR_NESTED_SINT7       | HV_X64_MSR_SINT7                                             |
| 0x40001098       | HV_X64_MSR_NESTED_SINT8       | HV_X64_MSR_SINT8                                             |
| 0x40001099       | HV_X64_MSR_NESTED_SINT9       | HV_X64_MSR_SINT9                                             |
| 0x4000109A       | HV_X64_MSR_NESTED_SINT10      | HV_X64_MSR_SINT10                                            |
| 0x4000109B       | HV_X64_MSR_NESTED_SINT11      | HV_X64_MSR_SINT11                                            |
| 0x4000109C       | HV_X64_MSR_NESTED_SINT12      | HV_X64_MSR_SINT12                                            |
| 0x4000109D       | HV_X64_MSR_NESTED_SINT13      | HV_X64_MSR_SINT13                                            |
| 0x4000109E       | HV_X64_MSR_NESTED_SINT14      | HV_X64_MSR_SINT14                                            |
| 0x4000109F       | HV_X64_MSR_NESTED_SINT15      | HV_X64_MSR_SINT15                                            |
