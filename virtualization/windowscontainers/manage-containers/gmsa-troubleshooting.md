---
title: Résoudre des problèmes de comptes de service administré de groupe pour des conteneurs Windows
description: Comment résoudre des problèmes de comptes de service administré de groupe pour des conteneurs Windows.
keywords: docker, conteneurs, Active Directory, compte de service administré de groupe, comptes de service administré de groupe, résolution de problèmes, résoudre des problèmes
author: rpsqrd
ms.author: jgerend
ms.date: 10/03/2019
ms.topic: troubleshooting
ms.assetid: 9e06ad3a-0783-476b-b85c-faff7234809c
ms.openlocfilehash: bffbf326c9a5a7d83625dc4421f8814425a8c680
ms.sourcegitcommit: 160405a16d127892b6e2897efa95680f29f0496a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90990902"
---
# <a name="troubleshoot-gmsas-for-windows-containers"></a>Résoudre des problèmes de comptes de service administré de groupe pour des conteneurs Windows

## <a name="known-issues"></a>Problèmes connus

### <a name="container-hostname-must-match-the-gmsa-name-for-windows-server-2016-and-windows-10-versions-1709-and-1803"></a>Le nom d’hôte du conteneur doit correspondre au nom du compte de service administré de groupe pour Windows Server 2016 et Windows 10, versions 1709 et 1803

Si vous exécutez Windows Server 2016, version 1709 ou 1803, le nom d’hôte de votre conteneur doit correspondre au nom de votre compte SAM de service administré de groupe.

Quand le nom d’hôte ne correspond pas au nom du compte de service administré de groupe, les demandes d’authentification NTLM entrantes et la traduction de nom/SID (utilisée par de nombreuses bibliothèques, comme le fournisseur de rôle d’appartenance ASP.NET) échouent. Kerberos continue à fonctionner normalement, même si le nom d’hôte et le nom de compte de service administré de groupe ne correspondent pas.

Cette limitation a été corrigée dans Windows Server 2019, où le conteneur utilise désormais toujours son nom de compte de service administré de groupe sur le réseau, quel que soit le nom d’hôte attribué.

### <a name="using-a-gmsa-with-more-than-one-container-simultaneously-leads-to-intermittent-failures-on-windows-server-2016-and-windows-10-versions-1709-and-1803"></a>L’utilisation simultanée d’un compte de service administré de groupe avec plusieurs conteneurs provoque des défaillances intermittentes sur Windows Server 2016 et Windows 10, versions 1709 et 1803

Étant donné que tous les conteneurs sont tenus d’utiliser le même nom d’hôte, un deuxième problème affecte les versions de Windows antérieures à Windows Server 2019 et Windows 10, version 1809. Si plusieurs conteneurs reçoivent les mêmes identité et nom d’hôte, une condition de concurrence peut se produire quand deux conteneurs communiquent simultanément avec le même contrôleur de domaine. Si un autre conteneur communique avec le même contrôleur de domaine, il annule la communication avec les conteneurs précédents utilisant la même identité. Cela peut entraîner des échecs d’authentification intermittents et parfois être observé comme échec d’approbation lorsque vous exécutez `nltest /sc_verify:contoso.com` à l’intérieur du conteneur.

Nous avons modifié le comportement de Windows Server 2019 pour séparer l’identité du conteneur du nom de l’ordinateur, ce qui permet à plusieurs conteneurs d’utiliser simultanément le même compte de service administré de groupe.

### <a name="you-cant-use-gmsas-with-hyper-v-isolated-containers-on-windows-10-versions-1703-1709-and-1803"></a>Vous ne pouvez pas utiliser de comptes de service administré de groupe avec des conteneurs isolés Hyper-V sur Windows 10, versions 1703, 1709 et 1803

L’initialisation du conteneur se bloque ou échoue quand vous tentez d’utiliser un compte de service administré de groupe avec un conteneur isolé Hyper-V sur Windows 10 et Windows Server, versions 1703, 1709 et 1803.

Ce bogue a été corrigé dans Windows Server 2019 et Windows 10, version 1809. Vous pouvez également exécuter des conteneurs isolés Hyper-V avec des comptes de service administré de groupe sur Windows Server 2016 et Windows 10, version 1607.

