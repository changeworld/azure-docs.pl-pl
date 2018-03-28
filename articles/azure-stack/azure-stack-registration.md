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
ms.date: 03/26/2018
ms.author: jeffgilb
ms.reviewer: avishwan
ms.openlocfilehash: 1dc3d9a96b9b27927cc8cc66b5e80987fba4f8ea
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="register-azure-stack-with-azure"></a>Zarejestruj stosu Azure przy użyciu platformy Azure
Rejestrowanie [stosu Azure](azure-stack-poc.md) z platformą Azure umożliwia pobieranie elementów marketplace z platformy Azure i ustawienia commerce danych raportowania z powrotem do firmy Microsoft. Po zarejestrowaniu stosu Azure użycia jest zgłaszany do handlu Azure i widoczny w ramach subskrypcji, używana do rejestracji. 

> [!IMPORTANT]
> Rejestracja jest wymagane, jeśli wybierzesz modelu rozliczeń płatności jako — użytkownik użycia. W przeciwnym razie będzie do naruszenia postanowień licencyjnych dotyczących wdrożenia stosu Azure zgodnie z użycia w przeciwnym razie nie będzie raportowana.

## <a name="prerequisites"></a>Wymagania wstępne
Przed zarejestrowaniem stosu Azure przy użyciu platformy Azure, musi mieć:

- Identyfikator subskrypcji dla subskrypcji platformy Azure. Można uzyskać Identyfikatora, logowanie do platformy Azure, kliknij przycisk **więcej usług** > **subskrypcje**, kliknij subskrypcję, którego chcesz użyć, a następnie w obszarze **Essentials** można znaleźć Identyfikator subskrypcji. 

  > [!NOTE]
  > Chin, Niemczech i instytucji rządowych Stanów Zjednoczonych subskrypcje w chmurze nie są obecnie obsługiwane. 

- Nazwa użytkownika i hasło dla konta, które jest właścicielem subskrypcji (obsługiwane są konta MSA/2FA)
- Zarejestrowano dostawcę zasobów Azure stosu (zobacz poniższą sekcję zarejestrować dostawcy zasobów stosu Azure, aby uzyskać szczegółowe informacje).

Jeśli nie masz subskrypcji platformy Azure, która spełnia te wymagania, możesz [utworzyć tutaj bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?b=17.06). Rejestrowanie stosu Azure wiąże się bez kosztów w ramach subskrypcji platformy Azure.

### <a name="bkmk_powershell"></a>Instalowanie programu PowerShell dla usługi Azure stosu
Musisz użyć najnowszej programu PowerShell dla usługi Azure stosu do rejestracji w usłudze Azure.

Jeśli nie jest jeszcze zainstalowana, [zainstalować program PowerShell Azure stosu](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install). 

### <a name="bkmk_tools"></a>Pobieranie narzędzia Azure stosu
Repozytorium GitHub narzędzia stosu Azure zawiera moduły programu PowerShell Azure stosu funkcjonalności. w tym funkcje rejestracji. Podczas rejestracji procesu, które mają być importowane i używane moduł RegisterWithAzure.psm1 PowerShell znalezione w repozytorium narzędzia Azure stosu, można zarejestrować wystąpienia stosu Azure przy użyciu platformy Azure. 

Aby upewnić się, czy używasz najnowszej wersji, należy usunąć istniejące wersje narzędzia Azure stosu i [najnowszą wersję można pobrać z witryny GitHub](azure-stack-powershell-download.md) przed zarejestrowaniem z platformy Azure.

## <a name="register-azure-stack-in-connected-environments"></a>Zarejestruj Azure stosu w środowiskach połączonych
Środowisk sieciowych można uzyskać dostępu do Internetu Azure. Dla tych środowisk należy zarejestrować dostawcę zasobów Azure stosu z platformy Azure, a następnie skonfiguruj modelu rozliczeń.

> [!NOTE]
> Kroki te należy uruchomić z komputera, który ma dostęp do uprzywilejowanych punktu końcowego. 

