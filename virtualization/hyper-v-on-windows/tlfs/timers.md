---
title: Minuteries
description: Minuteurs d’hyperviseur
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: acd5139517194db4f790f27e8b5cf12b3eac1749
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120719"
---
# <a name="timers"></a>Minuteries

L’hyperviseur fournit des services de minutage simples. Elles sont basées sur une source de temps de référence à taux constant (généralement le minuteur ACPI sur les systèmes x64).

Les services de minuteur suivants sont fournis :

- Compteur de temps de référence par partition.
- Quatre minuteries synthétiques par processeur virtuel. Chaque minuteur synthétique est un minuteur unique ou périodique qui remet un message ou déclare une interruption lorsqu’il expire.
- Un minuteur APIC virtuel par processeur virtuel.
- Une disponibilité de la référence de partition, basée sur la prise en charge de la plateforme hôte pour un compteur d’horodatage invariant (iTSC).

## <a name="reference-counter"></a>Compteur de références

L’hyperviseur gère un compteur de temps de référence par partition. Il présente la caractéristique selon laquelle les accès successifs à ce dernier renvoient des valeurs (temps) strictement monotones, telles qu’elles apparaissent par tous les processeurs virtuels d’une partition. En outre, le compteur de référence est une constante de vitesse et n’est pas affecté par les transitions de vitesse du processeur ou du bus ou les États d’économie d’énergie du processeur. Le compteur de temps de référence d’une partition est initialisé à zéro lorsque la partition est créée. Le compteur de référence pour toutes les partitions est compté à la même vitesse, mais à tout moment, leurs valeurs absolues diffèrent généralement, car les partitions auront des heures de création différentes.

Le compteur de références continue à compter tant qu’au moins un processeur virtuel n’est pas explicitement suspendu.

### <a name="partition-reference-counter-msr"></a>Compteur de référence de partition MSR

Le compteur de référence d’une partition est accessible via un MSR à l’ensemble de la partition.

| Adresse MSR      | Nom du Registre              | Description                                                                 |
|------------------|----------------------------|-----------------------------------------------------------------------------|
| 0x40000020       | HV_X64_MSR_TIME_REF_COUNT  | Nombre de références (au niveau de la partition)                                       |

Lors de la création d’une partition, la valeur de la TIME_REF_COUNT MSR est définie sur 0x0000000000000000. Cette valeur ne peut pas être modifiée par un processeur virtuel. Toute tentative d’écriture génère une erreur de #GP.

### <a name="partition-reference-time-enlightenment"></a>Délai de référence de la partition

L’indisponibilité de la référence de la partition présente une source de temps de référence à une partition qui ne nécessite pas d’interception dans l’hyperviseur. Cet accès est disponible uniquement lorsque la plateforme sous-jacente prend en charge un compteur d’horodatage de processeur invariant (TSC) ou iTSC. Dans ces plateformes, la fréquence TSC du processeur reste constante, quelle que soit la fréquence d’horloge du processeur, en raison de l’utilisation d’États de gestion de l’alimentation tels que les États des performances du processeur ACPI, les États de veille du processeur (États C ACPI), etc.

L’indisponibilité de la référence de la partition utilise une valeur TSC virtuelle, un décalage et un multiplicateur pour permettre à une partition invitée de calculer le temps de référence normalisé depuis la création de la partition, en unités de 100 ns. Le mécanisme permet également à une partition invitée de calculer atomiquement le temps de référence lorsque la partition invitée est migrée vers une plateforme avec un taux TSC différent, et fournit un mécanisme de secours pour prendre en charge la migration vers les plateformes sans la fonctionnalité TSC à débit constant.

Cette fonctionnalité n’est pas destinée à être utilisée comme source de temps horloge, étant donné que la durée de référence calculée à l’aide de cette fonction s’arrêtera pendant la durée d’enregistrement d’une partition invitée jusqu’à la restauration suivante.

