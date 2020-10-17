---
title: Types de données
description: Types de données hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 068d1c93d42422ac0c255c7201178a33040f3b47
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120607"
---
# <a name="data-types"></a>Types de données

## <a name="reserved-values"></a>Valeurs réservées

Cette spécification documente certains champs comme « réservés ». Ces champs peuvent avoir une signification particulière dans les futures versions de l’architecture hyperviseur. Pour une compatibilité ascendante maximale, les clients de l’interface hyperviseur doivent suivre les instructions fournies dans ce document. En général, deux formes d’instructions sont fournies. Conserver la valeur (documenté comme RsvdP dans les schémas et ReservedP dans les segments de code) : pour une compatibilité ascendante maximale, les clients doivent conserver la valeur dans ce champ. Cela s’effectue généralement en lisant la valeur actuelle, en modifiant les valeurs des champs non réservés et en réécrivant la valeur. Valeur zéro (documentée en tant que RsvdZ dans les schémas et ReservedZ dans les segments de code) : pour une compatibilité ascendante maximale, les clients doivent avoir une valeur égale à zéro dans ce champ.

Les champs réservés dans les structures en lecture seule sont simplement documentés en tant que RSVD dans les diagrammes et tout simplement réservés dans les segments de code. Pour une compatibilité ascendante maximale, les valeurs de ces champs doivent être ignorées. Les clients ne doivent pas supposer que ces valeurs seront toujours égales à zéro.

## <a name="simple-scalar-types"></a>Types scalaires simples

Les types de données hyperviseur sont créés à partir de types scalaires simples UINT8, UINT16, UINT32, UINT64 et UINT128. Chacun d’entre eux représente un type d’entier non signé simple avec le nombre de bits spécifié. Plusieurs scalaires entières signées correspondantes sont également définies : INT8, INT16, INT32 et INT64.
L’hyperviseur n’utilise ni les instructions à virgule flottante, ni les types à virgule flottante.

## <a name="hypercall-status-code"></a>Code d’État hypercall

Chaque hypercall retourne un code d’État 16 bits de type HV_STATUS.

 ```c
typedef UINT16 HV_STATUS;
 ```

## <a name="memory-address-space-types"></a>Types d’espace d’adressage de mémoire

L’architecture hyperviseur définit trois espaces d’adressage indépendants :

- Les **adresses physiques système** définissent l’espace d’adressage physique du matériel sous-jacent, tel qu’il est vu par les processeurs. Il n’existe qu’un seul espace d’adressage physique système pour l’ensemble de l’ordinateur.
- Les **adresses physiques invitées (GPAS)** définissent la vue de la mémoire physique de l’invité. GPAs peut être mappé à la sous-jacente de la vue. Il y a un espace d’adressage physique invité par partition.
- Les **adresses virtuelles invitées (GVAs)** sont utilisées dans l’invité lorsqu’elle active la traduction d’adresses et fournit une table des pages invitées valide.

Les trois espaces d’adressage ont une taille maximale de 264 octets. Les types suivants sont donc définis :

 ```c
typedef UINT64 HV_SPA;
typedef UINT64 HV_GPA;
typedef UINT64 HV_GVA;
 ```

De nombreuses interfaces hyperviseur agissent sur des pages de mémoire plutôt que sur des octets uniques. La taille de page minimale dépend de l’architecture. Pour x64, elle est définie comme 4K.

 ```c
#define X64_PAGE_SIZE 0x1000

#define HV_X64_MAX_PAGE_NUMBER (MAXUINT64/X64_PAGE_SIZE)
#define HV_PAGE_SIZE X64_PAGE_SIZE
#define HV_LARGE_PAGE_SIZE X64_LARGE_PAGE_SIZE
#define HV_PAGE_MASK (HV_PAGE_SIZE - 1)

typedef UINT64 HV_SPA_PAGE_NUMBER;
typedef UINT64 HV_GPA_PAGE_NUMBER;
typedef UINT64 HV_GVA_PAGE_NUMBER;
typedef UINT32 HV_SPA_PAGE_OFFSET
typedef HV_GPA_PAGE_NUMBER *PHV_GPA_PAGE_NUMBER;
 ```

Pour convertir en `HV_SPA` un `HV_SPA_PAGE_NUMBER` , il suffit de diviser par `HV_PAGE_SIZE` .

## <a name="structures-enumerations-and-bit-fields"></a>Structures, énumérations et champs de bits

De nombreuses structures de données et valeurs constantes définies plus loin dans cette spécification sont définies en termes d’énumérations et de structures de style C. Le langage C évite de définir certains détails d’implémentation. Toutefois, ce document suppose ce qui suit :

- Toutes les énumérations déclarées avec le mot clé « enum » définissent des valeurs entières signées 32 bits.
- Toutes les structures sont complétées de manière à ce que les champs soient alignés naturellement (autrement dit, un champ de 8 octets est aligné sur un décalage de 8 octets, etc.).
- Tous les champs de bits sont empaquetés des bits de poids faible aux bits de poids fort sans remplissage.

## <a name="endianness"></a>Endianness

L’interface hyperviseur est conçue pour être indépendante de l’endian (autrement dit, il doit être possible de déplacer l’hyperviseur vers un système Big endian ou Little-endian), mais certaines structures de données définies plus loin dans cette spécification supposent une disposition de faible endian. Ces structures de données devront être modifiées si et quand un port Big-endian est tenté.

## <a name="pointer-naming-convention"></a>Convention d’affectation des noms de pointeur

Le document utilise une convention d’affectation de noms pour les types pointeur. En particulier, un « P » ajouté à un type défini indique un pointeur vers ce type. Un « PC » ajouté à un type défini indique un pointeur vers une valeur constante de ce type.