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
ms.topic: get-started-article
ms.date: 06/08/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 51753a5324bbbcbf4e951628a42dd3bf425354af
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49957586"
---
# <a name="validate-azure-registration"></a>Sprawdź poprawność rejestracji platformy Azure 
Aby sprawdzić, czy Twoja subskrypcja platformy Azure jest gotowa do użycia z usługą Azure Stack, należy użyć narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker). Sprawdź poprawność rejestracji przed rozpoczęciem wdrażania usługi Azure Stack. Narzędzie sprawdzania gotowości weryfikuje:
- Subskrypcja platformy Azure, którego używasz typ nie jest obsługiwany. Subskrypcje musi być dostawca usług chmury (CSP) lub Enterprise Agreement (EA). 
- Konto, którego używasz, aby zarejestrować subskrypcję w usłudze Azure można zalogować się do platformy Azure i jest właścicielem subskrypcji. 

Aby uzyskać więcej informacji o rejestracji w usłudze Azure Stack, zobacz [zarejestrować w usłudze Azure Stack z platformą Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Pobierz narzędzie sprawdzania gotowości
Pobierz najnowszą wersję narzędzia narzędzie do sprawdzania gotowości usługi Azure Stack (AzsReadinessChecker) jest dostępna na [galerii programu PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Wymagania wstępne
Należy spełnić następujące wymagania wstępne.

**Komputer, na którym działa narzędzie:**
 - System Windows 10 lub Windows Server 2016 z łącznością z Internetem.
 - Program PowerShell 5.1 lub nowszej. Aby sprawdzić swoją wersję, uruchom następujące polecenie programu PowerShell, a następnie przejrzyj *głównych* wersji i *pomocnicza* wersji:  

    >`$PSVersionTable.PSVersion` 
 - Konfigurowanie [programu PowerShell dla usługi Azure Stack](azure-stack-powershell-install.md). 
 - Pobierz najnowszą wersję [Microsoft Azure Stack gotowości Checker](https://aka.ms/AzsReadinessChecker) narzędzia.  

**Środowisko usługi Active Directory platformy Azure:**
 - Określ nazwę użytkownika i hasło dla konta które jest właścicielem subskrypcji platformy Azure, które będą używane z usługą Azure Stack.  
 - Określ identyfikator subskrypcji dla subskrypcji platformy Azure, które będą używane. 
 - Identyfikowanie AzureEnvironment użyjesz: *AzureCloud*, *AzureGermanCloud*, lub *AzureChinaCloud*.

## <a name="validate-azure-registration"></a>Sprawdź poprawność rejestracji platformy Azure
1. Na komputerze, który spełnia wymagania wstępne Otwórz administracyjny wiersz polecenia PowerShell, a następnie uruchom następujące polecenie, aby zainstalować AzsReadinessChecker.
    > `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

2. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić *$registrationCredential* konta, które jest właścicielem subskrypcji.   Zastąp *subscriptionowner@contoso.onmicrosoft.com* przy użyciu swojego konta i dzierżawy. 
    > `$registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"`

3. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby ustawić *$subscriptionID* jako subskrypcji platformy Azure będą używane. Zastąp *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx* przy użyciu własnego identyfikatora subskrypcji.  
     > `$subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` 

4. W wierszu polecenia programu PowerShell, uruchom następujące polecenie, aby rozpocząć sprawdzanie poprawności subskrypcji 
   - Określ wartość dla AzureEnvironment jako *AzureCloud*, *AzureGermanCloud*, lub *AzureChinaCloud*.  
   - Podaj administratorem usługi Azure Active Directory i Twoja nazwa dzierżawy usługi Azure Active Directory. 

   > `Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID`

5. Po uruchomieniu narzędzia, przejrzyj dane wyjściowe. Upewnij się, że stan jest odpowiedni dla wymagań dotyczących rejestracji i logowania. Pomyślnej weryfikacji wygląda następująco:  
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````


## <a name="report-and-log-file"></a>Raport i plik dziennika
Uruchamia każdego czasu sprawdzania poprawności, rejestruje ono wyniki do **AzsReadinessChecker.log** i **AzsReadinessCheckerReport.json**. Wyświetla lokalizację tych plików z wynikami weryfikacji w programie PowerShell. 

Te pliki mogą pomóc współdzielony stan sprawdzania poprawności, zanim wdrożyć usługę Azure Stack lub badania problemów weryfikacji. Oba pliki są zachowywane wyniki każdego sprawdzenie poprawności kolejne. Raport zawiera potwierdzenie zespołu wdrażania konfiguracji tożsamości. Plik dziennika może pomóc zespołowi pomocy technicznej lub wdrożenia badać problemy ze sprawdzaniem poprawności. 

Domyślnie oba pliki są zapisywane w *C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Użyj **- OutputPath** ***&lt;ścieżki&gt;*** parametru na końcu wiersza polecenia do określenia lokalizacji inny raport.   
 - Użyj **- CleanReport** parametru na końcu polecenia uruchomienia, aby wyczyścić informacje z *AzsReadinessCheckerReport.json*.  temat poprzednie uruchomienia tego narzędzia. Aby uzyskać więcej informacji [raportu sprawdzania poprawności w usłudze Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Błędy sprawdzania poprawności
Jeśli sprawdzenie poprawności zakończy się niepowodzeniem, w oknie programu PowerShell są wyświetlane szczegóły dotyczące błędu. Narzędzie rejestruje także informacje o AzsReadinessChecker.log.

Poniższe przykłady zapewnić wskazówki dotyczące typowych błędów sprawdzania poprawności.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Użytkownik musi być właścicielem subskrypcji   
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Przyczyna** — konto nie jest administratorem subskrypcji platformy Azure.   

**Rozpoznawanie** — Użyj konta które jest administratorem subskrypcji platformy Azure, która zostanie pobrana opłata za użycie z wdrożenia usługi Azure Stack.


### <a name="expired-or-temporary-password"></a>Hasło wygasło lub tymczasowego 
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: 48fe06f5-a5b4-4961-ad45-a86964689900
Correlation ID: 3dd1c9b2-72fb-46a0-819d-058f7562cb1f
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Przyczyna** — konta nie można zalogować, ponieważ hasło jest wygasły lub jest tymczasowe.     

**Rozpoznawanie** — w programie PowerShell Uruchom i postępuj zgodnie z monitami, aby zresetować hasło. 
  > `Login-AzureRMAccount` 

Alternatywnie, zaloguj się do https://portal.azure.com zgodnie z konta i użytkownik musi zmienić hasło.


### <a name="unknown-user-type"></a>Typ Nieznany użytkownik  
````PowerShell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d using account admin@contoso.onmicrosoft.com failed with unknown_user_type: Unknown Us
er Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
````
**Przyczyna** — konto nie może logować się do określonego środowiska usługi Azure Active Directory. W tym przykładzie *AzureChinaCloud* jest określony jako *AzureEnvironment*.  

**Rozpoznawanie** — upewnij się, że konto jest nieprawidłowa dla określonego środowiska platformy Azure. W programie PowerShell uruchom następujące polecenie, aby sprawdzić, czy konto jest prawidłowe dla określonego środowiska.     
  > `Login-AzureRmAccount -EnvironmentName AzureChinaCloud`


## <a name="next-steps"></a>Następne kroki
[Weryfikowanie usługi Azure identity](azure-stack-validate-identity.md)
[wyświetlić raport gotowość](azure-stack-validation-report.md)
[zagadnienia dotyczące integracji usługi Azure Stack ogólne](azure-stack-datacenter-integration.md)

