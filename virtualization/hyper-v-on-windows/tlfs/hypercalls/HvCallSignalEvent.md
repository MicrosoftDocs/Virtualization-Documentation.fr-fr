---
title: HvCallSignalEvent
description: HvCallSignalEvent hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 7d64700cbb2589e9d9b7780ea5d1acb2f6b8cfe3
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120560"
---
# <a name="hvcallsignalevent"></a>HvCallSignalEvent

L’hyperappel HvCallSignalEvent signale un événement dans une partition qui possède le port associé à la connexion spécifiée.

L’événement est signalé par la définition d’un bit dans la page SIEF de l’un des processeurs virtuels de la partition de réception. L’appelant spécifie un numéro d’indicateur relatif. Le nombre de bits SIEF réel est calculé par l’hyperviseur en ajoutant le numéro d’indicateur spécifié au numéro de l’indicateur de base associé au port.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallSignalEvent(
    _In_ HV_CONNECTION_ID ConnectionId,
    _In_ UINT16 FlagNumber
    );
 ```

## <a name="call-code"></a>Appeler du code

`0x005D` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `ConnectionId`          | 0          | 4        | Spécifie l’ID de la connexion.       |
| `FlagNumber`            | 4          | 2        | Spécifie l’index relatif de l’indicateur d’événement que l’appelant souhaite définir dans la zone SIEF cible. Ce nombre est relatif au numéro d’indicateur de base associé au port. |
| RsvdZ                   | 6          | 2        |                                           |

## <a name="return-values"></a>Valeurs de retour

| Code d’état                         | Condition d'erreur                                       |
|-------------------------------------|-------------------------------------------------------|
| `HV_STATUS_ACCESS_DENIED`           | La partition de l’appelant ne possède pas le privilège SignalEvents. |
| `HV_STATUS_INVALID_CONNECTION_ID`   | L’ID de connexion spécifié n’est pas valide.               |
| `HV_STATUS_INVALID_PORT_ID`         | Le port associé à la connexion spécifiée a été supprimé. |
|                                     | Le port associé à la connexion spécifiée appartient à une partition qui n’est pas dans l’état « actif ». |
|                                     | Le port associé à la connexion spécifiée n’est pas un port de type « Event ». |
| `HV_STATUS_INVALID_PARAMETER`       | Le numéro d’indicateur spécifié est supérieur ou égal au nombre d’indicateurs du port. |
| `HV_STATUS_INVALID_VP_INDEX`        | Le VP cible n’existe plus ou il n’y a aucun VPs disponible sur lequel le message peut être publié. |
| `HV_STATUS_INVALID_SYNIC_STATE`     | Le SynIC du VP cible est désactivé et ne peut pas accepter les événements signalés. |
|                                     | La page SIEF du VP cible est désactivée.                 |