## <a name="general-troubleshooting-guidance"></a>Conseils généraux pour la résolution des problèmes

Si vous rencontrez des erreurs lors de l’exécution d’un conteneur avec un compte de service administré de groupe, les instructions suivantes peuvent vous aider à identifier la cause racine.

### <a name="make-sure-the-host-can-use-the-gmsa"></a>S’assurer que l’hôte peut utiliser le compte de service administré de groupe

1. Vérifiez que l’hôte est joint au domaine et peut atteindre le contrôleur de domaine.
2. Installez les outils PowerShell Active Directory à partir des Outils d’administration de serveur distant, puis exécutez la cmdlet [test-ADServiceAccount](/powershell/module/activedirectory/test-adserviceaccount) pour voir si l’ordinateur a accès à la récupération du compte de service administré de groupe. Si la cmdlet retourne **False**, l’ordinateur n’a pas accès au mot de passe du compte de service administré de groupe.

    ```powershell
    # To install the AD module on Windows Server, run Install-WindowsFeature RSAT-AD-PowerShell
    # To install the AD module on Windows 10 version 1809 or later, run Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
    # To install the AD module on older versions of Windows 10, see https://aka.ms/rsat

    Test-ADServiceAccount WebApp01
    ```

3. Si la cmdlet **test-ADServiceAccount** retourne **False**, vérifiez que l’hôte appartient à un groupe de sécurité qui peut accéder au mot de passe du compte de service administré de groupe.

    ```powershell
    # Get the current computer's group membership
    Get-ADComputer $env:computername | Get-ADPrincipalGroupMembership | Select-Object DistinguishedName

    # Get the groups allowed to retrieve the gMSA password
    # Change "WebApp01" for your own gMSA name
    (Get-ADServiceAccount WebApp01 -Properties PrincipalsAllowedToRetrieveManagedPassword).PrincipalsAllowedToRetrieveManagedPassword
    ```

4. Si votre hôte appartient à un groupe de sécurité autorisé à récupérer le mot de passe du compte de service administré de groupe mais que la cmdlet **test-ADServiceAccount** continue d’échouer, il se peut que vous deviez redémarrer votre ordinateur pour obtenir un nouveau ticket reflétant ses appartenances de groupe actuelles.

#### <a name="check-the-credential-spec-file"></a>Vérifier le fichier de spécification d’informations d’identification

