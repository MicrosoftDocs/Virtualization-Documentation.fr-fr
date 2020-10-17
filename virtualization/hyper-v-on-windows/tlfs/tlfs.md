---
title: Spécification de l’hyperviseur
description: Spécification de l’hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 97615dc78f31a3a619130abb5a4d3786fdd412b2
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120564"
---
# <a name="hypervisor-top-level-functional-specification"></a>Spécification fonctionnelle de niveau supérieur de l’hyperviseur

L’hyperviseur Hyper-V Top-Level spécification fonctionnelle (Téléchargements) décrit le comportement visible par l’invité de l’hyperviseur à d’autres composants du système d’exploitation. Cette spécification est destinée aux développeurs des systèmes d’exploitation invités.

> Elle est fournie dans le cadre de Microsoft Open Specification Promise.  Consultez les informations suivantes pour plus de détails sur [Microsoft Open Specification Promise](https://docs.microsoft.com/openspecs/dev_center/ms-devcentlp/51a0d3ff-9f77-464c-b83f-2de08ed28134).

Microsoft peut disposer de brevets, d’applications de brevets, de marques, de droits d’auteur ou d’autres droits de propriété intellectuelle couvrant les sujets abordés dans ces documents. Sauf stipulation expresse contraire de la promesse de spécification Microsoft Open, la fourniture de ces documents ne vous confère aucune licence sur ces brevets, marques, droits d’auteur ou autres droits de propriété intellectuelle.

## <a name="glossary"></a>Glossaire

- **Partition** : Hyper-V prend en charge l’isolement en termes de partition. Une partition est une unité logique d’isolation, prise en charge par l’hyperviseur, dans laquelle des systèmes d’exploitation s'exécutent.
- **Partition racine** : la partition racine (a. k. a « parent » ou « Host ») est une partition de gestion privilégiée. La partition racine gère les fonctions au niveau de l’ordinateur, telles que les pilotes de périphériques, la gestion de l’alimentation et l’ajout/la suppression d’appareils. La pile de virtualisation s’exécute dans la partition parente et dispose d’un accès direct aux périphériques matériels. La partition racine crée ensuite des partitions enfants qui hébergent les systèmes d’exploitation invités.
- **Partition enfant** : la partition enfant (également appelée l’invité (Guest) héberge un système d’exploitation invité. Tout accès à la mémoire physique et aux périphériques par une partition enfant est assuré via le bus d’ordinateurs virtuels (VMBus) ou l’hyperviseur.
- **Hypercall** : les hyperappels sont une interface pour la communication avec l’hyperviseur.

## <a name="specification-style"></a>Style de spécification

Le document suppose une bonne connaissance de l’architecture hyperviseur de haut niveau.

Cette spécification est informelle. autrement dit, les interfaces ne sont pas spécifiées dans un langage formel. Toutefois, il s’agit d’un objectif de précision. L’objectif est également de spécifier les comportements qui sont architecturaux et qui sont spécifiques à l’implémentation. Les appelants ne doivent pas s’appuyer sur les comportements qui se trouvent dans la dernière catégorie, car ils peuvent changer dans les implémentations ultérieures.

## <a name="previous-versions"></a>Versions antérieures

Libérer | Document
--- | ---
Windows Server 2019 (révision B) | [Spécification fonctionnelle de niveau supérieur de l’hyperviseur v6.0b.pdf](https://github.com/MicrosoftDocs/Virtualization-Documentation/raw/master/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v6.0b.pdf)
Windows Server 2016 (révision C) | [Spécification fonctionnelle de niveau supérieur de l’hyperviseur v5.0c.pdf](https://github.com/MicrosoftDocs/Virtualization-Documentation/raw/live/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v5.0C.pdf)
Windows Server 2012 R2 (révision B) | [Hypervisor Top Level Functional Specification v4.0b.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v4.0b.pdf) (Spécification fonctionnelle générale de l’hyperviseur)
Windows Server 2012 | [Hypervisor Top Level Functional Specification v3.0.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v3.0.pdf) (Spécification fonctionnelle générale de l’hyperviseur)
Windows Server 2008 R2 | [Hypervisor Top Level Functional Specification v2.0.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Hypervisor%20Top%20Level%20Functional%20Specification%20v2.0.pdf) (Spécification fonctionnelle générale de l’hyperviseur)

## <a name="requirements-for-implementing-the-microsoft-hypervisor-interface"></a>Configuration requise pour l’implémentation de l’interface de l’hyperviseur Microsoft

Le téléchargements décrit entièrement tous les aspects de l’architecture hyperviseur spécifique à Microsoft, qui est déclarée aux machines virtuelles invitées comme l’interface « n ° 1 ».  Toutefois, toutes les interfaces décrites dans le téléchargements ne doivent pas être implémentées par un hyperviseur tiers souhaitant déclarer la conformité avec la spécification de l’hyperviseur Microsoft. Le document « exigences relatives à l’implémentation de l’interface de l’hyperviseur Microsoft » décrit l’ensemble minimal d’interfaces hyperviseur qui doivent être implémentées par un hyperviseur qui affirme la compatibilité avec l’interface Microsoft de l’aide-du-1.

[Requirements for Implementing the Microsoft Hypervisor Interface.pdf](https://github.com/Microsoft/Virtualization-Documentation/raw/master/tlfs/Requirements%20for%20Implementing%20the%20Microsoft%20Hypervisor%20Interface.pdf) (Configuration requise pour l’implémentation de l’interface de l’hyperviseur Microsoft)