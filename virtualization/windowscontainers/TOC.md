# [Documentation Conteneurs sur Windows](index.yml) 

# Vue d’ensemble
## [À propos des conteneurs Windows](about/index.md)
## [Conteneurs ou machines virtuelles](about/containers-vs-vm.md)
## [Configuration système requise](deploy-containers/system-requirements.md)
## [Questions fréquentes (FAQ)](about/faq.md)

# Démarrer
## [Configurer votre environnement](quick-start/set-up-environment.md)
## [Exécuter votre premier conteneur](quick-start/run-your-first-container.md)
## [Conteneuriser un exemple d’application](quick-start/building-sample-app.md)
## [Lift-and-shift sur des conteneurs](quick-start/lift-shift-to-containers.md)

# Didacticiels
## Créer un conteneur Windows
### [Écrire un Dockerfile](manage-docker/manage-windows-dockerfile.md)
### [Optimiser un Dockerfile](manage-docker/optimize-windows-dockerfile.md)
## Exécuter sur Azure Kubernetes Service sur Azure Stack HCI
### [Créer un cluster Kubernetes sur Azure Stack HCI](/azure-stack/aks-hci/create-kubernetes-cluster) 
## Exécuter sur Azure Kubernetes Services
### [Créer un cluster de conteneurs Windows sur AKS](/azure/aks/windows-container-cli)
### [Limitations actuelles](/azure/aks/windows-node-limitations)
## Exécuter sur Service Fabric
### [Déployer votre premier conteneur](/azure/service-fabric/service-fabric-quickstart-containers)
### [Déployer une application .NET dans un conteneur Windows](/azure/service-fabric/service-fabric-host-app-in-a-container)
## Exécuter sur Azure App Service
### [Démarrage rapide d’Azure App Service](/azure/app-service/app-service-web-get-started-windows-container)
### [Migrer une application ASP.NET avec des conteneurs Windows et Azure App Service](/azure/app-service/app-service-web-tutorial-windows-containers-custom-fonts)
## Utiliser des conteneurs Linux sur Windows
### [Vue d’ensemble](deploy-containers/linux-containers.md)
### [Exécuter votre premier conteneur Linux](quick-start/quick-start-windows-10-linux.md)
## Utiliser des conteneurs avec le programme Windows Insider
### [Vue d’ensemble](deploy-containers/insider-overview.md)
## Conteneuriser des applications avec des services d’impression
### [Services d’impression](deploy-containers/print-spooler.md)
## Gérer les conteneurs avec Windows Admin Center
### [Vue d’ensemble de l’extension Conteneurs](WAC-tooling/WAC-extension.md)
### [Gérer des images conteneur](WAC-tooling/WAC-Manage.md)
### [Exécuter de nouveaux conteneurs](WAC-tooling/WAC-Containers.md)
### [Créer des images conteneur](WAC-tooling/WAC-Images.md)
### [Gérer Azure Container Registry](WAC-tooling/WAC-ACR.md)
### [Gérer Azure Container Instance](WAC-tooling/WAC-ACI.md)

# Concepts
## Éléments principaux des conteneurs Windows
### [Images conteneur de base](manage-containers/container-base-images.md)
### [Modes d’isolation](manage-containers/hyperv-container.md)
### [Compatibilité des versions](deploy-containers/version-compatibility.md)
### [Gérer les conteneurs](deploy-containers/update-containers.md)
### [Contrôles des ressources](manage-containers/resource-controls.md)
## Docker
### [Moteur Docker sur Windows](manage-docker/configure-docker-daemon.md)
### [Gestion à distance d’un hôte Windows Docker](management/manage_remotehost.md)
## Orchestration de conteneurs
### [Vue d’ensemble](about/overview-container-orchestrators.md)
### Kubernetes sur Windows
#### [Kubernetes sur Windows](kubernetes/getting-started-kubernetes-windows.md)
#### [Dépannage](kubernetes/common-problems.md)
#### [Compiler des fichiers binaires Kubernetes](kubernetes/compiling-kubernetes-binaries.md)
### Service Fabric
#### [Service Fabric et conteneurs](/azure/service-fabric/service-fabric-containers-overview)
#### [Gouvernance des ressources](/azure/service-fabric/service-fabric-resource-governance)
### Docker Swarm
#### [Mode Swarm](manage-containers/swarm-mode.md)
## Sécurité
### Group Managed Service Accounts
#### [Créer un gMSA](manage-containers/manage-serviceaccounts.md)
#### [Configurer votre application pour utiliser un gMSA](manage-containers/gmsa-configure-app.md)
#### [Exécuter un conteneur avec un gMSA](manage-containers/gmsa-run-container.md)
#### [Orchestrer des conteneurs avec un gMSA](manage-containers/gmsa-orchestrate-containers.md)
#### [Résoudre les problèmes de gMSA](manage-containers/gmsa-troubleshooting.md)
## Mise en réseau
### [Vue d’ensemble](container-networking/architecture.md)
### [Topologies et pilotes réseau](container-networking/network-drivers-topologies.md)
### [Isolement et sécurité réseau](container-networking/network-isolation-security.md)
### [Configurer les options réseau avancées](container-networking/advanced.md)
## Stockage
### [Vue d’ensemble](manage-containers/container-storage.md)
### [Stockage persistant](manage-containers/persistent-storage.md)
## Périphériques
### [Périphériques matériels](deploy-containers/hardware-devices-in-containers.md)
### [Accélération GPU](deploy-containers/gpu-acceleration.md)

# Référence
## [Cycles de vie de la maintenance des images de base](deploy-containers/base-image-lifecycle.md)
## [Événements](deploy-containers/events.md)
## [Optimisation de la protection antivirus](/windows-hardware/drivers/ifs/anti-virus-optimization-for-windows-containers)
## [Outils de plateforme de conteneurs](deploy-containers/containerd.md)
## [CLUF de l’image système d’exploitation de conteneur](./images-eula.md)

# Ressources
## [Problèmes connus](manage-containers/known-issues.md)
## [Exemples de conteneurs](samples.md)
## [Feuille de route des conteneurs Windows Server](https://github.com/microsoft/Windows-Containers/projects/1)
## [Résolution des problèmes](troubleshooting.md)
## [Forum sur les conteneurs](https://social.msdn.microsoft.com/Forums/home?forum=windowscontainers)
## [Vidéos et blogs de la communauté](communitylinks.md)