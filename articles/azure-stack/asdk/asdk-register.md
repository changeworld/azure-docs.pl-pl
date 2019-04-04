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
ms.date: 01/16/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 4dc4c9d4d936bbcf626884c5c90e16f640f268a0
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487774"
---
# <a name="azure-stack-registration"></a>Rejestracja w usłudze Azure Stack
Instalacja usługi Azure Stack Development Kit (ASDK) można zarejestrować za pomocą platformy Azure, aby pobieranie elementów portalu marketplace z platformy Azure i Ustaw dane handlu odsyłanie do firmy Microsoft. Rejestracja jest wymagany do obsługi pełnego funkcje usługi Azure Stack, w tym portalu marketplace syndykacji. Aby umożliwić testowanie ważnych funkcji usługi Azure Stack, takich jak syndykacji portalu marketplace i raportowanie użycia, wymagana jest rejestracja. Po zarejestrowaniu usługi Azure Stack, użycie jest zgłaszany do handlu platformy Azure. Można to sprawdzić w ramach subskrypcji, których użyto podczas rejestracji. Jednak użytkownicy ASDK nie są naliczane za dotychczasowe użycie raportowane.

Jeśli nie zarejestrujesz swoje ASDK, możesz zobaczyć **wymagana aktywacja** alert ostrzeżenia, zaleceniem rejestrowania usługi Azure Stack Development Kit. Takie zachowanie jest oczekiwane.

## <a name="prerequisites"></a>Wymagania wstępne
Przed użyciem tych instrukcji do rejestrowania ASDK na platformie Azure, upewnij się, że zainstalowany program Azure PowerShell stosu i pobrać narzędzia usługi Azure Stack, zgodnie z opisem w [konfiguracji powdrożeniowej](asdk-post-deploy.md) artykułu.

Ponadto tryb języka programu PowerShell musi być równa **FullLanguageMode** na komputerze używanym do rejestrowania ASDK na platformie Azure. Aby sprawdzić, czy bieżący tryb języka jest ustawiony na pełne, Otwórz okno programu PowerShell z podwyższonym i uruchom następujące polecenia programu PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Upewnij się, zwraca dane wyjściowe **FullLanguageMode**. Jeśli inny tryb języka jest zwracany, rejestracja będzie należy uruchomić na innym komputerze lub będą musieli można ustawić tryb języka **FullLanguageMode** przed kontynuowaniem.

Konta usługi Azure AD używane na potrzeby rejestracji, aby mieć dostęp do subskrypcji platformy Azure i masz uprawnienia do tworzenia aplikacji z obsługą tożsamości i nazwy główne usług w katalogu skojarzonego z posiadaną subskrypcją. Firma Microsoft zaleca rejestrowania usługi Azure Stack z platformą Azure za pomocą najniższych administracji przez [utworzenie konta usługi dla rejestracji](../azure-stack-registration-role.md) zamiast przy użyciu poświadczeń administratora globalnego.

## <a name="register-azure-stack-with-azure"></a>Rejestrowania usługi Azure Stack na platformie Azure
Wykonaj następujące kroki, aby zarejestrować ASDK z platformą Azure.

> [!NOTE]
> Wszystkie te kroki należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego. ASDK to komputerze-hoście development kit.

1. Otwórz konsolę programu PowerShell jako administrator.  

