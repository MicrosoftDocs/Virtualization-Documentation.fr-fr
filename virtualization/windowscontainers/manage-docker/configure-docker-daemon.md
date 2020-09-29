---
title: Configurer Docker dans Windows
description: Configurer Docker dans Windows
keywords: docker, conteneurs
author: PatrickLang
ms.author: jgerend
ms.date: 05/03/2019
ms.topic: overview
ms.assetid: 6885400c-5623-4cde-8012-f6a00019fafa
ms.openlocfilehash: d7d97963633fbcc24db4318f9456e9eee83855f2
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990622"
---
# <a name="docker-engine-on-windows"></a>Moteur Docker sur Windows

Le moteur et le client Docker ne sont pas inclus avec Windows, et doivent être installés et configurés individuellement. De plus, le moteur Docker accepte de nombreuses configurations personnalisées. Certains exemples incluent la configuration de la façon dont le démon accepte les requêtes entrantes, les options de mise en réseau par défaut et les paramètres de débogage/du journal. Sur Windows, ces configurations peuvent être spécifiées dans un fichier de configuration ou à l’aide du Gestionnaire de contrôle des services Windows. Ce document décrit en détail comment installer et configurer le moteur Docker, et fournit également quelques exemples de configurations fréquemment utilisées.

## <a name="install-docker"></a>Installer Docker

Docker est nécessaire pour utiliser les conteneurs Windows. Docker comprend le moteur Docker (dockerd.exe) et le client Docker (docker.exe). Le moyen le plus simple de tout installé se trouve dans le guide de démarrage rapide, qui vous permet de tout configurer et d'exécuter votre premier conteneur.

- [Installer Docker](../quick-start/set-up-environment.md)

