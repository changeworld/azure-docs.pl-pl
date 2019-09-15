---
title: Przykłady i definicje dzienników inspekcji w Azure Active Directory B2C
description: Przewodnik i przykłady dotyczące uzyskiwania dostępu do dzienników inspekcji Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: cbb748e9856b6de9004d57e4393e205ddfcfffb4
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998845"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) emituje dzienniki inspekcji zawierające informacje o działaniu dotyczące zasobów B2C, wystawionych tokenów i dostępu administratora. Ten artykuł zawiera krótkie omówienie informacji dostępnych w dziennikach inspekcji i instrukcje dotyczące uzyskiwania dostępu do tych danych dla dzierżawy Azure AD B2C.

Zdarzenia dziennika inspekcji są przechowywane tylko przez **siedem dni**. Zaplanuj pobieranie i przechowywanie dzienników przy użyciu jednej z metod przedstawionych poniżej, jeśli potrzebujesz dłuższego okresu przechowywania.

> [!NOTE]
> Nie widzisz logowania użytkownika dla poszczególnych aplikacji Azure AD B2C w sekcji **użytkownicy** **Azure Active Directory** lub **Azure AD B2C** stron w Azure Portal. Zdarzenia logowania wyświetlają aktywność użytkownika, ale nie mogą być skorelowane z powrotem do aplikacji B2C, do której użytkownik zalogował się. Należy użyć dzienników inspekcji dla tego programu, jak wyjaśniono dalsze w tym artykule.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Przegląd działań dostępnych w kategorii B2C dzienników inspekcji

Kategoria **B2C** w dziennikach inspekcji zawiera następujące typy działań:

|Typ działania |Opis  |
|---------|---------|
|Authorization |Działania dotyczące autoryzacji użytkownika w celu uzyskania dostępu do zasobów B2C (na przykład administratorów uzyskujących dostęp do listy zasad B2C).         |
|Katalog |Działania związane z atrybutami katalogu pobrane, gdy administrator zaloguje się przy użyciu Azure Portal. |
|Aplikacja | Tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) operacji na aplikacjach B2C. |
|Klucz |Operacje CRUD na kluczach przechowywanych w kontenerze kluczy B2C. |
|Resource |CRUD operacji na zasobach B2C. Na przykład zasady i dostawcy tożsamości.
|Authentication |Weryfikowanie poświadczeń użytkownika i wystawianie tokenów.|

W przypadku działań CRUD obiektów użytkownika zapoznaj się z kategorią **katalogu podstawowego** .

## <a name="example-activity"></a>Przykładowe działanie

Ten przykładowy obraz z Azure Portal przedstawia dane przechwycone, gdy użytkownik loguje się przy użyciu zewnętrznego dostawcy tożsamości, w tym przypadku w serwisie Facebook:

![Przykład strony szczegółów działania dziennika inspekcji w Azure Portal](./media/active-directory-b2c-reference-audit-logs/audit-logs-example.png)

Panel szczegóły działania zawiera następujące informacje:

|`Section`|Pole|Opis|
|-------|-----|-----------|
| Działanie | Name | Jakie działanie miało miejsce. Na przykład *wydaj id_token do aplikacji*, która zawiera rzeczywiste Logowanie użytkownika. |
| Zainicjowane przez (aktor) | ObjectId | **Identyfikator obiektu** aplikacji B2C, w której loguje się użytkownik. Ten identyfikator nie jest widoczny w Azure Portal, ale jest dostępny za pośrednictwem interfejsu API Microsoft Graph. |
| Zainicjowane przez (aktor) | Główna nazwa usługi | **Identyfikator aplikacji** B2C, w której loguje się użytkownik. |
| Elementy docelowe | ObjectId | **Identyfikator obiektu** użytkownika, który loguje się. |
| Dodatkowe szczegóły | TenantId | **Identyfikator dzierżawy** dzierżawy Azure AD B2C. |
| Dodatkowe szczegóły | `PolicyId` | **Identyfikator zasad** przepływu użytkownika (zasady) używany do podpisywania użytkownika w programie. |
| Dodatkowe szczegóły | ApplicationId | **Identyfikator aplikacji** B2C, w której loguje się użytkownik. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Wyświetlanie dzienników inspekcji w Azure Portal

