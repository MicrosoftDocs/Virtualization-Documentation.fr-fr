---
title: Détection des fonctionnalités et des interfaces
description: Fonctionnalité d’hyperviseur et détection d’interface
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: b88cb0be23f4e3ea4ba1007d48bd11fe48749f00
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120568"
---
# <a name="feature-and-interface-discovery"></a>Détection des fonctionnalités et des interfaces

Le logiciel invité interagit avec l’hyperviseur via un ensemble de mécanismes. Un grand nombre d’entre eux reflètent les mécanismes traditionnels utilisés par les logiciels pour interagir avec le processeur sous-jacent. Par conséquent, ces mécanismes sont spécifiques à l’architecture. Sur l’architecture x64, les mécanismes suivants sont utilisés :

- Instruction CPUID : utilisée pour les informations relatives à la fonctionnalité et à la version statiques.
- MSRs (registres spécifiques au modèle) : utilisé pour les valeurs d’État et de contrôle.
- Registres mappés en mémoire – utilisés pour les valeurs d’État et de contrôle.
- Interruptions de processeur : utilisées pour les événements asynchrones, les notifications et les messages.

En plus de ces interfaces spécifiques à l’architecture, l’hyperviseur fournit une interface procédurale simple implémentée avec des [hyperappels](hypercall-interface.md).

## <a name="hypervisor-discovery"></a>Détection d’hyperviseur

Avant d’utiliser des interfaces hyperviseur, les logiciels doivent d’abord déterminer s’ils s’exécutent dans un environnement virtualisé. Sur les plateformes x64 qui se conforment à cette spécification, cette opération s’effectue en exécutant l’instruction CPUID avec une valeur d’entrée (EAX) de 1. Lors de l’exécution, le code doit vérifier le bit 31 du registre ECX (le « bit présent de l’hyperviseur »). Si ce bit est défini, un hyperviseur est présent. Dans un environnement non virtualisé, le bit est clair.

 ```c
CPUID.01h.ECX:31 // if set, virtualization present
 ```

Si le « bit présent de l’hyperviseur » est défini, des feuilles CPUID supplémentaires peuvent être interrogées pour obtenir plus d’informations sur l’hyperviseur conforme et ses fonctionnalités. Deux de ces congés sont garantis disponibles : `0x40000000` et `0x40000001` . Les feuilles numérotées par la suite peuvent également être disponibles.

## <a name="standard-hypervisor-cpuid-leaves"></a>Les feuilles de l’hyperviseur standard restent

Lorsque la feuille à `0x40000000` est interrogée, l’hyperviseur retourne des informations qui fournissent le numéro de feuille maximal du CPUID hyperviseur et une signature d’ID de fournisseur.

| Inscrire  | Informations fournies                                        |
|-----------|-------------------------------------------------------------|
| EAX       | Valeur d’entrée maximale pour les informations CPUID hyperviseur    |
| EBX       | Signature de l’ID de fournisseur de l’hyperviseur                              |
| ECX       | Signature de l’ID de fournisseur de l’hyperviseur                              |
| EDX       | Signature de l’ID de fournisseur de l’hyperviseur                              |

Si la feuille à `0x40000001` est interrogée, elle retourne une valeur représentant une identification de l’interface d’hyperviseur indépendante du fournisseur. Cela détermine la sémantique des feuilles du à l' `0x4000002` aide de `0x400000FF` .

| Inscrire  | Informations fournies                                        |
|-----------|-------------------------------------------------------------|
| EAX       | Signature de l’interface hyperviseur.                             |
| EBX       | Réservé                                                    |
| ECX       | Réservé                                                    |
| EDX       | Réservé                                                    |

Ces deux feuilles permettent à l’invité d’interroger l’ID de fournisseur de l’hyperviseur et l’interface indépendamment. L’ID de fournisseur n’est fourni qu’à des fins d’information et de diagnostic. Il est recommandé que les logiciels ne prennent les décisions de compatibilité de base que sur la signature d’interface signalée via feuille `0x40000001` .

## <a name="microsoft-hypervisor-cpuid-leaves"></a>Les feuilles de l’hyperviseur Microsoft

