---
title: Conteneurs ou machines virtuelles
description: 'Cette rubrique présente quelques-unes des similitudes et différences clés entre les conteneurs, les machines virtuelles et leur utilisation. Les conteneurs et les machines virtuelles ont chacun leur utilisation propre : en fait, beaucoup de déploiements de conteneurs utilisent les machines virtuelles comme système d’exploitation hôte au lieu de s’exécuter directement sur le matériel, surtout lors de l’exécution de conteneurs dans le cloud.'
keywords: docker, conteneurs, machines virtuelles
author: jasongerend
ms.author: jgerend
ms.date: 10/21/2019
ms.topic: overview
ms.openlocfilehash: 04f5e73b590585d99662b9472a1762ecb9443de9
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990932"
---
# <a name="containers-vs-virtual-machines"></a>Conteneurs ou machines virtuelles

Cette rubrique présente quelques-unes des similitudes et différences clés entre les conteneurs, les machines virtuelles et leur utilisation. Les conteneurs et les machines virtuelles ont chacun leur utilisation propre : en fait, beaucoup de déploiements de conteneurs utilisent les machines virtuelles comme système d’exploitation hôte au lieu de s’exécuter directement sur le matériel, notamment lors de l’exécution de conteneurs dans le cloud.

Pour une vue d’ensemble des conteneurs, consultez [Windows et conteneurs](index.md).

## <a name="container-architecture"></a>Architecture de conteneur

Un conteneur est un silo léger et isolé qui permet l’exécution d’une application sur le système d’exploitation hôte. Les conteneurs s’appuient sur le noyau du système d’exploitation hôte (qui peut être considéré comme la plomberie enfouie du système d’exploitation) et contiennent uniquement des applications ainsi que quelques API de système d'exploitation légères et services s'exécutant en mode utilisateur, tel qu’illustré dans ce schéma.

![Diagramme d’architecture montrant comment les conteneurs s’exécutent au-dessus du noyau](media/container-diagram.svg)

## <a name="virtual-machine-architecture"></a>Architecture d'ordinateurs virtuels

Contrairement aux conteneurs, les machines virtuelles exécutent un système d’exploitation complet, y compris son propre noyau, comme illustré dans ce schéma.

![Diagramme d’architecture montrant comment les machines virtuelles exécutent un système d’exploitation complet à côté du système d’exploitation hôte](media/virtual-machine-diagram.svg)

## <a name="containers-vs-virtual-machines"></a>Conteneurs ou machines virtuelles

Le tableau suivant présente plusieurs similitudes et différences entre ces technologies complémentaires.

|Fonctionnalité    | Machine virtuelle  | Conteneur  |
| --------------  | ---------------- | ---------- |
| Isolation       | Fournit une isolation complète du système d’exploitation hôte et des autres machines virtuelles. Cela s'avère utile lorsqu'une limite de sécurité forte est essentielle (hébergement d'applications provenant d'entreprises concurrentes sur le même serveur ou cluster, par exemple). | Fournit généralement une isolation légère de l’hôte et d’autres conteneurs, mais moins de limite de sécurité qu’une machine virtuelle. (Vous pouvez renforcer la sécurité en utilisant [Mode d’isolation Hyper-V](../manage-containers/hyperv-container.md) pour isoler chaque conteneur dans une machine virtuelle légère). |
| Système d’exploitation | Exécute un système d’exploitation complet incluant le noyau, ce qui requiert davantage de ressources système (processeur, mémoire et stockage). | Exécute la partie mode utilisateur d’un système d’exploitation et peut être personnalisé pour contenir uniquement les services nécessaires à votre application, en utilisant moins de ressources système. |
| Compatibilité des invités | Exécute n’importe quel système d’exploitation à l’intérieur de la machine virtuelle | S’exécute sur la [même version du système d’exploitation que l'hôte](../deploy-containers/version-compatibility.md) (l’isolation Hyper-V vous permet d’exécuter des versions antérieures du même système d’exploitation dans un environnement de machine virtuelle léger)
| Déploiement     | Déployez des machines virtuelles individuelles à l’aide de Windows Admin Center ou du Gestionnaire Hyper-V ; déployez plusieurs machines virtuelles à l’aide de PowerShell ou de System Center Virtual Machine Manager. | Déployez des conteneurs individuels à l’aide de Docker via une ligne de commande ; déployez plusieurs conteneurs à l’aide d’un orchestrateur tel qu'Azure Kubernetes Service. |
| Mises à jour et mises à niveau du système d'exploitation | Téléchargez et installez les mises à jour du système d’exploitation sur chaque machine virtuelle. L’installation d’une nouvelle version du système d’exploitation requiert la mise à niveau ou la création d’une machine virtuelle entièrement nouvelle. Cela peut être chronophage, notamment en présence d'un grand nombre de machines virtuelles. | La procédure de mise à jour ou de mise à niveau des fichiers du système d’exploitation au sein d’un conteneur est la même : <br><ol><li>Modifiez le fichier de build de votre image de conteneur (connu sous le nom de fichier Dockerfile) pour pointer vers la dernière version de l’image de base Windows. </li><li>Régénérez votre image de conteneur avec cette nouvelle image de base.</li><li>Transmettez l’image de conteneur à votre registre de conteneurs.</li> <li>Redéployez à l’aide d’un orchestrateur.<br>L’orchestrateur offre une automatisation puissante pour le faire à grande échelle. Pour plus d’informations, consultez [Tutoriel : Mettre à jour une application dans Azure Kubernetes Service](/azure/aks/tutorial-kubernetes-app-update).</li></ol> |
| Stockage persistant | Utilisez un disque dur virtuel (VHD) à des fins de stockage local pour une machine virtuelle unique, ou un partage de fichiers SMB à des fins de stockage partagé par plusieurs serveurs | Utilisez des disques Azure à des fins de stockage local pour un seul nœud, ou Azure Files (partages SMB) à des fins de stockage partagé par plusieurs nœuds ou serveurs. |
| Équilibrage de la charge | L’équilibrage de charge des machines virtuelles déplace les machines virtuelles en cours d’exécution vers d’autres serveurs dans un cluster de basculement. | Les conteneurs à proprement parler ne sont pas déplacés, mais un orchestrateur peut démarrer ou arrêter automatiquement des conteneurs sur des nœuds de cluster pour gérer les modifications de charge et de disponibilité. |
| Tolérance de panne | Les machines virtuelles peuvent basculer vers un autre serveur dans un cluster, avec redémarrage du système d’exploitation de la machine virtuelle sur le nouveau serveur.  | En cas de défaillance d’un nœud de cluster, tous les conteneurs en cours d’exécution sont rapidement recréés par l’orchestrateur sur un autre nœud de cluster. |
| Mise en réseau     | Utilise des cartes réseau virtuelles. | Utilise une vue isolée de carte réseau virtuelle, ce qui fournit un peu moins de virtualisation. Le pare-feu de l’ordinateur hôte est partagé avec des conteneurs, ce qui sollicite moins de ressources. Pour plus d'informations, consultez [Mise en réseau de conteneur Windows](../container-networking/architecture.md). |