### <a name="register-the-azure-stack-resource-provider"></a>Rejestrowanie dostawcy zasobów Azure stosu
Aby zarejestrować stosu Azure dostawcy zasobów platformy Azure, uruchom program Powershell ISE jako administrator i użyj następujących poleceń programu PowerShell. Polecenia te obejmują:
- Wyświetlenie monitu zaloguj się jako właściciela subskrypcji Azure używanych i ustawić **EnvironmentName** parametr **AzureCloud**.
- Rejestrowanie dostawcy zasobów platformy Azure **Microsoft.AzureStack**.

1. Dodaj konto platformy Azure, która służy do rejestrowania stosu Azure. Aby dodać konta, uruchom **Add-AzureRmAccount** polecenia cmdlet. Zostanie wyświetlony monit o wprowadzenie poświadczeń konta administratora globalnego usługi Azure i może być konieczne użycie uwierzytelniania wieloskładnikowego 2 na podstawie konfiguracji Twoje konto.

   ```Powershell
      Add-AzureRmAccount -EnvironmentName AzureCloud
   ```

2. Jeśli masz wiele subskrypcji, uruchom następujące polecenie, wybierz ten, który ma być używany:  

   ```powershell
      Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

3. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów Azure stosu w Twojej subskrypcji platformy Azure:

   ```Powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

### <a name="register-azure-stack-with-azure-using-the-pay-as-you-use-billing-model"></a>Zarejestruj stosu Azure przy użyciu platformy Azure przy użyciu modelu rozliczeń płatności jako — użytkownik użycia
Użyj tych kroków zarejestrować stosu Azure za pomocą platformy Azure przy użyciu modelu rozliczeń płatności jako — użytkownik użycia.