#### <a name="partition-reference-time-stamp-counter-page"></a>Page compteur de l’horodatage de la référence de partition

L’hyperviseur fournit une page TSC de référence virtuelle à l’emplacement de la partition qui est superposée à l’espace des GPA de la partition. La page du compteur d’horodatage de référence d’une partition est accessible via le TSC du TSC de référence.

La page de référence TSC est définie à l’aide de la structure suivante :

 ```c
typedef struct
{
    volatile UINT32 TscSequence;
    UINT32 Reserved1;
    volatile UINT64 TscScale;
    volatile INT64 TscOffset;
    UINT64 Reserved2[509];
} HV_REFERENCE_TSC_PAGE;
 ```

#### <a name="reference-time-stamp-counter-tsc-page-msr"></a>Compteur d’horodatage de référence (TSC), page MSR

Un invité souhaitant accéder à sa page de référence TSC doit utiliser le Registre spécifique au modèle (MSR) suivant. Une partition qui possède le privilège AccessPartitionReferenceTsc peut accéder à la MSR.

| Adresse MSR      | Nom du Registre              | Description                                                                 |
|------------------|----------------------------|-----------------------------------------------------------------------------|
| 0x40000021       | HV_X64_MSR_REFERENCE_TSC   | Page de référence TSC                                                          |

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 63:12         | Numéro de page GPA                     | Lecture/écriture                                                |
| 11:1          | RsvdP (la valeur doit être conservée)   | Lecture/écriture                                                |
| 0             | Activer                              | Lecture/écriture                                                |

Au moment de la création de la partition invitée, la valeur du TSC de référence est 0x0000000000000000. Par conséquent, la page de référence TSC est désactivée par défaut. L’invité doit activer la page de référence TSC en définissant le bit 0. Si l’adresse de base spécifiée se situe au-delà de la fin de l’espace de la moyenne de la partition, la page de référence TSC n’est pas accessible à l’invité. Lors de la modification du Registre, les invités doivent conserver la valeur des bits réservés (1 à 11) pour assurer une compatibilité future.

#### <a name="partition-reference-tsc-mechanism"></a>Mécanisme TSC de référence de partition

Le temps de référence de la partition est calculé par la formule suivante :

ReferenceTime = ((VirtualTsc * TscScale)  >> 64) + TscOffset

La multiplication est une multiplication de 64 bits, qui produit un nombre de 128 bits qui est ensuite décalé de 64 fois vers la droite pour obtenir les bits de 64 élevés.

La valeur TscScale est utilisée pour ajuster la valeur TSC virtuelle entre des événements de migration afin d’atténuer les changements de fréquence TSC d’une plateforme à l’autre.

La valeur TscSequence est utilisée pour synchroniser l’accès au temps de référence activé si les champs Scale et/ou offset sont modifiés pendant l’enregistrement, la restauration ou la migration dynamique. Ce champ sert de numéro de séquence qui est incrémenté chaque fois que les champs Scale et/ou offset sont modifiés. La valeur spéciale 0x0 est utilisée pour indiquer que cette fonctionnalité n’est plus une source fiable de temps de référence et que la machine virtuelle doit revenir à une autre source.

Le code recommandé pour calculer le temps de référence de la partition à l’aide de cette recommandation est illustré ci-dessous :

```c
do
{
    StartSequence = ReferenceTscPage->TscSequence;
    if (StartSequence == 0)
    {
        // 0 means that the Reference TSC enlightenment is not available at
        // the moment, and the Reference Time can only be obtained from
        // reading the Reference Counter MSR.
        ReferenceTime = rdmsr(HV_X64_MSR_TIME_REF_COUNT);
        return ReferenceTime;
    }

    Tsc = rdtsc();

    // Assigning Scale and Offset should neither happen before
    // setting StartSequence, nor after setting EndSequence.
    Scale = ReferenceTscPage->TscScale;
    Offset = ReferenceTscPage->TscOffset;

    EndSequence = ReferenceTscPage->TscSequence;
} while (EndSequence != StartSequence);

// The result of the multiplication is treated as a 128-bit value.
ReferenceTime = ((Tsc * Scale) >> 64) + Offset;
return ReferenceTime;
```

