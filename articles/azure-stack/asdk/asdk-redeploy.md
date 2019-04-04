---
title: Ponowne wdrażanie usługi Azure Stack Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: W tym artykule dowiesz się, jak zainstalować ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 284e1ce3c3b9a63f3c25e85891b1d2688726183e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879985"
---
# <a name="redeploy-the-asdk"></a>Ponowne wdrażanie ASDK
W tym artykule dowiesz się, jak i ponowne wdrażanie usługi Azure Stack Development Kit (ASDK) w środowisku nieprodukcyjnym. Ponieważ uaktualnianie ASDK nie jest obsługiwane, należy całkowicie ją ponownie wdrożyć można przenieść do nowszej wersji. Można także wdrożyć ponownie ASDK w dowolnym momencie po prostu chcesz zacząć od początku, od podstaw.

> [!IMPORTANT]
> Uaktualnianie ASDK do nowej wersji nie jest obsługiwane. Należy ponownie wdrożyć ASDK na komputerze-hoście development kit każdorazowo, który chcesz ocenić nowszą wersję usługi Azure Stack.

## <a name="remove-azure-registration"></a>Usuń rejestrację platformy Azure 
Jeśli instalacja ASDK zostały wcześniej zarejestrowane za pomocą platformy Azure, należy usunąć zasób rejestracji przed ponownego wdrażania ASDK. Zarejestruj ponownie aplikację ASDK umożliwiające dostępność elementów w portalu marketplace podczas ponownego wdrażania ASDK. Jeśli nie została wcześniej zarejestrowana ASDK z subskrypcją platformy Azure, możesz pominąć tę sekcję.

Aby usunąć zasób rejestracji, użyj **AzsRegistration Usuń** polecenia cmdlet, aby wyrejestrować usługi Azure Stack. Następnie należy użyć **Remove-AzureRMResourceGroup** polecenia cmdlet, aby usunąć grupę zasobów usługi Azure Stack z subskrypcji platformy Azure:

1. Otwórz konsolę programu PowerShell jako administrator na komputerze, który ma dostęp do uprzywilejowanych punktu końcowego. ASDK to komputerze-hoście development kit.

2. Uruchom następujące polecenia programu PowerShell, aby wyrejestrować ASDK instalacji i usuwania **azurestack** grupy zasobów z subskrypcji platformy Azure:

   ```Powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. Monit logować się do subskrypcji platformy Azure i lokalnej instalacji ASDK po uruchomieniu skryptu.
4. Po zakończeniu działania skryptu powinny zostać wyświetlone komunikaty podobne do następujących:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).`
    `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Usługa Azure Stack teraz pomyślnie należy wyrejestrować z subskrypcji platformy Azure. Ponadto azurestack grupy zasobów, tworzony podczas rejestrowania ASDK dzięki systemowi Azure powinna również zostaną usunięte.

## <a name="deploy-the-asdk"></a>Wdrażanie ASDK
Aby przeprowadzić ponowne wdrożenie usługi Azure Stack, należy uruchomić za pośrednictwem od podstaw zgodnie z poniższym opisem. Kroki różnią się w zależności od tego, czy skryptu Instalatora (asdk installer.ps1) usługi Azure Stack jest używany do zainstalowania ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Ponowne wdrażanie ASDK przy użyciu skryptu Instalatora
1. Na komputerze ASDK, otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i przejdź do skryptu asdk installer.ps1 w **AzureStack_Installer** katalog znajduje się na dysku niesystemowym. Uruchom skrypt, a następnie kliknij przycisk **ponowny rozruch**.

   ![Uruchom skrypt asdk installer.ps1](media/asdk-redeploy/1.png)

2. Wybierz podstawowy system operacyjny (nie **usługi Azure Stack**) i kliknij przycisk **dalej**.

   ![Uruchom ponownie system operacyjny hosta](media/asdk-redeploy/2.png)

3. Po hosta zestaw development jest wykonywany ponowny rozruch podstawowego systemu operacyjnego, zaloguj się jako administrator lokalny. Zlokalizuj i Usuń **C:\CloudBuilder.vhdx** pliku, który został użyty jako część poprzedniego wdrożenia. 

4. Powtórz te same kroki, które zostały podjęte do pierwszego [wdrażanie ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Ponowne wdrażanie ASDK bez przy użyciu Instalatora
Jeśli nie używasz skryptu asdk installer.ps1 zainstalował ASDK, możesz ręcznie zmienić konfigurację komputera hosta development kit przed ponownego wdrażania ASDK.

1. Uruchom narzędzie konfiguracji systemu, uruchamiając **msconfig.exe** na komputerze ASDK. Na **rozruchu** , wybierz system operacyjny komputera hosta (nie usługi Azure Stack) kliknij pozycję **Ustaw jako domyślny**, a następnie kliknij przycisk **OK**. Kliknij przycisk **ponowne uruchomienie** po wyświetleniu monitu.

      ![Ustawienia konfiguracji rozruchu](media/asdk-redeploy/4.png)

2. Po hosta zestaw development jest wykonywany ponowny rozruch podstawowego systemu operacyjnego, zaloguj się jako administrator lokalny komputera hosta development kit. Zlokalizuj i Usuń **C:\CloudBuilder.vhdx** pliku, który został użyty jako część poprzedniego wdrożenia. 

3. Powtórz te same kroki, które zostały podjęte do pierwszego [wdrażanie ASDK przy użyciu programu PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Kolejne kroki
[Opublikuj ASDK zadania związane z wdrażaniem](asdk-post-deploy.md)




