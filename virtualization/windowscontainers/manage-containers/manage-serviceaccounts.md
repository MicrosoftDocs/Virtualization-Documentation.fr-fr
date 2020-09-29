---
title: Créer des comptes de service administré de groupe pour des conteneurs Windows
description: Comment créer des comptes de service administré de groupe pour des conteneurs Windows.
keywords: docker, conteneurs, Active Directory, compte de service administré de groupe, comptes de service administré de groupe
author: rpsqrd
ms.author: jgerend
ms.date: 01/03/2019
ms.topic: how-to
ms.assetid: 9e06ad3a-0783-476b-b85c-faff7234809c
ms.openlocfilehash: b8a224a34d5cb666c55190e30b7002e2483cf89c
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990912"
---
# <a name="create-gmsas-for-windows-containers"></a>Créer des comptes de service administré de groupe pour des conteneurs Windows

Les réseaux Windows utilisent couramment Active Directory (AD) pour faciliter l’authentification et l’autorisation entre utilisateurs, ordinateurs et autres ressources réseau. Les développeurs d’applications d’entreprise conçoivent souvent leurs applications pour qu’elles soient intégrées avec AD et s’exécutent sur des serveurs joints à un domaine afin de tirer parti de l’authentification Windows intégrée, ce qui facilite pour les utilisateurs et d’autres services la connexion automatique et transparente à l’application avec leurs identités.

Si les conteneurs Windows ne peuvent pas être joints à un domaine, ils peuvent toujours utiliser des identités de domaine Active Directory pour divers scénarios d’authentification.

Pour ce faire, vous pouvez configurer un conteneur Windows pour qu’il s’exécute avec un [compte de service administré de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview), qui est un type spécial de compte de service introduit dans Windows Server 2012, conçu pour permettre à plusieurs ordinateurs de partager une identité sans avoir besoin de connaître son mot de passe.

Quand vous exécutez un conteneur avec un compte de service administré de groupe, l’hôte du conteneur récupère le mot de passe du compte de service administré de groupe à partir d’un contrôleur de domaine Active Directory, et le transmet à l’instance de conteneur. Le conteneur utilise les informations d’identification du compte de service administré de groupe chaque fois que son compte d’ordinateur (système) a besoin d’accéder aux ressources réseau.

Cet article explique comment commencer à utiliser des comptes de service administré de groupe Active Directory avec des conteneurs Windows.

## <a name="prerequisites"></a>Prérequis

Pour exécuter un conteneur Windows avec un compte de service administré de groupe, vous aurez besoin des éléments suivants :

