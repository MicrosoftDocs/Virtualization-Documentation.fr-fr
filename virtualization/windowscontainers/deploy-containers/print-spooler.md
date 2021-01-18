---
title: Spouleur d’impression dans des conteneurs Windows
description: Explique le comportement de travail actuel du service de spouleur d’impression dans des conteneurs Windows.
keywords: docker, conteneurs, imprimante, spouleur
author: cwilhit
ms.author: cwilhit
ms.date: 10/22/2019
ms.topic: tutorial
ms.openlocfilehash: df51140f3e44bf24c5481d23a3437796fe4c3641
ms.sourcegitcommit: 24a7d693da95512ac371bdbf6466f46e187c9c58
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98181836"
---
# <a name="print-spooler-in-windows-containers"></a>Spouleur d’impression dans des conteneurs Windows

Les applications dépendant de services d’impression peuvent être correctement mises en conteneur avec des conteneurs Windows. Des exigences particulières doivent être satisfaites pour activer correctement la fonctionnalité du service d’imprimante. Ce guide explique comment configurer correctement votre déploiement.

> [!IMPORTANT]
> Si l’accès aux services d’impression fonctionne correctement dans les conteneurs, la fonctionnalité du formulaire est limitée. Il se peut que certaines actions liées à l’impression ne fonctionnent pas. Par exemple, des applications dépendant de l’installation de pilotes d’imprimante sur l’hôte ne peuvent pas être mises en conteneur parce que l’**installation de pilote à partir d’un conteneur n’est pas prise en charge**. Si vous trouvez une fonctionnalité d’impression non prise en charge dont vous souhaitez qu’elle soit prise en charge dans les conteneurs, veuillez ouvrir un commentaire ci-dessous.

## <a name="setup"></a>Installation

* L’hôte doit exécuter la version de Windows Server 2019 ou de Windows 10 Professionnel/Entreprise mise à jour en octobre 2018 ou une version ultérieure.
* L’image [mcr.microsoft.com/windows](https://hub.docker.com/_/microsoft-windowsfamily-windows) doit être l’image de base ciblée. Les autres images de base du conteneur Windows (telles que Nano Server et Windows Server Core) ne contiennent pas le rôle serveur d’impression.

### <a name="hyper-v-isolation"></a>Isolation Hyper-V

Nous vous recommandons d’exécuter votre conteneur avec une isolation Hyper-V. Quand il est exécuté dans ce mode, vous pouvez avoir autant de conteneurs que vous le souhaitez, qui s’exécutent avec un accès aux services d’impression. Vous n’avez pas besoin de modifier le service de spouleur sur l’hôte.

Vous pouvez vérifier la fonctionnalité avec la requête PowerShell suivante :

```PowerShell
PS C:\Users\Administrator> docker run -it --isolation hyperv mcr.microsoft.com/windows:1809 powershell.exe
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\> Get-Service spooler

Status   Name               DisplayName
------   ----               -----------
Running  spooler            Print Spooler


PS C:\> Get-Printer

Name                           ComputerName    Type         DriverName                PortName        Shared   Published
----                           ------------    ----         ----------                --------        ------   --------
Microsoft XPS Document Writer                  Local        Microsoft XPS Document... PORTPROMPT:     False    False
Microsoft Print to PDF                         Local        Microsoft Print To PDF    PORTPROMPT:     False    False
Fax                                            Local        Microsoft Shared Fax D... SHRFAX:         False    False


PS C:\>
```

### <a name="process-isolation"></a>Isolation des processus

Du fait que, par nature, les conteneurs isolés par processus partagent un noyau, le comportement actuel limite l’utilisateur à l’exécution d’**une seule instance** du service de spouleur d’imprimante sur l’hôte et tous ses conteneurs enfants. Si le spouleur d’imprimante de l’hôte est en cours d’exécution, vous devez arrêter le service sur l’hôte avant de tenter de lancer le service d’imprimante sur l’invité.

> [!TIP]
> Si vous lancez un conteneur et une requête pour le service de spouleur simultanément sur le conteneur et l’hôte, tous deux signalent leur état comme étant « en cours d’exécution ». Mais ne vous y trompez pas : le conteneur ne pourra pas lancer de requête pour obtenir la liste des imprimantes disponibles. Le service de spouleur de l’hôte ne doit pas s’exécuter.

Pour vérifier si l’hôte exécute le service d’imprimante, utilisez dans PowerShell la requête ci-dessous :

```PowerShell
PS C:\Users\Administrator> Get-Service spooler

Status   Name               DisplayName
------   ----               -----------
Running  spooler            Print Spooler

PS C:\Users\Administrator>
```

Pour arrêter le service de spooler sur l’hôte, utilisez dans PowerShell les commandes suivantes :

```PowerShell
Stop-Service spooler
Set-Service spooler -StartupType Disabled
```

Lancez le conteneur et vérifiez l’accès aux imprimantes.

```PowerShell
PS C:\Users\Administrator> docker run -it --isolation process mcr.microsoft.com/windows:1809 powershell.exe
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.


PS C:\> Get-Service spooler

Status   Name               DisplayName
------   ----               -----------
Running  spooler            Print Spooler


PS C:\> Get-Printer

Name                           ComputerName    Type         DriverName                PortName        Shared   Published
----                           ------------    ----         ----------                --------        ------   --------
Microsoft XPS Document Writer                  Local        Microsoft XPS Document... PORTPROMPT:     False    False
Microsoft Print to PDF                         Local        Microsoft Print To PDF    PORTPROMPT:     False    False
Fax                                            Local        Microsoft Shared Fax D... SHRFAX:         False    False


PS C:\>
```