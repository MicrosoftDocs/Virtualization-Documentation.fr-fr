---
title: Préparer les conteneurs du système d’exploitation Windows
description: Configurez Windows 10 ou Windows Server pour les conteneurs, puis passez à l'exécution de votre première image de conteneur.
keywords: docker, conteneurs, LCOW
author: cwilhit
ms.author: crwilhit
ms.date: 11/12/2019
ms.topic: quickstart
ms.assetid: bb9bfbe0-5bdc-4984-912f-9c93ea67105f
ms.openlocfilehash: 65860acf545f51a8ba86afcef1872191eda86624
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990602"
---
# <a name="get-started-prep-windows-for-containers"></a>Prise en main : Préparer Windows pour les conteneurs

Ce tutoriel explique comment :

- Configurer Windows 10 ou Windows Server pour les conteneurs
- Exécuter votre première image de conteneur
- Conteneuriser une application .NET Core simple

## <a name="prerequisites"></a>Prérequis

<!-- start tab view -->
# <a name="windows-server"></a>[Windows Server](#tab/Windows-Server)

Pour exécuter des conteneurs sous Windows Server, vous devez disposer d'un serveur physique ou d'une machine virtuelle qui exécute Windows Server (canal semi-annuel), Windows Server 2019 ou Windows Server 2016.

À des fins de tests, vous pouvez télécharger une copie de la [version d'évaluation de Windows Server 2019](https://www.microsoft.com/evalcenter/evaluate-windows-server-2019 ) ou de [Windows Server Insider Preview](https://insider.windows.com/for-business-getting-started-server/).

# <a name="windows-10"></a>[Windows 10](#tab/Windows-10-Client)

Pour exécuter des conteneurs sous Windows 10, vous devez disposer de ce qui suit :

- Un ordinateur physique exécutant Windows 10 Professionnel ou Entreprise et disposant de la Mise à jour anniversaire (version 1607) ou d'une version ultérieure.
- [Hyper-V](/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) doit être activé.

> [!NOTE]
>  Depuis la mise à jour d'octobre 2018 de Windows 10, nous n'interdisons plus aux utilisateurs d'exécuter un conteneur Windows en mode d'isolation des processus sous Windows 10 Entreprise ou Professionnel à des fins de développement/test. Pour en savoir plus, consultez le [Forum Aux Questions (FAQ)](../about/faq.md).
>
> Les conteneurs Windows Server utilisent l'isolation Hyper-V par défaut de Windows 10 afin de fournir aux développeurs une version et une configuration du noyau identiques à celles qui seront utilisées en production. Pour en savoir plus sur l'isolation Hyper-V, consultez la section [Concepts](../manage-containers/hyperv-container.md) de notre documentation.

---
<!-- stop tab view -->

## <a name="install-docker"></a>Installer Docker

La première étape consiste à installer Docker, qui est nécessaire pour utiliser des conteneurs Windows. Docker fournit un environnement d'exécution standard pour les conteneurs, avec une API et une interface de ligne de commande (CLI) communes.

Pour plus d'informations sur la configuration, consultez [Moteur Docker sous Windows](../manage-docker/configure-docker-daemon.md).

<!-- start tab view -->
# <a name="windows-server"></a>[Windows Server](#tab/Windows-Server)

Pour installer Docker sous Windows Server, vous pouvez utiliser un [module PowerShell OneGet](https://github.com/oneget/oneget) que Microsoft a publié sous le nom de [DockerMicrosoftProvider](https://github.com/OneGet/MicrosoftDockerProvider). Ce fournisseur active la fonctionnalité Conteneurs dans Windows et installe le moteur et le client Docker. Voici comment procéder :

1. Ouvrez une session PowerShell avec élévation de privilèges et installez le fournisseur Docker-Microsoft PackageManagement à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/DockerMsftProvider).

   ```powershell
   Install-Module -Name DockerMsftProvider -Repository PSGallery -Force
   ```

   Si vous êtes invité à installer le fournisseur NuGet, entrez `Y` pour l'installer également.

2. Utilisez le module PowerShell PackageManagement pour installer la dernière version de Docker.

   ```powershell
   Install-Package -Name docker -ProviderName DockerMsftProvider
   ```

   Quand PowerShell vous demande si la source du package « DockerDefault » doit être approuvée, tapez `A` pour poursuivre l’installation.
3. Au terme de l'installation, redémarrez l'ordinateur.

   ```powershell
   Restart-Computer -Force
   ```

Si vous souhaitez mettre à jour Docker ultérieurement :

- Vérifiez la version installée avec :
```powershell
Get-Package -Name Docker -ProviderName DockerMsftProvider
```
- Recherchez la version actuelle avec :
```powershell
Find-Package -Name Docker -ProviderName DockerMsftProvider
```
- Lorsque vous êtes prêt, procédez à la mise à niveau avec :
```powershell
Install-Package -Name Docker -ProviderName DockerMsftProvider -Update -Force
```
, suivi de 
```powershell
Start-Service Docker
```

# <a name="windows-10"></a>[Windows 10](#tab/Windows-10-Client)

Pour installer Docker sur les éditions Windows 10 Professionnel et Entreprise, procédez comme suit.

1. Téléchargez et installez [Docker Desktop](https://store.docker.com/editions/community/docker-ce-desktop-windows), en créant un compte Docker gratuit si vous n'en avez pas encore. Pour plus d'informations, consultez la [documentation Docker](https://docs.docker.com/docker-for-windows/install).

2. Pendant l'installation, définissez le type de conteneur par défaut sur Conteneurs Windows. Pour procéder au basculement après l'installation, vous pouvez utiliser l'élément Docker accessible via la barre d'état système de Windows (comme illustré ci-dessous), ou la commande suivante dans une invite PowerShell :

   ```console
   & $Env:ProgramFiles\Docker\Docker\DockerCli.exe -SwitchDaemon .
   ```

![Menu de la barre d'état système de Docker affichant la commande « Basculer vers les conteneurs Windows ».](./media/docker-for-win-switch.png)

---
<!-- stop tab view -->

## <a name="next-steps"></a>Étapes suivantes

Maintenant que votre environnement a été correctement configuré, suivez le lien pour savoir comment exécuter un conteneur.

> [!div class="nextstepaction"]
> [Exécuter votre premier conteneur](./run-your-first-container.md)