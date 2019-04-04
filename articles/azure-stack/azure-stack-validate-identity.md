---
title: Weryfikowanie tożsamości platformy Azure dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Użyj sprawdzania gotowości stosu Azure do weryfikowania tożsamości platformy Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: b6a859f1104be8650b95ac789f3da606fb1b70f6
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486686"
---
# <a name="validate-azure-identity"></a>Weryfikowanie tożsamości platformy Azure

Użyj narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (**AzsReadinessChecker**), aby sprawdzić, czy usługi Azure Active Directory (Azure AD) jest gotowa do użycia z usługą Azure Stack. Weryfikowanie rozwiązań tożsamość w systemie Azure, przed rozpoczęciem wdrażania usługi Azure Stack.  

Narzędzie sprawdzania gotowości weryfikuje:

- Azure Active Directory (Azure AD) jako dostawcy tożsamości dla usługi Azure Stack.
- Konto usługi Azure AD, do którego planujesz używać zalogować się jako administrator globalny usługi Azure Active Directory.

Sprawdzanie poprawności gwarantuje, że środowisko jest gotowe dla usługi Azure Stack do przechowywania informacji o użytkownikach, aplikacje, grupy i jednostki usługi z usługi Azure Stack w usłudze Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Pobierz narzędzie sprawdzania gotowości

Pobierz najnowszą wersję narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker) z [galerii programu PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane są następujące składniki:

**Komputer, na którym działa narzędzie:**

- System Windows 10 lub Windows Server 2016 z łącznością z Internetem.
- Program PowerShell 5.1 lub nowszej. Aby sprawdzić swoją wersję, uruchom następujące polecenie programu PowerShell, a następnie przejrzyj **głównych** wersji i **pomocnicza** wersji:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Skonfigurowany dla usługi Azure Stack PowerShell](azure-stack-powershell-install.md).
- Najnowszą wersję [Microsoft Azure Stack gotowości Checker](https://aka.ms/AzsReadinessChecker) narzędzia.

**Środowisko usługi Active Directory platformy Azure:**

- Określ konto usługi Azure AD do użycia dla usługi Azure Stack, a następnie upewnij się, że administrator globalny usługi Azure Active Directory.
- Zidentyfikuj nazwa dzierżawy usługi Azure AD. Nazwa dzierżawy musi być nazwą domeny głównej dla usługi Azure Active Directory; na przykład **contoso.onmicrosoft.com**.
- Zidentyfikuj środowiska platformy Azure, które będą używane. Obsługiwane wartości dla parametru Nazwa środowiska to **AzureCloud**, **AzureChinaCloud**, lub **AzureUSGovernment**, w zależności od subskrypcji platformy Azure możesz użyć.

## <a name="steps-to-validate-azure-identity"></a>Kroki, aby sprawdzić tożsamość w systemie Azure

1. Na komputerze, który spełnia wymagania wstępne, otwórz wiersz polecenia programu PowerShell, a następnie uruchom następujące polecenie, aby zainstalować **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić **$serviceAdminCredential** jako administrator usługi dla dzierżawy usługi Azure AD.  Zastąp **serviceadmin\@contoso.onmicrosoft.com** nazwą konta i dzierżawy:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności usługi Azure AD:

   - Określ wartość nazwy środowiska **AzureEnvironment**. Obsługiwane wartości dla parametru Nazwa środowiska to **AzureCloud**, **AzureChinaCloud**, lub **AzureUSGovernment**, w zależności od subskrypcji platformy Azure możesz użyć.
   - Zastąp **contoso.onmicrosoft.com** nazwą dzierżawy usługi Azure Active Directory.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Upewnij się, ma stan **OK** wymagań instalacyjnych. Pomyślnej weryfikacji wygląda podobnie do następującego:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>Raport i plik dziennika

Uruchamia każdego czasu sprawdzania poprawności, rejestruje ono wyniki do **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell.

Te pliki mogą pomóc współdzielony stan sprawdzania poprawności, zanim wdrożyć usługę Azure Stack lub badania problemów weryfikacji. Oba pliki są zachowywane wyniki każdego sprawdzenie poprawności kolejne. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Plik dziennika może pomóc zespołowi pomocy technicznej lub wdrożenia badać problemy ze sprawdzaniem poprawności.

Domyślnie oba pliki są zapisywane w **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Użyj **- OutputPath** ***&lt;ścieżki&gt;*** parametru na końcu wiersza polecenia do określenia lokalizacji inny raport.
- Użyj **- CleanReport** parametru na koniec uruchom polecenie, aby wyczyścić informacje o poprzednich przebiegów narzędzia z **AzsReadinessCheckerReport.json**.

Aby uzyskać więcej informacji, zobacz [raportu sprawdzania poprawności w usłudze Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Błędy sprawdzania poprawności

Jeśli sprawdzenie poprawności zakończy się niepowodzeniem, w oknie programu PowerShell są wyświetlane szczegóły dotyczące błędu. Narzędzie rejestruje także informacje o pliku AzsReadinessChecker.log.

Poniższe przykłady zapewnić wskazówki dotyczące typowych błędów sprawdzania poprawności.

### <a name="expired-or-temporary-password"></a>Hasło wygasło lub tymczasowego

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Przyczyna** — konta nie można zalogować, ponieważ hasło jest wygasły lub jest tymczasowe.

**Rozpoznawanie** — w programie PowerShell, uruchom następujące polecenie, a następnie postępuj zgodnie z monitami, aby zresetować hasło:

```powershell
Login-AzureRMAccount
```

Alternatywnie, zaloguj się do [witryny Azure portal](https://portal.azure.com) jako właściciela konta, a użytkownik musi zmienić hasło.

### <a name="unknown-user-type"></a>Typ Nieznany użytkownik 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Przyczyna** — konto zalogowanie się w określonej usłudze Azure Active Directory (**AADDirectoryTenantName**). W tym przykładzie **AzureChinaCloud** jest określony jako **AzureEnvironment**.

**Rozpoznawanie** — upewnij się, że konto jest prawidłowe dla określonego środowiska platformy Azure. W programie PowerShell uruchom następujące polecenie, aby sprawdzić, czy konto jest prawidłowe dla określonego środowiska:

```powershell
Login-AzureRmAccount –EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Konto nie jest administratorem

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Przyczyna** — mimo że to konto można pomyślnie zalogować się, konto nie jest administratorem usługi Azure Active Directory (**AADDirectoryTenantName**).  

**Rozpoznawanie** — Zaloguj się do [witryny Azure portal](https://portal.azure.com) jako właściciel konta, przejdź do **usługi Azure Active Directory**, następnie **użytkowników**, następnie **wybierz Użytkownik**, następnie **roli w katalogu**, a następnie upewnij się, użytkownik jest **administratora globalnego**. Jeśli konto jest **użytkownika**, przejdź do **usługi Azure Active Directory** > **niestandardowe nazwy domen**i upewnij się, że nazwa podana dla  **AADDirectoryTenantName** jest oznaczony jako podstawowej nazwy domeny dla tego katalogu. W tym przykładzie jest **contoso.onmicrosoft.com**.

Usługa Azure Stack wymaga, że nazwa domeny jest nazwa domeny głównej.

## <a name="next-steps"></a>Następne kroki

[Validate Azure registration (Sprawdzanie poprawności rejestracji na platformie Azure)](azure-stack-validate-registration.md)  
[Wyświetl raport gotowości](azure-stack-validation-report.md)  
[Zagadnienia dotyczące integracji usługi Azure Stack ogólne](azure-stack-datacenter-integration.md)  