Sur les hyperviseurs conformes à l’interface de l’hyperviseur Microsoft, les `0x40000000` `0x40000001` registres feuille et sont les valeurs suivantes.

### <a name="hypervisor-cpuid-leaf-range---0x40000000"></a>Ensemble de feuilles d’hyperviseur CPUID-0x40000000

EAX détermine la feuille du CPUID hyperviseur maximum. EBX-EDX contient la signature de l’ID du fournisseur de l’hyperviseur. La signature de l’ID de fournisseur doit être utilisée uniquement à des fins de création de rapports et de diagnostic.

| Inscrire  | Informations fournies                                        |
|-----------|-------------------------------------------------------------|
| EAX       | Valeur d’entrée maximale pour les informations CPUID de l’hyperviseur. Sur les hyperviseurs Microsoft, ce sera au moins `0x40000005` .  |
| EBX       | 0x7263694D : « MICR »                                           |
| ECX       | 0x666F736F—"osof"                                           |
| EDX       | 0x76482074 : « t... »                                           |

### <a name="hypervisor-vendor-neutral-interface-identification---0x40000001"></a>Fournisseur de l’hyperviseur-identification de l’interface neutre-0x40000001

EAX contient la signature d’identification de l’interface hyperviseur. Cela détermine la sémantique des feuilles du à l' `0x40000002` aide de `0x400000FF` .

| Inscrire  | Informations fournies                                        |
|-----------|-------------------------------------------------------------|
| EAX       | 0x31237648 — « n ° 1 »                                           |
| EBX       | Réservé                                                    |
| ECX       | Réservé                                                    |
| EDX       | Réservé                                                    |

Les hyperviseurs conformes à l’interface « n ° 1 » fournissent également au moins les feuilles suivantes.

### <a name="hypervisor-system-identity---0x40000002"></a>Identité du système de l’hyperviseur-0x40000002

<!---
+-----------+-------+------------------------+
| Register  | Bits  | Information Provided   |
+===========+=======+========================+
| EAX       |       | Build Number           |
+-----------+-------+------------------------+
| EBX       | 31-16 | Major Version          |
+           +-------+------------------------+
|           | 15-0  | Minor Version          |
+-----------+-------+------------------------+
| ECX       |       | Service Pack           |
+-----------+-------+------------------------+
| EDX       | 31-24 | Service Branch         |
+           +-------+------------------------+
|           | 23-0  | Service Number         |
+-----------+-------+------------------------+
-->

<table>
    <thead>
        <tr>
            <th>Inscrire</th>
            <th>Bits</th>
            <th>Informations fournies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>EAX</td>
            <td></td>
            <td>Numéro de version</td>
        </tr>
        <tr>
            <td rowspan="2">EBX</td>
            <td>31-16</td>
            <td>Version majeure</td>
        </tr>
         <tr>
            <td>15-0</td>
            <td>Version mineure</td>
        </tr>
    </tbody>
</table>

### <a name="hypervisor-feature-identification---0x40000003"></a>Identification des fonctionnalités de l’hyperviseur-0x40000003

EAX et EBX indiquent les fonctionnalités disponibles pour la partition en fonction des privilèges de partition actuels.