Azure Portal zapewnia dostęp do zdarzeń dziennika inspekcji w dzierżawie Azure AD B2C.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do katalogu, który zawiera Azure AD B2C dzierżawy, a następnie przejdź do **Azure AD B2C**.
1. W obszarze **działania** w menu po lewej stronie wybierz pozycję **dzienniki inspekcji**.

Zostanie wyświetlona lista zdarzeń działania zarejestrowanych w ciągu ostatnich siedmiu dni.

![Przykładowy filtr z dwoma zdarzeniami działania w Azure Portal](media/active-directory-b2c-reference-audit-logs/audit-logs-example-filter.png)

Dostępne są kilka opcji filtrowania, w tym:

* **Typ zasobu działania** — filtr według typów działań przedstawionych w tabeli w sekcji [Omówienie dostępnych działań](#overview-of-activities-available-in-the-b2c-category-of-audit-logs) .
* **Data** — filtruje zakres dat wyświetlanych działań.

Po wybraniu wiersza na liście zostaną wyświetlone szczegóły działania dla zdarzenia.

Aby pobrać listę zdarzeń działania w pliku wartości rozdzielanych przecinkami (CSV), wybierz pozycję **Pobierz**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Pobieranie dzienników inspekcji za pomocą interfejsu API raportowania usługi Azure AD

Dzienniki inspekcji są publikowane w tym samym potoku co inne działania dotyczące Azure Active Directory, dzięki czemu można uzyskać do nich dostęp za pomocą [interfejsu API raportowania Azure Active Directory](https://docs.microsoft.com/graph/api/directoryaudit-list). Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z interfejsem API raportowania Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Włącz dostęp do interfejsu API raportowania

Aby zezwolić na dostęp skryptowy lub oparty na aplikacji do interfejsu API raportowania usługi Azure AD, potrzebujesz aplikacji Azure Active Directory zarejestrowanej w dzierżawie Azure AD B2C z następującymi uprawnieniami interfejsu API:

* Microsoft Graph
  * Aplikacja: Odczytaj wszystkie dane dziennika inspekcji

Można włączyć te uprawnienia do istniejącej rejestracji aplikacji w ramach dzierżawy usługi B2C lub utworzyć nowy, przeznaczony do użycia z automatyzacją dzienników inspekcji.

Aby utworzyć nową aplikację, przypisz wymagane uprawnienia interfejsu API i Utwórz klucz tajny klienta, wykonując następujące czynności:

1. Zarejestruj aplikację
    1. Zaloguj się do [Azure Portal](https://portal.azure.com), przejdź do katalogu, który zawiera dzierżawę Azure AD B2C, a następnie przejdź do **Azure AD B2C**.
    1. W obszarze **Zarządzaj** w menu po lewej stronie wybierz pozycję **rejestracje aplikacji (starsza wersja)** .
    1. Wybierz pozycję **rejestracja nowej aplikacji**
    1. Wprowadź nazwę aplikacji. Na przykład *aplikacja dziennika inspekcji*.
    1. Wprowadź dowolny prawidłowy adres URL w **adresie URL logowania**. Na przykład *https://localhost* . Ten punkt końcowy nie musi być osiągalny, ale musi być prawidłowym adresem URL.
    1. Wybierz pozycję **Utwórz**.
    1. Zapisz **Identyfikator aplikacji** , który pojawia się na stronie **zarejestrowana aplikacja** . Ta wartość jest wymagana do uwierzytelniania w skryptach automatyzacji, takich jak przykładowy skrypt programu PowerShell przedstawiony w dalszej części.
1. Przypisywanie uprawnień dostępu do interfejsu API
    1. Na stronie Przegląd **zarejestrowanej aplikacji** wybierz pozycję **Ustawienia**.
    1. W obszarze **dostęp do interfejsu API**wybierz pozycję **wymagane uprawnienia**.
    1. Wybierz pozycję **Dodaj**, a następnie **Wybierz interfejs API**.
    1. Wybierz pozycję **Microsoft Graph**, a następnie **Wybierz pozycję**.
    1. W obszarze **uprawnienia aplikacji**wybierz opcję **Czytaj wszystkie dane dziennika inspekcji**.
    1. Wybierz przycisk **Wybierz** , a następnie wybierz pozycję **gotowe**.
    1. Wybierz **udzielić uprawnień**, a następnie wybierz pozycję **tak**.
1. Utwórz klucz tajny klienta
    1. W obszarze **dostęp do interfejsu API**wybierz pozycję **klucze**.
    1. Wprowadź opis klucza w polu **Opis klucza** . Na przykład *klucz dziennika inspekcji*.
    1. Wybierz **okres**ważności, a następnie wybierz pozycję **Zapisz**.
    1. Zapisz **wartość**klucza. Ta wartość jest wymagana do uwierzytelniania w skryptach automatyzacji, takich jak przykładowy skrypt programu PowerShell przedstawiony w dalszej części.

Masz teraz aplikację z wymaganym dostępem do interfejsu API, IDENTYFIKATORem aplikacji i kluczem, którego można użyć w skryptach automatyzacji. Zapoznaj się z sekcją skryptu programu PowerShell w dalszej części tego artykułu, aby zapoznać się z przykładem, jak można pobrać zdarzenia dotyczące aktywności ze skryptem.

### <a name="access-the-api"></a>Dostęp do interfejsu API

Aby pobrać Azure AD B2C zdarzenia dziennika inspekcji za pośrednictwem interfejsu API, przefiltruj `B2C` dzienniki w kategorii. Aby filtrować według kategorii, użyj `filter` parametru ciągu zapytania podczas wywoływania punktu końcowego interfejsu API raportowania usługi Azure AD.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Skrypt programu PowerShell

Poniższy skrypt programu PowerShell przedstawia przykład sposobu wykonywania zapytania dotyczącego interfejsu API raportowania usługi Azure AD. Po wykonaniu zapytania dotyczącego interfejsu API program drukuje zarejestrowane zdarzenia do wyjścia standardowego, a następnie zapisuje dane wyjściowe JSON do pliku.

Możesz wypróbować ten skrypt w [Azure Cloud Shell](../cloud-shell/overview.md). Pamiętaj, aby zaktualizować go przy użyciu identyfikatora aplikacji, klucza i nazwy dzierżawy Azure AD B2C.

```powershell
# This script requires the registration of a Web Application in Azure Active Directory:
# https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-reporting-api

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's Client ID, a GUID (registered by Global Admin)
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's Client secret/key
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant; for example, contoso.onmicrosoft.com
$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 thru n for number of nextLink pages
if ($oauth.access_token -ne $null) {
    $i=0
    $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}
    $url = "https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?`$filter=loggedByService eq 'B2C' and activityDateTime gt  " + $7daysago

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

Oto reprezentacja danych JSON przykładowego zdarzenia działania wyświetlonego wcześniej w artykule:

```JSON
{
    "id": "B2C_DQO3J_4984536",
    "category": "Authentication",
    "correlationId": "00000000-0000-0000-0000-000000000000",
    "result": "success",
    "resultReason": "N/A",
    "activityDisplayName": "Issue an id_token to the application",
    "activityDateTime": "2019-09-14T18:13:17.0618117Z",
    "loggedByService": "B2C",
    "operationType": "",
    "initiatedBy": {
        "user": null,
        "app": {
            "appId": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "servicePrincipalId": null,
            "servicePrincipalName": "00000000-0000-0000-0000-000000000000"
        }
    },
    "targetResources": [
        {
            "id": "00000000-0000-0000-0000-000000000000",
            "displayName": null,
            "type": "User",
            "userPrincipalName": null,
            "groupType": null,
            "modifiedProperties": []
        }
    ],
    "additionalDetails": [
        {
            "key": "TenantId",
            "value": "test.onmicrosoft.com"
        },
        {
            "key": "PolicyId",
            "value": "B2C_1A_signup_signin"
        },
        {
            "key": "ApplicationId",
            "value": "00000000-0000-0000-0000-000000000000"
        },
        {
            "key": "Client",
            "value": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/76.0.3809.132 Safari/537.36"
        },
        {
            "key": "IdentityProviderName",
            "value": "facebook"
        },
        {
            "key": "IdentityProviderApplicationId",
            "value": "0000000000000000"
        },
        {
            "key": "ClientIpAddress",
            "value": "127.0.0.1"
        }
    ]
}
```

## <a name="next-steps"></a>Następne kroki

Można zautomatyzować inne zadania administracyjne, na przykład [zarządzać użytkownikami przy użyciu platformy .NET](active-directory-b2c-devquickstarts-graph-dotnet.md).
