---
title: 'Usługa Azure Active Directory B2C: Przykłady interfejsu API raportowania użycia i definicje | Dokumentacja firmy Microsoft'
description: Przewodnik i przykłady dotyczące konfigurowania raportów dzierżawcy usługi Azure AD B2C, użytkowników, uwierzytelnianie i operacje uwierzytelniania wieloskładnikowego
services: active-directory-b2c
documentationcenter: dev-center-name
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 07029181423927f0796cb85e728df416d01466e8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Uzyskiwanie dostępu do raportów użycia w usłudze Azure AD B2C za pośrednictwem interfejsu API raportowania

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia uwierzytelnianie na podstawie użytkownika, logowanie i uwierzytelnianie wieloskładnikowe Azure. Uwierzytelnianie jest dostępna dla użytkowników końcowych rodziny aplikacji przez dostawców tożsamości. Gdy wiesz, liczbę użytkowników zarejestrowanych w dzierżawie, dostawców używanego do rejestrowania i liczbę uwierzytelnień według typu, pozwala odpowiedzieć na pytania, takich jak:
* Ilu użytkowników z każdego typu dostawcy tożsamości (na przykład konto Microsoft lub LinkedIn) zostały zarejestrowane w ciągu ostatnich 10 dni?
* Ile uwierzytelnienia przy użyciu usługi Multi-Factor Authentication została ukończona pomyślnie w ostatnim miesiącu?
* Ile uwierzytelnienia logowania w podstawie zostały ukończone w tym miesiącu? Dziennie? Na aplikację?
* Jak można oszacować oczekiwanego miesięczny koszt działania dzierżawy usługi Azure AD B2C

Ten artykuł dotyczy powiązane rozliczeń działalność, która jest oparta na liczbę użytkowników, rozliczeniowy Zaloguj się w podstawie uwierzytelnienia i uwierzytelnienia wieloskładnikowego raportów.


## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem pracy należy wykonać czynności opisane w [wymagania wstępne dotyczące raportowania interfejsów API usługi Azure AD dostęp](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Tworzenie aplikacji, uzyskać klucz tajny i przyznać jej dostęp prawa do dzierżawy usługi Azure AD B2C raportów. *Bash skryptu* i *skrypt w języku Python* zamieszczono przykłady, które również w tym miejscu. 

## <a name="powershell-script"></a>Skrypt programu PowerShell
Ten skrypt pokazuje tworzenia raportów użycia czterech przy użyciu `TimeStamp` parametru i `ApplicationId` filtru.

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
* **tenantUserCount**: liczba użytkowników w dzierżawie według typu dostawcy tożsamości, dziennie w ciągu ostatnich 30 dni. (Opcjonalnie `TimeStamp` filtru zawiera liczby użytkowników z określonej daty do daty bieżącej). Raport zawiera:
  * **TotalUserCount**: liczba wszystkie obiekty użytkowników.
  * **OtherUserCount**: liczba użytkowników usługi Azure Active Directory (nie użytkowników usługi Azure AD B2C).
  * **LocalUserCount**: liczba kont użytkowników usługi Azure AD B2C utworzone przy użyciu poświadczeń lokalnych do dzierżawy usługi Azure AD B2C.

* **AlternateIdUserCount**: liczba użytkowników usługi Azure AD B2C zarejestrowane przy pomocy dostawcy tożsamości zewnętrznych (na przykład, Facebook, konta Microsoft lub innej dzierżawy usługi Azure Active Directory, nazywana także `OrgId`).

* **b2cAuthenticationCountSummary**: Podsumowanie codzienne numer rozliczeniowy uwierzytelnienia w ciągu ostatnich 30 dni dzień, a typ Przepływ uwierzytelniania.

* **b2cAuthenticationCount**: liczbę uwierzytelnień w przedziale czasu. Wartość domyślna to ostatnich 30 dni.  (Opcjonalnie: na rozpoczęcie i zakończenie `TimeStamp` parametry definiują w określonym przedziale czasu.) Dane wyjściowe obejmują `StartTimeStamp` (najwcześniejsza data działania dla tej dzierżawy) i `EndTimeStamp` (najnowsza aktualizacja).

* **b2cMfaRequestCountSummary**: podsumowanie liczby codzienne operacje uwierzytelniania wieloskładnikowego, dzień, a typ (SMS lub głosowych).


## <a name="limitations"></a>Ograniczenia
Użytkownik liczba dane są odświeżane co 24 do 48 godzin. Uwierzytelnienia są aktualizowane kilka razy dziennie. Korzystając z `ApplicationId` filtru odpowiedzi pusty raport może być spowodowane jedną z następujących warunków:
  * Identyfikator aplikacji nie istnieje w dzierżawie. Upewnij się, że jest poprawny.
  * Identyfikator aplikacji istnieje, ale nie znaleziono danych w tym okresie raportowania. Przejrzyj parametry daty/godziny.


## <a name="next-steps"></a>Kolejne kroki
### <a name="monthly-bill-estimates-for-azure-ad"></a>Rachunek miesięczny szacuje dla usługi Azure AD
W połączeniu z [najbardziej bieżące usługi Azure AD B2C ceny dostępne](https://azure.microsoft.com/pricing/details/active-directory-b2c/), można oszacować dzienne, tygodniowe i miesięczne wykorzystania platformy Azure.  Szacowana jest szczególnie przydatne podczas planowania zmiany w zachowaniu dzierżawy, który może mieć wpływ na całkowity koszt. Możesz przejrzeć koszty rzeczywiste w Twojej [połączonej subskrypcji Azure](active-directory-b2c-how-to-enable-billing.md).

### <a name="options-for-other-output-formats"></a>Opcje dla innych formatów wyjściowych
Poniższy kod przedstawia przykłady wysyłanie danych wyjściowych do formatu JSON, listy wartości nazw i XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