<!---
+-----------+-------+---------------------------------------------------------------------------------------+
| Register  | Bits  | Information Provided                                                                  |
+===========+=======+=======================================================================================+
| EAX       |       | Corresponds to bits 31-0 of HV_PARTITION_PRIVILEGE_MASK                               |
+-----------+-------+---------------------------------------------------------------------------------------+
| EBX       |       | Corresponds to bits 63-32 of HV_PARTITION_PRIVILEGE_MASK                              |
+-----------+-------+---------------------------------------------------------------------------------------+
| ECX       |       |                                                                                       |
+-----------+-------+---------------------------------------------------------------------------------------+
| EDX       | 0     | Deprecated (previously indicated availability of the MWAIT command).                  |
+           +-------+---------------------------------------------------------------------------------------+
|           | 1     | Guest debugging support is available                                                  |
+           +-------+---------------------------------------------------------------------------------------+
|           | 2     | Performance Monitor support is available                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 3     | Support for physical CPU dynamic partitioning events is available                     |
+           +-------+---------------------------------------------------------------------------------------+
|           | 4     | Support for passing hypercall input parameter block via XMM registers is available    |
+           +-------+---------------------------------------------------------------------------------------+
|           | 5     | Support for a virtual guest idle state is available                                   |
+           +-------+---------------------------------------------------------------------------------------+
|           | 6     | Support for hypervisor sleep state is available                                       |
+           +-------+---------------------------------------------------------------------------------------+
|           | 7     | Support for querying NUMA distances is available                                      |
+           +-------+---------------------------------------------------------------------------------------+
|           | 8     | Support for determining timer frequencies is available                                |
+           +-------+---------------------------------------------------------------------------------------+
|           | 9     | Support for injecting synthetic machine checks is available                           |
+           +-------+---------------------------------------------------------------------------------------+
|           | 10    | Support for guest crash MSRs is available                                             |
+           +-------+---------------------------------------------------------------------------------------+
|           | 11    | Support for debug MSRs is available                                                   |
+           +-------+---------------------------------------------------------------------------------------+
|           | 12    | Support for NPIEP is available                                                        |
+           +-------+---------------------------------------------------------------------------------------+
|           | 13    | DisableHypervisorAvailable                                                            |
+           +-------+---------------------------------------------------------------------------------------+
|           | 14    | ExtendedGvaRangesForFlushVirtualAddressListAvailable                                  |
+           +-------+---------------------------------------------------------------------------------------+
|           | 15    | Support for returning hypercall output via XMM registers is available                 |
+           +-------+---------------------------------------------------------------------------------------+
|           | 16    | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 17    | SintPollingModeAvailable                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 18    | HypercallMsrLockAvailable                                                             |
+           +-------+---------------------------------------------------------------------------------------+
|           | 19    | Use direct synthetic timers                                                           |
+           +-------+---------------------------------------------------------------------------------------+
|           | 31-20 | Reserved                                                                              |
-->

<table>
    <thead>
        <tr>
            <th>Inscrire</th>
            <th>Bits</th>
            <th>Informations fournies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>EAX</td>
            <td></td>
            <td>Correspond à bits 31-0 de HV_PARTITION_PRIVILEGE_MASK</td>
        </tr>
        <tr>
            <td>EBX</td>
            <td></td>
            <td>Correspond à bits 63-32 de HV_PARTITION_PRIVILEGE_MASK</td>
        </tr>
        <tr>
            <td>ECX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td rowspan="27">EDX</td>
            <td>0</td>
            <td>Déconseillé (précédemment indiqué la disponibilité de l’instruction MWAIT)</td>
        </tr>
        <tr>
            <td>1</td>
            <td>La prise en charge du débogage invité est disponible</td>
        </tr>
        <tr>
            <td>2</td>
            <td>La prise en charge de l’analyseur de performances est disponible</td>
        </tr>
        <tr>
            <td>3</td>
            <td>La prise en charge des événements de partitionnement dynamique de l’UC physique est disponible</td>
        </tr>
        <tr>
            <td>4</td>
            <td>La prise en charge du passage du bloc de paramètres d’entrée hypercall via des registres XMM est disponible</td>
        </tr>
        <tr>
            <td>5</td>
            <td>La prise en charge d’un état inactif de l’invité virtuel est disponible</td>
        </tr>
        <tr>
            <td>6</td>
            <td>La prise en charge de l’état de veille de l’hyperviseur est disponible</td>
        </tr>
        <tr>
            <td>7</td>
            <td>La prise en charge de l’interrogation des distances NUMA est disponible</td>
        </tr>
        <tr>
            <td>8</td>
            <td>La prise en charge de la détermination des fréquences de minuteur est disponible</td>
        </tr>
        <tr>
            <td>9</td>
            <td>La prise en charge de l’injection de contrôles de machines synthétiques est disponible</td>
        </tr>
        <tr>
            <td>10</td>
            <td>La prise en charge d’un système d’incident d’invité MSRs est disponible</td>
        </tr>
        <tr>
            <td>11</td>
            <td>La prise en charge du débogage MSRs est disponible</td>
        </tr>
        <tr>
            <td>12</td>
            <td>La prise en charge de NPIEP est disponible</td>
        </tr>
        <tr>
            <td>13</td>
            <td>DisableHypervisorAvailable</td>
        </tr>
        <tr>
            <td>14</td>
            <td>ExtendedGvaRangesForFlushVirtualAddressListAvailable</td>
        </tr>
        <tr>
            <td>15</td>
            <td>La prise en charge du retour de la sortie d’hypercall via des registres XMM est disponible</td>
        </tr>
        <tr>
            <td>16</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>17</td>
            <td>SintPollingModeAvailable</td>
        </tr>
        <tr>
            <td>18</td>
            <td>HypercallMsrLockAvailable</td>
        </tr>
        <tr>
            <td>19</td>
            <td>Utiliser des minuteurs synthétiques directs</td>
        </tr>
        <tr>
            <td>20</td>
            <td>Prise en charge du Registre PAT disponible pour VSM</td>
        </tr>
        <tr>
            <td>21</td>
            <td>Prise en charge du Registre bndcfgs disponible pour VSM</td>
        </tr>
        <tr>
            <td>22</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>23</td>
            <td>Prise en charge du minuteur de temps synthétique non interrompu disponible</td>
        </tr>
        <tr>
            <td>25-24</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>26</td>
            <td>La fonctionnalité LBR (Last Branch record) d’Intel est prise en charge</td>
        </tr>
        <tr>
            <td>31-27</td>
            <td>Réservé</td>
        </tr>
    </tbody>
