---
title: Orchestrer des conteneurs avec un compte de service administré de groupe
description: Comment orchestrer des conteneurs Windows avec un compte de service administré de groupe.
keywords: docker, conteneurs, ACTIVE DIRECTORY, compte de service administré de groupe, orchestration, kubernetes, comptes de service administré de groupe
author: rpsqrd
ms.author: jgerend
ms.date: 09/10/2019
ms.topic: how-to
ms.assetid: 9e06ad3a-0783-476b-b85c-faff7234809c
ms.openlocfilehash: 64a5e5c010922bcc5e61f41c047ac965c4f31415
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990892"
---
# <a name="orchestrate-containers-with-a-gmsa"></a>Orchestrer des conteneurs avec un compte de service administré de groupe

En environnement de production, vous utiliserez souvent un orchestrateur de conteneur pour déployer et gérer vos applications et services. Chaque orchestrateur a ses propres paradigmes de gestion et est chargé d’accepter les spécifications d’informations d’identification à fournir à la plateforme de conteneurs Windows.

Quand vous orchestrez des conteneurs avec des comptes de service administré de groupe, vérifiez les points suivants :

> [!div class="checklist"]
> * Tous les hôtes de conteneur qui peuvent être planifiés pour exécuter des conteneurs avec des comptes de service administré de groupe sont joints à un domaine.
> * Les hôtes de conteneur disposent d’un accès pour récupérer les mots de passe de tous les comptes de service administré de groupe que les conteneurs utilisent.
> * Les fichiers de spécifications d’informations d’identification sont créés et chargés sur l’orchestrateur, ou copiés sur chaque hôte de conteneur, selon la façon dont l’orchestrateur préfère les traiter.
> * Les réseaux de conteneurs permettent aux conteneurs de communiquer avec les contrôleurs de domaine Active Directory pour récupérer les tickets de compte de service administré de groupe.

## <a name="how-to-use-gmsa-with-service-fabric"></a>Comment utiliser un compte de service administré de groupe avec Service Fabric

Service Fabric prend en charge l’exécution de conteneurs Windows avec un compte de service administré de groupe quand vous spécifiez l’emplacement des spécifications d’informations d’identification dans le manifeste de votre application. Vous devez créer le fichier de spécification d’informations d’identification et le placer dans le sous-répertoire **CredentialSpecs** du répertoire de données de Docker sur chaque hôte afin que Service Fabric puisse le localiser. Vous pouvez exécuter la cmdlet **CredentialSpec**, qui fait partie du [module PowerShell CredentialSpec](https://aka.ms/credspec), pour vérifier si vos spécifications d’informations d’identification se trouvent à l’emplacement approprié.

Pour plus d’informations sur la configuration de votre application, consultez [Démarrage rapide : déployer des conteneurs Windows sur Service Fabric](/azure/service-fabric/service-fabric-quickstart-containers) et [Configurer un compte de service administré de groupe pour des conteneurs Windows s’exécutant sur Service Fabric](/azure/service-fabric/service-fabric-setup-gmsa-for-windows-containers).

## <a name="how-to-use-gmsa-with-docker-swarm"></a>Comment utiliser un compte de service administré de groupe avec Docker Swarm

Pour utiliser un compte de service administré de groupe avec des conteneurs gérés par Docker Swarm, exécutez la commande [docker service create](https://docs.docker.com/engine/reference/commandline/service_create/) avec le paramètre `--credential-spec` :

```powershell
docker service create --credential-spec "file://contoso_webapp01.json" --hostname "WebApp01" <image name>
```

Pour plus d’informations sur l’utilisation des spécifications d’informations d’identification avec des services Docker, consultez [Exemple de Docker Swarm](https://docs.docker.com/engine/reference/commandline/service_create/#provide-credential-specs-for-managed-service-accounts-windows-only).

## <a name="how-to-use-gmsa-with-kubernetes"></a>Comment utiliser un compte de service administré de groupe avec Kubernetes

La prise en charge de la planification de conteneurs Windows avec des comptes de service administré de groupe dans Kubernetes est disponible en tant que fonctionnalité alpha dans Kubernetes 1.14. Pour obtenir les informations les plus récentes sur cette fonctionnalité et la manière de la tester dans votre distribution Kubernetes, consultez [Configurer un compte de service administré de groupe pour les pods et les conteneurs Windows](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa).

## <a name="next-steps"></a>Étapes suivantes

Outre l’orchestration de conteneurs, vous pouvez utiliser des comptes de service administré de groupe pour effectuer les opérations suivantes :

- [Configurer des applications](gmsa-configure-app.md)
- [Exécuter des conteneurs](gmsa-run-container.md)

Si vous rencontrez des problèmes lors de la configuration, vous trouverez peut-être des solutions dans notre [Guide de résolution des problèmes](gmsa-troubleshooting.md).