1. Exécutez la cmdlet **Get-CredentialSpec** à partir du [module PowerShell CredentialSpec](https://aka.ms/credspec) pour localiser toutes les spécifications d’informations d’identification sur l’ordinateur. Les spécifications d’informations d’identification doivent être stockées dans le répertoire « CredentialSpecs » sous le répertoire racine de Docker. Vous pouvez trouver le répertoire racine de Docker en exécutant la cmdlet **docker info -f "{{.DockerRootDir}}"** .
2. Ouvrez le fichier CredentialSpec et assurez-vous que les champs suivants sont remplis correctement :
    - **Sid** : SID de votre compte de service administré de groupe.
    - **MachineAccountName** : nom du compte SAM de service administré de groupe (ne pas inclure le nom de domaine complet ou le signe dollar).
    - **DnsTreeName** : nom de domaine complet (FQDN) de votre forêt Active Directory.
    - **DnsName** : nom de domaine complet du domaine auquel appartient le compte de service administré de groupe.
    - **NetBiosName** : nom NETBIOS du domaine auquel appartient le compte de service administré de groupe.
    - **GroupManagedServiceAccounts/Name** : nom du compte SAM de service administré de groupe (ne pas inclure le nom de domaine complet ou le signe dollar).
    - **GroupManagedServiceAccounts/étendue** : une entrée pour le nom de domaine complet du domaine et une autre pour le NETBIOS.

    Votre entrée doit ressembler à l’exemple suivant d’une spécification d’informations d’identification complète :

    ```json
    {
        "CmsPlugins": [
            "ActiveDirectory"
        ],
        "DomainJoinConfig": {
            "Sid": "S-1-5-21-702590844-1001920913-2680819671",
            "MachineAccountName": "webapp01",
            "Guid": "56d9b66c-d746-4f87-bd26-26760cfdca2e",
            "DnsTreeName": "contoso.com",
            "DnsName": "contoso.com",
            "NetBiosName": "CONTOSO"
        },
        "ActiveDirectoryConfig": {
            "GroupManagedServiceAccounts": [
                {
                    "Name": "webapp01",
                    "Scope": "contoso.com"
                },
                {
                    "Name": "webapp01",
                    "Scope": "CONTOSO"
                }
            ]
        }
    }
    ```

3. Vérifiez que le chemin d’accès au fichier de spécification d’informations d’identification est correct pour votre solution d’orchestration. Si vous utilisez Docker, assurez-vous que la commande d’exécution de conteneur contient `--security-opt="credentialspec=file://NAME.json"`, où « NAME.JSON » est remplacé par la sortie de nom obtenue par la commande **Get-CredentialSpec**. Le nom est un nom de fichier plat relatif au dossier CredentialSpecs sous le répertoire racine de Docker.

### <a name="check-the-firewall-configuration"></a>Définir la configuration du pare-feu

Si vous utilisez une stratégie de pare-feu stricte sur le réseau du conteneur ou de l’hôte, celle-ci peut bloquer les connexions requises au contrôleur de domaine Active Directory ou au serveur DNS.

| Protocole et port | Objectif |
|-------------------|---------|
| TCP et UDP 53 | DNS |
| TCP et UDP 88 | Kerberos |
| TCP 139 | Accès réseau (Netlogon) |
| TCP et UDP 389 | LDAP |
| TCP 636 | SSL LDAP |

Vous devrez peut-être autoriser l’accès à des ports supplémentaires en fonction du type de trafic que votre conteneur envoie à un contrôleur de domaine.
Pour obtenir la liste complète des ports utilisés par Active Directory, consultez [Configuration de ports requise pour Active Directory et Active Directory Domain Services](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772723(v=ws.10)#communication-to-domain-controllers).

### <a name="check-the-container"></a>Vérifier le conteneur

1. Si vous exécutez une version de Windows antérieure à Windows Server 2019 ou Windows 10, version 1809, le nom d’hôte de votre conteneur doit correspondre au nom de compte de service administré de groupe. Assurez-vous que le paramètre `--hostname` correspond au nom abrégé du compte de service administré de groupe (pas de composant de domaine ; par exemple, « webapp01 » au lieu de « webapp01.contoso.com »).

2. Contrôlez la configuration de mise en réseau de conteneur pour vérifier que le conteneur peut résoudre un contrôleur de domaine et y accéder pour le domaine du compte de service administré de groupe. Des serveurs DNS mal configurés dans le conteneur sont une cause répandue de problèmes d’identité.

3. Vérifiez si le conteneur dispose d’une connexion valide au domaine en exécutant la cmdlet suivante dans le conteneur (en utilisant `docker exec` ou un équivalent) :

    ```powershell
    nltest /sc_verify:contoso.com
    ```

    La vérification de l’approbation doit retourner `NERR_SUCCESS` si le compte de service administré de groupe est disponible et si la connectivité réseau permet au conteneur de communiquer avec le domaine. En cas d’échec, vérifiez la configuration réseau de l’hôte et du conteneur. Tous deux doivent être en mesure de communiquer avec le contrôleur de domaine.

4. Vérifiez si le conteneur peut obtenir un ticket TGT Kerberos valide :

    ```powershell
    klist get krbtgt
    ```

    Cette commande doit retourner la réponse « A ticket to krbtgt has been retrieved successfully » et indiquer le contrôleur de domaine utilisé pour récupérer le ticket. Si vous êtes en mesure d’obtenir un ticket TGT mais que la commande `nltest` de l’étape précédente échoue, cela peut indiquer que le compte de service administré de groupe est mal configuré. Pour plus d’informations, consultez [Vérifier le compte de service administré de groupe](#check-the-gmsa-account).

    Si vous ne pouvez pas obtenir de ticket TGT à l’intérieur du conteneur, cela peut indiquer des problèmes de connectivité réseau ou DNS. Assurez-vous que le conteneur peut résoudre un contrôleur de domaine à l’aide du nom DNS du domaine et que le contrôleur de domaine est routable à partir du conteneur.

5. Assurez-vous que votre application est [configurée pour utiliser le compte de service administré de groupe](gmsa-configure-app.md). Le compte d’utilisateur à l’intérieur du conteneur ne change pas lorsque vous utilisez un compte de service administré de groupe. Au lieu de cela, le compte système utilise le compte de service administré de groupe quand il communique avec d’autres ressources réseau. Cela signifie que votre application doit s’exécuter en tant que service réseau ou système local pour tirer parti de l’identité du compte de service administré de groupe.

    > [!TIP]
    > Si vous exécutez `whoami` ou utilisez un autre outil pour identifier votre contexte utilisateur actuel dans le conteneur, vous ne verrez pas le nom du compte de service administré de groupe lui-même. Cela est dû au fait que vous vous connectez toujours au conteneur en tant qu’utilisateur local plutôt qu’en tant qu’identité de domaine. Le compte d’ordinateur utilise le compte de service administré de groupe chaque fois qu’il communique avec des ressources réseau. Cela explique pourquoi votre application doit s’exécuter en tant que service réseau ou système local.

### <a name="check-the-gmsa-account"></a>Vérifier le compte de service administré de groupe

1. Si votre conteneur semble être correctement configuré mais que des utilisateurs ou d’autres services ne peuvent pas s’authentifier automatiquement auprès de votre application en conteneur, vérifiez les noms de principal de service sur votre compte de service administré de groupe. Les clients recherchent le compte de service administré de groupe par le nom auquel ils accèdent à votre application. Cela peut signifier que vous aurez besoin de noms de principal de service `host` supplémentaires pour votre compte de service administré de groupe si, par exemple, des clients se connectent à votre application via un équilibreur de charge ou un nom DNS différent.

2. Vérifiez que compte de service administré de groupe et l’hôte de conteneur appartiennent au même domaine Active Directory. L’hôte de conteneur ne peut pas récupérer le mot de passe du compte de service administré de groupe si celui-ci appartient à un autre domaine.

3. Assurez-vous qu’il n’existe dans votre domaine qu’un seul compte portant le même nom que votre compte de service administré de groupe. Les objets compte de service administré de groupe ont un signe dollar ($) ajouté à leur nom de compte SAM. Il est donc possible qu’un compte de service administré de groupe soit nommé « moncompte$ » et qu’un compte d’utilisateur non lié soit nommé « moncompte » dans le même domaine. Cela peut entraîner des problèmes si le contrôleur de domaine ou l’application doit rechercher le compte de service administré de groupe par son nom. Vous pouvez rechercher dans Active Directory des objets nommés de façon similaire à l’aide de la commande suivante :

    ```powershell
    # Replace "GMSANAMEHERE" with your gMSA account name (no trailing dollar sign)
    Get-ADObject -Filter 'sAMAccountName -like "GMSANAMEHERE*"'
    ```

4. Si vous avez activé une délégation sans contrainte sur le compte de service administré de groupe, assurez-vous que l’indicateur `WORKSTATION_TRUST_ACCOUNT`est toujours activé pour l’attribut [UserAccountControl](https://support.microsoft.com/help/305144/how-to-use-useraccountcontrol-to-manipulate-user-account-properties). Cet indicateur est requis pour NETLOGON dans le conteneur, pour communiquer avec le contrôleur de domaine, comme c’est le cas quand une application doit résoudre un nom en SID ou inversement. Vous pouvez vérifier si l’indicateur est configuré correctement avec les commandes suivantes :

    ```powershell
    $gMSA = Get-ADServiceAccount -Identity 'yourGmsaName' -Properties UserAccountControl
    ($gMSA.UserAccountControl -band 0x1000) -eq 0x1000
    ```

    Si les commandes ci-dessus retournent `False`, utilisez la commande suivante pour ajouter l’indicateur `WORKSTATION_TRUST_ACCOUNT` à la propriété UserAccountControl du compte de service administré de groupe. Cette commande efface également les indicateurs `NORMAL_ACCOUNT`, `INTERDOMAIN_TRUST_ACCOUNT` et `SERVER_TRUST_ACCOUNT` de la propriété UserAccountControl.

    ```powershell
    Set-ADObject -Identity $gMSA -Replace @{ userAccountControl = ($gmsa.userAccountControl -band 0x7FFFC5FF) -bor 0x1000 }
    ```