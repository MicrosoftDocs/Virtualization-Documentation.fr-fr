---
title: MMU virtuel
description: Interface MMU virtuelle de l’hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 7bfb7ba5426df0192bf142a2fc4e492da2fa21ef
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120487"
---
# <a name="virtual-mmu"></a>MMU virtuel

L’interface de machine virtuelle exposée par chaque partition comprend une unité de gestion de la mémoire (MMU). Les MMU virtuels exposés par les partitions hyperviseur sont généralement compatibles avec les MMUs existants.

## <a name="virtual-mmu-overview"></a>Vue d’ensemble des MMU virtuels

Les processeurs virtuels exposent la mémoire virtuelle et un TLB virtuel (la mémoire tampon de recherche de la traduction), qui met en cache les traductions des adresses virtuelles vers des adresses physiques (invitées). Comme avec le TLB sur un processeur logique, le TLB virtuel est un cache non cohérent, et cette non-cohérence est visible pour les invités. L’hyperviseur expose les opérations pour vider le TLB. Les invités peuvent utiliser ces opérations pour supprimer les entrées potentiellement incohérentes et rendre les traductions d’adresses virtuelles prévisibles.

### <a name="compatibility"></a>Compatibilité

Les MMU virtuels exposés par l’hyperviseur sont généralement compatibles avec les MMU physiques trouvés au sein d’un processeur x64. Les différences observables suivantes existent :

- CR3. PWT et CR3. Les bits PCD ne sont peut-être pas pris en charge dans certaines implémentations d’hyperviseur. Sur de telles implémentations, toute tentative de l’invité pour définir ces indicateurs via une instruction MOV à CR3 ou un commutateur de porte de tâche est ignorée. Toute tentative de définition de ces bits par programmation via HvSetVpRegisters ou HvSwitchVirtualAddressSpace peut provoquer une erreur.
- Les bits PWT et PCD dans une entrée de table de pages feuille (par exemple, une PTE pour 4-K pages et un PDE pour les grandes pages) spécifient la mise en cache de la page mappée. Les bits PAT, PWT et PCD dans les entrées de table de pages non-feuille indiquent la capacité de mise en cache de la table de page suivante dans la hiérarchie. Certaines implémentations d’hyperviseur ne prennent peut-être pas en charge ces indicateurs. Sur de telles implémentations, tous les accès aux tables de pages effectuées par l’hyperviseur sont effectués à l’aide des attributs du cache en écriture différée. Cela affecte, en particulier, les bits consultés et incorrects écrits dans les entrées de la table de pages. Si l’invité définit les bits PAT, PWT ou PCD dans des entrées de table de pages non-feuille, un message « fonctionnalité non prise en charge » peut être généré lorsqu’un processeur virtuel accède à une page mappée par cette table de pages.
- Registre CR0. Le bit de CD (cache désactivé) n’est peut-être pas pris en charge dans certaines implémentations d’hyperviseur. Sur de telles implémentations, registre CR0. Le bit CD doit avoir la valeur 0. Toute tentative de l’invité de définir cet indicateur via une instruction MOV à registre CR0 sera ignorée. Toute tentative de définition de ce bit par programmation via HvSetVpRegisters génère une erreur.
- La partition PAT (type d’adresse de page) MSR est un registre par VP. Toutefois, lorsque tous les processeurs virtuels d’une partition définissent la valeur de PAT MSR sur la même valeur, le nouvel effet devient un effet à l’ensemble de la partition.
- Pour des raisons de sécurité et d’isolation, l’instruction FAC est virtualisée pour agir comme une instruction WBINVD, avec quelques différences. Pour des raisons de sécurité, CLFLUSH doit être utilisé à la place.

### <a name="legacy-tlb-management-operations"></a>Opérations de gestion TLB héritées

L’architecture x64 offre plusieurs moyens de gérer le TLBs du processeur. Les mécanismes suivants sont virtualisés par l’hyperviseur :

- L’instruction INVLPG invalide la traduction pour une seule page à partir du TLB du processeur. Si l’adresse virtuelle spécifiée a été initialement mappée en tant que page de 4 Ko, la traduction de cette page est supprimée de l’TLB. Si l’adresse virtuelle spécifiée a été mappée à l’origine en tant que « grande page » (soit 2 Mo soit 4 Mo, selon le mode MMU), la traduction de la grande page est supprimée de l’TLB. L’instruction INVLPG vide les traductions globales et non globales. Les traductions globales sont définies comme celles pour lesquelles le bit « global » est défini dans l’entrée de la table de pages.
- Les commutateurs de tâches et d’instructions MOV à CR3 qui modifient CR3 invalident les traductions pour toutes les pages non globales dans le TLB du processeur.
- Instruction MOV à CR4 qui modifie la CR4. PGE (Global page Enable) bit, CR4. Les extensions PSE (page size extensions) ou CR4. Le bit PAE (page Address extensions) invalide toutes les traductions (globales et non globales) dans le TLB du processeur.

