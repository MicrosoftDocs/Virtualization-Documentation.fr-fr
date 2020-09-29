---
title: Déployer des conteneurs Windows sur Windows Server
description: Déployer des conteneurs Windows sur Windows Server
keywords: docker, conteneurs
author: taylorb-microsoft
ms.author: jgerend
ms.date: 09/09/2019
ms.topic: article
ms.assetid: ba4eb594-0cdb-4148-81ac-a83b4bc337bc
ms.openlocfilehash: fc2bd93e2582d2cb1a5e77f13babe8a417bb33eb
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990952"
---
# <a name="container-host-deployment-windows-server"></a>Déploiement d’un hôte de conteneur : Windows Server

Les étapes du déploiement d’un hôte de conteneur Windows sont différentes selon le système d’exploitation et le type de système hôte (physique ou virtuel). Ce document décrit en détail le déploiement d’un hôte de conteneur Windows vers Windows Server 2016 ou Windows Server Core 2016, sur un système physique ou virtuel.

## <a name="install-docker"></a>Installer Docker

Docker est nécessaire pour utiliser les conteneurs Windows. Docker comprend le moteur Docker et le client Docker.

Pour installer Docker, nous allons utiliser le [module PowerShell de fournisseur OneGet](https://github.com/OneGet/MicrosoftDockerProvider). Le fournisseur active la fonctionnalité de conteneurs sur votre ordinateur et installe Docker. Cette opération nécessite un redémarrage.

Ouvrez une session PowerShell avec élévation de privilèges, puis exécutez les cmdlets suivantes.

Installez le module PowerShell OneGet.

```PowerShell
Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
```

Utilisez OneGet pour installer la dernière version de Docker.

```PowerShell
Install-Package -Name docker -ProviderName DockerMsftProvider
```

Une fois l’installation terminée, redémarrez l’ordinateur.

```PowerShell
Restart-Computer -Force
```

## <a name="install-a-specific-version-of-docker"></a>Installer une version spécifique de Docker

Deux canaux sont actuellement disponibles pour Docker EE pour Windows Server :

* `17.06` - Optez pour cette version si vous utilisez Docker Enterprise Edition (Docker Engine, UCP, DTR). La valeur par défaut est `17.06`.
* `18.03` - Optez pour cette version si vous exécutez Docker EE Engine seul.

Pour installer une version spécifique, utilisez l’indicateur `RequiredVersion` :

```PowerShell
Install-Package -Name docker -ProviderName DockerMsftProvider -Force -RequiredVersion 18.03
```

L’installation de versions spécifiques de Docker EE peut nécessiter une mise à jour de modules DockerMsftProvider précédemment installés. Pour mettre à jour :

```PowerShell
Update-Module DockerMsftProvider
```

## <a name="update-docker"></a>Mettre à jour Docker

Si vous devez mettre à jour Docker EE Engine à partir d’un canal antérieur vers un canal ultérieur, utilisez les indicateurs `-Update` et `-RequiredVersion` :

```PowerShell
Install-Package -Name docker -ProviderName DockerMsftProvider -Update -Force -RequiredVersion 18.03
```

## <a name="install-base-container-images"></a>Installer des images de conteneur de base

Avant d’utiliser des conteneurs Windows, une image de base doit être installée. Les images de base sont disponibles avec Windows Server Core ou Nano Server comme système d’exploitation de conteneur. Pour plus d’informations sur les images de conteneur Docker, consultez [Créer vos propres images sur docker.com](https://docs.docker.com/engine/tutorials/dockerimages/).

> [!TIP]
> Depuis mai 2018, avec l’offre d’une expérience d’acquisition cohérente et digne de confiance, presque toutes les images de conteneur provenant de Microsoft sont fournies à partir du Registre de conteneurs Microsoft, _mcr.microsoft.com_, tandis que le processus de découverte actuel via [_Docker Hub_](https://hub.docker.com/publishers/microsoftowner) est maintenu.

### <a name="windows-server-2019-and-newer"></a>Windows Server 2019 et versions ultérieures

Pour installer l’image de base « Windows Server Core », exécutez la commande suivante :

```PowerShell
docker pull mcr.microsoft.com/windows/servercore:ltsc2019
```

Pour installer l’image de base « Nano Server », exécutez la commande suivante :

```PowerShell
docker pull mcr.microsoft.com/windows/nanoserver:1809
```

### <a name="windows-server-2016-versions-1607-1803"></a>Windows Server 2016 (versions 1607 à 1803)

Exécutez la commande suivante pour installer l’image de base Windows Server Core :

```PowerShell
docker pull mcr.microsoft.com/windows/servercore:1607
```

Pour installer l’image de base Nano Server, exécutez la commande suivante :

```PowerShell
docker pull mcr.microsoft.com/windows/nanoserver:1803
```

> Veuillez lire le Contrat de licence utilisateur final applicable à l’image de système d’exploitation de conteneurs Windows disponible ici : [CLUF](../images-eula.md).

## <a name="hyper-v-isolation-host"></a>Hôte avec Isolation Hyper-V

Pour activer l’isolation Hyper-V, vous devez disposer du rôle Hyper-V. Si l’hôte de conteneur Windows est lui-même une machine virtuelle Hyper-V, la virtualisation imbriquée doit être activée avant d’installer le rôle Hyper-V. Pour plus d’informations sur la virtualisation imbriquée, voir [Virtualisation imbriquée](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization).

### <a name="nested-virtualization"></a>Virtualisation imbriquée

Le script suivant configure la virtualisation imbriquée pour l’hôte de conteneur. Ce script est exécuté sur l’ordinateur Hyper-V parent. Vérifiez que la machine virtuelle de l’hôte de conteneur est désactivée lors de l’exécution de ce script.

```PowerShell
#replace with the virtual machine name
$vm = "<virtual-machine>"

#configure virtual processor
Set-VMProcessor -VMName $vm -ExposeVirtualizationExtensions $true -Count 2

#disable dynamic memory
Set-VMMemory -VMName $vm -DynamicMemoryEnabled $false

#enable mac spoofing
Get-VMNetworkAdapter -VMName $vm | Set-VMNetworkAdapter -MacAddressSpoofing On
```

### <a name="enable-the-hyper-v-role"></a>Activer le rôle Hyper-V

Pour activer la fonctionnalité Hyper-V à l’aide de PowerShell, exécutez la cmdlet dans une session PowerShell avec élévation de privilèges.

```PowerShell
Install-WindowsFeature hyper-v
```