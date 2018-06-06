---
title: Dzienniki inspekcji, przykłady i definicje w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przewodnik i przykłady dotyczące uzyskiwania dostępu do dzienników inspekcji usługi Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4828bf2f0faa596c8222c3a36dc3d38ec1b3bd1a
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709822"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Podczas uzyskiwania dostępu do dzienników inspekcji usługi Azure AD B2C

Usługa Azure Active Directory B2C (Azure AD B2C) emituje dzienników inspekcji zawierający informacje działania o zasoby B2C, wystawione tokeny i uprawnień dostępu administratora. Ten artykuł zawiera krótki przegląd informacji o dostępnych za pośrednictwem dzienniki inspekcji i instrukcje na temat dzierżawy usługi Azure AD B2C dostęp do tych danych.

> [!IMPORTANT]
> Dzienniki inspekcji są przechowywane tylko na siedem dni. Planowane pobierania i przechowywania dzienników przy użyciu jednej z metod, pokazano poniżej, jeśli potrzebna jest dłuższy okres przechowywania. 

##<a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Przegląd działań dostępnych w kategorii B2C dzienniki inspekcji
**B2C** kategorii w dziennikach inspekcji zawiera następujące typy działań:
|Typ działania |Opis  |
|---------|---------|
|Autoryzacja |Działania dotyczące zezwolenia użytkownikowi na dostęp do usługi B2C zasobów (na przykład administrator podczas uzyskiwania dostępu do listy zasad B2C)         |
|Katalog |Działania związane z atrybutów katalogu pobierane, gdy administrator loguje się przy użyciu portalu Azure |
|Aplikacja | Operacje CRUD na aplikacji B2C |
|Klucz |Operacje CRUD na klucze przechowywane w kontenerze kluczy B2C |
|Zasób |Operacje CRUD na B2C zasobów (na przykład zasady i dostawców tożsamości)
|Authentication |Sprawdzanie poprawności poświadczeń użytkownika i wystawiania tokenu|

> [!NOTE]
> Działania CRUD obiektu użytkownika, można znaleźć w temacie **katalogu Core** kategorii.

##<a name="example-activity"></a>Przykład działania
W poniższym przykładzie pokazano dane przechwycone, jeśli użytkownik zaloguje się za pomocą dostawcy tożsamości zewnętrznych: ![dzienniki inspekcji — przykład](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

##<a name="accessing-audit-logs-through-the-azure-portal"></a>Uzyskiwanie dostępu do dzienników inspekcji za pośrednictwem portalu Azure
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Upewnij się, że znajdują się w katalogu usługi B2C.
2. Polecenie **usługi Azure Active Directory** na pasku Ulubione po lewej stronie 
    
    ![Dzienniki inspekcji — przycisk AAD](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. W obszarze **działania**, kliknij **dzienniki inspekcji**

    ![Dzienniki inspekcji - sekcji dzienników](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. W **kategorii** dropbox, wybierz opcję **B2C**
3. Polecenie **Zastosuj**

    ![Dzienniki inspekcji - kategorii](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Zostanie wyświetlona lista działań rejestrowane w ciągu ostatnich siedmiu dni. 
- Użyj **typu zasobu działania** listy rozwijanej, aby filtrować według typów działań opisanych powyżej
- Użyj **zakres dat** listy rozwijanej, aby filtrować zakres dat działania wyświetlane
- Po kliknięciu określonego wiersza na liście kontekstowych pole po prawej stronie zostanie wyświetlona dodatkowe atrybuty skojarzone z działaniem
- Polecenie **Pobierz** do pobrania działania w formacie pliku csv

##<a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Uzyskiwanie dostępu do dzienników inspekcji za pośrednictwem raportowania interfejsu API usługi Azure AD
Dzienniki inspekcji są publikowane w tej samej potoku jako inne działania usługi Azure Active Directory, tak aby były dostępne za pośrednictwem [interfejsem API raportowania usługi Azure Active Directory](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-api-audit-reference). 

###<a name="prerequisites"></a>Wymagania wstępne
Do uwierzytelniania raportowania interfejsu API usługi Azure AD, należy najpierw zarejestrować aplikację. Upewnij się, że postępuj zgodnie z instrukcjami [wymagania wstępne dotyczące raportowania interfejsów API usługi Azure AD dostęp](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

###<a name="accesing-the-api"></a>Interfejs API w celu dostępu
Aby pobrać dzienniki inspekcji usługi Azure AD B2C w interfejsie API, należy filtrować dzienniki, aby **B2C** kategorii. Aby filtrować według kategorii, należy użyć parametru ciągu zapytania podczas wywoływania usługi Azure AD raportowania punkt końcowy interfejsu API, jak pokazano poniżej:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

###<a name="powershell-script"></a>Skrypt programu PowerShell
Następującego skryptu przykładowego zapytania raportowania interfejsu API usługi Azure AD i zapisane wyniki w formacie JSON przy użyciu programu PowerShell:

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
    Do{
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

