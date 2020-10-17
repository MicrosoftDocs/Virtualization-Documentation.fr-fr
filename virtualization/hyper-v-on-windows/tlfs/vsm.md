---
title: Mode sécurisé virtuel
description: Mode sécurisé virtuel
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: b858b24c8ddd672f8d109a1fbda4cb006437d68d
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120567"
---
# <a name="virtual-secure-mode"></a>Mode sécurisé virtuel

Le mode sécurisé virtuel (VSM) est un ensemble de fonctionnalités d’hyperviseur et d’invites offertes aux partitions hôtes et invités, ce qui permet la création et la gestion de nouvelles limites de sécurité au sein du logiciel du système d’exploitation. VSM est la fonction d’hyperviseur sur laquelle sont basées les fonctionnalités de sécurité Windows, notamment Device Guard, Credential Guard, les plateforme sécurisée virtuels et les machines virtuelles protégées. Ces fonctionnalités de sécurité ont été introduites dans Windows 10 et Windows Server 2016.

VSM active les logiciels du système d’exploitation dans les partitions racine et invitées pour créer des régions isolées de mémoire pour le stockage et le traitement des ressources de sécurité du système. L’accès à ces régions isolées est contrôlé et accordé uniquement via l’hyperviseur, qui est une partie hautement privilégiée et hautement fiable de la base de calcul approuvée (TCB) du système. Étant donné que l’hyperviseur s’exécute à un niveau de privilège plus élevé que le logiciel du système d’exploitation et qu’il contrôle de manière exclusive les principales ressources matérielles du système telles que les contrôles d’autorisation d’accès mémoire dans les éléments de l’UC MMU et IOMMU tôt dans l’initialisation du système, l’hyperviseur peut protéger ces régions isolées contre tout accès non autorisé, y compris , ou « Ring 0 »).

Avec cette architecture, même si le logiciel de niveau système normal s’exécutant en mode superviseur (par exemple, le noyau, les pilotes, etc.) est compromis par des logiciels malveillants, les ressources des régions isolées protégées par l’hyperviseur peuvent rester sécurisées.

## <a name="virtual-trust-level-vtl"></a>Niveau de confiance virtuel (VTL)

VSM réalise et gère l’isolation via des niveaux de confiance virtuels (VTL). Les VTL sont activées et gérées sur une base par partition et par processeur virtuel.

Les niveaux de confiance virtuelle sont hiérarchiques, avec des niveaux supérieurs plus élevés que les niveaux inférieurs. VTL0 est le niveau le moins privilégié, avec VTL1 plus privilégié que VTL0, VTL2 plus privilégié que VTL1, etc.

Architecturale, jusqu’à 16 niveaux de VTL sont pris en charge ; Toutefois, un hyperviseur peut choisir d’implémenter moins de 16 VTL. Actuellement, seules deux VTL sont implémentées.

 ```c
typedef UINT8 HV_VTL, *PHV_VTL;

#define HV_NUM_VTLS 2
#define HV_INVALID_VTL ((HV_VTL) -1)
#define HV_VTL_ALL 0xF
 ```

Chaque VTL possède son propre ensemble de protection d’accès mémoire. Ces protections d’accès sont gérées par l’hyperviseur dans l’espace d’adressage physique d’une partition et ne peuvent donc pas être modifiées par le logiciel de niveau système qui s’exécute dans la partition.

Étant donné que les VTL plus privilégiées peuvent appliquer leurs propres protections de mémoire, les VTL plus élevées peuvent protéger efficacement les zones de mémoire des VTL plus basses. Dans la pratique, cela permet à une VTL inférieure de protéger les régions de mémoire isolée en les sécurisant avec une VTL plus élevée. Par exemple, VTL0 peut stocker un secret dans VTL1, à quel moment seul VTL1 peut y accéder. Même si VTL0 est compromis, le secret est sécurisé.

### <a name="vtl-protections"></a>Protections de VTL

Il existe plusieurs facettes pour assurer l’isolation entre les VTL :

- Protection d’accès mémoire : chaque VTL gère un ensemble de protections d’accès à la mémoire physique invité. Les logiciels exécutés sur une VTL particulière peuvent uniquement accéder à la mémoire conformément à ces protections.
- État du processeur virtuel : les processeurs virtuels maintiennent un État séparé par VTL. Par exemple, chaque VTL définit un ensemble de registres de vice-président privés. Les logiciels exécutés sur une VTL inférieure ne peuvent pas accéder à l’État Register du processeur virtuel privé de la VTL.
- Interruptions : avec un état de processeur distinct, chaque VTL possède également son propre sous-système d’interruption (APIC local). Cela permet aux VTL plus élevées de traiter les interruptions sans risquer d’interférences à partir d’une VTL inférieure.
- Pages de superposition : certaines pages de superposition sont gérées par VTL, de telle sorte que les VTL plus élevées disposent d’un accès fiable. Par exemple, Il existe une page de superposition hypercall distincte par VTL.

## <a name="vsm-detection-and-status"></a>Détection et état de VSM

La fonctionnalité VSM est publiée sur les partitions via l’indicateur de privilège de partition AccessVsm. Seules les partitions avec tous les privilèges suivants peuvent utiliser VSM : AccessVsm, AccessVpRegisters et AccessSynicRegs.

### <a name="vsm-capability-detection"></a>Détection de fonctionnalité VSM

Les invités doivent utiliser le Registre spécifique au modèle suivant pour accéder à un rapport sur les fonctionnalités VSM :