## <a name="synthetic-timers"></a>Minuteurs synthétiques

Les minuteurs synthétiques fournissent un mécanisme permettant de générer une interruption après une période spécifiée à l’avenir. Les minuteurs à une seule capture et périodiques sont pris en charge. Une minuterie synthétique envoie un message à un SINTx SynIC spécifié (source d’interruptions synthétiques) lors de l’expiration, ou déclare une interruption, en fonction de la façon dont elle est configurée.

L’hyperviseur garantit qu’un signal d’expiration du minuteur ne sera jamais remis avant l’heure d’expiration. Le signal peut arriver à tout moment après l’heure d’expiration.

### <a name="periodic-timers"></a>Minuteurs périodiques

L’hyperviseur tente de signaler régulièrement des minuteries périodiques. Toutefois, si le processeur virtuel utilisé pour signaler l’expiration n’est pas disponible, certaines des expirations du minuteur peuvent être retardées. Un processeur virtuel peut ne pas être disponible, car il est suspendu (par exemple, pendant la gestion des interceptions) ou parce que le planificateur de l’hyperviseur a décidé que le processeur virtuel ne doit pas être planifié sur un processeur logique (par exemple, parce qu’un autre processeur virtuel utilise le processeur logique ou que le processeur virtuel a dépassé son quota).

Si un processeur virtuel n’est pas disponible pendant une période suffisamment longue, une période de minuteur complète peut être manquée. Dans ce cas, l’hyperviseur utilise l’une des deux techniques.

La première technique implique la modulation de la période de minuterie, en effet la réduction de la période jusqu’à ce que la minuterie « rattrape ». Si un nombre significatif de signaux de minuteur ont été manqués, l’hyperviseur peut ne pas être en mesure de compenser à l’aide de la modulation de période. Dans ce cas, certains signaux d’expiration du minuteur peuvent être ignorés complètement.

Pour les minuteurs marqués comme paresseux, l’hyperviseur utilise une deuxième technique pour traiter la situation dans laquelle un processeur virtuel est indisponible pendant une longue période de temps. Dans ce cas, le signal du minuteur est différé jusqu’à ce que ce processeur virtuel soit disponible. S’il n’est pas disponible avant l’expiration du minuteur suivant, il est ignoré entièrement.

### <a name="ordering-of-timer-expirations"></a>Ordonnancement des expirations de minuterie

Les minuteurs synthétiques et virtualisés génèrent des interruptions à leur heure d’expiration spécifiée ou près. En raison du matériel et des autres interactions de planification, les interruptions peuvent potentiellement être retardées. Aucun ordre n’est supposé entre un ensemble de minuteries.

### <a name="direct-synthetic-timers"></a>Minuteurs synthétiques directs

Les minuteurs synthétiques « directs » déclarent une interruption en cas d’expiration du minuteur au lieu d’envoyer un message à une source d’interruption synthétique SynIc. Un minuteur synthétique est défini en mode « direct » en définissant le champ « DirectMode » de la configuration du minuteur synthétique MSRs. Le champ « ApicVector » contrôle le vecteur d’interruption qui est déclaré lors de l’expiration du minuteur.

### <a name="synthetic-timer-msrs"></a>Minuteur synthétique MSRs

Les minuteurs synthétiques sont configurés à l’aide de registres spécifiques au modèle (MSRs) associés à chaque processeur virtuel. Chacun des quatre minuteurs synthétiques a une paire associée de MSRs.

