---
title: Compatibilité des versions avec les conteneurs Windows
description: Comment Windows peut générer et exécuter des conteneurs dans plusieurs versions
keywords: métadonnées, conteneurs, version
author: taylorb-microsoft
ms.author: jgerend
ms.topic: conceptual
ms.openlocfilehash: 05b63a12554b5ba8c7dd665def5166116a1ee0db
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990722"
---
# <a name="windows-container-version-compatibility"></a>Compatibilité des versions avec les conteneurs Windows

Windows Server 2016 et la mise à jour anniversaire Windows 10 (tous deux de version 14393) ont été les premières versions de Windows capables de générer et d’exécuter des conteneurs Windows Server. Les conteneurs créés à l’aide de ces versions peuvent s’exécuter sur des versions plus récentes, mais vous devez être au fait de certains éléments avant de commencer.

Étant donné que nous avons amélioré les fonctionnalités liées aux conteneurs Windows, nous avons dû apporter des modifications qui affectent la compatibilité. Les conteneurs plus anciens s’exécuteront de la même manière sur les ordinateurs hôtes plus récents avec l’[isolation Hyper-V](../manage-containers/hyperv-container.md), et utiliserons la même (ancienne) version du noyau. Toutefois, si vous souhaitez exécuter un conteneur basé sur une version plus récente de Windows, celui-ci ne pourra s’exécuter que sur la build plus récente de l’hôte.

## <a name="windows-server-host-os-compatibility"></a>Compatibilité du système d’exploitation hôte Windows Server

<!-- start tab view -->
# <a name="windows-server-version-2004"></a>[Windows Server, version 2004](#tab/windows-server-2004)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10004;|&#10004;|
|Windows Server, version 1909|&#10004;|&#10060;|
|Windows Server, version 1903|&#10004;|&#10060;|
|Windows Server 2019|&#10004;|&#10060;|
|Windows Server 2016|&#10004;|&#10060;|

# <a name="windows-server-version-1909"></a>[Windows Server, version 1909](#tab/windows-server-1909)

|Version de système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10060;|&#10060;|
|Windows Server, version 1909|&#10004;|&#10004;|
|Windows Server, version 1903|&#10004;|&#10060;|
|Windows Server 2019|&#10004;|&#10060;|
|Windows Server 2016|&#10004;|&#10060;|

# <a name="windows-server-version-1903"></a>[Windows Server, version 1903](#tab/windows-server-1903)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10060;|&#10060;|
|Windows Server, version 1909|&#10060;|&#10060;|
|Windows Server, version 1903|&#10004;|&#10004;|
|Windows Server 2019|&#10004;|&#10060;|
|Windows Server 2016|&#10004;|&#10060;|

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows-server-2019)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10060;|&#10060;|
|Windows Server, version 1909|&#10060;|&#10060;|
|Windows Server, version 1903|&#10060;|&#10060;|
|Windows Server 2019|&#10004;|&#10004;|
|Windows Server 2016|&#10004;|&#10060;|

# <a name="windows-server-2016"></a>[Windows Server 2016](#tab/windows-server-2016)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10060;|&#10060;|
|Windows Server, version 1909|&#10060;|&#10060;|
|Windows Server, version 1903|&#10060;|&#10060;|
|Windows Server 2019|&#10060;|&#10060;|
|Windows Server 2016|&#10004;|&#10004;|

---
<!-- stop tab view -->

## <a name="windows-10-host-os-compatibility"></a>Compatibilité du système d’exploitation hôte Windows 10

<!-- start tab view -->

# <a name="windows-10-version-2004"></a>[Windows 10, version 2004](#tab/windows-10-2004)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10004;|&#10004;|
|Windows Server, version 1909|&#10004;|&#10060;|
|Windows Server, version 1903|&#10004;|&#10060;|
|Windows Server 2019|&#10004;|&#10060;|
|Windows Server 2016|&#10004;|&#10060;|

# <a name="windows-10-version-1909"></a>[Windows 10, version 1909](#tab/windows-10-1909)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10060;|&#10060;|
|Windows Server, version 1909|&#10004;|&#10060;|
|Windows Server, version 1903|&#10004;|&#10060;|
|Windows Server 2019|&#10004;|&#10060;|
|Windows Server 2016|&#10004;|&#10060;|

# <a name="windows-10-version-1903"></a>[Windows 10, version 1903](#tab/windows-10-1903)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10060;|&#10060;|
|Windows Server, version 1909|&#10060;|&#10060;|
|Windows Server, version 1903|&#10004;|&#10060;|
|Windows Server 2019|&#10004;|&#10060;|
|Windows Server 2016|&#10004;|&#10060;|

# <a name="windows-10-version-1809"></a>[Windows 10, version 1809](#tab/windows-10-1809)

|Version du système d’exploitation de l’image de base du conteneur|Prend en charge l’isolation Hyper-V|Prend en charge l’isolation des processus|
|---|:---:|:---:|
|Windows Server, version 2004|&#10060;|&#10060;|
|Windows Server, version 1909|&#10060;|&#10060;|
|Windows Server, version 1903|&#10060;|&#10060;|
|Windows Server 2019|&#10004;|&#10060;|
|Windows Server 2016|&#10004;|&#10060;|

---
<!-- stop tab view -->

## <a name="matching-container-host-version-with-container-image-versions"></a>Correspondance de la version d’hôte de conteneur avec les versions des images de conteneur

### <a name="windows-server-containers"></a>Conteneurs Windows Server

Étant donné que les conteneurs Windows Server et l’hôte sous-jacent partagent un noyau unique, la version du système d’exploitation de l’image de base du conteneur doit correspondre à celle de l’hôte. Si les versions sont différentes, le conteneur peut démarrer, mais sa fonctionnalité complète ne peut pas être garantie. Le système d’exploitation Windows utilise quatre niveaux de gestion de version : majeure, mineure, build et révision. Par exemple, la version 10.0.14393.103 comprend la version majeure 10, la version mineure 0, la build 14393 et la révision 103. Le numéro de version change uniquement lorsque de nouvelles versions du système d’exploitation sont publiées, telles que les versions 1709, 1903, etc. Le numéro de révision est mis à jour quand des mises à jour Windows sont appliquées.

#### <a name="build-number-new-release-of-windows"></a>Numéro de build (nouvelle mise en production de Windows)