| Adresse MSR      | Nom du Registre                   | Description                                                    |
|------------------|---------------------------------|----------------------------------------------------------------|
| 0x000D0006       | HV_X64_REGISTER_VSM_CAPABILITIES| Rapport sur les fonctionnalités VSM.                                    |

Le format des fonctions de Registre VSM MSR est le suivant :

| Bits          | Description                         | Attributs                                                  |
|---------------|-------------------------------------|-------------------------------------------------------------|
| 63            | Dr6Shared                           | Lire                                                        |
| 62:47         | MbecVtlMask                         | Lire                                                        |
| 46            | DenyLowerVtlStartup                 | Lire                                                        |
| 45:0          | RsvdZ                               | Lire                                                        |

Dr6Shared indique à l’invité si Dr6 est un registre partagé entre les VTL.

MvecVtlMask indique à l’invité les VTL pour lesquelles MBEC peut être activé.

DenyLowerVtlStartup indique à l’invité si une VTL peut refuser la réinitialisation d’un VP par une VTL inférieure.

### <a name="vsm-status-register"></a>Registre d’État VSM

En plus d’un indicateur de privilège de partition, deux registres virtuels peuvent être utilisés pour obtenir des informations supplémentaires sur l’état de VSM : `HvRegisterVsmPartitionStatus` et `HvRegisterVsmVpStatus` .

#### <a name="hvregistervsmpartitionstatus"></a>HvRegisterVsmPartitionStatus

HvRegisterVsmPartitionStatus est un registre par partition en lecture seule qui est partagé entre toutes les VTL. Ce registre fournit des informations sur les librairies de VTL qui ont été activées pour la partition, les VTL dont les contrôles d’exécution basés sur le mode sont activés, ainsi que la VTL maximale autorisée.

```c
typedef union
{
    UINT64 AsUINT64;
    struct {
        UINT64 EnabledVtlSet : 16;
        UINT64 MaximumVtl : 4;
        UINT64 MbecEnabledVtlSet: 16;
        UINT64 ReservedZ : 28;
    };
} HV_REGISTER_VSM_PARTITION_STATUS;
```

#### <a name="hvregistervsmvpstatus"></a>HvRegisterVsmVpStatus

HvRegisterVsmVpStatus est un registre en lecture seule et est partagé entre toutes les VTL. Il s’agit d’un registre par VP, ce qui signifie que chaque processeur virtuel gère sa propre instance. Ce registre fournit des informations sur les librairies de VTL qui ont été activées, qui est active, ainsi que le mode MBEC actif sur un VP.

```c
typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 ActiveVtl : 4;
        UINT64 ActiveMbecEnabled : 1;
        UINT64 ReservedZ0 : 11;
        UINT64 EnabledVtlSet : 16;
        UINT64 ReservedZ1 : 32;
    };
} HV_REGISTER_VSM_VP_STATUS;
```

ActiveVtl est l’ID du contexte de VTL qui est actuellement actif sur le processeur virtuel.

ActiveMbecEnabled spécifie que MBEC est actuellement actif sur le processeur virtuel.

EnabledVtlSet est une bitmap des VTL qui sont activées sur le processeur virtuel.

### <a name="partition-vtl-initial-state"></a>État initial de la partition VTL

Lors du démarrage ou de la réinitialisation d’une partition, son exécution commence dans VTL0. Toutes les autres VTL sont désactivées lors de la création de la partition.

## <a name="vtl-enablement"></a>Activation des VTL

Pour commencer à utiliser une VTL, une VTL inférieure doit initier les opérations suivantes :

1. Activez la VTL cible pour la partition. Cela rend la VTL généralement disponible pour la partition.
2. Activez la VTL cible sur un ou plusieurs processeurs virtuels. Cela rend la VTL disponible pour un VP et définit son contexte initial. Il est recommandé que tous les VPs aient les mêmes VTL activées. Le fait d’avoir une VTL activée sur un VPs (mais pas tous) peut entraîner un comportement inattendu.
3. Une fois que la VTL est activée pour une partition et un VP, elle peut commencer à définir des protections d’accès une fois que l’indicateur EnableVtlProtection a été défini.

Notez que les VTL n’ont pas besoin d’être consécutives.

### <a name="enabling-a-target-vtl-for-a-partition"></a>Activation d’une VTL cible pour une partition

L’hyperappel [HvCallEnablePartitionVtl](hypercalls/HvCallEnablePartitionVtl.md) est utilisé pour activer une VTL pour une certaine partition. Notez qu’avant que le logiciel puisse réellement s’exécuter dans une VTL particulière, cette VTL doit être activée sur les processeurs virtuels de la partition.

### <a name="enabling-a-target-vtl-for-virtual-processors"></a>Activation d’une VTL cible pour les processeurs virtuels

Une fois qu’une VTL est activée pour une partition, elle peut être activée sur les processeurs virtuels de la partition. L’hyperappel [HvCallEnableVpVtl](hypercalls/HvCallEnableVpVtl.md) peut être utilisé pour activer des VTL pour un processeur virtuel, qui définit son contexte initial.

Les processeurs virtuels ont un « contexte » par VTL. Si une VTL est basculée, l' [État privé](#private-state) de la VTL est également basculé.

## <a name="vtl-configuration"></a>Configuration de la VTL

Une fois qu’une VTL a été activée, sa configuration peut être modifiée par un VP s’exécutant sur une VTL égale ou supérieure.

### <a name="partition-configuration"></a>Configuration de partition

Les attributs à l’ensemble de la partition peuvent être configurés à l’aide du Registre HvRegisterVsmPartitionConfig. Il existe une instance de ce registre pour chaque VTL (supérieure à 0) sur chaque partition.

