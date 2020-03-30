---
title: Uzyskiwanie dostępu do dzienników inspekcji i przeglądanie ich
titleSuffix: Azure AD B2C
description: Jak uzyskać dostęp do dzienników inspekcji usługi Azure AD B2C programowo i w witrynie Azure portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 83086fa2cb96eba423b9111134a0406d7256821f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264221"
---
# <a name="accessing-azure-ad-b2c-audit-logs"></a>Uzyskiwanie dostępu do dzienników inspekcji usługi Azure AD B2C

Usługa Azure Active Directory B2C (Azure AD B2C) emituje dzienniki inspekcji zawierające informacje o aktywności dotyczące zasobów B2C, wystawionych tokenów i dostępu administratora. Ten artykuł zawiera krótkie omówienie informacji dostępnych w dziennikach inspekcji i instrukcje dotyczące uzyskiwania dostępu do tych danych dla dzierżawy usługi Azure AD B2C.

Zdarzenia dziennika inspekcji są zachowywane tylko przez **siedem dni**. Zaplanuj pobranie i przechowywanie dzienników przy użyciu jednej z poniższych metod, jeśli potrzebujesz dłuższego okresu przechowywania.

> [!NOTE]
> Nie widać logowania użytkowników dla poszczególnych aplikacji usługi Azure AD B2C w sekcji **Użytkownicy** **usługi Azure Active Directory** lub stron Usługi Azure **AD B2C** w witrynie Azure Portal. Zdarzenia logowania tam pokazać aktywność użytkownika, ale nie można skorelować z powrotem do aplikacji B2C, że użytkownik zalogowany do. Należy użyć dzienników inspekcji do tego, jak wyjaśniono w dalszej części tego artykułu.

## <a name="overview-of-activities-available-in-the-b2c-category-of-audit-logs"></a>Przegląd działań dostępnych w kategorii B2C dzienników audytu

Kategoria **B2C** w dziennikach inspekcji zawiera następujące typy działań:

|Typ działania |Opis  |
|---------|---------|
|Autoryzacja |Działania dotyczące autoryzacji użytkownika dostępu do zasobów B2C (na przykład administrator uzyskujący dostęp do listy zasad B2C).         |
|Katalog |Działania związane z atrybutami katalogu pobierane, gdy administrator loguje się przy użyciu witryny Azure portal. |
|Aplikacja | Tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) operacji w aplikacjach B2C. |
|Klucz |Operacje CRUD na kluczach przechowywanych w kontenerze kluczy B2C. |
|Zasób |crud na zasobach B2C. Na przykład zasady i dostawców tożsamości.
|Uwierzytelnianie |Sprawdzanie poprawności poświadczeń użytkownika i wystawienie tokenu.|

Informacje na temat działań CRUD obiektu użytkownika można znaleźć w kategorii **Katalog podstawowy.**

## <a name="example-activity"></a>Przykładowe działanie

Ten przykładowy obraz z witryny Azure portal pokazuje dane przechwycone, gdy użytkownik loguje się za pomocą zewnętrznego dostawcy tożsamości, w tym przypadku Facebook:

![Przykład strony Szczegóły aktywności dziennika inspekcji w witrynie Azure portal](./media/view-audit-logs/audit-logs-example.png)

Panel szczegółów działania zawiera następujące istotne informacje:

|Sekcja|Pole|Opis|
|-------|-----|-----------|
| Działanie | Nazwa | Jakie działania miały miejsce. Na przykład *Rozejmij id_token do aplikacji*, która kończy rzeczywiste logowanie użytkownika. |
| Zainicjowane przez (aktor) | ObjectId | **Identyfikator obiektu** aplikacji B2C, do których użytkownik się loguje. Ten identyfikator nie jest widoczny w witrynie Azure portal, ale jest dostępny za pośrednictwem interfejsu API programu Microsoft Graph. |
| Zainicjowane przez (aktor) | Spn | **Identyfikator aplikacji** B2C, do których użytkownik się loguje. |
| Elementy docelowe | ObjectId | **Identyfikator obiektu** użytkownika, który się loguje. |
| Dodatkowe szczegóły | TenantId | **Identyfikator dzierżawy** dzierżawy usługi Azure AD B2C. |
| Dodatkowe szczegóły | PolicyId | **Identyfikator zasad** przepływu użytkownika (zasady) używany do logowania użytkownika. |
| Dodatkowe szczegóły | ApplicationId | **Identyfikator aplikacji** B2C, do których użytkownik się loguje. |

## <a name="view-audit-logs-in-the-azure-portal"></a>Wyświetlanie dzienników inspekcji w witrynie Azure portal

Portal Azure zapewnia dostęp do zdarzeń dziennika inspekcji w dzierżawie usługi Azure AD B2C.

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Przełącz się do katalogu zawierającego dzierżawę usługi Azure AD B2C, a następnie przejdź do **usługi Azure AD B2C.**
1. W obszarze **Działania** w menu po lewej stronie wybierz pozycję **Inspekcja dzienników**.

Zostanie wyświetlona lista zdarzeń aktywności zarejestrowanych w ciągu ostatnich siedmiu dni.

