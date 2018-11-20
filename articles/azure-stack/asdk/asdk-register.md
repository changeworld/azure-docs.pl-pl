---
title: Rejestrowania ASDK na platformie Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób rejestrowania usługi Azure Stack z platformą Azure, umożliwiając syndykacji portalu marketplace i raportowanie użycia.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: e86ff4ebf91d0c0b691caf429d9489bf769f16af
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975196"
---
# <a name="azure-stack-registration"></a>Rejestracja w usłudze Azure Stack
Instalacja usługi Azure Stack Development Kit (ASDK) można zarejestrować za pomocą platformy Azure, aby pobieranie elementów portalu marketplace z platformy Azure i Ustaw dane handlu odsyłanie do firmy Microsoft. Rejestracja jest wymagany do obsługi pełnego funkcje usługi Azure Stack, w tym portalu marketplace syndykacji. Rejestracja jest zalecane, ponieważ umożliwia testowanie ważnych funkcji usługi Azure Stack, takich jak syndykacji portalu marketplace i raportowanie użycia. Po zarejestrowaniu usługi Azure Stack, użycie jest zgłaszany do handlu platformy Azure. Można to sprawdzić w ramach subskrypcji, których użyto podczas rejestracji. Jednak użytkownicy ASDK nie są naliczane za dotychczasowe użycie raportowane.

Jeśli nie zarejestrujesz swoje ASDK, możesz zobaczyć **wymagana aktywacja** alert ostrzeżenia, zaleceniem rejestrowania usługi Azure Stack Development Kit. Takie zachowanie jest oczekiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem tych instrukcji do rejestrowania ASDK na platformie Azure, upewnij się, że zainstalowany program Azure PowerShell stosu i pobrać narzędzia usługi Azure Stack, zgodnie z opisem w [konfiguracji powdrożeniowej](asdk-post-deploy.md) artykułu.

Ponadto tryb języka programu PowerShell musi być równa **FullLanguageMode** na komputerze używanym do rejestrowania ASDK na platformie Azure. Aby sprawdzić, czy bieżący tryb języka jest ustawiony na pełne, Otwórz okno programu PowerShell z podwyższonym i uruchom następujące polecenia programu PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Upewnij się, zwraca dane wyjściowe **FullLanguageMode**. Jeśli inny tryb języka jest zwracany, rejestracja będzie należy uruchomić na innym komputerze lub będą musieli można ustawić tryb języka **FullLanguageMode** przed kontynuowaniem.

## <a name="register-azure-stack-with-azure"></a>Rejestrowania usługi Azure Stack na platformie Azure
Wykonaj następujące kroki, aby zarejestrować ASDK z platformą Azure.

> [!NOTE]
> Wszystkie te kroki należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego. ASDK to komputerze-hoście development kit.

1. Otwórz konsolę programu PowerShell jako administrator.  

