---
title: HV_TIMER_MESSAGE_PAYLOAD
description: HV_TIMER_MESSAGE_PAYLOAD
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 48a66d841b20f575868a30f2f08d3377c724effd
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120647"
---
# <a name="hv_timer_message_payload"></a>HV_TIMER_MESSAGE_PAYLOAD

Les messages d’expiration du minuteur sont envoyés lorsqu’un événement de minuterie se déclenche. Cette structure définit la charge utile de message.

## <a name="syntax"></a>Syntaxe

```c
typedef struct
{
    UINT32          TimerIndex;
    UINT32          Reserved;
    HV_NANO100_TIME ExpirationTime;     // When the timer expired
    HV_NANO100_TIME DeliveryTime;       // When the message was delivered
} HV_TIMER_MESSAGE_PAYLOAD;
 ```

TimerIndex est l’index du minuteur synthétique (de 0 à 3) qui a généré le message. Cela permet à un client de configurer plusieurs minuteurs pour qu’ils utilisent le même vecteur d’interruption et différencient leurs messages.

ExpirationTime est l’heure d’expiration attendue de la minuterie mesurée en unités de 100 nanosecondes à l’aide de la base de temps du compteur de temps de référence de la partition. Notez que le message d’expiration peut arriver après l’heure d’expiration.

DeliveryTime est l’heure à laquelle le message est placé dans l’emplacement de message respectif de la page SIM. L’heure est mesurée en unités de 100 nanosecondes en fonction du compteur de temps de référence de la partition.

## <a name="see-also"></a>Voir aussi

 [HV_MESSAGE](HV_MESSAGE.md)