Chaque VTL peut modifier sa propre instance de HV_REGISTER_VSM_PARTITION_CONFIG, ainsi que des instances de moins de VTL. Les VTL ne peuvent pas modifier ce registre pour des VTL plus élevées.

```c
typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 EnableVtlProtection : 1;
        UINT64 DefaultVtlProtectionMask : 4;
        UINT64 ZeroMemoryOnReset : 1;
        UINT64 DenyLowerVtlStartup : 1;
        UINT64 ReservedZ : 2;
        UINT64 InterceptVpStartup : 1;
        UINT64 ReservedZ : 54; };
} HV_REGISTER_VSM_PARTITION_CONFIG;
```

Les champs de ce registre sont décrits ci-dessous.

#### <a name="enable-vtl-protections"></a>Activer les protections VTL

Une fois qu’une VTL a été activée, l’indicateur EnableVtlProtection doit être défini avant de pouvoir commencer à appliquer des protections de mémoire.
Cet indicateur est écrit une fois, ce qui signifie qu’une fois qu’il a été défini, il ne peut pas être modifié.

#### <a name="default-protection-mask"></a>Masque de protection par défaut

Par défaut, le système applique les protections de RWX à toutes les pages mappées actuellement, ainsi qu’à toutes les pages « ajoutées à chaud » à l’avenir. Les pages ajoutées à chaud font référence à toute mémoire ajoutée à une partition au cours d’une opération de redimensionnement.

Une VTL plus élevée peut définir une stratégie de protection de la mémoire par défaut différente en spécifiant DefaultVtlProtectionMask dans HV_REGISTER_VSM_PARTITION_CONFIG. Ce masque doit être défini au moment où la VTL est activée. Elle ne peut pas être modifiée une fois qu’elle est définie, et elle est supprimée uniquement par une réinitialisation de partition.

| bit       | Description                                                                          |
|-----------|--------------------------------------------------------------------------------------|
| 0         | Lire                                                                                 |
| 1         | Write                                                                                |
| 2         | Exécution du mode utilisateur (UMX)                                                              |
| 3         | Mode noyau exécuté (KMX)                                                            |

#### <a name="zero-memory-on-reset"></a>Mémoire insuffisante lors de la réinitialisation

ZeroMemOnReset est un peu contrôlant si la mémoire est mise à zéro avant la réinitialisation d’une partition. Cette configuration est activée par défaut. Si le bit est défini, la mémoire de la partition est mise à zéro en cas de réinitialisation afin qu’une mémoire de VTL plus élevée ne puisse pas être compromise par une VTL inférieure.
Si ce bit est effacé, la mémoire de la partition n’est pas mise à zéro lors de la réinitialisation.

#### <a name="denylowervtlstartup"></a>DenyLowerVtlStartup

L’indicateur DenyLowerVtlStartup contrôle si un processeur virtuel peut être démarré ou réinitialisé par des VTL inférieures. Cela comprend les méthodes architecturales de réinitialisation d’un processeur virtuel (par exemple, SIPI sur x64), ainsi que l’hyperappel [HvCallStartVirtualProcessor](hypercalls/HvCallStartVirtualProcessor.md) .

#### <a name="interceptvpstartup"></a>InterceptVpStartup

Si l’indicateur InterceptVpStartup est défini, le démarrage ou la réinitialisation d’un processeur virtuel génère une interception vers la VTL supérieure.

### <a name="configuring-lower-vtls"></a>Configuration des VTL inférieures

Le Registre suivant peut être utilisé par d’autres VTL pour configurer le comportement des VTL moins importantes :

```c
typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 MbecEnabled : 1;
        UINT64 TlbLocked : 1;
        UINT64 ReservedZ : 62;
    };
} HV_REGISTER_VSM_VP_SECURE_VTL_CONFIG;
```

Chaque VTL (supérieure à 0) a une instance de ce registre pour chaque VTL inférieure à elle-même. Par exemple, VTL2 a deux instances de ce registre : une pour VTL1 et une seconde pour VTL0.

Les champs de ce registre sont décrits ci-dessous.

#### <a name="mbecenabled"></a>MbecEnabled

Ce champ permet de déterminer si MBEC est activé pour la VTL inférieure.

#### <a name="tlblocked"></a>TlbLocked

Ce champ verrouille le TLB de la VTL inférieure. Cette fonctionnalité peut être utilisée pour empêcher des VTL moins susceptibles de provoquer des invalidations d’TLB qui peuvent interférer avec une VTL plus élevée. Lorsque ce bit est défini, toutes les demandes de vidage de l’espace d’adressage de la VTL inférieure sont bloquées jusqu’à ce que le verrou soit levé.

Pour déverrouiller le TLB, la VTL la plus élevée peut effacer ce bit. En outre, une fois qu’un vice-président revient à une VTL inférieure, il libère tous les verrous TLB qu’il contient à ce moment-là.

## <a name="vtl-entry"></a>Entrée VTL

Une VTL est « entrée » lorsqu’un VP passe d’une VTL inférieure à une autre. Ceci peut se produire pour les raisons suivantes :

1. Appel de VTL : c’est quand un logiciel souhaite expressément appeler du code dans une VTL plus élevée.
2. Interruption sécurisée : si une interruption est reçue pour une VTL plus élevée, le VP entrera dans la VTL la plus élevée.
3. Interceptation sécurisée : certaines actions déclenchent une interruption sécurisée (accès à certains MSRs, par exemple).

Une fois qu’une VTL est entrée, elle doit se fermer volontairement. Une VTL plus élevée ne peut pas être préemptée par une VTL inférieure.

### <a name="identifying-vtl-entry-reason"></a>Identification du motif de l’entrée VTL

