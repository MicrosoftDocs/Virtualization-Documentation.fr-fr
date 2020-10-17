---
title: HV_MESSAGE
description: HV_MESSAGE
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: af5a6a314f51178021ffaa7b3cc07c8baa016990
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120684"
---
# <a name="hv_message"></a>HV_MESSAGE

Les messages SynIC sont d’une taille fixe composée d’un en-tête de message (qui comprend le type de message et les informations d’origine du message) suivi de la charge utile. Les messages envoyés en réponse à [HvCallPostMessage](../hypercalls/HvCallPostMessage.md) contiennent l’ID de port. Les messages d’interception contiennent l’ID de partition de la partition dont le processeur virtuel a généré l’interception. Les interceptions du minuteur n’ont pas d’ID d’origine (autrement dit, l’ID spécifié est zéro).

L’indicateur MessagePending indique si des messages sont en attente dans la file d’attente des messages de la source d’interruptions synthétiques. Si c’est le cas, une « fin de message » doit être exécutée par l’invité après avoir vidé l’emplacement du message. Cela permet d’effectuer des écritures opportunistes dans la partition FDM MSR (uniquement lorsque cela est nécessaire). Notez que cet indicateur peut être défini par l’hyperviseur lors de la remise de messages ou à tout moment par la suite. L’indicateur doit être testé une fois que l’emplacement du message a été vidé et, s’il est défini, il y a un ou plusieurs messages en attente et la « fin du message » doit être exécutée.

## <a name="syntax"></a>Syntaxe

 ```c
#define HV_MESSAGE_SIZE 256
#define HV_MESSAGE_MAX_PAYLOAD_BYTE_COUNT 240
#define HV_MESSAGE_MAX_PAYLOAD_QWORD_COUNT 30

typedef struct
{
    UINT8 MessagePending:1;
    UINT8 Reserved:7;
} HV_MESSAGE_FLAGS;

typedef struct
{
    HV_MESSAGE_TYPE MessageType;
    UINT16 Reserved;
    HV_MESSAGE_FLAGS MessageFlags;
    UINT8 PayloadSize;
    union
    {
        UINT64 OriginationId;
        HV_PARTITION_ID Sender;
        HV_PORT_ID Port;
    };
} HV_MESSAGE_HEADER;

typedef struct
{
    HV_MESSAGE_HEADER Header;
    UINT64 Payload[HV_MESSAGE_MAX_PAYLOAD_QWORD_COUNT];
} HV_MESSAGE;
 ```