| Adresse MSR      | Nom du Registre                   | Description                                                    |
|------------------|---------------------------------|----------------------------------------------------------------|
| 0x400000B0       | HV_X64_MSR_STIMER0_CONFIG       | Registre de configuration pour le minuteur synthétique 0.                  |
| 0x400000B1       | HV_X64_MSR_STIMER0_COUNT        | Délai d’expiration ou période pour le minuteur synthétique 0.               |
| 0x400000B2       | HV_X64_MSR_STIMER1_CONFIG       | Registre de configuration pour le minuteur synthétique 1.                  |
| 0x400000B3       | HV_X64_MSR_STIMER1_COUNT        | Délai d’expiration ou période pour le minuteur synthétique 1.               |
| 0x400000B4       | HV_X64_MSR_STIMER2_CONFIG       | Registre de configuration pour le minuteur synthétique 2.                  |
| 0x400000B5       | HV_X64_MSR_STIMER2_COUNT        | Délai d’expiration ou période pour le minuteur synthétique 2.               |
| 0x400000B6       | HV_X64_MSR_STIMER3_CONFIG       | Registre de configuration pour le minuteur synthétique 3.                  |
| 0x400000B7       | HV_X64_MSR_STIMER4_COUNT        | Heure ou période d’expiration du minuteur synthétique 3.               |

#### <a name="synthetic-timer-configuration-register"></a>Registre de configuration du minuteur synthétique

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 63:20         | RsvdZ (la valeur doit être égale à zéro) | Lecture/écriture                                                |
| 19:16         | SINTx-source d’interruption synthétique  | Lecture/écriture                                                |
| 15:13         | RsvdZ (la valeur doit être égale à zéro) | Lecture/écriture                                                |
| 12            | Mode direct-assertion et interruption lors de l’expiration du minuteur. | Lecture/écriture                          |
| 11:4          | ApicVector : contrôle le vecteur d’interruption déclaré en mode direct | Lecture/écriture                 |
| 3             | Autoenable-défini si l’écriture du compteur correspondant est implicitement activée | Lecture/écriture |
| 2             | Lazy-Set si le minuteur est paresseux          | Lecture/écriture                                               |
| 1             | Périodique-définir si le minuteur est périodique  | Lecture/écriture                                               |
| 0             | Activé : définit si la minuterie est activée    | Lecture/écriture                                               |

Lors de la création et de la réinitialisation d’un processeur virtuel, la valeur de tous les registres HV_X64_MSR_STIMERx_CONFIG (configuration du minuteur synthétique) est définie sur 0x0000000000000000. Par conséquent, tous les minuteurs synthétiques sont désactivés par défaut.

Si l’activation automatique est définie, l’écriture d’une valeur différente de zéro dans le registre de nombre correspondant entraînera la définition de Enable et l’activation du compteur. Dans le cas contraire, Enable doit être défini après l’écriture du registre de nombre correspondant afin d’activer le compteur. Pour plus d’informations sur le registre de nombre, consultez la section suivante.

Lorsqu’un minuteur se termine, il est automatiquement marqué comme désactivé. Les minuteurs périodiques restent activés jusqu’à ce qu’ils soient explicitement désactivés.

Si un seul cliché est activé et que le nombre spécifié se trouve dans le passé, il expire immédiatement.

Il n’est pas autorisé de définir le champ SINTx sur zéro pour un minuteur activé (qui n’est pas en mode direct). En cas de tentative, la minuterie est marquée comme désactivée (c’est-à-dire le bit 0 effacé) immédiatement.

L’écriture du registre de configuration d’un minuteur qui est déjà activé peut entraîner un comportement indéfini. Par exemple, il se peut que la simple modification d’un minuteur d’un seul coup à périodique ne produise pas ce qui est prévu. Les minuteurs doivent toujours être désactivés avant de modifier d’autres propriétés.

#### <a name="synthetic-timer-count-register"></a>Registre du nombre de minuteries synthétiques

