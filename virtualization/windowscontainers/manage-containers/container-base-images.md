---
title: Images de base de conteneur Windows
description: Vue d’ensemble des images de base de conteneur Windows et du moment opportun pour les utiliser.
keywords: Docker, conteneurs, hachages
author: patricklang
ms.date: 09/25/2019
ms.topic: conceptual
ms.assetid: 88e6e080-cf8f-41d8-a301-035959dc5ce0
ms.openlocfilehash: 375dbecece5f5166ce0d44c7317739658d42983f
ms.sourcegitcommit: dc5d955689dfb51d43809b4dcf38468737d89c21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97345712"
---
# <a name="container-base-images"></a>Images de base de conteneur

Windows propose quatre images de base de conteneur à partir desquelles les utilisateurs peuvent créer. Chaque image de base est un type distinct du système d’exploitation Windows, occupe un espace sur disque spécifique et contient une quantité différente de l’ensemble d’API Windows.

<ul class="columns is-multiline has-margin-left-none has-margin-bottom-none has-padding-top-medium">
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows-servercore" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="Microsoft logo" data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Windows Server Core</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Prend en charge les applications .NET Framework traditionnelles.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows-nanoserver" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="Microsoft logo." data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Nano Server</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Conçu pour les applications .NET Core.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="Microsoft logo." data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Windows</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Fournit l’ensemble API Windows complet.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
    <li class="column is-one-quarter has-padding-top-small-mobile has-padding-bottom-small">
        <a class="is-undecorated is-full-height is-block"
            href="https://hub.docker.com/_/microsoft-windows-iotcore" data-linktype="external">
            <article class="card has-outline-hover is-relative is-full-height has-padding-none">
                    <div class="cardImageOuter bgdAccent1 has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                        <div class="cardImage centered has-padding-top-large has-padding-bottom-large has-padding-left-large has-padding-right-large">
                            <img src="media/Microsoft_logo.svg" alt="Microsoft logo." data-linktype="relative-path">
                        </div>
                    </div>
                <div class="card-content has-text-overflow-ellipsis has-padding-top-small">
                    <div class="has-padding-bottom-none">
                        <h3 class="is-size-4 has-margin-top-none has-margin-bottom-none has-text-primary">Windows IoT Core</h3>
                    </div>
                    <div class="is-size-7 has-margin-top-small has-line-height-reset">
                        <p>Spécialement conçu pour les applications IoT.</p>
                    </div>
                </div>
            </article>
        </a>
    </li>
</ul>

## <a name="image-discovery"></a>Détection d’images

