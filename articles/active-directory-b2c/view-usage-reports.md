---
title: Przykłady i definicje interfejsu API raportowania użycia
titleSuffix: Azure AD B2C
description: Przewodnik i przykłady dotyczące uzyskiwania raportów na Azure AD B2C użytkowników dzierżawy, uwierzytelniania i uwierzytelniania wieloskładnikowego.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b4486c1459024d60b53292f070dd565d20d10c52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847734"
---
# <a name="accessing-usage-reports-in-azure-ad-b2c-via-the-reporting-api"></a>Uzyskiwanie dostępu do raportów użycia w Azure AD B2C za pośrednictwem interfejsu API raportowania

Azure Active Directory B2C (Azure AD B2C) zapewnia uwierzytelnianie na podstawie logowania użytkownika i Multi-Factor Authentication platformy Azure. Uwierzytelnianie jest udostępniane dla użytkowników końcowych rodziny aplikacji między dostawcami tożsamości. Gdy znasz liczbę użytkowników zarejestrowanych w dzierżawie, dostawcy, którzy zostali zarejestrowani, i liczbę uwierzytelnień według typu, możesz odpowiedzieć na pytania, takie jak:
* Ilu użytkowników z każdego typu dostawcy tożsamości (na przykład konta Microsoft lub LinkedIn) zarejestrowano w ciągu ostatnich 10 dni?
* Ile uwierzytelnień używających Multi-Factor Authentication zostało pomyślnie ukończonych w ciągu ostatniego miesiąca?
* Ile uwierzytelnień opartych na logowaniu zostało zakończonych w tym miesiącu? Dziennie? Na aplikację?
* Jak oszacować oczekiwany miesięczny koszt działania dzierżawy Azure AD B2C?

Ten artykuł koncentruje się na raportach związanych z działaniem rozliczeń, które są oparte na liczbie użytkowników, rozliczeniowych uwierzytelniania opartych na rejestrowaniu i uwierzytelnianiu wieloskładnikowym.


## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem należy wykonać kroki opisane w sekcji [wymagania wstępne, aby uzyskać dostęp do interfejsów API raportowania usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/). Utwórz aplikację, uzyskaj dla niej wpis tajny i Udziel jej praw dostępu do raportów dzierżawy Azure AD B2C. Przykłady *skryptów bash* i *skryptów języka Python* zostały również podane tutaj.

## <a name="powershell-script"></a>Skrypt programu PowerShell
Ten skrypt demonstruje tworzenie czterech raportów użycia przy użyciu parametru `TimeStamp` i filtru `ApplicationId`.

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
* **tenantUserCount**: liczba użytkowników w dzierżawie według typu dostawcy tożsamości, dziennie w ciągu ostatnich 30 dni. (Opcjonalnie filtr `TimeStamp` zapewnia liczbę użytkowników od określonej daty do bieżącej daty). Raport zawiera następujące informacje:
  * **TotalUserCount**: liczba wszystkich obiektów użytkownika.
  * **OtherUserCount**: liczba użytkowników, którzy Azure Active Directory (nie Azure AD B2C użytkowników).
  * **LocalUserCount**: liczba kont użytkowników Azure AD B2C utworzonych przy użyciu poświadczeń lokalnych dla dzierżawy Azure AD B2C.

* **AlternateIdUserCount**: Azure AD B2C liczba użytkowników zarejestrowanych z zewnętrznymi dostawcami tożsamości (na przykład Facebook, konto Microsoft lub innej dzierżawy Azure Active Directory, nazywana również `OrgId`).

* **b2cAuthenticationCountSummary**: Podsumowanie dziennej liczby uwierzytelnień rozliczanych w ciągu ostatnich 30 dni według dnia i typu przepływu uwierzytelniania.

* **b2cAuthenticationCount**: liczba uwierzytelnień w przedziale czasu. Wartość domyślna to 30 ostatnich dni.  (Opcjonalnie: początkowe i końcowe parametry `TimeStamp` definiują określony przedział czasu). Dane wyjściowe zawierają `StartTimeStamp` (najwcześniejszą datę działania dla tej dzierżawy) i `EndTimeStamp` (Najnowsza aktualizacja).

* **b2cMfaRequestCountSummary**: Podsumowanie dziennej liczby uwierzytelnień wieloskładnikowych, według dnia i typu (SMS lub głos).


## <a name="limitations"></a>Ograniczenia
Dane liczb użytkowników są odświeżane co 24 do 48 godzin. Uwierzytelnienia są aktualizowane kilka razy dziennie. W przypadku korzystania z filtru `ApplicationId` pustą odpowiedzią raportu może być jeden z następujących warunków:
  * Identyfikator aplikacji nie istnieje w dzierżawie. Upewnij się, że jest ona poprawna.
  * Identyfikator aplikacji istnieje, ale w okresie raportowania nie znaleziono żadnych danych. Przejrzyj parametry daty/godziny.


## <a name="next-steps"></a>Następne kroki
### <a name="monthly-bill-estimates-for-azure-ad"></a>Oszacowania miesięcznego rozliczeń w usłudze Azure AD
W połączeniu z [najnowszymi dostępnymi cenami Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)można oszacować dzienne, tygodniowe i miesięczne użycie platformy Azure.  Oszacowanie jest szczególnie przydatne w przypadku planowania zmian w zachowaniu dzierżawy, które mogą mieć wpływ na ogólny koszt. Rzeczywiste koszty można sprawdzić w [połączonej subskrypcji platformy Azure](billing.md).

### <a name="options-for-other-output-formats"></a>Opcje innych formatów danych wyjściowych
Poniższy kod przedstawia przykłady wysyłania danych wyjściowych do formatu JSON, listy wartości nazw i XML:
```powershell
# to output to JSON use following line in the PowerShell sample
$myReport.Content | Out-File -FilePath name-your-file.json -Force

# to output the content to a name value list
($myReport.Content | ConvertFrom-Json).value | Out-File -FilePath name-your-file.txt -Force

# to output the content in XML use the following line
(($myReport.Content | ConvertFrom-Json).value | ConvertTo-Xml).InnerXml | Out-File -FilePath name-your-file.xml -Force
```