Notez que toutes ces opérations d’invalidation n’affectent qu’un seul processeur. Pour invalider des traductions sur d’autres processeurs, le logiciel doit utiliser un mécanisme de « prise en compte des TLB » basé sur logiciel (généralement implémenté à l’aide d’interruptions inter-processus).

### <a name="virtual-tlb-enhancements"></a>Améliorations des TLB virtuels

En plus de prendre en charge les mécanismes de gestion TLB hérités décrits précédemment, l’hyperviseur prend également en charge un ensemble d’améliorations qui permettent à un invité de gérer plus efficacement le TLB virtuel. Ces opérations améliorées peuvent être utilisées de façon interchangeable avec les opérations de gestion TLB héritées.

L’hyperviseur prend en charge les hyperappels suivants pour invalider TLBs :

| Hypercall                                                                           | Description                                     |
|-------------------------------------------------------------------------------------|-------------------------------------------------|
| [HvCallFlushVirtualAddressSpace](hypercalls/HvCallFlushVirtualAddressSpace.md)      | Invalide toutes les entrées TLB virtuelles qui appartiennent à un espace d’adressage spécifié.    |
| [HvCallFlushVirtualAddressSpaceEx](hypercalls/HvCallFlushVirtualAddressSpaceEx.md)  | Semblable à HvCallFlushVirtualAddressSpace, prend un VP épars défini comme entrée.    |
| [HvCallFlushVirtualAddressList](hypercalls/HvCallFlushVirtualAddressList.md)        | Invalide une partie de l’espace d’adressage spécifié.    |
| [HvCallFlushVirtualAddressListEx](hypercalls/HvCallFlushVirtualAddressListEx.md)    | Semblable à HvCallFlushVirtualAddressList, prend un VP épars défini comme entrée.    |

Sur certains systèmes (ceux dotés d’une prise en charge de virtualisation suffisante dans le matériel), les instructions de gestion des TLB héritées peuvent être plus rapides pour l’invalidation des TLB locaux ou distants (inter-processeurs). Les invités qui sont intéressés par des performances optimales doivent utiliser le 0x40000004 de la feuille CPUID pour déterminer les comportements à implémenter à l’aide des hyperappels :

- UseHypercallForAddressSpaceSwitch : si cet indicateur est défini, l’appelant doit supposer qu’il est plus rapide d’utiliser HvCallSwitchAddressSpace pour basculer entre les espaces d’adressage. Si cet indicateur est désactivé, une instruction MOV à CR3 est recommandée.
- UseHypercallForLocalFlush : si cet indicateur est défini, l’appelant doit supposer qu’il est plus rapide d’utiliser des hyperappels (par opposition à INVLPG ou MOV à CR3) pour vider une ou plusieurs pages du TLB virtuel.
- UseHypercallForRemoteFlushAndLocalFlushEntire : si cet indicateur est défini, l’appelant doit supposer qu’il est plus rapide d’utiliser des hyperappels (par opposition à des interruptions inter-processeurs générées par l’invité) pour vider une ou plusieurs pages du TLB virtuel.

## <a name="memory-cache-control-overview"></a>Vue d’ensemble du contrôle de cache mémoire

L’hyperviseur prend en charge les paramètres de mise en cache définis par l’invité pour les pages mappées dans l’espace GVA de l’invité. Pour obtenir une description détaillée des paramètres de mise en cache disponibles et de leurs significations, reportez-vous à la documentation Intel ou AMD.

Lorsqu’un processeur virtuel accède à une page via son espace GVA, l’hyperviseur honore les bits d’attribut de cache (PAT, PWT et PCD) dans l’entrée de table de pages invitée utilisée pour mapper la page. Ces trois bits sont utilisés comme index dans le registre PAT de la partition (type d’adresse de la page) pour rechercher le paramètre de mise en cache final pour la page.

Les pages sont accessibles directement par le biais de l’espace GPA (par exemple, lorsque la pagination est désactivée, car Registre CR0. PG est effacé) utilisez une capacité de mise en cache définie par MTRRs. Si l’implémentation de l’hyperviseur ne prend pas en charge les MTRRs virtuels, WB Caching est pris en charge.

### <a name="mixing-cache-types-between-a-partition-and-the-hypervisor"></a>Mélange de types de cache entre une partition et l’hyperviseur

Les invités doivent savoir que certaines pages de l’espace de l’amp sont accessibles par l’hyperviseur. La liste suivante, et non exhaustive, fournit plusieurs exemples :

- page qui contient les paramètres d’entrée ou de sortie d’un hypercall
- Toutes les pages de superposition, y compris la page hypercall, les pages SynIC SIEF et SIM et les pages de statistiques

L’hyperviseur effectue toujours les accès aux paramètres hypercall et aux pages de superposition en utilisant le paramètre de mise en cache WB.