Uruchom program PowerShell ISE jako administrator, a następnie przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas możesz [pobrane narzędzia Azure stosu](#bkmk_tools). Importuj **RegisterWithAzure.psm1** modułu przy użyciu programu PowerShell: 

PowerShell wpisz:

```powershell
Import-Module .\RegisterWithAzure.psm1
```
W tej samej sesji programu PowerShell, uruchom **AzsRegistration zestaw** polecenia cmdlet. Po wyświetleniu monitu o poświadczenia, określ właściciela subskrypcji platformy Azure.  

PowerShell wpisz:

```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -BillingModel PayAsYouUse
```

|Parametr|Opis|
|-----|-----|
|CloudAdminCredential|Obiekt programu PowerShell, który zawiera informacje poświadczeń (nazwy użytkownika i hasło) używane do dostępu uprzywilejowanego punktu końcowego.|
|PrivilegedEndpoint|Wstępnie skonfigurowane zdalnego programu PowerShell konsoli, która dostarcza funkcje, takie jak zbierania dzienników oraz innych post zadań wdrażania. Aby dowiedzieć się więcej, zapoznaj się [przy użyciu punktu końcowego uprzywilejowanych](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint#access-the-privileged-endpoint) artykułu.|
|BillingModel|Modelu rozliczeń, który korzysta z subskrypcji. Dozwolone wartości tego parametru to: pojemności, PayAsYouUse i tworzenia.|

### <a name="register-azure-stack-with-azure-using-the-capacity-billing-model"></a>Zarejestruj stosu Azure przy użyciu platformy Azure przy użyciu modelu rozliczeń pojemności
Postępuj zgodnie z instrukcjami tego samego używana do rejestracji przy użyciu modelu rozliczeń płatności jako — użytkownik użycia, ale Dodaj numer umowy, w którym zakupiono pojemności i zmień `BillingModel` parametr **pojemności**. Wszystkie inne parametry nie uległy zmianie.

PowerShell wpisz:
```powershell
$AzureContext = Get-AzureRmContext
$CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials>  -Message "Enter the credentials to access the privileged endpoint"
Set-AzsRegistration `
    -CloudAdminCredential $CloudAdminCred `
    -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
    -AgreementNumber <EA agreement number> `
    -BillingModel Capacity
```

## <a name="register-azure-stack-in-disconnected-environments"></a>Zarejestruj w środowiskach rozłączonych Azure stosu 
*Informacje przedstawione w tej sekcji ma zastosowanie, począwszy od wersji aktualizacji 1712 stosu Azure (180106.1) i nie jest obsługiwane we wcześniejszych wersjach.*

W przypadku rejestracji Azure stosu w środowisku bez połączenia (bez łączności z Internetem), musisz pobrać rejestracji tokenu ze środowiska Azure stosu, a następnie użyj tokenu na komputerze, który może nawiązać połączenia z platformy Azure i ma [środowiska PowerShell Azure stosu zainstalowany](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Pobierz rejestracji token ze środowiska Azure stosu
  1. Aby uzyskać tokenu rejestracji, uruchom następujące polecenia programu PowerShell:  

     ```Powershell
        $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
        $RegistrationToken = Get-AzsRegistrationToken -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<your agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
      ```
      > [!TIP]  
      > Token rejestracji jest zapisany w określonym pliku *$env:SystemDrive\RegistrationToken.txt*.

  2. Zapisz ten token rejestracji do użycia na platformie Azure połączone maszyny.


### <a name="connect-to-azure-and-register"></a>Łączenie się z platformy Azure i rejestr
Uruchom program PowerShell ISE jako administrator, a następnie przejdź do **rejestracji** folderu w **AzureStack Narzędzia główne** Katalog utworzony podczas możesz [pobrane narzędzia Azure stosu](#bkmk_tools). Importuj **RegisterWithAzure.psm1** modułu: 

PowerShell wpisz:
```powershell
Import-Module .\RegisterWithAzure.psm1
```
Następnie w tej samej sesji programu PowerShell, określ token rejestracji do rejestracji w usłudze Azure:

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
> Zapisz nazwę zasobu rejestracji lub token rejestracji do użytku w przyszłości.

## <a name="verify-azure-stack-registration"></a>Weryfikuj rejestrację Azure stosu
Wykonaj następujące czynności, aby sprawdzić, czy Azure stos został pomyślnie zarejestrowany przy użyciu platformy Azure.
1. Zaloguj się do stosu Azure [portalu administratora](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-portals#access-the-administrator-portal): https&#58;/ / adminportal. *&lt;region >. &lt;fqdn >*.
2. Kliknij przycisk **więcej usług** > **zarządzania Marketplace** > **dodać z platformy Azure**.

Jeśli zobaczysz listę dostępnych z platformy Azure (na przykład WordPress) elementów, proces aktywacji zakończyło się pomyślnie.

> [!NOTE]
> Po zakończeniu rejestracji aktywne ostrzeżenie nie rejestracji nie będzie dłużej widoczny.

## <a name="renew-or-change-registration"></a>Należy odnowić lub zmienić rejestracji
Musisz zaktualizować lub odnowić rejestrację w następujących okolicznościach:
- Po odnowieniu subskrypcji corocznych na podstawie pojemności.
- Po zmianie modelu rozliczeń.
- Jeśli można skalować zmiany (dodawania i usuwania węzłów) rozliczania opartego na wydajność.

### <a name="change-the-subscription-you-use"></a>Zmień subskrypcję, których używasz
Jeśli chcesz zmienić subskrypcję w przypadku użycia, najpierw należy uruchomić **AzsRegistration Usuń** polecenia cmdlet, upewnij się, użytkownik jest zalogowany do poprawny kontekst programu Azure PowerShell, a na koniec uruchom **AzsRegistration zestawu**  ze wszystkimi zmienić parametry:

```powershell
Remove-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
Set-AzureRmContext -SubscriptionId $NewSubscriptionId
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```

### <a name="change-the-billing-model-or-syndication-features"></a>Zmień rozliczeń modelu lub zespolonego funkcji
Jeśli chcesz zmienić modelu rozliczeń lub zespolonego funkcji dla instalacji, należy wywołać funkcję rejestracji, aby ustawić nowe wartości. Nie należy najpierw usunąć bieżący rejestracji: 

```powershell
Set-AzsRegistration -CloudAdminCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel PayAsYouUse
```
## <a name="next-steps"></a>Kolejne kroki

[Pobieranie elementów marketplace z platformy Azure](azure-stack-download-azure-marketplace-item.md)