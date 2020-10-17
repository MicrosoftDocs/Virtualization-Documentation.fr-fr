---
title: Communication Inter-Partition
description: Communication Inter-Partition
keywords: Hyper-v
author: alexgrest
ms.author: alegre
ms.date: 10/15/2020
ms.topic: reference
ms.prod: windows-10-hyperv
ms.openlocfilehash: 5773ca47af0d5967186af80330e6387772646107
ms.sourcegitcommit: d43632e3dc22e2b7a256b5c15fbb665c047de286
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92120572"
---
# <a name="inter-partition-communication"></a>Communication Inter-Partition

L’hyperviseur fournit deux mécanismes simples pour qu’une partition communique avec une autre : messages et événements. Dans les deux cas, la notification est signalée à l’aide de SynIC (contrôleur d’interruptions synthétiques).

## <a name="synic-messages"></a>Messages SynIC

L’hyperviseur fournit une fonctionnalité simple de communication entre les partitions qui permet à une partition d’envoyer un message paramétré à une autre partition. (Étant donné que le message est envoyé de manière asynchrone, il est dit post.) La partition de destination peut être avertie de l’arrivée de ce message par le biais d’une interruption. Les messages peuvent être envoyés explicitement à l’aide de l’hypercall [HvCallPostMessage](hypercalls/HvCallPostMessage.md) ou implicitement par l’hyperviseur.

### <a name="messages"></a>Messages

Lorsqu’un message est envoyé, l’hyperviseur sélectionne une mémoire tampon de messages libre. L’ensemble des tampons de messages disponibles dépend de l’événement qui a déclenché l’envoi du message.

L’hyperviseur marque la mémoire tampon des messages « en cours d’utilisation » et remplit l’en-tête de message avec le type de message, la taille de la charge utile et les informations sur l’expéditeur. Enfin, il remplit la charge utile du message. Le contenu de la charge utile dépend de l’événement qui a déclenché le message.

L’hyperviseur ajoute ensuite le tampon de messages à une file d’attente de messages de réception. La file d’attente de messages de réception dépend de l’événement qui a déclenché l’envoi du message. Pour tous les types de messages, SINTx est implicite (dans le cas des messages d’interception), explicite (dans le cas des messages du minuteur) ou spécifié par un ID de port (dans le cas de messages invités). Le processeur virtuel cible est explicitement spécifié ou choisi par l’hyperviseur lorsque le message est mis en file d’attente. Les processeurs virtuels dont la page SynIC ou SIM est désactivée ne seront pas considérés comme des cibles potentielles. Si aucune cible n’est disponible, l’hyperviseur met fin à l’opération et retourne une erreur à l’appelant.

