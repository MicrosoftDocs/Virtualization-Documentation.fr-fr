---
title: HvCallPostMessage
description: HvCallPostMessage hypercall
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 7fc0693341316966f986bece60d007b651ce6870
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120483"
---
# <a name="hvcallpostmessage"></a>HvCallPostMessage

L’hyperappel HvCallPostMessage tente de poster (autrement dit, envoie de façon asynchrone) un message à la connexion spécifiée, qui a un port de destination associé. Si le message est correctement publié, il est mis en file d’attente pour être remis à un processeur virtuel au sein de la partition associée au port.

## <a name="interface"></a>Interface

 ```c
HV_STATUS
HvCallPostMessage(
    _In_ HV_CONNECTION_ID ConnectionId,
    _In_ HV_MESSAGE_TYPE MessageType,
    _In_ UINT32 PayloadSize,
    _In_reads_bytes_(PayloadSize) PCVOID Message
    );
 ```

## <a name="call-code"></a>Appeler du code

`0x005C` Simple

## <a name="input-parameters"></a>Paramètres d’entrée

| Nom                    | Offset     | Taille     | Informations fournies                      |
|-------------------------|------------|----------|-------------------------------------------|
| `ConnectionId`          | 0          | 4        | Spécifie l’ID de la connexion.       |
| RsvdZ                   | 4          | 4        |                                           |
| `MessageType`           | 8          | 4        | Spécifie le type de message qui s’affichera dans l’en-tête de message. L’appelant peut spécifier n’importe quel type de message 32 bits dont le bit le plus significatif est effacé, à l’exception de zéro. |
| `PayloadSize`           | 12         | 4        | Spécifie le nombre d’octets inclus dans le message. |
| `Message`               | 16         | 240      | Spécifie la charge utile du message, jusqu’à 240 octets au total. Seuls les n premiers octets sont réellement envoyés à la partition de destination, où n est fourni dans le paramètre PayloadSize. |

## <a name="return-values"></a>Valeurs de retour

| Code d’état                         | Condition d'erreur                                       |
|-------------------------------------|-------------------------------------------------------|
| `HV_STATUS_ACCESS_DENIED`           | La partition de l’appelant ne possède pas le privilège PostMessages. |
| `HV_STATUS_INVALID_CONNECTION_ID`   | L’ID de connexion spécifié n’est pas valide.               |
| `HV_STATUS_INVALID_PORT_ID`         | Le port associé à la connexion spécifiée a été supprimé. |
|                                     | Le port associé à la connexion spécifiée appartient à une partition qui n’est pas dans l’état « actif ». |
|                                     | Le port associé à la connexion spécifiée n’est pas un port de type « message ». |
| `HV_STATUS_INVALID_PARAMETER`       | Le bit le plus significatif du type de message spécifié est défini. |
|                                     | Le paramètre MessageType spécifie une valeur égale à zéro.  |
|                                     | La taille de la charge utile spécifiée dépasse 240 octets.         |
| `HV_STATUS_INSUFFICIENT_BUFFERS`    | Le port n’a pas de tampons de messages invités disponibles.      |
| `HV_STATUS_INVALID_VP_INDEX`        | Le VP cible n’existe plus ou il n’y a aucun VPs disponible sur lequel le message peut être publié. |
| `HV_STATUS_INVALID_SYNIC_STATE`     | Le SynIC du VP cible est désactivé et ne peut pas accepter les messages publiés. |
|                                     | La page SIM du VP cible est désactivée.                 |