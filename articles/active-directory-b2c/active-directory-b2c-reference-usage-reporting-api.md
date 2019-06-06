---
title: Przykłady interfejsu API z raportowania użycia i definicji w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przewodnik i przykłady dotyczące konfigurowania raportów, w dzierżawie usługi Azure AD B2C, użytkowników, uwierzytelnienia i uwierzytelnienia Multi-Factor Authentication.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fe7dd90bdec816ee433310a803d85c57f4892f8c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508711"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Uzyskiwanie dostępu do raportów użycia w usłudze Azure AD B2C za pośrednictwem interfejsu API raportowania

Usługa Azure Active Directory B2C (Azure AD B2C) udostępnia uwierzytelnianie oparte na użytkownika, logowanie i uwierzytelnianie wieloskładnikowe systemu Azure. Uwierzytelnianie jest obsługiwane dla użytkowników końcowych w Twojej rodzinie aplikacji w różnych dostawców tożsamości. Gdy wiadomo, liczbę użytkowników zarejestrowanych w dzierżawie, dostawcy, które są używane do rejestrowania i liczbę uwierzytelnień według typu, możesz odpowiadać na pytania, takie jak:
* Ilu użytkowników z każdego typu dostawcy tożsamości (na przykład konto Microsoft lub LinkedIn) zostały zarejestrowane w ciągu ostatnich 10 dni?
* Ile uwierzytelnienia za pomocą uwierzytelniania wieloskładnikowego zostały ukończone pomyślnie w ciągu ostatniego miesiąca?
* Ile uwierzytelnienia logowania w podstawie zostały ukończone w tym miesiącu? Każdego dnia? Na aplikację?
* Jak oszacować oczekiwany koszt miesięczny Moje działanie dzierżawy usługi Azure AD B2C

Ten artykuł koncentruje się na raportach powiązane działania rozliczeniowe, która jest oparta na liczbie użytkowników, płatnych uwierzytelnienia logowania w oparciu i uwierzytelnienia Multi-Factor Authentication.


## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy należy wykonać czynności opisane w [wymagania wstępne dotyczące raportowania interfejsów API usługi Azure AD dostęp](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Tworzenie aplikacji, uzyskać klucz tajny i przyznać jej dostęp uprawnienia do raportów w dzierżawie usługi Azure AD B2C. *Skryptu powłoki systemowej* i *skrypt w języku Python* przykłady znajdują się także w tym miejscu. 

## <a name="powershell-script"></a>Skrypt programu PowerShell
Ten skrypt demonstruje tworzenie cztery raporty użycia za pomocą `TimeStamp` parametru i `ApplicationId` filtru.

```powershell
# This script will require the Web Application and permissions setup in Azure Active Directory

# Constants
$ClientID      = "your-client-application-id-here"  
$ClientSecret  = "your-client-application-secret-here"
$loginURL      = "https://login.microsoftonline.com"
$tenantdomain  = "your-b2c-tenant-domain.onmicrosoft.com"  
# Get an Oauth 2 access token based on client id, secret and tenant domain
$body          = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth         = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body
if ($oauth.access_token -ne $null) {
    $headerParams  = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

    Write-host Data from the tenantUserCount report
    Write-host ====================================================
     # Returns a JSON document for the report
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the tenantUserCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/tenantUserCount?%24filter=TimeStamp+gt+2016-10-15&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCountSummary report
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=TimeStamp+gt+2016-09-20+and+TimeStamp+lt+2016-10-03&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cAuthenticationCount report with ApplicationId filter
    Write-host ====================================================
    # Returns a JSON document for the " " report
        $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cAuthenticationCount?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCountSummary
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with datetime filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCount?%24filter=TimeStamp+gt+2016-09-10+and+TimeStamp+lt+2016-10-04&api-version=beta")
    Write-host $myReport.Content

    Write-host Data from the b2cMfaRequestCount report with ApplicationId filter
    Write-host ====================================================
    $myReport = (Invoke-WebRequest -Headers $headerParams -Uri "https://graph.windows.net/$tenantdomain/reports/b2cMfaRequestCountSummary?%24filter=ApplicationId+eq+ada78934-a6da-4e69-b816-10de0d79db1d&api-version=beta")
     Write-host $myReport.Content

} else {
    Write-Host "ERROR: No Access Token"
    }
```


## <a name="usage-report-definitions"></a>Definicje raportów użycia
* **tenantUserCount**: Liczba użytkowników w dzierżawie, typ dostawcy tożsamości, dziennie w ciągu ostatnich 30 dni. (Opcjonalnie `TimeStamp` filtru zawiera liczby użytkowników z określonej daty do daty bieżącej). Raport zawiera:
  * **TotalUserCount**: Liczba wszystkich obiektów użytkownika.
  * **OtherUserCount**: Liczba użytkowników usługi Azure Active Directory (nie użytkowników usługi Azure AD B2C).
  * **LocalUserCount**: Liczba kont użytkowników usługi Azure AD B2C utworzone przy użyciu poświadczeń lokalnych do dzierżawy usługi Azure AD B2C.

* **AlternateIdUserCount**: Liczba użytkowników usługi Azure AD B2C zarejestrowanych za pomocą dostawców tożsamości zewnętrznych (na przykład, Facebook, konta Microsoft lub innej dzierżawy usługi Azure Active Directory, zwaną także `OrgId`).

* **b2cAuthenticationCountSummary**: Podsumowanie dzienne liczbę płatnych uwierzytelnień ostatnich 30 dni, dzień, a typ przepływu uwierzytelniania.

* **b2cAuthenticationCount**: Liczba uwierzytelnień w przedziale czasu. Wartość domyślna to 30 ostatnich dni.  (Opcjonalne: Początek i koniec `TimeStamp` parametry definiują określonym przedziale czasu.) Dane wyjściowe obejmują `StartTimeStamp` (najwcześniejsza data działania dla tej dzierżawy) i `EndTimeStamp` (najnowsza aktualizacja).

* **b2cMfaRequestCountSummary**: Podsumowanie dziennej liczby uwierzytelnienia Multi-Factor Authentication, według dnia i typów (SMS lub głosowych).


## <a name="limitations"></a>Ograniczenia
Dane dotyczące liczby użytkowników są odświeżane co 24-48 godzin. Uwierzytelnienia są aktualizowane kilka razy dziennie. Korzystając z `ApplicationId` filtr, odpowiedź pusty raport może być spowodowane jedną z następujących warunków:
  * Identyfikator aplikacji nie istnieje w dzierżawie. Upewnij się, że jest on poprawny.
  * Identyfikator aplikacji istnieje, ale nie znaleziono danych w okresie raportowania. Przejrzyj parametry daty/godziny.


## <a name="next-steps"></a>Kolejne kroki
### <a name="monthly-bill-estimates-for-azure-ad"></a>Miesięczny rachunek szacuje się dla usługi Azure AD
W połączeniu z [najbardziej aktualne usługi Azure AD B2C dostępne ceny](https://azure.microsoft.com/pricing/details/active-directory-b2c/), można oszacować dzienne, tygodniowe i miesięczne użycie platformy Azure.  Oszacowanie jest szczególnie przydatne podczas planowania związanego z zmiany w zachowaniu dzierżawy, mogą mieć wpływ na całkowity koszt. Możesz przejrzeć faktyczne koszty w swojej [połączonej subskrypcji platformy Azure](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opcje dla innych formatów danych wyjściowych
Poniższy kod przedstawia przykłady wysyłanie danych wyjściowych do formatu JSON, listy wartości nazw i XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
