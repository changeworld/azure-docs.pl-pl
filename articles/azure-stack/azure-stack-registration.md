---
title: Rejestracja w usłudze Azure dla usługi Azure Stack zintegrowane systemy | Dokumentacja firmy Microsoft
description: W tym artykule opisano proces rejestracji platformy Azure w celu wdrożeniach z wieloma węzłami podłączonej do usługi Azure Stack w usłudze Azure.
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
ms.date: 12/04/2018
ms.author: jeffgilb
ms.reviewer: brbartle
ms.openlocfilehash: 58dfb3f02b338d62fcfb10e4d8c1bc492cdacbda
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890555"
---
# <a name="register-azure-stack-with-azure"></a>Rejestrowania usługi Azure Stack na platformie Azure

Rejestrowanie usługi Azure Stack przy użyciu platformy Azure umożliwia pobieranie elementów portalu marketplace z platformy Azure i skonfiguruj dane handlu odsyłanie do firmy Microsoft. Po zarejestrowaniu usługi Azure Stack, użycie jest zgłaszany do handlu platformy Azure i będzie widoczny w ramach subskrypcji, używane do rejestracji.

Informacje przedstawione w tym artykule opisano rejestrowanie systemów usługi Azure Stack zintegrowane z platformą Azure. Aby uzyskać informacji na temat rejestrowania ASDK z platformą Azure, zobacz [rejestracji w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register) w dokumentacji ASDK.