Le démarrage des conteneurs Windows Server est bloqué quand le numéro de build de l’hôte du conteneur diffère de celui de l’image du conteneur. Par exemple, lorsque la version de l’hôte du conteneur est 10.0.14393.* (Windows Server 2016) et celle de l’image du conteneur est 10.0.16299.* (Windows Server, version 1709), le conteneur ne démarre pas.

#### <a name="revision-number-patching"></a>Numéro de révision (mise à jour corrective)

Actuellement, les conteneurs Windows Server ne prennent pas en charge les scénarios où des conteneurs basés sur Windows Server 2016 s’exécutent dans un système où les numéros de révision de l’hôte du conteneur et de l’image du conteneur diffèrent. Par exemple, si la version de l’hôte du conteneur est 10.0.14393.**1914** (Windows Server 2016 avec la mise à jour KB4051033) et celle de l’image du conteneur est 10.0.14393.**1944** (Windows Server 2016 avec la mise à jour KB4053579), l’image risque de ne pas démarrer.

Toutefois, pour les hôtes ou les images utilisant Windows Server, version 1809 ou ultérieure, cette règle ne s’applique pas, et les révisions de l’hôte et de l’image du conteneur ne doivent pas nécessairement correspondre.

Pour votre sécurité, nous vous recommandons de tenir à jour vos systèmes (hôte et conteneur) avec les correctifs et mises à jour les plus récents.