Pour réagir de manière appropriée à une entrée, une VTL plus élevée devra peut-être connaître la raison pour laquelle elle a été entrée. Pour une discernement entre les raisons de l’entrée, l’entrée VTL est incluse dans la structure [HV_VP_VTL_CONTROL](datatypes/HV_VP_VTL_CONTROL.md) .

### <a name="vtl-call"></a>Appel de VTL

Un « appel VTL » est lorsqu’une VTL inférieure initie une entrée dans une VTL plus élevée (par exemple, pour protéger une région de mémoire avec la VTL la plus élevée) via l’hyperappel [HvCallVtlCall](hypercalls/HvCallVtlCall.md) .

Les appels de VTL préservent l’état des registres partagés entre les commutateurs VTL. Les registres privés sont conservés sur un niveau par VTL. Les registres requis par la séquence de l’appel de la VTL sont la seule exception à ces restrictions. Les registres suivants sont requis pour un appel de VTL :

| x64     | x86     | Description                                                 |
|---------|---------|-------------------------------------------------------------|
| RCX     | EDX : EAX | Spécifie une entrée de contrôle d’appel de VTL pour l’hyperviseur        |
| RAX     | ECX     | Réservé                                                    |

Tous les bits de l’entrée de contrôle de l’appel de la VTL sont actuellement réservés.

#### <a name="vtl-call-restrictions"></a>Restrictions relatives aux appels de VTL

Les appels de VTL peuvent uniquement être lancés à partir du mode processeur le plus privilégié. Par exemple, sur les systèmes x64, un appel de VTL ne peut provenir que d’CPL0. Un appel de VTL lancé à partir d’un mode de processeur qui est tout sauf le plus privilégié sur le système entraîne l’injection par l’hyperviseur d’une exception #UD dans le processeur virtuel.

Un appel de VTL peut uniquement basculer vers la VTL la plus élevée suivante. En d’autres termes, si plusieurs VTL sont activées, un appel ne peut pas « ignorer » une VTL.
Les actions suivantes provoquent une exception #UD :

- Un appel de VTL lancé à partir d’un mode de processeur, qui est tout sauf le plus privilégié sur le système (spécifique à l’architecture).
- Un appel de VTL à partir du mode réel (x86/x64)
- Un appel de VTL sur un processeur virtuel sur lequel la VTL cible est désactivée (ou n’a pas encore été activée).
- Un appel de VTL avec une valeur d’entrée de contrôle non valide

## <a name="vtl-exit"></a>Quitter la VTL

Un basculement vers une VTL inférieure est appelé « retour ». Une fois le traitement d’un VTL terminé, il peut lancer un retour VTL pour basculer vers une VTL inférieure. La seule façon de retourner une VTL peut se produire si une VTL plus élevée Initialise volontairement une. Une VTL inférieure ne peut jamais en préempter une plus grande.

### <a name="vtl-return"></a>Retour VTL

Une « retour VTL » est lorsqu’une VTL plus élevée lance un commutateur dans une VTL inférieure via l’hyperappel [HvCallVtlReturn](hypercalls/HvCallVtlReturn.md) . Semblable à un appel de VTL, l’état du processeur privé est désactivé et l’état partagé reste en place. Si la VTL inférieure a été appelée explicitement dans la VTL la plus élevée, l’hyperviseur incrémente le pointeur d’instruction de la VTL la plus élevée avant que le retour ne soit terminé afin qu’il puisse continuer après un appel de VTL.

Une séquence de code de retour de VTL requiert l’utilisation des registres suivants :

| x64     | x86     | Description                                                 |
|---------|---------|-------------------------------------------------------------|
| RCX     | EDX : EAX | Spécifie une entrée de contrôle de retour de VTL pour l’hyperviseur      |
| RAX     | ECX     | Réservé                                                    |

L’entrée de contrôle de retour de la VTL a le format suivant :

| Bits      | Champ           | Description                                                                 |
|-----------|-----------------|-----------------------------------------------------------------------------|
| 63:1      | RsvdZ           |                                                                             |
| 0         | Retour rapide     | Les registres ne sont pas restaurés                                                  |

Les actions suivantes génèrent une exception #UD :

- Tentative de retour d’une VTL quand la VTL la plus faible est actuellement active
- Tentative de retour d’une VTL avec une valeur d’entrée de contrôle non valide
- La tentative d’une VTL revient d’un mode de processeur qui est tout sauf le plus privilégié sur le système (spécifique à l’architecture).

#### <a name="fast-return"></a>Retour rapide

Dans le cadre du traitement d’un retour, l’hyperviseur peut restaurer l’état du registre de la VTL inférieure à partir de la structure [HV_VP_VTL_CONTROL](datatypes/HV_VP_VTL_CONTROL.md) . Par exemple, après le traitement d’une interruption sécurisée, une VTL plus élevée peut être retournée sans interrompre l’état inférieur de la VTL. Par conséquent, l’hyperviseur fournit un mécanisme permettant simplement de restaurer les registres de la VTL inférieure à leur valeur pré-appel stockée dans la structure de contrôle de la VTL.

Si ce comportement n’est pas nécessaire, une VTL plus élevée peut utiliser un « retour rapide ». Un retour rapide se fait lorsque l’hyperviseur ne restaure pas l’état du Registre à partir de la structure de contrôle. Cela doit être utilisé dans la mesure du possible pour éviter un traitement inutile.

Ce champ peut être défini avec le bit 0 de l’entrée de la VTL. S’il est défini sur 0, les registres sont restaurés à partir de la structure HV_VP_VTL_CONTROL. Si ce bit est défini sur 1, les registres ne sont pas restaurés (retour rapide).