2. Uruchom następujące polecenia programu PowerShell w celu rejestrowania instalacji ASDK na platformie Azure. Musisz zalogować się do zarówno identyfikator subskrypcji Azure, jak i lokalnej instalacji ASDK. Jeśli nie masz rozliczeń identyfikator subskrypcji platformy Azure, ale możesz [Utwórz w tym miejscu Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie usługi Azure Stack jest naliczana opłata bez kosztów związanych z subskrypcją platformy Azure.<br><br>Ustawić unikatową nazwę dla rejestracji po uruchomieniu **AzsRegistration zestaw** polecenia cmdlet. **RegistrationName** parametr ma wartość domyślną **AzureStackRegistration**. Jednak jeśli używasz tej samej nazwie w więcej niż jedno wystąpienie usługi Azure Stack, skrypt zakończy się niepowodzeniem.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
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
3. Po ukończeniu działania skryptu, powinien zostać wyświetlony ten komunikat: **Środowisko jest teraz zarejestrowany i aktywować przy użyciu podanych parametrów.**

    ![Środowisko jest obecnie zarejestrowany.](media/asdk-register/1.PNG)


## <a name="register-in-disconnected-environments"></a>Zarejestruj się w środowiskach rozłączonych
W przypadku rejestracji usługi Azure Stack w środowisku bez połączenia (bez łączności z Internetem), należy do pobrania tokenu rejestracji ze środowiska usługi Azure Stack, a następnie użyć tego tokenu na komputerze, które można podłączyć do platformy Azure, aby zarejestrować i utworzyć aktywacji zasób w danym środowisku ASDK.
 
 > [!IMPORTANT]
 > Przed rozpoczęciem korzystania z tych instrukcji, można zarejestrować usługi Azure Stack, upewnij się, że zainstalowany program PowerShell dla usługi Azure Stack i pobrać narzędzia usługi Azure Stack, zgodnie z opisem w [konfiguracji powdrożeniowej](asdk-post-deploy.md) artykułu na hoście ASDK komputerze i na komputerze z dostępem do Internetu, używany do łączenia z platformy Azure i zarejestruj.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Pobrania tokenu rejestracji ze środowiska usługi Azure Stack
Na komputerze-hoście ASDK, uruchom program PowerShell jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas pobierania narzędzia Azure Stack. Użyj następujących poleceń programu PowerShell do importowania **RegisterWithAzure.psm1** modułu, a następnie użycie **Get AzsRegistrationToken** polecenie cmdlet umożliwiające uzyskanie tokenu rejestracji:  

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

Zapisz ten token rejestracji pod kątem użycia na komputerze połączonym z Internetem. Możesz skopiować plik lub tekst z pliku, utworzone przez parametr $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Łączenie się z platformy Azure i Zarejestruj się
W Internecie połączonych komputerów, użyj następujących poleceń programu PowerShell do importowania **RegisterWithAzure.psm1** modułu, a następnie użycie **AzsEnvironment rejestru** polecenia cmdlet, aby zarejestrować za pomocą platformy Azure za pomocą token rejestracji, który został utworzony i nazwa rejestracji:  

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Alternatywnie, można użyć **pobrania zawartości** polecenia cmdlet, aby wskazać plik zawierający tokenu programu rejestracji:

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Po zakończeniu rejestracji powinien zostać wyświetlony komunikat podobny do **środowiska usługi Azure Stack jest teraz zarejestrowany za pomocą platformy Azure.**

> [!IMPORTANT]
> Nie zamykaj okna programu PowerShell. 

Zapisz ten token rejestracji, a nazwa zasobu rejestracji do użytku w przyszłości.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Pobieranie klucza aktywacji z zasobu rejestracja w usłudze Azure

Nadal korzystając komputerze połączonym z Internetem **i tym samym oknie konsoli programu PowerShell**, pobrać klucza aktywacji z zasobu rejestracji tworzony podczas rejestrowania za pomocą platformy Azure.

Można pobrać klucza aktywacji, uruchom następujące polecenia programu PowerShell, należy użyć tej samej wartości Nazwa rejestracji otrzymane podczas rejestrowania za pomocą platformy Azure w poprzednim kroku:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```
### <a name="create-an-activation-resource-in-azure-stack"></a>Utwórz zasób usługi aktywacji w usłudze Azure Stack

Wróć do środowiska usługi Azure Stack z plikiem lub tekst z kluczem aktywacji utworzone na podstawie **Get AzsActivationKey**. Uruchom następujące polecenia programu PowerShell w celu utworzenia zasobu aktywacji w usłudze Azure Stack przy użyciu tego klucza aktywacji:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Alternatywnie, można użyć **pobrania zawartości** polecenia cmdlet, aby wskazać plik zawierający tokenu programu rejestracji:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Po zakończeniu aktywacji powinien zostać wyświetlony komunikat podobny do **środowiska zakończył się proces rejestracji i aktywacji.**

## <a name="verify-the-registration-was-successful"></a>Sprawdź, czy rejestracja zakończyła się pomyślnie

Możesz użyć **zarządzanie regionami** Kafelek, aby sprawdzić, czy rejestracji w usłudze Azure Stack zakończyła się pomyślnie. Ten Kafelek jest dostępny na domyślny pulpit nawigacyjny w portalu administratora.

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external).

2. Na pulpicie nawigacyjnym wybierz **zarządzanie regionami**.

    [![Kafelek Zarządzanie region](media/asdk-register/admin1sm.png "Kafelek Zarządzanie regionu")](media/asdk-register/admin1.png#lightbox)

3. Wybierz **właściwości**. Ten blok, stan i szczegółowe cechy danego środowiska. Możliwe stany **zarejestrowanej** lub **niezarejestrowany**. Jeśli nie zarejestrowano, pokazuje również identyfikator subskrypcji platformy Azure umożliwia rejestrowanie usługi Azure Stack, wraz z rejestracji grupy zasobów i nazwę.

## <a name="move-a-registration-resource"></a>Przenieś zasób rejestracji
Przenoszenie zasobów rejestracji między grupami zasobów w ramach tej samej subskrypcji **jest** obsługiwane. Aby uzyskać więcej informacji na temat przenoszenia zasobów do nowej grupy zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Kolejne kroki

- [Dodawanie elementu portalu marketplace usługi Azure Stack](../azure-stack-marketplace.md)