Pour les installations par script, consultez [Utiliser un script pour installer Docker EE](https://docs.docker.com/install/windows/docker-ee/#use-a-script-to-install-docker-ee).

Avant d'utiliser Docker, vous devez installer les images de conteneur. Pour plus d’informations, consultez la [documentation relative à nos images de base de conteneur](../manage-containers/container-base-images.md).

## <a name="configure-docker-with-a-configuration-file"></a>Configurer Docker avec un fichier de configuration

La méthode privilégiée pour configurer le moteur Docker sur Windows consiste à utiliser un fichier de configuration. Ce fichier de configuration se trouve dans C:\ProgramData\Docker\config\daemon.json. Vous pouvez créer ce fichier s’il n’existe pas déjà.

>[!NOTE]
>Toutes les options de configuration de Docker disponibles ne s’appliquent pas à Docker sur Windows. L'exemple suivant illustre les options de configuration qui s'appliquent. Pour plus d'informations sur la configuration du moteur Docker, consultez [Fichier de configuration du démon Docker](https://docs.docker.com/engine/reference/commandline/dockerd/#/windows-configuration-file).

```json
{
    "authorization-plugins": [],
    "dns": [],
    "dns-opts": [],
    "dns-search": [],
    "exec-opts": [],
    "storage-driver": "",
    "storage-opts": [],
    "labels": [],
    "log-driver": "",
    "mtu": 0,
    "pidfile": "",
    "data-root": "",
    "cluster-store": "",
    "cluster-advertise": "",
    "debug": true,
    "hosts": [],
    "log-level": "",
    "tlsverify": true,
    "tlscacert": "",
    "tlscert": "",
    "tlskey": "",
    "group": "",
    "default-ulimits": {},
    "bridge": "",
    "fixed-cidr": "",
    "raw-logs": false,
    "registry-mirrors": [],
    "insecure-registries": [],
    "disable-legacy-registry": false
}
```

Seules les modifications de configuration souhaitées doivent être ajoutées au fichier de configuration. Ainsi, l'exemple suivant configure le moteur Docker pour accepter les connexions entrantes sur le port 2375. Toutes les autres options de configuration utiliseront les valeurs par défaut.

```json
{
    "hosts": ["tcp://0.0.0.0:2375"]
}
```

De même, l'exemple suivant configure le démon Docker pour conserver les images et les conteneurs dans un autre chemin. Si aucune valeur n’est spécifiée, la valeur par défaut est `c:\programdata\docker`.

```json
{   
    "data-root": "d:\\docker"
}
```

L'exemple suivant configure le démon Docker pour accepter uniquement les connexions sécurisées sur le port 2376.

```json
{
    "hosts": ["tcp://0.0.0.0:2376", "npipe://"],
    "tlsverify": true,
    "tlscacert": "C:\\ProgramData\\docker\\certs.d\\ca.pem",
    "tlscert": "C:\\ProgramData\\docker\\certs.d\\server-cert.pem",
    "tlskey": "C:\\ProgramData\\docker\\certs.d\\server-key.pem",
}
```

## <a name="configure-docker-on-the-docker-service"></a>Configurer Docker sur le service Docker

Le moteur Docker peut également être configuré en modifiant le service Docker avec `sc config`. Avec cette méthode, les indicateurs du moteur Docker sont définis directement sur le service de Docker. Exécutez la commande suivante dans une invite de commandes (cmd.exe, pas PowerShell) :

```cmd
sc config docker binpath= "\"C:\Program Files\docker\dockerd.exe\" --run-service -H tcp://0.0.0.0:2375"
```

>[!NOTE]
>Vous n’êtes pas tenu d’exécuter cette commande si votre fichier daemon.json contient déjà l’entrée `"hosts": ["tcp://0.0.0.0:2375"]`.

## <a name="common-configuration"></a>Configuration commune

Les exemples de fichiers de configuration suivants présentent des configurations courantes de Docker. Elles peuvent être combinées en un seul fichier de configuration.

### <a name="default-network-creation"></a>Création de réseau par défaut

Pour configurer le moteur Docker de sorte qu’un réseau NAT par défaut ne soit pas créé, utilisez la configuration suivante.

```json
{
    "bridge" : "none"
}
```

Pour plus d’informations, voir la rubrique indiquant comment [gérer les réseaux Docker](../container-networking/network-drivers-topologies.md).

### <a name="set-docker-security-group"></a>Définir un groupe de sécurité Docker

Lorsque vous êtes connecté à l’hôte Docker et exécutez des commandes Docker localement, ces commandes sont exécutées via un canal nommé. Par défaut, seuls les membres du groupe Administrateurs peuvent accéder au moteur Docker via le canal nommé. Pour spécifier un groupe de sécurité bénéficiant de cet accès, utilisez l’indicateur `group`.

```json
{
    "group" : "docker"
}
```

## <a name="proxy-configuration"></a>Configuration du proxy

Pour définir des informations de proxy pour `docker search` et `docker pull`, créez une variable d’environnement Windows nommée `HTTP_PROXY` ou `HTTPS_PROXY`, et une valeur des informations de proxy. Vous pouvez effectuer cette opération dans PowerShell en utilisant une commande semblable à celle-ci :

```powershell
[Environment]::SetEnvironmentVariable("HTTP_PROXY", "http://username:password@proxy:port/", [EnvironmentVariableTarget]::Machine)
```

Une fois que la variable a été définie, redémarrez le service Docker.

```powershell
Restart-Service docker
```

Pour plus d’informations, consultez [Fichier de configuration Windows sur Docker.com](https://docs.docker.com/engine/reference/commandline/dockerd/#/windows-configuration-file).

## <a name="how-to-uninstall-docker"></a>Désinstaller Docker

Cette section vous explique comment désinstaller Docker et effectuer un nettoyage complet des composants système Docker de votre système Windows 10 ou Windows Server 2016.

>[!NOTE]
>Vous devez exécuter toutes les commandes de ces instructions à partir d’une session PowerShell avec élévation de privilèges.

### <a name="prepare-your-system-for-dockers-removal"></a>Préparer votre système à la suppression de Docker

Avant de désinstaller Docker, assurez-vous qu’aucun conteneur n’est en cours d’exécution sur votre système.

Exécutez les cmdlets suivantes pour vérifier les conteneurs en cours d’exécution :

```powershell
# Leave swarm mode (this will automatically stop and remove services and overlay networks)
docker swarm leave --force

# Stop all running containers
docker ps --quiet | ForEach-Object {docker stop $_}
```

Il est également recommandé de supprimer tous les conteneurs, images de conteneur, réseaux et volumes de votre système avant la suppression de Docker. Pour ce faire, exécutez la cmdlet suivante :

```powershell
docker system prune --volumes --all
```

### <a name="uninstall-docker"></a>Désinstallation de Docker

Vous devez ensuite désinstaller Docker.

Pour désinstaller Docker sur Windows 10

- Accédez à **Paramètres** > **Applications** sur votre ordinateur Windows 10
- Sous **Applications et fonctionnalités**, recherchez **Docker pour Windows**
- Accédez à **Docker pour Windows** > **Désinstaller**

Pour désinstaller Docker sur Windows Server 2016 :

À partir d’une session PowerShell avec élévation de privilèges, utilisez les cmdlets **Uninstall-Package** et **Uninstall-Module** pour supprimer le module Docker et le fournisseur de gestion de packages correspondant de votre système, comme illustré dans l'exemple suivant :

```powershell
Uninstall-Package -Name docker -ProviderName DockerMsftProvider
Uninstall-Module -Name DockerMsftProvider
```

>[!TIP]
>Vous pouvez rechercher le fournisseur de packages que vous avez utilisé pour installer Docker avec `PS C:\> Get-PackageProvider -Name *Docker*`

### <a name="clean-up-docker-data-and-system-components"></a>Nettoyer les données et les composants système Docker

Après avoir désinstallé Docker, vous devez supprimer les réseaux Docker par défaut afin de supprimer leur configuration de votre système. Pour ce faire, exécutez la cmdlet suivante :

```powershell
Get-HNSNetwork | Remove-HNSNetwork
```

Pour supprimer les réseaux Docker par défaut sur Windows Server 2016.
```powershell
Get-ContainerNetwork | Remove-ContainerNetwork
```

Exécutez la cmdlet suivante pour supprimer les données programme Docker de votre système :

```powershell
Remove-Item "C:\ProgramData\Docker" -Recurse
```

Vous pouvez également supprimer les fonctionnalités facultatives Windows associés aux conteneurs/Docker sur Windows.

Cela inclut la fonctionnalité « Conteneurs », qui est automatiquement activée sur Windows 10 ou Windows Server 2016 lorsque Docker est installé. Cela peut également inclure la fonctionnalité « Hyper-V », qui est automatiquement activée sur Windows 10 lorsque Docker est installé, mais qui doit être activée explicitement sur Windows Server 2016.

>[!IMPORTANT]
>[La fonctionnalité Hyper-V](/virtualization/hyper-v-on-windows/about/) est une fonctionnalité de virtualisation générale bien plus puissante que les conteneurs. Avant de désactiver la fonctionnalité Hyper-V, assurez-vous qu'aucun autre composant virtualisé de votre système ne requiert Hyper-V.

Pour supprimer les fonctionnalités Windows sur Windows 10 :

- Sélectionnez **Panneau de configuration** > **Programmes** > **Programmes et fonctionnalités** > **Activer ou désactiver des fonctionnalités Windows**.
- Recherchez le nom de la ou des fonctionnalités que vous souhaitez désactiver, en l’occurrence **Conteneurs** et (éventuellement) **Hyper-V**.
- Décochez la case en regard du nom de la fonctionnalité que vous souhaitez désactiver.
- Sélectionnez **« OK »** .

Pour supprimer les fonctionnalités Windows sur Windows Server 2016 :

À partir d’une session PowerShell avec élévation de privilèges, exécutez les cmdlets suivantes pour désactiver les fonctionnalités **Conteneurs** et (éventuellement) **Hyper-V** de votre système :

```powershell
Remove-WindowsFeature Containers
Remove-WindowsFeature Hyper-V
```

### <a name="reboot-your-system"></a>Redémarrer votre système

Pour terminer la désinstallation et le nettoyage, exécutez la cmdlet suivante à partir d’une session PowerShell avec élévation de privilèges afin de redémarrer votre système :

```powershell
Restart-Computer -Force
```