## <a name="hypercall-page-assist"></a>Aide sur la page hypercall

L’hyperviseur fournit des mécanismes pour faciliter les appels de VTL et retourne via la [page hypercall](hypercall-interface.md#establishing-the-hypercall-interface). Cette page résume la séquence de code spécifique requise pour basculer entre les VTL.

Les séquences de code permettant d’exécuter des appels et des retours de VTL sont accessibles en exécutant des instructions spécifiques dans la page hypercall. Les segments Call/Return sont situés à un décalage dans la page hypercall déterminée par le registre virtuel HvRegisterVsmCodePageOffset. Il s’agit d’un registre en lecture seule et à l’ensemble de la partition, avec une instance distincte par VTL.

Une VTL peut exécuter un appel/retour de VTL à l’aide de l’instruction CALL. Un appel à l’emplacement correct dans la page hypercall initie un appel/retour de VTL.

```c
typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 VtlCallOffset : 12;
        UINT64 VtlReturnOffset : 12;
        UINT64 ReservedZ : 40;
    };
} HV_REGISTER_VSM_CODE_PAGE_OFFSETS;
```

Pour résumer, les étapes d’appel d’une séquence de code à l’aide de la page hypercall sont les suivantes :

1. Mapper la page hypercall à l’espace GPA d’une VTL
2. Déterminez le décalage correct pour la séquence de code (appel ou retour de la VTL).
3. Exécutez la séquence de code à l’aide de CALL.

## <a name="memory-access-protections"></a>Protection d’accès mémoire

L’une des protections nécessaires fournies par VSM est la possibilité d’isoler les accès à la mémoire.

Les VTL les plus élevées disposent d’un degré élevé de contrôle sur le type d’accès à la mémoire autorisé par les VTL moins performants. Il existe trois types de protection de base qui peuvent être spécifiés par un VTL plus élevé pour une page GPA particulière : lecture, écriture et exécution. Celles-ci sont définies dans le tableau suivant :

| Nom                       | Description                                                         |
|----------------------------|---------------------------------------------------------------------|
| Lire                       | Contrôle si l’accès en lecture est autorisé sur une page de mémoire            |
| Write                      | Contrôle si l’accès en écriture autorisé à une page de mémoire              |
| Execute                    | Contrôle si les extractions d’instructions sont autorisées pour une page de mémoire. |

Ces trois combinaisons pour les types suivants de protection de la mémoire :

1. Aucun accès
2. Lecture seule, pas d’exécution
3. Lecture seule, exécuter
4. Lecture/écriture, aucune exécution
5. Lecture/écriture, exécution

Si « mode d’exécution basée sur le mode (MBEC)) » est enabed, les protections d’exécution des utilisateurs et du mode noyau peuvent être définies séparément.

Les VTL plus élevées peuvent définir la protection de la mémoire pour une GPA via l’hyperappel [HvCallModifyVtlProtectionMask](hypercalls/HvCallModifyVtlProtectionMask.md) .

### <a name="memory-protection-hierarchy"></a>Hiérarchie de protection de la mémoire

Les autorisations d’accès à la mémoire peuvent être définies par un certain nombre de sources pour une VTL particulière. Les autorisations de chaque VTL peuvent potentiellement être limitées par un certain nombre d’autres VTL, ainsi que par la partition de l’hôte. L’ordre dans lequel les protections sont appliquées est le suivant :

1. Protections de la mémoire définies par l’hôte
2. Protections de la mémoire définies par des VTL plus élevées

En d’autres termes, les protections VTL remplacent les protections de l’hôte. Les VTL de niveau supérieur remplacent les VTL de niveau inférieur. Notez qu’une VTL ne peut pas définir des autorisations d’accès à la mémoire pour elle-même.

Une interface conforme est supposée ne pas superposer un type non-RAM sur la RAM.

### <a name="memory-access-violations"></a>Violations d’accès mémoire

Si un VP s’exécutant dans une VTL inférieure tente de violer une protection de mémoire définie par une VTL plus élevée, une interception est générée. Cette interception est reçue par la VTL supérieure qui définit la protection. Cela permet à des VTL plus élevées de traiter la violation au cas par cas. Par exemple, la VTL la plus élevée peut choisir de retourner une erreur ou d’émuler l’accès.

### <a name="mode-based-execute-control-mbec"></a>Contrôle d’exécution basé sur le mode (MBEC)

Lorsqu’un VTL place une restriction de mémoire sur une VTL inférieure, il peut souhaiter faire une distinction entre le mode utilisateur et le mode noyau lors de l’octroi d’un privilège « exécuter ». Par exemple, si les contrôles d’intégrité du code devaient avoir lieu dans une VTL plus élevée, la possibilité de faire la distinction entre le mode utilisateur et le mode noyau signifie qu’une VTL pourrait appliquer l’intégrité du code pour les applications en mode noyau uniquement.

Outre les trois protections de mémoire traditionnelles (lecture, écriture, exécution), MBEC introduit une distinction entre le mode utilisateur et le mode noyau pour l’exécution des protections. Par conséquent, si MBEC est activé, une VTL a la possibilité de définir quatre types de protections de mémoire :