</table>

### <a name="implementation-recommendations---0x40000004"></a>Recommandations d’implémentation-0x40000004

Indique les comportements que l’hyperviseur recommande l’implémentation du système d’exploitation pour des performances optimales.

<!---
+-----------+-------+---------------------------------------------------------------------------------------+
| Register  | Bits  | Information Provided                                                                  |
+===========+=======+=======================================================================================+
| EAX       | 0     | Recommend using hypercall for address space switches rather than MOV to CR3 instruction
+           +-------+---------------------------------------------------------------------------------------+
|           | 1     | Recommend using hypercall for local TLB flushes rather than INVLPG or MOV to CR3 instructions
+           +-------+---------------------------------------------------------------------------------------+
|           | 2     | Recommend using hypercall for remote TLB flushes rather than inter-processor interrupts
+           +-------+---------------------------------------------------------------------------------------+
|           | 3     | Recommend using MSRs for accessing APIC registers EOI, ICR and TPR rather than their memory-mapped counterparts
+           +-------+---------------------------------------------------------------------------------------+
|           | 4     | Recommend using the hypervisor-provided MSR to initiate a system RESET                |
+           +-------+---------------------------------------------------------------------------------------+
|           | 5     | Recommend using relaxed timing for this partition. If used, the VM should disable any watchdog timeouts that rely on the timely delivery of external interrupts.
+           +-------+---------------------------------------------------------------------------------------+
|           | 6     | Recommend using DMA remapping                                                         |
+           +-------+---------------------------------------------------------------------------------------+
|           | 7     | Recommend using interrupt remapping                                                   |
+           +-------+---------------------------------------------------------------------------------------+
|           | 8     | Recommend using x2APIC MSRs                                                           |
+           +-------+---------------------------------------------------------------------------------------+
|           | 9     | Recommend deprecating AutoEOI                                                         |
+           +-------+---------------------------------------------------------------------------------------+
|           | 10    | Recommend using SyntheticClusterIpi hypercall                                         |
+           +-------+---------------------------------------------------------------------------------------+
|           | 11    | Recommend using the newer ExProcessorMasks interface                                  |
+           +-------+---------------------------------------------------------------------------------------+
|           | 12    |  Indicates that the hypervisor is nested within a Hyper-V partition.                  |
+           +-------+---------------------------------------------------------------------------------------+
|           | 13    | Recommend using INT for MBEC system calls                                             |
+           +-------+---------------------------------------------------------------------------------------+
|           | 14    | Recommend a nested hypervisor using the enlightened VMCS interface. Also indicates that additional nested enlightenments may be available (see leaf `0x4000000A`)
+           +-------+---------------------------------------------------------------------------------------+
|           | 15    | UseSyncedTimeline – Indicates the partition should consume the QueryPerformanceCounter bias provided by the root partition.
+           +-------+---------------------------------------------------------------------------------------+
|           | 16    | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 17    | UseDirectLocalFlushEntire – Indicates the guest should toggle CR4.PGE to flush the entire TLB, as this is more performant than making a hypercall.
+           +-------+---------------------------------------------------------------------------------------+
|           | 18    | NoNonArchitecturalCoreSharing - Indicates that a virtual processor will never share a physical core with another virtual processor, except for virtual processors that are reported as sibling SMT threads. This can be used as an optimization to avoid the performance overhead of STIBP.
+           +-------+---------------------------------------------------------------------------------------+
|           | 31-19 | Reserved                                                                              |
+-----------+-------+---------------------------------------------------------------------------------------+
| EBX       |       | Recommended number of attempts to retry a spinlock failure before notifying the hypervisor about the failures. 0xFFFFFFFF indicates never to retry.
+-----------+-------+---------------------------------------------------------------------------------------+
| ECX       | 6-0   | ImplementedPhysicalAddressBits – Reports the physical address width (MAXPHYADDR) reported by the system’s physical processors. If all bits contain 0, the feature is not supported. Note that the value reported is the actual number of physical address bits, and not the bit position used to represent that number.
+           +-------+---------------------------------------------------------------------------------------+
|           | 31-7  | Reserved                                                                              |
+-----------+-------+---------------------------------------------------------------------------------------+
| EDX       |       |   Reserved                                                                            |
-->

