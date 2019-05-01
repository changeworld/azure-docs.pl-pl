---
title: Dzienniki inspekcji przykładów i definicje w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przewodnik i przykłady związane z dostępem do dzienników inspekcji usługi Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fdc3578fe78e60086a148e4204019a8293030b83
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688765"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji usługi Azure AD B2C

Usługa Azure Active Directory B2C (Azure AD B2C), emituje dzienników inspekcji zawierający informacje o aktywności o zasobów B2C, wystawione tokeny i dostępu administratora. Ten artykuł zawiera krótkie omówienie informacje, które są dostępne za pośrednictwem dzienniki inspekcji i instrukcje dotyczące sposobu dostępu do tych danych dla dzierżawy usługi Azure AD B2C.

> [!IMPORTANT]
> Dzienniki inspekcji tylko są przechowywane przez 7 dni. Planowane pobierania i przechowywania dzienników przy użyciu jednej z metod poniżej, jeśli potrzebujesz dłuższy okres przechowywania danych.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Przegląd działań dostępnych w kategorii B2C dzienników inspekcji
**B2C** kategoria w dziennikach inspekcji zawiera następujące typy działań:

|Typ działania |Opis  |
|---------|---------|
|Autoryzacja |Działania dotyczące zezwolenia użytkownikowi dostęp do usługi B2C zasobów (na przykład administrator dostępu do listy zasad B2C)         |
|Katalog |Działania związane z atrybutów katalogu pobierane, gdy administrator loguje się przy użyciu witryny Azure Portal |
|Aplikacja | Wykonywanie operacji CRUD na aplikacji B2C |
|Klucz |Wykonywanie operacji CRUD na klucze przechowywane w kontenerze kluczy B2C |
|Resource |Wykonywanie operacji CRUD na B2C zasoby (na przykład zasady i dostawcy tożsamości)
|Authentication |Sprawdzanie poprawności poświadczeń użytkownika i wystawiania tokenów|

> [!NOTE]
> Działania CRUD obiektu użytkownika, można znaleźć **katalog podstawowy** kategorii.

## <a name="example-activity"></a>Przykład działania
Poniższy przykład pokazuje dane przechwycone, gdy użytkownik loguje się przy użyciu zewnętrznego dostawcy tożsamości: ![Dzienniki inspekcji — przykład](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Uzyskiwanie dostępu do dzienników inspekcji w portalu Azure
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Upewnij się, że znajdują się w katalogu usługi B2C.
2. Kliknij pozycję **usługi Azure Active Directory** na pasku po lewej stronie Ulubione
    
    ![Dzienniki inspekcji — przycisk usługi AAD](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. W obszarze **działania**, kliknij pozycję **dzienników inspekcji**

    ![Dzienniki inspekcji — sekcja dzienników](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. W **kategorii** dropbox, wybierz opcję **B2C**
3. Kliknij pozycję **zastosowania**

    ![Dzienniki inspekcji — kategoria](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Zostanie wyświetlona lista działań zarejestrowane w ciągu ostatnich siedmiu dni.
- Użyj **typ zasobu działania** listę rozwijaną, aby filtrować według typów działań opisanych powyżej
- Użyj **zakres dat** listę rozwijaną, aby filtrować zakres dat działania wyświetlane
- Po kliknięciu na określony wiersz w liście kontekstowe okno po prawej stronie zostanie wyświetlona dodatkowe atrybuty skojarzone z działania
- Kliknij pozycję **Pobierz** do pobrania działania jako plik csv

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Uzyskiwanie dostępu do dzienników inspekcji za pomocą interfejsu API raportowania usługi Azure AD
Dzienniki inspekcji są publikowane w tej samej potoku jako inne działania usługi Azure Active Directory, dzięki czemu są one dostępne za pośrednictwem [interfejsu API raportowania usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Wymagania wstępne
Do uwierzytelniania w usłudze Azure AD, interfejsu API raportowania, należy najpierw zarejestrować aplikację. Upewnij się, że postępuj zgodnie z instrukcjami w [wymagania wstępne dotyczące raportowania interfejsów API usługi Azure AD dostęp](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Uzyskiwanie dostępu do interfejsu API
Aby pobrać dzienniki inspekcji usługi Azure AD B2C za pośrednictwem interfejsu API, można filtrować dzienniki aby **B2C** kategorii. Aby filtrować według kategorii, należy użyć parametru ciągu zapytania podczas wywoływania raportowania w przypadku punktu końcowego interfejsu API usługi Azure AD, jak pokazano poniżej:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Skrypt programu PowerShell
Poniższy skrypt stanowi przykład użycia programu PowerShell do wykonywania zapytań interfejsu API raportowania usługi Azure AD i zapisać wyniki w formacie JSON:

```powershell
# This script will require registration of a Web Application in Azure Active Directory (see https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/)

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a Globally Unique ID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client Key/Secret string
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # AAD B2C Tenant; for example, contoso.onmicrosoft.com
$resource       = "https://graph.windows.net"             # Azure AD Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = 'https://graph.windows.net/' + $tenantdomain + '/activities/audit?api-version=beta&$filter=category eq ''B2C''and activityDate gt ' + $7daysago

    # loop through each query page (1 through n)
    Do {
        # display each event on the console window
        Write-Output "Fetching data using Uri: $url"
        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output ($event | ConvertTo-Json)
        }

        # save the query page to an output file
        Write-Output "Save the output to a file audit$i.json"
        $myReport.Content | Out-File -FilePath audit$i.json -Force
        $url = ($myReport.Content | ConvertFrom-Json).'@odata.nextLink'
        $i = $i+1
    } while($url -ne $null)
} else {
    Write-Host "ERROR: No Access Token"
}
```