| Nom                       | Description                                                         |
|----------------------------|---------------------------------------------------------------------|
| Lire                       | Contrôle si l’accès en lecture est autorisé sur une page de mémoire            |
| Write                      | Contrôle si l’accès en écriture autorisé à une page de mémoire              |
| Exécution du mode utilisateur (UMX)    | Contrôle si les extractions d’instructions générées en mode utilisateur sont autorisées pour une page de mémoire. Remarque : si MBEC est désactivé, ce paramètre est ignoré. |
| Mode noyau exécuté (UMX)  | Contrôle si les extractions d’instructions générées en mode noyau sont autorisées pour une page de mémoire. Remarque : si MBEC est désactivé, ce paramètre contrôle à la fois les accès en mode utilisateur et en mode noyau. |

La mémoire marquée avec les protections « exécution en mode utilisateur » ne peut être exécutée que lorsque le processeur virtuel s’exécute en mode utilisateur. De même, la mémoire « exécution en mode noyau » ne peut être exécutée que lorsque le processeur virtuel s’exécute en mode noyau.

KMX et UMX peuvent être définis indépendamment de sorte que les autorisations d’exécution soient appliquées différemment entre l’utilisateur et le mode noyau. Toutes les combinaisons de UMX et KMX sont prises en charge, à l’exception de KMX = 1, UMX = 0. Le comportement de cette combinaison n’est pas défini.

MBEC est désactivé par défaut pour tous les processeurs VTL et les processeurs virtuels. Quand MBEC est désactivé, le bit d’exécution en mode noyau détermine la restriction d’accès à la mémoire. Par conséquent, si MBEC est désactivé, KMX = 1 le code est exécutable à la fois en mode noyau et en mode utilisateur.

#### <a name="descriptor-tables"></a>Tables de descripteurs

Tout code en mode utilisateur qui accède à des tables de descripteur doit figurer dans les pages GPA marquées comme KMX = UMX = 1. Les logiciels en mode utilisateur qui accèdent aux tables de descripteurs à partir d’une page GPA marquée KMX = 0 ne sont pas pris en charge et entraînent une erreur de protection générale.

#### <a name="mbec-configuration"></a>Configuration de MBEC

Pour utiliser le contrôle d’exécution basé sur le mode, il doit être activé à deux niveaux :

1. Lorsque la VTL est activée pour une partition, MBEC doit être activé à l’aide de HvCallEnablePartitionVtl
2. MBEC doit être configuré sur une base par VP et par VTL, à l’aide de HvRegisterVsmVpSecureVtlConfig.

#### <a name="mbec-interaction-with-supervisor-mode-execution-prevention-smep"></a>Interaction MBEC avec la prévention de l’exécution en mode superviseur (SMEP)

Supervisor-Mode la prévention de l’exécution (SMEP) est une fonctionnalité de processeur prise en charge sur certaines plateformes. SMEP peut avoir un impact sur le fonctionnement de MBEC en raison de sa restriction d’accès du superviseur aux pages mémoire. L’hyperviseur adhère aux stratégies suivantes liées à SMEP :

- Si SMEP n’est pas disponible pour le système d’exploitation invité (qu’il soit en raison de fonctionnalités matérielles ou du mode de compatibilité du processeur), MBEC ne peut pas être affecté.
- Si SMEP est disponible et que est activé, MBEC ne fonctionne pas.
- Si SMEP est disponible et qu’elle est désactivée, toutes les restrictions d’exécution sont régies par le contrôle KMX. Ainsi, seul le code marqué KMX = 1 est autorisé à s’exécuter.

## <a name="virtual-processor-state-isolation"></a>Isolation de l’état du processeur virtuel

Les processeurs virtuels maintiennent des États distincts pour chaque VTL active. Toutefois, certains de cet État sont privés pour une VTL particulière et l’État restant est partagé entre toutes les VTL.

État conservé par VTL (également appelé État privé) est enregistré par l’hyperviseur sur des transitions de VTL. Si un commutateur VTL est lancé, l’hyperviseur enregistre l’État privé actuel pour la VTL active, puis bascule vers l’État privé de la VTL cible. L’état partagé reste actif, quels que soient les commutateurs de VTL.

### <a name="private-state"></a>État privé

En général, chaque VTL a ses propres registres de contrôle, registre RIP, registre RSP et MSRs. Vous trouverez ci-dessous une liste de registres spécifiques et de MSRs qui sont privés pour chaque VTL.

MSRs privé :

- SYSENTER_CS, SYSENTER_ESP, SYSENTER_EIP, STAR, LSTAR, CSTAR, SFMASK, EFER, PAT, KERNEL_GSBASE, FS. DE BASE, GS. DE BASE, TSC_AUX
- HV_X64_MSR_HYPERCALL
- HV_X64_MSR_GUEST_OS_ID
- HV_X64_MSR_REFERENCE_TSC
- HV_X64_MSR_APIC_FREQUENCY
- HV_X64_MSR_EOI
- HV_X64_MSR_ICR
- HV_X64_MSR_TPR
- HV_X64_MSR_APIC_ASSIST_PAGE
- HV_X64_MSR_NPIEP_CONFIG
- HV_X64_MSR_SIRBP
- HV_X64_MSR_SCONTROL
- HV_X64_MSR_SVERSION
- HV_X64_MSR_SIEFP
- HV_X64_MSR_SIMP
- HV_X64_MSR_EOM
- HV_X64_MSR_SINT0 – HV_X64_MSR_SINT15
- HV_X64_MSR_STIMER0_CONFIG – HV_X64_MSR_STIMER3_CONFIG
- HV_X64_MSR_STIMER0_COUNT – HV_X64_MSR_STIMER3_COUNT
- Registres APIC locaux (y compris CR8/TPR)

Registres privés :

- RIP, RSP
- RFLAGS
- REGISTRE CR0, CR3, CR4
- DR7
- IDTR, GDTR
- CS, DS, ES, FS, GS, SS, TR, LDTR
- TSC
- DR6 (* dépendant du type de processeur. Lire le registre virtuel HvRegisterVsmCapabilities pour déterminer l’état partagé/privé)