<table>
    <thead>
        <tr>
            <th>Inscrire</th>
            <th>Bits</th>
            <th>Informations fournies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="20">EAX</td>
            <td>0</td>
            <td>Il est recommandé d’utiliser l’hyperappel pour les commutateurs d’espace d’adressage plutôt que l’instruction MOV dans CR3.</td>
        </tr>
        <tr>
            <td>1</td>
            <td>Il est recommandé d’utiliser hypercall pour les vidages TLB locaux plutôt que INVLPG ou MOV vers CR3.</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Il est recommandé d’utiliser l’hyperappel pour les vidages TLB distants plutôt que les interruptions entre processeurs.</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Vous recommandons d’utiliser MSRs pour accéder à APIC Registers EOI, ICR et TPR plutôt que leurs équivalents mappés en mémoire.</td>
        </tr>
        <tr>
            <td>4</td>
            <td>Vous recommandons d’utiliser la MSR fournie par l’hyperviseur pour lancer une réinitialisation du système.</td>
        </tr>
        <tr>
            <td>5</td>
            <td>Vous recommandons d’utiliser le minutage souple pour cette partition. S’il est utilisé, la machine virtuelle doit désactiver les délais d’expiration de la surveillance qui reposent sur la remise en temps opportun des interruptions externes.</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Vous recommandons d’utiliser le remappage DMA.</td>
        </tr>
        <tr>
            <td>7</td>
            <td>Vous recommandons d’utiliser le remappage d’interruption.</td>
        </tr>
        <tr>
            <td>8</td>
            <td>Réservé.</td>
        </tr>
        <tr>
            <td>9</td>
            <td>Recommander la dépréciation de AutoEOI.</td>
        </tr>
        <tr>
            <td>10</td>
            <td>Vous recommandons d’utiliser SyntheticClusterIpi hypercall.</td>
        </tr>
        <tr>
            <td>11</td>
            <td>Recommander l’utilisation de l’interface ExProcessorMasks plus récente.</td>
        </tr>
        <tr>
            <td>12</td>
            <td>Indique que l’hyperviseur est imbriqué dans une partition Hyper-V.</td>
        </tr>
        <tr>
            <td>13</td>
            <td>Vous recommandons d’utiliser INT pour les appels système MBEC.</td>
        </tr>
        <tr>
            <td>14</td>
            <td>Recommander un hyperviseur imbriqué à l’aide de l’interface VMCS recommandée. Indique également que des autres indisponibilités imbriquées supplémentaires peuvent être disponibles (consultez feuille 0x4000000A).</td>
        </tr>
        <tr>
            <td>15</td>
            <td>UseSyncedTimeline : indique que la partition doit consommer l’écart QueryPerformanceCounter fourni par la partition racine.</td>
        </tr>
        <tr>
            <td>16</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>17</td>
            <td>UseDirectLocalFlushEntire : indique que l’invité doit basculer CR4. PGE pour vider l’ensemble du TLB, car il est plus performant que d’effectuer un hyperappel.</td>
        </tr>
        <tr>
            <td>18</td>
            <td>NoNonArchitecturalCoreSharing : indique que le partage de base n’est pas possible. Cela peut être utilisé comme optimisation pour éviter la surcharge de performance de STIBP.</td>
        </tr>
       <tr>
            <td>31-19</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>EBX</td>
            <td></td>
            <td>Nombre recommandé de tentatives de réexécution d’une erreur de verrouillage SpinLock avant de notifier l’hyperviseur des échecs. 0xFFFFFFFF indique ne jamais notifier.</td>
        </tr>
        <tr>
            <td rowspan="2">ECX</td>
            <td>6-0</td>
            <td>ImplementedPhysicalAddressBits : signale la largeur d’adresse physique (MAXPHYADDR) signalée par les processeurs physiques du système. Si tous les bits contiennent 0, la fonctionnalité n’est pas prise en charge. Notez que la valeur signalée est le nombre réel de bits d’adresse physique, et non la position de bit utilisée pour représenter ce nombre.</td>
        </tr>
        <tr>
            <td>31-7</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>EDX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
    </tbody>