| Bits          | Description                                                             | Attributs              |
|---------------|-------------------------------------------------------------------------|-------------------------|
| 63:0          | Nombre : délai d’expiration pour les minuteurs à une seule prise, durée des minuteries périodiques | Lecture/écriture            |

La valeur programmée dans le registre de nombre est une valeur de temps mesurée en unités de nanosecondes 100. L’écriture de la valeur zéro dans le registre de nombre arrêtera le compteur, désactivant ainsi la minuterie, indépendamment du paramètre d’activation automatique dans le registre de configuration.

Notez que le registre de nombre est autorisé à encapsuler. L’encapsulage n’a aucun effet sur le comportement du minuteur, quelle que soit la propriété de la minuterie.

Pour les minuteurs à une seule capture, il représente le temps d’expiration absolu de la minuterie. Le minuteur expire lorsque le compteur de référence pour la partition est supérieur ou égal à la valeur de nombre spécifiée.

Pour les minuteries périodiques, le nombre représente la période de la minuterie. La première période commence lorsque la minuterie synthétique est activée.

### <a name="synthetic-timer-expiration-message"></a>Message d’expiration du minuteur synthétique

Les messages d’expiration du minuteur sont envoyés lorsqu’un événement de minuterie se déclenche. Reportez-vous à la [HV_TIMER_MESSAGE_PAYLOAD](datatypes/HV_TIMER_MESSAGE_PAYLOAD.md) pour la définition de la charge utile de message.

### <a name="synthetic-time-unhalted-timer-msrs"></a>Minuteur de Time-Unhalted synthétique MSRs

Le minuteur de Time-Unhalted synthétique est disponible si une partition a le privilège AccessSyntheticTimerRegs et le bit 23 du Registre EDX dans la fonctionnalité d’hyperviseur, l’identification de la feuille 0x40000003 est définie. Le logiciel invité peut programmer le minuteur de temps synthétique non interrompu pour générer une interruption périodique après l’exécution pendant une durée spécifiée en unités de 100 ns. Lorsque l’interruption est déclenchée, le champ SyntheticTimeUnhaltedTimerExpired de la page d’assistance du VP sera défini sur TRUE. Le logiciel invité peut réinitialiser ce champ à FALSe. Contrairement aux compteurs de performance architecturaux, la minuterie synthétique n’est jamais réinitialisée par l’hyperviseur et s’exécute en continu entre les interruptions. Vectors = = 2 envoie un NMI, d’autres vecteurs envoient une interruption fixe.

Contrairement aux minuteries synthétiques standard qui accumulent du temps lorsque l’invité s’est arrêté (IE : devient inactif), le minuteur de Time-Unhalted synthétique accumule du temps uniquement lorsque l’invité n’est pas interrompu.

| Adresse MSR      | Nom du Registre                          | Description                                             |
|------------------|----------------------------------------|---------------------------------------------------------|
| 0x40000114       | HV_X64_MSR_STIME_UNHALTED_TIMER_CONFIG | Configuration du minuteur de Time-Unhalted synthétique MSR         |
| 0x40000115       | HV_X64_MSR_STIME_UNHALTED_TIMER_COUNT  | Nombre de minuteurs de Time-Unhalted synthétiques MSR                 |

#### <a name="synthetic-time-unhalted-timer-configuration-msr"></a>Configuration du minuteur de Time-Unhalted synthétique MSR

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 63:9          | RsvdZ (la valeur doit être égale à zéro) | Lecture/écriture                                                |
| 8             | Activé                             | Lecture/écriture                                                |
| 7:0           | Vecteur                              | Lecture/écriture                                                |

#### <a name="synthetic-time-unhalted-timer-count-msr"></a>Nombre de minuteurs de Time-Unhalted synthétiques MSR

| Bits          | Description                                                             | Attributs              |
|---------------|-------------------------------------------------------------------------|-------------------------|
| 63:0          | Taux d’interruptions périodiques en unités NS 100                             | Lecture/écriture            |