### <a name="shared-state"></a>État partagé

Les VTL partagent l’État pour réduire la surcharge des contextes de basculement. L’état de partage permet également une communication nécessaire entre les VTL. La plupart des registres à usage général et à virgule flottante sont partagés, comme c’est le cas pour la plupart des architectures MSRs. Voici la liste de MSRs et des registres spécifiques qui sont partagés entre les VTL :

MSRs partagé :

- HV_X64_MSR_TSC_FREQUENCY
- HV_X64_MSR_VP_INDEX
- HV_X64_MSR_VP_RUNTIME
- HV_X64_MSR_RESET
- HV_X64_MSR_TIME_REF_COUNT
- HV_X64_MSR_GUEST_IDLE
- HV_X64_MSR_DEBUG_DEVICE_OPTIONS
- MTRRs
- MCG_CAP
- MCG_STATUS

Registres partagés :

- Rax, RBX, RCx, RDX, RSI, RDI, RBP
- CR2
- R8 – R15
- DR0 – DR5
- État à virgule flottante X87
- État XMM
- État AVX
- XCR0 (XFEM)
- DR6 (* dépendant du type de processeur. Lire le registre virtuel HvRegisterVsmCapabilities pour déterminer l’état partagé/privé)

### <a name="real-mode"></a>Mode réel

Le mode réel n’est pris en charge pour aucune VTL supérieure à 0. Les VTL supérieures à 0 peuvent s’exécuter en mode 32 bits ou 64 bits.

## <a name="vtl-interrupt-management"></a>Gestion des interruptions VTL

Afin d’atteindre un niveau élevé d’isolation entre les niveaux de confiance virtuelle, le mode sécurisé virtuel fournit un sous-système d’interruption distinct pour chaque VTL activée sur un processeur virtuel. Cela garantit qu’une VTL peut envoyer et recevoir des interruptions sans interférence d’une VTL moins sécurisée.

Chaque VTL a son propre contrôleur d’interruption, qui est uniquement actif si le processeur virtuel est en cours d’exécution dans cette VTL particulière. Si un processeur virtuel change d’état de VTL, le contrôleur d’interruption actif sur le processeur est également basculé.

Une interruption ciblée sur une VTL supérieure à la VTL active entraîne un commutateur VTL immédiat. La VTL supérieure peut ensuite recevoir l’interruption. Si la VTL supérieure ne parvient pas à recevoir l’interruption en raison de sa valeur TPR/CR8, l’interruption est conservée en tant que « en attente » et la VTL n’est pas basculée. S’il existe plusieurs VTL avec des interruptions en attente, la VTL la plus élevée est prioritaire (sans préavis à la VTL inférieure).

Lorsqu’une interruption est ciblée sur une VTL inférieure, l’interruption n’est pas remise jusqu’à la prochaine transition du processeur virtuel vers la VTL ciblée. Les IPI INIT et Startup ciblés sur une VTL inférieure sont déposés sur un processeur virtuel avec une VTL supérieure activée. Étant donné que INIT/SIPI est bloqué, l’hypercall [HvCallStartVirtualProcessor](hypercalls/HvCallStartVirtualProcessor.md) doit être utilisé pour démarrer les processeurs.

### <a name="rflagsif"></a>RFLAGS. QUE

À des fins de commutation des VTL, RFLAGS. Si ne détermine pas si une interruption sécurisée déclenche un commutateur VTL. Si RFLAGS. Si est effacé pour masquer les interruptions, les interruptions dans les VTL les plus élevées entraînent toujours un basculement d’une VTL vers une VTL plus élevée. Seule la valeur TPR/CR8 de la VTL la plus élevée est prise en compte pour décider s’il faut interrompre immédiatement.

Ce comportement affecte également les interruptions en attente sur un retour VTL. Si RFLAGS. Si le bit est effacé pour masquer les interruptions dans une VTL donnée et que la VTL retourne (à une VTL inférieure), l’hyperviseur réévalue toutes les interruptions en attente. Cela entraîne un rappel immédiat de la VTL supérieure.

### <a name="virtual-interrupt-notification-assist"></a>Assistance pour les notifications d’interruptions virtuelles

Des VTL plus élevées peuvent s’inscrire pour recevoir une notification s’ils bloquent la remise immédiate d’une interruption à une VTL inférieure du même processeur virtuel. Les VTL plus élevées peuvent activer l’assistance de notifications d’interruptions virtuelles (VINA) via un registre virtuel HvRegisterVsmVina :

```c
typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 Vector : 8;
        UINT64 Enabled : 1;
        UINT64 AutoReset : 1;
        UINT64 AutoEoi : 1;
        UINT64 ReservedP : 53;
    };
} HV_REGISTER_VSM_VINA;
```

Chaque VTL de chaque VP possède sa propre instance VINA, ainsi que sa propre version de HvRegisterVsmVina. La fonction VINA génère une interruption déclenchée par une arête vers le VTL actuellement actif lorsqu’une interruption pour la VTL inférieure est prête pour une remise immédiate.

Afin d’éviter une saturation des interruptions lorsque cette fonctionnalité est activée, la fonction VINA comprend un État limité. Lors de la génération d’une interruption VINA, l’état de la fonctionnalité VINA est remplacé par « assertion ». L’envoi d’une fin d’interruption à la Saint-end associée à la fonction VINA n’efface pas l’État « déclaré ». L’État déclaré peut uniquement être effacé de l’une des deux manières suivantes :