>[!NOTE]
>Vous risquez de rencontrer des problèmes lors de l’utilisation de conteneurs Windows Server avec la version de mise à jour de sécurité du 11 février 2020 (également appelée « 2B ») ou des mises à jour de sécurité mensuelles ultérieures. Pour plus de détails, consultez [cet article](https://support.microsoft.com/help/4542617/you-might-encounter-issues-when-using-windows-server-containers-with-t).
>
>Nous vous recommandons vivement de mettre à jour votre hôte et vos conteneurs avec les correctifs et mises à jour les plus récents pour rester sécurisé et compatible. Pour obtenir des instructions importantes sur la mise à jour des conteneurs Windows, consultez [Mettre à jour les conteneurs Windows Server](update-containers.md).

#### <a name="practical-application"></a>Application pratique

Exemple 1 :  l’hôte du conteneur exécute Windows Server 2016 avec la mise à jour KB4041691. Tout conteneur Windows Server déployé sur cet hôte doit être basé sur la version 10.0.14393.1770 des images de base du conteneur. Si vous appliquez la mise à jour KB4053579 au conteneur hôte, vous devez également mettre à jour les images pour vous assurer que le conteneur hôte les prend en charge.

Exemple 2 : l’hôte du conteneur exécute Windows Server, version 1809 avec la mise à jour KB4534273. Tout conteneur Windows Server déployé sur cet hôte doit être basé sur une image de base de conteneur de Windows Server, version 1809 (10.0.17763), mais sa mise à jour KB ne doit pas nécessairement correspondre à celle de l’hôte. Si la mise à jour KB4534273 est appliquée à l’hôte, les images de conteneur sont toujours prises en charge, mais nous vous recommandons de les mettre à jour pour prévenir tout problème de sécurité potentiel.

#### <a name="querying-version"></a>Interrogation de la version

Méthode 1 : introduite dans la version 1709, l’invite de commandes et la commande **ver** retournent désormais les informations de révision.

```batch
Microsoft Windows [Version 10.0.16299.125]
(c) 2017 Microsoft Corporation. All rights reserved.

C:\>ver

Microsoft Windows [Version 10.0.16299.125]
```

Méthode 2 : interrogez la clé de Registre suivante : HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion

Par exemple :

```batch
C:\>reg query "HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion" /v BuildLabEx
```

```powershell
Windows PowerShell
Copyright (C) 2016 Microsoft Corporation. All rights reserved.

PS C:\Users\Administrator> (Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\').BuildLabEx
14393.321.amd64fre.rs1_release_inmarket.161004-2338
```

Pour vérifier la version que votre image de base utilise, examinez les balises sur le Docker Hub ou la table de hachage d’image fournie dans la description de l’image. La page [Historique des mises à jour de Windows 10](https://support.microsoft.com/help/12387/windows-10-update-history) répertorie la date de publication de chaque build et révision.

### <a name="hyper-v-isolation-for-containers"></a>Isolation Hyper-V pour les conteneurs

Vous pouvez exécuter les conteneurs Windows avec ou sans isolation Hyper-V. L’isolation Hyper-V crée une limite sécurisée autour du conteneur avec un ordinateur virtuel optimisé. Contrairement aux conteneurs Windows standard qui partagent le noyau avec l’hôte, chaque conteneur isolé Hyper-V dispose de sa propre instance du noyau Windows. Cela signifie que vous pouvez avoir des versions du système d’exploitation différentes dans l’hôte et dans l’image du conteneur (pour plus d’informations, consultez la matrice de compatibilité suivante).

Pour exécuter un conteneur ayant une isolation Hyper-V, ajoutez simplement la balise `--isolation=hyperv` à votre commande docker run.

## <a name="errors-from-mismatched-versions"></a>Erreurs en cas d’incompatibilité des versions

Si vous tentez d’exécuter une combinaison non prise en charge, vous obtiendrez l’erreur suivante :

```dockerfile
docker: Error response from daemon: container b81ed896222eb87906ccab1c3dd2fc49324eafa798438f7979b87b210906f839 encountered an error during CreateContainer: failure in a Windows system call: The operating system of the container does not match the operating system of the host. (0xc0370101) extra info: {"SystemType":"Container","Name":"b81ed896222eb87906ccab1c3dd2fc49324eafa798438f7979b87b210906f839","Owner":"docker","IsDummy":false,"VolumePath":"\\\\?\\Volume{2443d38a-1379-4bcf-a4b7-fc6ad4cd7b65}","IgnoreFlushesDuringBoot":true,"LayerFolderPath":"C:\\ProgramData\\docker\\windowsfilter\\b81ed896222eb87906ccab1c3dd2fc49324eafa798438f7979b87b210906f839","Layers":[{"ID":"1532b584-8431-5b5a-8735-5e1b4fe9c2a9","Path":"C:\\ProgramData\\docker\\windowsfilter\\b2b88bc2a47abcc682e422507abbba9c9b6d826d34e67b9e4e3144cc125a1f80"},{"ID":"a64b8da5-cd6e-5540-bc73-d81acae6da54","Path":"C:\\ProgramData\\docker\\windowsfilter\\5caaedbced1f546bccd01c9d31ea6eea4d30701ebba7b95ee8faa8c098a6845a"}],"HostName":"b81ed896222e","MappedDirectories":[],"HvPartition":false,"EndpointList":["002a0d9e-13b7-42c0-89b2-c1e80d9af243"],"Servicing":false,"AllowUnqualifiedDNSQuery":true}.
```

Vous pouvez résoudre cette erreur de trois façons :

- Reconstruire le conteneur en vous basant sur la version correcte de `mcr.microsoft.com/windows/nanoserver` ou de `mcr.microsoft.com/windows/servercore`.
- Si l’hôte est plus récent, exécuter la commande **docker run --isolation=hyperv ...** .
- Tenter d’exécuter le conteneur sur un autre hôte avec la même version de Windows.

## <a name="choose-which-container-os-version-to-use"></a>Choisir la version du système d’exploitation du conteneur à utiliser

>[!NOTE]
>Depuis le 16 avril 2019, la balise « latest » n’est plus publiée ou gérée pour les [images de conteneur du système d’exploitation de base Windows](https://hub.docker.com/_/microsoft-windows-base-os-images). Déclarez une balise spécifique lors de l’extraction ou du référencement d’images de ces référentiels.

Vous devez connaître la version à utiliser pour votre conteneur. Par exemple, si vous voulez que le système d’exploitation de votre conteneur soit Windows Server, version 1809, et souhaitez obtenir les derniers correctifs applicables à cette version, vous devez utiliser la balise `1809` lorsque vous spécifiez la version des images du conteneur du système d’exploitation de base de votre choix comme suit :

```dockerfile
FROM mcr.microsoft.com/windows/nanoserver:1809
...
```

Toutefois, si vous souhaitez appliquer un correctif spécifique de Windows Server, version 1809, vous pouvez spécifier le numéro de mise à jour KB dans la balise. Par exemple, pour obtenir l’image du conteneur du système d’exploitation de base Nano Server de Windows Server, version 1809 avec la mise à jour KB4493509, vous devez le spécifier comme suit :

```dockerfile
FROM mcr.microsoft.com/windows/nanoserver:1809-KB4493509
...
```

Vous pouvez également spécifier les correctifs exacts dont vous avez besoin avec le schéma utilisé précédemment en spécifiant la version du système d’exploitation dans la balise :

```dockerfile
FROM mcr.microsoft.com/windows/nanoserver:10.0.17763.437
...
```

Les images de base de Server Core basées sur Windows Server 2019 et Windows Server 2016 sont des mises en production du [Canal de maintenance à long terme](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc). Par exemple, si vous souhaitez que Windows Server 2019 soit le système d’exploitation du conteneur de votre image de Server Core et obtenir des derniers correctifs ad hoc, vous pouvez spécifier les mises en production du Canal de maintenance à long terme comme suit :

```dockerfile
FROM mcr.microsoft.com/windows/servercore:ltsc2019
...
```

## <a name="matching-versions-using-docker-swarm"></a>Correspondance des versions à l’aide de Docker Swarm

Docker Swarm n’inclut pas actuellement de méthode intégrée pour faire correspondre la version de Windows qu’un conteneur utilise à un hôte de la même version. Si vous mettez à jour le service pour utiliser un conteneur plus récent, celui-ci s’exécute correctement.

Si vous devez exécuter plusieurs versions de Windows pendant une longue période de temps, vous avez le choix entre deux approches : configurer les hôtes Windows pour qu’ils utilisent toujours l’isolation Hyper-V, ou utiliser des contraintes d’étiquette.

### <a name="finding-a-service-that-wont-start"></a>Recherche d’un service qui ne démarre pas

Si un service ne démarre pas, vous verrez que le `MODE` est `replicated`, mais que le nombre `REPLICAS` sera bloqué sur 0. Pour voir si le problème résulte de la version du système d’exploitation, utilisez les commandes suivantes :

Exécutez la commande **docker service ls** pour trouver le nom du service :

```dockerfile
ID                  NAME                MODE                REPLICAS            IMAGE                                             PORTS
xh6mwbdq2uil        angry_liskov        replicated          0/1                 microsoft/iis:windowsservercore-10.0.14393.1715
```

Exécutez la commande **docker service ps (nom du service**) pour connaître l’état et les dernières tentatives :

```dockerfile
C:\Program Files\Docker>docker service ps angry_liskov
ID                  NAME                 IMAGE                                             NODE                DESIRED STATE       CURRENT STATE               ERROR                              PORTS
klkbhn742lv0        angry_liskov.1       microsoft/iis:windowsservercore-10.0.14393.1715   WIN-BSTMQDRQC2E     Ready               Ready 3 seconds ago
y5blbdum70zo         \_ angry_liskov.1   microsoft/iis:windowsservercore-10.0.14393.1715   WIN-BSTMQDRQC2E     Shutdown            Failed 24 seconds ago       "starting container failed: co…"
yjq6zwzqj8kt         \_ angry_liskov.1   microsoft/iis:windowsservercore-10.0.14393.1715   WIN-BSTMQDRQC2E     Shutdown            Failed 31 seconds ago       "starting container failed: co…"

ytnnv80p03xx         \_ angry_liskov.1   microsoft/iis:windowsservercore-10.0.14393.1715   WIN-BSTMQDRQC2E     Shutdown            Failed about a minute ago   "starting container failed: co…"
xeqkxbsao57w         \_ angry_liskov.1   microsoft/iis:windowsservercore-10.0.14393.1715   WIN-BSTMQDRQC2E     Shutdown            Failed about a minute ago   "starting container failed: co…"
```

Si vous voyez le message `starting container failed: ...`, vous pouvez voir l’erreur complète avec la commande **docker service ps --no-trunc (nom du conteneur)**  :

```dockerfile
C:\Program Files\Docker>docker service ps --no-trunc angry_liskov
ID                          NAME                 IMAGE                                                                                                                     NODE                DESIRED STATE       CURRENT STATE                     ERROR                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          PORTS
dwsd6sjlwsgic5vrglhtxu178   angry_liskov.1       microsoft/iis:windowsservercore-10.0.14393.1715@sha256:868bca7e89e1743792e15f78edb5a73070ef44eae6807dc3f05f9b94c23943d5   WIN-BSTMQDRQC2E     Running             Starting less than a second ago
y5blbdum70zoh1f6uhx5nxsfv    \_ angry_liskov.1   microsoft/iis:windowsservercore-10.0.14393.1715@sha256:868bca7e89e1743792e15f78edb5a73070ef44eae6807dc3f05f9b94c23943d5   WIN-BSTMQDRQC2E     Shutdown            Failed 39 seconds ago             "starting container failed: container e7b5d3adba7e510569c18d8e55f7c689d7cb92be40a516c91b363e27f84604d0 encountered an error during CreateContainer: failure in a Windows system call: The operating system of the container does not match the operating system of the host. (0xc0370101) extra info: {"SystemType":"Container","Name":"e7b5d3adba7e510569c18d8e55f7c689d7cb92be40a516c91b363e27f84604d0","Owner":"docker","VolumePath":"\\\\?\\Volume{2443d38a-1379-4bcf-a4b7-fc6ad4cd7b65}","IgnoreFlushesDuringBoot":true,"LayerFolderPath":"C:\\ProgramData\\docker\\windowsfilter\\e7b5d3adba7e510569c18d8e55f7c689d7cb92be40a516c91b363e27f84604d0","Layers":[{"ID":"bcf2630f-ea95-529b-b33c-e5cdab0afdb4","Path":"C:\\ProgramData\\docker\\windowsfilter\\200235127f92416724ae1d53ed3fdc86d78767132d019bdda1e1192ee4cf3ae4"},{"ID":"e3ea10a8-4c2f-5b93-b2aa-720982f116f6","Path":"C:\\ProgramData\\docker\\windowsfilter\\0ccc9fa71a9f4c5f6f3bc8134fe3533e454e09f453de662cf99ab5d2106abbdc"},{"ID":"cff5391f-e481-593c-aff7-12e080c653ab","Path":"C:\\ProgramData\\docker\\windowsfilter\\a49576b24cd6ec4a26202871c36c0a2083d507394a3072186133131a72601a31"},{"ID":"499cb51e-b891-549a-b1f4-8a25a4665fbd","Path":"C:\\ProgramData\\docker\\windowsfilter\\fdf2f52c4323c62f7ff9b031c0bc3af42cf5fba91098d51089d039fb3e834c08"},{"ID":"1532b584-8431-5b5a-8735-5e1b4fe9c2a9","Path":"C:\\ProgramData\\docker\\windowsfilter\\b2b88bc2a47abcc682e422507abbba9c9b6d826d34e67b9e4e3144cc125a1f80"},{"ID":"a64b8da5-cd6e-5540-bc73-d81acae6da54","Path":"C:\\ProgramData\\docker\\windowsfilter\\5caaedbced1f546bccd01c9d31ea6eea4d30701ebba7b95ee8faa8c098a6845a"}],"HostName":"e7b5d3adba7e","HvPartition":false,"EndpointList":["298bb656-8800-4948-a41c-1b0500f3d94c"],"AllowUnqualifiedDNSQuery":true}"
```

Il s’agit de la même erreur que `CreateContainer: failure in a Windows system call: The operating system of the container does not match the operating system of the host. (0xc0370101)`.

### <a name="fix---update-the-service-to-use-a-matching-version"></a>Correctif : mettre à jour le service pour utiliser une version correspondante

Deux éléments sont à prendre en compte pour Docker Swarm. Si vous avez un fichier de composition disposant d’un service qui utilise une image que vous n’avez pas créée, vous pouvez mettre à jour la référence en conséquence. Par exemple :

``` yaml
version: '3'

services:
  YourServiceName:
    image: microsoft/windowsservercore:1709
...
```

Vous devez également déterminer si l’image vers laquelle vous pointez est une image que vous avez créée (par exemple, contoso/myimage) :

```yaml
version: '3'

services:
  YourServiceName:
    image: contoso/myimage
...
```

Dans ce cas, vous devez utiliser la méthode décrite dans [Erreurs en cas d’incompatibilité des versions](#errors-from-mismatched-versions) pour modifier ce fichier Dockerfile au lieu de la ligne docker-compose.

### <a name="mitigation---use-hyper-v-isolation-with-docker-swarm"></a>Atténuation : utiliser l’isolation Hyper-V avec Docker Swarm

Il existe une proposition de prise en charge de l’utilisation de l’isolation Hyper-V en fonction de chaque conteneur, mais le code n’est pas encore prêt. Vous pouvez suivre la progression sur [GitHub](https://github.com/moby/moby/issues/31616). Jusqu’à ce que cela soit prêt, les ordinateurs hôtes doivent être configurés de manière à toujours s’exécuter avec l’isolation Hyper-V.

Cela nécessite la modification de la configuration du service Docker, puis le redémarrage du moteur Docker.

1. Modifiez `C:\ProgramData\docker\config\daemon.json`.
2. Ajoutez une ligne contenant `"exec-opts":["isolation=hyperv"]`.

    >[!NOTE]
    >Le fichier daemon.json n’existe pas par défaut. Si, après vérification dans le répertoire, cela s’avère être effectivement le cas, vous devez créer le fichier. Vous pouvez ensuite y copier ce qui suit :

    ```JSON
    {
        "exec-opts":["isolation=hyperv"]
    }
    ```

3. Fermez et enregistrez le fichier, puis redémarrez le moteur Docker en exécutant les cmdlets suivantes dans PowerShell :

    ```powershell
    Stop-Service docker
    Start-Service docker
    ```

4. Une fois avoir redémarré le service, lancez vos conteneurs. Une fois ceux-ci en cours d’exécution, vous pouvez vérifier le niveau d’isolation d’un conteneur en inspectant ce dernier avec la cmdlet suivante :

    ```powershell
    docker inspect --format='{{json .HostConfig.Isolation}}' $instanceNameOrId
    ```

Soit « process », soit « hyperv » sera renvoyé. Si vous avez modifié et défini votre fichier daemon.json comme décrit ci-dessus, il doit indiquer cette dernière éventualité.

### <a name="mitigation---use-labels-and-constraints"></a>Atténuation : utiliser des étiquettes et des contraintes

Voici comment utiliser des étiquettes et des contraintes pour faire correspondre les versions :

1. Ajouter des étiquettes à chaque nœud.

    Sur chaque nœud, ajoutez deux étiquettes : `OS` et `OsVersion`. Cela suppose une exécution locale, mais pouvez modifier cela afin de les définir sur un hôte distant.

    ```powershell
    docker node update --label-add OS="windows" $ENV:COMPUTERNAME
    docker node update --label-add OsVersion="$((Get-ComputerInfo).OsVersion)" $ENV:COMPUTERNAME
    ```

    Par la suite, vous pourrez vérifier les étiquettes en exécutant la commande **docker node inspect**, qui doit afficher les étiquettes ajoutées récemment :

    ```yaml
           "Spec": {
                "Labels": {
                   "OS": "windows",
                   "OsVersion": "10.0.16296"
               },
                "Role": "manager",
                "Availability": "active"
            }
    ```

2. Ajoutez une contrainte de service.

    À présent que vous avez étiqueté chaque nœud, vous pouvez mettre à jour les contraintes qui déterminent l’emplacement des services. Dans l’exemple suivant, remplacez « contoso_service » par le nom de votre service réel :

    ```powershell
    docker service update \
        --constraint-add "node.labels.OS == windows" \
        --constraint-add "node.labels.OsVersion == $((Get-ComputerInfo).OsVersion)" \
        contoso_service
    ```

    Cela applique et limite l’emplacement d’exécution d’un nœud.

Pour plus d’informations sur l’utilisation de contraintes de service, consultez la [service create reference](https://docs.docker.com/engine/reference/commandline/service_create/#specify-service-constraints-constraint).

## <a name="matching-versions-using-kubernetes"></a>Correspondance des version à l’aide de Kubernetes

Le problème décrit dans [Correspondance des versions à l’aide de Docker Swarm](#matching-versions-using-docker-swarm) peut se produire lorsque des pods sont planifiés dans Kubernetes. Vous pouvez éviter ce problème à l’aide de stratégies similaires :

- Reconstruisez le conteneur en utilisant la même version du système d’exploitation en développement et en production. Pour savoir comment procéder, consultez [Choisir la version du système d’exploitation du conteneur à utiliser](#choose-which-container-os-version-to-use).
- Utilisez des étiquettes de nœud et des sélecteurs de nœuds pour vous assurer que les pods sont planifiés sur des nœuds compatibles si les nœuds de Windows Server 2016 et de Windows Server, version 1709 se trouvent dans le même cluster.
- Utilisez des clusters distincts en fonction de la version du système d’exploitation

### <a name="finding-pods-failed-on-os-mismatch"></a>Échec de la recherche des pods en raison de l’incompatibilité des systèmes d’exploitation

Dans ce cas, un déploiement incluait un pod qui était planifié sur un nœud dont la version du système d’exploitation était incompatible, et sur lequel l’isolation Hyper-V n’était pas activée.

Le même message d’erreur s’affiche dans les événements répertoriés, avec `kubectl describe pod <podname>`. Après plusieurs tentatives, l’état du pod sera probablement `CrashLoopBackOff`.

```
$ kubectl -n plang describe pod fabrikamfiber.web-789699744-rqv6p

Name:           fabrikamfiber.web-789699744-rqv6p
Namespace:      plang
Node:           38519acs9011/10.240.0.6
Start Time:     Mon, 09 Oct 2017 19:40:30 +0000
Labels:         io.kompose.service=fabrikamfiber.web
                pod-template-hash=789699744
Annotations:    kubernetes.io/created-by={"kind":"SerializedReference","apiVersion":"v1","reference":{"kind":"ReplicaSet","namespace":"plang","name":"fabrikamfiber.web-789699744","uid":"b5062a08-ad29-11e7-b16e-000d3a...
Status:         Running
IP:             10.244.3.169
Created By:     ReplicaSet/fabrikamfiber.web-789699744
Controlled By:  ReplicaSet/fabrikamfiber.web-789699744
Containers:
  fabrikamfiberweb:
    Container ID:       docker://eab0151378308315ed6c31adf4ad9e31e6d65fd300e56e742757004a969a803a
    Image:              patricklang/fabrikamfiber.web:latest
    Image ID:           docker-pullable://patricklang/fabrikamfiber.web@sha256:562741016ce7d9a232a389449a4fd0a0a55aab178cf324144404812887250ead
    Port:               80/TCP
    State:              Waiting
      Reason:           CrashLoopBackOff
    Last State:         Terminated
      Reason:           ContainerCannotRun
      Message:          container eab0151378308315ed6c31adf4ad9e31e6d65fd300e56e742757004a969a803a encountered an error during CreateContainer: failure in a Windows system call: The operating system of the container does not match the operating system of the host. (0xc0370101) extra info: {"SystemType":"Container","Name":"eab0151378308315ed6c31adf4ad9e31e6d65fd300e56e742757004a969a803a","Owner":"docker","IsDummy":false,"VolumePath":"\\\\?\\Volume{037b6606-bc9c-461f-ae02-829c28410798}","IgnoreFlushesDuringBoot":true,"LayerFolderPath":"C:\\ProgramData\\docker\\windowsfilter\\eab0151378308315ed6c31adf4ad9e31e6d65fd300e56e742757004a969a803a","Layers":[{"ID":"f8bc427f-7aa3-59c6-b271-7331713e9451","Path":"C:\\ProgramData\\docker\\windowsfilter\\e206d2514a6265a76645b9d6b3dc6a78777c34dbf5da9fa2d564651645685881"},{"ID":"a6f35e41-a86c-5e4d-a19a-a6c2464bfb47","Path":"C:\\ProgramData\\docker\\windowsfilter\\0f21f1e28ef13030bbf0d87cbc97d1bc75f82ea53c842e9a3250a2156ced12d5"},{"ID":"4f624ca7-2c6d-5c42-b73f-be6e6baf2530","Path":"C:\\ProgramData\\docker\\windowsfilter\\4d9e2ad969fbd74fd58c98b5ab61e55a523087910da5200920e2b6f641d00c67"},{"ID":"88e360ff-32af-521d-9a3f-3760c12b35e2","Path":"C:\\ProgramData\\docker\\windowsfilter\\9e16a3d53d3e9b33344a6f0d4ed34c8a46448ee7636b672b61718225b8165e6e"},{"ID":"20f1a4e0-a6f3-5db3-9bf2-01fd3e9e855a","Path":"C:\\ProgramData\\docker\\windowsfilter\\7eec7f59f9adce38cc0a6755da58a3589287d920d37414b5b21b5b543d910461"},{"ID":"c2b3d728-4879-5343-a92a-b735752a4724","Path":"C:\\ProgramData\\docker\\windowsfilter\\8ed04b60acc0f65f541292a9e598d5f73019c8db425f8d49ea5819eab16a42f3"},{"ID":"2973e760-dc59-5800-a3de-ab9d93be81e5","Path":"C:\\ProgramData\\docker\\windowsfilter\\cc71305d45f09ce377ef497f28c3a74ee027c27f20657d2c4a5f157d2457cc75"},{"ID":"454a7d36-038c-5364-8a25-fa84091869d6","Path":"C:\\ProgramData\\docker\\windowsfilter\\9e8cde1ce8f5de861a5f22841f1ab9bc53d5f606d06efeacf5177f340e8d54d0"},{"ID":"9b748c8c-69eb-55fb-a1c1-5688cac4efd8","Path":"C:\\ProgramData\\docker\\windowsfilter\\8e02bf5404057055a71d542780a2bb2731be4b3707c01918ba969fb4d83b98ec"},{"ID":"bfde5c26-b33f-5424-9405-9d69c2fea4d0","Path":"C:\\ProgramData\\docker\\windowsfilter\\77483cedfb0964008c33d92d306734e1fab3b5e1ebb27e898f58ccfd108108f2"},{"ID":"bdabfbf5-80d1-57f1-86f3-448ce97e2d05","Path":"C:\\ProgramData\\docker\\windowsfilter\\aed2ebbb31ad24b38ee8521dd17744319f83d267bf7f360bc177e27ae9a006cf"},{"ID":"ad9b34f2-dcee-59ea-8962-b30704ae6331","Path":"C:\\ProgramData\\docker\\windowsfilter\\d44d3a675fec1070b61d6ea9bacef4ac12513caf16bd6453f043eed2792f75d8"}],"HostName":"fabrikamfiber.web-789699744-rqv6p","MappedDirectories":[{"HostPath":"c:\\var\\lib\\kubelet\\pods\\b50f0027-ad29-11e7-b16e-000d3afd2878\\volumes\\kubernetes.io~secret\\default-token-rw9dn","ContainerPath":"c:\\var\\run\\secrets\\kubernetes.io\\serviceaccount","ReadOnly":true,"BandwidthMaximum":0,"IOPSMaximum":0}],"HvPartition":false,"EndpointList":null,"NetworkSharedContainerName":"586870f5833279678773cb700db3c175afc81d557a75867bf39b43f985133d13","Servicing":false,"AllowUnqualifiedDNSQuery":false}
      Exit Code:        128
      Started:          Mon, 09 Oct 2017 20:27:08 +0000
      Finished:         Mon, 09 Oct 2017 20:27:08 +0000
    Ready:              False
    Restart Count:      10
    Environment:        <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-rw9dn (ro)
Conditions:
  Type          Status
  Initialized   True
  Ready         False
  PodScheduled  True
Volumes:
  default-token-rw9dn:
    Type:       Secret (a volume populated by a Secret)
    SecretName: default-token-rw9dn
    Optional:   false
QoS Class:      BestEffort
Node-Selectors: beta.kubernetes.io/os=windows
Tolerations:    <none>
Events:
  FirstSeen     LastSeen        Count   From                    SubObjectPath                           Type            Reason                  Message
  ---------     --------        -----   ----                    -------------                           --------        ------                  -------
  49m           49m             1       default-scheduler                                               Normal          Scheduled               Successfully assigned fabrikamfiber.web-789699744-rqv6p to 38519acs9011
  49m           49m             1       kubelet, 38519acs9011                                           Normal          SuccessfulMountVolume   MountVolume.SetUp succeeded for volume "default-token-rw9dn"
  29m           29m             1       kubelet, 38519acs9011   spec.containers{fabrikamfiberweb}       Warning         Failed                  Failed to pull image "patricklang/fabrikamfiber.web:latest": rpc error: code = 2 desc = Error response from daemon: {"message":"Get https://registry-1.docker.io/v2/: dial tcp: lookup registry-1.docker.io: no such host"}
  49m           3m              12      kubelet, 38519acs9011   spec.containers{fabrikamfiberweb}       Normal          Pulling                 pulling image "patricklang/fabrikamfiber.web:latest"
  33m           3m              11      kubelet, 38519acs9011   spec.containers{fabrikamfiberweb}       Normal          Pulled                  Successfully pulled image "patricklang/fabrikamfiber.web:latest"
  33m           3m              11      kubelet, 38519acs9011   spec.containers{fabrikamfiberweb}       Normal          Created                 Created container
  33m           2m              11      kubelet, 38519acs9011   spec.containers{fabrikamfiberweb}       Warning         Failed                  Error: failed to start container "fabrikamfiberweb": Error response from daemon: {"message":"container fabrikamfiberweb encountered an error during CreateContainer: failure in a Windows system call: The operating system of the container does not match the operating system of the host. (0xc0370101) extra info: {\"SystemType\":\"Container\",\"Name\":\"fabrikamfiberweb\",\"Owner\":\"docker\",\"IsDummy\":false,\"VolumePath\":\"\\\\\\\\?\\\\Volume{037b6606-bc9c-461f-ae02-829c28410798}\",\"IgnoreFlushesDuringBoot\":true,\"LayerFolderPath\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\fabrikamfiberweb\",\"Layers\":[{\"ID\":\"f8bc427f-7aa3-59c6-b271-7331713e9451\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\e206d2514a6265a76645b9d6b3dc6a78777c34dbf5da9fa2d564651645685881\"},{\"ID\":\"a6f35e41-a86c-5e4d-a19a-a6c2464bfb47\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\0f21f1e28ef13030bbf0d87cbc97d1bc75f82ea53c842e9a3250a2156ced12d5\"},{\"ID\":\"4f624ca7-2c6d-5c42-b73f-be6e6baf2530\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\4d9e2ad969fbd74fd58c98b5ab61e55a523087910da5200920e2b6f641d00c67\"},{\"ID\":\"88e360ff-32af-521d-9a3f-3760c12b35e2\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\9e16a3d53d3e9b33344a6f0d4ed34c8a46448ee7636b672b61718225b8165e6e\"},{\"ID\":\"20f1a4e0-a6f3-5db3-9bf2-01fd3e9e855a\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\7eec7f59f9adce38cc0a6755da58a3589287d920d37414b5b21b5b543d910461\"},{\"ID\":\"c2b3d728-4879-5343-a92a-b735752a4724\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\8ed04b60acc0f65f541292a9e598d5f73019c8db425f8d49ea5819eab16a42f3\"},{\"ID\":\"2973e760-dc59-5800-a3de-ab9d93be81e5\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\cc71305d45f09ce377ef497f28c3a74ee027c27f20657d2c4a5f157d2457cc75\"},{\"ID\":\"454a7d36-038c-5364-8a25-fa84091869d6\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\9e8cde1ce8f5de861a5f22841f1ab9bc53d5f606d06efeacf5177f340e8d54d0\"},{\"ID\":\"9b748c8c-69eb-55fb-a1c1-5688cac4efd8\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\8e02bf5404057055a71d542780a2bb2731be4b3707c01918ba969fb4d83b98ec\"},{\"ID\":\"bfde5c26-b33f-5424-9405-9d69c2fea4d0\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\77483cedfb0964008c33d92d306734e1fab3b5e1ebb27e898f58ccfd108108f2\"},{\"ID\":\"bdabfbf5-80d1-57f1-86f3-448ce97e2d05\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\aed2ebbb31ad24b38ee8521dd17744319f83d267bf7f360bc177e27ae9a006cf\"},{\"ID\":\"ad9b34f2-dcee-59ea-8962-b30704ae6331\",\"Path\":\"C:\\\\ProgramData\\\\docker\\\\windowsfilter\\\\d44d3a675fec1070b61d6ea9bacef4ac12513caf16bd6453f043eed2792f75d8\"}],\"HostName\":\"fabrikamfiber.web-789699744-rqv6p\",\"MappedDirectories\":[{\"HostPath\":\"c:\\\\var\\\\lib\\\\kubelet\\\\pods\\\\b50f0027-ad29-11e7-b16e-000d3afd2878\\\\volumes\\\\kubernetes.io~secret\\\\default-token-rw9dn\",\"ContainerPath\":\"c:\\\\var\\\\run\\\\secrets\\\\kubernetes.io\\\\serviceaccount\",\"ReadOnly\":true,\"BandwidthMaximum\":0,\"IOPSMaximum\":0}],\"HvPartition\":false,\"EndpointList\":null,\"NetworkSharedContainerName\":\"586870f5833279678773cb700db3c175afc81d557a75867bf39b43f985133d13\",\"Servicing\":false,\"AllowUnqualifiedDNSQuery\":false}"}
  33m           11s             151     kubelet, 38519acs9011                                           Warning         FailedSync              Error syncing pod
  32m           11s             139     kubelet, 38519acs9011   spec.containers{fabrikamfiberweb}       Warning         BackOff                 Back-off restarting failed container
```

### <a name="mitigation---using-node-labels-and-nodeselector"></a>Atténuation – utilisation d’étiquettes de nœud et de sélecteur de nœud

Exécutez la commande **kubectl get node** pour obtenir la liste de tous les nœuds. Après cela, vous pouvez exécuter la commande **kubectl describe node (nom de nœud)** pour obtenir plus de détails.

Dans l’exemple suivant, deux nœuds Windows exécutent des versions différentes :

```
$ kubectl get node

NAME                        STATUS    AGE       VERSION
38519acs9010                Ready     21h       v1.7.7-7+e79c96c8ff2d8e
38519acs9011                Ready     4h        v1.7.7-25+bc3094f1d650a2
k8s-linuxpool1-38519084-0   Ready     21h       v1.7.7
k8s-master-38519084-0       Ready     21h       v1.7.7

$ kubectl describe node 38519acs9010

Name:                   38519acs9010
Role:
Labels:                 beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/instance-type=Standard_D2_v2
                        beta.kubernetes.io/os=windows
                        failure-domain.beta.kubernetes.io/region=westus2
                        failure-domain.beta.kubernetes.io/zone=0
                        kubernetes.io/hostname=38519acs9010
Annotations:            node.alpha.kubernetes.io/ttl=0
                        volumes.kubernetes.io/controller-managed-attach-detach=true
Taints:                 <none>
CreationTimestamp:      Fri, 06 Oct 2017 01:41:02 +0000

...

System Info:
 Machine ID:                    38519acs9010
 System UUID:
 Boot ID:
 Kernel Version:                10.0 14393 (14393.1715.amd64fre.rs1_release_inmarket.170906-1810)
 OS Image:
 Operating System:              windows
 Architecture:                  amd64
 ...

$ kubectl describe node 38519acs9011
Name:                   38519acs9011
Role:
Labels:                 beta.kubernetes.io/arch=amd64
                        beta.kubernetes.io/instance-type=Standard_DS1_v2
                        beta.kubernetes.io/os=windows
                        failure-domain.beta.kubernetes.io/region=westus2
                        failure-domain.beta.kubernetes.io/zone=0
                        kubernetes.io/hostname=38519acs9011
Annotations:            node.alpha.kubernetes.io/ttl=0
                        volumes.kubernetes.io/controller-managed-attach-detach=true
Taints:                 <none>
CreationTimestamp:      Fri, 06 Oct 2017 18:13:25 +0000
Conditions:
...

System Info:
 Machine ID:                    38519acs9011
 System UUID:
 Boot ID:
 Kernel Version:                10.0 16299 (16299.0.amd64fre.rs3_release.170922-1354)
 OS Image:
 Operating System:              windows
 Architecture:                  amd64
...

```

Utilisons cet exemple pour montrer comment faire correspondre les versions :

1. Prenez note de chaque nom de nœud et de la valeur `Kernel Version` dans les informations système.

    Dans notre exemple, les informations se présentent comme suit :

    Nom         | Version
    -------------|--------------------------------------------------------
    38519acs9010 | 14393.1715.amd64fre.rs1_release_inmarket.170906-1810
    38519acs9011 | 16299.0.amd64fre.rs3_release.170922-1354

2. Ajoutez à chaque nœud une étiquette appelée `beta.kubernetes.io/osbuild`. Windows Server 2016 a besoin que les versions majeure et mineure (14393.1715 dans cet exemple) soient prises en charge sans isolation Hyper-V. Windows Server, version 1709 a seulement besoin que la version majeure (16299 dans cet exemple) corresponde.

    Dans cet exemple, la commande pour l’ajout des étiquettes ressemble à ceci :

    ```
    $ kubectl label node 38519acs9010 beta.kubernetes.io/osbuild=14393.1715


    node "38519acs9010" labeled
    $ kubectl label node 38519acs9011 beta.kubernetes.io/osbuild=16299

    node "38519acs9011" labeled

    ```

3. Vérifiez la présence des étiquettes en exécutant la commande **kubectl get nodes --show-labels**.

    Dans cet exemple, la sortie doit ressembler à ceci :

    ```
    $ kubectl get nodes --show-labels

    NAME                        STATUS                     AGE       VERSION                    LABELS
    38519acs9010                Ready,SchedulingDisabled   3d        v1.7.7-7+e79c96c8ff2d8e    beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_D2_v2,beta.kubernetes.io/os=windows,beta.kubernetes.io/osbuild=14393.1715,failure-domain.beta.kubernetes.io/region=westus2,failure-domain.beta.kubernetes.io/zone=0,kubernetes.io/hostname=38519acs9010
    38519acs9011                Ready                      3d        v1.7.7-25+bc3094f1d650a2   beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=windows,beta.kubernetes.io/osbuild=16299,failure-domain.beta.kubernetes.io/region=westus2,failure-domain.beta.kubernetes.io/zone=0,kubernetes.io/hostname=38519acs9011
    k8s-linuxpool1-38519084-0   Ready                      3d        v1.7.7                     agentpool=linuxpool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_D2_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=westus2,failure-domain.beta.kubernetes.io/zone=0,kubernetes.io/hostname=k8s-linuxpool1-38519084-0,kubernetes.io/role=agent
    k8s-master-38519084-0       Ready                      3d        v1.7.7                     beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_D2_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=westus2,failure-domain.beta.kubernetes.io/zone=0,kubernetes.io/hostname=k8s-master-38519084-0,kubernetes.io/role=master
    ```

4. Ajoutez des sélecteurs de nœuds aux déploiements. Dans cet exemple, nous allons ajouter un `nodeSelector` à la spécification de conteneur avec `beta.kubernetes.io/os` = windows et `beta.kubernetes.io/osbuild` = 14393.* ou 16299 pour assurer la correspondance avec le système d’exploitation de base que le conteneur utilise.

    Voici un exemple complet pour l’exécution d’un conteneur conçu pour Windows Server 2016 :

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
      annotations:
        kompose.cmd: kompose convert -f docker-compose-combined.yml
        kompose.version: 1.2.0 (99f88ef)
      creationTimestamp: null
      labels:
        io.kompose.service: fabrikamfiber.web
      name: fabrikamfiber.web
    spec:
      replicas: 1
      strategy: {}
      template:
        metadata:
          creationTimestamp: null
          labels:
            io.kompose.service: fabrikamfiber.web
        spec:
          containers:
          - image: patricklang/fabrikamfiber.web:latest
            name: fabrikamfiberweb
            ports:
            - containerPort: 80
            resources: {}
          restartPolicy: Always
          nodeSelector:
            "beta.kubernetes.io/os": windows
            "beta.kubernetes.io/osbuild": "14393.1715"
    status: {}
    ```

    Le pod peut désormais lancer le déploiement mis à jour. Les sélecteurs de nœuds figurant également dans la commande `kubectl describe pod <podname>`, vous pouvez utiliser celle-ci pour vérifier qu’ils ont été ajoutés.

    La sortie de notre exemple est la suivante :

    ```
    $ kubectl -n plang describe po fa

    Name:           fabrikamfiber.web-1780117715-5c8vw
    Namespace:      plang
    Node:           38519acs9010/10.240.0.4
    Start Time:     Tue, 10 Oct 2017 01:43:28 +0000
    Labels:         io.kompose.service=fabrikamfiber.web
                    pod-template-hash=1780117715
    Annotations:    kubernetes.io/created-by={"kind":"SerializedReference","apiVersion":"v1","reference":{"kind":"ReplicaSet","namespace":"plang","name":"fabrikamfiber.web-1780117715","uid":"6a07aaf3-ad5c-11e7-b16e-000d3...
    Status:         Running
    IP:             10.244.1.84
    Created By:     ReplicaSet/fabrikamfiber.web-1780117715
    Controlled By:  ReplicaSet/fabrikamfiber.web-1780117715
    Containers:
      fabrikamfiberweb:
        Container ID:       docker://c94594fb53161f3821cf050d9af7546991aaafbeab41d333d9f64291327fae13
        Image:              patricklang/fabrikamfiber.web:latest
        Image ID:           docker-pullable://patricklang/fabrikamfiber.web@sha256:562741016ce7d9a232a389449a4fd0a0a55aab178cf324144404812887250ead
        Port:               80/TCP
        State:              Running
          Started:          Tue, 10 Oct 2017 01:43:42 +0000
        Ready:              True
        Restart Count:      0
        Environment:        <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-rw9dn (ro)
    Conditions:
      Type          Status
      Initialized   True
      Ready         True
      PodScheduled  True
    Volumes:
      default-token-rw9dn:
        Type:       Secret (a volume populated by a Secret)
        SecretName: default-token-rw9dn
        Optional:   false
    QoS Class:      BestEffort
    Node-Selectors: beta.kubernetes.io/os=windows
                    beta.kubernetes.io/osbuild=14393.1715
    Tolerations:    <none>
    Events:
      FirstSeen     LastSeen        Count   From                    SubObjectPath                           Type            Reason                  Message
      ---------     --------        -----   ----                    -------------                           --------        ------                  -------
      5m            5m              1       default-scheduler                                               Normal          Scheduled               Successfully assigned fabrikamfiber.web-1780117715-5c8vw to 38519acs9010
      5m            5m              1       kubelet, 38519acs9010                                           Normal          SuccessfulMountVolume   MountVolume.SetUp succeeded for volume "default-token-rw9dn"
      5m            5m              1       kubelet, 38519acs9010   spec.containers{fabrikamfiberweb}       Normal          Pulling                 pulling image "patricklang/fabrikamfiber.web:latest"
      5m            5m              1       kubelet, 38519acs9010   spec.containers{fabrikamfiberweb}       Normal          Pulled                  Successfully pulled image "patricklang/fabrikamfiber.web:latest"
      5m            5m              1       kubelet, 38519acs9010   spec.containers{fabrikamfiberweb}       Normal          Created                 Created container
      5m            5m              1       kubelet, 38519acs9010   spec.containers{fabrikamfiberweb}       Normal          Started                 Started container
    ```