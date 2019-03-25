---
title: Weryfikowanie rejestracji platformy Azure dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Narzędzie sprawdzania gotowości stosu Azure umożliwia sprawdzanie poprawności rejestracja w usłudze Azure.
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
ms.openlocfilehash: a777fc1d9052eb58bbebd319fe6cc7f42a09cb9a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403616"
---
# <a name="validate-azure-registration"></a>Sprawdź poprawność rejestracji platformy Azure

Użyj narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (**AzsReadinessChecker**), aby sprawdzić, czy Twoja subskrypcja platformy Azure jest gotowa do użycia z usługą Azure Stack. Sprawdź poprawność rejestracji przed rozpoczęciem wdrażania usługi Azure Stack. Narzędzie sprawdzania gotowości sprawdza, czy:

- Subskrypcja platformy Azure, którego używasz typ nie jest obsługiwany. Subskrypcje musi być dostawca usług chmury (CSP) lub Enterprise Agreement (EA).
- Konto, którego używasz, aby zarejestrować subskrypcję w usłudze Azure można zalogować się do platformy Azure i jest właścicielem subskrypcji.

Aby uzyskać więcej informacji o rejestracji w usłudze Azure Stack, zobacz [zarejestrować w usłudze Azure Stack z platformą Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Pobierz narzędzie sprawdzania gotowości

Pobierz najnowszą wersję **AzsReadinessChecker** z [galerii programu PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Wymagania wstępne

Wymagane są następujące składniki:

**Komputer, na którym działa narzędzie:**

- System Windows 10 lub Windows Server 2016 z łącznością z Internetem.
- Program PowerShell 5.1 lub nowszej. Aby sprawdzić swoją wersję, uruchom następujące polecenie cmdlet programu PowerShell, a następnie przejrzyj **głównych** i **pomocnicza** wersji:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [Skonfigurowany dla usługi Azure Stack PowerShell](azure-stack-powershell-install.md).
- Najnowszą wersję [Microsoft Azure Stack gotowości Checker](https://aka.ms/AzsReadinessChecker).  

**Środowisko usługi Active Directory platformy Azure:**

- Określ nazwę użytkownika i hasło dla konta które jest właścicielem subskrypcji platformy Azure, które będą używane z usługą Azure Stack.  
- Określ identyfikator subskrypcji dla subskrypcji platformy Azure, które będą używane.
- Identyfikowanie **AzureEnvironment** będą używane. Obsługiwane wartości dla parametru Nazwa środowiska to **AzureCloud**, **AzureChinaCloud**, lub **AzureUSGovernment**, w zależności od subskrypcji platformy Azure możesz za pomocą.

## <a name="steps-to-validate-azure-registration"></a>Kroki, aby sprawdzić rejestracja w usłudze Azure

1. Na komputerze, który spełnia wymagania wstępne, otwórz wiersz PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenie, aby zainstalować **AzsReadinessChecker**:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić `$registrationCredential` konta, które jest właścicielem subskrypcji. Zastąp `subscriptionowner@contoso.onmicrosoft.com` nazwą konta i dzierżawy:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Jako dostawca CSP, korzystając z usług udostępnionych lub IUR subskrypcji musisz podać poświadczenia użytkownika, z tym odpowiedniej usługi AAD. Zazwyczaj jest to podobne do `subscriptionowner@iurcontoso.onmicrosoft.com`. Ten użytkownik musi mieć odpowiednie poświadczenia, zgodnie z opisem w poprzednim kroku.

3. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić `$subscriptionID` jako subskrypcji platformy Azure do użycia. Zastąp `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` za pomocą Identyfikatora subskrypcji:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. W wierszu polecenia programu PowerShell uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności subskrypcji:

   - Określ wartość `AzureEnvironment` jako **AzureCloud**, **AzureGermanCloud**, lub **AzureChinaCloud**.  
   - Podaj administratorem usługi Azure Active Directory i nazwa dzierżawy usługi Azure Active Directory.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Upewnij się, że stan jest poprawny dla logowania i wymagań dotyczących rejestracji. Pomyślnej weryfikacji danych wyjściowych zostanie wyświetlony podobny do poniższego przykładu:

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Raport i plik dziennika

Uruchamia każdego czasu sprawdzania poprawności, rejestruje ono wyniki do **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Lokalizacja tych plików są wyświetlane wraz z wynikami weryfikacji w programie PowerShell.

Te pliki mogą pomóc współdzielony stan sprawdzania poprawności, zanim wdrożyć usługę Azure Stack lub badania problemów weryfikacji. Oba pliki są zachowywane wyniki każdego sprawdzenie poprawności kolejne. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Plik dziennika może pomóc zespołowi pomocy technicznej lub wdrożenia badać problemy ze sprawdzaniem poprawności.

Domyślnie oba pliki są zapisywane w **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Użyj **- OutputPath** ***&lt;ścieżki&gt;*** parametru na końcu wiersza polecenia do określenia lokalizacji inny raport.
- Użyj **- CleanReport** parametru na koniec uruchom polecenie, aby wyczyścić informacje o poprzednich przebiegów narzędzia z **AzsReadinessCheckerReport.json**.

Aby uzyskać więcej informacji, zobacz [raportu sprawdzania poprawności w usłudze Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Błędy sprawdzania poprawności

Jeśli sprawdzenie poprawności zakończy się niepowodzeniem, w oknie programu PowerShell są wyświetlane szczegóły dotyczące błędu. Narzędzie rejestruje także informacje **AzsReadinessChecker.log** pliku.

Poniższy przykład zawiera wskazówek na temat typowych błędów sprawdzania poprawności:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Użytkownik musi być właścicielem subskrypcji

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Przyczyna** — konto nie jest administratorem subskrypcji platformy Azure.

**Rozpoznawanie** — Użyj konta które jest administratorem subskrypcji platformy Azure, która zostanie pobrana opłata za użycie z wdrożenia usługi Azure Stack.

### <a name="expired-or-temporary-password"></a>Hasło wygasło lub tymczasowego

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Przyczyna** — konta nie zalogować się, ponieważ hasło jest wygasły lub jest tymczasowe.

**Rozpoznawanie** — w programie PowerShell, uruchom następujące polecenie i postępuj zgodnie z monitami, aby zresetować hasło.

```powershell
Login-AzureRMAccount
```

Alternatywnie, zaloguj się do [witryny Azure portal](https://portal.azure.com) jako właściciela konta, a użytkownik musi zmienić hasło.

### <a name="unknown-user-type"></a>Typ Nieznany użytkownik  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Przyczyna** — konta nie zalogować się do określonego środowiska usługi Azure Active Directory. W tym przykładzie **AzureChinaCloud** jest określony jako **AzureEnvironment**.  

**Rozpoznawanie** — upewnij się, że konto jest prawidłowe dla określonego środowiska platformy Azure. W programie PowerShell uruchom następujące polecenie, aby sprawdzić, czy konto jest prawidłowe dla określonego środowiska:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Następne kroki

- [Weryfikowanie tożsamości platformy Azure](azure-stack-validate-identity.md)
- [Wyświetl raport gotowości](azure-stack-validation-report.md)
- [Zagadnienia dotyczące integracji usługi Azure Stack ogólne](azure-stack-datacenter-integration.md)