</table>

### <a name="hypervisor-implementation-limits---0x40000005"></a>Limites d’implémentation de l’hyperviseur-0x40000005

Décrit les limites d’échelle prises en charge dans l’implémentation de l’hyperviseur actuel. Si une valeur est égale à zéro, l’hyperviseur n’expose pas les informations correspondantes ; dans le cas contraire, elles ont ces significations.

| Inscrire  | Informations fournies                                                                 |
|-----------|--------------------------------------------------------------------------------------|
| EAX       | Nombre maximal de processeurs virtuels pris en charge                                   |
| EBX       | Nombre maximal de processeurs logiques pris en charge                                   |
| ECX       | Nombre maximal de vecteurs d’interruption physiques disponibles pour le remappage d’interruption.  |
| EDX       | Réservé                                                                             |

### <a name="implementation-hardware-features---0x40000006"></a>Implémentation des fonctionnalités matérielles-0x40000006

Indique les fonctionnalités spécifiques au matériel qui ont été détectées et qui sont actuellement utilisées par l’hyperviseur.

<!---
+-----------+-------+---------------------------------------------------------------------------------------+
| Register  | Bits  | Information Provided                                                                  |
+===========+=======+=======================================================================================+
| EAX       | 0     | Support for APIC overlay assist is detected and in use                                |
+           +-------+---------------------------------------------------------------------------------------+
|           | 1     | Support for MSR bitmaps is detected and in use                                        |
+           +-------+---------------------------------------------------------------------------------------+
|           | 2     | Support for architectural performance counters is detected and in use                 |
+           +-------+---------------------------------------------------------------------------------------+
|           | 3     | Support for second level address translation is detected and in use                   |
+           +-------+---------------------------------------------------------------------------------------+
|           | 4     | Support for DMA remapping is detected and in use                                      |
+           +-------+---------------------------------------------------------------------------------------+
|           | 5     | Support for interrupt remapping is detected and in use                                |
+           +-------+---------------------------------------------------------------------------------------+
|           | 6     | Indicates that a memory patrol scrubber is present in the hardware                    |
+           +-------+---------------------------------------------------------------------------------------+
|           | 7     | DMA protection is in use                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 8     | HPET is requested                                                                     |
+           +-------+---------------------------------------------------------------------------------------+
|           | 9     | Synthetic timers are volatile                                                         |
+           +-------+---------------------------------------------------------------------------------------+
|           | 31-10 | Reserved                                                                              |
+-----------+-------+---------------------------------------------------------------------------------------+
| EBX       | Reserved                                                                                      |
+-----------+-----------------------------------------------------------------------------------------------+
| ECX       | Reserved                                                                                      |
+-----------+-----------------------------------------------------------------------------------------------+
| EDX       | Reserved                                                                                      |
-->