> [!IMPORTANT]  
> Rejestracja jest wymagany do obsługi pełną funkcjonalność usługi Azure Stack, w tym elementy w portalu marketplace oferty. Ponadto można narusza postanowienia licencyjne, jeżeli nie zarejestrujesz się tak, gdy za pomocą modelu rozliczeniowego płatności za użycie w usłudze Azure Stack. Aby dowiedzieć się więcej o usłudze Azure Stack, modele licencjonowania, zobacz [jak strony zakupu](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Wymagania wstępne

Będą potrzebne następujące elementy w miejscu, przed zarejestrowaniem:

 - Sprawdź swoje poświadczenia
 - Ustawianie trybu języka programu PowerShell
 - Instalowanie programu PowerShell dla usługi Azure Stack
 - Pobierz narzędzia usługi Azure Stack
 - Określić scenariusz rejestracji

### <a name="verify-your-credentials"></a>Sprawdź swoje poświadczenia

Przed zarejestrowaniem usługi Azure Stack przy użyciu platformy Azure, musisz mieć:

- Identyfikator subskrypcji dla subskrypcji platformy Azure. Umowy EA, dostawcy usług Kryptograficznych lub dostawcy usług Kryptograficznych udostępnionych usług, których subskrypcje są obsługiwane w przypadku rejestracji. Dostawcy usług kryptograficznych, musisz zdecydować, czy [korzystania z subskrypcji dostawcy usług Kryptograficznych lub APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Aby uzyskać identyfikator, zaloguj się do platformy Azure, kliknij przycisk **wszystkich usług**. Następnie w obszarze **ogólne** kategorii, wybierz opcję **subskrypcje**, kliknij subskrypcję, dla którego chcesz użyć, a następnie w obszarze **Essentials** można znaleźć identyfikatora subskrypcji.

  > [!Note]  
  > Subskrypcje w chmurze (Niemcy) nie są obecnie obsługiwane.

- Nazwa użytkownika i hasło dla konta które jest właścicielem subskrypcji.

- Konto użytkownika musi mieć dostęp do subskrypcji platformy Azure i masz uprawnienia do tworzenia aplikacji z obsługą tożsamości i nazwy główne usług w katalogu skojarzonego z posiadaną subskrypcją.

- Dostawca zasobów usługi Azure Stack zarejestrowany (patrz sekcja rejestrowanie dostawcy zasobów platformy Azure Stack poniżej, aby uzyskać szczegółowe informacje).

Po zarejestrowaniu uprawnienia administratora globalnego usługi Azure Active Directory nie jest wymagana. Jednak niektóre operacje mogą wymagać poświadczenia administratora globalnego. Na przykład skryptu Instalatora dostawcy zasobów lub nową funkcję wymagające uprawnienia przyznawane. Możesz tymczasowo przywrócić uprawnienia administratora globalnego dla konta lub użyj konta oddzielne administratora globalnego, który jest właścicielem *domyślne subskrypcję dostawcy*.

Jeśli nie masz subskrypcji platformy Azure, która spełnia te wymagania, możesz to zrobić [Utwórz w tym miejscu Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie usługi Azure Stack jest naliczana opłata bez kosztów związanych z subskrypcją platformy Azure.

### <a name="powershell-language-mode"></a>Tryb języka programu PowerShell

Aby pomyślnie zarejestrować usługi Azure Stack, tryb języka programu PowerShell musi być równa **FullLanguageMode**.  Aby sprawdzić, czy bieżący tryb język jest ustawiony na pełne, Otwórz okno programu PowerShell z podwyższonym i uruchom następujące polecenia cmdlet programu PowerShell:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Upewnij się, zwraca dane wyjściowe **FullLanguageMode**. Jeśli inny tryb języka jest zwracany, rejestracji będzie należy uruchomić na innym komputerze lub będą musieli można ustawić tryb języka **FullLanguageMode** przed kontynuowaniem.

### <a name="install-powershell-for-azure-stack"></a>Instalowanie programu PowerShell dla usługi Azure Stack

Należy użyć najnowszą wersję programu PowerShell dla usługi Azure Stack można zarejestrować za pomocą platformy Azure.

Jeśli nie jest to najnowsza wersja nie jest jeszcze zainstalowany, zobacz [zainstalować program PowerShell dla usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

### <a name="download-the-azure-stack-tools"></a>Pobierz narzędzia usługi Azure Stack

Repozytorium GitHub usługi Azure Stack narzędzia zawiera moduły programu PowerShell, które obsługuje funkcjonalności usługi Azure Stack. w tym funkcje rejestracji. Podczas procesu rejestracji, należy zaimportować i używać **RegisterWithAzure.psm1** moduł programu PowerShell, znaleziono w repozytorium narzędzia usługi Azure Stack można zarejestrować swojego wystąpienia usługi Azure Stack przy użyciu platformy Azure.

Aby upewnić się, którego używasz najnowszej wersji, należy usunąć wszystkie istniejące wersje narzędzia usługi Azure Stack i [najnowszą wersję można pobrać z witryny GitHub](azure-stack-powershell-download.md) przed zarejestrowaniem się z platformą Azure.

### <a name="determine-your-registration-scenario"></a>Określić scenariusz rejestracji

Wdrożenia usługi Azure Stack może być *połączone* lub *odłączony*.

 - **Połączone**  
 Połączony oznacza, że wdrożono usługi Azure Stack, dzięki czemu może nawiązać połączenie z Internetem i na platformie Azure. Należy mieć Azure Active Directory (Azure AD) lub Active Directory Federation Services (AD FS) dla magazynu tożsamości. Z wdrożeniem połączone, mogą wybierać dwa modele rozliczeniowe: płatność za użycie lub oparty na pojemności.
    - [Zarejestruj połączonej usługi Azure Stack przy użyciu platformy Azure za pomocą **płatność za użycie** model rozliczeń](#register-connected-with-pay-as-you-go-billing)
    - [Zarejestruj połączonej usługi Azure Stack przy użyciu platformy Azure za pomocą **pojemności** model rozliczeń](#register-connected-with-capacity-billing)

 - **Odłączony**  
 O odłączony z poziomu opcji wdrażania na platformie Azure, wdrażanie i używanie usługi Azure Stack bez połączenia z Internetem. Jednak z wdrożeniem odłączonego, są ograniczone do magazynu tożsamości usługi AD FS i model rozliczeń oparty na pojemności.
    - [Zarejestruj bez połączenia przy użyciu usługi Azure Stack **pojemności** model rozliczeń ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Określ nazwę unikatową rejestracji, do użycia 
Podczas rejestrowania usługi Azure Stack na platformie Azure musisz podać nazwę unikatową rejestracji. Prosty sposób, aby skojarzyć subskrypcję usługi Azure Stack przy użyciu rejestracja w usłudze Azure jest użycie usługi Azure Stack **identyfikator chmury**. 

> [!NOTE]
> Azure rejestracje stosu przy użyciu model rozliczeń oparty na pojemności, należy zmienić unikatową nazwę, rejestrując ponownie po wygaśnięciu tych subskrypcji rocznej, chyba że użytkownik [Usuń wygasłe rejestracji](azure-stack-registration.md#change-the-subscription-you-use) i ponownie zarejestrowanie w usłudze Azure.

Aby określić identyfikator chmury dla danego wdrożenia usługi Azure Stack, Otwórz program PowerShell jako administrator na komputerze, nie mogą uzyskiwać dostęp do uprzywilejowanych punktu końcowego, uruchom następujące polecenia i Zarejestruj **CloudID** wartość: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: get-azurestackstampinformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Zarejestruj się związanych z rozliczeniami zgodnie z rzeczywistym użyciem

Wykonaj następujące kroki, aby zarejestrować usługi Azure Stack z platformą Azure za pomocą modelu rozliczeniowego płatności jako — rzeczywiste użycie.

> [!Note]  
> Wszystkie te kroki należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego (program ten). Aby uzyskać szczegółowe informacje o program ten, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md).

Środowisk sieciowych można uzyskać dostęp do Internetu i platformy Azure. Dla tych środowisk należy zarejestrować dostawcę zasobów usługi Azure Stack przy użyciu platformy Azure, a następnie skonfiguruj model rozliczeń.

1. Można zarejestrować dostawcy zasobów usługi Azure Stack przy użyciu platformy Azure, uruchom program PowerShell ISE z uprawnieniami administracyjnymi, a następnie użyj następujących poleceń cmdlet programu PowerShell przy użyciu **EnvironmentName** zestaw parametrów na typ odpowiednią subskrypcję platformy Azure (patrz Parametry poniżej).

2. Dodaj konto platformy Azure, która służy do rejestrowania usługi Azure Stack. Aby dodać konto, należy uruchomić **Add-AzureRmAccount** polecenia cmdlet. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure i może być konieczne użycie uwierzytelnianie 2-etapowe, na podstawie konfiguracji konta.

   ```PowerShell
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Opis |  
   |-----|-----|
   | EnvironmentName | Nazwa środowiska subskrypcji platformy Azure w chmurze. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, czy przy użyciu subskrypcji platformy Azure (Chiny) **AzureChinaCloud**.  |

3. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, aby wybrać subskrypcję, który chcesz użyć:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów usługi Azure Stack, w ramach subskrypcji platformy Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Uruchom program PowerShell ISE jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** utworzony katalog, gdy możesz [pobrać narzędzia usługi Azure Stack](#bkmk_tools). Importuj **RegisterWithAzure.psm1** modułu przy użyciu programu PowerShell:

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Następnie w tej samej sesji programu PowerShell, upewnij się, że użytkownik jest zalogowany do poprawnego kontekstu programu PowerShell platformy Azure. To jest konto platformy Azure, którego użyto do zarejestrowania dostawcy zasobów usługi Azure Stack powyżej. Program PowerShell w celu uruchomienia:

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Opis |  
   |-----|-----|
   | EnvironmentName | Nazwa środowiska subskrypcji platformy Azure w chmurze. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, czy przy użyciu subskrypcji platformy Azure (Chiny) **AzureChinaCloud**.  |

7. W tej samej sesji programu PowerShell, uruchom **AzsRegistration zestaw** polecenia cmdlet. Program PowerShell w celu uruchomienia:  

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Aby uzyskać więcej informacji na temat polecenia cmdlet Set-AzsRegistration, zobacz [dokumentacja rejestracji](#registration-reference).

  Proces potrwa od 10 do 15 minut. Po zakończeniu wykonywania polecenia zostanie wyświetlony komunikat **"środowiska jest teraz zarejestrowany i aktywować przy użyciu podanych parametrów."**

## <a name="register-connected-with-capacity-billing"></a>Zarejestruj się związanych z rozliczeniami pojemności

Wykonaj następujące kroki, aby zarejestrować usługi Azure Stack z platformą Azure za pomocą modelu rozliczeniowego płatności jako — rzeczywiste użycie.

> [!Note]  
> Wszystkie te kroki należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego (program ten). Aby uzyskać szczegółowe informacje o program ten, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md).

Środowisk sieciowych można uzyskać dostęp do Internetu i platformy Azure. Dla tych środowisk należy zarejestrować dostawcę zasobów usługi Azure Stack przy użyciu platformy Azure, a następnie skonfiguruj model rozliczeń.

1. Można zarejestrować dostawcy zasobów usługi Azure Stack przy użyciu platformy Azure, uruchom program PowerShell ISE z uprawnieniami administracyjnymi, a następnie użyj następujących poleceń cmdlet programu PowerShell przy użyciu **EnvironmentName** zestaw parametrów na typ odpowiednią subskrypcję platformy Azure (patrz Parametry poniżej).

2. Dodaj konto platformy Azure, która służy do rejestrowania usługi Azure Stack. Aby dodać konto, należy uruchomić **Add-AzureRmAccount** polecenia cmdlet. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure i może być konieczne użycie uwierzytelnianie 2-etapowe, na podstawie konfiguracji konta.

   ```PowerShell  
      Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Opis |  
   |-----|-----|
   | EnvironmentName | Nazwa środowiska subskrypcji platformy Azure w chmurze. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, czy przy użyciu subskrypcji platformy Azure (Chiny) **AzureChinaCloud**.  |

3. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, aby wybrać subskrypcję, który chcesz użyć:  

   ```PowerShell  
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów usługi Azure Stack, w ramach subskrypcji platformy Azure:

   ```PowerShell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Uruchom program PowerShell ISE jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** utworzony katalog, gdy możesz [pobrać narzędzia usługi Azure Stack](#bkmk_tools). Importuj **RegisterWithAzure.psm1** modułu przy użyciu programu PowerShell:

  ```PowerShell  
  $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
  ```
   > [!Note]  
   > Można wyłączyć raportowanie z parametrem UsageReportingEnabled dla użycia **AzsRegistration zestaw** polecenia cmdlet przez ustawienie parametru na wartość false. 
   
  Aby uzyskać więcej informacji na temat polecenia cmdlet Set-AzsRegistration, zobacz [dokumentacja rejestracji](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Zarejestruj bez połączenia z rozliczeniem pojemności

W przypadku rejestracji usługi Azure Stack w środowisku bez połączenia (bez łączności z Internetem), należy do pobrania tokenu rejestracji ze środowiska usługi Azure Stack, a następnie użyć tego tokenu na komputerze, który umożliwia połączenie z platformy Azure i ma [programu PowerShell dla usługi Azure Stack zainstalowane](#bkmk_powershell).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Pobrania tokenu rejestracji ze środowiska usługi Azure Stack

1. Uruchom program PowerShell ISE jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** utworzony katalog, gdy możesz [pobrać narzędzia usługi Azure Stack](#bkmk_tools). Importuj **RegisterWithAzure.psm1** modułu:  

   ```PowerShell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Aby uzyskać tokenu rejestracji, uruchom następujące polecenia cmdlet programu PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Aby uzyskać więcej informacji na temat polecenia cmdlet Get-AzsRegistrationToken, zobacz [dokumentacja rejestracji](#registration-reference).

   > [!Tip]  
   > Token rejestracji jest zapisany w określonym dla pliku *$FilePathForRegistrationToken*. Ścieżka pliku lub nazwę pliku można zmienić, według uznania.

3. Zapisz ten token rejestracji na platformie Azure do użytku połączonych maszyn. Możesz skopiować plik lub tekst z $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Łączenie się z platformy Azure i Zarejestruj się

Na komputerze, na którym jest połączony z Internetem wykonaj te same kroki, aby zaimportować moduł RegisterWithAzure.psm1 i zaloguj się do poprawnego kontekstu programu Azure Powershell. Następnie wywołaj AzsEnvironment rejestru. Określ token rejestracji do rejestrowania na platformie Azure. W przypadku rejestracji więcej niż jedno wystąpienie usługi Azure Stack przy użyciu tego samego Identyfikatora subskrypcji platformy Azure, należy określić nazwę unikatową rejestracji. Uruchom następujące polecenie cmdlet:

  ```PowerShell  
  $registrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken  -RegistrationName $RegistrationName
  ```

Opcjonalnie służy polecenie cmdlet Get-Content wskaż plik zawierający tokenu programu rejestracji:

  ```PowerShell  
  $registrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $registrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Zapisz nazwę zasobu rejestracji i tokenu rejestracji do użytku w przyszłości.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Pobieranie klucza aktywacji z zasobu rejestracja w usłudze Azure

Następnie należy pobrać klucza aktywacji z zasobu rejestracji na platformie Azure utworzone podczas AzsEnvironment rejestru.

Aby uzyskać klucz aktywacji, uruchom następujące polecenia cmdlet programu PowerShell:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Klucz aktywacji jest zapisywany w pliku określonym dla *$KeyOutputFilePath*. Ścieżka pliku lub nazwę pliku można zmienić, według uznania.

### <a name="create-an-activation-resource-in-azure-stack"></a>Utwórz zasób usługi aktywacji w usłudze Azure Stack

Wróć do środowiska usługi Azure Stack, przy użyciu pliku lub wiadomości SMS z klucza aktywacji utworzone na podstawie Get AzsActivationKey. Następnie utwórz zasób usługi aktywacji w usłudze Azure Stack przy użyciu tego klucza aktywacji. Aby utworzyć zasób usługi aktywacji, uruchom następujące polecenia cmdlet programu PowerShell:  

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Opcjonalnie służy polecenie cmdlet Get-Content wskaż plik zawierający tokenu programu rejestracji:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Weryfikuj rejestrację w usłudze Azure Stack

Wykonaj następujące kroki, aby sprawdzić, usługi Azure Stack po pomyślnym zarejestrowaniu za pomocą platformy Azure.

1. Zaloguj się do usługi Azure Stack [portalu administracyjnego](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;region >. &lt;fqdn >*.
2. Wybierz **wszystkich usług**, a następnie w obszarze **administracji** kategorii, wybierz opcję **zarządzania Marketplace** > **DodajplatformyAzure**.

Jeśli zobaczysz listę dostępnych na platformie Azure (takich jak WordPress) elementów, proces aktywacji zakończyło się pomyślnie. Jednak w środowiskach rozłączonych nie zobaczysz elementów portalu Azure marketplace w witrynie marketplace usługi Azure Stack.

> [!Note]  
> Po zakończeniu rejestracji pojawi się nie jest już aktywne ostrzeżenie nie rejestrowania.

## <a name="renew-or-change-registration"></a>Odnów lub zmienić rejestracji

### <a name="renew-or-change-registration-in-connected-environments"></a>Odnów lub zmienić rejestracji w środowiskach połączone

Musisz zaktualizować lub odnowić rejestrację w następujących okolicznościach:

- Po odnowieniu subskrypcji rocznej oparty na pojemności.
- Po zmianie modelu rozliczeń.
- Po przeskalowaniu zmiany (Dodaj/Usuń węzły) dla rozliczania opartego na pojemności.

#### <a name="change-the-subscription-you-use"></a>Zmień subskrypcję, których używasz

Jeśli chcesz zmienić subskrypcję możesz użyć, należy najpierw uruchomić **AzsRegistration Usuń** polecenia cmdlet, upewnij się, że użytkownik jest zalogowany do poprawnego kontekstu programu Azure PowerShell, a na koniec uruchom **AzsRegistration zestawu**  parametrami zmiany w tym \<model rozliczeń\>:

  ```PowerShell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Zmień model rozliczeń lub jak oferują funkcje

Jeśli chcesz zmienić model rozliczeń lub zaoferować funkcji dla instalacji, można wywołać funkcję rejestracji, aby ustawić nowe wartości. Musisz najpierw usunąć bieżący rejestracji:

  ```PowerShell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Odnów lub zmienić rejestracji w środowiskach rozłączonych

Musisz zaktualizować lub odnowić rejestrację w następujących okolicznościach:

- Po odnowieniu subskrypcji rocznej oparty na pojemności.
- Po zmianie modelu rozliczeń.
- Po przeskalowaniu zmiany (Dodaj/Usuń węzły) dla rozliczania opartego na pojemności.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Usuń zasób aktywacji z usługi Azure Stack

Najpierw należy usunąć zasób aktywacji z usługi Azure Stack, a następnie zasobu rejestracji na platformie Azure.  

Aby usunąć zasób aktywacji w usłudze Azure Stack, uruchom następujące polecenia cmdlet programu PowerShell w środowisku usługi Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Następnie można usunąć zasobu rejestracji na platformie Azure, upewnij się, znajdują się na platformie Azure połączoną komputer, zaloguj się do poprawnego kontekstu programu Azure PowerShell i uruchom odpowiednich poleceń cmdlet programu PowerShell, zgodnie z poniższym opisem.

Możesz użyć tokenu rejestracji użyty do utworzenia zasobu:  

  ```Powershell
  $registrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $registrationToken
  ```

Lub możesz użyć nazwy rejestracji:

  ```Powershell
  $registrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $registrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Zarejestruj ponownie, wykonując rozłączona kroki

Teraz mogła całkowicie wyrejestrować w scenariuszu rozłączonych i należy powtórzyć kroki rejestrowania środowiska usługi Azure Stack w przypadku odłączonych.

### <a name="disable-or-enable-usage-reporting"></a>Wyłącz lub Włącz raportowanie użycia

W środowiskach usługi Azure Stack, które korzystają z modelu rozliczeń pojemności Wyłącz funkcję raportowania z użycia **UsageReportingEnabled** za pomocą parametru **AzsRegistration zestaw** lub  **Get-AzsRegistrationToken** polecenia cmdlet. Usługa Azure Stack raporty metryk użycia domyślnie. Operatory o pojemności używa lub obsługa środowiska bez połączenia należy wyłączyć raportowanie użycia.

#### <a name="with-a-connected-azure-stack"></a>Za pomocą połączonej usługi Azure Stack

   ```PowerShell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Bez połączenia usługi Azure Stack

1. Aby zmienić tokenu rejestracji, uruchom następujące polecenia cmdlet programu PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Token rejestracji jest zapisany w określonym dla pliku *$FilePathForRegistrationToken*. Ścieżka pliku lub nazwę pliku można zmienić, według uznania.

2. Zapisz ten token rejestracji na platformie Azure do użytku połączonych maszyn. Możesz skopiować plik lub tekst z $FilePathForRegistrationToken.

## <a name="move-a-registration-resource"></a>Przenieś zasób rejestracji
Przenoszenie zasobów rejestracji między grupami zasobów w ramach tej samej subskrypcji **jest** obsługiwane we wszystkich środowiskach. Jednak przenoszenia zasobu rejestracji między subskrypcjami jest obsługiwana tylko dla dostawców usług kryptograficznych, gdy obie subskrypcje prowadzić do tego samego identyfikatora partnera. Aby uzyskać więcej informacji na temat przenoszenia zasobów do nowej grupy zasobów, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Dokumentacja rejestracji

### <a name="set-azsregistration"></a>Zestaw AzsRegistration

Zestaw AzsRegistration służy do rejestrowania usługi Azure Stack na platformie Azure i włączyć lub wyłączyć oferty elementów w portalu marketplace i raportowanie użycia.

Aby uruchomić polecenia cmdlet, potrzebne są:
- Globalny subskrypcji platformy Azure z dowolnego typu.
- Użytkownik musi również być zalogowany w programie Azure PowerShell przy użyciu konta, który jest właścicielem lub współautorem tej subskrypcji.

```PowerShell
    Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
   ```

| Parametr | Typ | Opis |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Poświadczenia używane do [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Nazwa użytkownika jest w formacie **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Ciąg | Wstępnie skonfigurowane zdalnego programu PowerShell konsoli, która udostępnia możliwości, takich jak zbieranie danych dziennika i innych post zadania związane z wdrażaniem. Aby dowiedzieć się więcej, zapoznaj się [przy użyciu uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artykułu. |
| AzureContext | PSObject |  |
| ResourceGroupName | Ciąg |  |
| ResourceGroupLocation | Ciąg |  |
| BillingModel | Ciąg | Model rozliczeń, który korzysta z subskrypcji. Dozwolone wartości tego parametru to: pojemności, PayAsYouUse i rozwoju. |
| MarketplaceSyndicationEnabled | PRAWDA/FAŁSZ | Określa, czy funkcja zarządzania portalu marketplace jest dostępna w portalu. Ustaw wartość true, jeśli rejestrowanie przy użyciu łączności z Internetem. Ustawienie wartości false, jeśli rejestrowanie w środowiskach rozłączonych. Dla odłączonych rejestracji [narzędzie w trybie offline syndykacji](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) może służyć do pobierania elementów portalu marketplace. |
| UsageReportingEnabled | PRAWDA/FAŁSZ | Usługa Azure Stack raporty metryk użycia domyślnie. Operatory o pojemności używa lub obsługa środowiska bez połączenia należy wyłączyć raportowanie użycia. Dozwolone wartości tego parametru to: True i False. |
| AgreementNumber | Ciąg |  |
| registrationName | Ciąg | Ustawić unikatową nazwę dla rejestracji, jeśli skrypt rejestracji są uruchomione na więcej niż jedno wystąpienie usługi Azure Stack przy użyciu tego samego Azure identyfikator subskrypcji. Parametr ma wartość domyślną **AzureStackRegistration**. Jednak jeśli używasz tej samej nazwie w więcej niż jedno wystąpienie usługi Azure Stack, skrypt zakończy się niepowodzeniem. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken spowoduje wygenerowanie tokenu rejestracji z poziomu parametrów wejściowych.

```PowerShell  
    Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parametr | Typ | Opis |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Poświadczenia używane do [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Nazwa użytkownika jest w formacie **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Ciąg |  Wstępnie skonfigurowane zdalnego programu PowerShell konsoli, która udostępnia możliwości, takich jak zbieranie danych dziennika i innych post zadania związane z wdrażaniem. Aby dowiedzieć się więcej, zapoznaj się [przy użyciu uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artykułu. |
| AzureContext | PSObject |  |
| ResourceGroupName | Ciąg |  |
| ResourceGroupLocation | Ciąg |  |
| BillingModel | Ciąg | Model rozliczeń, który korzysta z subskrypcji. Dozwolone wartości tego parametru to: pojemności, PayAsYouUse i rozwoju. |
| MarketplaceSyndicationEnabled | PRAWDA/FAŁSZ |  |
| UsageReportingEnabled | PRAWDA/FAŁSZ | Usługa Azure Stack raporty metryk użycia domyślnie. Operatory o pojemności używa lub obsługa środowiska bez połączenia należy wyłączyć raportowanie użycia. Dozwolone wartości tego parametru to: True i False. |
| AgreementNumber | Ciąg |  |


## <a name="next-steps"></a>Kolejne kroki

[Pobieranie elementów portalu marketplace z platformy Azure](azure-stack-download-azure-marketplace-item.md)