Toutes les images de base de conteneur Windows sont détectables via le [Docker Hub](https://hub.docker.com/_/microsoft-windows-base-os-images). Les images de base de conteneur Windows proprement dites sont servies à partir de [mcr.microsoft.com](https://azure.microsoft.com/services/container-registry/), le Registre de conteneurs Microsoft. C’est pourquoi les commandes d’extraction des images de base de conteneur Windows se présentent comme suit :

```code
docker pull mcr.microsoft.com/windows/servercore:ltsc2019
```

Le Registre de conteneurs Microsoft n’a pas son propre catalogue et est destiné à prendre en charge des catalogues existants tels que le Docker Hub. Grâce à l’empreinte mondiale d’Azure et couplée avec Azure CDN, le Registre de conteneurs Microsoft offre une expérience d’extraction d’image cohérente et rapide. Les clients Azure, qui exécutent leurs charges de travail dans Azure, bénéficient des améliorations des performances dans le réseau, ainsi que de l’intégration étroite avec le Registre de conteneurs Microsoft (source pour les images de conteneur Microsoft), de la Place de marché Azure et du nombre croissant de services dans Azure qui proposent des conteneurs en tant que format de package de déploiement.

## <a name="choosing-a-base-image"></a>Choix d’une image de base

Comment choisir l’image de base appropriée à partir de laquelle créer ? Pour la plupart des utilisateurs, `Windows Server Core` et `Nanoserver` constitueront l’image la plus appropriée à utiliser.

### <a name="guidelines"></a>Recommandations

 Si vous êtes libre de cibler l’image que vous voulez, voici quelques recommandations pour vous aider à orienter votre choix :

- **Votre application a-t-elle besoin du .NET Framework complet ?** Si la réponse à cette question est affirmative, vous devez cibler `Windows Server Core`.
- **Créez-vous une application Windows basée sur .NET Core ?** Si la réponse à cette question est affirmative, vous devez cibler `Nanoserver`.
- **Créez-vous une application IoT ?** Si la réponse à cette question est affirmative, vous devez cibler `IoT Core`.
- **L’image du conteneur Windows Server Core manque-t-elle d’une dépendance dont votre application a besoin ?** Si la réponse à cette question est affirmative, vous devez tenter de cibler `Windows`. Cette image est beaucoup plus volumineuse que les autres images de base, mais elle contient un grand nombre des bibliothèques principales de Windows (telles que la bibliothèque GDI).
- **Participez-vous au programme Windows Insider ?** Si c’est le cas, vous devez envisager d’utiliser la version Insider des images. Consultez ci-dessous la section « Images de base pour les participants au programme Windows Insider ».

> [!TIP]
> De nombreux utilisateurs de Windows souhaitent placer en conteneur des applications qui dépendent de .NET. En plus des quatre images de base décrites ici, Microsoft publie plusieurs images de conteneur Windows qui sont préconfigurées avec des infrastructures Microsoft populaires, telles que les images [.NET Framework](https://hub.docker.com/_/microsoft-dotnet-framework) et [ASP.NET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/).

### <a name="base-images-for-windows-insiders"></a>Images de base pour les participants au programme Windows Insider

Microsoft fournit des versions « Insider » de chaque image de base de conteneur. Ces images de conteneur pour les participants au programme Windows Insider contiennent le développement de fonctionnalités le plus récent et le plus volumineux de nos images de conteneur. L’usage de ces images est recommandé si vous exécutez un ordinateur hôte qui est une version « Insider » de Windows (Windows Insider ou Windows Server Insider). Les images « Insider » sont disponibles sur le Docker Hub :

- [mcr.microsoft.com/windows/servercore/insider](https://hub.docker.com/_/microsoft-windows-servercore-insider)
- [mcr.microsoft.com/windows/nanoserver/insider](https://hub.docker.com/_/microsoft-windows-nanoserver-insider)
- [mcr.microsoft.com/windows/iotcore/insider](https://hub.docker.com/_/microsoft-windows-iotcore-insider)
- [mcr.microsoft.com/windows/insider](https://hub.docker.com/_/microsoft-windows-insider)

Pour en savoir plus, lisez [Utiliser des conteneurs avec le programme Windows Insider](../deploy-containers/insider-overview.md).

### <a name="windows-server-core-vs-nanoserver"></a>Windows Server Core et Nano Server

`Windows Server Core` et `Nanoserver` sont les images de base les plus courantes à cibler. La principale différence entre ces images est que la surface d’API de Nano Server est beaucoup plus petite. PowerShell, WMI et la pile de maintenance Windows sont absents de l’image Nano Server.

Nano Server a été conçu afin de fournir juste assez de surface d’API pour exécuter des applications qui ont une dépendance de .NET Core ou d’autres infrastructures open source modernes. La moindre surface d’APi est compensée par le fait que l’empreinte sur disque de l’image Nano Server est sensiblement plus petite que celle des autres images de base Windows. N’oubliez pas que vous pouvez ajouter autant de couches que vous le souhaitez sur Nano Server. Pour obtenir un exemple, consultez le [fichier Dockerfile Nano Server .NET Core](https://github.com/dotnet/dotnet-docker/blob/master/src/sdk/2.1/nanoserver-1909/amd64/Dockerfile).