![Przykładowy filtr z dwoma zdarzeniami aktywności w witrynie Azure portal](./media/view-audit-logs/audit-logs-example-filter.png)

Dostępnych jest kilka opcji filtrowania, w tym:

* **Typ zasobu działania** — filtrowanie według typów działań wyświetlanych w tabeli w sekcji [Omówienie dostępnych działań.](#overview-of-activities-available-in-the-b2c-category-of-audit-logs)
* **Data** — filtruj zakres dat wyświetlanych działań.

Jeśli wybierzesz wiersz na liście, zostaną wyświetlone szczegóły działania dla zdarzenia.

Aby pobrać listę zdarzeń aktywności w pliku wartości oddzielonych przecinkami (CSV), wybierz **pobierz**.

## <a name="get-audit-logs-with-the-azure-ad-reporting-api"></a>Pobierz dzienniki inspekcji za pomocą interfejsu API raportowania usługi Azure AD

Dzienniki inspekcji są publikowane w tym samym potoku co inne działania usługi Azure Active Directory, dzięki czemu można uzyskać do nich dostęp za pośrednictwem [interfejsu API raportowania usługi Azure Active Directory.](https://docs.microsoft.com/graph/api/directoryaudit-list) Aby uzyskać więcej informacji, zobacz [Wprowadzenie do interfejsu API raportowania usługi Azure Active Directory](../active-directory/reports-monitoring/concept-reporting-api.md).

### <a name="enable-reporting-api-access"></a>Włączanie raportowania dostępu do interfejsu API

Aby zezwolić na dostęp oparty na skryptach lub aplikacjach do interfejsu API raportowania usługi Azure AD, potrzebujesz aplikacji zarejestrowanej w dzierżawie usługi Azure AD B2C z następującymi uprawnieniami interfejsu API. Można włączyć te uprawnienia do istniejącej rejestracji aplikacji w dzierżawie B2C lub utworzyć nowy specjalnie do użytku z automatyzacją dziennika inspekcji.

* Microsoft Graph > uprawnienia aplikacji > AuditLog > AuditLog.Read.All

Wykonaj kroki opisane w poniższym artykule, aby zarejestrować aplikację z wymaganymi uprawnieniami:

[Zarządzanie usługą Azure AD B2C za pomocą programu Microsoft Graph](microsoft-graph-get-started.md)

Po zarejestrowaniu aplikacji z odpowiednimi uprawnieniami, zobacz sekcji skrypt programu PowerShell w dalszej części tego artykułu na przykład, jak można uzyskać zdarzenia aktywności za pomocą skryptu.

### <a name="access-the-api"></a>Dostęp do interfejsu API

Aby pobrać zdarzenia dziennika inspekcji usługi Azure AD B2C `B2C` za pośrednictwem interfejsu API, filtruj dzienniki w kategorii. Aby filtrować według kategorii, należy użyć parametru `filter` ciągu kwerendy podczas wywoływania punktu końcowego interfejsu API raportowania usługi Azure AD.

```HTTP
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=loggedByService eq 'B2C' and activityDateTime gt 2019-09-10T02:28:17Z
```

### <a name="powershell-script"></a>Skrypt programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykład sposobu wykonywania zapytań o interfejs API raportowania usługi Azure AD. Po kwerendzie interfejsu API, drukuje zarejestrowane zdarzenia do standardowego wyjścia, a następnie zapisuje dane wyjściowe JSON do pliku.

Możesz wypróbować ten skrypt w [usłudze Azure Cloud Shell](overview.md). Należy zaktualizować go za pomocą identyfikatora aplikacji, klucza tajnego klienta i nazwy dzierżawy usługi Azure AD B2C.

```powershell
# This script requires an application registration that's granted Microsoft Graph API permission
# https://docs.microsoft.com/azure/active-directory-b2c/microsoft-graph-get-started

# Constants
$ClientID       = "your-client-application-id-here"       # Insert your application's client ID, a GUID
$ClientSecret   = "your-client-application-secret-here"   # Insert your application's client secret
$tenantdomain   = "your-b2c-tenant.onmicrosoft.com"       # Insert your Azure AD B2C tenant domain name

$loginURL       = "https://login.microsoftonline.com"
$resource       = "https://graph.microsoft.com"           # Microsoft Graph API resource URI
$7daysago       = "{0:s}" -f (get-date).AddDays(-7) + "Z" # Use 'AddMinutes(-5)' to decrement minutes, for example
Write-Output "Searching for events starting $7daysago"

# Create HTTP header, get an OAuth2 access token based on client id, secret and tenant domain
$body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
$oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

# Parse audit report items, save output to file(s): auditX.json, where X = 0 through n for number of nextLink pages
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

Oto reprezentacja JSON przykładowego zdarzenia działania pokazanego wcześniej w artykule:

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

Można zautomatyzować inne zadania administracyjne, na przykład [zarządzać kontami użytkowników usługi Azure AD B2C za pomocą programu Microsoft Graph](manage-user-accounts-graph-api.md).