<table>
    <thead>
        <tr>
            <th>Inscrire</th>
            <th>Bits</th>
            <th>Informations fournies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="11">EAX</td>
            <td>0</td>
            <td>La prise en charge de l’assistance à la superposition APIC est détectée et en cours d’utilisation.</td>
        </tr>
        <tr>
            <td>1</td>
            <td>La prise en charge des bitmaps MSR est détectée et en cours d’utilisation.</td>
        </tr>
        <tr>
            <td>2</td>
            <td>La prise en charge des compteurs de performance architecturaux est détectée et en cours d’utilisation.</td>
        </tr>
        <tr>
            <td>3</td>
            <td>La prise en charge de la traduction d’adresses de second niveau est détectée et en cours d’utilisation.</td>
        </tr>
        <tr>
            <td>4</td>
            <td>La prise en charge du remappage DMA est détectée et en cours d’utilisation.</td>
        </tr>
        <tr>
            <td>5</td>
            <td>La prise en charge du remappage d’interruption est détectée et en cours d’utilisation.</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Indique qu’un épurateur de patrouille de mémoire est présent dans le matériel.</td>
        </tr>
        <tr>
            <td>7</td>
            <td>La protection DMA est en cours d’utilisation.</td>
        </tr>
        <tr>
            <td>8</td>
            <td>HPET est demandé.</td>
        </tr>
        <tr>
            <td>9</td>
            <td>Les minuteurs synthétiques sont volatils.</td>
        </tr>
        <tr>
            <td>31-10</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>EBX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>ECX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>EDX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
    </tbody>
</table>

### <a name="nested-hypervisor-feature-identification---0x40000009"></a>Identification des fonctionnalités de l’hyperviseur imbriqué-0x40000009

Décrit les fonctionnalités exposées à la partition par l’hyperviseur lors de l’exécution du imbriqué. EAX décrit l’accès à MSRs virtuel. EDX décrit l’accès aux hyperappels.

<!---
+-----------+-------+---------------------------------------------------------------------------------------+
| Register  | Bits  | Information Provided                                                                  |
+===========+=======+=======================================================================================+
| EAX       | 1-0   | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 2     | AccessSynicRegs                                                                       |
+           +-------+---------------------------------------------------------------------------------------+
|           | 3     | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 4     | AccessIntrCtrlRegs                                                                    |
+           +-------+---------------------------------------------------------------------------------------+
|           | 5     | AccessHypercallMsrs                                                                   |
+           +-------+---------------------------------------------------------------------------------------+
|           | 6     | AccessVpIndex                                                                         |
+           +-------+---------------------------------------------------------------------------------------+
|           | 11-7  | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 12    | AccessReenlightenmentControls                                                         |
+           +-------+---------------------------------------------------------------------------------------+
|           | 31-13 | Reserved                                                                              |
+-----------+-------+---------------------------------------------------------------------------------------+
| EBX       | Reserved                                                                                      |
+-----------+-----------------------------------------------------------------------------------------------+
| ECX       | Reserved                                                                                      |
+-----------+-----------------------------------------------------------------------------------------------+
| EDX       | 3-0   | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 4     | XmmRegistersForFastHypercallAvailable                                                 |
+           +-------+---------------------------------------------------------------------------------------+
|           | 14-5  | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 15    | FastHypercallOutputAvailable                                                          |
+           +-------+---------------------------------------------------------------------------------------+
|           | 16    | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 17    | SintPollingModeAvailable                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 31-18 | Reserved                                                                              |
-->

