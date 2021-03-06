---
title: Problèmes connus pour les builds Insider
description: Problèmes connus pour les builds Insider.
keywords: docker, conteneurs
ms.topic: quickstart
author: cwilhit
ms.openlocfilehash: 7bb5567be29d93310e77b28e5718303bcdf39ab3
ms.sourcegitcommit: bb18e6568393da748a6d511d41c3acbe38c62668
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88161688"
---
# <a name="known-issues-for-insider-builds"></a>Problèmes connus pour les builds Insider

## <a name="build-16237"></a>Build 16237

- L’isolation Hyper-V ne fonctionne pas correctement. Cette solution de contournement permet d’utiliser l'isolation Hyper-V dans la build 16237. Exécutez les commandes suivantes dans PowerShell :

```PowerShell
Get-ComputeProcess | ? IsTemplate -eq $true | Stop-ComputeProcess -Force
Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\Containers\' -Name TemplateVmCount -Type dword -Value 0 -Force
```

- Nano Server s’exécute désormais en tant qu’utilisateur et, par conséquent, les commandes qui nécessitent des droits d’administrateur échoueront. L’inclusion d’une ligne telle que « RUN setx /M PATH » entraînera l’échec de la build. Pour ce scénario, vous pouvez utiliser cette solution :

```dockerfile
RUN setx PATH <path>
```