1. L’État peut être effacé manuellement en écrivant dans le champ VinaAsserted de la structure [HV_VP_VTL_CONTROL](datatypes/HV_VP_VTL_CONTROL.md) .
2. L’État est automatiquement effacé à l’entrée suivante de la VTL si l’option « réinitialisation automatique sur l’entrée de la VTL » est activée dans le registre HvRegisterVsmVina.

Cela permet au code s’exécutant dans une VTL sécurisée d’être simplement notifié de la première interruption reçue pour une VTL inférieure. Si une VTL sécurisée souhaite être avertie d’interruptions supplémentaires, elle peut effacer le champ VinaAsserted de la page d’assistance du VP, et elle est informée de la nouvelle interruption suivante.

## <a name="secure-intercepts"></a>Interceptions sécurisées

L’hyperviseur permet à une VTL plus élevée d’installer des interceptions pour les événements qui se produisent dans le contexte d’une VTL inférieure. Cela donne aux VTL plus hauts un niveau élevé de contrôle sur les ressources de la VTL inférieure. Les interceptions sécurisées peuvent être utilisées pour protéger les ressources critiques du système et pour empêcher les attaques des VTL de bas niveau.

Une interception sécurisée est mise en file d’attente dans la VTL supérieure et cette VTL est rendue exécutable sur le VP.

### <a name="secure-intercept-types"></a>Types d’interception sécurisés

| Type d’interception             | L’interception s’applique à                                                |
|----------------------------|---------------------------------------------------------------------|
| Accès mémoire              | Tentative d’accès à des protections GPA établies par une VTL plus élevée.   |
| Contrôler l’accès aux registres    | Tentative d’accès à un ensemble de registres de contrôle spécifiés par une VTL plus élevée. |

### <a name="nested-intercepts"></a>Interceptions imbriquées

Plusieurs VTL peuvent installer des interceptions sécurisées pour le même événement dans une VTL inférieure. Par conséquent, une hiérarchie est établie pour décider où les interceptions imbriquées sont notifiées. La liste suivante indique l’ordre dans lequel les interceptions sont notifiées :

1. VTL inférieure
2. VTL plus élevée

### <a name="handling-secure-intercepts"></a>Gestion des interceptions sécurisées

Une fois qu’une VTL a été notifiée d’une interception sécurisée, elle doit prendre des mesures pour que la VTL inférieure puisse continuer.
La VTL plus élevée peut gérer l’interception de plusieurs façons, notamment : injecter une exception, émuler l’accès ou fournir un proxy à l’accès. Dans tous les cas, si l’État privé du VP le plus bas doit être modifié, [HvCallSetVpRegisters](hypercalls/HvCallSetVpRegisters.md) doit être utilisé.

### <a name="secure-register-intercepts"></a>Interceptions de registres sécurisés

Une VTL plus élevée peut intercepter les accès à certains registres de contrôle. Cela est possible en définissant HvX64RegisterCrInterceptControl à l’aide de l’hypercall [HvCallSetVpRegisters](hypercalls/HvCallSetVpRegisters.md) . La définition du bit de contrôle dans HvX64RegisterCrInterceptControl déclenche une opération d’interception pour chaque accès du registre de contrôle correspondant.

```c
typedef union
{
    UINT64 AsUINT64;
    struct
    {
        UINT64 Cr0Write : 1;
        UINT64 Cr4Write : 1;
        UINT64 XCr0Write : 1;
        UINT64 IA32MiscEnableRead : 1;
        UINT64 IA32MiscEnableWrite : 1;
        UINT64 MsrLstarRead : 1;
        UINT64 MsrLstarWrite : 1;
        UINT64 MsrStarRead : 1;
        UINT64 MsrStarWrite : 1;
        UINT64 MsrCstarRead : 1;
        UINT64 MsrCstarWrite : 1;
        UINT64 ApicBaseMsrRead : 1;
        UINT64 ApicBaseMsrWrite : 1;
        UINT64 MsrEferRead : 1;
        UINT64 MsrEferWrite : 1;
        UINT64 GdtrWrite : 1;
        UINT64 IdtrWrite : 1;
        UINT64 LdtrWrite : 1;
        UINT64 TrWrite : 1;
        UINT64 MsrSysenterCsWrite : 1;
        UINT64 MsrSysenterEipWrite : 1;
        UINT64 MsrSysenterEspWrite : 1;
        UINT64 MsrSfmaskWrite : 1;
        UINT64 MsrTscAuxWrite : 1;
        UINT64 MsrSgxLaunchControlWrite : 1;
        UINT64 RsvdZ : 39;
    };
} HV_REGISTER_CR_INTERCEPT_CONTROL;
```

#### <a name="mask-registers"></a>Registres de masque

Pour permettre un contrôle plus fin, un sous-ensemble de registres de contrôle a également des registres de masque correspondants. Les registres de masque peuvent être utilisés pour installer les interceptions sur un sous-ensemble des registres de contrôle correspondants. Lorsqu’un registre de masque n’est pas défini, tout accès (tel que défini par HvX64RegisterCrInterceptControl) déclenche une opération d’interception.

L’hyperviseur prend en charge les registres de masque suivants : HvX64RegisterCrInterceptCr0Mask, HvX64RegisterCrInterceptCr4Mask et HvX64RegisterCrInterceptIa32MiscEnableMask.

## <a name="dma-and-devices"></a>DMA et appareils

Les appareils ont le même niveau de privilège que VTL0. Lorsque VSM est activé, toute la mémoire allouée par l’appareil est marquée en tant que VTL0. Tous les accès DMA ont les mêmes privilèges que VTL0.
