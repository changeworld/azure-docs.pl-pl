---
title: Rejestracja w usłudze Azure stosu Azure zintegrowanych systemów | Dokumentacja firmy Microsoft
description: Zawiera opis procesu rejestracji Azure wdrożeniach z wieloma węzłami połączone Azure stosu Azure.
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
ms.date: 05/11/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: f34c4697439685ce6ea0ce3f2c7e954ee81b5079
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="register-azure-stack-with-azure"></a>Zarejestruj stosu Azure przy użyciu platformy Azure
Rejestrowanie [stosu Azure](azure-stack-poc.md) z platformą Azure umożliwia pobieranie elementów marketplace z platformy Azure i ustawienia commerce danych raportowania z powrotem do firmy Microsoft. Po zarejestrowaniu stosu Azure użycia jest zgłaszany do handlu Azure i widoczny w ramach subskrypcji, używana do rejestracji. 

> [!IMPORTANT]
> Rejestracja jest wymagany do obsługi pełnej funkcjonalności stosu Azure, łącznie z witryny marketplace zespolonego. Ponadto można naruszenie umowy licencyjnej, jeśli nie zarejestrujesz się tak, gdy przy użyciu modelu rozliczeń płatności jako — użytkownik użycia stosu Azure. Aby dowiedzieć się więcej na temat licencjonowania modeli stosu Azure, zobacz [jak kupić strony](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Wymagania wstępne
Przed zarejestrowaniem stosu Azure przy użyciu platformy Azure, musi mieć:

- Identyfikator subskrypcji dla subskrypcji platformy Azure. Można uzyskać Identyfikatora, logowanie do platformy Azure, kliknij przycisk **więcej usług** > **subskrypcje**, kliknij subskrypcję, którego chcesz użyć, a następnie w obszarze **Essentials** można znaleźć Identyfikator subskrypcji. 

  > [!NOTE]
  > Niemcy i instytucji rządowych Stanów Zjednoczonych subskrypcje w chmurze nie są obecnie obsługiwane.

- Nazwa użytkownika i hasło dla konta, które jest właścicielem subskrypcji (kont MSA/2FA są obsługiwane).
- Zarejestrowano dostawcę zasobów Azure stosu (zobacz poniższą sekcję zarejestrować dostawcy zasobów stosu Azure, aby uzyskać szczegółowe informacje).

Jeśli nie masz subskrypcji platformy Azure, która spełnia te wymagania, możesz [utworzyć tutaj bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie stosu Azure wiąże się bez kosztów w ramach subskrypcji platformy Azure.

### <a name="bkmk_powershell"></a>Instalowanie programu PowerShell dla usługi Azure stosu
Musisz użyć najnowszej programu PowerShell dla usługi Azure stosu do rejestracji w usłudze Azure.

Jeśli nie jest jeszcze zainstalowana, [zainstalować program PowerShell Azure stosu](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Pobieranie narzędzia Azure stosu
Repozytorium GitHub narzędzia stosu Azure zawiera moduły programu PowerShell Azure stosu funkcjonalności. w tym funkcje rejestracji. Podczas procesu rejestracji, musisz zaimportować i użyć modułu programu RegisterWithAzure.psm1 PowerShell znalezione w repozytorium narzędzia Azure stosu, można zarejestrować wystąpienia stosu Azure przy użyciu platformy Azure. 

Aby upewnić się, czy używasz najnowszej wersji, należy usunąć istniejące wersje narzędzia Azure stosu i [najnowszą wersję można pobrać z witryny GitHub](azure-stack-powershell-download.md) przed zarejestrowaniem z platformy Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Zarejestruj Azure stosu w środowiskach połączonych
Środowisk sieciowych można uzyskać dostępu do Internetu Azure. Dla tych środowisk należy zarejestrować dostawcę zasobów Azure stosu z platformy Azure, a następnie skonfiguruj modelu rozliczeń.

> [!NOTE]
> Kroki te należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego. 

### <a name="register-the-azure-stack-resource-provider"></a>Rejestrowanie dostawcy zasobów Azure stosu
Aby zarejestrować stosu Azure dostawcy zasobów platformy Azure, uruchom program PowerShell ISE jako administrator i użyj następujących poleceń programu PowerShell z **EnvironmentName** parametr wartość typu odpowiednią subskrypcję platformy Azure (zobacz Parametry poniżej). 

1. Dodaj konto platformy Azure, która służy do rejestrowania stosu Azure. Aby dodać konta, uruchom **Add-AzureRmAccount** polecenia cmdlet. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta administratora globalnego usługi Azure i może być konieczne użycie uwierzytelniania wieloskładnikowego 2 na podstawie konfiguracji Twoje konto.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<Either AzureCloud or AzureChinaCloud>"
   ```

   | Parametr | Opis |  
   |-----|-----|
   | EnvironmentName | Nazwa środowiska subskrypcji chmury Azure. Nazwy środowiska obsługiwane są **AzureCloud** lub, jeśli przy użyciu subskrypcję Azure Chin **AzureChinaCloud**.  |
   |  |  |

2. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, wybierz ten, który ma być używany:  

   ```PowerShell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów Azure stosu w Twojej subskrypcji platformy Azure:

   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Zarejestruj stosu Azure przy użyciu platformy Azure przy użyciu modelu rozliczeń płatności jako — użytkownik użycia
Zarejestrować stosu Azure za pomocą platformy Azure przy użyciu modelu rozliczeń płatności jako — użytkownik użycia, wykonaj następujące czynności.

1. Uruchom program PowerShell ISE jako administrator, a następnie przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas możesz [pobrane narzędzia Azure stosu](#bkmk_tools). Importuj **RegisterWithAzure.psm1** modułu przy użyciu programu PowerShell: 

  ```powershell
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Następnie w tej samej sesji programu PowerShell, upewnij się, że użytkownik jest zalogowany do poprawny kontekst środowiska PowerShell usługi Azure. To jest konto platformy azure, użytej można zarejestrować dostawcy zasobów Azure stosu powyżej. PowerShell wpisz: 

  ```powershell 
  Add-AzureRmAccount -Environment "<Either AzureCloud or AzureChinaCloud>" 
  ``` 

3. W tej samej sesji programu PowerShell, uruchom **AzsRegistration zestaw** polecenia cmdlet. PowerShell wpisz:  

  ```powershell
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse
  ```

  |Parametr|Opis|
  |-----|-----|
  |PrivilegedEndpointCredential|Poświadczenia używane do [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Nazwa użytkownika jest w formacie **AzureStackDomain\CloudAdmin**.|
  |PrivilegedEndpoint|Wstępnie skonfigurowane zdalnego programu PowerShell konsoli, która dostarcza funkcje, takie jak zbierania dzienników oraz innych post zadań wdrażania. Aby dowiedzieć się więcej, zapoznaj się [przy użyciu punktu końcowego uprzywilejowanych](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artykułu.|
  |BillingModel|Modelu rozliczeń, który korzysta z subskrypcji. Dozwolone wartości tego parametru to: pojemności, PayAsYouUse i tworzenia.|
  |  |  |

  Proces potrwa od 10 do 15 minut. Po zakończeniu wykonywania polecenia zostanie wyświetlony komunikat **"środowiska został zarejestrowany i aktywować przy użyciu podanych parametrów."**

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Zarejestruj stosu Azure przy użyciu platformy Azure przy użyciu modelu rozliczeń pojemności
Postępuj zgodnie z instrukcjami tego samego używana do rejestracji przy użyciu modelu rozliczeń płatności jako — użytkownik użycia, ale Dodaj numer umowy, w którym zakupiono pojemności i zmień **BillingModel** parametr **pojemności**. Wszystkie inne parametry nie uległy zmianie.

PowerShell wpisz:

```powershell
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Zarejestruj w środowiskach rozłączonych Azure stosu 
*Informacje przedstawione w tej sekcji ma zastosowanie, począwszy od wersji aktualizacji 1712 stosu Azure (180106.1) i nie jest obsługiwane we wcześniejszych wersjach.*

W przypadku rejestracji Azure stosu w środowisku bez połączenia (bez łączności z Internetem), musisz pobrać rejestracji tokenu ze środowiska Azure stosu, a następnie użyj tokenu na komputerze, który może nawiązać połączenia z platformy Azure i ma [środowiska PowerShell Azure stosu zainstalowany](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Pobierz rejestracji token ze środowiska Azure stosu

1. Uruchom program PowerShell ISE jako administrator, a następnie przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas możesz [pobrane narzędzia Azure stosu](#bkmk_tools). Importuj **RegisterWithAzure.psm1** modułu:  

  ```powershell 
  Import-Module .\RegisterWithAzure.psm1 
  ``` 

2. Aby uzyskać tokenu rejestracji, uruchom następujące polecenia programu PowerShell:  

  ```Powershell
  $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
  $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
  ```
  
  > [!TIP]  
  > Token rejestracji jest zapisany w określonym pliku *$FilePathForRegistrationToken*. Ścieżka lub nazwa pliku można zmienić, według uznania. 

3. Zapisz ten token rejestracji do użycia na platformie Azure połączone maszyny. Z $FilePathForRegistrationToken można skopiować pliku lub tekst.


### <a name="connect-to-azure-and-register"></a>Łączenie się z platformy Azure i rejestr
Na komputerze, który jest połączonych z Internetem wykonaj te same kroki, aby zaimportować moduł RegisterWithAzure.psm1 i logowania do poprawny kontekst programu Azure Powershell. Następnie wywołaj AzsEnvironment rejestru i określ token rejestracji do rejestracji w usłudze Azure:

  ```Powershell  
  $registrationToken = "<Your Registration Token>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
Opcjonalnie możesz użyć polecenia cmdlet Get-Content wskaż plik, który zawiera token rejestracji:

  ```Powershell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken  
  ```
  > [!NOTE]  
  > Zapisz nazwę zasobu rejestracji i token rejestracji do użytku w przyszłości.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Pobrać klucza aktywacji z zasobów platformy Azure rejestracji 
Następnie należy pobrać klucza aktywacji z zasobu rejestracji utworzona na platformie Azure podczas AzsEnvironment rejestru. 
 
Aby uzyskać klucz aktywacji, uruchom następujące polecenia programu PowerShell:  

  ```Powershell 
  $RegistrationResourceName = "AzureStack-<Cloud Id for the Environment to register>" 
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt" 
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath 
  ``` 
  > [!TIP]   
  > Klucz aktywacji jest zapisywany w określonym pliku *$KeyOutputFilePath*. Ścieżka lub nazwa pliku można zmienić, według uznania. 

### <a name="create-an-activation-resource-in-azure-stack"></a>Utwórz zasób aktywacji w stosie Azure 
Wróć do środowiska Azure stosu, z tekstem i pliku z kluczem aktywacji utworzone na podstawie Get AzsActivationKey. Następnie utworzysz aktywacji zasobu w stosie Azure przy użyciu tego klucza aktywacji. Aby utworzyć zasób aktywacji, uruchom następujące polecenia programu PowerShell:  

  ```Powershell 
  $ActivationKey = "<activation key>" 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

Opcjonalnie możesz użyć polecenia cmdlet Get-Content wskaż plik, który zawiera token rejestracji: 

  ```Powershell   
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>' 
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey 
  ``` 

## <a name="verify-azure-stack-registration"></a>Weryfikuj rejestrację Azure stosu
Wykonaj następujące czynności, aby sprawdzić, czy Azure stos został pomyślnie zarejestrowany przy użyciu platformy Azure.
1. Zaloguj się do stosu Azure [portalu administratora](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;region >. &lt;fqdn >*.
2. Kliknij przycisk **więcej usług** > **zarządzania Marketplace** > **dodać z platformy Azure**.

Jeśli zobaczysz listę dostępnych z platformy Azure (na przykład WordPress) elementów, proces aktywacji zakończyło się pomyślnie. Jednak w środowiskach rozłączonych nie zobaczysz elementy witrynę Azure marketplace w stosie Azure marketplace.

> [!NOTE]
> Po zakończeniu rejestracji aktywne ostrzeżenie nie rejestracji nie będzie dłużej widoczny.

## <a name="renew-or-change-registration"></a>Należy odnowić lub zmienić rejestracji
### <a name="renew-or-change-registration-in-connected-environments"></a>Odnów lub zmień rejestracji w środowiskach połączonych
Musisz zaktualizować lub odnowić rejestrację w następujących okolicznościach:
- Po odnowieniu subskrypcji corocznych na podstawie pojemności.
- Po zmianie modelu rozliczeń.
- Jeśli można skalować zmiany (dodawania i usuwania węzłów) rozliczania opartego na wydajność.

#### <a name="change-the-subscription-you-use"></a>Zmień subskrypcję, których używasz
Jeśli chcesz zmienić subskrypcję w przypadku użycia, najpierw należy uruchomić **AzsRegistration Usuń** polecenia cmdlet, upewnij się, użytkownik jest zalogowany do poprawny kontekst programu Azure PowerShell, a na koniec uruchom **AzsRegistration zestawu**  ze wszystkimi zmienić parametry:

  ```powershell
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

#### <a name="change-the-billing-model-or-syndication-features"></a>Zmień rozliczeń modelu lub zespolonego funkcji
Jeśli chcesz zmienić modelu rozliczeń lub zespolonego funkcji dla instalacji, należy wywołać funkcję rejestracji, aby ustawić nowe wartości. Nie należy najpierw usunąć bieżący rejestracji: 

  ```powershell
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Należy odnowić lub zmienić rejestracji w środowiskach rozłączonych 
Musisz zaktualizować lub odnowić rejestrację w następujących okolicznościach: 
- Po odnowieniu subskrypcji corocznych na podstawie pojemności. 
- Po zmianie modelu rozliczeń. 
- Jeśli można skalować zmiany (dodawania i usuwania węzłów) rozliczania opartego na wydajność. 

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Usuń zasób aktywacji ze stosu Azure 
Należy najpierw usunąć aktywacji z stosu Azure, a następnie zasobów rejestracji na platformie Azure.  

Aby usunąć zasób aktywacji w stosie Azure, uruchom następujące polecenia programu PowerShell w środowisku Azure stosu:  

  ```Powershell 
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint 
  ``` 

Następnie usunąć zasób rejestracji na platformie Azure, upewnij się, są na platformie Azure połączone komputera, zaloguj się do poprawny kontekst programu Azure PowerShell i uruchom odpowiednich poleceń programu PowerShell, zgodnie z poniższym opisem.

Można użyć tokenu rejestracji użytego do utworzenia zasobu:  

  ```Powershell 
  $registrationToken = "<registration token>" 
  Unregister-AzsEnvironment -RegistrationToken $registrationToken 
  ``` 
  
Lub można użyć nazwy rejestracji: 

  ```Powershell 
  $registrationName = "AzureStack-<Cloud Id of Azure Stack Environment>" 
  Unregister-AzsEnvironment -RegistrationName $registrationName 
  ``` 

### <a name="re-register-using-disconnected-steps"></a>Zarejestruj ponownie przy użyciu odłączonej kroki 
Teraz mogła całkowicie wyrejestrować w scenariuszu rozłączona i należy powtórzyć kroki rejestrowania środowiska Azure stosu w scenariuszu odłączone. 

## <a name="next-steps"></a>Kolejne kroki

[Pobieranie elementów marketplace z platformy Azure](azure-stack-download-azure-marketplace-item.md)
