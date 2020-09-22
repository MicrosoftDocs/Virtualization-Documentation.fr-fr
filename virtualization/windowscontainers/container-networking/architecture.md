---
title: Mise en réseau de conteneurs Windows
description: Présentation de l’architecture des réseaux de conteneurs Windows.
keywords: docker, conteneurs
author: daschott
ms.author: jgerend
ms.date: 08/13/2020
ms.topic: overview
ms.assetid: 538871ba-d02e-47d3-a3bf-25cda4a40965
ms.openlocfilehash: 71228abea66084f9de1ee29daaff1d3d3f1ffc40
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990872"
---
# <a name="windows-container-networking"></a>Mise en réseau de conteneurs Windows

>[!IMPORTANT]
>Reportez-vous à l'article [Mise en réseau de conteneur Docker](https://docs.docker.com/engine/userguide/networking/) pour les commandes, les options et la syntaxe de mise en réseau de docker en général. À l’exception des cas décrits dans [fonctionnalités non prises en charge et options réseau](#unsupported-features-and-network-options), toutes les commandes de mise en réseau de l’ancrage sont prises en charge sur Windows avec la même syntaxe que sur Linux. Toutefois, les piles réseau Windows et Linux sont différentes et, par conséquent, vous constaterez que certaines commandes réseau Linux (par exemple, ifconfig) ne sont pas prises en charge sur Windows.

## <a name="basic-networking-architecture"></a>Architecture de mise en réseau de base

Cette rubrique fournit une vue d’ensemble de la façon dont l’ancrage crée et gère les réseaux hôtes sur Windows. Sur le plan de la mise en réseau, les conteneurs Windows fonctionnent de la même façon que des machines virtuelles. Chaque conteneur possède une carte réseau virtuelle (vNIC) connectée à un commutateur virtuel Hyper-V (vSwitch). Windows prend en charge cinq [pilotes ou modes de mise en réseau](./network-drivers-topologies.md) différents qui peuvent être créés par le biais de l’arrimeur : *NAT*, *Overlay*, *transparent*, *l2bridge*et *l2tunnel*. Choisissez le pilote réseau le mieux adapté à l’infrastructure de votre réseau physique et à la configuration requise du réseau (hôte unique ou hôtes multiples).

![texte](media/windowsnetworkstack-simple.png)

Lors de sa première exécution, le moteur Docker crée un réseau NAT par défaut, « nat », qui utilise un vSwitch interne et un composant Windows nommé `WinNAT`. S’il existe des commutateurs virtuels externes préexistants sur l’hôte qui ont été créés via PowerShell ou le Gestionnaire Hyper-V, ils sont également disponibles pour l’ancrage à l’aide du pilote réseau *transparent* et peuvent être affichés lorsque vous exécutez la ``docker network ls`` commande.

![texte](media/docker-network-ls.png)

- Un vswitch **interne** est un serveur qui n’est pas directement connecté à une carte réseau sur l’hôte de conteneur.
- Un vswitch **externe** est directement connecté à une carte réseau sur l’hôte de conteneur.

![texte](media/get-vmswitch.png)

Le réseau « nat » est le réseau par défaut des conteneurs qui s’exécutent sur Windows. Tous les conteneurs qui s'exécutent sur Windows sans indicateurs ou arguments pour implémenter des configurations de réseau spécifiques sont attachés au réseau « nat » par défaut, et une adresse IP leur est affectée à partir de la plage IP de préfixe interne du réseau « nat ». Le préfixe IP interne par défaut utilisé pour « nat » est 172.16.0.0/16.

## <a name="container-network-management-with-host-network-service"></a>Gestion de réseau de conteneur avec le service HNS

Le service de mise en réseau hôte (HNS) et le service de calcul hôte (HCS) fonctionnent ensemble pour créer des conteneurs et attacher des points de terminaison à un réseau.

### <a name="network-creation"></a>Création de réseau

- HNS crée un commutateur virtuel Hyper-V pour chaque réseau
- HNS crée des pools NAT et IP en fonction des besoins

### <a name="endpoint-creation"></a>Création du point de terminaison

- HNS crée un espace de noms réseau par point de terminaison de conteneur
- HCS de la carte réseau de l’espace de noms de réseau
- Les ports de création (vSwitch) de HNS
- HNS attribue l’adresse IP, les informations DNS, les itinéraires, etc. (sous le mode de mise en réseau) au point de terminaison.

### <a name="policy-creation"></a>Création d'une stratégie

- Réseau NAT par défaut : HNS crée des règles/mappages de réacheminement de port Winnat avec les règles d’autorisation du pare-feu Windows correspondantes
- Tous les autres réseaux : HNS utilise la plateforme de filtrage virtuel (VFP) pour la création de stratégies
    - Cela comprend : équilibrage de charge, listes de contrôle d’accès, encapsulation, etc.
    - Recherchez nos API et schémas HNS publiés [ici](/windows-server/networking/technologies/hcn/hcn-top)

![texte](media/HNS-Management-Stack.png)

## <a name="unsupported-features-and-network-options"></a>Fonctionnalités et options réseau non prises en charge

Les options de mise en réseau suivantes ne sont actuellement **pas** prises en charge sur Windows :

- Les conteneurs Windows attachés aux réseaux l2bridge, NAT et de superposition ne prennent pas en charge la communication sur la pile IPv6.
- Communication du conteneur chiffré via IPsec.
- Prise en charge du proxy HTTP pour les conteneurs.
- Mise [en réseau en mode hôte](https://docs.docker.com/ee/ucp/interlock/config/host-mode-networking/)
- Mise en réseau sur l’infrastructure Azure virtualisée via le pilote réseau transparent.

| Commande        | Option non prise en charge   |
|---------------|:--------------------:|
| ``docker run``|   ``--ip6``, ``--dns-option`` |
| ``docker network create``| ``--aux-address``, ``--internal``, ``--ip-range``, ``--ipam-driver``, ``--ipam-opt``, ``--ipv6``, ``--opt encrypted`` |