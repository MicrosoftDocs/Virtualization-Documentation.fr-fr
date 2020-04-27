---
title: Activer Hyper-V sur Windows 10
description: Installer Hyper-V sur Windows 10
keywords: Windows 10, Hyper-V
author: scooley
ms.date: 02/15/2019
ms.topic: article
ms.prod: windows-10-hyperv
ms.assetid: 752dc760-a33c-41bb-902c-3bb2ecd9ac86
ms.openlocfilehash: bad59fcc65bf66ab3c6dc940a17111e46a9bc226
ms.sourcegitcommit: 16ebc4f00773d809fae84845208bd1dcf08a889c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "77439696"
---
# <a name="install-hyper-v-on-windows-10"></a>Installer Hyper-V sur Windows 10

Activez Hyper-V pour créer des machines virtuelles sur Windows 10.  
Hyper-V peut être activé de nombreuses manières, y compris à l’aide du Panneau de configuration de Windows 10, de PowerShell ou de l’outil Gestion et maintenance des images de déploiement (DISM). Ce document présente chacune de ces options.

> **Remarque :**  Hyper-V est intégré à Windows en tant que fonctionnalité facultative ; il n’est pas disponible en téléchargement.

## <a name="check-requirements"></a>Vérifier la configuration requise

* Windows 10 Entreprise, Professionnel ou Éducation
* Processeur 64 bits avec traduction d’adresse de second niveau (SLAT).
* Processeur prenant en charge les extensions de mode du moniteur de machine virtuelle (VT-c sur les processeurs Intel).
* Au minimum 4 Go de mémoire.

Le rôle Hyper-V **ne peut pas** être installé sur Windows 10 Famille.

Mettez à niveau l’édition Windows 10 Famille vers Windows 10 Professionnel en ouvrant **Paramètres** > **Mise à jour et sécurité** > **Activation**.

Pour plus d’informations et pour connaître les étapes de résolution des problèmes, voir [Configuration requise pour Hyper-V sur Windows 10](../reference/hyper-v-requirements.md).

## <a name="enable-hyper-v-using-powershell"></a>Activer Hyper-V à l’aide de PowerShell

1. Ouvrez une console PowerShell en tant qu’administrateur.

2. Exécutez la commande suivante :

  ```powershell
  Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
  ```

  Si la commande est introuvable, assurez-vous que vous exécutez PowerShell en tant qu’administrateur.

Une fois l’installation terminée, redémarrez.

## <a name="enable-hyper-v-with-cmd-and-dism"></a>Activer Hyper-V avec CMD et DISM

L’outil Gestion et maintenance des images de déploiement (DISM, Deployment Image Servicing and Management) vous aide à configurer Windows et les images Windows.  DSIM permet, entre autres, d’activer des fonctionnalités Windows pendant que le système d’exploitation est en cours d’exécution.

Pour activer le rôle Hyper-V à l’aide de DISM :

1. Ouvrez une session PowerShell ou CMD en tant qu’administrateur.

1. Tapez la commande suivante :

  ```powershell
  DISM /Online /Enable-Feature /All /FeatureName:Microsoft-Hyper-V
  ```

  ![Fenêtre de console montrant Hyper-V en cours d’activation.](media/dism_upd.png)

Pour plus d’informations sur DSIM, voir [DISM - Informations techniques de référence sur l’outil Gestion et maintenance des images de déploiement](<https://docs.microsoft.com/previous-versions/windows/it-pro/windows-8.1-and-8/hh824821(v=win.10)>).

## <a name="enable-the-hyper-v-role-through-settings"></a>Activer le rôle Hyper-V via les paramètres

1. Cliquez avec le bouton droit sur le bouton Windows et sélectionnez Applications et fonctionnalités.

2. Sélectionnez **Programmes et fonctionnalités** à droite, sous les paramètres associés. 

3. Sélectionnez **Activer ou désactiver des fonctionnalités Windows**.

4. Sélectionnez **Hyper-V**, puis cliquez sur **OK**.

![Boîte de dialogue Programmes et fonctionnalités Windows](media/enable_role_upd.png)

Une fois l’installation terminée, vous êtes invité à redémarrer votre ordinateur.

## <a name="make-virtual-machines"></a>Créer des machines virtuelles

[Créer votre première machine virtuelle](quick-create-virtual-machine.md)