- Un domaine Active Directory avec au moins un contrôleur de domaine exécutant Windows Server 2012 ou version ultérieure. Aucune exigence de niveau fonctionnel de forêt ou de domaine n’est liée à l’utilisation de comptes de service administré de groupe, mais les mots de passe de ceux-ci ne peuvent être distribués que par des contrôleurs de domaine exécutant Windows Server 2012 ou version ultérieure. Pour plus d’informations, consultez [Exigences Active Directory pour les comptes de service administré de groupe](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts#BKMK_gMSA_Req).
- L’autorisation de créer un compte de service administré de groupe. Pour créer un compte de service administré de groupe, vous devez être administrateur de domaine ou utiliser un compte auquel est déléguée l’autorisation *Créer des objets msDS-GroupManagedServiceAccount*.
- Un accès à Internet pour télécharger le module PowerShell CredentialSpec. Si vous travaillez dans un environnement déconnecté, vous pouvez [enregistrer le module](/powershell/module/powershellget/save-module?view=powershell-5.1) sur un ordinateur disposant d’un accès à Internet et le copier sur votre ordinateur de développement ou hôte de conteneur.

## <a name="one-time-preparation-of-active-directory"></a>Préparation unique d’Active Directory

Si vous n’avez pas encore créé de compte de service administré de groupe dans votre domaine, vous devez générer la clé racine du service de distribution de clés. Le service de distribution de clés est chargé de la création, de la rotation et de la publication du mot de passe de compte de service administré de groupe pour les hôtes autorisés. Quand un hôte de conteneur doit utiliser le compte de service administré de groupe pour exécuter un conteneur, il contacte le service de distribution de clés pour récupérer le mot de passe actuel.

Pour vérifier si la clé racine du service de distribution de clés a déjà été créée, exécutez la cmdlet PowerShell suivante en tant qu’administrateur de domaine sur un contrôleur de domaine ou un membre de domaine sur lequel les outils AD PowerShell sont installés :

```powershell
Get-KdsRootKey
```

Si la commande retourne un ID de clé, vous êtes prêt et pouvez passer directement à la section [Créer un compte de service administré de groupe](#create-a-group-managed-service-account). Sinon, poursuivez pour créer la clé racine du service de distribution de clés.

Dans un environnement de production ou de test comptant plusieurs contrôleurs de domaine, exécutez la cmdlet suivante dans PowerShell en tant qu’administrateur de domaine pour créer la clé racine du service de distribution de clés.

```powershell
# For production environments
Add-KdsRootKey -EffectiveImmediately
```

Bien que la commande implique que la clé prendra effet immédiatement, vous devrez attendre 10 heures avant que la clé racine du service de distribution de clés soit répliquée et disponible pour utilisation sur tous les contrôleurs de domaine.

Si vous n’avez qu’un seul contrôleur de domaine dans votre domaine, vous pouvez accélérer le processus en définissant l’horodatage de prise d’effet de la clé pour 10 heures plut tôt.

>[!IMPORTANT]
>N’utilisez pas cette technique dans un environnement de production.

```powershell
# For single-DC test environments ONLY
Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
```

## <a name="create-a-group-managed-service-account"></a>Créer un compte de service administré de groupe

Chaque conteneur utilisant l’authentification Windows intégrée a besoin d’au moins un compte de service administré de groupe. Le compte de service administré de groupe principal est utilisé chaque fois que des applications s’exécutant en tant que service système ou réseau accèdent à des ressources sur le réseau. Le nom du compte de service administré de groupe devient le nom du conteneur sur le réseau, quel que soit le nom d’hôte attribué au conteneur. Vous pouvez également configurer des conteneurs avec des comptes de service administré de groupe supplémentaires si vous souhaitez exécuter un service ou une application dans le conteneur sous une identité différente de celle du compte d’ordinateur de conteneur.

Lorsque vous créez un compte de service administré de groupe, vous créez également une identité partagée que vous pouvez utiliser simultanément sur de nombreux ordinateurs différents. L’accès au mot de passe du compte de service administré de groupe est protégé par une liste de contrôle d’accès Active Directory. Nous vous recommandons de créer un groupe de sécurité pour chaque compte de service administré de groupe, et d’ajouter les hôtes de conteneur appropriés au groupe de sécurité pour limiter l’accès au mot de passe.

Enfin, étant donné que les conteneurs n’inscrivent pas automatiquement de noms de principal de service, vous devez créer manuellement au moins un nom de principal de service d’hôte pour votre compte de service administré de groupe.

En règle générale, l’hôte ou le nom de principal de service HTTP sont inscrits sous le même nom que le compte de service administré de groupe, mais il se peut que vous deviez utiliser un nom de service différent si les clients accèdent à l’application en conteneur derrière un équilibreur de charge ou un nom DNS différent du nom de compte de service administré de groupe.

Par exemple, si le compte de service administré de groupe est nommé « WebApp01 », mais que vos utilisateurs accèdent au site via la page `mysite.contoso.com`, vous devez inscrire un nom de principal de service `http/mysite.contoso.com` sur le compte de service administré de groupe.

Certaines applications peuvent nécessiter des noms de principal de service supplémentaires pour leurs protocoles spécifiques. Par exemple, SQL Server requiert le nom de principal de service `MSSQLSvc/hostname`.

Le tableau suivant répertorie les attributs requis pour la création d’un compte de service administré de groupe.

|Propriété de compte de service administré de groupe | Valeur requise | Exemple |
|--------------|----------------|--------|
|Nom | N’importe quel nom de compte valide. | `WebApp01` |
|DNSHostName | Nom de domaine ajouté au nom du compte. | `WebApp01.contoso.com` |
|ServicePrincipalNames | Définissez au moins le nom de principal de service de l’hôte, puis ajoutez d’autres protocoles si nécessaire. | `'host/WebApp01', 'host/WebApp01.contoso.com'` |
|PrincipalsAllowedToRetrieveManagedPassword | Groupe de sécurité contenant vos hôtes de conteneur. | `WebApp01Hosts` |

Une fois que vous avez choisi le nom de votre compte de service administré de groupe, exécutez les cmdlets suivantes dans PowerShell pour créer le groupe de sécurité et le compte de service administré de groupe.

> [!TIP]
> Vous devez utiliser un compte appartenant au groupe de sécurité **Admins du domaine** ou auquel est déléguée l’autorisation **Créer des objets msDS-GroupManagedServiceAccount** nécessaire pour exécuter les commandes suivantes.
> La cmdlet [New-ADServiceAccount](/powershell/module/addsadministration/new-adserviceaccount?view=win10-ps) fait partie des outils PowerShell Active Directory appelés [Outils d’administration de serveur distant](/troubleshoot/windows-server/system-management-components/remote-server-administration-tools).

```powershell
# Replace 'WebApp01' and 'contoso.com' with your own gMSA and domain names, respectively

# To install the AD module on Windows Server, run Install-WindowsFeature RSAT-AD-PowerShell
# To install the AD module on Windows 10 version 1809 or later, run Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
# To install the AD module on older versions of Windows 10, see https://aka.ms/rsat

# Create the security group
New-ADGroup -Name "WebApp01 Authorized Hosts" -SamAccountName "WebApp01Hosts" -GroupScope DomainLocal

# Create the gMSA
New-ADServiceAccount -Name "WebApp01" -DnsHostName "WebApp01.contoso.com" -ServicePrincipalNames "host/WebApp01", "host/WebApp01.contoso.com" -PrincipalsAllowedToRetrieveManagedPassword "WebApp01Hosts"

# Add your container hosts to the security group
Add-ADGroupMember -Identity "WebApp01Hosts" -Members "ContainerHost01$", "ContainerHost02$", "ContainerHost03$"
```

Nous vous recommandons de créer des comptes de service administré de groupe distincts pour vos environnements de développement, de test et de production.

## <a name="prepare-your-container-host"></a>Préparer votre hôte de conteneur

Chaque hôte de conteneur appelé à exécuter un conteneur Windows avec un compte de service administré de groupe doit être joint à un domaine et disposer de l’accès nécessaire pour récupérer le mot de passe du compte de service administré de groupe.

1. Joignez votre ordinateur à un domaine Active Directory.
2. Assurez-vous que votre hôte appartient au groupe de sécurité contrôlant l’accès au mot de passe du compte de service administré de groupe.
3. Redémarrez l’ordinateur pour qu’il récupère sa nouvelle appartenance de groupe.
4. Configurez [Docker Desktop pour Windows 10](https://docs.docker.com/docker-for-windows/install/) ou [Docker pour Windows Server](https://docs.docker.com/install/windows/docker-ee/).
5. (Recommandé) Vérifiez que l’hôte peut utiliser le compte de service administré de groupe en exécutant la commande [test-ADServiceAccount](/powershell/module/activedirectory/test-adserviceaccount). Si la commande retourne **False**, suivez les [Instructions de résolution des problèmes](gmsa-troubleshooting.md#make-sure-the-host-can-use-the-gmsa).

    ```powershell
    # To install the AD module on Windows Server, run Install-WindowsFeature RSAT-AD-PowerShell
    # To install the AD module on Windows 10 version 1809 or later, run Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
    # To install the AD module on older versions of Windows 10, see https://aka.ms/rsat

    Test-ADServiceAccount WebApp01
    ```

## <a name="create-a-credential-spec"></a>Créer une spécification d’informations d’identification

Un fichier de spécification d’informations d’identification est un document JSON contenant des métadonnées relatives aux comptes de service administré de groupe que vous souhaitez qu’un conteneur utilise. En gardant la configuration d’identité séparée de l’image de conteneur, vous pouvez changer le compte de service administré de groupe que le conteneur utilise en remplaçant simplement le fichier de spécification d’informations d’identification. Aucun changement de code n’est nécessaire.

Le fichier de spécification d’informations d’identification est créé à l’aide du [module PowerShell CredentialSpec](https://aka.ms/credspec) sur un hôte de conteneur joint à un domaine.
Une fois que vous avez créé le fichier, vous pouvez le copier vers d’autres hôtes de conteneur ou votre orchestrateur de conteneurs.
Le fichier de spécification d’informations d’identification ne contient pas de secret tel que le mot de passe du compte de service administré de groupe, car l’hôte de conteneur récupère le compte de service administré de groupe au nom du conteneur.

Docker s’attend à trouver le fichier de spécification d’informations d’identification sous le sous-répertoire **CredentialSpecs** du répertoire de données Docker. Dans une installation par défaut, vous trouverez ce dossier à l’adresse `C:\ProgramData\Docker\CredentialSpecs`.

Pour créer un fichier de spécification d’informations d’identification sur votre hôte de conteneur :

1. Installer les outils PowerShell Active Directory nommés Outils d’administration de serveur distant
    - Pour Windows Server, exécutez la commande **install-WindowsFeature RSAT-AD-PowerShell**.
    - Pour Windows 10, version 1809 ou ultérieure, exécutez la commande **Add-WindowsCapability -Online -Name ’Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0’** .
    - Pour les versions antérieures de Windows 10, consultez la page <https://aka.ms/rsat>.
2. Exécutez la cmdlet suivante pour installer la dernière version du [module PowerShell CredentialSpec](https://aka.ms/credspec) :

    ```powershell
    Install-Module CredentialSpec
    ```

    Si vous n’avez pas accès à Internet sur votre hôte de conteneur, exécutez la commande `Save-Module CredentialSpec` sur un ordinateur connecté à Internet, et copiez le dossier du module vers `C:\Program Files\WindowsPowerShell\Modules` ou un autre emplacement dans `$env:PSModulePath` sur l’hôte de conteneur.

3. Exécutez la cmdlet suivante pour créer le fichier de spécification d’informations d’identification :

    ```powershell
    New-CredentialSpec -AccountName WebApp01
    ```

    Par défaut, la cmdlet crée une spécification d’informations d’identification utilisant le nom de compte de service administré de groupe fourni comme compte d’ordinateur pour le conteneur. Le fichier est enregistré dans le répertoire CredentialSpecs de Docker en utilisant comme nom de fichier le domaine du compte de service administré de groupe et le nom du compte.

    Si vous souhaitez enregistrer le fichier dans un autre répertoire, utilisez le paramètre `-Path` :

    ```powershell
    New-CredentialSpec -AccountName WebApp01 -Path "C:\MyFolder\WebApp01_CredSpec.json"
    ```

    Vous pouvez également créer une spécification d’informations d’identification qui inclut des comptes de service administré de groupe supplémentaires si vous exécutez un service ou un processus en tant que compte de service administré de groupe secondaire dans le conteneur. Pour ce faire, utilisez le paramètre `-AdditionalAccounts` :

    ```powershell
    New-CredentialSpec -AccountName WebApp01 -AdditionalAccounts LogAgentSvc, OtherSvc
    ```

    Pour obtenir la liste complète des paramètres pris en charge, exécutez la commande `Get-Help New-CredentialSpec -Full`.

4. Pour afficher la liste de toutes les spécifications d’informations d’identification avec leur chemin d’accès complet, utilisez la cmdlet suivante :

    ```powershell
    Get-CredentialSpec
    ```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré votre compte de service administré de groupe, vous pouvez l’utiliser pour :

- [Configurer des applications](gmsa-configure-app.md)
- [Exécuter des conteneurs](gmsa-run-container.md)
- [Orchestrer des conteneurs](gmsa-orchestrate-containers.md)

Si vous rencontrez des problèmes lors de la configuration, vous trouverez peut-être des solutions dans notre [Guide de résolution des problèmes](gmsa-troubleshooting.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- Pour en savoir plus sur les comptes de service administré de groupe, consultez la [Vue d’ensemble des comptes de service administré de groupe](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).
- Pour une démonstration vidéo, regardez notre [démonstration enregistrée dans le cadre d’Ignite 2016](https://youtu.be/cZHPz80I-3s?t=2672).