2. Uruchom następujące polecenia programu PowerShell w celu rejestrowania instalacji ASDK na platformie Azure. Musisz zalogować się do subskrypcji platformy Azure i lokalnej instalacji ASDK. Jeśli nie masz subskrypcji platformy Azure, ale możesz [Utwórz w tym miejscu Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie usługi Azure Stack jest naliczana opłata bez kosztów związanych z subskrypcją platformy Azure.<br><br>Jeśli skrypt rejestracji są uruchomione na więcej niż jedno wystąpienie usługi Azure Stack przy użyciu tego samego Identyfikatora subskrypcji platformy Azure, należy ustawić unikatową nazwę dla rejestracji po uruchomieniu **AzsRegistration zestaw** polecenia cmdlet. **RegistrationName** parametr ma wartość domyślną **AzureStackRegistration**. Jednak jeśli używasz tej samej nazwie w więcej niż jedno wystąpienie usługi Azure Stack, skrypt zakończy się niepowodzeniem.

    ```PowerShell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```
3. Po ukończeniu działania skryptu, powinien zostać wyświetlony ten komunikat: **środowiska jest teraz zarejestrowany i aktywować przy użyciu podanych parametrów.**

    ![Środowisko jest obecnie zarejestrowany.](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Zarejestruj się w środowiskach rozłączonych
W przypadku rejestracji usługi Azure Stack w środowisku bez połączenia (bez łączności z Internetem), należy do pobrania tokenu rejestracji ze środowiska usługi Azure Stack, a następnie użyć tego tokenu na komputerze, które można podłączyć do platformy Azure, aby zarejestrować i utworzyć aktywacji zasób w danym środowisku ASDK.
 
 > [!IMPORTANT]
 > Przed rozpoczęciem korzystania z tych instrukcji, można zarejestrować usługi Azure Stack, upewnij się, że zainstalowany program PowerShell dla usługi Azure Stack i pobrać narzędzia usługi Azure Stack, zgodnie z opisem w [konfiguracji powdrożeniowej](asdk-post-deploy.md) artykułu na hoście ASDK komputerze i na komputerze z dostępem do Internetu, używany do łączenia z platformy Azure i zarejestruj.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Pobrania tokenu rejestracji ze środowiska usługi Azure Stack
Na komputerze-hoście ASDK, uruchom program PowerShell jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas pobierania narzędzia Azure Stack. Użyj następujących poleceń programu PowerShell do importowania **RegisterWithAzure.psm1** modułu, a następnie użycie **Get AzsRegistrationToken** polecenie cmdlet umożliwiające uzyskanie tokenu rejestracji:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```
Domyślnie tokenu rejestracji jest zapisywane w pliku określonym dla *$FilePathForRegistrationToken* parametru. Ścieżka pliku lub nazwę pliku można zmienić, według uznania.

Zapisz ten token rejestracji pod kątem użycia na komputerze połączonym z Internetem. Możesz skopiować plik lub tekst z $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Łączenie się z platformy Azure i Zarejestruj się
Na komputerze połączonych internet, uruchom program PowerShell jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas pobierania platformy Azure Narzędzia stosu. Użyj następujących poleceń programu PowerShell do importowania **RegisterWithAzure.psm1** modułu, a następnie użycie **AzsEnvironment rejestru** polecenia cmdlet rejestrowania na platformie Azure, zapewniając rejestrację tokenem możesz właśnie utworzony i nazwa rejestracji:  

  ```PowerShell  
  $registrationToken = "<your registration token>"
  $RegistrationName = "<unique registration name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Alternatywnie, można użyć **pobrania zawartości** polecenia cmdlet, aby wskazać plik zawierający tokenu programu rejestracji:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<path>\<registration token file>'
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Zapisz ten token rejestracji, a nazwa zasobu rejestracji do użytku w przyszłości.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Pobieranie klucza aktywacji z zasobu rejestracja w usłudze Azure

Nadal korzystając z komputerze połączonym z Internetem, pobrać klucza aktywacji z zasobu rejestracji tworzony podczas rejestrowania za pomocą platformy Azure.

Można pobrać klucza aktywacji, uruchom następujące polecenia programu PowerShell, należy użyć tej samej wartości Nazwa rejestracji otrzymane podczas rejestrowania za pomocą platformy Azure w poprzednim kroku:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

Klucz aktywacji jest zapisywany w pliku określonym dla *$KeyOutputFilePath*. Ścieżka pliku lub nazwę pliku można zmienić, według uznania.

### <a name="create-an-activation-resource-in-azure-stack"></a>Utwórz zasób usługi aktywacji w usłudze Azure Stack

Wróć do środowiska usługi Azure Stack z plikiem lub tekst z kluczem aktywacji utworzone na podstawie **Get AzsActivationKey**. Uruchom następujące polecenia programu PowerShell w celu utworzenia zasobu aktywacji w usłudze Azure Stack przy użyciu tego klucza aktywacji:   

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Alternatywnie, można użyć **pobrania zawartości** polecenia cmdlet, aby wskazać plik zawierający tokenu programu rejestracji:

  ```Powershell
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = Get-Content -Path '<path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

## <a name="verify-the-registration-was-successful"></a>Sprawdź, czy rejestracja zakończyła się pomyślnie
Wykonaj następujące kroki, aby sprawdzić, czy rejestracji ASDK z platformą Azure zakończyła się pomyślnie.

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external).

2. Kliknij przycisk **zarządzania Marketplace** > **Dodaj na platformie Azure**.

    ![](media/asdk-register/2.PNG)

3. Jeśli zobaczysz listę elementów dostępnych na platformie Azure, proces aktywacji zakończyło się pomyślnie.

    ![](media/asdk-register/3.PNG)

## <a name="move-a-registration-resource"></a>Przenieś zasób rejestracji
Przenoszenie zasobów rejestracji między grupami zasobów w ramach tej samej subskrypcji **jest** obsługiwane. Aby uzyskać więcej informacji na temat przenoszenia zasobów do nowej grupy zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Kolejne kroki
[Dodawanie elementu portalu marketplace usługi Azure Stack](.\.\azure-stack-marketplace.md)