L’hyperviseur détermine ensuite si l’emplacement de message SINTx spécifié dans la [page SIM](#sim-page) du processeur virtuel cible est vide. Si le type de message dans l’emplacement du message est égal à HvMessageTypeNone (autrement dit, zéro), l’emplacement du message est supposé être vide. Dans ce cas, l’hyperviseur met en file d’attente la mémoire tampon des messages et copie son contenu dans l’emplacement du message dans la page SIM. L’hyperviseur ne peut copier que le nombre d’octets de charge utile associés au message. L’hyperviseur tente également de générer une interruption déclenchée par le bord pour le SINTx spécifié. Si l’APIC est un logiciel désactivé ou si le SINTx est masqué, l’interruption est perdue. L’arrivée de cette interruption indique à l’invité qu’un nouveau message est arrivé. Si la page SIM est désactivée ou si l’emplacement du message dans la page SIM n’est pas vide, le message reste en file d’attente et aucune interruption n’est générée.

Comme pour toute interruption de priorité fixe, l’interruption n’est pas reconnue par le processeur virtuel tant que le fichier PPR (Register Priority Register) est inférieur au vecteur spécifié dans le registre SINTx et que les interruptions ne sont pas masquées par le processeur virtuel (rFLAGS [IF] a la valeur 1).

Plusieurs mémoires tampons de messages avec le même SINTx peuvent être mises en file d’attente sur un processeur virtuel. Dans ce cas, l’hyperviseur remet le premier message (c’est-à-dire, l’écrit dans la page SIM) et laisse les autres en file d’attente jusqu’à ce que l’un des trois événements se produise :

- Une autre mémoire tampon de messages est mise en file d’attente.
- L’invité indique la « fin de l’interruption » en écrivant dans le registre EOI de l’APIC.
- L’invité indique la « fin du message » en écrivant dans le [Registre FDM](#eom-register)de SynIC.

Dans les trois cas, l’hyperviseur analyse une ou plusieurs files d’attente de tampons de messages et tente de remettre des messages supplémentaires. L’hyperviseur tente également de générer une interruption déclenchée par le bord, indiquant qu’un nouveau message est arrivé.

#### <a name="sim-page"></a>Page SIM

La page SIM se compose d’un tableau de 16 éléments de messages de 256 octets (consultez [HV_MESSAGE](datatypes/HV_MESSAGE.md) structure de données). Chaque élément de tableau (également appelé emplacement de message) correspond à une source d’interruption synthétique unique (SINTx). Un emplacement de message est dit « vide » si le type de message de l’emplacement est égal à HvMessageTypeNone.

L’adresse de la page SIM est spécifiée dans le [Registre simp](#simp-register). L’adresse de la page SIM doit être unique pour chaque processeur virtuel. La programmation de ces pages pour chevaucher d’autres instances des pages SIEF ou SIM ou toute autre page de superposition (par exemple, la page hypercall) entraînera un comportement indéfini.

Les accès en lecture et en écriture par un processeur virtuel à la page SIM se comportent comme des accès en lecture et en écriture à la RAM. Toutefois, l’implémentation SynIC de l’hyperviseur écrit également dans les pages en réponse à certains événements.

Lors de la création et de la réinitialisation du processeur virtuel, la page SIM est effacée à zéro.

#### <a name="recommended-message-handling"></a>Gestion des messages recommandée

Le mécanisme de remise de messages SynIC est conçu pour gérer efficacement la remise et la réception de messages dans une partition cible. Il est recommandé que l’ISR de gestion des messages (routine de service d’interruption) au sein de la partition cible effectue les étapes suivantes :

- Examinez le message qui a été déposé dans l’emplacement de message SIM.
- Copiez le contenu du message dans un autre emplacement et définissez le type de message dans l’emplacement du message sur HvMessageTypeNone.
- Indiquez la fin de l’interruption pour le vecteur en écrivant dans le registre EOI de l’APIC.
- Effectuez toutes les actions impliquées dans le message.

#### <a name="message-sources"></a>Sources de messages

Les classes d’événements qui peuvent déclencher l’envoi d’un message sont les suivantes :

- Interceptions : toute interception d’un processeur virtuel entraîne l’envoi d’un message à la partition parente ou à une VTL plus élevée.
- Minuteurs : les mécanismes de minuterie entraînent l’envoi des messages. Chaque processeur virtuel est associé à quatre tampons de messages de minuteur dédiés, un pour chaque minuterie. La file d’attente de messages de réception appartient à SINTx du processeur virtuel dont le minuteur a déclenché l’envoi du message.
- Messages invités : l’hyperviseur prend en charge le passage de messages en tant que mécanisme de communication entre les hôtes. Les interfaces définies dans cette section permettent à un invité d’envoyer des messages à un autre invité. Les tampons de messages utilisés pour les messages de cette classe proviennent du pool par port du récepteur des tampons de messages invités.

### <a name="message-buffers"></a>Mémoires tampons de messages

Un tampon de messages est utilisé en interne dans l’hyperviseur pour stocker un message jusqu’à ce qu’il soit remis au destinataire. L’hyperviseur gère plusieurs jeux de mémoires tampons de messages.

#### <a name="guest-message-buffers"></a>Mémoires tampons de messages invités

L’hyperviseur gère un ensemble de tampons de messages invités pour chaque port. Ces mémoires tampons sont utilisées pour les messages envoyés de manière explicite d’une partition à l’autre par un invité. Lorsqu’un port est créé, l’hyperviseur alloue seize (16) tampons de messages à partir du pool de mémoire du propriétaire du port. Ces mémoires tampons de messages sont retournées au pool de mémoire lors de la suppression du port.

#### <a name="message-buffer-queues"></a>Files d’attente de tampons de messages

Pour chaque partition et chaque processeur virtuel de la partition, l’hyperviseur gère une file d’attente de mémoires tampons de messages pour chaque SINTx (source d’interruption synthétique) dans le SynIC du processeur virtuel. Toutes les files d’attente de messages d’un processeur virtuel sont vides lors de la création ou de la réinitialisation du processeur virtuel.

#### <a name="reliability-and-sequencing-of-guest-message-buffers"></a>Fiabilité et séquencement des tampons de messages invités

Les messages publiés avec succès par un invité ont été mis en file d’attente de remise par l’hyperviseur. La remise et la réception réelles par la partition cible dépendent de son fonctionnement correct. Les partitions peuvent désactiver la remise de messages à des processeurs virtuels particuliers en désactivant son SynIC ou en désactivant le SIMP.

L’interruption d’une connexion n’affecte pas les messages non remis (en file d’attente). La suppression du port cible libère toujours toutes les mémoires tampons de messages du port, qu’elles soient disponibles ou qu’elles contiennent des messages non remis (en attente).

Les messages arrivent dans l’ordre dans lequel ils ont été publiés avec succès. Si le port de réception est associé à un processeur virtuel spécifique, les messages arrivent dans l’ordre dans lequel ils ont été publiés. Si le port de réception est associé à HV_ANY_VP, il n’est pas garanti que les messages arrivent dans un ordre particulier.

## <a name="synic-event-flags"></a>Indicateurs d’événement SynIC

En plus des messages, le SynIC prend en charge un deuxième type de mécanisme de notification entre les partitions appelé indicateurs d’événement. Les indicateurs d’événement peuvent être définis explicitement à l’aide de l’hypercall [HvCallSignalEvent](hypercalls/HvCallSignalEvent.md) ou implicitement par l’hyperviseur.

### <a name="event-flags-versus-messages"></a>Indicateurs d’événement et messages

Les indicateurs d’événement sont plus légers que les messages et représentent donc une surcharge plus faible. En outre, les indicateurs d’événement ne nécessitent pas d’allocation de mémoire tampon ni de mise en file d’attente dans l’hyperviseur, de sorte que HvCallSignalEvent n’échouera jamais en raison de ressources insuffisantes.

### <a name="event-flag-delivery"></a>Remise de l’indicateur d’événement

Lorsqu’une partition appelle HvCallSignalEvent, elle spécifie un numéro d’indicateur d’événement. L’hyperviseur réagit en définissant atomiquement un bit dans la [page SIEF](#sief-page)du processeur virtuel de réception. Les processeurs virtuels dont la page SynIC ou SIEF est désactivée ne sont pas considérés comme des cibles potentielles. Si aucune cible n’est disponible, l’hyperviseur met fin à l’opération et retourne une erreur à l’appelant.

Si l’indicateur d’événement a été précédemment effacé, l’hyperviseur tente d’informer la partition de réception que l’indicateur est maintenant défini en générant une interruption déclenchée par un bord. Le processeur virtuel cible, ainsi que le SINTx cible, sont spécifiés dans le cadre de la création d’un port. Si le SINTx est masqué, HvSignalEvent retourne HV_STATUS_INVALID_SYNIC_STATE.

Comme pour toute interruption externe de priorité fixe, l’interruption n’est pas reconnue par le processeur virtuel tant que le registre de priorité de processus (PPR) est inférieur au vecteur spécifié dans le registre SINTx et que les interruptions ne sont pas masquées par le processeur virtuel (rFLAGS [IF] a la valeur 1).

#### <a name="sief-page"></a>Page SIEF

La page SIEF se compose d’un tableau de 16 éléments d’indicateurs d’événements de 256 octets (voir [HV_SYNIC_EVENT_FLAGS](datatypes/HV_SYNIC_EVENT_FLAGS.md)). Chaque élément de tableau correspond à une source d’interruption synthétique unique (SINTx).

L’adresse de la page SIEF est spécifiée dans le [Registre SIEF](#siefp-register). L’adresse de la page SIEF doit être unique pour chaque processeur virtuel. La programmation de ces pages pour chevaucher d’autres instances des pages SIEF ou SIM ou toute autre page de superposition (par exemple, la page hypercall) entraînera un comportement indéfini.

Les accès en lecture et en écriture par un processeur virtuel à la page SIEF se comportent comme des accès en lecture et en écriture à la RAM. Toutefois, l’implémentation SynIC de l’hyperviseur écrit également dans les pages en réponse à certains événements.

Lors de la création et de la réinitialisation du processeur virtuel, la page SIEF est effacée à zéro.

### <a name="recommended-event-flag-handling"></a>Gestion des indicateurs d’événements recommandée

Il est recommandé d’effectuer les étapes suivantes dans la routine d’interruption (ISR) d’indicateur d’événement dans la partition cible :

- Examinez les indicateurs d’événement et déterminez ceux qui, le cas échéant, sont définis.
- Effacez un ou plusieurs indicateurs d’événement à l’aide d’une opération verrouillée (atomique) telle que LOCK ou LOCK CMPXCHG.
- Indiquez la fin de l’interruption pour le vecteur en écrivant dans le registre EOI de l’APIC.
- Effectuez toutes les actions impliquées par les indicateurs d’événement qui ont été définis.

## <a name="ports-and-connections"></a>Ports et connexions

Un message ou un événement envoyé d’un invité à un autre doit être envoyé par le biais d’une connexion pré-allouée. Une connexion, à son tour, doit être associée à un port de destination.

Un port est alloué à partir du pool de mémoire du récepteur et spécifie le processeur virtuel et le SINTx à cibler. Les ports d’événement ont un « numéro d’indicateur de base » et un « nombre d’indicateurs » qui permettent à l’appelant de spécifier une plage d’indicateurs d’événement valides pour ce port.

Les connexions sont allouées à partir du pool de mémoire de l’expéditeur. Lorsqu’une connexion est créée, elle doit être associée à un port valide. Cette liaison crée un canal de communication unidirectionnel simple. Si un port est supprimé par la suite, sa connexion, alors qu’il reste, devient inutile.

## <a name="synic-msrs"></a>SynIC MSRs

Outre les registres mappés en mémoire définis pour un APIC local, les registres spécifiques aux modèles (MSRs) suivants sont définis dans le SynIC. Chaque processeur virtuel possède sa propre copie de ces registres, afin qu’ils puissent être programmés indépendamment.

| Adresse MSR             | Nom du Registre         | Fonction                             |
|-------------------------|-----------------------|--------------------------------------|
| 0x40000080              | SCONTROL              | Contrôle SynIC                        |
| 0x40000081              | SVERSION              | Version de SynIC                        |
| 0x40000082              | SIEFP                 | Page indicateurs d’événement d’interruption           |
| 0x40000083              | SIMPLIFIÉ                  | Page message d’interruption               |
| 0x40000084              | MOIS                   | Fin du message                       |
| 0x40000090              | SINT0                 | Source d’interruption 0 (hyperviseur)      |
| 0x40000090              | SINT1                 | Source d’interruption 1                   |
| 0x40000090              | SINT2                 | Source d’interruption 2                   |
| 0x40000090              | SINT3                 | Source d’interruption 3                   |
| 0x40000090              | SINT4                 | Source d’interruption 4                   |
| 0x40000090              | SINT5                 | Source d’interruption 5                   |
| 0x40000090              | SINT6                 | Source d’interruption 6                   |
| 0x40000090              | SINT7                 | Source d’interruption 7                   |
| 0x40000090              | SINT8                 | Source d’interruption 8                   |
| 0x40000090              | SINT9                 | Source d’interruption 9                   |
| 0x40000090              | SINT10                | Source d’interruption 10                  |
| 0x40000090              | SINT11                | Source d’interruption 11                  |
| 0x40000090              | SINT12                | Source d’interruption 12                  |
| 0x40000090              | SINT13                | Source d’interruption 13                  |
| 0x40000090              | SINT14                | Source d’interruption 14                  |
| 0x40000090              | SINT15                | Source d’interruption 15                  |

### <a name="scontrol-register"></a>Registre SCONTROL

Ce registre est utilisé pour contrôler le comportement SynIC du processeur virtuel.

Au moment de la création du processeur virtuel et lors de la réinitialisation du processeur, la valeur de ce SCONTROL (registre de contrôle SynIC) est 0x0000000000000000. Par conséquent, la mise en file d’attente de messages et les notifications d’indicateur d’événement seront désactivées.

| Bits      | Champ           | Description                                                                 | Attributs     |
|-----------|-----------------|-----------------------------------------------------------------------------|----------------|
| 63:1      | RsvdP           | La valeur doit être conservée                                                     | Lecture/écriture   |
| 0         | Activer          | Lorsqu’il est défini, ce processeur virtuel permet de publier les notifications de mise en file d’attente et Message Queuing dans son SynIC. Lorsque vous désactivez, Message Queuing et les notifications d’indicateur d’événement ne peuvent pas être dirigés vers ce processeur virtuel. | Lecture/écriture |

### <a name="sversion-register"></a>Registre SVERSION

Il s’agit d’un registre en lecture seule qui retourne le numéro de version du SynIC. Les tentatives d’écriture dans ce registre entraînent une erreur de #GP.

| Bits      | Champ           | Description                                                                 | Attributs     |
|-----------|-----------------|-----------------------------------------------------------------------------|----------------|
| 63:32     | RsvdP           |                                                                             | Lire           |
| 31:0      | Version de SynIC   | Numéro de version du SynIc                                                 | Lire           |

#### <a name="siefp-register"></a>Registre SIEFP

Au moment de la création du processeur virtuel et lors de la réinitialisation du processeur, la valeur de cette SIEFP (page indicateurs d’événements d’interruptions synthétiques) est 0x0000000000000000. Par conséquent, le SIEFP est désactivé par défaut. L’invité doit l’activer en définissant le bit 0. Si l’adresse de base spécifiée se situe au-delà de la fin de l’espace de l’adresse GPA de la partition, la page SIEFP n’est pas accessible à l’invité. Lors de la modification du Registre, les invités doivent conserver la valeur des bits réservés (1 à 11) pour assurer une compatibilité future.

| Bits      | Champ           | Description                                                                 | Attributs     |
|-----------|-----------------|-----------------------------------------------------------------------------|----------------|
| 63:12     | Adresse de base    | Adresse de base (dans l’espace GPA) de SIEFP (12 bits de poids faible supposé être désactivé)   | Lecture/écriture   |
| 11:1      | RsvdP           | Réservé, la valeur doit être conservée                                         | Lecture/écriture   |
| 0         | Activer          | SIEFP activer                                                                | Lecture/écriture   |

#### <a name="simp-register"></a>Registre SIMP

Au moment de la création du processeur virtuel et lors de la réinitialisation du processeur, la valeur de cette page de message d’interruptions synthétiques est 0x0000000000000000. Par conséquent, le SIMP est désactivé par défaut. L’invité doit l’activer en définissant le bit 0. Si l’adresse de base spécifiée se situe au-delà de la fin de l’espace de l’adresse GPA de la partition, la page SIMP n’est pas accessible à l’invité. Lors de la modification du Registre, les invités doivent conserver la valeur des bits réservés (1 à 11) pour assurer une compatibilité future.

| Bits      | Champ           | Description                                                                 | Attributs     |
|-----------|-----------------|-----------------------------------------------------------------------------|----------------|
| 63:12     | Adresse de base    | Adresse de base (dans l’espace GPA) de la valeur SIMP (12 bits de poids faible supposé être désactivée)   | Lecture/écriture   |
| 11:1      | RsvdP           | Réservé, la valeur doit être conservée                                         | Lecture/écriture   |
| 0         | Activer          | Activation SIMP                                                                 | Lecture/écriture   |

#### <a name="sintx-registers"></a>Registres SINTx

Au moment de la création du processeur virtuel, la valeur par défaut de tous les registres SINTx (source d’interruption synthétique) est 0x0000000000010000. Par conséquent, toutes les sources d’interruptions synthétiques sont masquées par défaut. L’invité doit les afficher en programmant un vecteur approprié et en effaçant le bit 16.

La définition du bit d’interrogation aura pour effet de démasquer une source d’interruption, sauf qu’une interruption réelle n’est pas générée.

L’indicateur AutoEOI indique qu’un EOI implicite doit être exécuté par l’hyperviseur lorsqu’une interruption est remise au processeur virtuel. En outre, l’hyperviseur efface automatiquement l’indicateur correspondant dans le « registre in-service » (ISR) de l’APIC virtuel. Si l’invité active ce comportement, il ne doit pas effectuer de EOI dans sa routine de service d’interruption.
L’indicateur AutoEOI peut être activé à tout moment, même si l’invité doit effectuer une EOI explicite sur une interruption en cours. la considération de la durée d’exécution rend difficile de savoir si une interruption particulière a besoin d’être EOI. par conséquent, il est recommandé de ne pas modifier ses paramètres. De même, l’indicateur AutoEOI peut être désactivé à tout moment, même si les mêmes préoccupations en matière d’interruptions en vol s’appliquent

Les valeurs valides pour Vector sont 16-255 inclusives. La spécification d’un numéro de vecteur non valide entraîne une #GP.

| Bits      | Champ           | Description                                                                 | Attributs     |
|-----------|-----------------|-----------------------------------------------------------------------------|----------------|
| 63:19     | RsvdP           | Réservé, la valeur doit être conservée                                         | Lecture/écriture   |
| 18        | Interrogation         | Active le mode d’interrogation                                                        | Lecture/écriture   |
| 17        | AutoEOI         | Définir si un EOI implicite doit être exécuté en cas de remise d’interruption          | Lecture/écriture   |
| 16        | Masqué          | Définir si le Saint-est masqué                                                   | Lecture/écriture   |
| 15:8      | RsvdP           | Réservé, la valeur doit être conservée                                         | Lecture/écriture   |
| 7:0       | Vecteur          | Vecteur d’interruption                                                            | Lecture/écriture   |

#### <a name="eom-register"></a>Registre FDM

Une écriture sur la fin du message (FDM) inscrite par l’invité entraîne l’analyse par l’hyperviseur de la ou des files d’attente de tampons de messages internes associées au processeur virtuel. Si une file d’attente de tampons de messages contient une mémoire tampon de messages en file d’attente, l’hyperviseur tente de remettre le message. La remise de messages a échoué si la page SIM est activée et que l’emplacement du message correspondant à SINTx est vide (autrement dit, le type de message dans l’en-tête est défini sur HvMessageTypeNone). Si un message est correctement remis, sa mémoire tampon de messages interne correspondante est déplacée dans la file d’attente et marquée comme étant libre. Si le SINTx correspondant n’est pas masqué, une interruption déclenchée par le bord est remise (autrement dit, le bit correspondant dans la fonction IRR est défini).

Ce registre peut être utilisé par les invités pour « interroger » les messages. Il peut également être utilisé pour vider la file d’attente de messages d’un SINTx qui a été désactivé (autrement dit, masqué).

Si les files d’attente de messages sont toutes vides, une écriture sur le registre FDM est une absence d’opération.

Les lectures de l’enregistrement FDM renvoient toujours des zéros.

| Bits      | Champ           | Description                                                                 | Attributs     |
|-----------|-----------------|-----------------------------------------------------------------------------|----------------|
| 63:0      | RsvdZ           | Déclencheur en écriture seule                                                          | Write          |