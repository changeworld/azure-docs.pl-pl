---
title: Przykłady i definicje dzienników inspekcji w Azure Active Directory B2C | Microsoft Docs
description: Przewodnik i przykłady dotyczące uzyskiwania dostępu do dzienników inspekcji Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: d8cc67b8e243fb2b97cd1522a850adc63c84428e
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69969625"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) emituje dzienniki inspekcji zawierające informacje o działaniu dotyczące zasobów B2C, wystawionych tokenów i dostępu administratora. Ten artykuł zawiera krótkie omówienie informacji dostępnych za pomocą dzienników inspekcji i instrukcje dotyczące uzyskiwania dostępu do tych danych dla dzierżawy Azure AD B2C.

> [!IMPORTANT]
> Dzienniki inspekcji są przechowywane tylko przez siedem dni. Zaplanuj pobieranie i przechowywanie dzienników przy użyciu jednej z metod przedstawionych poniżej, jeśli potrzebujesz dłuższego okresu przechowywania.

> [!NOTE]
> Nie można zobaczyć logowania użytkowników dla poszczególnych aplikacji Azure AD B2C w sekcji **Użytkownicy** w **Azure Active Directory** lub **Azure AD B2C** . W przypadku logowania zostanie wyświetlona aktywność użytkownika, ale nie będzie możliwe jej skorelowanie z aplikacją B2C, w której użytkownik zalogował się. Należy użyć dzienników inspekcji dla tego programu, jak wyjaśniono dalsze w tym artykule.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Przegląd działań dostępnych w kategorii B2C dzienników inspekcji
Kategoria **B2C** w dziennikach inspekcji zawiera następujące typy działań:

|Typ działania |Opis  |
|---------|---------|
|Authorization |Działania dotyczące autoryzacji użytkownika w celu uzyskania dostępu do zasobów B2C (na przykład administrator uzyskujący dostęp do listy zasad B2C)         |
|Katalog |Działania związane z atrybutami katalogu pobrane, gdy administrator zaloguje się przy użyciu Azure Portal |
|Aplikacja | Operacje CRUD na aplikacjach B2C |
|Klucz |Operacje CRUD na kluczach przechowywanych w kontenerze kluczy B2C |
|Resource |CRUD operacji na zasobach B2C (na przykład zasad i dostawców tożsamości)
|Authentication |Weryfikowanie poświadczeń użytkownika i wystawianie tokenów|

> [!NOTE]
> W przypadku działań CRUD obiektów użytkownika zapoznaj się z kategorią **katalogu podstawowego** .

## <a name="example-activity"></a>Przykładowe działanie
W poniższym przykładzie przedstawiono dane przechwycone, gdy użytkownik loguje się przy użyciu zewnętrznego dostawcy tożsamości:  
    ![Przykład strony szczegółów działania dziennika inspekcji w Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Panel szczegóły działania zawiera następujące informacje:

|`Section`|Pole|Opis|
|-------|-----|-----------|
| Działanie | Name | Jakie działanie miało miejsce. Na przykład "Wydaj id_token do aplikacji" (która zawiera rzeczywiste Logowanie użytkownika). |
| Zainicjowane przez (aktor) | ObjectId | **Identyfikator obiektu** aplikacji B2C, do której loguje się użytkownik (ten identyfikator nie jest widoczny w Azure Portal ale jest dostępny za pośrednictwem interfejs API programu Graph na przykład). |
| Zainicjowane przez (aktor) | Główna nazwa usługi | **Identyfikator aplikacji** B2C, w której loguje się użytkownik. |
| Elementy docelowe | ObjectId | **Identyfikator obiektu** użytkownika, który loguje się. |
| Dodatkowe szczegóły | TenantId | **Identyfikator dzierżawy** dzierżawy Azure AD B2C. |
| Dodatkowe szczegóły | `PolicyId` | **Identyfikator zasad** przepływu użytkownika (zasady) używany do podpisywania użytkownika w programie. |
| Dodatkowe szczegóły | ApplicationId | **Identyfikator aplikacji** B2C, w której loguje się użytkownik. |

## <a name="accessing-audit-logs-through-the-azure-portal"></a>Uzyskiwanie dostępu do dzienników inspekcji za pomocą Azure Portal
1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Upewnij się, że jesteś w katalogu B2C.
2. Kliknij **Azure Active Directory** na pasku ulubionych po lewej stronie.

    ![Wyróżniono przycisk Azure Active Directory w menu portalu po lewej stronie](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-aad.png)

1. W obszarze **aktywność**kliknij pozycję **dzienniki inspekcji** .

    ![Przycisk dzienników inspekcji wyróżniony w sekcji działanie menu](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-section.png)

2. W **kategorii** Dropbox wybierz pozycję **B2C**
3. Kliknij przycisk **Zastosuj**

    ![Przycisk Kategoria i Zastosuj wyróżniony w filtrze dziennika inspekcji](./media/active-directory-b2c-reference-audit-logs/audit-logs-portal-category.png)

Zostanie wyświetlona lista działań zarejestrowanych w ciągu ostatnich siedmiu dni.
- Użyj listy rozwijanej **Typ zasobu działania** , aby filtrować według typów działań wymienionych powyżej
- Użyj listy rozwijanej **zakres dat** , aby odfiltrować zakres dat wyświetlanych działań
- Jeśli klikniesz konkretny wiersz na liście, w polu kontekstowym po prawej stronie zostaną wyświetlone dodatkowe atrybuty skojarzone z działaniem
- Kliknij pozycję **Pobierz** , aby pobrać działania jako plik CSV

> [!NOTE]
> Dzienniki inspekcji można także wyświetlić, przechodząc do **Azure AD B2C** , a nie **Azure Active Directory** na pasku ulubionych po lewej stronie. W obszarze **działania**kliknij pozycję **dzienniki inspekcji**, gdzie znajdują się te same dzienniki z podobnymi możliwościami filtrowania.

## <a name="accessing-audit-logs-through-the-azure-ad-reporting-api"></a>Uzyskiwanie dostępu do dzienników inspekcji za pomocą interfejsu API raportowania usługi Azure AD
Dzienniki inspekcji są publikowane w tym samym potoku co inne działania dotyczące Azure Active Directory, dzięki czemu można uzyskać do nich dostęp za pomocą [interfejsu API raportowania Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference).

### <a name="prerequisites"></a>Wymagania wstępne
Aby uwierzytelnić się w interfejsie API raportowania usługi Azure AD, musisz najpierw zarejestrować aplikację. Wykonaj kroki opisane w sekcji [wymagania wstępne, aby uzyskać dostęp do interfejsów API raportowania usługi Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-reporting-api-getting-started/).

### <a name="accessing-the-api"></a>Uzyskiwanie dostępu do interfejsu API
Aby pobrać Azure AD B2C dzienniki inspekcji za pośrednictwem interfejsu API, należy przefiltrować dzienniki do kategorii **B2C** . Aby filtrować według kategorii, użyj parametru ciągu zapytania podczas wywoływania punktu końcowego interfejsu API raportowania usługi Azure AD, jak pokazano poniżej:

`https://graph.windows.net/your-b2c-tentant.onmicrosoft.com/activities/audit?api-version=beta&$filter=category eq 'B2C'`

### <a name="powershell-script"></a>Skrypt programu PowerShell
Poniższy skrypt zawiera przykład użycia programu PowerShell do wysyłania zapytań do interfejsu API raportowania usługi Azure AD i przechowywania wyników w postaci pliku JSON:

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
