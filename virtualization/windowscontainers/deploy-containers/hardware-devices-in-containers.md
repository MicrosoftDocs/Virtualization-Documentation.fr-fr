---
title: Appareils dans des conteneurs sur Windows
description: Quelle prise en charge des appareils existe-t-il pour les conteneurs sur Windows
keywords: docker, conteneurs, appareils, matériel
author: cwilhit
ms.author: jgerend
ms.topic: how-to
ms.openlocfilehash: 8ada973b6e79837374c5f3d918fb4c60da031b49
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990942"
---
# <a name="devices-in-containers-on-windows"></a>Appareils dans les conteneurs sur Windows

Par défaut, les conteneurs Windows bénéficient d’un accès minimal aux appareils hôtes tels que des conteneurs Linux. Cela est bénéfique, voire impératif, pour permettre à certaines charges de travail d’accéder à des appareils hôtes et de communiquer avec ceux-ci. Ce guide traite des appareils pris en charge dans des conteneurs et de leur prise en main.

## <a name="requirements"></a>Conditions requises

Pour que cette fonctionnalité soit opérationnelle, votre environnement doit satisfaire aux exigences suivantes :
- L’hôte de conteneur doit exécuter Windows Server 2019 ou Windows 10, version 1809 ou ultérieure.
- La version de l’image de base du conteneur doit être 1809 ou une version ultérieure.
- Vos conteneurs doivent être des conteneurs Windows s’exécutant en mode isolé par processus.
- L’hôte de conteneur doit exécuter Docker Engine, version 19.03 ou plus récente.

## <a name="run-a-container-with-a-device"></a>Exécuter un conteneur avec un appareil

Pour démarrer un conteneur avec un appareil, utilisez la commande suivante :

```shell
docker run --isolation=process --device="class/{interface class GUID}" mcr.microsoft.com/windows/servercore:1809
```

Vous devez remplacer `{interface class guid}` par un [GUID de classe d’interface d’appareil approprié](/windows-hardware/drivers/install/overview-of-device-interface-classes), que vous trouverez dans la section ci-dessous.

Pour démarrer un conteneur avec plusieurs appareils, utilisez la commande suivante en enchaînant plusieurs arguments `--device` :

```shell
docker run --isolation=process --device="class/{interface class GUID}" --device="class/{interface class GUID}" mcr.microsoft.com/windows/servercore:1809
```

Dans Windows, tous les appareils déclarent une liste de classes d’interface qu’ils implémentent. En transmettant cette commande à Docker, il s’assure que tous les appareils qui s’identifient comme implémentant la classe demandée seront raccordés dans le conteneur.

Cela signifie que vous n’affectez **pas** l’appareil en dehors de l’hôte. Au lieu de cela, l’hôte le partage avec le conteneur. De même, étant donné que vous spécifiez un GUID de classe, _tous_ les appareils qui implémentent celui-ci seront partagés avec le conteneur.

## <a name="what-devices-are-supported"></a>Quels sont les appareils pris en charge ?

Les appareils pris en charge aujourd’hui (et leurs GUID de classe d’interface d’appareil) sont les suivants :

<table border="1" style="background-color:FFFFCC;border-collapse:collapse;border:1px solid FFCC00;color:000000;width:75%" cellpadding="5" cellspacing="5">
<thead>
<tr valign="top">
<th><center>Type d’appareil</center></th>
<th><center>GUID de classe d’interface</center></th>
</tr>
</thead>
<tbody>
<tr valign="top">
<td><center>GPIO</center></td>
<td><center>916EF1CB-8426-468D-A6F7-9AE8076881B3</center></td>
</tr>
<tr valign="top">
<td><center>Bus I2C</center></td>
<td><center>A11EE3C6-8421-4202-A3E7-B91FF90188E4</center></td>
</tr>
<tr valign="top">
<td><center>Port COM</center></td>
<td><center>86E0D1E0-8089-11D0-9CE4-08003E301F73</center></td>
</tr>
<tr valign="top">
<td><center>Bus SPI</center></td>
<td><center>DCDE6AF9-6610-4285-828F-CAAF78C424CC</center></td>
</tr>
<tr valign="top">
<td><center>Accélération GPU DirectX</center></td>
<td><center>Voir les documents sur l’<a href="/virtualization/windowscontainers/deploy-containers/gpu-acceleration">accélération GPU</a></center></td>
</tr>
</tbody>
</table>

> [!IMPORTANT]
> La prise en charge des appareils dépend du pilote. Toute tentative de transmettre des GUID de classe non définis dans le tableau ci-dessus peut entraîner un comportement imprévisible.

## <a name="hyper-v-isolated-windows-container-support"></a>Prise en charge de conteneur Windows isolé par Hyper-V

L’attribution et le partage d’appareil pour les charges de travail dans des conteneurs Windows isolés par Hyper-V ne sont pas prises en charge actuellement.

## <a name="hyper-v-isolated-linux-container-support"></a>Prise en charge de conteneur Linux isolé par Hyper-V

L’attribution et le partage d’appareil pour les charges de travail dans des conteneurs Linux isolés par Hyper-V ne sont pas prises en charge actuellement.