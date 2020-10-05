---
title: Plateforme de conteneur Windows
description: Apprenez-en davantage sur les nouveaux blocs de construction de conteneur disponibles dans Windows.
keywords: conteneurs Linux, docker, conteneurs, mise en conteneur, conteneurisation, runhcs, runc, containerd
author: scooley
ms.author: jgerend
ms.date: 11/19/2018
ms.topic: conceptual
ms.assetid: a0e62b32-0c4c-4dd4-9956-8056e9abd9e5
ms.openlocfilehash: 55c404252dc9243070631e2012326d4b0cca886f
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990972"
---
# <a name="container-platform-tools-on-windows"></a>Outils de plateforme de conteneurs sur Windows

La plateforme de conteneurs Windows est en cours expansion. Après Docker, première partie du parcours, nous créons à présent d’autres outils de plateforme de conteneurs.

* [containerd/cri](https://github.com/containerd/cri) : nouveauté dans Windows Server 2019/Windows 10 1809.
* [runhcs](https://github.com/Microsoft/hcsshim/tree/master/cmd/runhcs) : hôte de conteneur Windows équivalent de runc pour Linux.
* [hcs](/virtualization/api/) : service de calcul hôte + shims pratiques pour faciliter son utilisation.
  * [hcsshim](https://github.com/microsoft/hcsshim)
  * [dotnet-computevirtualization](https://github.com/microsoft/dotnet-computevirtualization)

Cet article traite de la plateforme de conteneurs Windows et Linux, ainsi que des outils de plateforme de chaque conteneur.

## <a name="windows-and-linux-container-platform"></a>Plateforme de conteneur Windows et Linux

Dans les environnements Linux, les outils de gestion de conteneurs tels que Docker sont basés sur des outils de conteneur plus spécifiques : [Runc](https://github.com/opencontainers/runc) et [containerd](https://containerd.io/).

![Architecture Docker sur Linux](media/docker-on-linux.png)

`runc` est un outil en ligne de commande Linux permettant de créer et d’exécuter des conteneurs conformément à la [spécification de runtime de conteneur OCI](https://github.com/opencontainers/runtime-spec).

`containerd` est un démon qui gère le cycle de vie d’un conteneur, depuis le téléchargement et la décompression de l’image du conteneur jusqu’à l’exécution et la surveillance de celui-ci.

Sur Windows, nous avons adopté une approche différente.  Lorsque nous avons commencé à utiliser Docker pour la prise en charge des conteneurs Windows, nous avons directement créé le service de calcul hôte (HCS).  [Ce billet de blog](https://techcommunity.microsoft.com/t5/Containers/Introducing-the-Host-Compute-Service-HCS/ba-p/382332) regorge d’informations sur la raison pour laquelle nous avons créé le service de calcul hôte et adopté initialement cette approche des conteneurs.

![Architecture initiale du moteur Docker sur Windows](media/hcs.png)

Actuellement, Docker continue de faire appel directement au service de calcul hôte. À l’avenir cependant, les outils de gestion de conteneur qui s’étoffent pour inclure des conteneurs Windows et l’hôte de conteneur Windows pourraient faire appel à containerd et à runhcs tout comme ils font appel à containerd et runc sur Linux.

## <a name="runhcs"></a>runhcs

`runhcs` est une duplication (fork) de `runc`.  Comme `runc`, `runhcs` est un client de ligne de commande pour l’exécution d’applications empaquetées au format OCI (Open Container Initiative) et constitue une implémentation conforme de la spécification Open Container Initiative.

Les différences fonctionnelles entre runc et runhcs sont les suivantes :

* `runhcs` s’exécute sur Windows.  Il communique avec le [service de calcul hôte](containerd.md#hcs) pour créer et gérer des conteneurs.
* `runhcs` peut exécuter différents types de conteneurs.

  * [Isolation Hyper-V](../manage-containers/hyperv-container.md) de Windows et Linux
  * Conteneurs de processus Windows (l’image du conteneur doit correspondre à l’hôte du conteneur)

**Utilisation :**

``` cmd
runhcs run [ -b bundle ] <container-id>
```

`<container-id>` est le nom de l’instance de conteneur que vous démarrez. Le nom doit être unique sur l’hôte de votre conteneur.

Le répertoire du bundle (utilisant `-b bundle`) est facultatif.
Comme avec runc, les conteneurs sont configurés à l’aide de bundles. Le bundle d’un conteneur est le répertoire contenant le fichier de spécification OCI du conteneur, « config.json ».  La valeur par défaut de « bundle » est le répertoire actif.

Le fichier de spécifications OCI, « config.json », doit avoir deux champs pour s’exécuter correctement :

* Chemin d’accès à l’espace de travail du conteneur.
* Chemin d’accès au répertoire de couche du conteneur.

Les commandes de conteneur disponibles dans runhcs sont les suivantes :

* Outils de création et d’exécution d’un conteneur :
  * **run** : crée et exécute un conteneur.
  * **create** : crée un conteneur.

* Outils de gestion des processus s’exécutant dans un conteneur :
  * **start** : exécute le processus défini par l’utilisateur dans un conteneur créé
  * **exec** : exécute un nouveau processus à l’intérieur du conteneur.
  * **pause** : suspend tous les processus à l’intérieur du conteneur.
  * **resume** reprend tous les processus précédemment suspendus.
  * **ps** : affiche les processus en cours d’exécution à l’intérieur d’un conteneur.

* Outils de gestion de l’état d’un conteneur :
  * **state** : indique l’état d’un conteneur.
  * **kill** : envoie le signal spécifié (par défaut, SIGTERM) au processus d’initialisation du conteneur.
  * **delete** : supprime toutes les ressources détenues par le conteneur, souvent utilisé avec le conteneur détaché.

La seule commande qui peut être considérée comme multiconteneur est **list**.  Elle répertorie les conteneurs en cours d’exécution ou suspendus qui ont été démarrés par runhcs avec la racine donnée.

### <a name="hcs"></a>Service de calcul hôte

Deux wrappers sont disponibles sur GitHub pour s’interfacer avec le service de calcul hôte. Étant donné que service de calcul hôte est une API C, les wrappers facilitent l’appel de ce service à partir de langages de niveau supérieur.

* [hcsshim](https://github.com/microsoft/hcsshim) : HCSShim est écrit en Go et constitue la base pour runhcs.
Récupérez-le à partir d’AppVeyor ou générez-le vous-même.
* [dotnet-computevirtualization](https://github.com/microsoft/dotnet-computevirtualization) : wrapper en C# pour le service de calcul hôte.

Si vous souhaitez utiliser le service de calcul hôte (directement ou via un wrapper), ou créer un wrapper Rust/Haskell/InsérezVotreLangage autour du service de calcul hôte, laissez un commentaire.

Pour un examen approfondi du service de calcul hôte, regardez la [présentation DockerCon de John Stark](https://www.youtube.com/watch?v=85nCF5S8Qok).

## <a name="containerdcri"></a>containerd/cri

> [!IMPORTANT]
> La prise en charge de l’interface de runtime de conteneur (Container Runtime Interface, CRI) n’est disponible que dans Windows Server 2019/Windows 10, versions 1809 et ultérieures.  Nous continuons de développer activement containerd pour Windows.
> Dev/test uniquement.

Tandis que les spécifications OCI ne définissent qu’un seul conteneur, l’interface [CRI](https://github.com/kubernetes/kubernetes/blob/master/pkg/kubelet/apis/cri/runtime/v1alpha2/api.proto) décrit les conteneurs en tant que charges de travail dans un environnement de bac à sable (sandbox) partagé nommé pod.  Des pods peuvent contenir une ou plusieurs charges de travail de conteneur.  Ils permettent à des orchestrateurs de conteneurs tels que Kubernetes et Service Fabric Mesh de gérer des charges de travail groupées qui doivent se trouver sur le même hôte avec des ressources partagées telles que la mémoire et les réseaux virtuels.

containerd/cri active la matrice de compatibilité suivante pour les pods :

| Système d’exploitation hôte | Image du | Isolation | Prise en charge de pod ? |
|:-------------------------------------------------------------------------|:-----------------------------------------------------------------------------|:---------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------|
| <ul><li>Windows Server 2019/1809</ul></li><ul><li>Windows 10 1809</ul></li> | Linux | `hyperv` | Oui : prend en charge les pods multiconteneurs véritables. |
|  | Windows Server 2019/1809 | `process`* ou `hyperv` | Oui : prend en charge les pods multiconteneurs véritables si le système d’exploitation du conteneur de chaque charge de travail correspond à celui de la machine virtuelle de l’utilitaire. |
|  | Windows Server 2016,</br>Windows Server 1709,</br>Windows Server 1803 | `hyperv` | Partielle : prend en charge les bacs à sable de pod qui prennent en charge un seul conteneur isolé par processus par machine virtuelle d’utilitaire si le système d’exploitation du conteneur correspond à celui de la machine virtuelle de l’utilitaire. |

\*Les hôtes Windows 10 ne prennent en charge que l’isolation Hyper-V

Liens vers la spécification de l’interface CRI :

* [RunPodSandbox](https://github.com/kubernetes/kubernetes/blob/master/pkg/kubelet/apis/cri/runtime/v1alpha2/api.proto#L24) : spécification de pod.
* [CreateContainer](https://github.com/kubernetes/kubernetes/blob/master/pkg/kubelet/apis/cri/runtime/v1alpha2/api.proto#L47) : spécification de charge de travail.

![Environnements de conteneurs basés sur containerd](media/containerd-platform.png)

Alors que runhcs et containerd peuvent tous deux opérer sur n’importe quel serveur système Windows Server 2016 ou version ultérieure, la prise en charge des pods (groupes de conteneurs) nécessitait l’apport de modifications radicales aux outils de conteneur dans Windows.  La prise en charge de l’interface CRI est disponible sur Windows Server 2019/Windows 10, versions 1809 et ultérieures.