<table>
    <thead>
        <tr>
            <th>Inscrire</th>
            <th>Bits</th>
            <th>Informations fournies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="9">EAX</td>
            <td>1-0</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>2</td>
            <td>AccessSynicRegs</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>4</td>
            <td>AccessIntrCtrlRegs</td>
        </tr>
        <tr>
            <td>5</td>
            <td>AccessHypercallMsrs</td>
        </tr>
        <tr>
            <td>6</td>
            <td>AccessVpIndex</td>
        </tr>
        <tr>
            <td>11-7</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>12</td>
            <td>AccessReenlightenmentControls</td>
        </tr>
        <tr>
            <td>31-13</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>EBX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>ECX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td rowspan="7">EDX</td>
            <td>3-0</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>4</td>
            <td>XmmRegistersForFastHypercallAvailable</td>
        </tr>
        <tr>
            <td>14-5</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>15</td>
            <td>FastHypercallOutputAvailable</td>
        </tr>
        <tr>
            <td>16</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>17</td>
            <td>SintPollingModeAvailable</td>
        </tr>
        <tr>
            <td>31-18</td>
            <td>Réservé</td>
        </tr>
    </tbody>
</table>

### <a name="hypervisor-nested-virtualization-features---0x4000000a"></a>Fonctionnalités de virtualisation imbriquée de l’hyperviseur-0x4000000A

Indique les optimisations de virtualisation imbriquée disponibles pour un hyperviseur imbriqué.

<!---
+-----------+-------+---------------------------------------------------------------------------------------+
| Register  | Bits  | Information Provided                                                                  |
+===========+=======+=======================================================================================+
| EAX       | 7-0   | Enlightened VMCS version (low)                                                        |
+           +-------+---------------------------------------------------------------------------------------+
|           | 15-8  | Enlightened VMCS version (high)                                                       |
+           +-------+---------------------------------------------------------------------------------------+
|           | 16    | Reserved                                                                              |
+           +-------+---------------------------------------------------------------------------------------+
|           | 17    | Indicates support for direct virtual flush hypercalls                                 |
+           +-------+---------------------------------------------------------------------------------------+
|           | 18    | Indicates support for the HvFlushGuestPhysicalAddressSpace and HvFlushGuestPhysicalAddressList hypercalls
+           +-------+---------------------------------------------------------------------------------------+
|           | 19    | Indicates support for using an enlightened MSR bitmap.                                |
+           +-------+---------------------------------------------------------------------------------------+
|           | 31-20 | Reserved                                                                              |
+-----------+-------+---------------------------------------------------------------------------------------+
| EBX       | Reserved                                                                                      |
+-----------+-----------------------------------------------------------------------------------------------+
| ECX       | Reserved                                                                                      |
+-----------+-----------------------------------------------------------------------------------------------+
| EDX       | Reserved                                                                                      |
-->

<table>
    <thead>
        <tr>
            <th>Inscrire</th>
            <th>Bits</th>
            <th>Informations fournies</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="8">EAX</td>
            <td>7-0</td>
            <td>Version de VMCS (faible)</td>
        </tr>
        <tr>
            <td>15-8</td>
            <td>Version de VMCS (élevée)</td>
        </tr>
        <tr>
            <td>16</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>17</td>
            <td>Indique la prise en charge des hyperappels de vidage virtuel direct.</td>
        </tr>
        <tr>
            <td>18</td>
            <td>Indique la prise en charge des hyperappels HvFlushGuestPhysicalAddressSpace et HvFlushGuestPhysicalAddressList.</td>
        </tr>
        <tr>
            <td>19</td>
            <td>Indique la prise en charge de l’utilisation d’une bitmap MSR.</td>
        </tr>
        <tr>
            <td>20</td>
            <td>Indique la prise en charge de la combinaison d’exceptions de virtualisation dans la classe d’exception de défaut de page.</td>
        </tr>
        <tr>
            <td>31-21</td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>EBX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>ECX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
        <tr>
            <td>EDX</td>
            <td></td>
            <td>Réservé</td>
        </tr>
    </tbody>
</table>

## <a name="versioning"></a>Gestion de version

Les informations de version de l’hyperviseur sont encodées dans le nœud terminal `0x40000002` . Deux numéros de version sont fournis : la version principale et la version du service.

La version principale comprend un numéro de version principale et secondaire et un numéro de Build. Celles-ci correspondent aux numéros de version de Microsoft Windows. La version du service décrit les modifications apportées à la version principale.

Les clients sont vivement encouragés à vérifier les fonctionnalités de l’hyperviseur à l’aide de CPUID, `0x40000003` `0x40000005` plutôt qu’en comparant les plages de versions.