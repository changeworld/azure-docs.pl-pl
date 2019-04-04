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
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: brbartle
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 70408f11c8656fb62c8613777d1837d934f67074
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487570"
---
# <a name="register-azure-stack-with-azure"></a>Rejestrowania usługi Azure Stack na platformie Azure

Zarejestrowanie usługi Azure Stack na platformie Azure umożliwia pobranie elementów platformy handlowej z platformy Azure i skonfigurowanie danych handlowych odsyłanych do firmy Microsoft. Po zarejestrowaniu usługi Azure Stack, użycie jest zgłaszany do platformy Azure handlu i będzie widoczny w obszarze Azure rozliczeń identyfikator subskrypcji używany do rejestracji.

Informacje przedstawione w tym artykule opisano rejestrowanie systemów usługi Azure Stack zintegrowane z platformą Azure. Aby uzyskać informacji na temat rejestrowania ASDK z platformą Azure, zobacz [rejestracji w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-register) w dokumentacji ASDK.

> [!IMPORTANT]  
> Rejestracja jest wymagany do obsługi pełną funkcjonalność usługi Azure Stack, w tym elementy w portalu marketplace oferty. Ponadto można narusza postanowienia licencyjne, jeżeli nie zarejestrujesz się tak, gdy za pomocą modelu rozliczeniowego płatności za użycie w usłudze Azure Stack. Aby dowiedzieć się więcej o usłudze Azure Stack, modele licencjonowania, zobacz [jak strony zakupu](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Wymagania wstępne

Przed zarejestrowaniem są potrzebne następujące elementy w miejscu:

 - Weryfikowanie poświadczeń
 - Ustawianie trybu języka programu PowerShell
 - Instalowanie programu PowerShell dla usługi Azure Stack
 - Pobierz narzędzia usługi Azure Stack
 - Określić scenariusz rejestracji

### <a name="verify-your-credentials"></a>Weryfikowanie poświadczeń

Przed zarejestrowaniem usługi Azure Stack przy użyciu platformy Azure, musisz mieć:

- Identyfikator subskrypcji dla subskrypcji platformy Azure. Umowy EA, dostawcy usług Kryptograficznych lub dostawcy usług Kryptograficznych udostępnionych usług, których subskrypcje są obsługiwane w przypadku rejestracji. Dostawcy usług kryptograficznych, musisz zdecydować, czy [korzystania z subskrypcji dostawcy usług Kryptograficznych lub APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Aby uzyskać identyfikator, zaloguj się do platformy Azure, kliknij przycisk **wszystkich usług**. Następnie w obszarze **ogólne** kategorii, wybierz opcję **subskrypcje**, kliknij subskrypcję, dla którego chcesz użyć, a następnie w obszarze **Essentials** można znaleźć identyfikatora subskrypcji.

  > [!Note]  
  > Subskrypcje w chmurze (Niemcy) nie są obecnie obsługiwane.

- Nazwa użytkownika i hasło dla konta które jest właścicielem subskrypcji.

- Konto użytkownika musi mieć dostęp do subskrypcji platformy Azure i masz uprawnienia do tworzenia aplikacji z obsługą tożsamości i nazwy główne usług w katalogu skojarzonego z posiadaną subskrypcją. Firma Microsoft zaleca rejestrowania usługi Azure Stack przy użyciu platformy Azure przy użyciu uprawnień administracyjnych. Aby uzyskać więcej informacji na temat tworzenia definicji roli niestandardowej, która ogranicza dostęp do Twojej subskrypcji do rejestracji, zobacz [utworzyć rolę rejestracji dla usługi Azure Stack](azure-stack-registration-role.md).

- Dostawca zasobów usługi Azure Stack zarejestrowany (zobacz w poniższej sekcji rejestrowanie dostawcy zasobów platformy Azure Stack, aby uzyskać szczegółowe informacje).

Po zarejestrowaniu uprawnienia administratora globalnego usługi Azure Active Directory nie jest wymagana. Jednak niektóre operacje mogą wymagać poświadczenia administratora globalnego. Na przykład skryptu Instalatora dostawcy zasobów lub nową funkcję wymagające uprawnienia przyznawane. Możesz tymczasowo przywrócić uprawnienia administratora globalnego dla konta lub użyj konta oddzielne administratora globalnego, który jest właścicielem *domyślne subskrypcję dostawcy*.

Użytkownik, który rejestruje usługi Azure Stack jest właścicielem usługi nazwy głównej usługi Azure Active Directory. Tylko użytkownik, który jest zarejestrowany w usłudze Azure Stack można zmodyfikować rejestracji w usłudze Azure Stack. Jeśli użytkownik bez uprawnień administratora, który nie jest właścicielem nazwy głównej usługi rejestracji próbuje zarejestrować lub ponownie zarejestrować w usłudze Azure Stack, które mogą oni natrafić odpowiedź 403. Odpowiedź 403 wskazuje, że użytkownik nie ma wystarczających uprawnień do ukończenia tej operacji.

Jeśli nie masz subskrypcji platformy Azure, która spełnia te wymagania, możesz to zrobić [Utwórz w tym miejscu Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie usługi Azure Stack jest naliczana opłata bez kosztów związanych z subskrypcją platformy Azure.

> [!NOTE]
> Jeśli masz więcej niż jednej usługi Azure Stack, najlepszym rozwiązaniem jest zarejestrować każdej usługi Azure Stack do jego własnej subskrypcji. Ułatwi umożliwiające śledzenie użycia.

### <a name="powershell-language-mode"></a>Tryb języka programu PowerShell

Aby pomyślnie zarejestrować usługi Azure Stack, tryb języka programu PowerShell musi być równa **FullLanguageMode**.  Aby sprawdzić, czy bieżący tryb język jest ustawiony na pełne, Otwórz okno programu PowerShell z podwyższonym i uruchom następujące polecenia cmdlet programu PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Upewnij się, zwraca dane wyjściowe **FullLanguageMode**. Jeśli inny tryb języka jest zwracany, rejestracji musi zostać uruchomione na innym komputerze lub tryb języka musi być równa **FullLanguageMode** przed kontynuowaniem.

### <a name="install-powershell-for-azure-stack"></a>Instalowanie programu PowerShell dla usługi Azure Stack

Najnowszą wersję programu PowerShell dla usługi Azure Stack umożliwia rejestrowania na platformie Azure.

Jeśli najnowsza wersja nie jest jeszcze zainstalowany, zobacz [zainstalować program PowerShell dla usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).

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

Aby określić identyfikator chmury dla danego wdrożenia usługi Azure Stack, Otwórz program PowerShell jako administrator na komputerze, który może uzyskiwać dostęp uprzywilejowany punktu końcowego, uruchom następujące polecenia i zapisać **CloudID** wartość: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Zarejestruj się związanych z rozliczeniami zgodnie z rzeczywistym użyciem

Wykonaj następujące kroki, aby zarejestrować usługi Azure Stack z platformą Azure za pomocą modelu rozliczeniowego płatności jako — rzeczywiste użycie.

> [!Note]  
> Wszystkie te kroki należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego (program ten). Aby uzyskać szczegółowe informacje o program ten, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md).

Środowisk sieciowych można uzyskać dostęp do Internetu i platformy Azure. Dla tych środowisk należy zarejestrować dostawcę zasobów usługi Azure Stack przy użyciu platformy Azure, a następnie skonfiguruj model rozliczeń.

1. Można zarejestrować dostawcy zasobów usługi Azure Stack przy użyciu platformy Azure, uruchom program PowerShell ISE z uprawnieniami administracyjnymi, a następnie użyj następujących poleceń cmdlet programu PowerShell przy użyciu **EnvironmentName** zestaw parametrów na typ odpowiednią subskrypcję platformy Azure (patrz Parametry poniżej).

2. Dodaj konto platformy Azure, która służy do rejestrowania usługi Azure Stack. Aby dodać konto, należy uruchomić **Add-AzureRmAccount** polecenia cmdlet. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure i może być konieczne użycie uwierzytelnianie 2-etapowe, na podstawie konfiguracji konta.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Opis |  
   |-----|-----|
   | EnvironmentName | Nazwa środowiska subskrypcji platformy Azure w chmurze. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, czy przy użyciu subskrypcji platformy Azure (Chiny) **AzureChinaCloud**.  |

3. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, aby wybrać subskrypcję, który chcesz użyć:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów usługi Azure Stack, w ramach subskrypcji platformy Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Uruchom program PowerShell ISE jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas pobierania narzędzia Azure Stack. Importuj **RegisterWithAzure.psm1** modułu przy użyciu programu PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Następnie w tej samej sesji programu PowerShell, upewnij się, że użytkownik jest zalogowany do poprawnego kontekstu programu PowerShell platformy Azure. To jest konto platformy Azure, który został użyty do wcześniej Zarejestruj dostawcę zasobów usługi Azure Stack. Program PowerShell w celu uruchomienia:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parametr | Opis |  
   |-----|-----|
   | EnvironmentName | Nazwa środowiska subskrypcji platformy Azure w chmurze. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, czy przy użyciu subskrypcji platformy Azure (Chiny) **AzureChinaCloud**.  |

7. W tej samej sesji programu PowerShell, uruchom **AzsRegistration zestaw** polecenia cmdlet. Program PowerShell w celu uruchomienia:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Aby uzyskać więcej informacji na temat polecenia cmdlet Set-AzsRegistration, zobacz [dokumentacja rejestracji](#registration-reference).

   Ten proces może potrwać od 10 do 15 minut. Po zakończeniu wykonywania polecenia zostanie wyświetlony komunikat **"środowiska jest teraz zarejestrowany i aktywować przy użyciu podanych parametrów."**

## <a name="register-connected-with-capacity-billing"></a>Zarejestruj się związanych z rozliczeniami pojemności

Wykonaj następujące kroki, aby zarejestrować usługi Azure Stack z platformą Azure za pomocą modelu rozliczeniowego płatności jako — rzeczywiste użycie.

> [!Note]  
> Wszystkie te kroki należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego (program ten). Aby uzyskać szczegółowe informacje o program ten, zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](azure-stack-privileged-endpoint.md).

Środowisk sieciowych można uzyskać dostęp do Internetu i platformy Azure. Dla tych środowisk należy zarejestrować dostawcę zasobów usługi Azure Stack przy użyciu platformy Azure, a następnie skonfiguruj model rozliczeń.

1. Można zarejestrować dostawcy zasobów usługi Azure Stack przy użyciu platformy Azure, uruchom program PowerShell ISE z uprawnieniami administracyjnymi, a następnie użyj następujących poleceń cmdlet programu PowerShell przy użyciu **EnvironmentName** zestaw parametrów na typ odpowiednią subskrypcję platformy Azure (patrz Parametry poniżej).

2. Dodaj konto platformy Azure, która służy do rejestrowania usługi Azure Stack. Aby dodać konto, należy uruchomić **Add-AzureRmAccount** polecenia cmdlet. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta platformy Azure i może być konieczne użycie uwierzytelnianie 2-etapowe, na podstawie konfiguracji konta.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parametr | Opis |  
   |-----|-----|
   | EnvironmentName | Nazwa środowiska subskrypcji platformy Azure w chmurze. Nazwy środowiska obsługiwane są **AzureCloud**, **AzureUSGovernment**, czy przy użyciu subskrypcji platformy Azure (Chiny) **AzureChinaCloud**.  |

3. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, aby wybrać subskrypcję, który chcesz użyć:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów usługi Azure Stack, w ramach subskrypcji platformy Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Uruchom program PowerShell ISE jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas pobierania narzędzia Azure Stack. Importuj **RegisterWithAzure.psm1** modułu przy użyciu programu PowerShell:

   ```powershell  
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

W przypadku rejestracji usługi Azure Stack w środowisku bez połączenia (bez łączności z Internetem), należy do pobrania tokenu rejestracji ze środowiska usługi Azure Stack, a następnie użyć tego tokenu na komputerze, który umożliwia połączenie z platformy Azure i zapewnia środowiska PowerShell dla usługi Azure Stack zainstalowane.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Pobrania tokenu rejestracji ze środowiska usługi Azure Stack

1. Uruchom program PowerShell ISE jako administrator i przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas pobierania narzędzia Azure Stack. Importuj **RegisterWithAzure.psm1** modułu:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Aby uzyskać tokenu rejestracji, uruchom następujące polecenia cmdlet programu PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Aby uzyskać więcej informacji na temat polecenia cmdlet Get-AzsRegistrationToken, zobacz [dokumentacja rejestracji](#registration-reference).

   > [!Tip]  
   > Token rejestracji jest zapisany w określonym dla pliku *$FilePathForRegistrationToken*. Ścieżka pliku lub nazwę pliku można zmienić, według uznania.

3. Zapisz ten token rejestracji na platformie Azure do użytku połączonych maszyn. Możesz skopiować plik lub tekst z $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Łączenie się z platformy Azure i Zarejestruj się

Na komputerze, na którym jest połączony z Internetem wykonaj te same kroki, aby zaimportować moduł RegisterWithAzure.psm1 i zaloguj się do poprawnego kontekstu programu Azure Powershell. Następnie wywołaj AzsEnvironment rejestru. Określ token rejestracji do rejestrowania na platformie Azure. W przypadku rejestracji więcej niż jedno wystąpienie usługi Azure Stack przy użyciu tego samego Identyfikatora subskrypcji platformy Azure, należy określić nazwę unikatową rejestracji. Uruchom następujące polecenie cmdlet:

  ```powershell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

Opcjonalnie służy polecenie cmdlet Get-Content wskaż plik zawierający tokenu programu rejestracji:

  ```powershell  
  $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
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

Możesz użyć **zarządzanie regionami** Kafelek, aby sprawdzić, czy rejestracji w usłudze Azure Stack zakończyła się pomyślnie. Ten Kafelek jest dostępny na domyślny pulpit nawigacyjny w portalu administracyjnym. Stan mogą być zarejestrowane lub niezarejestrowany. Jeśli nie zarejestrowano, pokazuje również identyfikator subskrypcji platformy Azure umożliwia rejestrowanie usługi Azure Stack oraz rejestracji grupy zasobów i nazwę.

1. Zaloguj się do [portalu administratora usługi Azure Stack](https://adminportal.local.azurestack.external).

2. Na pulpicie nawigacyjnym wybierz **zarządzanie regionami**.

3. Wybierz **właściwości**. Ten blok, stan i szczegółowe cechy danego środowiska. Możliwe stany **zarejestrowanej** lub **niezarejestrowany**.

    [![Kafelek Zarządzanie region](media/azure-stack-registration/admin1sm.png "Kafelek Zarządzanie regionu")](media/azure-stack-registration/admin1.png#lightbox)

    Jeśli nie zarejestrowano, właściwości obejmują:
    
    - **Identyfikator subskrypcji rejestracji**: Identyfikator subskrypcji platformy Azure, rejestrowane i powiązanych z usługi Azure Stack
    - **Grupa zasobów rejestracji**: Grupa zasobów platformy Azure w ramach subskrypcji skojarzonych zawierającą odpowiednie zasoby usługi Azure Stack.

4. Użyj witryny Azure portal, aby wyświetlić rejestracje aplikacji usługi Azure Stack. Zaloguj się do witryny Azure portal przy użyciu konta skojarzonego z subskrypcją używane do rejestrowania usługi Azure Stack. Przełącz się do dzierżawy skojarzonej z usługą Azure Stack.
5. Przejdź do **usługi Azure Active Directory > Rejestracje aplikacji > Wyświetl wszystkie aplikacje**.

    ![Rejestracje aplikacji](media/azure-stack-registration/app-registrations.png)

    Rejestracje aplikacji w usłudze Azure Stack mają prefiks **usługi Azure Stack**.

Alternatywnie można sprawdzić, czy rejestracja powiodła się przy użyciu funkcji zarządzania w portalu Marketplace. Jeśli zobaczysz listę elementów portalu marketplace w bloku zarządzania portalu Marketplace, proces rejestracji zakończyło się pomyślnie. Jednak w środowiskach rozłączonych nie będzie można zobaczyć elementy portalu marketplace w portalu Marketplace zarządzania.

> [!NOTE]
> Po zakończeniu rejestracji pojawi się nie jest już aktywne ostrzeżenie nie rejestrowania. W scenariuszach bez połączenia zostanie wyświetlony komunikat w portalu Marketplace management prośbą do rejestrowania i aktywowania usługi Azure Stack, nawet wtedy, gdy został pomyślnie zarejestrowany.

## <a name="renew-or-change-registration"></a>Odnów lub zmienić rejestracji

### <a name="renew-or-change-registration-in-connected-environments"></a>Odnów lub zmienić rejestracji w środowiskach połączone

Musisz zaktualizować lub odnowić rejestrację w następujących okolicznościach:

- Po odnowieniu subskrypcji rocznej oparty na pojemności.
- Po zmianie modelu rozliczeń.
- Po przeskalowaniu zmiany (Dodaj/Usuń węzły) dla rozliczania opartego na pojemności.

#### <a name="change-the-subscription-you-use"></a>Zmień subskrypcję, których używasz

Jeśli chcesz zmienić subskrypcję możesz użyć, należy najpierw uruchomić **AzsRegistration Usuń** polecenia cmdlet, upewnij się, że użytkownik jest zalogowany do poprawnego kontekstu programu Azure PowerShell, a na koniec uruchom **AzsRegistration zestawu**  parametrami zmiany w tym \<model rozliczeń\>:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Zmień model rozliczeń lub jak oferują funkcje

Jeśli chcesz zmienić model rozliczeń lub zaoferować funkcji dla instalacji, można wywołać funkcję rejestracji, aby ustawić nowe wartości. Musisz najpierw usunąć bieżący rejestracji:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Odnów lub zmienić rejestracji w środowiskach rozłączonych

Musisz zaktualizować lub odnowić rejestrację w następujących okolicznościach:

- Po odnowieniu subskrypcji rocznej oparty na pojemności.
- Po zmianie modelu rozliczeń.
- Po przeskalowaniu zmiany (Dodaj/Usuń węzły) dla rozliczania opartego na pojemności.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Usuń zasób aktywacji z usługi Azure Stack

Należy najpierw usunąć zasób aktywacji z usługi Azure Stack, a następnie zasobu rejestracji na platformie Azure.  

Aby usunąć zasób aktywacji w usłudze Azure Stack, uruchom następujące polecenia cmdlet programu PowerShell w środowisku usługi Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Następnie można usunąć zasobu rejestracji na platformie Azure, upewnij się, znajdują się na platformie Azure połączoną komputer, zaloguj się do poprawnego kontekstu programu Azure PowerShell i uruchom odpowiednich poleceń cmdlet programu PowerShell, zgodnie z poniższym opisem.

Możesz użyć tokenu rejestracji użyty do utworzenia zasobu:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Lub możesz użyć nazwy rejestracji:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Zarejestruj ponownie, wykonując rozłączona kroki

Teraz mogła całkowicie wyrejestrować w scenariuszu rozłączonych i należy powtórzyć kroki rejestrowania środowiska usługi Azure Stack w przypadku odłączonych.

### <a name="disable-or-enable-usage-reporting"></a>Wyłącz lub Włącz raportowanie użycia

W środowiskach usługi Azure Stack, które korzystają z modelu rozliczeń pojemności Wyłącz funkcję raportowania z użycia **UsageReportingEnabled** za pomocą parametru **AzsRegistration zestaw** lub  **Get-AzsRegistrationToken** polecenia cmdlet. Usługa Azure Stack raporty metryk użycia domyślnie. Operatory o pojemności używa lub obsługa środowiska bez połączenia trzeba wyłączyć raportowanie użycia.

#### <a name="with-a-connected-azure-stack"></a>Za pomocą połączonej usługi Azure Stack

   ```powershell  
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

### <a name="set-azsregistration"></a>Set-AzsRegistration

Zestaw AzsRegistration służy do rejestrowania usługi Azure Stack na platformie Azure i włączyć lub wyłączyć oferty elementów w portalu marketplace i raportowanie użycia.

Aby uruchomić polecenia cmdlet, potrzebne są:
- Globalny subskrypcji platformy Azure z dowolnego typu.
- Użytkownik musi również być zalogowany w programie Azure PowerShell przy użyciu konta, który jest właścicielem lub współautorem tej subskrypcji.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Parametr | Type | Opis |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Poświadczenia używane do [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Nazwa użytkownika jest w formacie **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String | Wstępnie skonfigurowane zdalnego programu PowerShell konsoli, która udostępnia możliwości, takich jak zbieranie danych dziennika i innych post zadania związane z wdrażaniem. Aby dowiedzieć się więcej, zapoznaj się [przy użyciu uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artykułu. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Model rozliczeń, który korzysta z subskrypcji. Dozwolone wartości tego parametru to: Pojemność, PayAsYouUse i rozwoju. |
| MarketplaceSyndicationEnabled | PRAWDA/FAŁSZ | Określa, czy funkcja zarządzania portalu marketplace jest dostępna w portalu. Ustaw wartość true, jeśli rejestrowanie przy użyciu łączności z Internetem. Ustawienie wartości false, jeśli rejestrowanie w środowiskach rozłączonych. Dla odłączonych rejestracji [narzędzie w trybie offline syndykacji](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) może służyć do pobierania elementów portalu marketplace. |
| UsageReportingEnabled | PRAWDA/FAŁSZ | Usługa Azure Stack raporty metryk użycia domyślnie. Operatory o pojemności używa lub obsługa środowiska bez połączenia trzeba wyłączyć raportowanie użycia. Dozwolone wartości tego parametru to: True, False. |
| AgreementNumber | String |  |
| RegistrationName | String | Ustawić unikatową nazwę dla rejestracji, jeśli skrypt rejestracji są uruchomione na więcej niż jedno wystąpienie usługi Azure Stack przy użyciu tego samego Azure identyfikator subskrypcji. Parametr ma wartość domyślną **AzureStackRegistration**. Jednak jeśli używasz tej samej nazwie w więcej niż jedno wystąpienie usługi Azure Stack, skrypt zakończy się niepowodzeniem. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken generuje token rejestracji z poziomu parametrów wejściowych.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parametr | Type | Opis |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Poświadczenia używane do [dostęp uprzywilejowany punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Nazwa użytkownika jest w formacie **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String |  Wstępnie skonfigurowane zdalnego programu PowerShell konsoli, która udostępnia możliwości, takich jak zbieranie danych dziennika i innych post zadania związane z wdrażaniem. Aby dowiedzieć się więcej, zapoznaj się [przy użyciu uprzywilejowanych punktu końcowego](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) artykułu. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Model rozliczeń, który korzysta z subskrypcji. Dozwolone wartości tego parametru to: Pojemność, PayAsYouUse i rozwoju. |
| MarketplaceSyndicationEnabled | PRAWDA/FAŁSZ |  |
| UsageReportingEnabled | PRAWDA/FAŁSZ | Usługa Azure Stack raporty metryk użycia domyślnie. Operatory o pojemności używa lub obsługa środowiska bez połączenia trzeba wyłączyć raportowanie użycia. Dozwolone wartości tego parametru to: True, False. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Błędy rejestracji

Podczas próby rejestracji usługi Azure Stack, może zostać wyświetlony jeden z błędów poniżej:
1. Nie można pobrać informacji obowiązkowe sprzętu $hostName. Sprawdź fizycznego hosta bramy i łączność, a następnie spróbuj ponownie przeprowadzić rejestrację.

2. Nie można nawiązać połączenia z $hostName Pobierz informacje dotyczące sprzętu — należy sprawdzić fizycznego hosta bramy i łączność a następnie spróbuj ponownie przeprowadzić rejestrację.

> Przyczyna: jest to zazwyczaj spowodowane możemy spróbować uzyskać szczegóły sprzętu, takich jak identyfikator UUID systemu Bios i procesora CPU z hostów w celu podjęcia próby aktywacji i nie udało się ze względu na brak możliwości połączenia hosta fizycznego.

Podczas próby dostępu do zarządzania w portalu Marketplace, wystąpi błąd podczas próby zespól produktów. 
> Przyczyna: zwykle dzieje się tak w przypadku usługi Azure Stack nie może uzyskać dostępu do rejestracji zasobu. Jedną typową przyczyną tego jest, że po zmianie subskrypcji platformy Azure directory dzierżawy resetuje rejestracji. Nie masz dostępu do użycia usługi Azure Stack witryny marketplace lub w raporcie po zmianie dzierżawy katalogu dla subskrypcji. Należy ponownie zarejestrować, aby rozwiązać ten problem.

Zarządzanie w portalu Marketplace nadal wymaga rejestrowania i aktywowania usługi Azure Stack, nawet wtedy, gdy użytkownik jest już zarejestrowany z sygnatury rozłączona podczas procesu. 
> Przyczyna: jest to znany problem w środowiskach rozłączonych. Możesz zweryfikować swój status rejestracji, postępując zgodnie z [te kroki](azure-stack-registration.md#verify-azure-stack-registration). Aby można było korzystać z portalu Marketplace management, należy użyć [narzędzie offline](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario). 

## <a name="next-steps"></a>Kolejne kroki

[Pobieranie elementów portalu marketplace z platformy Azure](azure-stack-download-azure-marketplace-item.md)
