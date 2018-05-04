---
title: Wdróż ponownie zestaw deweloperski Azure stosu (ASDK) | Dokumentacja firmy Microsoft
description: W tym samouczku Dowiedz się jak zainstalować ASDK.
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
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 33879187a912394b5cec6e9f9a8898f431134f5c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-redeploy-the-asdk"></a>Samouczek: należy ponownie wdrożyć ASDK
W tym samouczku możesz dowiedzieć się, jak można wdrożyć ponownie Azure stosu Development Kit (ASDK) w środowisku produkcyjnym z systemem innym niż. Ponieważ uaktualnienie ASDK nie jest obsługiwane, należy całkowicie wdrożenie przejście do nowszej wersji. Można także wdrożyć ponownie ASDK w dowolnym momencie, która po prostu chcesz rozpocząć od początku od początku.

> [!IMPORTANT]
> Uaktualnienie ASDK do nowej wersji nie jest obsługiwane. Należy ponownie wdrożyć ASDK na komputerze hosta development kit zawsze chcesz ocenić nowszej wersji programu Azure stosu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuń rejestracja w usłudze Azure 
> * Wdróż ponownie ASDK

## <a name="remove-azure-registration"></a>Usuń rejestracja w usłudze Azure 
Jeśli instalacja ASDK zostały wcześniej zarejestrowane przy użyciu platformy Azure, należy usunąć zasobu rejestracji przed ponownego wdrażania ASDK. Zarejestruj ponownie ASDK umożliwiające zespolonego marketplace podczas ponownego wdrażania ASDK. Jeśli nie zostały wcześniej zarejestrowane ASDK z subskrypcją platformy Azure, możesz pominąć tę sekcję.

Aby usunąć zasób rejestracji, użyj **AzsRegistration Usuń** polecenia cmdlet, aby wyrejestrować stosu Azure. Następnie należy użyć **AzureRMRsourceGroup Usuń** polecenia cmdlet, aby usunąć grupę zasobów Azure stosu z subskrypcją platformy Azure:

1. Otwórz konsolę programu PowerShell jako administrator na komputerze, który ma dostęp do uprzywilejowanych punktu końcowego. W przypadku ASDK, który jest development kit hosta.

2. Uruchom następujące polecenia programu PowerShell, aby wyrejestrować ASDK instalację i usuwanie **azurestack** grupy zasobów z subskrypcją platformy Azure:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Connect-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -CloudAdminCredential $YourCloudAdminCredential `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Zostanie wyświetlony monit Zaloguj się do subskrypcji platformy Azure i lokalnej instalacji ASDK po uruchomieniu skryptu.
4. Po ukończeniu działania skryptu, powinny zostać wyświetlone komunikaty podobne do następujących:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Azure stosu teraz pomyślnie należy wyrejestrować z subskrypcją platformy Azure. Ponadto azurestack grupy zasobów, tworzone podczas rejestrowania ASDK z platformy Azure, należy również zostaną usunięte.

## <a name="redeploy-the-asdk"></a>Wdróż ponownie ASDK
Aby ponownie wdrożyć stosu Azure, należy uruchomić za pośrednictwem od początku zgodnie z poniższym opisem. Te kroki są różne w zależności od tego, czy skrypt Instalatora (asdk installer.ps1) stosu Azure zostały użyte do zainstalowania ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Należy ponownie wdrożyć ASDK przy użyciu skryptu Instalatora
1. Na komputerze ASDK, otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i przejdź do skryptu asdk installer.ps1 **AzureStack_Installer** katalog znajduje się na dysku niesystemowym. Uruchom skrypt, a następnie kliknij przycisk **ponowny rozruch**.

   ![Uruchom skrypt asdk installer.ps1](media/asdk-redeploy/1.png)

2. Wybierz podstawowego systemu operacyjnego (nie **stosu Azure**) i kliknij przycisk **dalej**.

   ![Uruchom ponownie do systemu operacyjnego hosta](media/asdk-redeploy/2.png)

3. Po zestawie hosta ponowny rozruch w podstawowego systemu operacyjnego zaloguj jako administrator lokalny. Zlokalizuj i Usuń **C:\CloudBuilder.vhdx** pliku, który został użyty w ramach poprzedniego wdrożenia. 

4. Powtórz te same kroki, które do pierwszego [wdrażanie ASDK](asdk-deploy.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Wdróż ponownie ASDK bez za pomocą Instalatora
Jeśli nie używasz skryptu asdk installer.ps1 do zainstalowania ASDK, należy ręcznie ponownie skonfigurować development kit hosta przed ponownego wdrażania ASDK.

1. Uruchom narzędzie konfiguracji systemu, uruchamiając **msconfig.exe** na komputerze ASDK. Na **rozruchu** , wybierz system operacyjny komputera hosta (a nie stosu Azure), kliknij pozycję **Ustaw jako domyślny**, a następnie kliknij przycisk **OK**. Kliknij przycisk **ponowne uruchomienie** po wyświetleniu monitu.

      ![Ustaw konfigurację rozruchu](media/asdk-redeploy/4.png)

2. Po zestawie hosta ponowny rozruch w podstawowego systemu operacyjnego zaloguj jako administrator lokalny komputera hosta development kit. Zlokalizuj i Usuń **C:\CloudBuilder.vhdx** pliku, który został użyty w ramach poprzedniego wdrożenia. 

3. Powtórz te same kroki, które do pierwszego [wdrażanie ASDK przy użyciu programu PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Kolejne kroki
W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Usuń rejestracja w usłudze Azure 
> * Wdróż ponownie ASDK

Przejdź do następnego samouczkiem, aby dowiedzieć się, jak dodać element stosu Azure marketplace.

> [!div class="nextstepaction"]
> [Dodawanie elementu stosu Azure marketplace](asdk-marketplace-item.md)




