---
title: Operacje dostawca usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Szczegóły operacje dostępne dla dostawców zasobów Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 0b8c8823c6d21df96dcfd926db1855169f1570e4
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Operacje Menedżera zasobów dostawcy zasobów platformy Azure

Ten dokument zawiera listę czynności dla każdego dostawcy zasobów programu Microsoft Azure Resource Manager. Mogą one używane w niestandardowych ról do zapewniają szczegółowe uprawnienia kontroli dostępu opartej na rolach (RBAC) do zasobów na platformie Azure. Należy pamiętać, nie jest to kompleksowe i operacje może dodać lub usunąć każdy dostawca jest aktualizowana. Operacja ciągów wykonaj format `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Lista bieżących i wszechstronne użyj `Get-AzureRmProviderOperation` (w programie PowerShell) lub `az provider operation list` (w wiersza polecenia platformy Azure w wersji 2) do operacji listy dostawców zasobów platformy Azure.

## <a name="microsoftaad"></a>Microsoft.AAD

| Operacja | Opis |
|---|---|
|/domainServices/delete|Deletes Domain Services.|
|/domainServices/read|Reads Domain Services.|
|/domainServices/write|Write Domain Services|
|/Locations/operationresults/Read|Przeczytaj stan operacji asynchronicznej.|
|Operacje/odczytu|Zlokalizowane przyjazny opis dla tej operacji, w jakiej powinny być wyświetlane dla użytkownika.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Operacja | Opis |
|---|---|
|/diagnosticsettings/DELETE|Usuwanie ustawienie diagnostyczne|
|/diagnosticsettings/Read|Odczytywanie ustawienie diagnostyczne|
|/ diagnosticsettings/zapisu|Zapisywanie ustawień diagnostycznych|
|/diagnosticsettingscategories/Read|Odczytywanie kategorii ustawienie diagnostyczne|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla dzierżawców|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Operacja | Opis |
|---|---|
|/ configuration/działania|Aktualizacje konfiguracji dzierżawy.|
|/Configuration/Read|Odczytuje konfiguracji dzierżawy.|
|/ configuration/zapisu|Tworzy konfiguracji dzierżawy.|
|/ services/działania|Aktualizuje wystąpienie usługi, w dzierżawie.|
|/services/alerts/read|Odczytuje alerty dla usługi.|
|/services/alerts/read|Odczytuje alerty dla usługi.|
|/services/delete|Usuwa wystąpienie usługi, w dzierżawie.|
|/Services/Read|Odczytuje wystąpień usługi w dzierżawie.|
|/Services/servicemembers/Action|Tworzy wystąpienie elementu członkowskiego usługi w usłudze.|
|/Services/servicemembers/Alerts/Read|Odczytuje alerty dla elementu członkowskiego usługi.|
|/Services/servicemembers/DELETE|Usuwa wystąpienie elementu członkowskiego usługi w usłudze.|
|/Services/servicemembers/Read|Odczytuje element członkowski wystąpienia usługi w usłudze.|
|/ services/zapisu|Tworzy wystąpienie usługi w dzierżawie.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Operacja | Opis |
|---|---|
|/Configurations/Read|Uzyskiwanie konfiguracji|
|/ konfiguracje/zapisu|Tworzy aktualizacji konfiguracji|
|/ generateRecommendations/działania|Generuje zalecenia|
|/generateRecommendations/Read|Pobiera Generowanie zaleceń stanu|
|/Operations/Read|Pobiera operacje dla Advisor firmy Microsoft|
|/recommendations/Read|Odczytuje zalecenia|
|/recommendations/suppressions/DELETE|Usuwa pominięć|
|/recommendations/suppressions/Read|Pobiera pominięć|
|/recommendations/suppressions/Write|Tworzy aktualizacje pominięć|
|/ register/działania|Rejestruje subskrypcję Microsoft Advisor|
|/suppressions/DELETE|Usuwa pominięć|
|/suppressions/Read|Pobiera pominięć|
|/ pominięć/zapisu|Tworzy aktualizacje pominięć|
|/ unregister/działania|Wyrejestrowuje subskrypcję Microsoft Advisor|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Operacja | Opis |
|---|---|
|/alerts/read|Pobierz wszystkie alerty dla filtrów wejściowych.|
|/Alerts/Resolve/Action|Zmień stan alertu na "Usuń"|
|/alertsSummary/Read|Pobierz podsumowanie alertów|
|Operacje/odczytu|Odczytuje działania określone|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Operacja | Opis |
|---|---|
|/locations/checkNameAvailability/action|Sprawdza, czy dany serwer analiz nazwa jest prawidłowa, a nie korzystać.|
|/Locations/operationresults/Read|Pobiera informacje o wyniku określonej operacji.|
|/Locations/operationstatuses/Read|Pobiera informacje o stanie określonej operacji.|
|/Operations/Read|Pobiera informacje o operacji|
|/ register/działania|Rejestruje dostawcę zasobów usług Analysis Services.|
|/servers/delete|Usuwa Analysis Server.|
|/servers/listGatewayStatus/action|Lista stanu skojarzonym z serwerem bramy.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla serwera analiz|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla serwera analiz|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla serwerów|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla serwera analiz|
|/servers/read|Pobiera informacje o określonym Analysis Server.|
|/Servers/resume/Action|Wznawia Analysis Server.|
|/servers/skus/read|Pobierz dostępne jednostki SKU informacje dotyczące serwera|
|/Servers/suspend/Action|Wstrzymuje Analysis Server.|
|/ serwery/zapisu|Tworzy lub aktualizuje określony Analysis Server.|
|/skus/read|Pobiera informacje jednostki SKU|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Operacja | Opis |
|---|---|
|/checkNameAvailability/read|Sprawdza, czy pod warunkiem, że nazwa usługi jest niedostępna|
|/Operations/Read|Dostępne wszystkie operacje interfejsu API dla zasobu Microsoft.ApiManagement odczytu|
|/ register/działania|Zarejestruj subskrypcję dostawcy zasobów Microsoft.ApiManagement|
|/Reports/Read|Pobierz raporty w okresach, regionu geograficznego, deweloperów, produktów, interfejsów API, operacje, subskrypcji i byRequest.|
|/service/apis/delete|Usuń istniejące interfejsu API|
|/Service/APIs/Diagnostics/DELETE|Usuń istniejące diagnostyki|
|/Service/APIs/Diagnostics/loggers/DELETE|Usuń mapowanie rejestratora przy użyciu ustawienia diagnostyczne|
|/service/apis/diagnostics/loggers/read|Pobierz listę istniejących diagnostycznych rejestratorów|
|/service/apis/diagnostics/loggers/write|Mapowanie rejestratora na ustawienie diagnostyczne|
|/Service/APIs/Diagnostics/Read|Pobierz listę diagnostyki lub pobranie szczegółów diagnostyki|
|/Service/APIs/Diagnostics/Write|Dodaj nowe diagnostyki lub zaktualizować istniejący szczegóły diagnostyczne|
|/Service/APIs/Operations/DELETE|Usuń istniejące operacji interfejsu API|
|/Service/APIs/Operations/policies/DELETE|Usuń konfigurację zasad od zasad operacji interfejsu API|
|/Service/APIs/Operations/policies/Read|Pobrania zasad dla operacji interfejsu API lub pobranie szczegółów konfiguracji zasad dla operacji interfejsu API|
|/Service/APIs/Operations/policies/Write|Ustaw szczegóły konfiguracji zasad dla operacji interfejsu API|
|/Service/APIs/Operations/Policy/DELETE|Usuń konfigurację zasad z operacji|
|/Service/APIs/Operations/Policy/Read|Uzyskiwanie szczegółów konfiguracji zasad dla operacji|
|/Service/APIs/Operations/Policy/Write|Ustaw szczegóły konfiguracji zasad dla operacji|
|/Service/APIs/Operations/Read|Pobierz listę istniejących operacji interfejsu API lub uzyskać szczegółowe informacje o operacji interfejsu API|
|/Service/APIs/Operations/Tags/DELETE|Usuń powiązanie istniejącego znacznika z istniejącą operację|
|/Service/APIs/Operations/Tags/Read|Pobierz Znaczniki skojarzone ze szczegółami operacji lub pobrać tagu|
|/Service/APIs/Operations/Tags/Write|Skojarz istniejącego znacznika z istniejącą operację|
|/Service/APIs/Operations/Write|Tworzenie nowej operacji interfejsu API lub zaktualizować istniejący operacji interfejsu API|
|/service/apis/operationsByTags/read|Pobierz listę operacji/Tag skojarzenia|
|/service/apis/policies/delete|Usuń konfigurację zasad od zasad interfejsu API|
|/service/apis/policies/read|Pobierz zasady dla interfejsu API lub Get szczegóły konfiguracji zasad interfejsu API|
|/service/apis/policies/write|Ustaw szczegóły konfiguracji zasad interfejsu API|
|/service/apis/policy/delete|Usuń konfigurację zasad z interfejsu API|
|/Service/APIs/Policy/Read|Uzyskiwanie szczegółów konfiguracji zasad interfejsu API|
|/service/apis/policy/write|Ustaw szczegóły konfiguracji zasad interfejsu API|
|/Service/APIs/Products/Read|Pobierz wszystkie produkty, które interfejs API jest częścią|
|/service/apis/read|Pobierz listę wszystkich zarejestrowanych szczegółów interfejsów API lub Get interfejsu API|
|/service/apis/releases/delete|Usuwa wszystkie wersje wersji interfejsu API lub usuń interfejsu API|
|/service/apis/releases/read|Zainstalowane wersje dla interfejsu API lub Get szczegóły reelase interfejsu API|
|/service/apis/releases/write|Utwórz nową wersję interfejsu API lub zaktualizuj istniejącą wersji interfejsu API|
|/Service/APIs/revisions/DELETE|Usuwa wszystkie wersje interfejsu API|
|/Service/APIs/revisions/Read|Pobierz poprawki należących do interfejsu API|
|/service/apis/schemas/delete|Usuwa istniejące schematu|
|/Service/APIs/schemas/Document/Read|Pobierz dokument zawierający schematu|
|/service/apis/schemas/document/write|Aktualizacja schematu opisujący|
|/service/apis/schemas/read|Pobiera wszystkich schematów dla danego interfejsu API lub pobiera schematów używany przez interfejs API|
|/Service/APIs/schemas/Write|Ustawia schematów używany przez interfejs API|
|/Service/APIs/tagDescriptions/DELETE|Usuń opis tagu z interfejsu API|
|/Service/APIs/tagDescriptions/Read|Pobierz opisy tagów w zakresie opisu interfejsu API lub pobrać tagu w zakresie interfejsu API|
|/Service/APIs/tagDescriptions/Write|Opis Tag utworzyć zmiany w zakresie interfejsu API|
|/Service/APIs/Tags/DELETE|Usuń istniejące skojarzenie interfejsu API/Tag|
|/Service/APIs/Tags/Read|Pobierz wszystkie skojarzenia interfejsu API/tagu dla interfejsu API lub Get szczegółów skojarzenia interfejsu API/Tag|
|/Service/APIs/Tags/Write|Dodaj nowe skojarzenie interfejsu API/Tag|
|/Service/APIs/Write|Utwórz nowy interfejs API lub zaktualizuj istniejącą szczegóły interfejsu API|
|/service/apisByTags/read|Pobierz listę skojarzenia interfejsu API/Tag|
|/service/api-version-sets/delete|Usuń istniejące VersionSet|
|/service/api-version-sets/read|Pobierz listę wersji grupy jednostek lub pobiera szczegóły VersionSet|
|/service/api-version-sets/versions/read|Pobierz listę wersji jednostki|
|/service/api-version-sets/write|Utwórz VersionSet nowego lub istniejącego VersionSet szczegóły aktualizacji|
|/service/applynetworkconfigurationupdates/action|Aktualizuje zasoby Microsoft.ApiManagement systemem w sieci wirtualnej w celu zastosowania zaktualizowanych ustawień sieciowych.|
|/service/authorizationServers/delete|Usuń istniejący serwer autoryzacji|
|/service/authorizationServers/read|Pobierz listę serwerów autoryzacji lub pobrać szczegółów serwera autoryzacji|
|/Service/authorizationServers/Write|Tworzenie nowego serwera autoryzacji lub szczegóły aktualizacji istniejącego serwera autoryzacji|
|/Service/backends/DELETE|Usuń istniejące wewnętrznej bazy danych|
|/Service/backends/Read|Pobierz listę zapleczy lub uzyskać szczegółowe informacje o wewnętrznej bazy danych|
|/Service/backends/reconnect/Action|Utwórz żądanie ponownego nawiązania połączenia|
|/Service/backends/Write|Dodać nowego zaplecza lub zaktualizować istniejący szczegóły wewnętrznej bazy danych|
|/Service/Backup/Action|Usługi zarządzania interfejsu API tworzenia kopii zapasowej do określonego kontenera użytkownika podane konto magazynu|
|/service/certificates/delete|Usuń istniejący certyfikat|
|/service/certificates/read|Pobierz listę certyfikatów lub Pobierz szczegóły certyfikatu|
|/Service/Certificates/Write|Dodaj nowy certyfikat|
|/service/delete|Usuń wystąpienia usługi interfejsu API zarządzania|
|/Service/Diagnostics/DELETE|Usuń istniejące diagnostyki|
|/Service/Diagnostics/loggers/DELETE|Usuń mapowanie rejestratora przy użyciu ustawienia diagnostyczne|
|/service/diagnostics/loggers/read|Pobierz listę istniejących diagnostycznych rejestratorów|
|/service/diagnostics/loggers/write|Mapowanie rejestratora na ustawienie diagnostyczne|
|/Service/Diagnostics/Read|Pobierz listę diagnostyki lub pobranie szczegółów diagnostyki|
|/Service/Diagnostics/Write|Dodaj nowe diagnostyki lub zaktualizować istniejący szczegóły diagnostyczne|
|/service/getssotoken/action|Pobiera token logowania jednokrotnego, który może służyć do logowania do portalu starsza wersja usługi zarządzania interfejsu API z uprawnieniami administratora|
|/service/groups/delete|Usuń istniejącą grupę|
|/service/groups/read|Pobierz listę grup lub pobiera szczegóły grupy|
|/service/groups/users/delete|Usuwanie istniejącego użytkownika z istniejącej grupy|
|/service/groups/users/read|Pobierz listę grupy użytkowników|
|/service/groups/users/write|Dodaj istniejącego użytkownika do istniejącej grupy|
|/service/groups/write|Utwórz nową grupę lub zaktualizować istniejący szczegóły grupy|
|/service/identityProviders/delete|Usuń istniejące dostawcy tożsamości|
|/service/identityProviders/read|Pobierz listę dostawców tożsamości lub Uzyskaj szczegóły dostawcy tożsamości|
|/service/identityProviders/write|Utwórz nowe szczegóły dostawcy tożsamości lub aktualizację istniejącego dostawcy tożsamości|
|/service/locations/networkstatus/read|Pobiera stan dostępu do sieci, zasobów, na których zależy od usługi w lokalizacji.|
|/service/loggers/delete|Usuń istniejące rejestratora|
|/Service/loggers/Read|Pobierz listę rejestratorów lub pobrać szczegółów rejestratora|
|/Service/loggers/Write|Dodać nowego rejestratora lub zaktualizować istniejący szczegóły rejestratora|
|/Service/managedeployments/Action|Zmień jednostki SKU/jednostki, dodawania i usuwania wdrożenia regionalnych interfejsu API usługi zarządzania|
|/service/networkstatus/read|Pobiera stan dostępu do sieci, zasobów, na których zależy od usługi.|
|/Service/Notifications/Action|Wysyła powiadomienie do określonego użytkownika|
|/Service/Notifications/Read|Pobiera wszystkie powiadomienia wydawcy interfejsu API zarządzania lub uzyskać zarządzanie interfejsami API wydawcy szczegóły powiadomienia|
|/service/notifications/recipientEmails/delete|Usuwa istniejące adresu E-mail skojarzonego z powiadomienia|
|/service/notifications/recipientEmails/read|Pobierz adresatów wiadomości E-mail skojarzony z interfejsu API zarządzania wydawcy powiadomień|
|/Service/Notifications/recipientEmails/Write|Utwórz nowy adres E-mail odbiorcy powiadomienia|
|/service/notifications/recipientUsers/delete|Usuwa użytkownika skojarzonego z adresatów powiadomień|
|/service/notifications/recipientUsers/read|Pobierz odbiorcy użytkownicy skojarzeni z tym powiadomienie|
|/service/notifications/recipientUsers/write|Dodaj użytkownika do adresatów powiadomień|
|/Service/Notifications/Write|Tworzenie lub aktualizacja interfejsu API zarządzania wydawcy powiadomień|
|/service/openidConnectProviders/delete|Usuń istniejące OpenID Connect dostawcy|
|/service/openidConnectProviders/read|Pobierz listę dostawców OpenID Connect lub pobranie szczegółów OpenID Connect dostawcy|
|/service/openidConnectProviders/write|Utwórz nowe szczegóły OpenID Connect dostawcy lub aktualizację istniejącego dostawcy uwierzytelniania OpenID Connect|
|/service/operationresults/read|Pobiera bieżący stan długotrwałej operacji|
|/service/policies/delete|Usuń konfigurację zasad od dzierżawcy zasad|
|/service/policies/read|Pobierz zasady dla dzierżawy lub Get szczegóły konfiguracji zasad dla dzierżawcy|
|/service/policies/write|Ustaw szczegóły konfiguracji zasad dla dzierżawcy|
|/service/policySnippets/read|Pobierz wszystkie fragmenty zasad|
|/service/portalsettings/read|Tworzenie konta ustawienia uzyskać portalu lub Get podpisywania w ustawieniach portalu lub pobrać ustawień delegowania dla portalu|
|/service/portalsettings/write|Zaktualizować ustawień Utwórz konto lub ustawienia aktualizacji Utwórz konto lub ustawienia aktualizacji logowania lub ustawienia aktualizacji logowania lub ustawienia delegowania aktualizacji lub ustawienia delegowania aktualizacji|
|/service/products/apis/delete|Usuń istniejące interfejsu API z istniejącego produktu|
|/Service/Products/APIs/Read|Pobierz listę dodane do istniejących interfejsów API|
|/Service/Products/APIs/Write|Dodaj istniejący interfejs API do istniejącego produktu|
|/service/products/delete|Usuń istniejące produkt|
|/service/products/groups/delete|Usuń skojarzenie istniejąca grupa deweloperów z istniejącego produktu|
|/service/products/groups/read|Pobierz listę grup developer skojarzonych z produktu|
|/service/products/groups/write|Skojarz istniejąca grupa deweloperów z istniejącego produktu|
|/service/products/policies/delete|Usuń konfigurację zasad od zasad produktu|
|/Service/Products/policies/Read|Pobierz zasady produktu lub Get zasad szczegółowe informacje dotyczące konfiguracji produktu|
|/service/products/policies/write|Ustaw szczegóły konfiguracji zasad produktu|
|/Service/Products/Policy/DELETE|Usuń zasady konfiguracji z istniejącego produktu|
|/Service/Products/Policy/Read|Uzyskiwanie konfiguracji zasad istniejącego produktu|
|/service/products/policy/write|Ustaw konfigurację zasad dla istniejącego produktu|
|/Service/Products/Read|Pobierz listę produktów lub uzyskać szczegółowe informacje o produkcie|
|/Service/Products/Subscriptions/Read|Pobierz listę subskrypcji produktu|
|/service/products/tags/delete|Usuń powiązanie istniejącego znacznika z istniejącego produktu|
|/Service/Products/Tags/Read|Pobierz Znaczniki skojarzone z szczegóły produktu lub pobrać tagu|
|/service/products/tags/write|Skojarz istniejącego znacznika z istniejącego produktu|
|/service/products/write|Tworzenie nowego produktu lub zaktualizować istniejące informacje szczegółowe|
|/service/properties/delete|Usuwa istniejące właściwości|
|/Service/Properties/Read|Pobiera listę właściwości wszystkich lub pobiera szczegóły określonej właściwości|
|/service/properties/write|Tworzy nową właściwość lub aktualizuje wartość dla określonej właściwości|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla usługi Zarządzanie interfejsami API|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla usługi Zarządzanie interfejsami API|
|/service/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla usługi Zarządzanie interfejsami API|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla usługi Zarządzanie interfejsami API|
|/service/quotas/periods/read|Pobierz wartość licznika przydziału dla okresu|
|/service/quotas/periods/write|Ustaw bieżącą wartość licznika przydziału|
|/service/quotas/read|Pobiera wartości dla limitu przydziału|
|/service/quotas/write|Ustaw bieżącą wartość licznika przydziału|
|/service/read|Odczytać metadanych dla wystąpienia interfejsu API usługi zarządzania|
|/service/reports/read|Pobierz raport w okresach lub raportu Get w regionie geograficznym lub raportu Get agregowana przez deweloperów. lub Załóż raportu w produktów. lub Załóż agregowane przez interfejsy API lub Get raportu w operacji lub Get raportu w subskrypcji raportu. lub pobrać żądań danych raportowania|
|/service/restore/action|Przywracanie usługi interfejsu API zarządzania z kontenera określonej w użytkownika podane konto magazynu|
|/Service/Subscriptions/DELETE|Usuń subskrypcję. Ta operacja może służyć do usuwania subskrypcji|
|/Service/Subscriptions/Read|Pobiera listę subskrypcji produktu lub uzyskać szczegółów subskrypcji produktu|
|/service/subscriptions/regeneratePrimaryKey/action|Ponowne generowanie klucza podstawowego subskrypcji|
|/service/subscriptions/regenerateSecondaryKey/action|Ponowne wygenerowanie klucza pomocniczego subskrypcji|
|/Service/Subscriptions/Write|Subskrypcja istniejącego użytkownika do istniejącego produktu lub zaktualizować istniejący Szczegóły subskrypcji. Ta operacja może służyć do odnowienia subskrypcji|
|/service/tagResources/read|Pobierz listę tagów ze skojarzonymi zasobami|
|/service/tags/delete|Usuń istniejące Tag|
|/service/tags/read|Pobierz listę tagów lub pobrać szczegółów znacznika|
|/service/tags/write|Dodaj nowy znacznik lub zaktualizować istniejący Szczegóły tagu|
|/service/templates/delete|Resetuj domyślny szablon wiadomości e-mail zarządzanie interfejsami API|
|/service/templates/read|Pobiera wszystkie szablony wiadomości e-mail lub wiadomości e-mail pobiera zarządzanie interfejsami API Szczegóły szablonu|
|/service/templates/write|Utwórz lub zaktualizuj zarządzanie interfejsami API szablon wiadomości e-mail lub aktualizuje zarządzanie interfejsami API szablon wiadomości e-mail|
|/Service/tenant/DELETE|Usuń konfigurację zasad dla dzierżawcy|
|/Service/tenant/Deploy/Action|Uruchamia zadanie wdrażania można zastosować zmian z gałęzi git określony w konfiguracji w bazie danych.|
|/service/tenant/operationResults/read|Pobierz listę wyników operacji lub uzyskanie wyniku operacji|
|/Service/tenant/Read|Pobierz konfigurację zasad dla Get lub dzierżawy szczegółowe informacje dotyczące dostępu|
|/service/tenant/regeneratePrimaryKey/action|Ponownie wygenerować podstawowy klucz dostępu|
|/service/tenant/regenerateSecondaryKey/action|Wygeneruj ponownie pomocniczy klucz dostępu|
|/Service/tenant/Save/Action|Tworzy zatwierdzenia z konfiguracji migawek dla określonej gałęzi w repozytorium|
|/service/tenant/syncState/read|Pobierz stan ostatniej synchronizacji git|
|/service/tenant/validate/action|Weryfikuje zmian z gałęzi git określony|
|/service/tenant/write|Ustaw konfigurację zasad dla dzierżawy lub szczegółowe informacje dotyczące aktualizacji dzierżawy dostępu|
|/service/updatecertificate/action|Przekaż certyfikat protokołu SSL dla interfejsu API usługi zarządzania|
|/service/updatehostname/action|Konfiguracja, zaktualizować lub usunąć nazwy domeny niestandardowej dla usługi interfejsu API zarządzania|
|/Service/Users/Action|Zarejestruj nowy użytkownik|
|/Service/Users/Applications/Attachments/DELETE|Usuwa załącznika|
|/service/users/applications/attachments/read|Pobiera załączniki aplikacji lub pobiera załącznika|
|/Service/Users/Applications/Attachments/Write|Dodaj załącznik do aplikacji|
|/Service/Users/Applications/DELETE|Usuwa istniejące aplikacji|
|/Service/Users/Applications/Read|Pobierz listę wszystkich aplikacji użytkownika lub szczegóły aplikacji pobiera zarządzanie interfejsami API|
|/service/users/applications/write|Rejestruje aplikację interfejsu API zarządzania lub aktualizacje szczegóły aplikacji|
|/service/users/delete|Usuń konto użytkownika|
|/service/users/generateSsoUrl/action|Generowania adresu URL logowania jednokrotnego. Adres URL można uzyskać dostęp do portalu administratora|
|/service/users/groups/read|Pobierz listę grup użytkowników|
|/service/users/keys/read|Pobierz listę kluczy użytkownika|
|/service/users/read|Pobierz listę zarejestrowanych użytkowników lub pobrać szczegółów konta użytkownika|
|/service/users/subscriptions/read|Pobierz listę subskrypcji użytkownika|
|/service/users/token/action|Uzyskaj token dostępu token dla użytkownika|
|/service/users/write|Zarejestrować nowego użytkownika lub szczegóły konta aktualizacji istniejącego użytkownika|
|/ service/zapisu|Utwórz nowe wystąpienie interfejsu API usługi zarządzania|
|/ unregister/działania|Wyrejestrowanie subskrypcji dla dostawcy zasobów Microsoft.ApiManagement|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Operacja | Opis |
|---|---|
|/checkAccess/action|Sprawdza, czy element wywołujący ma autoryzację do wykonania określonej akcji|
|/classicAdministrators/delete|Usuwa administratora z subskrypcji.|
|/classicAdministrators/read|Odczytuje administratorów dla subskrypcji.|
|/classicAdministrators/write|Dodaje lub modyfikuje administratora dla subskrypcji.|
|/elevateAccess/action|Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy|
|/Locks/DELETE|Usuwa blokady w podanym zakresie.|
|/Locks/Read|Pobiera blokady w podanym zakresie.|
|/ blokady zapisu|Dodaje blokady w podanym zakresie.|
|/permissions/Read|Wyświetla wszystkie uprawnienia obiektu wywołującego w danym zakresie.|
|/policyAssignments/delete|Usuń przypisanie zasad w podanym zakresie.|
|/policyAssignments/read|Pobierz informacje o przypisaniu zasad.|
|/policyAssignments/write|Utwórz przypisanie zasad w podanym zakresie.|
|/policyDefinitions/delete|Usuń definicję zasad.|
|/policyDefinitions/read|Pobierz informacje o definicji zasad.|
|/policyDefinitions/write|Utwórz niestandardową definicję zasad.|
|/policySetDefinitions/delete|Usuń definicję zestawu zasad.|
|/policySetDefinitions/read|Pobierz informacje o definicji zestawu zasad.|
|/policySetDefinitions/write|Utwórz niestandardową definicję zestawu zasad.|
|/providerOperations/Read|Pobiera operacje dla wszystkich dostawców zasobów do użycia w definicjach ról.|
|/roleAssignments/delete|Usuwa przypisanie roli w podanym zakresie.|
|/roleAssignments/read|Pobiera informacje o przypisaniu roli.|
|/roleAssignments/write|Tworzy przypisanie roli w podanym zakresie.|
|/roleDefinitions/delete|Usuwa określoną niestandardową definicję roli.|
|/roleDefinitions/read|Pobiera informacje o definicji roli.|
|/roleDefinitions/write|Tworzy lub aktualizuje niestandardową definicję roli z określonymi uprawnieniami i zakresami możliwymi do przypisania.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Operacja | Opis |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Przeczytaj informacje rejestracyjne DSC automatyzacji Azure|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Zapisuje żądanie, aby ponownie wygenerować klucze Konfiguracja DSC automatyzacji Azure|
|/automationAccounts/certificates/delete|Usuwa zasób certyfikatu usługi Automatyzacja Azure|
|/automationAccounts/certificates/read|Pobiera zasób certyfikatu usługi Automatyzacja Azure|
|/automationAccounts/certificates/write|Tworzy lub aktualizuje zasób certyfikatu usługi Automatyzacja Azure|
|/automationAccounts/compilationjobs/read|Odczytuje kompilacji DSC usługi Automatyzacja Azure|
|/automationAccounts/compilationjobs/write|Zapisuje kompilacji DSC usługi Automatyzacja Azure|
|/automationAccounts/configurations/delete|Usuwa zawartość DSC automatyzacji Azure|
|/automationAccounts/configurations/getCount/action|Odczytuje liczba zawartości DSC automatyzacji Azure|
|/automationAccounts/configurations/read|Pobiera zawartość DSC automatyzacji Azure|
|/automationAccounts/configurations/write|Zapisuje zawartość DSC automatyzacji Azure|
|/automationAccounts/connections/delete|Usuwa zasób połączenia usługi Automatyzacja Azure|
|/automationAccounts/Connections/Read|Pobiera zasób połączenia usługi Automatyzacja Azure|
|/automationAccounts/connections/write|Tworzy lub aktualizuje zasób połączenia usługi Automatyzacja Azure|
|/automationAccounts/connectionTypes/delete|Usuwa zasób typu połączenia usługi Automatyzacja Azure|
|/automationAccounts/connectionTypes/read|Pobiera zasób typu połączenia usługi Automatyzacja Azure|
|/automationAccounts/connectionTypes/write|Tworzy zasób typu połączenia usługi Automatyzacja Azure|
|/automationAccounts/credentials/delete|Usuwa zasób poświadczenia usługi Automatyzacja Azure|
|/automationAccounts/credentials/read|Pobiera zasób poświadczenia usługi Automatyzacja Azure|
|/automationAccounts/credentials/write|Tworzy lub aktualizuje zasób poświadczenia usługi Automatyzacja Azure|
|/automationAccounts/delete|Usuwa konto usługi Automatyzacja Azure|
|/automationAccounts/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/automationAccounts/diagnosticSettings/write|Konfiguruje ustawienie diagnostyczne dla zasobu|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Usuwa zasoby hybrydowego procesu roboczego elementu Runbook|
|/automationAccounts/hybridRunbookWorkerGroups/read|Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook|
|/automationAccounts/Jobs/Output/Action|Pobiera dane wyjściowe zadania|
|/automationAccounts/Jobs/Output/Action|Pobiera dane wyjściowe zadania|
|/automationAccounts/jobs/read|Pobiera zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/read|Pobiera zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/resume/action|Wznawia zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/resume/action|Wznawia zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/runbookContent/action|Pobiera zawartość elementu runbook usługi Automatyzacja Azure w czasie wykonywania zadania|
|/automationAccounts/jobs/runbookContent/action|Pobiera zawartość elementu runbook usługi Automatyzacja Azure w czasie wykonywania zadania|
|/automationAccounts/Jobs/Stop/Action|Zatrzymuje zadanie usługi Automatyzacja Azure|
|/automationAccounts/Jobs/Stop/Action|Zatrzymuje zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/streams/read|Pobiera strumień zadań usługi Automatyzacja Azure|
|/automationAccounts/jobs/streams/read|Pobiera strumień zadań usługi Automatyzacja Azure|
|/automationAccounts/jobs/suspend/action|Wstrzymuje zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/suspend/action|Wstrzymuje zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/write|Tworzy zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobs/write|Tworzy zadanie usługi Automatyzacja Azure|
|/automationAccounts/jobSchedules/delete|Usuwa harmonogram zadań usługi Automatyzacja Azure|
|/automationAccounts/jobSchedules/read|Pobiera harmonogram zadań usługi Automatyzacja Azure|
|/automationAccounts/jobSchedules/write|Tworzy harmonogram zadań usługi Automatyzacja Azure|
|/automationAccounts/linkedWorkspace/read|Pobiera obszar roboczy połączony z kontem automatyzacji|
|/automationAccounts/logDefinitions/read|Pobiera dostępne dzienniki dla konta automatyzacji|
|/automationAccounts/modules/activities/read|Pobiera działania usługi Automatyzacja Azure|
|/automationAccounts/modules/delete|Usuwa moduł usługi Automatyzacja Azure|
|/automationAccounts/modules/read|Pobiera moduł usługi Automatyzacja Azure|
|/automationAccounts/modules/write|Tworzy lub aktualizuje moduł usługi Automatyzacja Azure|
|/automationAccounts/nodeConfigurations/delete|Usuwa konfiguracji węzła DSC automatyzacji Azure|
|/automationAccounts/nodeConfigurations/read|Odczytuje konfiguracji węzła DSC automatyzacji Azure|
|/automationAccounts/nodeConfigurations/readContent/action|Odczytuje zawartość konfiguracji węzła DSC automatyzacji Azure|
|/automationAccounts/nodeConfigurations/write|Zapisuje konfiguracji węzła DSC automatyzacji Azure|
|/automationAccounts/nodes/delete|Usuwa węzłów DSC automatyzacji Azure|
|/automationAccounts/nodes/read|Odczytuje węzłów DSC automatyzacji Azure|
|/automationAccounts/nodes/reports/read|Odczytuje contentss raportu konfiguracji DSC automatyzacji Azure|
|/automationAccounts/nodes/reports/read|Odczytuje raporty konfiguracji DSC automatyzacji Azure|
|/automationAccounts/objectDataTypes/fields/read|Pobiera TypeFields usługi Automatyzacja Azure|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Pobiera definicje metryk automatyzacji|
|/automationAccounts/read|Pobiera konto usługi Automatyzacja Azure|
|/automationAccounts/runbooks/delete|Usuwa element runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/publish/action|Publikuje wersję roboczą elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/read|Pobiera wersję roboczą elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/readContent/action|Pobiera zawartość wersji roboczej elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/testJob/read|Pobiera zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/testJob/resume/action|Wznawia zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/testJob/stop/action|Zatrzymuje zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Wstrzymuje zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/testJob/write|Tworzy zadanie testowe wersji roboczej elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/undoEdit/action|Cofnij operacje edycji próbnej wersji elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/draft/writeContent/action|Tworzy zawartość wersji roboczej elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/read|Pobiera element runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/readContent/action|Pobiera zawartość elementu runbook usługi Automatyzacja Azure|
|/automationAccounts/runbooks/write|Tworzy lub aktualizuje element runbook usługi Automatyzacja Azure|
|/automationAccounts/schedules/delete|Usuwa zasób harmonogramu usługi Automatyzacja Azure|
|/automationAccounts/schedules/read|Pobiera zasób harmonogramu usługi Automatyzacja Azure|
|/automationAccounts/schedules/write|Tworzy lub aktualizuje zasób harmonogramu usługi Automatyzacja Azure|
|/automationAccounts/statistics/read|Pobiera statystyki usługi Automatyzacja Azure|
|/automationAccounts/usages/read|Pobiera użycia usługi Automatyzacja Azure|
|/automationAccounts/variables/delete|Usuwa zasób zmiennej usługi Automatyzacja Azure|
|/automationAccounts/variables/read|Odczytuje zasób zmiennej usługi Automatyzacja Azure|
|/automationAccounts/variables/write|Tworzy lub aktualizuje zasób zmiennej usługi Automatyzacja Azure|
|/automationAccounts/watchers/streams/read|Pobiera strumień zadań usługi Automatyzacja Azure obserwatora|
|/automationAccounts/webhooks/delete|Usuwa element webhook usługi Automatyzacja Azure |
|/automationAccounts/webhooks/generateUri/action|Generuje identyfikator URI dla elementu webhook usługi Automatyzacja Azure|
|/automationAccounts/webhooks/read|Odczytuje element webhook usługi Automatyzacja Azure|
|/automationAccounts/webhooks/write|Tworzy lub aktualizuje element webhook usługi Automatyzacja Azure|
|/automationAccounts/write|Tworzy lub aktualizuje konto usługi Automatyzacja Azure|
|/automationAccounts/write|Tworzy lub aktualizuje konto usługi Automatyzacja Azure|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Operacja | Opis |
|---|---|
|/b2cDirectories/DELETE|Usuń zasób katalogu B2C|
|/b2cDirectories/Read|Wyświetl zasób katalogu B2C|
|/ b2cDirectories/zapisu|Utwórz lub zaktualizuj zasób katalogu B2C|
|/Operations/Read|Odczytuj wszystkie operacje interfejsu API dostępne dla dostawcy zasobów Microsoft.AzureActiveDirectory|
|/ register/działania|Rejestruj subskrypcję dostawcy zasobów Microsoft.AzureActiveDirectory|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Operacja | Opis |
|---|---|
|Operacje/odczytu|Pobiera właściwości operacji dostawcy zasobów|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów Microsoft.AzureStack|
|/Registrations/customerSubscriptions/DELETE|Usuwa subskrypcji platformy Azure stosu klienta|
|/Registrations/customerSubscriptions/Read|Pobiera właściwości subskrypcji platformy Azure stosu klienta|
|/Registrations/customerSubscriptions/Write|Tworzy lub aktualizuje subskrypcji platformy Azure stosu klienta|
|/Registrations/DELETE|Usuwa rejestrację Azure stosu|
|/registrations/getActivationKey/action|Pobiera najnowsze klucza aktywacji Azure stosu|
|/registrations/products/listDetails/action|Pobiera rozszerzony szczegóły produktu Azure Marketplace stosu|
|/Registrations/Products/Read|Pobiera właściwości produktu Azure Marketplace stosu|
|/Registrations/Read|Pobiera właściwości rejestracji Azure stosu|
|/ rejestracji/zapisu|Tworzy lub aktualizuje rejestracji Azure stosu|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Operacja | Opis |
|---|---|
|/batchAccounts/applications/delete|Usuwa aplikacji|
|/batchAccounts/Applications/Read|Wyświetla listę aplikacji lub pobiera właściwości aplikacji|
|/batchAccounts/applications/versions/activate/action|Aktywuje pakietu aplikacji|
|/batchAccounts/applications/versions/delete|Usuwa pakiet aplikacji|
|/batchAccounts/applications/versions/read|Pobiera właściwości pakietu aplikacji|
|/batchAccounts/applications/versions/write|Tworzy nowy pakiet aplikacji lub aktualizuje istniejący pakiet aplikacji|
|/batchAccounts/applications/write|Tworzy nową aplikację lub aktualizuje istniejącą aplikację|
|/batchAccounts/certificateOperationResults/read|Pobiera wyniki długotrwałej operacji certyfikatu na konto usługi partia zadań|
|/batchAccounts/certificates/cancelDelete/action|Anuluje nie powiodło się usunięcie certyfikatu na konto usługi partia zadań|
|/batchAccounts/certificates/delete|Usuwa certyfikat z konta usługi partia zadań|
|/batchAccounts/certificates/read|Wyświetla listę certyfikatów na konto usługi partia zadań lub pobiera właściwości certyfikatu|
|/batchAccounts/certificates/write|Tworzy nowy certyfikat na konto usługi partia zadań lub aktualizuje istniejący certyfikat|
|/batchAccounts/delete|Usuwa konto usługi partia zadań|
|/batchAccounts/listkeys/action|Wyświetla uzyskać dostęp do kluczy dla konta usługi partia zadań|
|/batchAccounts/operationResults/read|Pobiera wyniki długotrwałej operacji konto usługi partia zadań|
|/batchAccounts/poolOperationResults/read|Pobiera wyniki długotrwałej operacji puli na konto usługi partia zadań|
|/batchAccounts/pools/delete|Usuwa pulę z konta usługi partia zadań|
|/batchAccounts/pools/disableAutoscale/action|Powoduje wyłączenie automatycznego skalowania puli konta usługi partia zadań|
|/batchAccounts/pools/Read|Wyświetla listę pul na konto usługi partia zadań lub pobiera właściwości puli|
|/batchAccounts/pools/stopResize/action|Zatrzymuje jako bieżące zmiany rozmiaru operacji w puli konta usługi partia zadań|
|/batchAccounts/pools/upgradeOs/action|Uaktualnia system operacyjny puli konta usługi partia zadań|
|/batchAccounts/pools/write|Tworzy nową pulę na konto usługi partia zadań lub aktualizuje istniejącą pulę|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla usługi partia zadań|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla usługi partia zadań|
|/batchAccounts/read|Lista kont usługi partia zadań lub pobiera właściwości konta usługi partia zadań|
|/batchAccounts/regeneratekeys/action|Odtwarza uzyskać dostęp do kluczy dla konta usługi partia zadań|
|/batchAccounts/syncAutoStorageKeys/action|Synchronizuje klucze dostępu dla konta magazynu automatycznie skonfigurowany dla konta usługi partia zadań|
|/batchAccounts/write|Tworzy nowe konto wsadowe lub aktualizuje istniejące konto usługi partia zadań|
|/locations/checkNameAvailability/action|Sprawdza, czy nazwa konta jest nieprawidłowa i nie w użyciu.|
|/Locations/Quotas/Read|Pobiera limity przydziału partii określonej subskrypcji w wybranym regionie Azure|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów partii i umożliwia tworzenie konta usługi partia zadań|
|/ unregister/działania|Wyrejestrowuje subskrypcji dla dostawcy zasobów partii uniemożliwia tworzenie kont usługi partia zadań|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Operacja | Opis |
|---|---|
|/Clusters/DELETE|Usuwa klastra partii AI|
|/Clusters/Read|Wyświetla listę klastrów AI partii lub pobiera właściwości klastra partii AI|
|/clusters/remoteLoginInformation/action|Wyświetla informacje logowania zdalnego dla klastra partii AI|
|/ klastrów/zapisu|Tworzy nowy klaster AI partii lub aktualizuje istniejący klaster AI partii|
|/fileservers/delete|Usuwa plików wsadowych AI|
|/fileservers/read|Wyświetla listę fileservers AI partii lub pobiera właściwości plików wsadowych AI|
|/fileservers/resume/Action|Wznawia plików wsadowych AI|
|/fileservers/suspend/Action|Wstrzymuje plików wsadowych AI|
|/fileservers/write|Tworzy nowy plików AI partii lub aktualizuje istniejące plików AI partii|
|/jobs/delete|Usuwa zadanie wsadowe AI|
|/jobs/read|Zawiera listę zadań wsadowych AI lub pobiera właściwości zadania wsadowego AI|
|/jobs/remoteLoginInformation/action|Wyświetla informacje logowania zdalnego zadania wsadowego AI|
|/Jobs/terminate/Action|Kończy zadanie wsadowe AI|
|/ zadania/zapisu|Tworzy nowe zadanie wsadowe AI lub aktualizuje istniejące zadanie wsadowe AI|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów AI partii i umożliwia tworzenie zasobów partii AI|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Operacja | Opis |
|---|---|
|/billingPeriods/Read|Wyświetla listę dostępnych okresów rozliczeń|
|/invoices/read|Wyświetla dostępne faktury|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Operacja | Opis |
|---|---|
|/mapApis/Delete|Operacja usuwania|
|/mapApis/listSecrets/action|Wyświetl klucze tajne|
|/mapApis/listSingleSignOnToken/action|Odczyt tokenu autoryzacji logowania jednokrotnego dla zasobu|
|/mapApis/Read|Operacja odczytu|
|/mapApis/regenerateKey/action|Generuje ponownie klucz|
|/mapApis/Write|Operacja zapisu|
|Operacje/odczytu|Opis operacji.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Operacja | Opis |
|---|---|
|/ checknameavailability/działania|Sprawdza, czy nazwa jest dostępna do użycia z nową pamięcią podręczną Redis Cache|
|/Operations/Read|Zawiera listę operacji obsługiwanych przez dostawcę "Microsoft.Cache".|
|/redis/delete|Usuń całą pamięć podręczną Redis|
|/redis/export/action|Wyeksportuj dane usługi Redis do określonych prefiksem obiektów blob magazynu w podanym formacie|
|/redis/firewallRules/delete|Usuń reguły zapory IP pamięci podręcznej Redis Cache|
|/redis/firewallRules/read|Pobierz reguły zapory IP pamięci podręcznej Redis Cache|
|/redis/firewallRules/write|Edytuj reguły zapory IP pamięci podręcznej Redis Cache|
|/redis/forceReboot/action|Wymuś ponowny rozruch wystąpienia pamięci podręcznej, nawet z utratą danych.|
|/redis/import/action|Zaimportuj dane w podanym formacie z wielu obiektów blob do usługi Redis|
|/redis/linkedservers/delete|Usuń podłączony serwer z usługi Redis Cache|
|/redis/linkedservers/read|Pobierz połączone serwery skojarzona z usługą Redis Cache.|
|/redis/linkedservers/write|Dodaj połączony serwer do usługi Redis Cache|
|/redis/listKeys/action|Wyświetl wartości kluczy dostępu pamięci podręcznej Redis w portalu zarządzania|
|/redis/listUpgradeNotifications/read|Lista najnowszych powiadomień o uaktualnieniach dla dzierżawy pamięci podręcznej.|
|/redis/locations/operationresults/read|Pobiera wynik długi uruchomiona operacja, dla którego nagłówka "Location" wcześniej została zwrócona do klienta|
|/redis/metricDefinitions/read|Pobiera dostępne metryki pamięci podręcznej Redis|
|/redis/patchSchedules/delete|Usuń harmonogram stosowania poprawek pamięci podręcznej Redis Cache|
|/redis/patchSchedules/read|Pobiera harmonogram stosowania poprawek pamięci podręcznej Redis Cache|
|/redis/patchSchedules/write|Modyfikuj harmonogram stosowania poprawek pamięci podręcznej Redis Cache|
|/redis/read|Wyświetl ustawienia i konfigurację pamięci podręcznej Redis w portalu zarządzania|
|/redis/regenerateKey/Action|Zmień wartość kluczy dostępu pamięci podręcznej Redis w portalu zarządzania|
|/redis/Start/Action|Uruchom wystąpienie pamięci podręcznej.|
|/redis/Stop/Action|Zatrzymaj wystąpienie pamięci podręcznej.|
|/ redis/zapisu|Zmodyfikuj ustawienia i konfigurację pamięci podręcznej Redis w portalu zarządzania|
|/ register/działania|Rejestruje dostawcę zasobów „Microsoft.Cache” z subskrypcją|
|/ unregister/działania|Wyrejestrowuje dostawcę zasobów „Microsoft.Cache” z subskrypcją|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Operacja | Opis |
|---|---|
|/ register/działania|Rejestruje dostawcę zasobów pojemności i umożliwia tworzenie pojemności zasobów.|
|/ reservationorders/działania|Zaktualizuj zastrzeżenie|
|/reservationorders/DELETE|Usuń zastrzeżenie|
|/reservationorders/Read|Wszystkie rezerwacje do odczytu|
|/reservationorders/reservations/Action|Zaktualizuj zastrzeżenie|
|/reservationorders/reservations/DELETE|Usuń zastrzeżenie|
|/reservationorders/reservations/Read|Wszystkie rezerwacje do odczytu|
|/reservationorders/reservations/revisions/Read|Wszystkie rezerwacje do odczytu|
|/reservationorders/reservations/Write|Utwórz zastrzeżenie|
|/ reservationorders/zapisu|Utwórz zastrzeżenie|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Operacja | Opis |
|---|---|
|/CheckNameAvailability/action||
|/ CheckResourceUsage/działania||
|/edgenodes/delete||
|/edgenodes/read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/Purge/action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/ operationresults/zapisu||
|/Operations/Read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/Profiles/Endpoints/DELETE||
|/Profiles/Endpoints/Load/Action||
|/profiles/endpoints/origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyki dla zasobu|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki dla zasobu|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla Microsoft.Cdn|
|/profiles/endpoints/Purge/action||
|/profiles/endpoints/read||
|/Profiles/Endpoints/Start/Action||
|/profiles/endpoints/Stop/action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/profiles/endpoints/write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/Profiles/Read||
|/profiles/write||
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów CDN i umożliwia tworzenie profilów CDN.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Operacja | Opis |
|---|---|
|/certificateOrders/certificates/Delete|Usuń istniejący certyfikat|
|/certificateOrders/certificates/Read|Pobierz listę certyfikatów|
|/certificateOrders/certificates/Write|Dodaj nowy certyfikat lub zaktualizuj istniejącą|
|/certificateOrders/Delete|Usuń istniejące AppServiceCertificate|
|/certificateOrders/operations/Read|Wyświetl listę wszystkich działań z rejestracji certyfikatu usługi aplikacji|
|/certificateOrders/Read|Pobierz listę CertificateOrders|
|/certificateOrders/reissue/Action|Wydaj ponownie istniejących certificateorder|
|/certificateOrders/renew/Action|Odnów istniejący certificateorder|
|/certificateOrders/resendEmail/Action|Ponowne wysyłanie wiadomości e-mail certyfikatu|
|/certificateOrders/resendRequestEmails/Action|Ponowne wysyłanie wiadomości e-mail żądania do innego adresu e-mail|
|/certificateOrders/resendRequestEmails/Action|Pobrać zamknięcia lokacji w celu wystawiony certyfikat usługi aplikacji|
|/certificateOrders/retrieveCertificateActions/Action|Pobierz listę akcji certyfikatu|
|/certificateOrders/retrieveEmailHistory/Action|Pobieranie certyfikatu historii wiadomości e-mail|
|/certificateOrders/verifyDomainOwnership/Action|Weryfikowanie własności domeny|
|/certificateOrders/Write|Dodaj nowy certificateOrder lub zaktualizuj istniejącą|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Zainicjuj obsługę nazwy głównej usługi dla nazwy głównej usługi aplikacji|
|/ register/działania|Rejestrowanie dostawcy zasobów Certificates firmy Microsoft dla subskrypcji|
|/validateCertificateRegistrationInformation/Action|Sprawdzanie poprawności obiektu zakupu certyfikatu bez przesyłania|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Operacja | Opis |
|---|---|
|/Capabilities/Read|Pokazuje możliwości|
|/checkDomainNameAvailability/action|Sprawdza dostępność danej nazwy domeny.|
|/domainNames/active/write|Ustawia nazwę aktywnej domeny.|
|/domainNames/availabilitySets/read|Pokazuje zestaw dostępności dla zasobu.|
|/domainNames/Capabilities/Read|Pokazuje możliwości nazwy domeny|
|/domainNames/delete|Usuwa nazwy domen dla zasobów.|
|/domainNames/extensions/delete|Usuwa rozszerzenia nazwy domeny.|
|/domainNames/extensions/operationStatuses/read|Odczytuje stan operacji dla rozszerzeń nazw domen.|
|/domainNames/extensions/read|Zwraca rozszerzenia nazwy domeny.|
|/domainNames/extensions/write|Dodaje rozszerzenia nazwy domeny.|
|/domainNames/internalLoadBalancers/delete|Usuwa nową wewnętrzną usługę równoważenia obciążenia.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Odczytuje stan operacji dla wewnętrznych modułów równoważenia obciążenia nazw domen.|
|/domainNames/internalLoadBalancers/read|Pobiera wewnętrzne usługi równoważenia obciążenia.|
|/domainNames/internalLoadBalancers/write|Tworzy nową wewnętrzną usługę równoważenia obciążenia.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Odczytuje stan operacji dla zestawów punktów końcowych z równoważeniem obciążenia nazw domen.|
|/domainNames/loadBalancedEndpointSets/read|Pokazuje zestawy punktów końcowych ze zrównoważonym obciążeniem|
|/domainNames/read|Zwraca nazwy domen dla zasobów.|
|/domainNames/serviceCertificates/delete|Usuwa używane certyfikaty usługi.|
|/domainNames/serviceCertificates/operationStatuses/read|Odczytuje stan operacji dla certyfikatów usługi nazw domen.|
|/domainNames/serviceCertificates/read|Zwraca używane certyfikaty usługi.|
|/domainNames/serviceCertificates/write|Dodaje lub modyfikuje używane certyfikaty usługi.|
|/domainNames/slots/delete|Usuwa dane miejsce wdrożenia.|
|/domainNames/slots/operationStatuses/read|Odczytuje stan operacji dla gniazd nazw domen.|
|/domainNames/slots/read|Pokazuje miejsca wdrożenia.|
|/domainNames/slots/roles/extensionReferences/delete|Usuwa odwołanie do rozszerzenia dla roli miejsca wdrożenia.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Odczytuje stan operacji dla odwołań rozszerzenia ról w ramach gniazd nazw domen.|
|/domainNames/slots/roles/extensionReferences/read|Zwraca odwołanie do rozszerzenia dla roli miejsca wdrożenia.|
|/domainNames/slots/roles/extensionReferences/write|Dodaje lub modyfikuje odwołanie do rozszerzenia dla roli miejsca wdrożenia.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Dodaje lub modyfikuje ustawienia diagnostyczne.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Pobiera definicje metryk.|
|/domainNames/slots/roles/read|Pobiera rolę dla miejsca wdrożenia.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Odczytuje stan operacji dla wystąpień roli w ramach ról gniazd nazw domen.|
|/domainNames/slots/roles/roleInstances/read|Pobiera wystąpienie roli.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Ponownie kompiluje wystąpienie roli.|
|/domainNames/slots/roles/roleInstances/reimage/action|Odtwarza z obrazu wystąpienie roli.|
|/domainNames/slots/roles/roleInstances/restart/action|Uruchamia ponownie wystąpienia roli.|
|/domainNames/slots/start/action|Uruchamia miejsce wdrożenia.|
|/domainNames/slots/state/start/write|Zmienia stan miejsca wdrożenia na Zatrzymane.|
|/domainNames/slots/state/stop/write|Zmienia stan miejsca wdrożenia na Uruchomione.|
|/domainNames/slots/stop/action|Wstrzymuje miejsce wdrożenia.|
|/domainNames/slots/upgradeDomain/write|Uaktualnia domenę (z przeszukaniem).|
|/domainNames/slots/write|Tworzy lub aktualizuje wdrożenie.|
|/domainNames/swap/action|Zamienia miejsce przejściowe z miejscem produkcyjnym.|
|/domainNames/write|Dodaje lub modyfikuje nazwy domen dla zasobów.|
|/moveSubscriptionResources/action|Przenieś wszystkie zasoby klasyczne do innej subskrypcji.|
|/operatingSystemFamilies/read|Generuje listę rodzin systemów operacyjnych gościa dostępnych na platformie Microsoft Azure, a dodatkowo pokazuje wersje systemu operacyjnego dostępne w ramach każdej rodziny.|
|/operatingSystems/read|Generuje listę wersji systemów operacyjnych gościa dostępnych obecnie na platformie Microsoft Azure.|
|/quotas/read|Pobiera przydział dla subskrypcji.|
|/ register/działania|Zarejestruj się w usłudze Classic Compute|
|/resourceTypes/skus/read|Pobiera listę jednostek Sku dla obsługiwanych typów zasobów.|
|/validateSubscriptionMoveAvailability/action|Weryfikuj dostępność subskrypcji dla klasycznej operacji przenoszenia.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Usuwa sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszyną wirtualną.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Pobiera sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Dodaje sieciową grupę zabezpieczeń skojarzoną z maszyną wirtualną.|
|/virtualMachines/asyncOperations/read|Pobiera dostępne operacje asynchroniczne|
|/virtualMachines/attachDisk/action|Dołącza dysk z danymi do maszyny wirtualnej.|
|/virtualMachines/delete|Usuwa maszyny wirtualne.|
|/virtualMachines/detachDisk/action|Odłącza dysk z danymi od maszyny wirtualnej.|
|/virtualMachines/disks/read|Pobiera listę dysków z danymi|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Pobiera plik RDP dla maszyny wirtualnej.|
|/virtualMachines/extensions/operationStatuses/read|Odczytuje stan operacji dla rozszerzeń maszyny wirtualnej.|
|/virtualMachines/extensions/read|Pobiera rozszerzenie maszyny wirtualnej.|
|/virtualMachines/extensions/write|Umieszcza rozszerzenie maszyny wirtualnej.|
|/virtualMachines/metrics/read|Pobiera metryki.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Usuwa sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/ operationStatuses/read|Odczytuje stan operacji dla grup zabezpieczeń sieci skojarzonych z maszyną wirtualną.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Pobiera sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Dodaje sieciową grupę zabezpieczeń skojarzoną z interfejsem sieciowym.|
|/virtualMachines/operationStatuses/read|Odczytuje stan operacji dla maszyn wirtualnych.|
|/virtualMachines/performMaintenance/action|Wykonuje konserwację maszyny wirtualnej.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Dodaje lub modyfikuje ustawienia diagnostyczne.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Pobiera definicje metryk.|
|/virtualMachines/read|Pobiera listę maszyn wirtualnych.|
|/virtualMachines/redeploy/action|Ponownie wdraża maszynę wirtualną.|
|/virtualMachines/restart/action|Uruchamia ponownie maszyny wirtualne.|
|/virtualMachines/shutdown/action|Zamyka maszynę wirtualną.|
|/virtualMachines/Start/Action|Uruchamia maszynę wirtualną.|
|/virtualMachines/stop/action|Zatrzymuje maszynę wirtualną.|
|/virtualMachines/write|Dodaje lub modyfikuje maszyny wirtualne.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Operacja | Opis |
|---|---|
|/gatewaySupportedDevices/read|Pobiera listę obsługiwanych urządzeń.|
|/networkSecurityGroups/delete|Usuwa sieciową grupę zabezpieczeń.|
|/networkSecurityGroups/operationStatuses/read|Odczytuje stan operacji dla grupy zabezpieczeń sieci.|
|/networkSecurityGroups/read|Pobiera sieciową grupę zabezpieczeń.|
|/networkSecurityGroups/securityRules/delete|Usuwa regułę zabezpieczeń.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Odczytuje stan operacji dla reguł zabezpieczeń grupy zabezpieczeń sieci.|
|/networkSecurityGroups/securityRules/read|Pobiera regułę zabezpieczeń.|
|/networkSecurityGroups/securityRules/write|Dodaje lub aktualizuje regułę zabezpieczeń.|
|/networkSecurityGroups/write|Dodaje nową sieciową grupę zabezpieczeń.|
|/quotas/read|Pobiera przydział dla subskrypcji.|
|/ register/działania|Zarejestruj się w usłudze Classic Network|
|/reservedIps/delete|Usuwa zastrzeżony adres IP.|
|/reservedIps/join/action|Dołącz zarezerwowany adres IP|
|/reservedIps/link/action|Połącz zarezerwowany adres IP|
|/reservedIps/operationStatuses/read|Odczytuje stan operacji dla zarezerwowanych adresów IP.|
|/reservedIps/read|Pobiera zastrzeżone adresy IP|
|/reservedIps/write|Dodaje nowy zastrzeżony adres IP|
|/virtualNetworks/capabilities/read|Pokazuje możliwości|
|/virtualNetworks/checkIPAddressAvailability/action|Sprawdza dostępność danego adresu IP w sieci wirtualnej.|
|/virtualNetworks/delete|Usuwa sieć wirtualną.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Odwołuje cofnięcie certyfikatu klienta.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Odczytuje cofnięte certyfikaty klienta.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Cofa certyfikat klienta.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Usuwa certyfikat klienta bramy sieci wirtualnej.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Pobiera certyfikat na podstawie odcisku palca.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Wyświetla pakiet certyfikatu bramy sieci wirtualnej.|
|/virtualNetworks/gateways/clientRootCertificates/read|Znajduje certyfikaty główne klienta.|
|/virtualNetworks/gateways/clientRootCertificates/write|Przekazuje nowy certyfikat główny klienta.|
|/virtualNetworks/gateways/connections/connect/action|Tworzy połączenie bramy między lokacjami.|
|/virtualNetworks/gateways/connections/disconnect/action|Rozłącza połączenie bramy między lokacjami.|
|/virtualNetworks/gateways/connections/read|Pobiera listę połączeń.|
|/virtualNetworks/gateways/connections/test/action|Testuje połączenie bramy między lokacjami.|
|/virtualNetworks/gateways/delete|Usuwa bramę sieci wirtualnej.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Pobiera skrypt konfiguracji urządzenia.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Pobiera dane diagnostyczne bramy.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Pobiera klucz usługi obwodu.|
|/virtualNetworks/gateways/listPackage/action|Wyświetla pakiet bramy sieci wirtualnej.|
|/virtualNetworks/gateways/operationStatuses/read|Odczytuje stan operacji dla bram sieci wirtualnych.|
|/virtualNetworks/gateways/packages/read|Pobiera pakiet bramy sieci wirtualnej.|
|/virtualNetworks/gateways/read|Pobiera bramy sieci wirtualnej.|
|/virtualNetworks/gateways/startDiagnostics/action|Uruchamia diagnostykę bramy sieci wirtualnej.|
|/virtualNetworks/gateways/stopDiagnostics/action|Zatrzymuje diagnostykę bramy sieci wirtualnej.|
|/virtualNetworks/gateways/write|Dodaje bramę sieci wirtualnej.|
|/virtualNetworks/join/action|Umożliwia dołączenie do sieci wirtualnej.|
|/virtualNetworks/operationStatuses/read|Odczytuje stan operacji dla sieci wirtualnych.|
|/virtualNetworks/peer/action|Łączy równorzędnie sieć wirtualną z inną siecią wirtualną.|
|/virtualNetworks/read|Pobiera sieć wirtualną.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Usuwa sieciową grupę zabezpieczeń skojarzoną z podsiecią.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Odczytuje stan operacji dla grupy zabezpieczeń sieci skojarzonej z podsiecią sieci wirtualnej.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Pobiera sieciową grupę zabezpieczeń skojarzoną z podsiecią.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Dodaje sieciową grupę zabezpieczeń skojarzoną z podsiecią.|
|/virtualNetworks/write|Dodaje nową sieć wirtualną.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Operacja | Opis |
|---|---|
|/Capabilities/Read|Pokazuje możliwości|
|/checkStorageAccountAvailability/action|Sprawdza dostępność konta magazynu.|
|/disks/read|Zwraca dysk konta magazynu.|
|/images/Read|Zwraca obraz.|
|/osImages/read|Zwraca obraz systemu operacyjnego.|
|/publicImages/read|Pobiera publiczny obraz maszyny wirtualnej|
|/quotas/read|Pobiera przydział dla subskrypcji.|
|/ register/działania|Zarejestruj w magazynie klasycznym|
|/storageAccounts/delete|Usuwa konto magazynu.|
|/storageAccounts/disks/delete|Usuwa dany dysk konta magazynu.|
|/storageAccounts/disks/operationStatuses/read|Odczytuje stan operacji dla zasobu.|
|/storageAccounts/disks/read|Zwraca dysk konta magazynu.|
|/storageAccounts/disks/write|Dodaje dysk konta magazynu.|
|/storageAccounts/images/delete|Usuwa określony obraz konta magazynu.|
|/storageAccounts/images/read|Zwraca obraz konta magazynu.|
|/storageAccounts/listKeys/action|Wyświetla klucze dostępu dla kont magazynu.|
|/storageAccounts/operationStatuses/read|Odczytuje stan operacji dla zasobu.|
|/storageAccounts/osImages/delete|Usuwa określony obraz systemu operacyjnego konta magazynu.|
|/storageAccounts/osImages/read|Zwraca obraz systemu operacyjnego konta magazynu.|
|/storageAccounts/read|Zwraca konto magazynu z podanym kontem.|
|/storageAccounts/regenerateKey/action|Generuje ponownie istniejące klucze dostępu dla konta magazynu.|
|/storageAccounts/services/diagnosticSettings/read|Pobiera ustawienia diagnostyczne.|
|/storageAccounts/services/diagnosticSettings/write|Dodaje lub modyfikuje ustawienia diagnostyczne.|
|/storageAccounts/services/metricDefinitions/read|Pobiera definicje metryk.|
|/storageAccounts/services/metrics/read|Pobiera metryki.|
|/storageAccounts/services/read|Pobiera dostępne usługi.|
|/storageAccounts/write|Dodaje nowe konto magazynu.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Operacja | Opis |
|---|---|
|/accounts/DELETE|Usuwa konta interfejsów API|
|/accounts/listKeys/action|Wylicz klucze|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne dla zasobu.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla usług Cognitive Services.|
|/accounts/Read|Odczytuje konta interfejsów API.|
|/accounts/regenerateKey/Action|Wygeneruj ponownie klucz|
|/accounts/skus/Read|Odczytuje jednostki SKU dostępne dla istniejącego zasobu.|
|/accounts/Usages/Read|Pobierz użycie przydziału dla istniejącego zasobu.|
|/ kont/zapisu|Zapisuje konta interfejsów API.|
|/Locations/checkSkuAvailability/Action|Odczytuje dostępne jednostki SKU na potrzeby subskrypcji.|
|Operacje/odczytu|Wyświetl listę wszystkich dostępnych operacji|
|/ register/działania|Rejestruje subskrypcję usług kognitywnych|
|/skus/read|Odczytuje dostępne jednostki SKU dla usług kognitywnych.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Operacja | Opis |
|---|---|
|/RateCard/read|Zwraca oferują danych, metadane Miernik/zasobów i szybkości dla danej subskrypcji.|
|/UsageAggregates/read|Pobiera Microsoft Azure — użytek przez subskrypcję. Wynik zawiera agreguje dane dotyczące użycia, subskrypcji i zasobu powiązane informacje, w określonym czasie.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Operacja | Opis |
|---|---|
|/availabilitySets/DELETE|Usuwa zestaw dostępności|
|/availabilitySets/read|Pobierz właściwości zestawu dostępności|
|/availabilitySets/vmSizes/read|Wyświetl dostępne rozmiary na potrzeby tworzenia lub aktualizowania maszyny wirtualnej w zestawie dostępności|
|/availabilitySets/write|Tworzy nowy zestaw dostępności lub aktualizuje istniejący zestaw|
|/disks/beginGetAccess/action|Pobierz identyfikator URI sygnatury dostępu współdzielonego dysku na potrzeby uzyskiwania dostępu do obiektu blob|
|/disks/delete|Usuwa dysk|
|/disks/endGetAccess/action|Wycofaj identyfikator URI sygnatury dostępu współdzielonego dysku|
|/disks/read|Pobierz właściwości dysku|
|/ dysków/zapisu|Tworzy nowy dysk lub aktualizuje istniejący|
|/images/delete|Usuwa obraz|
|/images/Read|Pobierz właściwości obrazu|
|/ obrazów/zapisu|Tworzy nowy obraz lub aktualizuje istniejący obraz|
|/Locations/capsOperations/Read|Pobiera stan operacji asynchronicznej CAP|
|/locations/diskOperations/read|Pobiera stan operacji asynchronicznej dysku|
|/Locations/Operations/Read|Pobiera stan operacji asynchronicznej|
|/locations/publishers/artifacttypes/offers/read|Pobierz właściwości obrazu platformy oferty|
|/locations/publishers/artifacttypes/offers/skus/read|Pobierz właściwości Sku obrazu platformy|
|/locations/publishers/artifacttypes/offers/skus/versions/read|Pobierz właściwości wersję obrazu platformy|
|/locations/publishers/artifacttypes/types/read|Pobierz właściwości typu VMExtension|
|/locations/publishers/artifacttypes/types/versions/read|Pobierz właściwości wersji VMExtension|
|/locations/publishers/read|Pobierz właściwości wydawcy|
|/Locations/runCommands/Read|Wyświetla listę dostępnych poleceń uruchamiania w lokalizacji|
|/Locations/Usages/Read|Pobiera limity usług i bieżące dane użycia zasobów obliczeniowych subskrypcji w danej lokalizacji|
|/Locations/vmSizes/Read|Wyświetla listę dostępnych rozmiarów maszyny wirtualnej w danej lokalizacji|
|/Operations/Read|Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.Compute|
|/ register/działania|Rejestruje subskrypcję za pomocą dostawcy zasobów Microsoft.Compute|
|/restorePointCollections/DELETE|Usuwa kolekcję punktów przywracania i należące do niej punkty przywracania|
|/restorePointCollections/Read|Pobierz właściwości kolekcji punktów przywracania|
|/restorePointCollections/restorePoints/DELETE|Usuwa punkt przywracania|
|/restorePointCollections/restorePoints/Read|Pobierz właściwości punktu przywracania|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Pobierz właściwości punktu przywracania z identyfikatorami URI SAS obiektów blob|
|/restorePointCollections/restorePoints/Write|Tworzy nowy punkt przywracania|
|/ restorePointCollections/zapisu|Tworzy nową kolekcję punktów przywracania lub aktualizuje istniejącą|
|/sharedVMImages/delete|Usuwa SharedVMImage|
|/sharedVMImages/read|Pobierz właściwości SharedVMImage|
|/sharedVMImages/versions/delete|Usuń SharedVMImageVersion|
|/sharedVMImages/versions/read|Pobierz właściwości SharedVMImageVersion|
|/sharedVMImages/versions/replicate/action|Replikacja SharedVMImageVersion do regiony docelowe|
|/sharedVMImages/versions/write|Utwórz nowy SharedVMImageVersion lub zaktualizuj istniejącą|
|/sharedVMImages/write|Tworzy nowy SharedVMImage lub aktualizuje istniejący zestaw|
|/skus/read|Pobiera listę jednostek SKU Microsoft.Compute dostępne dla Twojej subskrypcji|
|/snapshots/beginGetAccess/action|Pobierz identyfikator URI sygnatury dostępu Współdzielonego migawki dla dostępu do obiektów blob|
|/snapshots/delete|Usuń migawkę|
|/snapshots/endGetAccess/action|Wycofaj identyfikator URI sygnatury dostępu Współdzielonego migawki|
|/snapshots/read|Pobierz właściwości migawki|
|/snapshots/write|Utwórz nową migawkę lub zaktualizuj istniejącą|
|/virtualMachines/capture/action|Przechwytuje maszynę wirtualną przez skopiowanie wirtualnych dysków twardych oraz generuje szablon umożliwiający tworzenie podobnych maszyn wirtualnych|
|/virtualMachines/convertToManagedDisks/action|Konwertuje oparte na obiektach blob dyski maszyny wirtualnej na dyski zarządzane|
|/virtualMachines/deallocate/action|Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe|
|/virtualMachines/delete|Usuwa maszynę wirtualną|
|/virtualMachines/extensions/delete|Usuwa rozszerzenie maszyny wirtualnej|
|/virtualMachines/extensions/read|Pobierz właściwości rozszerzenia maszyny wirtualnej|
|/virtualMachines/extensions/write|Tworzy nowe rozszerzenie maszyny wirtualnej lub aktualizuje istniejące rozszerzenie|
|/virtualMachines/generalize/action|Ustawia stan maszyny wirtualnej „Uogólniono” i przygotowuje maszynę wirtualną do przechwycenia|
|/virtualMachines/instanceView/read|Pobiera szczegółowy stan środowiska uruchomieniowego dla maszyny wirtualnej i jej zasobów|
|/virtualMachines/performMaintenance/action|Wykonuje operację konserwacji maszyny wirtualnej.|
|/virtualMachines/powerOff/action|Wyłącza maszynę wirtualną. Należy pamiętać, że maszyna wirtualna nadal będą naliczane opłaty.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Odczytuje definicje metryk maszyny wirtualnej|
|/virtualMachines/read|Pobierz właściwości maszyny wirtualnej|
|/virtualMachines/redeploy/action|Ponownie wdraża maszynę wirtualną|
|/virtualMachines/reimage/action|Odtwarza z obrazu maszynę wirtualną używającą dysku różnicowego.|
|/virtualMachines/restart/action|Uruchamia ponownie maszynę wirtualną|
|/virtualMachines/runCommand/action|Wykonuje wstępnie zdefiniowany skrypt na maszynie wirtualnej|
|/virtualMachines/Start/Action|Uruchamia maszynę wirtualną|
|/virtualMachines/vmSizes/read|Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną|
|/virtualMachines/write|Tworzy nową maszynę wirtualną lub aktualizuje istniejącą maszynę wirtualną|
|/virtualMachineScaleSets/deallocate/action|Wyłącza i zwalnia zasoby obliczeniowe dla wystąpień zestawu skalowania maszyn wirtualnych |
|/virtualMachineScaleSets/delete|Usuwa zestaw skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/delete/action|Usuwa wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/extensions/delete|Usuwa rozszerzenie zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/extensions/read|Pobiera właściwości rozszerzenia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/extensions/write|Tworzy nowe rozszerzenie zestawu skalowania maszyn wirtualnych lub aktualizuje już istniejące|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Ręcznie przeprowadzić domen aktualizacji platformy usługi service fabric, zestawu skalowania maszyn wirtualnych do zakończenia oczekujących aktualizacji, który jest zablokowany|
|/virtualMachineScaleSets/instanceView/read|Pobiera widok wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/manualUpgrade/action|Ręcznie aktualizuje wystąpienia do najnowszego modelu zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/networkInterfaces/read|Pobierz właściwości wszystkich interfejsów sieciowych zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/osUpgradeHistory/read|Pobiera historii uaktualnień systemu operacyjnego dla zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/performMaintenance/action|Wykonuje zaplanowanej konserwacji na wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/powerOff/action|Wyłącza wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Odczytuje definicje metryk zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/publicIPAddresses/read|Pobierz właściwości publicznych adresów IP z zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/read|Pobiera właściwości zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/redeploy/action|Wdróż ponownie wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/reimage/action|Odtwarza z obrazu wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/restart/action|Uruchamia ponownie wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Anuluje operację stopniowego uaktualniania zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/rollingUpgrades/read|Pobierz najnowszy stan stopniowego uaktualniania dla zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/scale/action|Sprawdź, czy dla istniejącego zestawu skalowania maszyn wirtualnych można przeprowadzić skalowanie do wewnątrz / na zewnątrz do określonej liczby wystąpień|
|/virtualMachineScaleSets/skus/read|Wyświetla prawidłowe jednostki SKU dla istniejącego zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/start/action|Uruchamia wystąpienia zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Wyłącza i zwalnia zasoby obliczeniowe dla maszyny wirtualnej w zestawie skalowania maszyn wirtualnych.|
|/virtualMachineScaleSets/virtualMachines/delete|Usuń konkretną maszynę wirtualną z zestawu skalowania maszyn wirtualnych.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Pobiera widok wystąpienia maszyny wirtualnej w zestawie skalowania maszyn wirtualnych.|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ ipConfigurations/publicIPAddresses/read|Pobierz właściwości publicznego adresu IP utworzonych przy użyciu zestawu skalowania maszyn wirtualnych. Zestaw skalowania maszyn wirtualnych można utworzyć co najwyżej jeden publiczny adres IP dla elementu ipconfiguration (prywatnego adresu IP)|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Pobierz właściwości jednego lub wszystkich konfiguracji IP interfejsu sieciowego utworzone za pomocą zestawu skalowania maszyn wirtualnych. Konfiguracje adresów IP reprezentują prywatnych adresów IP|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Pobierz właściwości jednego lub wszystkich interfejsów sieciowych maszyny wirtualnej utworzonej przy użyciu zestawu skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Wykonuje zaplanowanej konserwacji na wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Wyłącza wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Odczytuje definicje metryk maszyny wirtualnej w zestawie skalowania|
|/virtualMachineScaleSets/virtualMachines/read|Pobiera właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|Wdraża ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Odtwarza z obrazu wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Uruchamia ponownie wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych.|
|/virtualMachineScaleSets/virtualMachines/start/action|Uruchamia wystąpienie maszyny wirtualnej w zestawie skalowania maszyn wirtualnych.|
|/virtualMachineScaleSets/virtualMachines/write|Aktualizuje właściwości maszyny wirtualnej w zestawie skalowania maszyn wirtualnych|
|/virtualMachineScaleSets/write|Tworzy nowy zestaw skalowania maszyn wirtualnych lub aktualizuje istniejący zestaw|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Operacja | Opis |
|---|---|
|/balances/Read|Wyświetl użycie podsumowania okresie rozliczeniowym dla grupy zarządzania.|
|/budgets/read|Wyświetl listę budżetów subskrypcji lub grupy zarządzania.|
|/budgets/write|Tworzy, aktualizować i usuwać budżetów subskrypcji lub grupy zarządzania.|
|/marketplaces/Read|Wyświetl szczegóły użycia zasobów marketplace dla zakresu EA i WebDirect subskrypcji.|
|/Operations/Read|Wyświetl listę operacji wszystkie obsługiwane przez dostawcę zasobów Microsoft.Consumption.|
|/pricesheets/read|Wyświetlanie danych Pricesheets dla subskrypcji lub grupy zarządzania.|
|/reservationDetails/Read|Wyświetl szczegóły użycia dla wystąpień zastrzeżone przez grupy zarządzania lub kolejności rezerwacji. Szczegóły danych jest dla każdego wystąpienia na poziomie dnia.|
|/reservationSummaries/Read|Wyświetl podsumowanie zastrzeżone wystąpienia według grup zarządzania lub kolejności rezerwacji wykorzystania. Podsumowanie danych jest na poziomie miesiąc lub codziennie.|
|/reservationTransactions/Read|Listy historii transakcji dla wystąpień zastrzeżone przez grupy zarządzania.|
|/terms/read|Lista warunków dla subskrypcji lub grupy zarządzania.|
|/usageDetails/Read|Wyświetl szczegóły użycia dla zakresu EA i WebDirect subskrypcji.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Operacja | Opis |
|---|---|
|/containerGroups/containers/logs/read|Pobierz dzienniki dla określonego kontenera.|
|/containerGroups/delete|Usuń określoną grupę kontenerów.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla grupy kontenerów.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla grupy kontenerów.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki grupy kontenerów.|
|/containerGroups/read|Pobierz wszystkie grupy kontenerów.|
|/containerGroups/write|Utwórz lub zaktualizuj określoną grupę kontenerów.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Operacja | Opis |
|---|---|
|/checkNameAvailability/read|Sprawdza, czy nazwa rejestru kontenera jest dostępne do użycia.|
|/Locations/operationResults/Read|Pobiera wynik operacji asynchronicznej|
|/Operations/Read|Wyświetla listę wszystkich dostępnych operacji interfejsu API REST Azure kontenera rejestru|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów rejestru kontenera i umożliwia tworzenie kontenera rejestrów.|
|/registries/delete|Usuwa rejestru kontenera.|
|/registries/eventGridFilters/delete|Usuwa filtr siatki zdarzeń z rejestru kontenera.|
|/registries/eventGridFilters/read|Pobiera właściwości siatki filtru określonego zdarzenia lub wyświetla listę wszystkich filtrów siatki zdarzeń rejestru określonego kontenera.|
|/registries/eventGridFilters/write|Tworzy lub aktualizuje filtr zdarzeń siatki dla kontenera rejestru z określonymi parametrami.|
|/registries/listCredentials/action|Wyświetla listę poświadczenia logowania dla określonego kontenera rejestru.|
|/registries/listUsages/read|Wyświetla listę użycia przydziału dla określonego kontenera rejestru.|
|/registries/operationStatuses/Read|Pobiera stan operacji asynchronicznej rejestru|
|/registries/Read|Pobiera właściwości rejestru określonego kontenera lub wyświetla ich listę wszystkich rejestrów kontenera w ramach określonej grupy zasobów lub subskrypcji.|
|/registries/regenerateCredential/action|Generuje ponownie jednego poświadczenia logowania dla określonego kontenera rejestru.|
|/registries/replications/DELETE|Usuwa replikacji z rejestru kontenera.|
|/registries/replications/operationStatuses/Read|Pobiera stan operacji asynchronicznej dla replikacji|
|/registries/replications/Read|Pobiera właściwości określonej replikacji lub wyświetla ich listę wszystkich replikacje rejestru określonego kontenera.|
|/registries/replications/Write|Tworzy lub aktualizuje Replikacja rejestru kontenera z określonymi parametrami.|
|/registries/webhooks/delete|Usuwa elementu webhook z rejestru kontenera.|
|/registries/webhooks/getCallbackConfig/action|Pobiera konfiguracji identyfikator URI usługi i niestandardowe nagłówki dla elementu webhook.|
|/registries/webhooks/listEvents/action|Wyświetla listę ostatnio zdarzeń dla określonego elementu webhook.|
|/registries/webhooks/operationStatuses/read|Pobiera stan operacji asynchronicznej dla elementu webhook|
|/registries/webhooks/ping/Action|Wyzwala zdarzenie typu ping do wysłania do elementu webhook.|
|/registries/webhooks/read|Pobiera właściwości określonego elementu webhook lub wyświetla ich listę wszystkich elementów webhook rejestru określonego kontenera.|
|/registries/webhooks/Write|Tworzy lub aktualizuje elementu webhook dla rejestru kontenera z określonymi parametrami.|
|/ rejestrów/zapisu|Tworzy lub aktualizuje rejestr kontenera z określonymi parametrami.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Operacja | Opis |
|---|---|
|/containerServices/delete|Usuwa określoną usługę kontenera|
|/containerServices/read|Pobiera określoną usługę kontenera|
|/containerServices/write|Umieść lub zaktualizuj określoną usługę kontenera|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Operacja | Opis |
|---|---|
|/Applications/DELETE|Operacja usuwania|
|/applications/listSecrets/action|Wyświetl klucze tajne|
|/applications/listSingleSignOnToken/action|Odczyt jednokrotnego tokenów|
|/Applications/Read|Operacja odczytu|
|/ applications/zapisu|Operacja zapisu|
|/ applications/zapisu|Operacja zapisu|
|/listCommunicationPreference/action|Lista komunikacji preferencji|
|/Operations/Read|operacje odczytu|
|/updateCommunicationPreference/action|Preferencji komunikacji aktualizacji|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Operacja | Opis |
|---|---|
|/hubs/adobemetadata/action|Utwórz lub zaktualizuj wszystkie metadane Adobe Insights klienta platformy Azure|
|/hubs/adobemetadata/read|Odczytać metadanych Adobe Insights dowolnego klienta platformy Azure|
|/hubs/authorizationPolicies/delete|Usuń dowolne zasady sygnatury dostępu współdzielonego Insights klienta platformy Azure|
|/hubs/authorizationPolicies/read|Przeczytaj zasady podpisu dostępu udostępniony żadnych informacji szczegółowych Azure klienta|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Ponowne wygenerowanie klucza podstawowego Azure klienta Insights zasad dostępu współużytkowanego podpisu|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Ponowne wygenerowanie klucza pomocniczego Azure klienta Insights zasad dostępu współużytkowanego podpisu|
|/hubs/authorizationPolicies/write|Utwórz lub zaktualizuj dowolne zasady sygnatury dostępu współdzielonego Insights klienta Azure|
|/hubs/connectors/activate/action|Aktywowanie wszystkich łączników Insights klienta platformy Azure|
|/hubs/connectors/activate/action|Aktywowanie wszystkich łączników Insights klienta platformy Azure|
|/hubs/connectors/delete|Usunięcie łącznika Insights dowolnego klienta platformy Azure|
|/hubs/connectors/getruntimestatus/action|Pobierz stan czasu wykonywania żadnych łącznika Insights klienta usługi Azure|
|/hubs/connectors/mappings/activate/action|Aktywuj żadnego mapowania łącznika Insights klienta platformy Azure|
|/hubs/connectors/mappings/delete|Usunąć wszystkie mapowania łącznika Insights klienta Azure|
|/hubs/Connectors/Mappings/Operations/Read|Odczytaj dowolny wynik operacji mapowania łącznika Insights klienta Azure|
|/hubs/connectors/mappings/read|Przeczytaj żadnego mapowania łącznika Insights klienta platformy Azure|
|/hubs/connectors/mappings/write|Utwórz lub zaktualizuj żadnego mapowania łącznika Insights klienta platformy Azure|
|/hubs/connectors/operations/read|Odczytaj dowolny wynik operacji łącznika Insights klienta usługi Azure|
|/hubs/connectors/read|Każdy łącznik Insights Azure klienta do odczytu|
|/hubs/connectors/saveauthinfo/action|Utwórz lub zaktualizuj bramą uwierzytelniania dowolnego klienta Azure Insights łącznika|
|/hubs/connectors/update/action|Aktualizacja wszystkich łączników Insights klienta platformy Azure|
|/hubs/connectors/write|Utwórz lub zaktualizuj każdy łącznik Insights klienta platformy Azure|
|/hubs/crmmetadata/action|Utwórz lub zaktualizuj wszystkie metadane Crm Insights klienta platformy Azure|
|/hubs/crmmetadata/read|Odczytać metadanych Crm Insights dowolnego klienta platformy Azure|
|/hubs/delete|Usunąć wszelkie Centrum Insights klienta platformy Azure|
|/hubs/gdpr/delete|Usuń wszelkie Gdpr Insights klienta Azure|
|/hubs/gdpr/read|Przeczytaj Gdpr Insights dowolnego klienta platformy Azure|
|/hubs/gdpr/write|Utwórz lub zaktualizuj Gdpr Insights dowolnego klienta platformy Azure|
|/hubs/getbillingcredits/read|Pobierz klienta platformy Azure Insights Centrum rozliczeń środków|
|/hubs/getbillinghistory/read|Pobierz historię rozliczeń Centrum Insights klienta usługi Azure|
|/hubs/images/delete|Usuń obraz Insights dowolnego klienta Azure|
|/hubs/images/read|Przeczytaj żadnego obrazu Insights klienta platformy Azure|
|/hubs/images/write|Utwórz lub zaktualizuj żadnego obrazu Insights klienta platformy Azure|
|/hubs/interactions/delete|Usuń wszelkie Insights klientami azure|
|/hubs/interactions/operations/read|Odczytaj dowolny wynik operacji interakcji Insights klienta Azure|
|/hubs/interactions/read|Przeczytaj interakcji Insights klienta platformy Azure|
|/hubs/interactions/suggestrelationshiplinks/action|Sugeruj łącza relacji dla dowolnego Insights klientami Azure|
|/hubs/interactions/write|Utwórz lub zaktualizuj interakcji Insights klienta platformy Azure|
|/hubs/kpi/delete|Usuń wszelkie klienta Azure Insights kluczowy wskaźnik wydajności|
|/hubs/kpi/operations/read|Odczytaj dowolny wynik operacji Azure klienta Insights kluczowych wskaźników wydajności|
|/hubs/kpi/read|Przeczytaj dowolnego klienta Azure Insights kluczowy wskaźnik wydajności|
|/hubs/kpi/reprocess/action|Ponownie przetworzyć dowolnego klienta Azure Insights kluczowe wskaźniki wydajności|
|/hubs/kpi/write|Utwórz lub zaktualizuj dowolnego klienta Azure Insights kluczowy wskaźnik wydajności|
|/hubs/links/delete|Usuń wszystkie linki Insights klienta usługi Azure|
|/hubs/links/operations/read|Odczytaj dowolny wynik operacji Azure klienta Insights łączy|
|/hubs/links/read|Przeczytaj łącza Insights klienta platformy Azure|
|/hubs/links/write|Utwórz lub zaktualizuj łącza klienta platformy Azure|
|/hubs/msemetadata/action|Utwórz lub zaktualizuj wszystkie metadane Mse Insights klienta platformy Azure|
|/hubs/msemetadata/read|Odczytać metadanych Mse Insights dowolnego klienta platformy Azure|
|/hubs/operationresults/read|Wyniki operacji centrum Insights klienta platformy Azure|
|/hubs/predictions/delete|Usuń wszelkie prognoz Insights klienta Azure|
|/hubs/predictions/operations/read|Odczytaj dowolny wynik operacji prognoz Insights klienta Azure|
|/hubs/predictions/read|Przeczytaj prognoz Insights dowolnego klienta Azure|
|/hubs/predictions/write|Utwórz lub zaktualizuj wszystkie operacje przewidywania dla klienta usługi Azure|
|/hubs/predictivematchpolicies/delete|Usuń wszystkie zasady predykcyjnej dopasowania Insights klienta usługi Azure|
|/hubs/predictivematchpolicies/operations/read|Odczytaj dowolny wynik operacji Azure klienta Insights predykcyjnej dopasowanie zasad|
|/hubs/predictivematchpolicies/read|Odczytu żadnych zasad predykcyjnej dopasowania Insights klienta Azure|
|/hubs/predictivematchpolicies/write|Utwórz lub zaktualizuj wszystkie zasady predykcyjnej dopasowania Insights klienta usługi Azure|
|/hubs/profiles/delete|Usuń profil Insights dowolnego klienta usługi Azure|
|/hubs/profiles/operations/read|Odczytaj dowolny wynik operacji profil Insights klienta Azure|
|/hubs/profiles/read|Przeczytaj dowolnego profilu Insights klienta platformy Azure|
|/hubs/profiles/write|Zapis dowolnego profilu Insights klienta platformy Azure|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla zasobu|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla zasobu|
|/hubs/read|Przeczytaj dowolnego Centrum Insights Azure klienta|
|/hubs/relationshiplinks/delete|Usuń wszystkie linki relacji Insights klienta usługi Azure|
|/hubs/relationshiplinks/operations/read|Odczytaj dowolny wynik operacji łączy relacja Insights klienta Azure|
|/hubs/relationshiplinks/read|Odczytywanie dowolnego klienta Azure Insights relacji|
|/hubs/relationshiplinks/write|Utwórz lub zaktualizuj łącza relacji Insights klienta platformy Azure|
|/hubs/relationships/delete|Usuń wszystkie relacje Insights klienta Azure|
|/hubs/relationships/operations/read|Odczytaj dowolny wynik operacji Insights z klientami Azure|
|/hubs/relationships/read|Wszystkie relacje Insights Azure klienta do odczytu|
|/hubs/relationships/write|Utwórz lub zaktualizuj wszystkie relacje Insights klienta platformy Azure|
|/hubs/roleAssignments/delete|Usuń wszelkie przydział Rbac Insights klienta Azure|
|/hubs/roleAssignments/operations/read|Odczytaj dowolny wynik operacji Azure klienta Insights Rbac przypisania|
|/hubs/roleAssignments/read|Przeczytaj przydziału Rbac Insights klienta platformy Azure|
|/hubs/roleAssignments/write|Utwórz lub zaktualizuj wszystkie przypisania Rbac Insights klienta Azure|
|/hubs/roles/read|Wszystkie role Rbac Insights Azure klienta do odczytu|
|/hubs/salesforcemetadata/action|Utwórz lub zaktualizuj wszystkie metadane SalesForce Insights klienta platformy Azure|
|/hubs/salesforcemetadata/read|Odczytać metadanych SalesForce Insights dowolnego klienta platformy Azure|
|/hubs/segments/delete|Usuń segmentów Insights klienta Azure|
|/hubs/segments/dynamic/action|Segmenty dynamicznie szczegółowe informacje o wszelkich klienta usługi Azure Management|
|/hubs/segments/read|Przeczytaj segmentów Insights klienta platformy Azure|
|/hubs/segments/static/action|Segmenty wszystkie statyczne wglądu Azure klienta zarządzania|
|/hubs/segments/write|Utwórz lub zaktualizuj segmentów Insights klienta platformy Azure|
|/hubs/sqlconnectionstrings/delete|Usuń wszelkie SqlConnectionStrings Insights klienta Azure|
|/hubs/sqlconnectionstrings/read|Przeczytaj SqlConnectionStrings Insights dowolnego klienta platformy Azure|
|/hubs/sqlconnectionstrings/write|Utwórz lub zaktualizuj SqlConnectionStrings Insights dowolnego klienta platformy Azure|
|/hubs/suggesttypeschema/action|Generowanie wskazujące typ schematu z przykładowych danych|
|/hubs/tenantmanagement/read|Zarządzanie ustawieniami Centrum żadnych Insights klienta Azure|
|/hubs/views/delete|Usuń wszelkie widok aplikacji Insights klienta usługi Azure|
|/hubs/views/read|Przeczytaj dowolnym widoku App Insights klienta usługi Azure|
|/hubs/views/write|Utwórz lub zaktualizuj dowolnym widoku App Insights klienta usługi Azure|
|/hubs/widgettypes/read|Przeczytaj żadnych typów App Widget Insights klienta usługi Azure|
|/hubs/write|Utwórz lub zaktualizuj Centrum Insights dowolnego klienta usługi Azure|
|/Operations/Read|Przeczytaj Metadatas interfejsu Api Insights klienta platformy Azure|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów Insights klientów i umożliwia tworzenie zasobów Insights klienta|
|/ unregister/działania|Wyrejestrowuje subskrypcji dla dostawcy zasobów Insights klienta|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Operacja | Opis |
|---|---|
|/Catalogs/DELETE|Usuwa katalogu.|
|/Catalogs/Read|Pobierz właściwości dla katalogu lub katalogów w ramach subskrypcji lub grupy zasobów.|
|/catalogs/write|Tworzy katalog lub aktualizuje znaczniki i właściwości dla katalogu.|
|/checkNameAvailability/action|Służy do sprawdzania dostępności nazwy katalogu dla dzierżawcy.|
|/Operations/Read|Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.DataCatalog.|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów Microsoft.DataCatalog.|
|/ unregister/działania|Wyrejestrowuje subskrypcji z Microsoft.DataCatalog dostawcy zasobów.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Operacja | Opis |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla datafactories|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla fabryki|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla fabryki|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Operacja | Opis |
|---|---|
|/accounts/computePolicies/delete|Usuń zasady obliczeń.|
|/accounts/computePolicies/Read|Pobierz informacje o zasadach obliczeń.|
|/accounts/computePolicies/write|Utwórz lub zaktualizuj zasady obliczeń.|
|/accounts/dataLakeStoreAccounts/delete|Odłącz od konta DataLakeAnalytics przy użyciu konta DataLakeStore.|
|/accounts/dataLakeStoreAccounts/read|Pobiera informacje o połączonego konta DataLakeStore DataLakeAnalytics konta.|
|/accounts/dataLakeStoreAccounts/write|Utwórz lub zaktualizuj połączonego konta DataLakeStore DataLakeAnalytics konta.|
|/accounts/DELETE|Usuwanie konta DataLakeAnalytics.|
|/accounts/firewallRules/delete|Usuwanie reguły zapory.|
|/accounts/firewallRules/read|Pobierz informacje dotyczące reguły zapory.|
|/accounts/firewallRules/write|Utwórz lub zaktualizuj regułę zapory.|
|/accounts/operationResults/read|Pobierz wynik operacji DataLakeAnalytics konta.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Pobierz ustawienia diagnostyki dla konta DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Utwórz lub zaktualizuj ustawienia diagnostyki dla konta DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Pobierz dostępne dzienniki dla konta DataLakeAnalytics.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Pobierz dostępne metryki dla konta DataLakeAnalytics.|
|/accounts/Read|Pobiera informacje o istniejącym koncie DataLakeAnalytics.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Wyświetl listę tokeny sygnatury dostępu Współdzielonego dla kontenerów magazynów konta DataLakeAnalytics połączonego konta magazynu.|
|/accounts/storageAccounts/Containers/read|Pobierz kontenery konta DataLakeAnalytics połączonego konta magazynu.|
|/accounts/storageAccounts/delete|Odłączanie konta magazynu z konta DataLakeAnalytics.|
|/accounts/storageAccounts/read|Pobiera informacje o połączonego konta magazynu DataLakeAnalytics konta.|
|/accounts/storageAccounts/write|Utwórz lub zaktualizuj konto magazynu połączonego konta DataLakeAnalytics.|
|/accounts/TakeOwnerShip/Action|Przyznaj uprawnienia, aby anulować zadania przesłane przez innych użytkowników.|
|/ kont/zapisu|Utwórz lub zaktualizuj konto DataLakeAnalytics.|
|/Locations/CAPABILITY/Read|Uzyskiwanie informacji o subskrypcji dotyczące przy użyciu DataLakeAnalytics.|
|/locations/checkNameAvailability/action|Sprawdź dostępność nazwy konta DataLakeAnalytics.|
|/Locations/operationResults/Read|Pobierz wynik operacji DataLakeAnalytics konta.|
|/Operations/Read|Pobiera dostępne operacje z DataLakeAnalytics.|
|/ register/działania|Zarejestruj subskrypcję DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Operacja | Opis |
|---|---|
|/accounts/DELETE|Usuwanie konta DataLakeStore.|
|/accounts/enableKeyVault/action|Włącz konto DataLakeStore KeyVault.|
|/accounts/firewallRules/delete|Usuwanie reguły zapory.|
|/accounts/firewallRules/read|Pobierz informacje dotyczące reguły zapory.|
|/accounts/firewallRules/write|Utwórz lub zaktualizuj regułę zapory.|
|/accounts/operationResults/read|Pobierz wynik operacji DataLakeStore konta.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Pobierz ustawienia diagnostyki dla konta DataLakeStore.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Utwórz lub zaktualizuj ustawienia diagnostyki dla konta DataLakeStore.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Pobierz dostępne dzienniki dla konta DataLakeStore.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Pobierz dostępne metryki dla konta DataLakeStore.|
|/accounts/Read|Pobiera informacje o istniejącym koncie DataLakeStore.|
|/accounts/SUPERUSER/Action|Przyznaj administratora w usłudze Data Lake Store po udzieleniu z Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Usuń zaufanego dostawcę tożsamości.|
|/accounts/trustedIdProviders/read|Pobiera informacje o zaufanego dostawcę tożsamości.|
|/accounts/trustedIdProviders/write|Utwórz lub zaktualizuj zaufanego dostawcę tożsamości.|
|/ kont/zapisu|Utwórz lub zaktualizuj konto DataLakeStore.|
|/Locations/CAPABILITY/Read|Uzyskiwanie informacji o subskrypcji dotyczące przy użyciu DataLakeStore.|
|/locations/checkNameAvailability/action|Sprawdź dostępność nazwy konta DataLakeStore.|
|/Locations/operationResults/Read|Pobierz wynik operacji DataLakeStore konta.|
|/Operations/Read|Pobiera dostępne operacje z DataLakeStore.|
|/ register/działania|Zarejestruj subskrypcję DataLakeStore.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Operacja | Opis |
|---|---|
|/locations/performanceTiers/read|Zwraca listę dostępnych poziomów wydajności.|
|/performanceTiers/read|Zwraca listę dostępnych poziomów wydajności.|
|/servers/delete|Usuwa istniejącego serwera.|
|/servers/firewallRules/delete|Usuwa istniejącą regułę zapory.|
|/servers/firewallRules/read|Zwraca listę zapory zasady serwera lub pobiera właściwości dla reguły zapory określony.|
|/servers/firewallRules/write|Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera disagnostic ustawienie dla zasobu|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Zwracane typy metryk, które są dostępne dla baz danych|
|/servers/read|Zwraca listę serwerów lub pobiera właściwości dla określonego serwera.|
|/servers/recoverableServers/read|Powrót do odzyskania informacji o serwerze MySQL|
|/servers/virtualNetworkRules/delete|Usuwa istniejącą regułę sieci wirtualnej|
|/servers/virtualNetworkRules/read|Zwraca listę sieci wirtualnej reguły lub pobiera właściwości dla reguły określonej sieci wirtualnej.|
|/servers/virtualNetworkRules/write|Tworzy regułę sieci wirtualnej z określonymi parametrami lub zaktualizować właściwości bądź tagi dla reguły określonej sieci wirtualnej.|
|/ serwery/zapisu|Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Operacja | Opis |
|---|---|
|/locations/performanceTiers/read|Zwraca listę dostępnych poziomów wydajności.|
|/performanceTiers/read|Zwraca listę dostępnych poziomów wydajności.|
|/servers/delete|Usuwa istniejącego serwera.|
|/servers/firewallRules/delete|Usuwa istniejącą regułę zapory.|
|/servers/firewallRules/read|Zwraca listę zapory zasady serwera lub pobiera właściwości dla reguły zapory określony.|
|/servers/firewallRules/write|Tworzy regułę zapory z podanymi parametrami lub aktualizuje istniejącą regułę.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera disagnostic ustawienie dla zasobu|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Zwracane typy dzienników dostępnych dla baz danych|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Zwracane typy metryk, które są dostępne dla baz danych|
|/servers/read|Zwraca listę serwerów lub pobiera właściwości dla określonego serwera.|
|/servers/recoverableServers/read|Powrót do odzyskania informacji o serwerze PostgreSQL|
|/servers/virtualNetworkRules/delete|Usuwa istniejącą regułę sieci wirtualnej|
|/servers/virtualNetworkRules/read|Zwraca listę sieci wirtualnej reguły lub pobiera właściwości dla reguły określonej sieci wirtualnej.|
|/servers/virtualNetworkRules/write|Tworzy regułę sieci wirtualnej z określonymi parametrami lub zaktualizować właściwości bądź tagi dla reguły określonej sieci wirtualnej.|
|/ serwery/zapisu|Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Operacja | Opis |
|---|---|
|/checkNameAvailability/Action|Sprawdź, czy jest dostępna nazwa Jeśli Centrum IotHub|
|/checkProvisioningServiceNameAvailability/Action|Sprawdź, czy jest dostępna nazwa Jeśli Centrum IotHub|
|/ElasticPools/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/ElasticPools/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/elasticPools/iotHubTenants/Delete|Usuń zasób dzierżawy Centrum IotHub|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Usuwanie grupy konsumentów EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Pobierz grupy konsumentów EventHub|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Tworzenie grupy konsumentów EventHub|
|/elasticPools/iotHubTenants/exportDevices/Action|Eksportuj urządzeń|
|/elasticPools/iotHubTenants/getStats/Read|Pobiera zasób Statystyka dzierżawy Centrum IotHub|
|/elasticPools/iotHubTenants/importDevices/Action|Importuj urządzeń|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Pobiera klucz dzierżawy Centrum IotHub|
|/elasticPools/iotHubTenants/jobs/Read|Pobierz zadania szczegóły przesyłane na podany Centrum IotHub|
|/elasticPools/iotHubTenants/listKeys/Action|Pobiera klucze dzierżawy Centrum IotHub|
|/ElasticPools/IotHubTenants/logDefinitions/read|Pobiera definicje dzienników dostępnych dla usługi Centrum IotHub|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Pobiera dostępne metryki dla usługi Centrum IotHub|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Pobieraj metryki przydziału|
|/elasticPools/iotHubTenants/Read|Pobiera zasób dzierżawy Centrum IotHub|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Testowa wiadomość przed wszystkie istniejące trasy|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Testowa wiadomość przed podany test trasy|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Pobiera stan wszystkich punktów końcowych routingu dla Centrum IotHub|
|/elasticPools/iotHubTenants/Write|Utwórz lub zaktualizuj zasób dzierżawy Centrum IotHub|
|/ElasticPools/metricDefinitions/read|Pobiera dostępne metryki dla usługi Centrum IotHub|
|/iotHubs/certificates/generateVerificationCode/Action|Generuj kod weryfikacyjny|
|/iotHubs/certificates/verify/Action|Sprawdź zasobów certyfikatu|
|/iotHubs/Delete|Usuń zasób z Centrum IotHub|
|/IotHubs/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/IotHubs/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/iotHubs/eventGridFilters/Delete|Usuwa filtr zdarzeń siatki|
|/iotHubs/eventGridFilters/Read|Pobiera filtr zdarzeń siatki|
|/iotHubs/eventGridFilters/Write|Utworzyć lub zaktualizować istniejący filtr zdarzeń siatki|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Usuwanie grupy konsumentów EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Pobierz grupy konsumentów EventHub|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Tworzenie grupy konsumentów EventHub|
|/iotHubs/exportDevices/Action|Eksportuj urządzeń|
|/iotHubs/importDevices/Action|Importuj urządzeń|
|/iotHubs/iotHubKeys/listkeys/Action|Pobierz klucz Centrum IotHub dla podanej nazwie|
|/iotHubs/iotHubStats/Read|Uzyskać statystyki Centrum IotHub|
|/iotHubs/jobs/Read|Pobierz zadania szczegóły przesyłane na podany Centrum IotHub|
|/iotHubs/listkeys/Action|Pobierz wszystkie klucze Centrum IotHub|
|/IotHubs/logDefinitions/read|Pobiera definicje dzienników dostępnych dla usługi Centrum IotHub|
|/IotHubs/metricDefinitions/read|Pobiera dostępne metryki dla usługi Centrum IotHub|
|/iotHubs/quotaMetrics/Read|Pobieraj metryki przydziału|
|/iotHubs/Read|Pobiera zasoby Centrum IotHub|
|/iotHubs/routing/$testall/Action|Testowa wiadomość przed wszystkie istniejące trasy|
|/iotHubs/routing/$testnew/Action|Testowa wiadomość przed podany test trasy|
|/iotHubs/routingEndpointsHealth/Read|Pobiera stan wszystkich punktów końcowych routingu dla Centrum IotHub|
|/iotHubs/skus/Read|Pobierz prawidłowe jednostki SKU z Centrum IotHub|
|/iotHubs/Write|Utwórz lub zaktualizuj zasób z Centrum IotHub|
|/ operations/odczytu|Pobierz wszystkie operacje ResourceProvider|
|/provisioningServices/certificates/generateVerificationCode/Action|Generuj kod weryfikacyjny|
|/provisioningServices/certificates/verify/Action|Sprawdź zasobów certyfikatu|
|/provisioningServices/Delete|Usuń zasób IotDps|
|/provisioningServices/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/provisioningServices/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/provisioningServices/listkeys/Action|Pobierz wszystkie klucze IotDps|
|/provisioningServices/logDefinitions/read|Pobiera definicje dzienników dostępnych dla inicjowania obsługi usługi|
|/provisioningServices/metricDefinitions/read|Pobiera dostępne metryki dla usługi udostępniania|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Pobieranie kluczy IotDps dla nazwy klucza|
|/provisioningServices/Read|Uzyskanie IotDps zasobu|
|/provisioningServices/skus/Read|Pobierz prawidłowe IotDps jednostki SKU|
|/provisioningServices/Write|Utwórz zasób IotDps|
|/ register/działania|Zarejestruj subskrypcję dostawcy zasobów Centrum IotHub i umożliwia tworzenie zasobów Centrum IotHub|
|/ register/działania|Zarejestruj subskrypcję dostawcy zasobów Centrum IotHub i umożliwia tworzenie zasobów Centrum IotHub|
|/ użycia/odczytu|Uzyskanie subskrypcji Szczegóły obciążenia dla tego dostawcy.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Operacja | Opis |
|---|---|
|/labCenters/delete|Usuń centrów laboratorium.|
|/labCenters/read|Przeczytaj centrów laboratorium.|
|/labCenters/write|Dodaje lub modyfikuje centrów laboratorium.|
|/labs/artifactSources/armTemplates/read|Przeczytaj szablony Menedżera zasobów platformy azure.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Generuje szablonu usługi ARM dla danego artefaktu, przekazuje wymaganych plików do konta magazynu i weryfikuje wygenerowanego artefaktu.|
|/Labs/artifactSources/artifacts/Read|Przeczytaj artefaktów.|
|/Labs/artifactSources/DELETE|Usuń źródła artefaktu.|
|/Labs/artifactSources/Read|Przeczytaj źródeł artefaktu.|
|/Labs/artifactSources/Write|Dodaje lub modyfikuje źródeł artefaktu.|
|/labs/ClaimAnyVm/action|Oświadczenia losowe claimable maszynę wirtualną w laboratorium.|
|/Labs/Costs/Read|Przeczytaj kosztów.|
|/Labs/Costs/Write|Dodaje lub modyfikuje kosztów.|
|/Labs/CreateEnvironment/Action|Tworzenie maszyn wirtualnych w laboratorium.|
|/Labs/customImages/DELETE|Usuwanie obrazów niestandardowych.|
|/Labs/customImages/Read|Niestandardowe obrazy.|
|/labs/customImages/write|Dodaje lub modyfikuje niestandardowych obrazów.|
|/Labs/DELETE|Usuń labs.|
|/labs/ExportResourceUsage/action|Eksportuje użycia zasobów laboratorium do konta magazynu|
|/Labs/Formulas/DELETE|Usuń formuły.|
|/Labs/Formulas/Read|Przeczytaj formuły.|
|/labs/formulas/write|Dodaje lub modyfikuje formuły.|
|/labs/galleryImages/read|Przeczytaj obrazów w galerii.|
|/labs/GenerateUploadUri/action|Generuj identyfikator URI dla przesyłania obrazów dysku niestandardowych do laboratorium.|
|/Labs/ImportVirtualMachine/Action|Zaimportuj maszynę wirtualną do innego laboratorium.|
|/labs/ListVhds/action|Listy obrazów dysku dostępne na potrzeby tworzenia niestandardowego obrazu.|
|/labs/notificationChannels/delete|Usuń notificationchannels.|
|/labs/notificationChannels/Notify/action|Wyślij powiadomienia do podanego kanału.|
|/labs/notificationChannels/read|Przeczytaj notificationchannels.|
|/labs/notificationChannels/write|Dodaje lub modyfikuje notificationchannels.|
|/Labs/policySets/EvaluatePolicies/Action|Ocenia zasady laboratorium.|
|/Labs/policySets/policies/DELETE|Usuwanie zasad.|
|/labs/policySets/policies/read|Przeczytaj zasady.|
|/labs/policySets/policies/write|Dodaje lub modyfikuje zasady.|
|/Labs/Read|Laboratoria odczytu.|
|/Labs/Schedules/DELETE|Usuń harmonogramów.|
|/Labs/Schedules/EXECUTE/Action|Wykonanie zgodnie z harmonogramem.|
|/Labs/Schedules/ListApplicable/Action|Wyświetla listę wszystkich odpowiednich harmonogramów|
|/Labs/Schedules/Read|Przeczytaj harmonogramów.|
|/Labs/Schedules/Write|Dodaje lub modyfikuje harmonogramów.|
|/labs/serviceRunners/delete|Usuń używanych modułów uruchamiających usługi.|
|/labs/serviceRunners/read|Przeczytaj używanych modułów uruchamiających usługi.|
|/labs/serviceRunners/write|Dodaje lub modyfikuje używanych modułów uruchamiających usługi.|
|/Labs/Users/DELETE|Usuń profile użytkowników.|
|/labs/users/disks/Attach/action|Dołącz i tworzenia dzierżawy dysku do maszyny wirtualnej.|
|/Labs/Users/Disks/DELETE|Usuń dyski.|
|/labs/users/disks/Detach/action|Odłącz i przerwie dzierżawy dysków dołączonych do maszyny wirtualnej.|
|/Labs/Users/Disks/Read|Przeczytaj dysków.|
|/Labs/Users/Disks/Write|Dodaje lub modyfikuje dysków.|
|/Labs/Users/environments/DELETE|Usuwanie środowiska.|
|/Labs/Users/environments/Read|Przeczytaj środowisk.|
|/Labs/Users/environments/Write|Dodaje lub modyfikuje środowisk.|
|/Labs/Users/Read|Przeczytaj profile użytkowników.|
|/Labs/Users/secrets/DELETE|Usuwanie kluczy tajnych.|
|/Labs/Users/secrets/Read|Odczyt kluczy tajnych.|
|/Labs/Users/secrets/Write|Dodaje lub modyfikuje kluczy tajnych.|
|/labs/users/serviceFabrics/delete|Usunięcie sieci szkieletowej usług.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Wyświetla listę wszystkich odpowiednich harmonogramów|
|/labs/users/serviceFabrics/read|Sieci szkieletowe odczytu usługi.|
|/labs/users/serviceFabrics/schedules/delete|Usuń harmonogramów.|
|/labs/users/serviceFabrics/schedules/Execute/action|Wykonanie zgodnie z harmonogramem.|
|/labs/users/serviceFabrics/schedules/read|Przeczytaj harmonogramów.|
|/labs/users/serviceFabrics/schedules/write|Dodaje lub modyfikuje harmonogramów.|
|/labs/users/serviceFabrics/Start/action|Uruchom usługi sieć szkieletowa.|
|/labs/users/serviceFabrics/Stop/action|Zatrzymaj usługi sieć szkieletowa|
|/labs/users/serviceFabrics/write|Dodaje lub modyfikuje sieci szkieletowej usług.|
|/Labs/Users/Write|Dodaje lub modyfikuje profile użytkowników.|
|/labs/virtualMachines/AddDataDisk/action|Dołączenie dysku danych nowej lub istniejącej maszyny wirtualnej.|
|/labs/virtualMachines/ApplyArtifacts/action|Zastosuj artefakty do maszyny wirtualnej.|
|/labs/virtualMachines/Claim/action|Przejmowanie na własność istniejącej maszyny wirtualnej|
|/labs/virtualMachines/delete|Usuń maszyny wirtualne.|
|/labs/virtualMachines/DetachDataDisk/action|Odłączyć określonego dysku od maszyny wirtualnej.|
|/labs/virtualMachines/ListApplicableSchedules/action|Wyświetla listę wszystkich odpowiednich harmonogramów|
|/labs/virtualMachines/read|Maszyny wirtualne do odczytu.|
|/labs/virtualMachines/Restart/action|Uruchom ponownie maszynę wirtualną.|
|/labs/virtualMachines/schedules/delete|Usuń harmonogramów.|
|/labs/virtualMachines/schedules/Execute/action|Wykonanie zgodnie z harmonogramem.|
|/labs/virtualMachines/schedules/read|Przeczytaj harmonogramów.|
|/labs/virtualMachines/schedules/write|Dodaje lub modyfikuje harmonogramów.|
|/labs/virtualMachines/Start/action|Uruchom maszynę wirtualną.|
|/labs/virtualMachines/Stop/action|Zatrzymaj maszynę wirtualną|
|/labs/virtualMachines/TransferDisks/action|Przenieś własność dysków danych maszyny wirtualnej do siebie|
|/labs/virtualMachines/UnClaim/action|Własność wersji istniejącej maszyny wirtualnej|
|/labs/virtualMachines/write|Dodaje lub modyfikuje maszyny wirtualne.|
|/labs/virtualNetworks/delete|Usuwanie sieci wirtualnych.|
|/labs/virtualNetworks/read|Przeczytaj sieci wirtualnych.|
|/labs/virtualNetworks/write|Dodaje lub modyfikuje sieci wirtualnych.|
|/ labs/zapisu|Dodaje lub modyfikuje labs.|
|/Locations/Operations/Read|Operacje odczytu.|
|/ register/działania|Rejestruje subskrypcję|
|/schedules/delete|Usuń harmonogramów.|
|/Schedules/EXECUTE/Action|Wykonanie zgodnie z harmonogramem.|
|/schedules/read|Przeczytaj harmonogramów.|
|/Schedules/Retarget/Action|Aktualizuje zasób docelowy zgodnie z harmonogramem identyfikatora.|
|/schedules/write|Dodaje lub modyfikuje harmonogramów.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Operacja | Opis |
|---|---|
|/databaseAccountNames/read|Sprawdza dostępność nazwy.|
|/databaseAccounts/changeResourceGroup/action|Zmiana grupy zasobów dla konta bazy danych|
|/databaseAccounts/databases/collections/metricDefinitions/read|Odczytuje kolekcję definicji metryk.|
|/databaseAccounts/databases/collections/metrics/read|Odczytuje metryki kolekcji.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Przeczytaj kluczowych metryk poziomu bazy danych konta partycji|
|/databaseAccounts/databases/collections/partitions/metrics/read|Przeczytaj metryki poziomu partycji konta bazy danych|
|/databaseAccounts/databases/collections/partitions/usages/read|Poziom użycia partycji do odczytu konta bazy danych|
|/databaseAccounts/databases/collections/usages/read|Odczytuje użycia kolekcji.|
|/databaseAccounts/databases/metricDefinitions/read|Odczytuje bazę danych definicji metryk|
|/databaseAccounts/databases/metrics/read|Odczytuje metryki bazy danych.|
|/databaseAccounts/databases/usages/read|Odczytuje użycia bazy danych.|
|/databaseAccounts/delete|Usuwa konta bazy danych.|
|/databaseAccounts/failoverPriorityChange/action|Zmiany priorytetów trybu failover regionów konta bazy danych. To jest używana do wykonywania operacji ręcznego przełączania trybu failover|
|/databaseAccounts/listConnectionStrings/action|Pobrać parametry połączenia dla konta bazy danych|
|/databaseAccounts/listKeys/action|Listy kluczy konta bazy danych|
|/databaseAccounts/metricDefinitions/read|Odczytuje definicje metryk konta bazy danych.|
|/databaseAccounts/metrics/read|Odczytuje metryki konta bazy danych.|
|/databaseAccounts/operationResults/read|Odczytanie stanu operacji asynchronicznych|
|/databaseAccounts/percentile/metrics/read|Metryki opóźnienie odczytu|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Odczytać metryki opóźnienie dla konkretnego regionu źródłowa i docelowa|
|/databaseAccounts/percentile/targetRegion/metrics/read|Metryki opóźnienie odczytu dla konkretnego regionu|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Pobiera catageries dostępnych dzienników dla konta bazy danych|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla bazy danych konta|
|/databaseAccounts/read|Odczytuje konta bazy danych.|
|/databaseAccounts/readonlykeys/action|Odczytuje klucze tylko do odczytu konta bazy danych.|
|/databaseAccounts/readonlykeys/read|Odczytuje klucze tylko do odczytu konta bazy danych.|
|/databaseAccounts/regenerateKey/action|Obróć klucze konta bazy danych|
|/databaseAccounts/region/databases/collections/metrics/read|Odczytuje metryki regionalnych kolekcji.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Przeczytaj kluczowych metryk poziomu regionalnych bazy danych konta partycji|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Odczytać metryki poziomu partycji konta regionalnych bazy danych|
|/databaseAccounts/region/databases/collections/partitions/read|Przeczytaj partycjach konta bazy danych w kolekcji|
|/databaseAccounts/region/metrics/read|Odczytuje metryki konta regionu i bazy danych.|
|/databaseAccounts/usages/read|Odczytuje użycia konta bazy danych.|
|/databaseAccounts/write|Aktualizacja konta bazy danych.|
|/locations/deleteVirtualNetworkOrSubnets/action|Powiadamia Microsoft.DocumentDB, że została usunięta VirtualNetwork lub podsieci|
|/operationResults/read|Odczytanie stanu operacji asynchronicznych|
|/Operations/Read|Dostępne operacje odczytu dla Microsoft DocumentDB |
|/ register/działania| Rejestrowanie dostawcy zasobów Microsoft DocumentDB dla subskrypcji|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Operacja | Opis |
|---|---|
|/checkDomainAvailability/Action|Sprawdź, czy domeny jest dostępne do zakupu|
|/ domen/Delete|Usuń istniejącą domenę.|
|/domains/domainownershipidentifiers/Delete|Usuń identyfikator własności|
|/Domains/domainownershipidentifiers/Read|Lista identyfikatorów własności|
|/Domains/domainownershipidentifiers/Read|Pobierz identyfikator własności|
|/Domains/domainownershipidentifiers/Write|Utwórz lub zaktualizuj identyfikator|
|/Domains/operationresults/Read|Operacja domeny pobierania|
|/Domains/Operations/Read|Wyświetl listę wszystkich działań z rejestracji domen usługi aplikacji|
|/ domen/odczytu|Pobierz listę domen|
|/ domen/odczytu|Pobierz domeny|
|/Domains/renew/Action|Odnów istniejącej domeny.|
|/ domen/zapisu|Dodaj nową domenę lub zaktualizuj istniejącą|
|/generateSsoRequest/Action|Generuje żądanie podpisywania domeny control Center.|
|/ listDomainRecommendations/działania|Pobieranie listy zaleceń domeny na podstawie słów kluczowych|
|/ register/działania|Rejestrowanie dostawcy zasobów Domains firmy Microsoft dla subskrypcji|
|/topLevelDomains/listAgreements/Action|Wyświetl listę akcji umowy|
|/ topLevelDomains/odczytu|Pobierz toplevel domen|
|/ topLevelDomains/odczytu|Pobierz toplevel domeny|
|/validateDomainRegistrationInformation/Action|Sprawdzanie poprawności obiektu zakupu domeny bez przesyłania|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Operacja | Opis |
|---|---|
|/lcsprojects/clouddeployments/read|Wyświetl Microsoft Dynamics AX 2012 R3 oceny wdrożenia w projekcie Microsoft Dynamics cyklu życia usług, które należą do użytkownika|
|/lcsprojects/clouddeployments/write|Utwórz Microsoft Dynamics AX 2012 R3 oceny wdrożenia w projekcie Microsoft Dynamics cyklu życia usług, które należą do użytkownika. Wdrożeń można zarządzać za pomocą portalu zarządzania Azure|
|/lcsprojects/Connectors/Read|Łączniki odczytu, które należą do usług Microsoft Dynamics cyklu życia projektu|
|/lcsprojects/Connectors/Write|Tworzenie i aktualizowanie łączniki, które należą do usług Microsoft Dynamics cyklu życia projektu|
|/lcsprojects/DELETE|Usuwanie usług programu Microsoft Dynamics cyklu życia projektów, które należą do użytkownika|
|/lcsprojects/Read|Wyświetl projekty usług Microsoft Dynamics cyklu życia, które należą do użytkownika|
|/ lcsprojects/zapisu|Tworzenie i aktualizowanie projektów usług Microsoft Dynamics cyklu życia, które należą do użytkownika. Można aktualizować tylko nazwę i opis właściwości. Nie można zaktualizować subskrypcji i lokalizacji skojarzony z projektem po utworzeniu|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Operacja | Opis |
|---|---|
|/eventSubscriptions/DELETE|Usuń eventSubscription|
|/eventSubscriptions/getFullUrl/action|Pobierz pełny adres url dla subskrypcji zdarzeń|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla subskrypcji zdarzeń|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki dla subskrypcji zdarzeń|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla eventSubscriptions|
|/eventSubscriptions/read|Przeczytaj eventSubscription|
|/ eventSubscriptions/zapisu|Utwórz lub zaktualizuj eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla — tematy|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla — tematy|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla — tematy|
|/ register/działania|Rejestruje dostawcę zasobów EventGrid eventSubscription i umożliwia tworzenie subskrypcji zdarzeń siatki.|
|/topics/DELETE|Usunięcie tematu|
|/topics/listKeys/action|Lista kluczy dla tematu|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla — tematy|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla — tematy|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla — tematy|
|/topics/read|Przeczytaj temat|
|/topics/regenerateKey/action|Regenerate klucz dla tematu|
|/ Tematy/zapisu|Utwórz lub zaktualizuj tematu|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Operacja | Opis |
|---|---|
|/checkNameAvailability/action|Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji.|
|/checkNamespaceAvailability/action|Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailabiltiy.|
|/Namespaces/authorizationRules/Action|Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest depricated. Można zaktualizować reguły autoryzacji Namespace zamiast tego użyj wywołania PUT. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/authorizationRules/DELETE|Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej. |
|/Namespaces/authorizationRules/listkeys/Action|Pobierz parametry połączenia z przestrzenią nazw|
|/Namespaces/authorizationRules/Read|Pobierz listę opisów reguł autoryzacji przestrzeni nazw.|
|/namespaces/authorizationRules/regenerateKeys/action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/Namespaces/authorizationRules/Write|Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze.|
|przestrzenie nazw/Delete|Usuń zasób przestrzeni nazw|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Pobiera autoryzacja klucze reguły dla odzyskiwania po awarii podstawowej przestrzeni nazw|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Pobierz awaryjnego odzyskiwania podstawowej Namespace dla reguły autoryzacji|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Sprawdzanie dostępności aliasu przestrzeni nazw w podanej subskrypcji.|
|/namespaces/disasterRecoveryConfigs/delete|Usuwa konfiguracji odzyskiwania po awarii, skojarzone z przestrzenią nazw. Tę operację można wywołać tylko za pomocą głównej przestrzeni nazw.|
|/namespaces/disasterRecoveryConfigs/failover/action|Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw.|
|/namespaces/disasterRecoveryConfigs/read|Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw.|
|/namespaces/disasterRecoveryConfigs/write|Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw.|
|/namespaces/eventhubs/authorizationRules/action|Operacja aktualizowania EventHub. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji.|
|/namespaces/eventhubs/authorizationRules/delete|Operację można usunąć reguły autoryzacji EventHub|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Pobierz ciąg połączenia EventHub|
|/namespaces/eventhubs/authorizationRules/read| Pobierz listę reguł autoryzacji EventHub|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/namespaces/eventhubs/authorizationRules/write|Tworzenie reguł autoryzacji EventHub i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia.|
|/namespaces/eventHubs/consumergroups/Delete|Operację można usunąć grupy konsumentów zasobów|
|/namespaces/eventHubs/consumergroups/read|Pobierz listę opisów zasobów grupy konsumentów|
|/namespaces/eventHubs/consumergroups/write|Utwórz lub właściwości grupy konsumentów aktualizacji.|
|/namespaces/eventhubs/Delete|Operacja usunąć EventHub zasobu|
|/namespaces/eventhubs/read|Pobierz listę opisów zasobów EventHub|
|/namespaces/eventhubs/write|Tworzenie lub aktualizacja EventHub właściwości.|
|/Namespaces/messagingPlan/Read|Pobiera Plan obsługi komunikatów dla przestrzeni nazw. Ten interfejs API jest przestarzały. Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego). Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/messagingPlan/Write|Aktualizuje wiadomości Plan dla przestrzeni nazw. Ten interfejs API jest przestarzały. Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego). Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/operationresults/Read|Pobierz stan operacji przestrzeni nazw|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Pobierz listę Namespace ustawień diagnostycznych zasobu opisów|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Pobierz listę Namespace ustawień diagnostycznych zasobu opisów|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Pobierz listę dzienników Namespace opisów zasobów|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Pobierz listę metryki Namespace opisów zasobów|
|/Namespaces/Read|Pobierz listę opisów zasobów przestrzeni nazw|
|/ przestrzenie nazw/zapisu|Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane.|
|/Operations/Read|Pobierz operacje|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów usługi EventHub i włącza funkcję tworzenia zasobów usługi EventHub|
|/SKU/Read|Pobierz listę opisów zasobów jednostki Sku|
|/SKU/regions/Read|Pobierz listę opisów zasobów SkuRegions|
|/ unregister/działania|Rejestruje dostawcę zasobów usługi EventHub|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Operacja | Opis |
|---|---|
|/features/read|Pobiera funkcje subskrypcji.|
|/Providers/Features/Read|Pobiera funkcję subskrypcji danego dostawcy zasobów.|
|/Providers/Features/Register/Action|Rejestruje funkcję subskrypcji danego dostawcy zasobów.|
|/Providers/Features/unregister/Action|Wyrejestrowuje funkcję subskrypcji danego dostawcy zasobów.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Operacja | Opis |
|---|---|
|/Clusters/changerdpsetting/Action|Zmień ustawienie protokołu RDP dla klastra usługi HDInsight|
|/Clusters/Configurations/Action|Zaktualizuj konfigurację klastra usługi HDInsight|
|/Clusters/Configurations/Read|Pobierz konfiguracje klastrów usługi HDInsight|
|/Clusters/DELETE|Usuwanie klastra usługi HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu klastra usługi HDInsight|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu klastra usługi HDInsight|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla klastra usługi HDInsight|
|/Clusters/Read|Uzyskiwanie szczegółowych informacji dotyczących klastra usługi HDInsight|
|/Clusters/Roles/Resize/Action|Zmień rozmiar klastra usługi HDInsight|
|/ klastrów/zapisu|Utwórz lub zaktualizuj klastra usługi HDInsight|
|/Locations/Capabilities/Read|Pobrać możliwości subskrypcji|
|/locations/checkNameAvailability/read|Sprawdź dostępność nazwy|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Operacja | Opis |
|---|---|
|/jobs/delete|Usuwa istniejące zadanie.|
|/jobs/listBitLockerKeys/action|Pobiera klucze funkcji BitLocker dla określonego zadania.|
|/jobs/read|Pobiera właściwości dla określonego zadania lub zwraca listę zadań.|
|/ zadania/zapisu|Tworzy zadanie z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego zadania.|
|/Locations/Read|Pobiera właściwości dla określonej lokalizacji lub zwraca listę lokalizacji.|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów importu/eksportu i umożliwia tworzenie zadania importu/eksportu.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Operacja | Opis |
|---|---|
|/ActionGroups/Delete|Usuwanie grupy akcji|
|/ActionGroups/Read|Odczytywanie grupy akcji|
|/ActionGroups/Write|Zapisywanie grupy akcji|
|/ActivityLogAlerts/Activated/Action|Wyzwolony alert dziennika aktywności|
|/ActivityLogAlerts/Delete|Usuwanie alertu dziennika aktywności|
|/ActivityLogAlerts/Read|Odczytywanie alertu dziennika aktywności|
|/ActivityLogAlerts/Write|Odczytywanie alertu dziennika aktywności|
|/AlertRules/Activated/Action|Aktywowana reguła alertu|
|/AlertRules/Delete|Usuwanie konfiguracji reguły alertu|
|/AlertRules/Incidents/Read|Odczytywanie konfiguracji zdarzenia reguły alertu|
|/AlertRules/Read|Odczytywanie konfiguracji reguły alertu|
|/AlertRules/Resolved/Action|Rozwiązana reguła alertu|
|/AlertRules/Throttled/Action|Reguła alertu jest ograniczana|
|/AlertRules/Write|Zapisywanie w konfiguracji reguły alertu|
|/AutoscaleSettings/Delete|Usuwanie konfiguracji ustawienia autoskalowania|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Przeczytaj definicje metryk|
|/AutoscaleSettings/Read|Odczytywanie konfiguracji ustawienia autoskalowania|
|/AutoscaleSettings/Scaledown/Action|Autoskalowanie — operacja skalowania w dół|
|/AutoscaleSettings/Scaleup/Action|Autoskalowanie — operacja skalowania w górę|
|/AutoscaleSettings/Write|Zapisywanie w konfiguracji ustawienia autoskalowania|
|/Components/AnalyticsItems/Delete|Usunięcie elementu analytics usługi Application Insights|
|/Components/AnalyticsItems/Read|Odczytywanie elementu analytics usługi Application Insights|
|/Components/AnalyticsItems/Write|Zapisywanie elementu analytics usługi Application Insights|
|/Components/AnalyticsTables/Action|Application Insights analytics tabeli akcji|
|/Components/AnalyticsTables/Delete|Usuwanie usługi Application Insights analytics tabeli schematu|
|/Components/AnalyticsTables/Read|Odczytywanie usługi Application Insights analytics tabeli schematu|
|/Components/AnalyticsTables/Write|Pisanie usługi Application Insights analytics tabeli schematu|
|/ Składniki/adnotacje/usuwania|Usuwanie adnotacji usługi Application Insights|
|Składniki/adnotacje/odczytu|Odczytywanie adnotacji usługi Application Insights|
|Składniki/adnotacje/zapisu|Zapisywanie adnotacji usługi Application Insights|
|/Components/Api/Read|Odczytywanie danych składnika usługi Application Insights interfejsu API|
|/ Składniki/ApiKeys/działania|Generowanie klucza interfejsu API usługi Application Insights|
|/ Składniki/ApiKeys/usuwania|Usuwanie klucza interfejsu API usługi Application Insights|
|Składniki/ApiKeys/odczytu|Odczytywanie klucza interfejsu API usługi Application Insights|
|Składniki/BillingPlanForComponent/odczytu|Odczytywanie plan rozliczeniowy dla składnika usługi Application Insights|
|/Components/CurrentBillingFeatures/Read|Odczytywanie bieżące funkcje rozliczeń dla składnika usługi Application Insights|
|/Components/CurrentBillingFeatures/Write|Pisanie bieżące funkcje rozliczeń dla składnika usługi Application Insights|
|/Components/DefaultWorkItemConfig/Read|Odczytywanie konfigurację integracji usługi Application Insights domyślne ALM|
|/ Składniki/usuwania|Usuwanie konfiguracji składnika usługi Application Insights|
|/ Składniki/ExportConfiguration/działania|Usługa Application Insights wyeksportować ustawienia akcji|
|/Components/ExportConfiguration/Delete|Trwa usuwanie usługi Application Insights wyeksportować ustawienia|
|Składniki/ExportConfiguration/odczytu|Odczytywanie Application Insights wyeksportować ustawienia|
|Składniki/ExportConfiguration/zapisu|Zapisywanie Application Insights wyeksportować ustawienia|
|Składniki/ExtendQueries/odczytu|Składnik usługi Application Insights odczytu rozszerzony wyników zapytania|
|/ Składniki/Ulubione/usuwania|Usuwanie ulubionego elementu w usłudze Application Insights|
|Składniki/Ulubione/odczytu|Odczyt ulubionych usługi Application Insights|
|Składniki/Ulubione/zapisu|Zapisywanie ulubionego elementu w usłudze Application Insights|
|Składniki/FeatureCapabilities/odczytu|Możliwości funkcji składnika odczytu Application Insights|
|/Components/GetAvailableBillingFeatures/Read|Odczytywanie Application Insights składników dostępnych funkcji rozliczeń|
|/Components/GetToken/Read|Odczytywanie tokenu składnika usługi Application Insights|
|/ Składniki/ListMigrationDate/działania|Data migracji subskrypcji wstecz Get|
|Składniki/ListMigrationDate/odczytu|Data migracji subskrypcji wstecz Get|
|/Components/MetricDefinitions/Read|Odczyt definicji metryk składnika usługi Application Insights|
|/Components/Metrics/Read|Metryki składnika odczytu Application Insights|
|/Components/MigrateToNewpricingModel/Action|Migrowanie subskrypcji do nowego modelu cenowego|
|/ Składniki/Move/działania|Przenieś składnika aplikacji wgląd do innej grupy zasobów lub subskrypcji|
|/Components/MyAnalyticsItems/Delete|Usunięcie elementu osobiste analytics usługi Application Insights|
|/Components/MyAnalyticsItems/Read|Odczytywanie elementu osobiste analytics usługi Application Insights|
|/Components/MyAnalyticsItems/Write|Zapisywanie elementu osobiste analytics usługi Application Insights|
|Składniki/Moje/odczytu|Odczyt ulubionych osobiste usługi Application Insights|
|Składniki/PricingPlans/odczytu|Odczytywanie składnika usługi Application Insights wyceny planu|
|Składniki/PricingPlans/zapisu|Zapisywanie składnika usługi Application Insights wyceny planu|
|/Components/ProactiveDetectionConfigs/Read|Konfiguracja aktywnego wykrywania odczytu Application Insights|
|/Components/ProactiveDetectionConfigs/Write|Zapisywanie konfiguracji aktywnego wykrywania usługi Application Insights|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Przeczytaj definicje metryk|
|Składniki/QuotaStatus/odczytu|Odczyt stanu przydziału składników usługi Application Insights|
|Składniki/odczytu|Odczytywanie konfiguracji składnika usługi Application Insights|
|/Components/RollbackToLegacyPricingModel/Action|Subskrypcja wycofywania starszych model cenowy|
|/Components/SyntheticMonitorLocations/Read|Lokalizacje webtest odczytu Application Insights|
|/ Składniki/WorkItemConfigs/usuwania|Usuwanie konfiguracji integracji Application Insights ALM|
|Składniki/WorkItemConfigs/odczytu|Odczytywanie konfiguracji integracji Application Insights ALM|
|Składniki/WorkItemConfigs/zapisu|Zapisywanie konfiguracji integracji Application Insights ALM|
|Składniki/zapisu|Zapisywanie w konfiguracji składnika usługi Application Insights|
|/DiagnosticSettings/Delete|Usuwanie konfiguracji ustawień diagnostycznych|
|/DiagnosticSettings/Read|Odczytywanie konfiguracji ustawień diagnostycznych|
|/DiagnosticSettings/Write|Zapisywanie w konfiguracji ustawień diagnostycznych|
|/EventCategories/Read|Odczytywanie kategorii zdarzenia|
|/eventtypes/digestevents/Read|Typ zdarzeń zarządzania — odczytaj podsumowanie|
|/eventtypes/VALUES/Read|Typ zdarzeń zarządzania — odczytaj wartości|
|/ExtendedDiagnosticSettings/Delete|Usuwanie konfiguracji ustawień diagnostyki rozszerzonej|
|/ExtendedDiagnosticSettings/Read|Odczytywanie konfiguracji ustawień diagnostyki rozszerzonej|
|/ExtendedDiagnosticSettings/Write|Zapisywanie w konfiguracji ustawień diagnostyki rozszerzonej|
|LogDefinitions/odczytu|Przeczytaj definicje dzienników|
|/LogProfiles/Delete|Usuń konfigurację profilów dzienników|
|/LogProfiles/Read|Odczyt profilów dzienników|
|/LogProfiles/Write|Zapisywanie w konfiguracji profilu dziennika|
|/MetricAlerts/Delete|Usuwanie alertu metryki|
|/MetricAlerts/Read|Odczytywanie alertu metryki|
|/MetricAlerts/Write|Zapisywanie alertu metryki|
|/MetricDefinitions/Microsoft.Insights/Read|Przeczytaj definicje metryk|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Przeczytaj definicje metryk|
|/MetricDefinitions/Read|Przeczytaj definicje metryk|
|/Metrics/providers/Metrics/Read|Odczytaj metryki|
|/Metrics/Read|Odczytaj metryki|
|/Metrics/Write|Zapis metryk|
|Operacje/odczytu|Odczytywanie operacji|
|/ Register/działania|Zarejestruj dostawcę usługi Microsoft Insights|
|/ Dzierżaw/Register/działania|Inicjuje dostawcę usługi Microsoft Insights|
|/ Unregister/działania|Zarejestruj dostawcę usługi Microsoft Insights|
|/ Webtests/usuwania|Usuwanie konfiguracji testu internetowego|
|/Webtests/GetToken/Read|Odczytywanie tokenu webtest|
|/Webtests/MetricDefinitions/Read|Odczyt definicji metryk webtest|
|/Webtests/Metrics/Read|Odczytywanie metryki webtest|
|/Webtests/Read|Odczytywanie konfiguracji testu internetowego|
|/Webtests/Write|Zapisywanie w konfiguracji testu internetowego|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Operacja | Opis |
|---|---|
|/checkNameAvailability/read|Sprawdza, czy nazwa magazynu kluczy jest prawidłowa oraz czy została już użyta|
|/deletedVaults/read|Wyświetl właściwości usuniętych nietrwale magazynów kluczy|
|/hsmPools/delete|Usuń pulę modułów HSM|
|/hsmPools/joinVault/action|Dołącz magazyn kluczy do puli modułów HSM|
|/hsmPools/read|Wyświetl właściwości puli modułów HSM|
|/hsmPools/write|Utwórz nową pulę modułów HSM lub zaktualizuj właściwości istniejącej puli modułów HSM|
|/Locations/deletedVaults/PURGE/Action|Przeczyść usunięty nietrwale magazyn kluczy|
|/Locations/deletedVaults/Read|Wyświetl właściwości usuniętego nietrwale magazynu kluczy|
|/locations/deleteVirtualNetworkOrSubnets/action|Powiadamia element Microsoft.KeyVault o usuwaniu podsieci lub sieci wirtualnej|
|/Locations/operationResults/Read|Sprawdź wynik operacji długiego uruchamiania|
|/Operations/Read|Wyświetla operacje dostępne dla dostawcy zasobów Microsoft.KeyVault|
|/ register/działania|Rejestruje subskrypcję|
|/ unregister/działania|Wyrejestrowuje subskrypcję|
|/vaults/accessPolicies/write|Aktualizowanie istniejących zasad dostępu przez scalanie lub zastępowanie albo dodawanie nowych zasad dostępu do magazynu.|
|/vaults/delete|Usuń magazyn kluczy|
|/vaults/deploy/action|Zapewnia dostęp do wpisów tajnych w magazynie kluczy podczas wdrażania zasobów platformy Azure|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Pobiera ustawienie diagnostyczne dla zasobu|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla magazynu kluczy|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla magazynu kluczy|
|/vaults/read|Wyświetl właściwości magazynu kluczy|
|/vaults/secrets/read|Wyświetl właściwości wpisu tajnego, ale nie jego wartość|
|/vaults/secrets/Write|Utwórz nowy wpis tajny lub zaktualizuj wartość istniejącego wpisu tajnego|
|/vaults/write|Utwórz nowy magazyn kluczy lub zaktualizuj właściwości istniejącego magazynu kluczy|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Operacja | Opis |
|---|---|
|/labAccounts/CreateLab/action|Tworzenie laboratorium w ramach konta laboratorium.|
|/labAccounts/delete|Usuwanie kont laboratorium.|
|/labAccounts/labs/delete|Usuń labs.|
|/labAccounts/labs/environmentSettings/delete|Usuń ustawienie środowiska.|
|/labAccounts/labs/environmentSettings/environments/delete|Usuwanie środowiska.|
|/labAccounts/labs/environmentSettings/environments/read|Przeczytaj środowisk.|
|/labAccounts/labs/environmentSettings/environments/write|Dodaje lub modyfikuje środowisk.|
|/labAccounts/labs/environmentSettings/Publish/action|Przepisy/deprovisions wymaganych zasobów dla ustawienia środowiska na podstawie bieżącego stanu ustawienia/w środowisku laboratoryjnym.|
|/labAccounts/labs/environmentSettings/read|Ustawienie środowiska odczytu.|
|/labAccounts/labs/environmentSettings/write|Dodaje lub modyfikuje ustawienia środowiska.|
|/labAccounts/labs/read|Laboratoria odczytu.|
|/labAccounts/labs/users/delete|Usuwanie użytkowników.|
|/labAccounts/labs/users/read|Do odczytu użytkowników.|
|/labAccounts/labs/users/write|Dodaje lub modyfikuje użytkowników.|
|/labAccounts/labs/write|Dodaje lub modyfikuje labs.|
|/labAccounts/read|Odczytywanie kont laboratorium.|
|/labAccounts/write|Dodaje lub modyfikuje konta laboratorium.|
|/Locations/Operations/Read|Operacje odczytu.|
|/ register/działania|Rejestruje subskrypcję|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Operacja | Opis |
|---|---|
|/accounts/DELETE|Usuń lokalizacji na podstawie konto usług.|
|/accounts/listKeys/action|Klucze konta usługi na podstawie lokalizacji listy|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla kont usług na podstawie lokalizacji|
|/accounts/Read|Uzyskiwanie lokalizacji na podstawie konto usług.|
|/accounts/regenerateKey/Action|Generuj nowy klucz podstawowy lub pomocniczy konta usługi na podstawie lokalizacji|
|/ kont/zapisu|Utwórz lub zaktualizuj konto usługi na podstawie lokalizacji.|
|/ register/działania|Zarejestruj dostawcę|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Operacja | Opis |
|---|---|
|/integrationAccounts/agreements/delete|Usuwa umowy konta integracji.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Pobiera adres URL wywołania zwrotnego do treści umowy konta integracji.|
|/integrationAccounts/agreements/read|Odczytuje umowy konta integracji.|
|/integrationAccounts/agreements/write|Tworzy lub aktualizuje umowy konta integracji.|
|/integrationAccounts/assemblies/delete|Usuwa zestaw konta integracji.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla konta integracji zestawu zawartości.|
|/integrationAccounts/assemblies/read|Odczytuje zestaw konta integracji.|
|/integrationAccounts/assemblies/write|Tworzy lub aktualizuje zestawu na koncie integracji.|
|/integrationAccounts/batchConfigurations/delete|Usuwa konfigurację partii na koncie integracji.|
|/integrationAccounts/batchConfigurations/read|Odczytuje konfigurację partii na koncie integracji.|
|/integrationAccounts/batchConfigurations/write|Tworzy lub aktualizuje konfigurację partii na koncie integracji.|
|/integrationAccounts/certificates/delete|Usuwa certyfikat konta integracji.|
|/integrationAccounts/certificates/read|Odczytuje certyfikatu konta integracji.|
|/integrationAccounts/certificates/write|Tworzy lub aktualizuje certyfikat konta integracji.|
|/integrationAccounts/delete|Usuwa konto integracji.|
|/integrationAccounts/listCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla konta integracji.|
|/integrationAccounts/listKeyVaultKeys/action|Pobiera klucze w magazynie kluczy.|
|/integrationAccounts/logTrackingEvents/action|Dzienniki zdarzeń śledzenia w ramach konta integracji.|
|/integrationAccounts/maps/delete|Usuwa mapy na koncie integracji.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla zawartości mapy na koncie integracji.|
|/integrationAccounts/maps/read|Odczytuje mapy na koncie integracji.|
|/integrationAccounts/maps/write|Tworzy lub aktualizuje mapy na koncie integracji.|
|/integrationAccounts/partners/delete|Usuwa partnera w konta integracji.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla partnera zawartości na koncie integracji.|
|/integrationAccounts/partners/read|Odczytuje wspólnik konta integracji.|
|/integrationAccounts/partners/write|Tworzy lub aktualizuje partnera w konta integracji.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Odczytuje definicje dziennika konta integracji.|
|/integrationAccounts/read|Odczytuje konta integracji.|
|/integrationAccounts/regenerateAccessKey/action|Generuje ponownie wpisy tajne klucza dostępu.|
|/integrationAccounts/schemas/delete|Usuwa schematu na koncie integracji.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla schematu zawartości na koncie integracji.|
|/integrationAccounts/schemas/read|Odczytuje schematu na koncie integracji.|
|/integrationAccounts/schemas/write|Tworzy lub aktualizuje schematu na koncie integracji.|
|/integrationAccounts/sessions/delete|Usuwa sesji na koncie integracji.|
|/integrationAccounts/sessions/read|Odczytuje konfigurację partii na koncie integracji.|
|/integrationAccounts/sessions/write|Tworzy lub aktualizuje sesji na koncie integracji.|
|/integrationAccounts/write|Tworzy lub aktualizuje konto integracji.|
|/Locations/Workflows/Validate/Action|Sprawdza poprawność przepływu pracy.|
|/Operations/Read|Pobiera operację.|
|/ register/działania|Rejestruje dostawcę zasobów Microsoft.Logic dla danej subskrypcji.|
|/Workflows/accessKeys/DELETE|Usuwa klucz dostępu.|
|/Workflows/accessKeys/list/Action|Wyświetla wpisy tajne klucza dostępu.|
|/Workflows/accessKeys/Read|Odczytuje klucz dostępu.|
|/Workflows/accessKeys/regenerate/Action|Generuje ponownie wpisy tajne klucza dostępu.|
|/Workflows/accessKeys/Write|Tworzy lub aktualizuje klucz dostępu.|
|/Workflows/DELETE|Usuwa przepływ pracy.|
|/Workflows/disable/Action|Wyłącza przepływ pracy.|
|/Workflows/enable/Action|Włącza przepływ pracy.|
|/workflows/listCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla przepływu pracy.|
|/workflows/listSwagger/action|Pobiera definicje struktury Swagger przepływu pracy.|
|/Workflows/Move/Action|Przenosi przepływ pracy z jego obecnego identyfikatora subskrypcji, grupy zasobów i/lub nazwy do innego identyfikatora subskrypcji, grupy zasobów i/lub nazwy.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Odczytuje ustawienia diagnostyki przepływu pracy.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub modyfikuje ustawienie diagnostyki przepływu pracy.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Odczytuje definicje dzienników przepływu pracy.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Odczytuje definicje metryk przepływu pracy.|
|/Workflows/Read|Odczytuje przepływ pracy.|
|/workflows/regenerateAccessKey/action|Generuje ponownie wpisy tajne klucza dostępu.|
|/Workflows/Run/Action|Uruchamia przebieg przepływu pracy.|
|/workflows/runs/actions/listExpressionTraces/action|Pobiera ślady wyrażenia akcji uruchamiania przepływu pracy.|
|/Workflows/runs/Actions/Read|Odczytuje akcję przebiegu przepływu pracy.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Pobiera ślady wyrażenia powtórzenia akcji przebiegu przepływu pracy.|
|/Workflows/runs/Actions/repetitions/Read|Odczytuje powtórzenie akcji przebiegu przepływu pracy.|
|/Workflows/runs/Actions/scoperepetitions/Read|Odczytuje powtórzenie zakresu akcji przebiegu przepływu pracy.|
|/Workflows/runs/Cancel/Action|Anuluje przebieg przepływu pracy.|
|/Workflows/runs/Operations/Read|Odczytuje stan operacji przebiegu przepływu pracy.|
|przepływy pracy/uruchamia/Odczyt|Odczytuje przebieg przepływu pracy.|
|/Workflows/suspend/Action|Wstrzymuje przepływ pracy.|
|/Workflows/Triggers/histories/Read|Odczytuje historie wyzwalacza.|
|/Workflows/Triggers/histories/resubmit/Action|Przesyła ponownie wyzwalacz przepływu pracy.|
|/workflows/triggers/listCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla wyzwalacza.|
|/Workflows/Triggers/Read|Odczytuje wyzwalacz.|
|/Workflows/Triggers/reset/Action|Resetuje wyzwalacza.|
|/Workflows/Triggers/Run/Action|Uruchamia wyzwalacz.|
|/Workflows/Triggers/setState/Action|Ustawia stan wyzwalacza.|
|/Workflows/Validate/Action|Sprawdza poprawność przepływu pracy.|
|/Workflows/Versions/Read|Odczytuje wersję przepływu pracy.|
|/workflows/versions/triggers/listCallbackUrl/action|Pobiera adres URL wywołania zwrotnego dla wyzwalacza.|
|/ przepływy pracy/zapisu|Tworzy lub modyfikuje przepływ pracy.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Operacja | Opis |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Przenieś wszystkie Machine Learning skojarzenia planu zobowiązań|
|/commitmentPlans/commitmentAssociations/read|Przeczytaj maszyn zobowiązań skojarzenia Plan nauki|
|/commitmentPlans/DELETE|Usuń wszystkie maszyny zobowiązań Plan nauki|
|/commitmentPlans/JOIN/Action|Dołącz do dowolnej maszyny zobowiązań Plan nauki|
|/commitmentPlans/Read|Przeczytaj maszyn zobowiązań Plan nauki|
|/ commitmentPlans/zapisu|Utwórz lub zaktualizuj żadnych zobowiązań Plan nauki maszyny|
|/Locations/operationresults/Read|Pobierz wynik operacji Machine Learning|
|/Locations/operationsstatus/Read|Pobierz stan trwającą operacją Machine Learning|
|/Operations/Read|Pobierz operacje uczenia maszynowego|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów usługi sieci web uczenia maszynowego i umożliwia tworzenie usług sieci web.|
|/skus/read|Pobierz jednostki SKU planu zobowiązań uczenia maszynowego|
|/webServices/action|Utwórz regionalnych właściwości obsługiwanych regionów usługi sieci Web|
|/webServices/delete|Usuń usługi sieci Web Machine Learning|
|/webServices/read|Usługi sieci Web Machine Learning do odczytu|
|/webServices/write|Utwórz lub zaktualizuj usługi sieci Web Machine Learning|
|/ Workspaces/usuwania|Usuń wszelkie Machine Learning obszaru roboczego|
|/Workspaces/listworkspacekeys/action|Lista kluczy dla obszaru roboczego Machine Learning|
|Obszary robocze/odczytu|Wszelkie Machine Learning obszaru roboczego do odczytu|
|/Workspaces/resyncstoragekeys/action|Zsynchronizowanie klucze konta magazynu skonfigurowanego dla obszaru roboczego Machine Learning|
|/ Workspaces/zapisu|Utwórz lub zaktualizuj wszystkie Machine Learning obszaru roboczego|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Operacja | Opis |
|---|---|
|/operationalizationClusters/checkUpdate/action|Sprawdź, czy aktualizacje są dostępne dla systemu usług dla klastra operationalization|
|/operationalizationClusters/delete|Usuń dowolne konto hostingu|
|/operationalizationClusters/listKeys/action|Utwórz listę kluczy skojarzony z klastrem operationalization|
|/operationalizationClusters/read|Przeczytaj dowolne konto hostingu|
|/operationalizationClusters/updateSystem/action|Aktualizacja usługi systemowe w klastrze operationalization|
|/operationalizationClusters/write|Utwórz lub zaktualizuj dowolne konto hostingu|
|/ register/działania|Rejestruje identyfikator subskrypcji dostawcy zasobów i umożliwia tworzenie zasoby obliczeniowe uczenia maszynowego|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Operacja | Opis |
|---|---|
|/accounts/DELETE|Usuń dowolne konto hostingu|
|/accounts/Read|Przeczytaj dowolne konto hostingu|
|/ kont/zapisu|Utwórz lub zaktualizuj dowolne konto hostingu|
|/ register/działania|Rejestruje identyfikator subskrypcji dostawcy zasobów i umożliwia tworzenie konta hostingu|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Operacja | Opis |
|---|---|
|/userAssignedIdentities/assign/action|Akcja RBAC przypisywania istniejącego użytkownika do zasobu przydzielono tożsamości|
|/userAssignedIdentities/delete|Usuwa istniejącego użytkownika przypisane tożsamości|
|/userAssignedIdentities/read|Pobiera istniejącego użytkownika przypisane tożsamości|
|/userAssignedIdentities/write|Tworzy nowego użytkownika przypisane tożsamości lub aktualizuje do tagów skojarzonych z istniejącego użytkownika przypisane tożsamości|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Operacja | Opis |
|---|---|
|/labAccounts/CreateLab/action|Tworzenie laboratorium w ramach konta laboratorium.|
|/labAccounts/delete|Usuwanie kont laboratorium.|
|/labAccounts/labs/delete|Usuń labs.|
|/labAccounts/labs/environmentSettings/delete|Usuń ustawienie środowiska.|
|/labAccounts/labs/environmentSettings/environments/delete|Usuwanie środowiska.|
|/labAccounts/labs/environmentSettings/environments/read|Przeczytaj środowisk.|
|/labAccounts/labs/environmentSettings/environments/write|Dodaje lub modyfikuje środowisk.|
|/labAccounts/labs/environmentSettings/read|Ustawienie środowiska odczytu.|
|/labAccounts/labs/environmentSettings/write|Dodaje lub modyfikuje ustawienia środowiska.|
|/labAccounts/labs/labVms/delete|Usuń maszyny wirtualne laboratorium.|
|/labAccounts/labs/labVms/read|Przeczytaj maszyn wirtualnych laboratorium.|
|/labAccounts/labs/labVms/write|Dodaje lub modyfikuje maszyny wirtualne laboratorium.|
|/labAccounts/labs/read|Laboratoria odczytu.|
|/labAccounts/labs/write|Dodaje lub modyfikuje labs.|
|/labAccounts/read|Odczytywanie kont laboratorium.|
|/labAccounts/write|Dodaje lub modyfikuje konta laboratorium.|
|/Locations/Operations/Read|Operacje odczytu.|
|/ register/działania|Rejestruje subskrypcję|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Operacja | Opis |
|---|---|
|/checkNameAvailability/action|Sprawdza, czy nazwa grupy zarządzania określony jest prawidłowa i unikatowe.|
|/getEntities/action|Wyświetl listę wszystkich jednostek (grup zarządzania, subskrypcje, itp.) dla tego uwierzytelnionego użytkownika.|
|/managementGroups/delete|Usuwanie grupy zarządzania.|
|/managementGroups/read|Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika.|
|/managementGroups/subscriptions/delete|Kojarzy usuwania subskrypcji z grupy zarządzania.|
|/managementGroups/subscriptions/write|Stowarzyszonych istniejącej subskrypcji z grupą zarządzania.|
|/managementGroups/write|Utwórz lub zaktualizuj grupy zarządzania.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Operacja | Opis |
|---|---|
|/ClassicDevServices/delete|Wykonuje operację usuwania zasobu usługi klasycznego deweloperów.|
|/ClassicDevServices/listSecrets/action|Pobiera klucze zarządzania zasobów usługi klasycznego deweloperów.|
|/ClassicDevServices/listSingleSignOnToken/action|Pobiera pojedynczy znak na adres URL dla usługi klasycznego deweloperów.|
|/ClassicDevServices/read|Wykonuje operację GET w usłudze klasycznego deweloperów.|
|/ClassicDevServices/regenerateKey/action|Generuje klucze zarządzania zasobów klasycznych deweloperów usługi.|
|Operacje/odczytu|Operacje dla wszystkich typów zasobów odczytu.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Operacja | Opis |
|---|---|
|/agreements/offers/plans/cancel/action|Anuluj umowę dla elementu danej witryny marketplace|
|/agreements/offers/plans/read|Zwraca umowę dla elementu danej witryny marketplace|
|/agreements/offers/plans/sign/action|Podpisać umowę dla elementu danej witryny marketplace|
|/agreements/Read|Zwróć wszystkie umowy w podanej subskrypcji|
|/offertypes/publishers/offers/plans/agreements/read|Pobierz umowę dla elementu marketplace danej maszyny wirtualnej|
|/offertypes/publishers/offers/plans/agreements/write|Zaloguj się, lub Anuluj umowę dla elementu marketplace danej maszyny wirtualnej|

## <a name="microsoftmedia"></a>Microsoft.Media

| Operacja | Opis |
|---|---|
|/ checknameavailability/działania|Sprawdza, czy nazwa konta usługi Media Services jest dostępna|
|/mediaservices/delete|Usuń wszystkie konta usługi Media Services|
|/mediaservices/listKeys/action|Utwórz listę kluczy ACS dla konta usługi Media Services|
|/mediaservices/read|Wszystkie konta usługi Media Services do odczytu|
|/mediaservices/regenerateKey/action|Ponowne wygenerowanie klucza nośnika usług ACS|
|/mediaservices/syncStorageKeys/action|Synchronizowanie klucza magazynu dołączonego konta usługi Azure Storage|
|/mediaservices/write|Utwórz lub zaktualizuj wszystkie konta usługi Media Services|
|/Operations/Read|Wszystkie konta usługi Media Services do odczytu|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów usługi Media Services i umożliwia tworzenie konta usługi Media Services|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Operacja | Opis |
|---|---|
|Operacje/odczytu|Odczytuje ujawnione operacje|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Operacja | Opis |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Ssl bramy aplikacji wstępnie zdefiniowane zasady|
|/applicationGatewayAvailableSslOptions/read|Dostępne opcje Ssl bramy aplikacji|
|/applicationGatewayAvailableWafRuleSets/read|Pobiera bramę aplikacji, ustawia dostępne reguły zapory aplikacji sieci Web|
|/applicationGateways/backendAddressPools/join/action|Dołącza do puli adresów zaplecza bramy aplikacji|
|/applicationGateways/backendhealth/action|Pobiera kondycji zaplecza bramy aplikacji|
|/applicationGateways/delete|Usuwa bramę aplikacji|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Pobierz tabelę tras skonfigurowany dla bramy aplikacji|
|/applicationGateways/effectiveRouteTable/action|Pobierz tabelę tras skonfigurowany dla bramy aplikacji|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Pobiera zdarzenia bramy aplikacji|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki bramy aplikacji|
|/applicationGateways/read|Pobiera bramę aplikacji|
|/applicationGateways/setSecurityCenterConfiguration/action|Centrum zabezpieczeń bramy aplikacji zestawów konfiguracji|
|/applicationGateways/start/action|Uruchamia bramy aplikacji|
|/applicationGateways/stop/action|Zatrzymuje bramy aplikacji|
|/applicationGateways/write|Tworzy bramę aplikacji lub aktualizuje istniejącą bramę aplikacji|
|/applicationSecurityGroups/delete|Usuwa grupę zabezpieczeń aplikacji|
|/applicationSecurityGroups/joinIpConfiguration/action|Tworzy sprzężenie konfigurację protokołu IP dla grup zabezpieczeń aplikacji.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Tworzy sprzężenie reguły zabezpieczeń dla grup zabezpieczeń aplikacji.|
|/applicationSecurityGroups/read|Pobiera identyfikator grupy zabezpieczeń aplikacji.|
|/applicationSecurityGroups/write|Tworzy grupę zabezpieczeń aplikacji, lub aktualizuje istniejącą grupę zabezpieczeń aplikacji.|
|/bgpServiceCommunities/read|Pobierz społeczności usługi protokołu Bgp|
|/checkTrafficManagerNameAvailability/action|Sprawdza dostępność nazwy DNS względem usługi Traffic Manager.|
|/Connections/DELETE|Deletes VirtualNetworkGatewayConnection|
|/Connections/Read|Gets VirtualNetworkGatewayConnection|
|/Connections/sharedkey/Action|Pobierz element VirtualNetworkGatewayConnection SharedKey|
|/Connections/sharedKey/Read|Gets VirtualNetworkGatewayConnection SharedKey|
|/Connections/sharedKey/Write|Tworzy lub aktualizuje istniejącą SharedKey element VirtualNetworkGatewayConnection|
|/connections/vpndeviceconfigurationscript/read|Gets Vpn Device Configuration of VirtualNetworkGatewayConnection|
|/ połączeń/zapisu|Tworzy lub aktualizuje istniejący element VirtualNetworkGatewayConnection|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Usuwa serwer Proxy planu ochrony przed atakami DDoS|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Pobiera definicję ochrony przed atakami DDoS Planowanie serwera Proxy|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Tworzy serwer Proxy Plan ochrony przed atakami DDoS lub aktualizacji i serwer Proxy Plan ochrony przed atakami DDoS istniejących|
|/ddosProtectionPlans/delete|Usuwa Plan ochrony przed atakami DDoS|
|/ddosProtectionPlans/join/action|Tworzy sprzężenie Plan ochrony przed atakami DDoS|
|/ddosProtectionPlans/read|Pobiera Plan ochrony przed atakami DDoS|
|/ddosProtectionPlans/write|Tworzy Plan ochrony przed atakami DDoS lub aktualizuje Plan ochrony przed atakami DDoS |
|/dnsoperationresults/read|Pobiera wyniki operacji DNS|
|/dnsoperationstatuses/read|Pobiera stan operacji DNS |
|/dnszones/A/delete|Usuń zestaw rekordów o danej nazwie i typu "A" ze strefy DNS.|
|/dnszones/A/read|Pobierz zestaw rekordów typu "A", w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag.|
|/dnszones/A/write|Utwórz lub zaktualizuj zestaw rekordów typu "A" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów.|
|/dnszones/AAAA/delete|Usuń zestaw rekordów o danej nazwie i typu "AAAA" ze strefy DNS.|
|/dnszones/AAAA/read|Pobierz zestaw rekordów typu "AAAA" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag.|
|/dnszones/AAAA/write|Utwórz lub zaktualizuj zestaw rekordów typu "AAAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów.|
|/dnszones/all/read|Pobiera różnych typów zestawów rekordów DNS|
|/dnszones/CAA/delete|Usuń zestaw rekordów o danej nazwie i wpisz "CAA" ze strefy DNS.|
|/dnszones/CAA/read|Pobierz zestaw rekordów typu "CAA" w formacie JSON. Zestaw rekordów zawiera TTL, tag i etag.|
|/dnszones/CAA/write|Utwórz lub zaktualizuj zestaw rekordów typu "CAA" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów.|
|/dnszones/CNAME/delete|Usuń zestaw rekordów o danej nazwie i typu "CNAME" ze strefy DNS.|
|/dnszones/CNAME/read|Pobierz zestaw rekordów typu "CNAME" w formacie JSON. Zestaw rekordów zawiera TTL, tag i etag.|
|/dnszones/CNAME/write|Utwórz lub zaktualizuj zestaw rekordów typu "CNAME" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów.|
|/dnszones/delete|Usuń strefę DNS, w formacie JSON. Właściwości strefy obejmują tag, etag, numberOfRecordSets i maxNumberOfRecordSets.|
|/dnszones/MX/delete|Usuń zestaw rekordów o danej nazwie i typu "MX" ze strefy DNS.|
|/dnszones/MX/read|Pobierz zestaw rekordów typu "MX" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag.|
|/dnszones/MX/write|Utwórz lub zaktualizuj zestaw rekordów typu "MX" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów.|
|/dnszones/NS/delete|Usuwa zestaw rekordów DNS typu NS|
|/dnszones/NS/read|Pobiera zestaw rekordów DNS typu NS|
|/dnszones/NS/write|Tworzy lub aktualizuje zestaw rekordów DNS typu NS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyki strefy DNS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki strefy DNS|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Pobiera definicje metryk strefy DNS|
|/dnszones/PTR/delete|Usuń zestaw rekordów o danej nazwie i typu "PTR" ze strefy DNS.|
|/dnszones/PTR/read|Pobierz zestaw rekordów typu "PTR" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag.|
|/dnszones/PTR/write|Utwórz lub zaktualizuj zestaw rekordów typu "PTR" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów.|
|/dnszones/read|Pobierz strefę DNS, w formacie JSON. Właściwości strefy obejmują tag, etag, numberOfRecordSets i maxNumberOfRecordSets. Należy pamiętać, że to polecenie nie pobiera zestawów rekordów znajdujących się w strefie.|
|/dnszones/recordsets/read|Pobiera różnych typów zestawów rekordów DNS|
|/dnszones/SOA/read|Pobiera zestaw rekordów DNS typu SOA|
|/dnszones/SOA/write|Tworzy lub aktualizuje zestaw rekordów DNS typu SOA|
|/dnszones/SRV/delete|Usuń zestaw rekordów o danej nazwie i typu "SRV" ze strefy DNS.|
|/dnszones/SRV/read|Pobierz zestaw rekordów typu "SRV" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag.|
|/dnszones/SRV/write|Utwórz lub zaktualizuj zestaw rekordów typu SRV|
|/dnszones/TXT/delete|Usuń zestaw rekordów o danej nazwie i typu "TXT" ze strefy DNS.|
|/dnszones/TXT/read|Pobierz zestaw rekordów typu "TXT" w formacie JSON. Zestaw rekordów zawiera listę rekordów oraz TTL, znaczników i etag.|
|/dnszones/TXT/write|Utwórz lub zaktualizuj zestaw rekordów typu "TXT" w strefie DNS. Określone rekordy zastąpią bieżące rekordy w zestawie rekordów.|
|/dnszones/write|Utwórz lub zaktualizuj strefę DNS w ramach grupy zasobów.  Używane do aktualizowania elementów tag w zasobie strefy DNS. Należy pamiętać, że to polecenie nie może służyć do utworzenia ani zaktualizowania zestawów rekordów w strefie.|
|/expressRouteCircuits/authorizations/delete|Usuwa ExpressRouteCircuit autoryzacji|
|/expressRouteCircuits/authorizations/read|Pobiera autoryzacji ExpressRouteCircuit|
|/expressRouteCircuits/authorizations/write|Tworzy lub aktualizuje istniejącą autoryzacji ExpressRouteCircuit|
|/expressRouteCircuits/delete|Usuwa ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Pobiera ExpressRouteCircuit ArpTable komunikacji równorzędnej|
|/expressRouteCircuits/peerings/connections/delete|Usuwa połączenie ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/read|Pobiera połączenie ExpressRouteCircuit|
|/expressRouteCircuits/peerings/connections/write|Tworzy lub aktualizuje istniejący zasób połączenia ExpressRouteCircuit|
|/expressRouteCircuits/peerings/delete|Usuwa ExpressRouteCircuit komunikacji równorzędnej|
|/expressRouteCircuits/peerings/read|Pobiera ExpressRouteCircuit komunikacji równorzędnej|
|/expressRouteCircuits/peerings/routeTables/action|Pobiera stan równorzędna ExpressRouteCircuit|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Pobiera ExpressRouteCircuit równorzędna stan podsumowanie|
|/expressRouteCircuits/peerings/stats/read|Pobiera ExpressRouteCircuit równorzędna Stat|
|/expressRouteCircuits/peerings/write|Tworzy lub aktualizuje istniejącą ExpressRouteCircuit komunikacji równorzędnej|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyki dla obwody usługi ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki dla obwody usługi ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Uzyskać zdarzenia obwody usługi ExpressRoute|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Pobiera metryki definicje obwody usługi ExpressRoute|
|/expressRouteCircuits/read|Get an ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Gets an ExpressRouteCircuit Stat|
|/expressRouteCircuits/write|Tworzy lub aktualizuje istniejące ExpressRouteCircuit|
|/expressRouteCrossConnections/delete|Usuń Express Route Cross połączenia|
|/expressRouteCrossConnections/join/action|Połączenie między sprzężenia usługi Express Route|
|/expressRouteCrossConnections/peerings/arpTables/action|Pobiera Express Route Cross tabeli protokołu Arp równorzędna połączenia|
|/expressRouteCrossConnections/peerings/delete|Usuwa Cross połączenia komunikacji równorzędnej Expressroute|
|/expressRouteCrossConnections/peerings/read|Pobiera Cross połączenia komunikacji równorzędnej Expressroute|
|/expressRouteCrossConnections/peerings/routeTables/action|Pobiera Express Route Cross połączenia komunikacji równorzędnej tabeli tras|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Pobiera Cross podsumowanie tabeli tras połączenia komunikacji równorzędnej Expressroute|
|/expressRouteCrossConnections/peerings/stats/read|Pobiera Cross połączenia Stat komunikacji równorzędnej Expressroute|
|/expressRouteCrossConnections/peerings/write|Tworzy trasę dla wielu połączeń równorzędnej lub aktualizuje istniejącą Cross połączenia komunikacji równorzędnej Express Route|
|/expressRouteCrossConnections/read|Pobierz Express Route Cross połączenia|
|/expressRouteCrossConnections/write|Utwórz lub zaktualizuj Express Route Cross połączenia|
|/expressRouteServiceProviders/read|Pobiera dostawców usługi Expressroute|
|/loadBalancers/backendAddressPools/join/action|Dołącza do puli adresów zaplecza modułu równoważenia obciążenia|
|/loadBalancers/backendAddressPools/read|Pobiera definicję puli adresów zaplecza modułu równoważenia obciążenia|
|/loadBalancers/delete|Usuwa usługę równoważenia obciążenia|
|/loadBalancers/frontendIPConfigurations/read|Pobiera definicję konfiguracji IP frontonu modułu równoważenia obciążenia|
|/loadBalancers/inboundNatPools/join/action|Tworzy sprzężenie modułu równoważenia obciążenia ruchu przychodzącego pulę translacji nat|
|/loadBalancers/inboundNatPools/read|Pobiera moduł równoważenia obciążenia ruchu przychodzącego definicję puli translacji nat|
|/loadBalancers/inboundNatRules/delete|Usuwa regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia|
|/loadBalancers/inboundNatRules/join/action|Tworzy sprzężenie reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia|
|/loadBalancers/inboundNatRules/read|Pobiera moduł równoważenia obciążenia definicję przychodzącej reguły nat|
|/loadBalancers/inboundNatRules/write|Tworzy regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia lub aktualizuje istniejącą regułę nat dla ruchu przychodzącego modułu równoważenia obciążenia|
|/loadBalancers/loadBalancingRules/read|Pobiera definicję modułu równoważenia obciążenia obciążenia równoważenia reguły|
|/loadBalancers/networkInterfaces/read|Pobiera odwołania do wszystkich interfejsów sieciowych należących do modułu równoważenia obciążenia|
|/loadBalancers/outboundNatRules/read|Pobiera definicję reguły nat ruchu wychodzącego modułu równoważenia obciążenia|
|/loadBalancers/probes/join/action|Zezwala przy użyciu sondy modułu równoważenia obciążenia. Na przykład z tą właściwością healthProbe uprawnienia skali maszyny Wirtualnej zestawu można odwoływać się sondy.|
|/loadBalancers/probes/read|Pobiera sondę modułu równoważenia obciążenia|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyki usługi równoważenia obciążenia|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki usługi równoważenia obciążenia|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Pobiera zdarzenia do modułu równoważenia obciążenia|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla usługi równoważenia obciążenia|
|/loadBalancers/read|Pobiera definicję modułu równoważenia obciążenia|
|/loadBalancers/virtualMachines/read|Pobiera odwołania do wszystkich maszyn wirtualnych należących do modułu równoważenia obciążenia|
|/loadBalancers/write|Tworzy moduł równoważenia obciążenia lub aktualizuje istniejący moduł równoważenia obciążenia|
|/localnetworkgateways/delete|Usuwa LocalNetworkGateway|
|/localnetworkgateways/read|Gets LocalNetworkGateway|
|/localnetworkgateways/write|Tworzy lub aktualizuje istniejące LocalNetworkGateway|
|/locations/checkDnsNameAvailability/read|Sprawdza, czy etykieta dns jest dostępna w określonej lokalizacji|
|/Locations/operationResults/Read|Pobiera wynik operacji asynchronicznej POST lub operacja usuwania|
|/Locations/Operations/Read|Pobiera zasób operacji reprezentujący stan operacji asynchronicznej|
|/Locations/Usages/Read|Pobiera metryki użycia zasobów|
|/locations/virtualNetworkAvailableEndpointServices/read|Pobiera listę dostępnych usług punktu końcowego sieci wirtualnej|
|/networkInterfaces/delete|Usuwa interfejs sieciowy|
|/networkInterfaces/diagnosticIdentity/read|Pobiera tożsamość diagnostycznych zasobu|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Pobieranie grup zabezpieczeń sieci skonfigurowane w sieci interfejsu z maszyny wirtualnej|
|/networkInterfaces/effectiveRouteTable/action|Pobierz tabelę tras skonfigurowane dla interfejsu sieciowego maszyny wirtualnej|
|/networkInterfaces/ipconfigurations/read|Pobiera definicję konfiguracji adresu ip interfejsu sieciowego. |
|/networkInterfaces/join/action|Dołącza maszynę wirtualną do interfejsu sieciowego|
|/networkInterfaces/loadBalancers/read|Pobiera wszystkich usług równoważenia obciążenia interfejsu sieciowego jest częścią|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki interfejsu sieciowego|
|/networkInterfaces/read|Pobiera definicję interfejsu sieciowego. |
|/networkInterfaces/write|Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy. |
|/networkSecurityGroups/defaultSecurityRules/read|Pobiera domyślną definicję reguły zabezpieczeń|
|/networkSecurityGroups/delete|Usuwa sieciową grupę zabezpieczeń|
|/networkSecurityGroups/join/action|Dołącza do sieciowej grupy zabezpieczeń|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyki grup zabezpieczeń sieci|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki sieciowych grup zabezpieczeń, ta operacja jest uzupełniana przez dostawcę zasobów.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Pobiera zdarzenia sieciowej grupy zabezpieczeń|
|/networkSecurityGroups/read|Pobiera definicję grupy zabezpieczeń sieci|
|/networkSecurityGroups/securityRules/delete|Usuwa regułę zabezpieczeń|
|/networkSecurityGroups/securityRules/read|Pobiera definicję reguły zabezpieczeń|
|/networkSecurityGroups/securityRules/write|Tworzy regułę zabezpieczeń lub aktualizuje istniejącą regułę zabezpieczeń|
|/networkSecurityGroups/write|Tworzy sieciową grupę zabezpieczeń lub aktualizuje istniejącą sieciową grupę zabezpieczeń|
|/networkWatchers/availableProvidersList/action|Zwraca wszystkie dostępne internet dostawcy usług dla określonego regionu platformy Azure.|
|/networkWatchers/azureReachabilityReport/action|Zwraca wynik względną opóźnienia Internetu dostawcy usług z określonej lokalizacji do regiony platformy Azure.|
|/networkWatchers/configureFlowLog/action|Konfiguruje przepływu rejestrowanie dla zasobu docelowego.|
|/networkWatchers/connectionMonitors/delete|Usuwa Monitor połączenia|
|/networkWatchers/connectionMonitors/Providers/Microsoft.Insights/ diagnosticSettings/Odczyt|Pobierz ustawienia diagnostyki monitora połączenia|
|/networkWatchers/connectionMonitors/Providers/Microsoft.Insights/ diagnosticSettings/zapisu|Tworzy lub aktualizuje ustawienia diagnostyki Monitor połączenia|
|/networkWatchers/connectionMonitors/Providers/Microsoft.Insights/ metricDefinitions/Odczyt|Pobiera dostępne metryki dla połączenia monitora|
|/networkWatchers/connectionMonitors/query/action|Monitorowanie łączności między punktami końcowymi określonego zapytania|
|/networkWatchers/connectionMonitors/read|Uzyskiwanie szczegółowych informacji Monitor połączenia|
|/networkWatchers/connectionMonitors/start/action|Rozpocznij monitorowanie łączność między określonym punkty końcowe|
|/networkWatchers/connectionMonitors/stop/action|Monitorowanie łączności między punktami końcowymi określonego Stop/Wstrzymaj|
|/networkWatchers/connectionMonitors/write|Tworzy Monitor połączenia|
|/networkWatchers/connectivityCheck/action|Sprawdza, czy możliwość nawiązywania bezpośredniego połączenia TCP z maszyny wirtualnej do danego punktu końcowego, łącznie z innej maszyny Wirtualnej lub dowolnego serwera zdalnego.|
|/networkWatchers/delete|Usuwa obserwatora sieciowego|
|/networkWatchers/ipFlowVerify/action|Zwraca czy pakiet jest dozwolony lub odmowa dostępu do lub z danego przeznaczenia.|
|/networkWatchers/lenses/delete|Usuwa obiektyw|
|/networkWatchers/lenses/query/action|Monitorowanie ruchu w sieci w określonym punkcie końcowym zapytania|
|/networkWatchers/lenses/read|Uzyskiwanie szczegółowych informacji obiektyw|
|/networkWatchers/lenses/start/action|Rozpocznij monitorowanie ruchu sieciowego na określony punkt końcowy|
|/networkWatchers/lenses/stop/action|Monitorowanie ruchu w sieci w określonym punkcie końcowym Stop/Wstrzymaj|
|/networkWatchers/lenses/write|Tworzy obiektyw|
|/networkWatchers/nextHop/action|Określony obiekt docelowy oraz docelowy adres IP zwracany typ następnego przeskoku i dalej nadzieję, że adres IP.|
|/networkWatchers/packetCaptures/delete|Usuwa przechwytywania pakietów|
|/networkWatchers/packetCaptures/queryStatus/action|Pobiera informacje o właściwościach i stan zasobu przechwytywania pakietów.|
|/networkWatchers/packetCaptures/read|Pobierz definicję przechwytywania pakietów|
|/networkWatchers/packetCaptures/stop/action|Zatrzymaj uruchomiona sesja przechwytywania pakietów.|
|/networkWatchers/packetCaptures/write|Tworzy przechwytywania pakietów|
|/networkWatchers/queryFlowLogStatus/action|Pobiera stan przepływu rejestrowania w zasobie.|
|/networkWatchers/queryTroubleshootResult/action|Pobiera wynik rozwiązywania problemów z poprzednio uruchomionymi lub aktualnie uruchomiona operacja rozwiązywania problemów.|
|/networkWatchers/read|Pobierz definicję obserwatora sieciowego|
|/networkWatchers/securityGroupView/action|Wyświetl reguły grupy zabezpieczeń sieci skonfigurowane i skuteczne, stosowane na maszynie Wirtualnej.|
|/networkWatchers/topology/action|Pobiera widok poziomu sieci, zasobów i ich relacje w grupie zasobów.|
|/networkWatchers/troubleshoot/action|Uruchamia Rozwiązywanie problemów w zasobie sieci na platformie Azure.|
|/networkWatchers/write|Tworzy obserwatora sieciowego lub aktualizuje istniejącą obserwatora sieciowego|
|/Operations/Read|Pobiera dostępne operacje|
|/publicIPAddresses/delete|Usuwa publiczny adres Ip.|
|/publicIPAddresses/join/action|Dołącza do publicznego adresu ip|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Pobierz ustawienia diagnostyki publicznego adresu IP|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Utwórz lub zaktualizuj ustawienia diagnostyki publicznego adresu IP|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Pobierz definicje dziennika publicznego adresu IP|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Pobierz definicje metryk publicznego adresu IP|
|/publicIPAddresses/read|Pobiera definicję adres publiczny adres ip.|
|/publicIPAddresses/write|Tworzy publiczny adres Ip lub aktualizuje istniejący publiczny adres Ip. |
|/ register/działania|Rejestruje subskrypcję|
|/routeFilters/delete|Usuwa definicję filtru tras|
|/routeFilters/join/action|Dołącza filtr tras|
|/routeFilters/read|Pobiera definicję filtru tras|
|/routeFilters/routeFilterRules/delete|Usuwa definicję reguły filtru trasy|
|/routeFilters/routeFilterRules/read|Pobiera definicję reguły filtru trasy|
|/routeFilters/routeFilterRules/write|Tworzy regułę filtru tras lub aktualizuje istniejącą regułę filtru trasy|
|/routeFilters/write|Tworzy filtr tras lub aktualizuje istniejący filtr tras|
|/routeTables/DELETE|Usuwa definicję tabeli tras|
|/routeTables/JOIN/Action|Tworzy sprzężenie tabeli tras|
|/routeTables/Read|Pobiera definicję tabeli tras|
|/routeTables/Routes/DELETE|Usuwa definicję trasy|
|/routeTables/Routes/Read|Pobiera definicję trasy|
|/routeTables/routes/write|Tworzy trasę lub aktualizuje istniejącą trasę|
|/ routeTables/zapisu|Tworzy tabelę tras lub aktualizuje istniejącą tabelę tras|
|/securegateways/applicationRuleCollections/delete|Usuwa kolekcji reguł aplikacji bezpiecznego bramy|
|/securegateways/applicationRuleCollections/read|Pobierania kolekcję reguł aplikacji dla danej bramy Secure|
|/securegateways/applicationRuleCollections/write|Tworzy lub aktualizuje kolekcję reguł aplikacji bezpiecznego bramy|
|/securegateways/delete|Usuń bramę bezpieczne|
|/securegateways/networkRuleCollections/delete|Usuwa kolekcji reguł sieciowej bezpiecznej bramy|
|/securegateways/networkRuleCollections/read|Pobierania kolekcję reguł sieci dla danego bramy Secure|
|/securegateways/networkRuleCollections/write|Tworzy lub aktualizuje kolekcję reguł sieciowej Secure bramy|
|/securegateways/read|Pobierz bezpieczny bramy|
|/ securegateways/zapisu|Tworzy lub aktualizuje Secure bramy|
|/serviceEndpointPolicies/delete|Usuwa zasady punktu końcowego usługi|
|/serviceEndpointPolicies/join/action|Tworzy sprzężenie zasad punktu końcowego usługi|
|/serviceEndpointPolicies/joinSubnet/action|Tworzy sprzężenie podsieci zasad punktu końcowego usługi|
|/serviceEndpointPolicies/read|Pobiera opis zasad punktu końcowego usługi|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|Usuwa definicję zasad punktu końcowego usługi|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|Pobiera wywieranych usługi punktu końcowego zasady definicji przez|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Tworzy zasady definicji punktu końcowego usługi lub aktualizuje istniejącą definicję zasad punktu końcowego usługi|
|/serviceEndpointPolicies/write|Tworzy zasady punktu końcowego usługi lub aktualizuje istniejące zasady punktu końcowego usługi|
|/trafficManagerGeographicHierarchies/read|Pobiera zawierający regionów, które mogą być używane z metody routingu ruchu geograficznego hierarchii Geographic Menedżera ruchu|
|/trafficManagerProfiles/azureEndpoints/delete|Usuwa punktu końcowego platformy Azure z istniejącego profilu Menedżera ruchu. Menedżer ruchu przestanie routingu ruchu usunięto punkt końcowy usługi Azure.|
|/trafficManagerProfiles/azureEndpoints/read|Pobiera punktu końcowego platformy Azure, która należy do profilu Menedżera ruchu, w tym wszystkie właściwości tego punktu końcowego platformy Azure.|
|/trafficManagerProfiles/azureEndpoints/write|Dodaj nowy punkt końcowy Azure w istniejącego profilu Menedżera ruchu lub zaktualizować właściwości istniejącego punktu końcowego platformy Azure, w tym profilu usługi Traffic Manager.|
|/trafficManagerProfiles/delete|Usuń profil Menedżera ruchu. Wszystkie ustawienia skojarzone z profilem Menedżera ruchu zostaną utracone i profilu nie może służyć do kierowania ruchem.|
|/trafficManagerProfiles/externalEndpoints/delete|Usuwa zewnętrznego punktu końcowego z istniejącego profilu Menedżera ruchu. Menedżer ruchu przestanie routingu ruchu do usuniętego zewnętrznego punktu końcowego.|
|/trafficManagerProfiles/externalEndpoints/read|Pobiera zewnętrznego punktu końcowego, który należy do profilu usługi Traffic Manager, w tym wszystkie właściwości tego zewnętrznego punktu końcowego.|
|/trafficManagerProfiles/externalEndpoints/write|Dodaj nowe zewnętrznego punktu końcowego w istniejącego profilu Menedżera ruchu lub zaktualizować właściwości istniejącego zewnętrznego punktu końcowego, w tym profilu usługi Traffic Manager.|
|/trafficManagerProfiles/heatMaps/read|Pobiera Mapa cieplna Menedżera ruchu dla danego profilu usługi Traffic Manager zawierający dane liczby i opóźnienia zapytania według lokalizacji i źródła adresów IP.|
|/trafficManagerProfiles/nestedEndpoints/delete|Usuwa zagnieżdżone punkt końcowy z istniejącego profilu Menedżera ruchu. Menedżer ruchu przestanie routingu ruchu usunięto punkt końcowy zagnieżdżone.|
|/trafficManagerProfiles/nestedEndpoints/read|Pobiera zagnieżdżone punktu końcowego, który należy do profilu usługi Traffic Manager, w tym wszystkie właściwości tego punktu końcowego zagnieżdżone.|
|/trafficManagerProfiles/nestedEndpoints/write|Dodaj nowy punkt końcowy zagnieżdżone w istniejącego profilu Menedżera ruchu lub zaktualizować właściwości istniejący zagnieżdżone punkt końcowy w tym profilu usługi Traffic Manager.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyki Menedżera ruchu|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki Menedżera ruchu, ta operacja jest uzupełniana przez dostawcę zasobów szczegółowych informacji.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Pobiera zdarzenia dla Menedżera ruchu|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla Menedżera ruchu.|
|/trafficManagerProfiles/read|Pobierz konfigurację profilu Menedżera ruchu. W tym ustawienia DNS, ustawienia kierowania ruchu, ustawienia monitorowania punktu końcowego i listę punktów końcowych kierowanych przez ten profil Menedżera ruchu.|
|/trafficManagerProfiles/write|Tworzenie profilu Menedżera ruchu lub zmodyfikować konfigurację istniejącego profilu Menedżera ruchu. Dotyczy to również włączenia lub wyłączenia profilu i modyfikowanie ustawień DNS, ustawienia kierowania ruchu lub ustawień monitorowania dla punktu końcowego. Punkty końcowe kierowane przez profil Menedżera ruchu można dodać, usunąć, włączone lub wyłączone.|
|/trafficManagerUserMetricsKeys/delete|Usuwa używane do zbierania metryk użytkownika w czasie rzeczywistym klucza na poziomie subskrypcji.|
|/trafficManagerUserMetricsKeys/read|Pobiera klucz poziom subskrypcji używane do zbierania metryk użytkownika w czasie rzeczywistym.|
|/trafficManagerUserMetricsKeys/write|Tworzy nowy klucz poziom subskrypcji do użycia dla kolekcji metryk użytkownika w czasie rzeczywistym.|
|/ unregister/działania|Wyrejestrowuje subskrypcji|
|/virtualHubs/delete|Usuwa koncentratora wirtualnego|
|/virtualHubs/hubVirtualNetworkConnections/delete|Usuwa HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/read|Get a HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/write|Utwórz lub zaktualizuj HubVirtualNetworkConnection|
|/virtualHubs/read|Pobierz koncentratora wirtualnego|
|/virtualHubs/write|Utwórz lub zaktualizuj Centrum wirtualnego|
|/virtualnetworkgateways/connections/read|Get VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|Usuwa element virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnclientpackage/action|Generate VpnClient package for virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnprofile/action|Generowanie pakietu VpnProfile VirtualNetworkGateway|
|/virtualnetworkgateways/getadvertisedroutes/action|Pobiera virtualNetworkGateway anonsowane tras|
|/virtualnetworkgateways/getbgppeerstatus/action|Pobiera stan elementu równorzędnego protokołu bgp virtualNetworkGateway|
|/virtualnetworkgateways/getlearnedroutes/action|Pobiera trasy virtualnetworkgateway rozpoznane|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Pobierz klienta VPN Ipsec parametrów VirtualNetworkGateway P2S klienta.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Pobiera adres URL profilu pakiet klienta vpn wstępnie wygenerowane|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyki bramy sieci wirtualnej|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyki bramy sieci wirtualnej, ta operacja jest uzupełniana przez dostawcę zasobów szczegółowych informacji.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Pobiera zdarzenia bramy sieci wirtualnej|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla bramy sieci wirtualnej|
|/virtualNetworkGateways/read|Pobiera element VirtualNetworkGateway|
|/virtualnetworkgateways/reset/action|Resetuje element virtualNetworkGateway|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Ustaw parametry klienta VirtualNetworkGateway P2S Ipsec klienta VPN.|
|/virtualnetworkgateways/supportedvpndevices/action|Przedstawia obsługiwane urządzenia sieci Vpn|
|/virtualNetworkGateways/write|Tworzy lub aktualizuje element VirtualNetworkGateway|
|/virtualNetworks/checkIpAddressAvailability/read|Sprawdź, czy adres Ip jest dostępna w określonej sieci wirtualnej|
|/virtualNetworks/customViews/get/action|Pobieranie zawartości widok niestandardowy sieci wirtualnej|
|/virtualNetworks/customViews/read|Pobierz definicję widoku niestandardowego sieci wirtualnej|
|/virtualNetworks/delete|Usuwa sieci wirtualnej|
|/virtualNetworks/peer/action|Równorzędnymi użytkownikami sieci wirtualnej z inną siecią wirtualną|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Pobierz ustawienia diagnostyki sieci wirtualnej|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Utwórz lub zaktualizuj ustawienia diagnostyki sieci wirtualnej|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Pobierz definicje dziennika sieci wirtualnej|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Pobierz definicje metryk sieci wirtualnej|
|/virtualNetworks/read|Pobierz definicję sieci wirtualnej|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Usuwa serwer proxy sieci wirtualnej komunikacji równorzędnej|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Pobiera sieci wirtualnej komunikacji równorzędnej definicji serwera proxy|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Tworzy serwer proxy komunikacji równorzędnej sieci wirtualnej lub aktualizuje istniejące sieci wirtualnej komunikacji równorzędnej serwera proxy|
|/virtualNetworks/subnets/delete|Usuwa podsieć sieci wirtualnej|
|/virtualNetworks/subnets/join/action|Tworzy sprzężenie sieci wirtualnej|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Tworzy sprzężenie zasobów, takich jak konta magazynu lub bazy danych SQL do podsieci.|
|/virtualNetworks/subnets/read|Pobiera definicję podsieci sieci wirtualnej|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Usuwa zasób łącza nawigacji|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Pobierz definicję zasobu łącza nawigacji|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Tworzy łącze nawigacji zasobu lub aktualizuje istniejące łącze nawigacji zasobu|
|/virtualNetworks/subnets/virtualMachines/read|Pobiera odwołania do wszystkich maszyn wirtualnych w podsieci sieci wirtualnej|
|/virtualNetworks/subnets/write|Tworzy podsieć sieci wirtualnej lub aktualizuje istniejącą podsieć sieci wirtualnej|
|/virtualNetworks/taggedTrafficConsumers/delete|Usuwa konsumenta oznakowanych ruchu|
|/virtualNetworks/taggedTrafficConsumers/read|Pobierz definicję oznakowane konsumenta ruchu|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Weryfikuje konsumenta oznakowanych ruchu|
|/virtualNetworks/taggedTrafficConsumers/write|Tworzy konsumenta ruchu oznakowane lub aktualizuje istniejące konsumenta ruchu oznakowane|
|/virtualNetworks/usages/read|Pobierz użycia adresu IP dla każdej podsieci sieci wirtualnej|
|/virtualNetworks/virtualMachines/read|Pobiera odwołania do wszystkich maszyn wirtualnych w sieci wirtualnej|
|/virtualNetworks/virtualNetworkPeerings/delete|Usuwa element równorzędny sieci wirtualnej|
|/virtualNetworks/virtualNetworkPeerings/read|Pobiera definicję sieci wirtualnej komunikacji równorzędnej|
|/virtualNetworks/virtualNetworkPeerings/write|Tworzy element równorzędny sieci wirtualnej lub aktualizuje istniejące sieci wirtualnej komunikacji równorzędnej|
|/virtualNetworks/write|Tworzy sieć wirtualną lub aktualizuje istniejącą sieć wirtualną|
|/virtualNetworkTaps/delete|Usuń naciśnij sieci wirtualnej|
|/virtualNetworkTaps/join/action|Tworzy sprzężenie naciśnij sieci wirtualnej|
|/virtualNetworkTaps/read|Pobierz naciśnij sieci wirtualnej|
|/virtualNetworkTaps/write|Utwórz lub zaktualizuj naciśnij sieci wirtualnej|
|/virtualwans/delete|Usuwa wirtualnych sieci Wan|
|/virtualwans/Read|Get a wirtualnych sieci Wan|
|/virtualWans/virtualHubProxies/delete|Usuwa serwer proxy koncentratora wirtualnego|
|/virtualWans/virtualHubProxies/read|Pobiera definicję serwera proxy koncentratora wirtualnego|
|/virtualWans/virtualHubProxies/write|Tworzy serwer proxy koncentratora wirtualnej lub aktualizuje serwer proxy koncentratora wirtualnego|
|/virtualwans/virtualHubs/read|Pobiera wszystkie wirtualnego koncentratorów, które są skojarzone z wirtualnych sieci Wan.|
|/virtualwans/vpnconfiguration/read|Pobiera konfigurację sieci Vpn|
|/virtualWans/vpnSiteProxies/delete|Usuwa serwer proxy w lokacji sieci Vpn|
|/virtualWans/vpnSiteProxies/read|Pobiera definicję serwera proxy w lokacji sieci Vpn|
|/virtualWans/vpnSiteProxies/write|Tworzy serwer proxy w lokacji sieci Vpn lub aktualizuje proxy witryny sieci Vpn|
|/virtualwans/vpnSites/read|Pobiera wszystkie witryny sieci VPN, które są skojarzone z wirtualnych sieci Wan.|
|/virtualwans/write|Utwórz lub zaktualizuj wirtualnego Wan|
|/vpnGateways/read|Pobiera element VpnGateway.|
|/vpnGateways/vpnConnections/read|Pobiera VpnConnection.|
|/vpnGateways/vpnConnections/write|Umieszcza VpnConnection.|
|/vpnGateways/write|Umieszcza element VpnGateway.|
|/vpnsites/delete|Usuwa zasób lokacji sieci Vpn.|
|/vpnsites/Read|Pobiera zasób lokacji sieci Vpn.|
|/ vpnsites/zapisu|Tworzy lub aktualizuje zasób lokacji sieci Vpn.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Operacja | Opis |
|---|---|
|/CheckNamespaceAvailability/action|Sprawdza, czy dana nazwa zasobu przestrzeni nazw jest dostępna w usłudze NotificationHub.|
|/ Przestrzenie nazw/authorizationRules/działania|Pobierz listę opisów reguł autoryzacji przestrzeni nazw.|
|/Namespaces/authorizationRules/delete|Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej. |
|/Namespaces/authorizationRules/listkeys/action|Pobierz parametry połączenia z przestrzenią nazw|
|/Namespaces/authorizationRules/read|Pobierz listę opisów reguł autoryzacji przestrzeni nazw.|
|/Namespaces/authorizationRules/regenerateKeys/action|Reguła autoryzacji przestrzeni nazw: wygeneruj ponownie klucz podstawowy/pomocniczy. Określ klucz do ponownego wygenerowania|
|/Namespaces/authorizationRules/write|Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze.|
|/Namespaces/CheckNotificationHubAvailability/action|Sprawdza, czy dana nazwa usługi NotificationHub jest dostępna w przestrzeni nazw.|
|/ Przestrzenie nazw/usuwania|Usuń zasób przestrzeni nazw|
|/Namespaces/NotificationHubs/authorizationRules/action|Pobierz listę reguł autoryzacji centrum powiadomień|
|/Namespaces/NotificationHubs/authorizationRules/delete|Usuń reguły autoryzacji centrum powiadomień|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Pobierz parametry połączenia z centrum powiadomień|
|/Namespaces/NotificationHubs/authorizationRules/read|Pobierz listę reguł autoryzacji centrum powiadomień|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Reguła autoryzacji centrum powiadomień: wygeneruj ponownie klucz główny/pomocniczy. Określ klucz do ponownego wygenerowania|
|/Namespaces/NotificationHubs/authorizationRules/write|Tworzenie reguł autoryzacji Centrum powiadomień i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze.|
|/Namespaces/NotificationHubs/debugSend/action|Wyślij testowe powiadomienie wypychane.|
|/Namespaces/NotificationHubs/Delete|Usuń zasób centrum powiadomień|
|/Namespaces/NotificationHubs/metricDefinitions/read|Pobierz listę metryki Namespace opisów zasobów|
|/Namespaces/NotificationHubs/pnsCredentials/action|Pobierz wszystkie poświadczenia systemu powiadomień platformy Centrum powiadomień. Obejmuje to poświadczenia usługi WNS, MPNS, APNS, usługi GCM i Baidu|
|/Namespaces/NotificationHubs/read|Pobierz listę opisów zasobów centrum powiadomień|
|/Namespaces/NotificationHubs/write|Tworzenie Centrum powiadomień i aktualizowanie jej właściwości. Jego właściwości obejmują głównie poświadczeń systemu powiadomień platformy. Reguły autoryzacji i czas wygaśnięcia|
|Przestrzenie nazw/odczytu|Pobierz listę opisów zasobów przestrzeni nazw|
|Przestrzenie nazw/zapisu|Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane.|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów usługi NotifciationHubs i włącza funkcję tworzenia przestrzeni nazw i centrów NotificationHub|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Operacja | Opis |
|---|---|
|/linkTargets/read|Wyświetla listę istniejących kont, które nie są skojarzone z subskrypcją platformy Azure. Aby połączyć tej subskrypcji platformy Azure do obszaru roboczego, użyj identyfikator klienta zwracane przez tę operację we właściwości klienta identyfikator operacji Utwórz obszar roboczy.|
|/ register/działania|Zarejestruj subskrypcję dostawcy zasobów.|
|/workspaces/analytics/query/action|Wyszukiwanie przy użyciu nowy aparat.|
|/workspaces/analytics/query/schema/read|Pobieranie schematu wyszukiwania V2.|
|/workspaces/API/Query/Action|Wyszukiwanie przy użyciu nowy aparat.|
|/workspaces/api/query/schema/read|Pobieranie schematu wyszukiwania V2.|
|/workspaces/configurationScopes/delete|Usuwanie konfiguracji zakresu|
|/workspaces/configurationScopes/read|Pobierz konfigurację zakresu|
|/workspaces/configurationScopes/write|Ustawianie zakresu konfiguracji|
|/workspaces/datasources/delete|Usuwanie źródeł danych w obszarze roboczym.|
|/workspaces/datasources/read|Pobierz źródeł danych w obszarze roboczym.|
|/workspaces/datasources/write|Utwórz/Aktualizuj źródeł danych w obszarze roboczym.|
|/workspaces/DELETE|Usuwa obszaru roboczego. Jeśli obszar roboczy został połączony z istniejącym obszarem roboczym w czasie tworzenia obszaru roboczego, który był połączony z nie zostanie usunięta.|
|/workspaces/generateregistrationcertificate/action|Generuje certyfikatu rejestracji dla obszaru roboczego. Ten certyfikat służy do nawiązania połączenia obszaru roboczego programu Microsoft System Center Operations Manager.|
|/workspaces/intelligencepacks/disable/Action|Wyłącza pakietu intelligence pack dla danego obszaru roboczego.|
|/workspaces/intelligencepacks/enable/Action|Włącza pakiet analizy dla danego obszaru roboczego.|
|/workspaces/intelligencepacks/Read|Wyświetla listę wszystkich pakietów analizy, które są widoczne dla danego worksapce, a także wyświetla informację, czy pakiet jest włączone lub wyłączone dla tego obszaru roboczego.|
|/workspaces/linkedServices/delete|Usuń połączone usługi na podstawie danego obszaru roboczego.|
|/workspaces/linkedServices/read|Pobierz połączonej usługi w obszarze roboczym podane.|
|/workspaces/linkedServices/write|Utwórz/zaktualizuj połączone usługi danego obszaru roboczego.|
|/workspaces/listKeys/Action|Pobiera listę kluczy dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego.|
|/workspaces/listKeys/read|Pobiera listę kluczy dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego.|
|/workspaces/managementGroups/Read|Pobiera nazwy i metadanych dla grupy zarządzania programu System Center Operations Manager podłączone do tego obszaru roboczego.|
|/workspaces/metricDefinitions/Read|Pobierz definicje metryki w obszarze roboczym|
|/workspaces/notificationSettings/delete|Usuń użytkownika ustawienia powiadomień w obszarze roboczym.|
|/workspaces/notificationSettings/read|Pobierz ustawienia powiadomień użytkownika dla obszaru roboczego.|
|/workspaces/notificationSettings/write|Określ ustawienia powiadomień użytkownika dla obszaru roboczego.|
|/workspaces/PURGE/Action|Usuń określone dane z obszaru roboczego|
|/workspaces/Read|Pobiera istniejący obszar roboczy|
|/workspaces/savedSearches/delete|Usuwa kwerendę zapisanego wyszukiwania|
|/workspaces/savedSearches/read|Pobiera zapytanie zapisanego wyszukiwania|
|/workspaces/savedSearches/write|Tworzy zapytanie zapisanego wyszukiwania|
|/workspaces/schema/read|Pobiera schematu wyszukiwania dla obszaru roboczego.  Schematu wyszukiwania zawiera narażonych pól i ich typów.|
|/workspaces/Search/Action|Wykonuje zapytanie wyszukiwania|
|/workspaces/sharedKeys/Action|Pobiera klucze udostępnionego dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego.|
|/workspaces/sharedKeys/Read|Pobiera klucze udostępnionego dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego.|
|/workspaces/storageinsightconfigs/delete|Usuwa konfiguracji magazynu. Przestanie Microsoft Operational Insights odczytywanie danych z konta magazynu.|
|/workspaces/storageinsightconfigs/read|Pobiera konfiguracji magazynu.|
|/workspaces/storageinsightconfigs/write|Tworzy nową konfigurację magazynu. Te konfiguracje są używane do pobierania danych z lokalizacji w istniejącego konta magazynu.|
|/workspaces/Usages/Read|Pobiera dane użycia dla obszaru roboczego, w tym ilość danych do odczytu w obszarze roboczym.|
|/ workspaces/zapisu|Tworzy nowy obszar roboczy lub łączy z istniejącym obszarem roboczym, podając identyfikator klienta z istniejącym obszarem roboczym.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Operacja | Opis |
|---|---|
|/managementAssociations/delete|Usuń istniejące skojarzenie zarządzania|
|/managementAssociations/read|Pobierz istniejące skojarzenie zarządzania|
|/managementAssociations/write|Utwórz nowe skojarzenie zarządzania|
|/managementConfigurations/DELETE|Usuń istniejące Configuratin zarządzania|
|/managementConfigurations/Read|Pobierz istniejące konfiguracji zarządzania|
|/ managementConfigurations/zapisu|Utwórz nową konfigurację zarządzania|
|/ register/działania|Zarejestruj subskrypcję dostawcy zasobów.|
|/Solutions/DELETE|Usuń istniejące rozwiązanie OMS|
|/Solutions/Read|Pobierz Kończenie rozwiązania OMS|
|/ rozwiązań/zapisu|Utwórz nowe rozwiązanie OMS|

## <a name="microsoftportal"></a>Microsoft.Portal

| Operacja | Opis |
|---|---|
|/Dashboards/DELETE|Usuwa pulpit nawigacyjny z subskrypcji.|
|/Dashboards/Read|Odczytuje pulpity nawigacyjne z subskrypcji.|
|/ Pulpity nawigacyjne/zapisu|Dodaj pulpit nawigacyjny do subskrypcji lub zmodyfikuj go.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Operacja | Opis |
|---|---|
|/capacities/checkNameAvailability/action|Sprawdza, czy podany Power BI w wersji dedykowanej pojemności nazwa jest prawidłowa, a nie korzystać.|
|/capacities/DELETE|Usuwa usługi Power BI w wersji dedykowanej pojemności.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki Power BI w wersji dedykowanej pojemności.|
|/capacities/Read|Pobiera informacje o pojemności określonego Power BI w wersji dedykowanej.|
|/ pojemności/zapisu|Tworzy lub aktualizuje określony Power BI w wersji dedykowanej pojemność.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Operacja | Opis |
|---|---|
|/locations/allocatedStamp/read|Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę|
|/Locations/allocateStamp/Action|AllocateStamp jest używane przez usługę operacji wewnętrznych|
|/Locations/backupPreValidateProtection/Action||
|/Locations/backupStatus/Action|Sprawdź stan kopii zapasowej dla Magazyny usług odzyskiwania|
|/Locations/backupValidateFeatures/Action|Sprawdź poprawność funkcji|
|/Operations/Read|Operacja zwraca listę operacji dla dostawcy zasobów|
|/ register/działania|Rejestruje subskrypcji danego dostawcy zasobów|
|/Vaults/backupconfig/read|Zwraca konfigurację Recovery usług magazynu.|
|/Vaults/backupconfig/write|Konfigurowanie aktualizacji odzyskiwania usług magazynu.|
|/Vaults/backupEngines/read|Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|Wyświetlanie wszystkich elementów w kontenerze|
|/Vaults/backupFabrics/backupProtectionIntent/write|Tworzenie kopii zapasowej opcje ochrony|
|/Vaults/backupFabrics/operationResults/read|Zwraca stan operacji|
|/Vaults/backupFabrics/protectableContainers/read|Pobierz wszystkie kontenery chronione|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Wykonaj zapytanie dla obciążeń w kontenerze|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Pobiera wynik operacji wykonanej na kontenerze ochrony.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Wykonuje kopię zapasową elementu chronionego.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Usuwa chronionego elementu|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Pobiera wynik operacji wykonanej na elementach chronionych.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Zwraca stan operacji wykonanej na elementach chronionych.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Zwraca szczegóły obiektu elemencie chroniony|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Odzyskiwanie elementów błyskawicznych udostępniania dla chronionego elementu|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Pobierz punkty odzyskiwania dla elementów chronionych.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/restore/action|Przywróć punkty odzyskiwania dla elementów chronionych.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Odwołanie elementu błyskawicznych odzyskiwania dla chronionego elementu|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Tworzenie kopii zapasowej elementu chronione|
|/Vaults/backupFabrics/protectionContainers/read|Zwraca wszystkich zarejestrowanych kontenerów|
|/Vaults/backupFabrics/protectionContainers/write|Tworzy kontener zarejestrowanych|
|/Vaults/backupFabrics/refreshContainers/action|Odświeża listę kontenera|
|/ Magazyny/backupJobs / / akcję anulowania|Anulowanie zadania|
|/Vaults/backupJobs/operationResults/read|Zwraca wynik operacji zadania.|
|/Vaults/backupJobs/read|Zwraca wszystkie obiekty zadania|
|/Vaults/backupJobsExport/action|Eksportowanie zadań|
|/Vaults/backupJobsExport/operationResults/read|Zwraca wynik operacji eksportowania zadania.|
|/Vaults/backupManagementMetaData/read|Zwraca metadane zarządzania kopiami zapasowymi magazynu usług Recovery Services.|
|/Vaults/backupOperationResults/read|Zwraca wynik operacji tworzenia kopii zapasowej magazynu usług Recovery Services.|
|/Vaults/backupOperations/read|Zwraca operacji tworzenia kopii zapasowej odzyskiwania stanu usługi magazynu.|
|/Vaults/backupPolicies/delete|Usuń zasady ochrony|
|/Vaults/backupPolicies/operationResults/read|Pobierz wyniki operacji zasad.|
|Magazyny/backupPolicies/operations/odczytu|Pobierz stan operacji zasad.|
|/Vaults/backupPolicies/read|Zwraca wszystkie zasady ochrony|
|/Vaults/backupPolicies/write|Tworzy zasady ochrony|
|/Vaults/backupProtectableItems/read|Zwraca listę wszystkich elementów podlegających ochronie|
|/Vaults/backupProtectedItems/read|Zwraca listę wszystkich elementów chronionych.|
|/Vaults/backupProtectionContainers/read|Zwraca wszystkie kontenery należących do subskrypcji|
|/ Magazyny/backupSecurityPIN/działania|Zwraca zabezpieczający numer PIN informacji odzyskiwania usług magazynu.|
|/Vaults/backupstorageconfig/read|Zwraca konfigurację magazynu dla odzyskiwania usług magazynu.|
|/Vaults/backupstorageconfig/write|Aktualizacje konfiguracji magazynu dla odzyskiwania usług magazynu.|
|/Vaults/backupUsageSummaries/read|Zwraca podsumowania dla chronionych serwerów i chronione elementy usług odzyskiwania.|
|/Vaults/certificates/write|Operacja aktualizacji zasobów certyfikatu aktualizuje certyfikat poświadczeń zasobów/magazynu.|
|/Vaults/delete|Operacja usuwania magazynu usuwa określony zasób Azure typu "magazynu"|
|/Vaults/extendedInformation/delete|Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn”|
|/Vaults/extendedInformation/read|Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn”|
|/Vaults/extendedInformation/write|Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn”|
|/Vaults/monitoringAlerts/read|Pobiera alertów dla magazynu usług odzyskiwania.|
|/Vaults/monitoringAlerts/write|Usuwa alert.|
|/Vaults/monitoringConfigurations/read|Pobiera konfigurację powiadomień magazynu usług odzyskiwania.|
|/Vaults/monitoringConfigurations/write|Konfiguruje powiadomienia pocztą e-mail do magazynu usług odzyskiwania.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Diagnostyka kopia zapasowa Azure|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Diagnostyka kopia zapasowa Azure|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Dzienniki kopii zapasowych Azure|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Metryki kopii zapasowej systemu Azure|
|/Vaults/read|Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu"|
|/Vaults/registeredIdentities/delete|Operacja wyrejestrować kontenera można wyrejestrować kontenera.|
|/Vaults/registeredIdentities/operationResults/read|Wyniki operacji Get operacji można używać Pobierz stan operacji i wynik operacji asynchronicznie przesłane|
|/Vaults/registeredIdentities/read|Kontenery pobrać operacji można używać Pobierz kontenery zarejestrowanych dla każdego zasobu.|
|/Vaults/registeredIdentities/write|Operacji zarejestrować kontenera usług można używać do rejestrowania kontener z usługą odzyskiwania.|
|/vaults/replicationAlertSettings/read|Wszystkie ustawienia alertów do odczytu|
|/vaults/replicationAlertSettings/write|Utwórz lub zaktualizuj ustawienia alertów|
|/vaults/replicationEvents/read|Przeczytaj żadnych zdarzeń|
|/vaults/replicationFabrics/checkConsistency/action|Sprawdza spójność sieci szkieletowej|
|/vaults/replicationFabrics/delete|Usuń wszystkie sieci szkieletowe|
|/vaults/replicationFabrics/deployProcessServerImage/action|Wdróż obraz serwera przetwarzania|
|/vaults/replicationFabrics/read|Żadnych sieci szkieletowych do odczytu|
|/vaults/replicationFabrics/reassociateGateway/action|Ponownie Skojarz bramy|
|/vaults/replicationFabrics/remove/action|Usunięcie sieci szkieletowej|
|/vaults/replicationFabrics/renewcertificate/action|Odnów certyfikat sieci szkieletowej|
|/vaults/replicationFabrics/replicationNetworks/read|Przeczytaj żadnych sieci|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Usuń wszelkie mapowania sieci|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Przeczytaj wszelkie mapowania sieci|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Utwórz lub zaktualizuj wszystkie mapowania sieci|
|/vaults/replicationFabrics/replicationProtectionContainers/ discoverProtectableItem/action|Odkryj element chronione|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Wszystkie kontenery ochrony|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Usunięcie kontenera ochrony|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectableItems/read|Wszystkie chronione elementy do odczytu|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Zastosuj punktu odzyskiwania|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/delete|Usuń wszystkie chronione elementy|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Zatwierdzanie trybu failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Planowany tryb Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Wszystkie chronione elementy do odczytu|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/recoveryPoints/read|Przeczytaj punktów odzyskiwania replikacji|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Usuwanie chronionego elementu|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Naprawa replikacji|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/reProtect/action|Włącz ponownie ochronę chronionego elementu|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Testowanie trybu Failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Wyczyszczenia testu pracy awaryjnej|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Tryb failover|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Aktualizacja usługi mobilności|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/write|Utwórz lub zaktualizuj wszystkie chronione elementy.|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/delete|Usuwanie mapowań kontenera ochrony|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/read|Przeczytaj mapowań kontenera ochrony|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/remove/action|Usuń mapowanie kontenera ochrony|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectionContainerMappings/write|Utwórz lub zaktualizuj mapowań kontenera ochrony|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Kontener ochrony przełącznika|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Utwórz lub zaktualizuj kontenerach ochrony|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Usuwanie wszystkich dostawców usług odzyskiwania|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Read Any Recovery Services Providers|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/ refreshProvider/action|Odśwież dostawcę|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Usuń dostawcę usług odzyskiwania|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Utwórz lub zaktualizuj wszystkich dostawców usług odzyskiwania|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Przeczytaj wszystkie klasyfikacje magazynu|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/delete|Usuń wszelkie mapowania klasyfikacji magazynu|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/read|Wszelkie mapowania klasyfikacji magazynu do odczytu|
|/vaults/replicationFabrics/replicationStorageClassifications/ replicationStorageClassificationMappings/write|Utwórz lub zaktualizuj wszystkie mapowania klasyfikacji magazynu|
|/vaults/replicationFabrics/replicationvCenters/delete|Usuń wszystkie zadania|
|/vaults/replicationFabrics/replicationvCenters/read|Wszystkie zadania do odczytu|
|/vaults/replicationFabrics/replicationvCenters/write|Utwórz lub zaktualizuj wszystkie zadania|
|/vaults/replicationFabrics/write|Utwórz lub zaktualizuj żadnych sieci szkieletowych|
|/vaults/replicationJobs/cancel/action|Anuluj zadanie|
|/vaults/replicationJobs/read|Wszystkie zadania do odczytu|
|/vaults/replicationJobs/restart/action|Uruchom ponownie zadanie|
|/vaults/replicationJobs/resume/action|Wznów zadanie|
|/vaults/replicationPolicies/delete|Usuń wszystkie zasady|
|/vaults/replicationPolicies/read|Wszystkie zasady do odczytu|
|/vaults/replicationPolicies/write|Utwórz lub zaktualizuj wszystkie zasady|
|/vaults/replicationRecoveryPlans/delete|Usuń wszystkie plany odzyskiwania|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Zatwierdzanie trybu failover planu odzyskiwania|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Plan odzyskiwania usługi planowanego trybu Failover|
|/vaults/replicationRecoveryPlans/read|Wszystkie plany odzyskiwania do odczytu|
|/vaults/replicationRecoveryPlans/reProtect/action|Włącz ponownie ochronę planu odzyskiwania|
|/vaults/replicationRecoveryPlans/testFailover/action|Plan odzyskiwania trybu Failover testu|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Plan odzyskiwania testu czyszczenia trybu Failover|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plan odzyskiwania trybu failover|
|/vaults/replicationRecoveryPlans/write|Utwórz lub zaktualizuj wszystkie plany odzyskiwania|
|/Vaults/tokenInfo/read|Zwraca token informacje magazynie usług odzyskiwania.|
|/vaults/Usages/Read|Przeczytaj również użycia magazynu|
|Magazyny/użycia/odczytu|Zwraca szczegóły użycia magazynu usług Recovery Services.|
|/Vaults/vaultTokens/read|Magazyn Token operacji można uzyskać tokenu magazynu dla operacji poziomu wewnętrznej bazy danych magazynu.|
|/Vaults/write|Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn”|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Operacja | Opis |
|---|---|
|/checkNameAvailability/action|Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji.|
|/checkNamespaceAvailability/action|Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailabiltiy.|
|/Namespaces/authorizationRules/Action|Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest depricated. Można zaktualizować reguły autoryzacji Namespace zamiast tego użyj wywołania PUT. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/authorizationRules/DELETE|Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej. |
|/Namespaces/authorizationRules/listkeys/Action|Pobierz parametry połączenia z przestrzenią nazw|
|/Namespaces/authorizationRules/Read|Pobierz listę opisów reguł autoryzacji przestrzeni nazw.|
|/namespaces/authorizationRules/regenerateKeys/action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/Namespaces/authorizationRules/Write|Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze.|
|przestrzenie nazw/Delete|Usuń zasób przestrzeni nazw|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Pobiera autoryzacja klucze reguły dla odzyskiwania po awarii podstawowej przestrzeni nazw|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Pobierz awaryjnego odzyskiwania podstawowej Namespace dla reguły autoryzacji|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Sprawdzanie dostępności aliasu przestrzeni nazw w podanej subskrypcji.|
|/namespaces/disasterRecoveryConfigs/delete|Usuwa konfiguracji odzyskiwania po awarii, skojarzone z przestrzenią nazw. Tę operację można wywołać tylko za pomocą głównej przestrzeni nazw.|
|/namespaces/disasterRecoveryConfigs/failover/action|Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw.|
|/namespaces/disasterRecoveryConfigs/read|Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw.|
|/namespaces/disasterRecoveryConfigs/write|Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw.|
|/Namespaces/HybridConnections/authorizationRules/Action|Operacja aktualizowania HybridConnection. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji.|
|/Namespaces/HybridConnections/authorizationRules/DELETE|Operację można usunąć reguły autoryzacji HybridConnection|
|/Namespaces/HybridConnections/authorizationRules/listkeys/Action|Pobierz ciąg połączenia HybridConnection|
|/Namespaces/HybridConnections/authorizationRules/Read| Pobierz listę reguł autoryzacji HybridConnection|
|/Namespaces/HybridConnections/authorizationRules/regeneratekeys/Action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/Namespaces/HybridConnections/authorizationRules/Write|Tworzenie reguł autoryzacji HybridConnection i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia.|
|/Namespaces/HybridConnections/DELETE|Operacja usunąć HybridConnection zasobu|
|/Namespaces/HybridConnections/Read|Pobierz listę opisów zasobów HybridConnection|
|/Namespaces/HybridConnections/Write|Tworzenie lub aktualizacja HybridConnection właściwości.|
|/Namespaces/messagingPlan/Read|Pobiera Plan obsługi komunikatów dla przestrzeni nazw. Ten interfejs API jest przestarzały. Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego). Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/messagingPlan/Write|Aktualizuje wiadomości Plan dla przestrzeni nazw. Ten interfejs API jest przestarzały. Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego). Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/operationresults/Read|Pobierz stan operacji przestrzeni nazw|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Pobierz listę metryki Namespace opisów zasobów|
|/Namespaces/Read|Pobierz listę opisów zasobów przestrzeni nazw|
|/Namespaces/WcfRelays/authorizationRules/Action|Operacja aktualizowania WcfRelay. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji.|
|/namespaces/WcfRelays/authorizationRules/delete|Operację można usunąć reguły autoryzacji WcfRelay|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Pobierz ciąg połączenia WcfRelay|
|/namespaces/WcfRelays/authorizationRules/read| Pobierz listę reguł autoryzacji WcfRelay|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/namespaces/WcfRelays/authorizationRules/write|Tworzenie reguł autoryzacji WcfRelay i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia.|
|/Namespaces/WcfRelays/DELETE|Operacja usunąć WcfRelay zasobu|
|/Namespaces/WcfRelays/Read|Pobierz listę opisów zasobów WcfRelay|
|/Namespaces/WcfRelays/Write|Tworzenie lub aktualizacja WcfRelay właściwości.|
|/ przestrzenie nazw/zapisu|Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane.|
|/Operations/Read|Pobierz operacje|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów usługi Relay i włącza funkcję tworzenia zasobów usługi Relay|
|/ unregister/działania|Rejestruje subskrypcję dostawcy zasobów usługi Relay i włącza funkcję tworzenia zasobów usługi Relay|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Operacja | Opis |
|---|---|
|AvailabilityStatuses/bieżącego/odczytu|Pobiera stan dostępności określonego zasobu|
|/AvailabilityStatuses/read|Pobiera stany dostępności wszystkich zasobów w określonym zakresie|
|/ healthevent/działania|Oznacza zmianę stanu kondycji dla określonego zasobu|
|/healthevent/activated/Action|Oznacza zmianę stanu kondycji dla określonego zasobu|
|/healthevent/InProgress/action|Oznacza zmianę stanu kondycji dla określonego zasobu|
|/healthevent/pending/Action|Oznacza zmianę stanu kondycji dla określonego zasobu|
|/healthevent/Resolved/Action|Oznacza zmianę stanu kondycji dla określonego zasobu|
|/healthevent/Updated/action|Oznacza zmianę stanu kondycji dla określonego zasobu|
|/ register/działania|Rejestruje subskrypcję na potrzeby usługi Microsoft Resource Health|

## <a name="microsoftresources"></a>Microsoft.Resources

| Operacja | Opis |
|---|---|
|/checkResourceName/action|Sprawdź poprawność nazwy zasobu.|
|/Deployments/Cancel/Action|Anuluje wdrożenie.|
|/Deployments/DELETE|Usuwa wdrożenie.|
|/Deployments/Operations/Read|Pobiera operacje wdrażania lub wyświetla ich listę.|
|/Deployments/Read|Pobiera wdrożenia lub wyświetla ich listę.|
|/Deployments/Validate/Action|Weryfikuje wdrożenie.|
|/ wdrożeń/zapisu|Tworzy lub aktualizuje wdrożenie.|
|/links/delete|Usuwa link do zasobu.|
|/links/read|Pobiera linki do zasobów lub wyświetla ich listę.|
|/ łącza/zapisu|Tworzy lub aktualizuje link do zasobu.|
|/Marketplace/Purchase/Action|Pozwala kupić zasób w portalu Marketplace.|
|/Providers/Read|Pobiera listę dostawców.|
|/Resources/Read|Pobiera listę zasobów zgodnie z filtrami.|
|/Subscriptions/Locations/Read|Pobiera listę obsługiwanych lokalizacji.|
|/Subscriptions/operationresults/Read|Pobiera wyniki operacji subskrypcji.|
|/Subscriptions/Providers/Read|Pobiera dostawców zasobów lub wyświetla ich listę.|
|/Subscriptions/Read|Pobiera listę subskrypcji.|
|/subscriptions/resourceGroups/delete|Usuwa grupę zasobów i wszystkie zasoby w tej grupie.|
|/subscriptions/resourcegroups/deployments/operations/read|Pobiera operacje wdrażania lub wyświetla ich listę.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Pobiera stany operacji wdrażania lub wyświetla ich listę.|
|/subscriptions/resourcegroups/deployments/read|Pobiera wdrożenia lub wyświetla ich listę.|
|/subscriptions/resourcegroups/deployments/write|Tworzy lub aktualizuje wdrożenie.|
|/subscriptions/resourceGroups/moveResources/action|Przenosi zasoby z jednej grupy zasobów do innej.|
|/subscriptions/resourceGroups/read|Pobiera grupy zasobów lub wyświetla ich listę.|
|/subscriptions/resourcegroups/resources/read|Pobiera zasoby grupy zasobów.|
|/subscriptions/resourceGroups/validateMoveResources/action|Waliduj przeniesienie zasobów między grupami zasobów.|
|/subscriptions/resourceGroups/write|Tworzy lub aktualizuje grupę zasobów.|
|/Subscriptions/Resources/Read|Pobiera zasoby subskrypcji.|
|/subscriptions/tagNames/delete|Usuwa tag subskrypcji.|
|/subscriptions/tagNames/read|Pobiera tagi subskrypcji lub wyświetla ich listę.|
|/subscriptions/tagNames/tagValues/delete|Usuwa wartość tagu subskrypcji.|
|/Subscriptions/tagNames/tagValues/Read|Pobiera wartości tagów lub wyświetla ich listę.|
|/subscriptions/tagNames/tagValues/write|Dodaje wartość tagu subskrypcji.|
|/subscriptions/tagNames/write|Dodaje tag subskrypcji.|
|/tenants/Read|Pobiera listę dzierżawców.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Operacja | Opis |
|---|---|
|/jobcollections/DELETE|Usuwa kolekcję zadań.|
|/jobcollections/disable/Action|Wyłącza kolekcję zadań.|
|/jobcollections/enable/Action|Włącza kolekcję zadań.|
|/jobcollections/Jobs/DELETE|Usuwa zadanie.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Generuje definicję aplikacji logiki na podstawie zadania usługi Scheduler.|
|/jobcollections/Jobs/jobhistories/Read|Pobiera historię zadania.|
|/jobcollections/Jobs/Read|Pobiera zadanie.|
|/jobcollections/jobs/run/action|Uruchamia zadanie.|
|/jobcollections/Jobs/Write|Tworzy lub aktualizuje zadanie.|
|/jobcollections/Read|Pobierz kolekcję zadań|
|/ kolekcjach zadań/zapisu|Tworzy lub aktualizuje kolekcję zadań.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Operacja | Opis |
|---|---|
|/checkNameAvailability/action|Służy do sprawdzania dostępności nazwy usługi.|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów wyszukiwania i umożliwia tworzenie usługi wyszukiwania.|
|/searchServices/createQueryKey/action|Tworzy klucza zapytania.|
|/searchServices/delete|Usuwa usługę wyszukiwania.|
|/searchServices/diagnosticSettings/read|Pobiera diganostic ustawienie odczytu zasobu|
|/searchServices/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diganostic dla zasobu|
|/searchServices/listAdminKeys/action|Odczytuje kluczy administratora.|
|/searchServices/logDefinitions/read|Pobiera dostępne dzienniki usługi wyszukiwania|
|/searchServices/metricDefinitions/read|Pobiera dostępne metryki dla usługi wyszukiwania|
|/searchServices/queryKey/delete|Usuwa klucza zapytania.|
|/searchServices/queryKey/read|Odczytuje klucze zapytania.|
|/searchServices/read|Odczytuje usługi wyszukiwania.|
|/searchServices/regenerateAdminKey/action|Generuje klucz administratora.|
|/searchServices/Start/Action|Uruchamia usługę wyszukiwania.|
|/searchServices/stop/action|Zatrzymuje usługę wyszukiwania.|
|/searchServices/write|Tworzy lub aktualizuje usługę wyszukiwania.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Operacja | Opis |
|---|---|
|/alerts/read|Pobiera wszystkie alerty zabezpieczeń dostępne|
|/applicationWhitelistings/read|Pobiera whitelistings aplikacji|
|/applicationWhitelistings/write|Tworzy nowy listę dozwolonych aplikacji podobnej lub aktualizuje istniejący zestaw|
|/complianceResults/read|Pobiera wyniki oceny zgodności dla zasobu|
|/Locations/Alerts/Activate/Action|Aktywuj alert zabezpieczeń|
|/Locations/Alerts/dismiss/Action|Odrzuć alert zabezpieczeń|
|/Locations/Alerts/Read|Pobiera wszystkie alerty zabezpieczeń dostępne|
|/locations/jitNetworkAccessPolicies/initiate/action|Inicjuje zasady dostępu do sieci w czasie|
|/locations/jitNetworkAccessPolicies/read|Pobiera zasady dostępu do sieci w czasie|
|/locations/jitNetworkAccessPolicies/write|Tworzy nowe zasady dostępu do sieci w czasie, lub aktualizuje istniejący zestaw|
|/Locations/Read|Pobiera lokalizację danych zabezpieczeń|
|/Locations/Tasks/Activate/Action|Aktywuj zalecana ze względów bezpieczeństwa|
|/Locations/Tasks/dismiss/Action|Odrzuć zalecana ze względów bezpieczeństwa|
|/Locations/Tasks/Read|Pobiera wszystkie zalecenia dotyczące zabezpieczeń dostępne|
|/Locations/Tasks/Resolve/Action|Rozwiąż zalecana ze względów bezpieczeństwa|
|/Locations/Tasks/Start/Action|Uruchom zalecana ze względów bezpieczeństwa|
|/Policies/Read|Pobiera zasady zabezpieczeń|
|/policies/write|Aktualizacje zasad zabezpieczeń|
|/pricings/delete|Usuwa cenową ustawienia dla zakresu|
|/pricings/read|Pobiera ustawienia cenową dla zakresu|
|/pricings/write|Zaktualizowanie ustawień cenową dla zakresu|
|/ register/działania|Rejestruje subskrypcję Centrum zabezpieczeń Azure|
|/securityContacts/delete|Usuwa kontaktu zabezpieczeń|
|/securityContacts/read|Pobiera kontaktu zabezpieczeń|
|/securityContacts/write|Aktualizacje zabezpieczeń skontaktuj się z|
|/securitySolutions/delete|Usuwa rozwiązania zabezpieczeń|
|/securitySolutions/read|Pobiera rozwiązań zabezpieczeń|
|/securitySolutions/write|Tworzy nowe rozwiązanie zabezpieczeń lub aktualizuje istniejący zestaw|
|/securitySolutionsReferenceData/read|Pobiera dane referencyjne rozwiązań zabezpieczeń|
|/securityStatuses/Read|Pobiera stany kondycji zabezpieczeń zasobów platformy Azure|
|/securityStatusesSummaries/Read|Pobiera podsumowań stanów w zakresie zabezpieczeń|
|/Tasks/Read|Pobiera wszystkie zalecenia dotyczące zabezpieczeń dostępne|
|/webApplicationFirewalls/delete|Usuwa zapory aplikacji sieci web|
|/webApplicationFirewalls/read|Pobiera zapory aplikacji sieci web|
|/webApplicationFirewalls/write|Nowa Zapora aplikacji sieci web tworzy lub aktualizuje istniejący zestaw|
|/workspaceSettings/connect/action|Zmień ustawienia ponowne nawiązanie połączenia ustawień obszaru roboczego|
|/workspaceSettings/delete|Usuwa ustawienia obszaru roboczego|
|/workspaceSettings/read|Pobiera ustawienia obszaru roboczego|
|/workspaceSettings/write|Zaktualizowanie ustawień obszaru roboczego|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Operacja | Opis |
|---|---|
|/checkNameAvailability/action|Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji.|
|/checkNamespaceAvailability/action|Sprawdza dostępność przestrzeni nazw w ramach danej subskrypcji. Ten interfejs API jest przestarzały. zamiast tego użyj CheckNameAvailabiltiy.|
|/Namespaces/authorizationRules/Action|Reguły autoryzacji Namespace aktualizacji. Ten interfejs API jest depricated. Można zaktualizować reguły autoryzacji Namespace zamiast tego użyj wywołania PUT. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/authorizationRules/DELETE|Usuń regułę autoryzacji Namespace. Nie można usunąć reguły autoryzacji Namespace domyślnej. |
|/Namespaces/authorizationRules/listkeys/Action|Pobierz parametry połączenia z przestrzenią nazw|
|/Namespaces/authorizationRules/Read|Pobierz listę opisów reguł autoryzacji przestrzeni nazw.|
|/namespaces/authorizationRules/regenerateKeys/action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/Namespaces/authorizationRules/Write|Tworzenie reguł autoryzacji z poziomu Namespace i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia, serwer podstawowy i klucze pomocnicze.|
|przestrzenie nazw/Delete|Usuń zasób przestrzeni nazw|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Pobiera autoryzacja klucze reguły dla odzyskiwania po awarii podstawowej przestrzeni nazw|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Pobierz awaryjnego odzyskiwania podstawowej Namespace dla reguły autoryzacji|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Wyłącza odzyskiwanie po awarii i zatrzymuje replikowanie zmian z podstawowych do pomocniczych przestrzeni nazw.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Sprawdzanie dostępności aliasu przestrzeni nazw w podanej subskrypcji.|
|/namespaces/disasterRecoveryConfigs/delete|Usuwa konfiguracji odzyskiwania po awarii, skojarzone z przestrzenią nazw. Tę operację można wywołać tylko za pomocą głównej przestrzeni nazw.|
|/namespaces/disasterRecoveryConfigs/failover/action|Wywołuje tryb pracy awaryjnej GEO DR i ponownie konfiguruje alias przestrzeni nazw, aby wskazywał pomocniczą przestrzeń nazw.|
|/namespaces/disasterRecoveryConfigs/read|Pobiera konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw.|
|/namespaces/disasterRecoveryConfigs/write|Tworzy lub aktualizuje konfigurację odzyskiwania po awarii skojarzoną z przestrzenią nazw.|
|/namespaces/eventGridFilters/delete|Usuwa filtr siatki zdarzenia związane z przestrzenią nazw.|
|/namespaces/eventGridFilters/read|Pobiera filtr siatki zdarzenia związane z przestrzenią nazw.|
|/namespaces/eventGridFilters/write|Tworzy lub aktualizuje filtr siatki zdarzenia związane z przestrzenią nazw.|
|/namespaces/eventhubs/read|Pobierz listę opisów zasobów EventHub|
|/Namespaces/messagingPlan/Read|Pobiera Plan obsługi komunikatów dla przestrzeni nazw. Ten interfejs API jest przestarzały. Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego). Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/messagingPlan/Write|Aktualizuje wiadomości Plan dla przestrzeni nazw. Ten interfejs API jest przestarzały. Właściwości udostępniane za pośrednictwem zasobów MessagingPlan zostaną przeniesione do zasobu Namespace w nowszej wersji interfejsu API (nadrzędnego). Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API.|
|/Namespaces/migrate/Action|Operacja migracji przestrzeni nazw|
|/Namespaces/operationresults/Read|Pobierz stan operacji przestrzeni nazw|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Pobierz listę Namespace ustawień diagnostycznych zasobu opisów|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Pobierz listę Namespace ustawień diagnostycznych zasobu opisów|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Pobierz listę dzienników Namespace opisów zasobów|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Pobierz listę metryki Namespace opisów zasobów|
|/namespaces/queues/authorizationRules/action|Operacja aktualizowania kolejki. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji.|
|/namespaces/queues/authorizationRules/delete|Operację można usunąć reguły autoryzacji kolejki|
|/namespaces/queues/authorizationRules/listkeys/action|Pobrać parametry połączenia do kolejki|
|/namespaces/queues/authorizationRules/read| Pobierz listę reguł autoryzacji kolejki|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/namespaces/queues/authorizationRules/write|Tworzenie reguł autoryzacji kolejki i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia.|
|/namespaces/queues/Delete|Operację można usunąć kolejki zasobów|
|/namespaces/queues/read|Pobierz listę opisów zasobów kolejki|
|/namespaces/queues/write|Utwórz lub właściwości kolejki aktualizacji.|
|/Namespaces/Read|Pobierz listę opisów zasobów przestrzeni nazw|
|/Namespaces/topics/authorizationRules/Action|Operacja aktualizowania tematu. Ta operacja nie jest obsługiwana w wersji 2017-04-01 interfejsu API. Reguły autoryzacji. Użyj wywołania PUT, aby zaktualizować reguły autoryzacji.|
|/namespaces/topics/authorizationRules/delete|Operację można usunąć reguły autoryzacji w temacie|
|/namespaces/topics/authorizationRules/listkeys/action|Pobrać parametry połączenia do tematu|
|/namespaces/topics/authorizationRules/read| Pobierz listę reguł autoryzacji tematu|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Ponownie wygeneruj klucz podstawowy lub pomocniczy dla zasobu|
|/namespaces/topics/authorizationRules/write|Tworzenie reguł autoryzacji tematu i aktualizowanie jej właściwości. Można zaktualizować reguły autoryzacji uprawnienia.|
|/Namespaces/topics/DELETE|Operację można usunąć zasobu tematu|
|/Namespaces/topics/Read|Pobierz listę opisów zasobów tematu|
|/namespaces/topics/subscriptions/Delete|Operacja usunąć TopicSubscription zasobu|
|/namespaces/topics/subscriptions/read|Pobierz listę opisów zasobów TopicSubscription|
|/namespaces/topics/subscriptions/rules/Delete|Operację można usunąć zasobu reguły|
|/namespaces/topics/subscriptions/rules/read|Pobierz listę reguł opisów zasobów|
|/namespaces/topics/subscriptions/rules/write|Tworzenie lub aktualizacja reguły właściwości.|
|/namespaces/topics/subscriptions/write|Tworzenie lub aktualizacja TopicSubscription właściwości.|
|/Namespaces/topics/Write|Tworzenie lub aktualizacja tematu właściwości.|
|/ przestrzenie nazw/zapisu|Utwórz zasób Namespace i zaktualizuj jego właściwości. Znaczniki i pojemności Namespace są właściwości, które mogą być aktualizowane.|
|/Operations/Read|Pobierz operacje|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów usługi ServiceBus i włącza funkcję tworzenia zasobów usługi ServiceBus|
|/SKU/Read|Pobierz listę opisów zasobów jednostki Sku|
|/SKU/regions/Read|Pobierz listę opisów zasobów SkuRegions|
|/ unregister/działania|Rejestruje subskrypcję dostawcy zasobów usługi ServiceBus i włącza funkcję tworzenia zasobów usługi ServiceBus|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Operacja | Opis |
|---|---|
|/Clusters/Applications/DELETE|Usuń dowolną aplikację|
|/Clusters/Applications/Read|Odczytaj dowolną aplikację|
|/Clusters/Applications/Services/DELETE|Usuń dowolną usługę|
|/Clusters/Applications/Services/Partitions/Read|Odczytaj dowolną partycję|
|/Clusters/Applications/Services/Partitions/Replicas/Read|Odczytaj dowolną replikę|
|/Clusters/Applications/Services/Read|Odczytaj dowolną usługę|
|/Clusters/Applications/Services/statuses/Read|Odczytaj stan dowolnej usługi|
|/Clusters/Applications/Services/Write|Utwórz lub zaktualizuj dowolną usługę|
|/Clusters/Applications/Write|Utwórz lub zaktualizuj dowolną aplikację|
|/Clusters/applicationTypes/DELETE|Usuń dowolny typ aplikacji|
|/clusters/applicationTypes/read|Odczytaj dowolny typ aplikacji|
|/Clusters/applicationTypes/Versions/DELETE|Usuń dowolną wersję typu aplikacji|
|/clusters/applicationTypes/versions/read|Odczytaj dowolną wersję typu aplikacji|
|/clusters/applicationTypes/versions/write|Utwórz lub zaktualizuj dowolną wersję typu aplikacji|
|/clusters/applicationTypes/write|Utwórz lub zaktualizuj dowolny typ aplikacji|
|/Clusters/DELETE|Usuń dowolny klaster|
|/clusters/nodes/read|Odczytaj dowolny węzeł|
|/Clusters/Read|Odczytaj dowolny klaster|
|/Clusters/statuses/Read|Odczytaj stan dowolnego klastra|
|/ klastrów/zapisu|Utwórz lub zaktualizuj dowolny klaster|
|/Locations/clusterVersions/Read|Odczytaj wersję dowolnego klastra|
|/Locations/environments/clusterVersions/Read|Odczytaj dowolną wersję klastra dla określonego środowiska|
|/Locations/operationresults/Read|Odczytaj dowolne wyniki operacji|
|/Locations/Operations/Read|Odczytaj dowolne operacje według lokalizacji|
|/Operations/Read|Odczytaj dowolne dostępne operacje|
|/ register/działania|Zarejestruj dowolną akcję|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Operacja | Opis |
|---|---|
|/applicationDefinitions/delete|Usuwa definicję aplikacji.|
|/applicationDefinitions/read|Pobiera listę definicji aplikacji.|
|/applicationDefinitions/write|Dodaj lub zmodyfikuj definicję aplikacji.|
|/Applications/DELETE|Usuwa aplikację.|
|/Applications/Read|Pobiera listę aplikacji.|
|/ applications/zapisu|Tworzy aplikację.|
|/Locations/operationStatuses/Read|Odczytuje stan operacji dla zasobu.|
|/ register/działania|Zarezerwuj w rozwiązaniach.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Operacja | Opis |
|---|---|
|/checkNameAvailability/action|Sprawdź, czy nazwa danego serwera jest dostępne do alokowania na całym świecie, w ramach danej subskrypcji.|
|/locations/auditingSettingsAzureAsyncOperation/read|Pobierz wynik obiektu blob serwera rozszerzonej inspekcji operacji zestawu zasad|
|/locations/auditingSettingsOperationResults/read|Pobieranie wyników inspekcji operacji zestawu zasad obiektu blob serwera|
|/Locations/Capabilities/Read|Pobiera możliwości dla tej subskrypcji w danej lokalizacji|
|/locations/databaseAzureAsyncOperation/read|Pobiera stan operacji bazy danych.|
|/locations/databaseOperationResults/read|Pobiera stan operacji bazy danych.|
|/locations/deletedServerAsyncOperation/read|Pobiera w trakcie wykonywania operacji na serwerze o usuniętych|
|/locations/deletedServerOperationResults/read|Pobiera w trakcie wykonywania operacji na serwerze o usuniętych|
|/locations/deletedServers/read|Zwraca listę usuniętych serwerów lub pobiera właściwości dla określonego serwera usunięte.|
|/locations/deletedServers/recover/action|Odzyskiwanie usuniętego serwera|
|/locations/deleteVirtualNetworkOrSubnets/action|Usuwa zasady sieci wirtualnej skojarzonych podsieci lub sieci wirtualnej|
|/locations/elasticPoolAzureAsyncOperation/read|Pobiera operacji asynchronicznej azure dla operacji asynchronicznej puli elastycznej|
|/locations/elasticPoolOperationResults/read|Pobiera wynik operacji puli elastycznej.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Pobierz wynik obiektu blob serwera rozszerzonej inspekcji operacji zestawu zasad|
|/locations/extendedAuditingSettingsOperationResults/read|Pobierz wynik obiektu blob serwera rozszerzonej inspekcji operacji zestawu zasad|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Kończy operację przywracania zarządzanej bazie danych|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Pobiera w trakcie wykonywania operacji na zarządzanej bazie danych przezroczystego szyfrowania danych|
|/locations/managedTransparentDataEncryptionOperationResults/read|Pobiera w trakcie wykonywania operacji na zarządzanej bazie danych przezroczystego szyfrowania danych|
|/Locations/Read|Pobiera dostępne lokalizacje w ramach danej subskrypcji|
|/locations/syncAgentOperationResults/read|Pobierz wynik operacji zasobów agenta synchronizacji|
|/locations/syncDatabaseIds/read|Pobierz identyfikatory bazy danych synchronizacji dla konkretnego regionu i subskrypcji|
|/locations/syncGroupOperationResults/read|Pobierz wynik operacji synchronizacji grupy zasobów|
|/locations/syncMemberOperationResults/read|Pobierz wynik operacji synchronizacji członek zasobów|
|/Locations/Usages/Read|Pobiera kolekcję metryki użycia dla tej subskrypcji w lokalizacji|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Zwraca szczegóły zasad sieci wirtualnej określonej operacji asynchronicznej azure |
|/locations/virtualNetworkRulesOperationResults/read|Zwraca szczegóły operacji reguły określonej sieci wirtualnej |
|/managedInstances/Administrators/DELETE|Usuwa istniejącego administratora zarządzanego wystąpienia.|
|/managedInstances/Administrators/Read|Pobiera listę zarządzanych wystąpienia administratorów.|
|/managedInstances/administrators/write|Tworzy lub aktualizuje administratora zarządzanego wystąpienia z określonymi parametrami.|
|/managedInstances/databases/delete|Usuwa istniejącą bazę danych zarządzanych|
|/managedInstances/databases/read|Pobiera istniejące zarządzanej bazie danych|
|/managedInstances/databases/securityAlertPolicies/read|Pobieranie szczegółów zasad wykrywania zagrożeń bazy danych skonfigurowanych dla danego zarządzanego bazy danych|
|/managedInstances/databases/securityAlertPolicies/write|Zmień zasady wykrywania zagrożeń bazy danych dla danego zarządzanego bazy danych|
|/managedInstances/databases/securityEvents/read|Pobiera zdarzenia zabezpieczeń zarządzanej bazie danych|
|/managedInstances/databases/transparentDataEncryption/read|Pobieranie szczegółów przezroczystego szyfrowania danych bazy danych dla danego zarządzanego bazy danych|
|/managedInstances/databases/transparentDataEncryption/write|Zmień bazę danych przezroczystego szyfrowania danych dla danego zarządzanego bazy danych|
|/managedInstances/databases/write|Tworzy nową bazę danych lub aktualizuje istniejącą bazę danych.|
|/managedInstances/DELETE|Usuwa istniejące wystąpienie zarządzanych.|
|/managedInstances/metricDefinitions/read|Pobierz definicje metryki zarządzanego wystąpienia|
|/managedInstances/metrics/read|Pobieraj metryki zarządzanego wystąpienia|
|/managedInstances/read|Zwraca listę zarządzanych wystąpień lub pobiera właściwości dla określonego wystąpienia zarządzane.|
|/managedInstances/securityAlertPolicies/read|Pobieranie szczegółów skonfigurowanych na danym serwerze zarządzanym zasad wykrywania zagrożeń serwerów zarządzanych|
|/managedInstances/securityAlertPolicies/write|Zmień zasady wykrywania zagrożeń serwera zarządzanego dla danego serwera zarządzanego|
|/managedInstances/write|Tworzy wystąpienie zarządzanego z określonymi parametrami lub zaktualizuj właściwości lub tagi dla określonego wystąpienia zarządzane.|
|/Operations/Read|Pobiera dostępne operacje REST|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów bazy danych Microsoft SQL i umożliwia tworzenie baz danych serwera SQL firmy Microsoft.|
|/servers/administratorOperationResults/read|Pobiera w trakcie wykonywania operacji na administratorom serwerów|
|/servers/administrators/delete|Usuwanie administratora serwera|
|/servers/administrators/read|Pobieranie szczegółów administratora serwera|
|/servers/administrators/write|Utwórz lub zaktualizuj administratora serwera|
|/servers/advisors/read|Zwraca listę doradcy jest dostępna dla serwera|
|/servers/advisors/recommendedActions/read|Zwraca listę zalecane działania advisor określonego serwera|
|/servers/advisors/recommendedActions/write|Zastosuj akcję zalecaną na serwerze|
|/servers/advisors/write|Aktualizacje automatyczne execute stanu usługi advisor na poziomie serwera.|
|/servers/auditingPolicies/read|Pobieranie szczegółów tabeli serwera domyślne zasady skonfigurowane na danym serwerze inspekcji|
|/servers/auditingPolicies/write|Zmień domyślny serwer tabeli inspekcji dla danego serwera|
|/servers/auditingSettings/operationResults/read|Pobieranie wyników inspekcji operacji zestawu zasad obiektu blob serwera|
|/servers/auditingSettings/read|Pobieranie szczegółów zasady inspekcji serwera obiektu blob skonfigurowane na danym serwerze|
|/servers/auditingSettings/write|Zmienianie serwera Inspekcja obiektów blob dla danego serwera|
|/servers/automaticTuning/read|Zwraca ustawienia automatycznego dostrajania dla serwera|
|/servers/automaticTuning/write|Aktualizuje ustawienia automatycznego dostrajania dla serwera i zwraca zaktualizowanymi ustawieniami|
|/servers/backupLongTermRetentionVaults/delete|Usuwa istniejące magazyn archiwizacji kopii zapasowych.|
|/servers/backupLongTermRetentionVaults/read|Ta operacja jest używany do pobierania magazyn przechowywania długoterminowej kopii zapasowej. Zwraca informacje o magazynie zarejestrowane na tym serwerze|
|/servers/backupLongTermRetentionVaults/write|Ta operacja jest używana do rejestrowania przechowywania długoterminowej kopii zapasowej magazynu na serwerze|
|/servers/communicationLinks/delete|Usuwa istniejące łącze komunikacji serwera.|
|/servers/communicationLinks/read|Zwraca listę łączy komunikacji z określonego serwera.|
|/servers/communicationLinks/write|Utwórz lub zaktualizuj łącza komunikacyjnego serwera.|
|/servers/connectionPolicies/read|Zwraca listę zasad połączenia serwera z określonego serwera.|
|/servers/connectionPolicies/write|Utwórz lub zaktualizuj zasady połączeń serwera.|
|/servers/databases/advisors/read|Zwraca listę doradcy jest dostępna dla bazy danych|
|/servers/databases/advisors/recommendedActions/read|Zwraca listę zalecane działania advisor określonej bazy danych|
|/servers/databases/advisors/recommendedActions/write|Zastosuj zalecaną akcję w bazie danych|
|/servers/databases/advisors/write|Aktualizacja automatycznego wykonywania stanu usługi advisor na poziomie bazy danych.|
|/servers/databases/auditingPolicies/read|Pobieranie szczegółów tabeli zasad inspekcji skonfigurowany w danej bazie danych|
|/servers/databases/auditingPolicies/write|Zmień zasady inspekcji tabeli określonej bazy danych|
|/servers/databases/auditingSettings/read|Pobieranie szczegółów obiektu blob zasad inspekcji skonfigurowany w danej bazie danych|
|/servers/databases/auditingSettings/write|Zmień zasady inspekcji obiektu blob określonej bazy danych|
|/servers/databases/auditRecords/read|Pobieranie rekordów inspekcji bazy danych obiektów blob|
|/servers/databases/automaticTuning/read|Zwraca ustawienia automatycznego dostrajania dla bazy danych|
|/servers/databases/automaticTuning/write|Aktualizuje ustawienia automatycznego dostrajania dla bazy danych i zwraca zaktualizowanymi ustawieniami|
|/servers/databases/azureAsyncOperation/read|Pobiera stan operacji bazy danych.|
|/servers/databases/backupLongTermRetentionPolicies/read|Zwraca listę archiwizacji zasad tworzenia kopii zapasowych określonej bazy danych.|
|/servers/databases/backupLongTermRetentionPolicies/write|Utwórz lub zaktualizuj zasady archiwizacji kopii zapasowej bazy danych.|
|/servers/databases/connectionPolicies/read|Pobieranie szczegółów zasady połączeń skonfigurowany w danej bazie danych|
|/servers/databases/connectionPolicies/write|Zmień zasady połączenia dla danego bazy danych|
|/servers/databases/dataMaskingPolicies/read|Zwraca listę maskowania zasad danych bazy danych.|
|/servers/databases/dataMaskingPolicies/rules/delete|Usuń dane maskowania reguły określonej bazy danych|
|/servers/databases/dataMaskingPolicies/rules/read|Pobieranie szczegółów danych maskowania regułę skonfigurowany w danej bazie danych|
|/servers/databases/dataMaskingPolicies/rules/write|Zmień dane maskowania reguły określonej bazy danych|
|/servers/databases/dataMaskingPolicies/write|Zmień zasady dla danych maskowania danych|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Zwraca informacje krok zapytań rozproszonych zapytania magazynu danych dla Identyfikatora wybrany krok|
|/servers/databases/dataWarehouseQueries/read|Zwraca informacje zapytania dystrybucji magazynu danych dla Identyfikatora wybranego zapytania|
|/servers/databases/dataWarehouseUserActivities/read|Pobiera aktywności użytkowników wystąpienia SQL Data Warehouse, która zawiera kwerendy działa i jest wstrzymany|
|/Servers/Databases/DELETE|Usuwa istniejącą bazę danych.|
|/servers/databases/export/action|Eksportowanie bazy danych Azure SQL|
|/servers/databases/extendedAuditingSettings/read|Pobieranie szczegółów zasady inspekcji rozszerzonego obiektu blob skonfigurowany w danej bazie danych|
|/servers/databases/extendedAuditingSettings/write|Zmień zasady inspekcji rozszerzonego obiektu blob określonej bazy danych|
|/servers/databases/extensions/read|Pobiera kolekcję rozszerzeń dla bazy danych.|
|/servers/databases/extensions/write|Zmień rozszerzenie określonej bazy danych|
|/servers/databases/geoBackupPolicies/read|Pobieranie zasad tworzenia kopii zapasowych geograficznie określonej bazy danych|
|/servers/databases/geoBackupPolicies/write|Utwórz lub zaktualizuj zasady geobackup bazy danych|
|/servers/databases/importExportOperationResults/read|Pobiera w trakcie operacji importu/eksportu|
|/servers/databases/metricDefinitions/read|Zwracane typy metryk, które są dostępne dla baz danych|
|/servers/databases/metrics/read|Zwraca metryki dla baz danych|
|/Servers/Databases/Move/Action|Zmień nazwę bazy danych Azure SQL|
|/servers/databases/operationResults/read|Pobiera stan operacji bazy danych.|
|/Servers/Databases/Operations/Cancel/Action|Anuluje bazy danych SQL Azure oczekująca operacja asynchroniczna, która nie jest jeszcze zakończone.|
|/servers/databases/operations/read|Zwraca listę operacji wykonywanych na bazie danych|
|/servers/databases/pause/action|Baza danych magazynu danych Azure SQL Wstrzymaj|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla baz danych|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Zwracane typy metryk, które są dostępne dla baz danych|
|/servers/databases/queryStore/queryTexts/read|Zwraca kolekcję teksty zapytania, które odpowiadają na określone parametry.|
|/servers/databases/queryStore/read|Zwraca bieżące wartości ustawień magazyn zapytań dla bazy danych.|
|/servers/databases/queryStore/write|Aktualizuje ustawienia magazynu zapytań dla bazy danych|
|/servers/databases/read|Zwraca listę baz danych lub pobiera właściwości dla określonej bazy danych.|
|/servers/databases/replicationLinks/delete|Zakończenie relacji replikacji wymuszone i o utracie danych|
|/servers/databases/replicationLinks/failover/action|Trybu failover po zsynchronizowaniu wszystkich zmian z serwera podstawowego udostępniania tę bazę danych do relationship\u0027s replikacji głównej i wprowadzania zdalnego podstawowej do dodatkowej|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Trybu failover natychmiast o utracie danych, co ta baza danych do relationship\u0027s replikacji głównej i wprowadzania zdalnego podstawowej do dodatkowej|
|/servers/databases/replicationLinks/read|Zwracany szczegółów dotyczących łącz replikacji dla określonej bazy danych|
|/servers/databases/replicationLinks/unlink/action|Zakończenie relacji replikacji wymuszone lub po synchronizacji z partnera|
|/servers/databases/replicationLinks/updateReplicationMode/action|Tryb aktualizacji replikacji dla łącza do trybu synchronicznego lub asynchronicznego|
|/servers/databases/restorePoints/action|Tworzy nowy punkt przywracania|
|/servers/databases/restorePoints/read|Zwraca przywrócić punkty dla bazy danych.|
|/servers/databases/resume/action|Baza danych magazynu danych Azure SQL Wznów|
|/servers/databases/schemas/read|Pobieranie listy schematów bazy danych|
|/servers/databases/schemas/tables/columns/read|Pobieranie listy kolumn tabeli.|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Usuń etykietę czułości danej kolumnie|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Pobierz etykiety ważności danego kolumny|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Utwórz lub zaktualizuj etykiety ważności danego kolumny|
|/servers/databases/schemas/tables/read|Pobieranie listy tabel bazy danych|
|/servers/databases/schemas/tables/recommendedIndexes/read|Pobieranie listy zalecenia dotyczące indeksu dla bazy danych|
|/servers/databases/schemas/tables/recommendedIndexes/write|Zastosuj zalecenie dotyczące indeksu|
|/servers/databases/securityAlertPolicies/read|Pobieranie szczegółów zasady wykrywania zagrożeń skonfigurowany w danej bazie danych|
|/servers/databases/securityAlertPolicies/write|Zmień zasady wykrywania zagrożeń określonej bazy danych|
|/servers/databases/securityMetrics/read|Pobiera kolekcję metryki zabezpieczeń bazy danych|
|/servers/databases/sensitivityLabels/read|Lista czułości etykiety danego bazy danych|
|/servers/databases/serviceTierAdvisors/read|Zwraca sugestii dotyczących skalowania bazy danych w górę lub w dół w oparciu statystyk wykonywania zapytań do zwiększenia wydajności i zmniejszenie kosztów|
|/servers/databases/syncGroups/cancelSync/action|Anulowanie synchronizacji grupy synchronizacji|
|/servers/databases/syncGroups/delete|Usuwa istniejącą grupę synchronizacji.|
|/servers/databases/syncGroups/hubSchemas/read|Zwraca listę synchronizacji schematów bazy danych Centrum|
|/servers/databases/syncGroups/logs/read|Zwraca listę dzienników grupy synchronizacji|
|/servers/databases/syncGroups/read|Zwraca listę synchronizacji grupy lub pobiera właściwości dla określonego synchronizacji grupy.|
|/servers/databases/syncGroups/refreshHubSchema/action|Odśwież schemat bazy danych Centrum synchronizacji|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Pobierz wynik operacji odświeżania schematu Centrum synchronizacji|
|/servers/databases/syncGroups/syncMembers/delete|Usuwa istniejącego elementu członkowskiego synchronizacji.|
|/servers/databases/syncGroups/syncMembers/read|Zwraca listę elementów synchronizacji lub pobiera właściwości dla synchronizacji określonego elementu członkowskiego.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Odśwież schemat elementu członkowskiego synchronizacji|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Pobierz wynik operacji odświeżania schematu synchronizacji elementu członkowskiego|
|/servers/databases/syncGroups/syncMembers/schemas/read|Zwraca listę synchronizacji schematów bazy danych elementu członkowskiego|
|/servers/databases/syncGroups/syncMembers/write|Tworzy element członkowski synchronizacji z określonymi parametrami lub zaktualizować właściwości synchronizacji określonego elementu członkowskiego.|
|/servers/databases/syncGroups/triggerSync/action|Wyzwalacz synchronizacji grupy synchronizacji|
|/servers/databases/syncGroups/write|Tworzy grupę synchronizacji z określonymi parametrami lub zaktualizować właściwości synchronizacji określonej grupy.|
|/servers/databases/topQueries/queryText/action|Zwraca tekst języka Transact-SQL dla wybranego zapytania o identyfikatorze|
|/servers/databases/topQueries/read|Zwraca zagregowane statystyk czasu wykonywania dla wybranego zapytania w wybranym okresie|
|/servers/databases/topQueries/statistics/read|Zwraca zagregowane statystyk czasu wykonywania dla wybranego zapytania w wybranym okresie|
|/servers/databases/transparentDataEncryption/operationResults/read|Pobiera w trakcie wykonywania operacji na przezroczystego szyfrowania danych|
|/servers/databases/transparentDataEncryption/read|Pobieranie stanu i szczegółowe informacje o funkcji zabezpieczeń szyfrowania danych przezroczysty określonej bazy danych|
|/servers/databases/transparentDataEncryption/write|Zmiany stanu szyfrowania danych przezroczyste|
|/servers/databases/upgradeDataWarehouse/action|Uaktualnianie bazy danych magazynu danych Azure SQL|
|/servers/databases/usages/read|Pobiera dane użycia bazy danych SQL Azure|
|/servers/databases/vulnerabilityAssessments/delete|Usuń oceny luk w zabezpieczeniach określonej bazy danych|
|/servers/databases/vulnerabilityAssessments/read|Pobieranie szczegółów oceny luk w zabezpieczeniach skonfigurowany w danej bazie danych|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Usuń linię bazową reguły oceny luk w zabezpieczeniach określonej bazy danych|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Pobierz bazowej reguły oceny luk w zabezpieczeniach określonej bazy danych|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Zmiany planu bazowego reguły oceny luk w zabezpieczeniach określonej bazy danych|
|/servers/databases/vulnerabilityAssessments/scans/action|Wykonywanie skanowania bazy danych w celu oceny luk w zabezpieczeniach.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Konwertowanie istniejących wyników skanowania na człowieka czytelnym formacie. Jeśli istnieje już nic się stanie,|
|/servers/databases/vulnerabilityAssessments/scans/read|Zwraca listę luki w zabezpieczeniach bazy danych rekordów skanowania oceny lub uzyskać rekordu skanowania skanowanie określonego identyfikatora.|
|/servers/databases/vulnerabilityAssessments/write|Zmień oceny luk w zabezpieczeniach określonej bazy danych|
|/servers/databases/vulnerabilityAssessmentScans/action|Wykonywanie skanowania bazy danych w celu oceny luk w zabezpieczeniach.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Pobieranie wyników skanowania oceny luk w zabezpieczeniach bazy danych operacji Execute|
|/servers/databases/vulnerabilityAssessmentSettings/read|Pobieranie szczegółów oceny luk w zabezpieczeniach skonfigurowany w danej bazie danych|
|/servers/databases/vulnerabilityAssessmentSettings/write|Zmień oceny luk w zabezpieczeniach określonej bazy danych|
|/servers/databases/write|Tworzy bazy danych z określonymi parametrami lub aktualizuje właściwości bądź tagi określonej bazy danych.|
|/servers/delete|Usuwa istniejącego serwera.|
|/servers/disasterRecoveryConfiguration/delete|Usuwa istniejące konfiguracjami odzyskiwania po awarii dla danego serwera|
|/servers/disasterRecoveryConfiguration/failover/action|Tryb failover DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Wymuszenie trybu Failover DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/read|Pobiera kolekcję po awarii z konfiguracjami odzyskiwania, które obejmują tego serwera|
|/servers/disasterRecoveryConfiguration/write|Konfiguracja odzyskiwania po awarii serwera zmiany|
|/servers/elasticPoolEstimates/read|Zwraca listę już utworzone dla tego serwera oszacowania puli elastycznej|
|/servers/elasticPoolEstimates/write|Tworzy nowego szacowania puli elastycznej dla listy baz danych, pod warunkiem|
|/servers/elasticPools/advisors/read|Zwraca listę doradcy jest dostępna dla puli elastycznej|
|/servers/elasticPools/advisors/recommendedActions/read|Zwraca listę zalecane działania advisor określony dla puli elastycznej|
|/servers/elasticPools/advisors/recommendedActions/write|Zastosuj akcję zalecaną dla elastycznej puli|
|/servers/elasticPools/advisors/write|Aktualizacja automatycznego wykonywania stanu usługi advisor na poziomie puli elastycznej.|
|/servers/elasticPools/databases/read|Pobiera listę baz danych dla puli elastycznej|
|/servers/elasticPools/delete|Usuń istniejącej puli elastycznej|
|/servers/elasticPools/elasticPoolActivity/read|Pobieranie działań i szczegółowe informacje o puli elastycznej bazy danych|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Pobieranie działań i szczegóły dotyczące danego bazy danych, która jest częścią puli elastycznej bazy danych|
|/servers/elasticPools/metricDefinitions/read|Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych|
|/servers/elasticPools/metrics/read|Zwraca metryki dla elastycznych pul baz danych|
|/servers/elasticPools/operations/cancel/action|Anuluje puli elastycznej Azure SQL oczekująca operacja asynchroniczna, która nie jest jeszcze zakończone.|
|/servers/elasticPools/operations/read|Zwraca listę operacji wykonywanych na puli elastycznej|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Zwracane typy metryk, które są dostępne dla elastycznych pul baz danych|
|/servers/elasticPools/read|Pobieranie szczegółów puli elastycznej na danym serwerze|
|/servers/elasticPools/skus/read|Pobiera kolekcję dostępne jednostki SKU dla tej puli elastycznej|
|/servers/elasticPools/write|Utwórz nową lub zmodyfikować właściwości istniejącej puli elastycznej|
|/servers/encryptionProtector/read|Zwraca listę protectors szyfrowania serwera lub pobiera właściwości dla określonego serwera ochrony szyfrowania.|
|/servers/encryptionProtector/write|Zaktualizuj właściwości dla określonego ochrony szyfrowania serwera.|
|/servers/extendedAuditingSettings/read|Pobieranie szczegółów obiektu blob serwera rozszerzonej skonfigurowane na danym serwerze zasady inspekcji|
|/servers/extendedAuditingSettings/write|Zmienianie serwera rozszerzonej Inspekcja obiektów blob dla danego serwera|
|/servers/failoverGroups/delete|Usuwa istniejącą grupę trybu failover.|
|/Servers/failoverGroups/Failover/Action|Wykonuje planowany tryb failover w istniejącej grupie trybu failover.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Wykonuje wymuszenia pracy awaryjnej w istniejącej grupie trybu failover.|
|/servers/failoverGroups/read|Zwraca listę trybu failover grupy lub pobiera właściwości dla określonego trybu failover grupy.|
|/servers/failoverGroups/write|Tworzy grupę trybu failover z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego trybu failover grupy.|
|/servers/firewallRules/delete|Usuwa istniejącą regułę zapory serwera.|
|/servers/firewallRules/read|Zwraca listę Zapora serwera reguły lub pobiera właściwości dla określonego serwera reguły zapory.|
|/servers/firewallRules/write|Powoduje utworzenie reguły zapory serwera z określonymi parametrami, należy zaktualizować właściwości określonej reguły lub zastąpienie wszystkich istniejących reguł nowych reguł zapory serwera.|
|/servers/import/action|Utwórz nową bazę danych na serwerze i wdrażanie schemat i dane z pakietu DacPac|
|/servers/importExportOperationResults/read|Pobiera w trakcie operacji importu/eksportu|
|/servers/keys/delete|Usuwa istniejący klucz serwera.|
|/servers/keys/read|Zwraca listę serwera kluczy lub pobiera właściwości dla klucza określonego serwera.|
|/servers/keys/write|Tworzy klucz z określonymi parametrami lub aktualizuje właściwości bądź tagi dla klucza określonego serwera.|
|/servers/operationResults/read|Pobiera w trakcie operacji serwera|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Zwracane typy metryk, które są dostępne dla serwerów|
|/servers/read|Zwraca listę serwerów lub pobiera właściwości dla określonego serwera.|
|/servers/recommendedElasticPools/databases/read|Pobrać metryki dla zalecane elastyczne pule baz danych dla danego serwera|
|/servers/recommendedElasticPools/read|Pobierz zalecenia dla elastycznych pul baz danych na zmniejszenie kosztów oraz pomagają w poprawieniu wydajności na podstawie wykorzystania zasobów historica|
|/servers/recoverableDatabases/read|Ta operacja jest używana do odzyskiwania po awarii na żywo bazy danych, aby przywrócić ostatnią znaną dobrą punktu kopii zapasowej bazy danych. Zwraca informacje o ostatniej dobrej kopii zapasowej, ale doesn\u0027t faktycznie przywrócić bazę danych.|
|/Servers/restorableDroppedDatabases/Read|Pobranie listy baz danych, które zostały usunięte na danym serwerze znajdujące się nadal w ramach zasad przechowywania.|
|/servers/securityAlertPolicies/operationResults/read|Pobierz wyniki serwerze operacji zapisu zasad wykrywania zagrożeń|
|/servers/securityAlertPolicies/read|Pobieranie szczegółów serwera zasad wykrywania zagrożeń skonfigurowane na danym serwerze|
|/servers/securityAlertPolicies/write|Zmień zasady wykrywania zagrożeń serwera dla danego serwera|
|/servers/serviceObjectives/read|Pobieranie listy celów poziomu usługi (znanej także jako warstwy wydajności) dostępnych na danym serwerze|
|/servers/syncAgents/delete|Usuwa istniejące agenta synchronizacji.|
|/servers/syncAgents/generateKey/action|Generowanie klucza Rejestracja agenta synchronizacji|
|/servers/syncAgents/linkedDatabases/read|Zwraca listę synchronizacji agenta połączone z baz danych|
|/servers/syncAgents/read|Zwraca listę agentów synchronizacji lub pobiera właściwości dla agenta określonego synchronizacji.|
|/servers/syncAgents/write|Tworzy agent synchronizacji z określonymi parametrami lub zaktualizować właściwości synchronizacji określonego agenta.|
|/Servers/Usages/Read|Zwraca limit przydziału jednostek dtu w warstwie serwera i bieżący consuption jednostek dtu w warstwie przez wszystkie bazy danych w ramach serwera|
|/servers/virtualNetworkRules/delete|Usuwa istniejącą regułę sieci wirtualnej|
|/servers/virtualNetworkRules/read|Zwraca listę sieci wirtualnej reguły lub pobiera właściwości dla reguły określonej sieci wirtualnej.|
|/servers/virtualNetworkRules/write|Tworzy regułę sieci wirtualnej z określonymi parametrami lub zaktualizować właściwości bądź tagi dla reguły określonej sieci wirtualnej.|
|/ serwery/zapisu|Tworzy serwer z określonymi parametrami lub aktualizuje właściwości bądź tagi dla określonego serwera.|
|/ unregister/działania|Wyrejestrowuje subskrypcji dla dostawcy zasobów bazy danych Microsoft SQL i umożliwia tworzenie baz danych serwera SQL firmy Microsoft.|
|/virtualClusters/read|Zwraca listę klastrów wirtualnych lub pobiera właściwości dla określonego klastra wirtualnego.|
|/virtualClusters/write|Aktualizuje tagi wirtualny klastra.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Operacja | Opis |
|---|---|
|/checknameavailability/Read|Sprawdza, czy nazwa konta jest prawidłowa i czy nie jest używana.|
|/locations/deleteVirtualNetworkOrSubnets/action|Powiadamia element Microsoft.Storage o usuwaniu sieci wirtualnej lub podsieci|
|/Operations/Read|Sonduje stan operacji asynchronicznej.|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów magazynu i umożliwia tworzenie kont magazynu.|
|/skus/read|Wyświetla listę jednostek SKU obsługiwanych przez zasób Microsoft.Storage.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Usuń stan archiwizacji ze względów prawnych dla kontenera obiektów blob|
|/storageAccounts/blobServices/containers/delete|Zwraca wynik usunięcia kontenera|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Usuń zasady niezmienności kontenera obiektów blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Rozszerz zasady niezmienności kontenera obiektów blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Zablokuj zasady niezmienności kontenera obiektów blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Uzyskaj zasady niezmienności kontenera obiektów blob|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Dodaj zasady niezmienności kontenera obiektów blob|
|/storageAccounts/blobServices/containers/read|Zwraca kontener lub listę kontenerów|
|/storageAccounts/blobServices/containers/setLegalHold/action|Ustaw stan archiwizacji ze względów prawnych dla kontenera obiektów blob|
|/storageAccounts/blobServices/containers/write|Zwraca wynik umieszczania lub dzierżawienia kontenera obiektów blob|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Uzyskaj listę definicji metryk magazynu firmy Microsoft.|
|/storageAccounts/blobServices/read|Zwraca statystyki lub wartości usługi Blob Service|
|/storageAccounts/blobServices/write|Zwraca wynik umieszczenia właściwości usługi obiektów blob|
|/storageAccounts/delete|Usuwa istniejące konto magazynu.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Uzyskaj listę definicji metryk magazynu firmy Microsoft.|
|/storageAccounts/listAccountSas/action|Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu.|
|/storageAccounts/listkeys/action|Zwraca klucze dostępu dla podanego konta magazynu.|
|/storageAccounts/listServiceSas/action|Zwraca token sygnatury dostępu współdzielonego usługi dla określonego konta magazynu.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Uzyskaj listę definicji metryk magazynu firmy Microsoft.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Uzyskaj listę definicji metryk magazynu firmy Microsoft.|
|/storageAccounts/queueServices/queues/delete|Zwraca wynik usunięcia kolejki|
|/storageAccounts/queueServices/queues/read|Zwraca kolejkę lub listę kolejek.|
|/storageAccounts/queueServices/queues/write|Zwraca wynik zapisania kolejki|
|/storageAccounts/queueServices/read|Zwraca statystyki lub wartości usługi kolejki.|
|/storageAccounts/queueServices/write|Zwraca wynik ustawienia właściwości usługi kolejki|
|/storageAccounts/read|Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu.|
|/storageAccounts/regeneratekey/action|Generuje ponownie klucze dostępu dla podanego konta magazynu.|
|/storageAccounts/services/diagnosticSettings/write|Utwórz lub zaktualizuj ustawienia diagnostyczne konta magazynu.|
|/storageAccounts/storageAccounts/queueServices/providers/ Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienia diagnostyczne dla zasobu.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Uzyskaj listę definicji metryk magazynu firmy Microsoft.|
|/storageAccounts/write|Tworzy konto magazynu z podanymi parametrami lub aktualizuje właściwości bądź tagi albo dodaje niestandardową domenę dla podanego konta magazynu.|
|/Usages/Read|Zwraca limit i bieżącą liczbę użyć dla zasobów w określonej subskrypcji|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Operacja | Opis |
|---|---|
|/storageSyncServices/delete|Usuń wszystkie usługi synchronizacji magazynu|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla usługi synchronizacji magazynu|
|/storageSyncServices/read|Wszystkie usługi synchronizacji magazynu do odczytu|
|/storageSyncServices/registeredServers/delete|Usuń wszelkie zarejestrowanego serwera|
|/storageSyncServices/registeredServers/read|Przeczytaj żadnych zarejestrowanego serwera|
|/storageSyncServices/registeredServers/write|Utwórz lub zaktualizuj wszystkie zarejestrowanego serwera|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Usuń wszystkie punkty końcowe chmury|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|Interfejs api lokalizacji dla tworzenia kopii zapasowej wywołania asynchroniczne|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Wywołaj tę akcję po utworzeniu kopii zapasowej|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Wywołaj tę akcję po przywróceniu|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Wywołanie przed kopii zapasowej|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Wywołanie przed przywróceniem|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Przeczytaj żadnych punktów końcowych w chmurze|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Przywróć pulsu|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Utwórz lub zaktualizuj żadnych punktów końcowych w chmurze|
|/storageSyncServices/syncGroups/delete|Usuń wszystkie grupy synchronizacji|
|/storageSyncServices/syncGroups/read|Przeczytaj grupy synchronizacji|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Usuń wszystkie punkty końcowe serwera|
|/storageSyncServices/syncGroups/serverEndpoints/read|Przeczytaj żadnych punktów końcowych serwera|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Wywołaj tę akcję, aby odwołać pliki na serwerze|
|/storageSyncServices/syncGroups/serverEndpoints/write|Utwórz lub zaktualizuj wszystkie punkty końcowe serwera|
|/storageSyncServices/syncGroups/write|Utwórz lub zaktualizuj wszystkie grupy synchronizacji|
|/storageSyncServices/write|Utwórz lub zaktualizuj wszystkie usługi synchronizacji magazynu|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Operacja | Opis |
|---|---|
|/managers/accessControlRecords/delete|Usuwa rekordy kontroli dostępu|
|/managers/accessControlRecords/read|Wyświetla listę lub pobiera rekordy kontroli dostępu|
|/managers/accessControlRecords/write|Utwórz lub zaktualizuj rekordy kontroli dostępu|
|/managers/alerts/read|Wyświetla listę lub pobiera alerty|
|/managers/bandwidthSettings/delete|Usuwa istniejące ustawienia przepustowości (8000 serii tylko)|
|/managers/bandwidthSettings/read|Wyświetl listę ustawień przepustowości (tylko 8000 Series)|
|/managers/bandwidthSettings/write|Tworzy nowy lub aktualizuje ustawienia przepustowości (8000 serii tylko)|
|Menedżerowie/certyfikatów/zapisu|Operacja aktualizacji zasobów certyfikatu aktualizuje certyfikat poświadczeń zasobów/magazynu.|
|/managers/clearAlerts/action|Wyczyść wszystkie alerty skojarzonych z Menedżerem urządzeń.|
|/managers/cloudApplianceConfigurations/read|Lista urządzenia chmury obsługiwane konfiguracje|
|/managers/configureDevice/action|Konfiguruje urządzenia|
|/managers/DELETE|Usuwa menedżerów urządzeń|
|/ Menedżerów/usuwania|Operacja usuwania magazynu usuwa określony zasób Azure typu "magazynu"|
|/managers/devices/alertSettings/read|Wyświetla listę lub pobiera ustawienia alertu|
|/managers/devices/alertSettings/write|Utwórz lub zaktualizuj ustawienia alertu|
|/managers/Devices/backupPolicies/Backup/Action|Tworzenie kopii zapasowej ręcznie utworzyć na żądanie kopii zapasowych wszystkich woluminów, które są chronione przez zasady.|
|/managers/Devices/backupPolicies/DELETE|Usuwa istniejące zasady tworzenia kopii zapasowej (8000 serii tylko)|
|/managers/Devices/backupPolicies/Read|Tworzenie kopii zapasowej listy zasad (tylko 8000 Series)|
|/managers/Devices/backupPolicies/Schedules/DELETE|Usuwa istniejących harmonogramów|
|/managers/Devices/backupPolicies/Schedules/Read|Lista harmonogramów|
|/managers/Devices/backupPolicies/Schedules/Write|Tworzy nowy lub aktualizuje harmonogramów|
|/managers/Devices/backupPolicies/Write|Tworzy nowy lub aktualizuje zasady tworzenia kopii zapasowej (8000 serii tylko)|
|/managers/Devices/Backups/DELETE|Usuwa zestaw kopii zapasowych|
|/managers/devices/backups/elements/clone/action|Klonowanie udziale lub woluminie przy użyciu kopii zapasowej elementu.|
|/managers/Devices/Backups/Read|Wyświetla listę lub pobiera zestawu kopii zapasowych|
|/managers/Devices/Backups/Restore/Action|Przywróć wszystkie woluminy z zestawu kopii zapasowych.|
|/managers/devices/backupScheduleGroups/delete|Usuwa harmonogram tworzenia kopii zapasowych grupy|
|/managers/devices/backupScheduleGroups/read|Wyświetla listę lub pobiera grup harmonogramu tworzenia kopii zapasowej|
|/managers/devices/backupScheduleGroups/write|Utwórz lub zaktualizuj grupy harmonogramu tworzenia kopii zapasowej|
|/managers/devices/chapSettings/delete|Usuwa ustawienia protokołu Chap|
|/managers/devices/chapSettings/read|Wyświetla listę lub pobiera ustawienia protokołu Chap|
|/managers/devices/chapSettings/write|Utwórz lub zaktualizuj ustawienia protokołu Chap|
|/managers/Devices/Deactivate/Action|Dezaktywuje urządzenia.|
|/managers/Devices/DELETE|Usuwa urządzenia|
|/managers/Devices/Download/Action|Pobierania aktualizacji dla urządzenia.|
|/managers/Devices/Failover/Action|Tryb pracy awaryjnej urządzenia.|
|/managers/Devices/fileservers/Backup/Action|Pobierz kopię zapasową serwera plików.|
|/managers/devices/fileservers/delete|Usuwa na serwerach plików|
|/managers/devices/fileservers/metrics/read|Wyświetla listę lub pobiera metryki|
|/managers/devices/fileservers/metricsDefinitions/read|Wyświetla listę lub pobiera definicje metryk|
|/managers/devices/fileservers/read|Wyświetla listę lub pobiera na serwerach plików|
|/managers/devices/fileservers/shares/delete|Usuwa akcji|
|/managers/devices/fileservers/shares/metrics/read|Wyświetla listę lub pobiera metryki|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Wyświetla listę lub pobiera definicje metryk|
|/managers/devices/fileservers/shares/read|Wyświetla listę lub pobiera akcji|
|/managers/devices/fileservers/shares/write|Utwórz lub zaktualizuj akcji|
|/managers/devices/fileservers/write|Utwórz lub zaktualizuj na serwerach plików|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Zmiany stanu zasilania kontrolera grup składników sprzętowych|
|/managers/devices/hardwareComponentGroups/read|Lista grup składników sprzętowych|
|/managers/devices/install/action|Zainstaluj aktualizacje na urządzeniu.|
|/managers/devices/installUpdates/action|Instaluje aktualizacje na urządzeniach|
|/managers/Devices/iscsiservers/Backup/Action|Pobierz kopię zapasową serwera iSCSI.|
|/managers/devices/iscsiservers/delete|Usuwa iSCSI serwerów|
|/managers/devices/iscsiservers/disks/delete|Usuwa dysków|
|/managers/devices/iscsiservers/disks/metrics/read|Wyświetla listę lub pobiera metryki|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Wyświetla listę lub pobiera definicje metryk|
|/managers/devices/iscsiservers/disks/read|Wyświetla listę lub pobiera dysków|
|/managers/devices/iscsiservers/disks/write|Utwórz lub zaktualizuj dysków|
|/managers/devices/iscsiservers/metrics/read|Wyświetla listę lub pobiera metryki|
|/managers/devices/iscsiservers/metricsDefinitions/read|Wyświetla listę lub pobiera definicje metryk|
|/managers/devices/iscsiservers/read|Wyświetla listę lub pobiera iSCSI serwerów|
|/managers/devices/iscsiservers/write|Utwórz lub zaktualizuj iSCSI serwerów|
|/managers/Devices/Jobs/Cancel/Action|Anuluj uruchomione zadania|
|/managers/Devices/Jobs/Read|Wyświetla listę lub pobiera zadania|
|/managers/devices/listFailoverSets/action|Lista zestawów trybu failover do istniejącego urządzenia.|
|/managers/Devices/listFailoverTargets/Action|Cele trybu failover listy urządzeń|
|/managers/Devices/Metrics/Read|Wyświetla listę lub pobiera metryki|
|/managers/Devices/metricsDefinitions/Read|Wyświetla listę lub pobiera definicje metryk|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Potwierdza udanej migracji, a następnie przekazać go.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Pobierz stan potwierdzenia migracji.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Pobierz stan zadania migracji szacowania.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Pobierz stan migracji.|
|/managers/devices/migrationSourceConfigurations/import/action|Importowanie konfiguracji źródłowego do migracji|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Uruchom migrację za pomocą konfiguracji źródła|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Uruchom zadanie, aby oszacować w czasie trwania procesu migracji.|
|/managers/devices/networkSettings/read|Wyświetla listę lub pobiera ustawienia sieci|
|/managers/devices/networkSettings/write|Tworzy nowy lub aktualizuje ustawienia sieci|
|/managers/devices/publicEncryptionKey/action|Lista publicznego klucza szyfrowania Menedżera urządzeń|
|/managers/devices/publishSupportPackage/action|Publikowanie pakietu obsługi urządzenia do firmy Microsoft Support rozwiązywania problemów.|
|/managers/Devices/Read|Wyświetla listę lub pobiera urządzenia|
|/managers/devices/scanForUpdates/action|Skanowanie w poszukiwaniu aktualizacji na urządzeniu.|
|/managers/devices/securitySettings/read|Wyświetl listę ustawień zabezpieczeń|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Synchronizuj certyfikat zdalnego zarządzania dla urządzenia.|
|/managers/devices/securitySettings/update/action|Zaktualizuj ustawienia zabezpieczeń.|
|/managers/devices/securitySettings/write|Tworzy nowy lub aktualizuje ustawienia zabezpieczeń|
|/managers/devices/sendTestAlertEmail/action|Wyślij testową wiadomość e-mail alertów do adresatów wiadomości e-mail skonfigurowany.|
|/managers/devices/timeSettings/read|Wyświetla listę lub pobiera ustawienia czasu|
|/managers/devices/timeSettings/write|Tworzy nowy lub aktualizuje ustawienia czasu|
|/managers/devices/updateSummary/read|Wyświetla listę lub pobiera aktualizacji — podsumowanie|
|/managers/devices/volumeContainers/delete|Usuwa istniejące kontenery woluminów (8000 serii tylko)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Lista kluczy szyfrowania woluminu kontenerów|
|/managers/devices/volumeContainers/metrics/read|Lista metryk|
|/managers/devices/volumeContainers/metricsDefinitions/read|Lista definicji metryk|
|/managers/devices/volumeContainers/read|Lista kontenery woluminów (tylko 8000 Series)|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Klucze szyfrowania przerzucania kontenery woluminów|
|/managers/devices/volumeContainers/volumes/delete|Usuwa istniejące woluminy|
|/managers/devices/volumeContainers/volumes/metrics/read|Lista metryk|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Lista definicji metryk|
|/managers/devices/volumeContainers/volumes/read|Wyświetl listę woluminów|
|/managers/devices/volumeContainers/volumes/write|Tworzy nowy lub aktualizuje woluminów|
|/managers/devices/volumeContainers/write|Tworzy nowy lub aktualizuje kontenery woluminów (8000 serii tylko)|
|/managers/Devices/Write|Utwórz lub zaktualizuj urządzenia|
|/managers/encryptionSettings/read|Wyświetla listę lub pobiera ustawienia szyfrowania|
|/Managers/extendedInformation/delete|Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn”|
|/Managers/extendedInformation/read|Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn”|
|/Managers/extendedInformation/write|Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn”|
|/managers/getActivationKey/action|Menedżer urządzeń, należy pobrać klucza aktywacji.|
|/managers/getEncryptionKey/action|Pobierz klucz szyfrowania dla Menedżera urządzeń.|
|/managers/listActivationKey/action|Pobiera klucz aktywacji Menedżera urządzeń StorSimple.|
|/managers/listPrivateEncryptionKey/action|Pobiera klucz prywatny szyfrowania dla Menedżera urządzeń StorSimple.|
|/managers/listPublicEncryptionKey/action|Lista kluczy szyfrowania publicznego z Menedżera urządzeń StorSimple.|
|/managers/Metrics/Read|Wyświetla listę lub pobiera metryki|
|/managers/metricsDefinitions/read|Wyświetla listę lub pobiera definicje metryk|
|/managers/provisionCloudAppliance/action|Utwórz nowe urządzenia chmury.|
|/managers/Read|Wyświetla listę lub pobiera menedżerów urządzeń|
|Menedżerowie/odczytu|Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu"|
|/managers/regenarateRegistationCertificate/action|Ponowne generowanie certyfikatu rejestracji dla menedżerów urządzeń.|
|/managers/regenerateActivationKey/action|Menedżer urządzeń, należy ponownie wygenerować klucza aktywacji.|
|/managers/storageAccountCredentials/delete|Usuwa poświadczenia konta magazynu|
|/managers/storageAccountCredentials/listAccessKey/action|Listy kluczy dostępu do poświadczeń konta magazynu|
|/managers/storageAccountCredentials/read|Wyświetla listę lub pobiera poświadczeń konta magazynu|
|/managers/storageAccountCredentials/write|Utwórz lub zaktualizuj poświadczenia konta magazynu|
|/managers/storageDomains/delete|Usuwa domen magazynu|
|/managers/storageDomains/read|Wyświetla listę lub pobiera domen magazynu|
|/managers/storageDomains/Write|Utwórz lub zaktualizuj domen magazynu|
|/ menedżerów/zapisu|Utwórz lub zaktualizuj Menedżera urządzeń|
|Menedżerowie/zapisu|Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn”|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Operacja | Opis |
|---|---|
|/Locations/Quotas/Read|Limit przydziału dla subskrypcji odczytu Stream Analytics|
|/ operations/odczytu|Operacji analizy strumienia odczytu|
|/ Register/działania|Zarejestrować subskrypcji u dostawcy zasobów usługi analiza strumienia|
|/ streamingjobs/Delete|Usuwanie zadania usługi analiza strumienia|
|/streamingjobs/functions/Delete|Usuń funkcję zadania usługi analiza strumienia|
|/streamingjobs/Functions/operationresults/Read|Wyniki operacji odczytu dla funkcji zadania usługi analiza strumienia|
|/streamingjobs/Functions/Read|Funkcję zadania usługi analiza strumienia odczytu|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Pobieranie definicji domyślnej funkcji zadania usługi analiza strumienia|
|/streamingjobs/functions/Test/action|Funkcja zadanie analizy strumienia testu|
|/streamingjobs/functions/Write|Write — funkcja zadania usługi analiza strumienia|
|/streamingjobs/inputs/DELETE|Usuń dane wejściowe zadania usługi analiza strumienia|
|/streamingjobs/inputs/operationresults/Read|Wyniki operacji odczytu dla danych wejściowych zadania usługi analiza strumienia|
|/streamingjobs/inputs/Read|Wejścia zadania usługi analiza strumienia odczytu|
|/streamingjobs/inputs/Sample/Action|Przykładowe Stream Analytics zadania w danych wejściowych|
|/streamingjobs/inputs/test/Action|Test Stream Analytics zadania w danych wejściowych|
|/streamingjobs/inputs/Write|Zapisu danych wejściowych zadania usługi analiza strumienia|
|/streamingjobs/metricdefinitions/Read|Odczyt definicji metryk|
|/streamingjobs/operationresults/Read|Wyniki operacji odczytu dla zadania usługi analiza strumienia|
|/streamingjobs/outputs/DELETE|Usuń dane wyjściowe zadania usługi analiza strumienia|
|/streamingjobs/outputs/operationresults/Read|Wyniki operacji odczytu dla danych wyjściowych zadania usługi analiza strumienia|
|/streamingjobs/outputs/Read|Odczytu Stream Analytics — dane wyjściowe zadania|
|/streamingjobs/outputs/test/Action|Danych wyjściowych zadania analizy strumienia testu|
|/streamingjobs/outputs/Write|Zapisywania danych wyjściowych zadania usługi analiza strumienia|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Ustawienie diagnostyczne dla odczytu.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Zapis ustawienie diagnostyczne.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Pobiera dostępne dzienniki dla streamingjobs|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla streamingjobs|
|/ streamingjobs/odczytu|Zadania usługi analiza strumienia odczytu|
|/streamingjobs/Start/Action|Uruchom zadania usługi analiza strumienia|
|/streamingjobs/Stop/Action|Stop Stream Analytics Job|
|/streamingjobs/transformations/Delete|Usuń przekształcenie zadania usługi analiza strumienia|
|/streamingjobs/transformations/Read|Odczyt Stream Analytics zadania przekształcania|
|/streamingjobs/transformations/Write|Zapis przekształcenie zadania usługi analiza strumienia|
|/ streamingjobs/zapisu|Zapis zadania usługi analiza strumienia|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Operacja | Opis |
|---|---|
|/SubscriptionDefinitions/read|Pobierz definicję subskrypcji platformy Azure, w grupie zarządzania.|
|/SubscriptionDefinitions/write|Utwórz definicję subskrypcji platformy Azure|

## <a name="microsoftsupport"></a>Microsoft.Support

| Operacja | Opis |
|---|---|
|/ register/działania|Przeprowadza rejestrację u dostawcy zasobów pomocy technicznej|
|/supportTickets/read|Pobiera szczegóły biletu pomocy technicznej (w tym stan, ważność, szczegóły dotyczące kontaktu i wiadomości) lub pobiera listę biletów pomocy technicznej dla różnych subskrypcji.|
|/supportTickets/write|Tworzy lub aktualizuje bilet pomocy technicznej. Można utworzyć biletu pomocy technicznej dla technicznych, rozliczeń, limity przydziału lub zarządzaniem subskrypcjami problemy związane z. Możesz zaktualizować ważność, szczegóły dotyczące kontaktu i komunikacji dla istniejących biletów pomocy technicznej.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Operacja | Opis |
|---|---|
|/environments/accesspolicies/DELETE|Usuwa zasady dostępu.|
|/environments/accesspolicies/Read|Pobierz właściwości zasad dostępu.|
|/environments/accesspolicies/Write|Tworzy nową zasadę dostępu dla środowiska, lub aktualizuje istniejące zasady dostępu.|
|/environments/DELETE|Usuwa środowiska.|
|/environments/eventsources/DELETE|Usuwa źródło zdarzenia.|
|/environments/eventsources/eventsources/Providers/Microsoft.Insights/ diagnosticSettings/zapisu|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla eventsources|
|/environments/eventsources/Read|Pobierz właściwości źródła zdarzenia.|
|/environments/eventsources/Write|Tworzy nowe źródło zdarzeń dla środowiska, lub aktualizuje istniejące źródło zdarzenia.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Pobiera dostępne metryki dla środowisk|
|/environments/Read|Pobierz właściwości środowiska.|
|/environments/referencedatasets/delete|Usuwa odwołanie do zestawu danych.|
|/environments/referencedatasets/read|Pobierz właściwości zestawu danych odwołania.|
|/environments/referencedatasets/write|Tworzy nowy zestaw danych odwołania dla środowiska, lub aktualizuje istniejący zestaw danych odwołania.|
|/environments/status/Read|Pobierz stan środowiska stanu jego skojarzony operacje, takie jak transfer danych przychodzących.|
|/ środowisk/zapisu|Tworzy nowe środowisko, lub aktualizuje istniejącego środowiska.|
|/ register/działania|Rejestruje subskrypcję dostawcy zasobów Insights serie czasu i umożliwia tworzenie środowisk Insights serii czasu.|

## <a name="microsoftweb"></a>microsoft.web

| Operacja | Opis |
|---|---|
|/apimanagementaccounts/apiacls/read|Pobierz interfejs Api zarządzania kontami Apiacls.|
|/apimanagementaccounts/apis/apiacls/delete|Usunięcie interfejsu Api zarządzania kontami interfejsów API Apiacls.|
|/apimanagementaccounts/APIs/apiacls/Read|Pobierz interfejs Api zarządzania kontami interfejsów API Apiacls.|
|/apimanagementaccounts/APIs/apiacls/Write|Aktualizowanie interfejsu Api zarządzania kontami interfejsów API Apiacls.|
|/apimanagementaccounts/apis/connectionacls/read|Pobierz interfejs Api zarządzania kontami interfejsów API Connectionacls.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Potwierdź zgody kodu interfejsu Api zarządzania kontami interfejsów API połączeń.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Usunięcie interfejsu Api zarządzania kontami interfejsów API połączeń Connectionacls.|
|/apimanagementaccounts/APIs/Connections/connectionacls/Read|Pobierz interfejs Api zarządzania kontami interfejsów API połączeń Connectionacls.|
|/apimanagementaccounts/APIs/Connections/connectionacls/Write|Aktualizowanie interfejsu Api zarządzania kontami interfejsów API połączeń Connectionacls.|
|/apimanagementaccounts/APIs/Connections/DELETE|Usunięcie interfejsu Api zarządzania kontami interfejsów API połączeń.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Pobierz łącza zgody dla interfejsu Api zarządzania kontami interfejsów API połączeń.|
|/apimanagementaccounts/APIs/Connections/listconnectionkeys/Action|Lista kluczy interfejsu Api zarządzania kont interfejsów API połączenia.|
|/apimanagementaccounts/APIs/Connections/listsecrets/Action|Lista kluczy tajnych interfejsu Api zarządzania kont interfejsów API połączeń.|
|/apimanagementaccounts/APIs/Connections/Read|Pobierz interfejs Api zarządzania kontami interfejsów API połączeń.|
|/apimanagementaccounts/APIs/Connections/Write|Aktualizowanie interfejsu Api zarządzania kontami interfejsów API połączeń.|
|/apimanagementaccounts/apis/delete|Usuń interfejsów API usługi Api Management kont.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Usuń usługi Api Management API kont zlokalizowane definicje.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Pobierz interfejs Api zarządzania interfejsów API kont zlokalizowane definicje.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Aktualizacja interfejsu Api zarządzania kontami API zlokalizowane definicje.|
|/apimanagementaccounts/apis/read|Pobierz interfejsów API usługi Api Management kont.|
|/apimanagementaccounts/APIs/Write|Zaktualizuj interfejsów API usługi Api Management kont.|
|/apimanagementaccounts/connectionacls/read|Pobierz interfejs Api zarządzania kontami Connectionacls.|
|/availablestacks/Read|Pobierz stosy dostępne.|
|/billingmeters/Read|Pobierz listę liczników rozliczeń.|
|/ certyfikatów/Delete|Usuwanie istniejącego certyfikatu.|
|/ certyfikatów/odczytu|Pobierz listę certyfikatów.|
|/ certyfikatów/zapisu|Dodaj nowy certyfikat lub zaktualizuj istniejącą.|
|/checknameavailability/Read|Sprawdź, czy nazwa zasobu jest dostępna.|
|/classicmobileservices/Read|Pobierz klasycznego usług mobilnych.|
|/connectionGateways/Delete|Usuwa bramę połączenia.|
|/connectionGateways/Join/Action|Tworzy sprzężenie bramy połączenia.|
|/connectiongateways/liststatus/action|Lista stanu połączenia bramy.|
|/connectionGateways/ListStatus/Action|Wyświetla stan połączenia bramy.|
|/connectionGateways/Move/Action|Przenosi bramy połączenia.|
|/connectionGateways/Read|Pobierz listę połączenia bramy.|
|/connectionGateways/Write|Tworzy lub aktualizuje bramy połączenia.|
|/connections/confirmconsentcode/action|Potwierdź kod zgody połączenia.|
|/ połączeń/Delete|Usuwa połączenie.|
|/Connections/JOIN/Action|Tworzy sprzężenie połączenia.|
|/connections/listconsentlinks/action|Lista łączy zgody dla połączeń.|
|/Connections/Move/Action|Przenosi połączenie.|
|/ połączeń/odczytu|Pobierz listę połączeń.|
|/ połączeń/zapisu|Tworzy lub aktualizuje połączenia.|
|/ customApis/Delete|Usuwa niestandardowego interfejsu API.|
|/customApis/extractApiDefinitionFromWsdl/Action|Pobiera definicję interfejsu API z WSDL.|
|/customApis/Join/Action|Tworzy sprzężenie niestandardowego interfejsu API.|
|/customApis/listWsdlInterfaces/Action|Zawiera listę WSDL interfejsów API niestandardowe.|
|/customApis/Move/Action|Przenosi niestandardowego interfejsu API.|
|/ customApis/odczytu|Pobierz listę niestandardowego interfejsu API.|
|/ customApis/zapisu|Tworzy lub aktualizuje API niestandardowe.|
|/deploymentlocations/Read|Uzyskanie lokalizacji wdrożenia.|
|/ geoRegions/odczytu|Pobierz listę regionów geograficznych.|
|/hostingenvironments/capacities/Read|Pobierz Hosting możliwości środowiska.|
|/hostingEnvironments/Delete|Usuwanie środowiska usługi aplikacji|
|/hostingenvironments/Diagnostics/Read|Pobierz Hosting środowisk diagnostyki.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Pobierz punktów końcowych sieci wszystkie zależności dla ruchu przychodzącego.|
|/hostingenvironments/metricdefinitions/read|Pobierz Hosting definicji metryk środowisk.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Pobierz Hosting definicji metryk pełniących wiele pul środowisk.|
|/hostingenvironments/multirolepools/metrics/read|Pobierz Hosting środowisk pełniących wiele pul metryki.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Pobiera dostępne metryki dla MultiRole środowiska usługi aplikacji|
|/hostingEnvironments/multiRolePools/Read|Pobierz właściwości puli frontonu w środowisku usługi aplikacji|
|/hostingenvironments/multirolepools/skus/read|Pobierz Hosting jednostki SKU pełniących wiele pul środowisk.|
|/hostingenvironments/multirolepools/usages/read|Pobierz Hosting środowisk pełniących wiele pul użycia.|
|/hostingEnvironments/multiRolePools/Write|Tworzenie nowej puli frontonu w środowisku usługi aplikacji lub zaktualizuj istniejącą|
|/hostingenvironments/Operations/Read|Pobierz Hosting operacji środowisk.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Pobierz punktów końcowych sieci wszystkie zależności ruchu wychodzącego.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/ hostingEnvironments/odczytu|Pobierz właściwości środowiska usługi aplikacji|
|/hostingEnvironments/reboot/Action|Ponownie wszystkie maszyny w środowisku usługi aplikacji|
|/hostingenvironments/resume/Action|Wznów środowiskach hostingu.|
|/hostingenvironments/serverfarms/read|Pobierz Hosting środowisk planów usługi aplikacji.|
|/hostingenvironments/Sites/Read|Pobierz aplikacje sieci Web środowiskach hostingu.|
|/hostingenvironments/suspend/Action|Wstrzymaj środowiskach hostingu.|
|/hostingenvironments/Usages/Read|Pobierz Hosting użycia środowiska.|
|/hostingenvironments/workerpools/metricdefinitions/read|Pobierz Hosting definicje Metryka Workerpools środowisk.|
|/hostingenvironments/workerpools/metrics/read|Pobierz Hosting środowisk Workerpools metryki.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Pobiera dostępne metryki dla WorkerPool środowiska usługi aplikacji|
|/hostingEnvironments/workerPools/Read|Pobierz właściwości puli procesów roboczych w środowisku usługi aplikacji|
|/hostingenvironments/workerpools/skus/Read|Pobierz Hosting jednostki SKU Workerpools środowisk.|
|/hostingenvironments/workerpools/Usages/Read|Pobierz Hosting środowisk Workerpools użycia.|
|/hostingEnvironments/workerPools/Write|Tworzenie nowej puli procesów roboczych w środowisku usługi aplikacji lub zaktualizuj istniejącą|
|/ hostingEnvironments/zapisu|Tworzenie nowego środowiska usługi aplikacji lub zaktualizować istniejący|
|/ishostingenvironmentnameavailable/read|GET, jeśli jest dostępna nazwa środowiska hostingu.|
|/ishostnameavailable/read|Sprawdź, czy nazwa hosta jest dostępny.|
|/isusernameavailable/read|Sprawdź, czy nazwa użytkownika jest dostępna.|
|/listSitesAssignedToHostName/Read|Pobierz nazwy lokacji przypisane do nazwy hosta.|
|/Locations/apioperations/Read|Pobierz operacje lokalizacje interfejsu API.|
|/locations/connectiongatewayinstallations/read|Pobierz lokalizacje połączenia bramy instalacji.|
|/locations/extractapidefinitionfromwsdl/action|Wyodrębnij definicji interfejsu Api z WSDL dla lokalizacji.|
|/locations/listwsdlinterfaces/action|Lista interfejsów WSDL dla lokalizacji.|
|/Locations/managedapis/apioperations/Read|Operacje zarządzanego interfejsu API lokalizacji pobierania.|
|/Locations/managedapis/JOIN/Action|Tworzy sprzężenie zarządzanego interfejsu API.|
|/Locations/managedapis/Read|Pobierz lokalizacje zarządzanych interfejsów API.|
|/Operations/Read|Pobierz operacje.|
|/publishingusers/read|Pobierz publikowanie użytkowników.|
|/publishingusers/write|Aktualizacja publikowania użytkowników.|
|/ zalecenia/odczytu|Pobierz listę zaleceń dotyczących subskrypcji.|
|/ register/działania|Rejestrowanie dostawcy zasobów Microsoft.Web dla subskrypcji.|
|/resourcehealthmetadata/read|Pobranie metadanych kondycji zasobów.|
|/serverfarms/Capabilities/Read|Skorzystaj z możliwości planów usługi aplikacji.|
|/serverfarms/Delete|Usuń istniejący Plan usługi App Service|
|/serverfarms/firstpartyapps/settings/delete|Usuń ustawienia aplikacje firm pierwszy planów usługi aplikacji.|
|/serverfarms/firstpartyapps/settings/read|Pobierz ustawienia aplikacje firm pierwszy planów usługi aplikacji.|
|/serverfarms/firstpartyapps/settings/write|Zaktualizuj ustawienia aplikacje firm pierwszy planów usługi aplikacji.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Usuwanie aplikacji usługi planów hybrydowego połączenia przestrzenie nazw przekaźników.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Pobierz usługi aplikacji planów hybrydowego połączenia przestrzenie nazw przekaźników.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Pobierz hybrydowego planów usługi aplikacji połączenia aplikacji sieci Web przekaźników przestrzeni nazw.|
|/serverfarms/hybridconnectionplanlimits/read|Pobiera limity planu połączeń hybrydowych planów usługi aplikacji.|
|/serverfarms/hybridconnectionrelays/read|Pobierz przekaźników połączenia hybrydowe planów usługi aplikacji.|
|/serverfarms/metricdefinitions/read|Pobierz definicje metryki planów usługi aplikacji.|
|/serverfarms/metrics/read|Pobieraj metryki planów usługi aplikacji.|
|/serverfarms/operationresults/read|Pobierz wyniki operacji planów usługi aplikacji.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Pobiera dostępne metryki dla planu usługi App Service|
|/ serverfarms/odczytu|Pobiera właściwości planu usługi App Service|
|/serverfarms/restartSites/Action|Uruchom ponownie wszystkie aplikacje sieci Web w planie usługi aplikacji|
|/serverfarms/Sites/Read|Pobierz aplikacje sieci Web planów usługi aplikacji.|
|/serverfarms/skus/read|Pobierz jednostki SKU planów usługi aplikacji.|
|/serverfarms/Usages/Read|Pobierz użycia planów usługi aplikacji.|
|/serverfarms/virtualnetworkconnections/gateways/write|Zaktualizuj bramy połączenia wirtualnej sieci planów usługi aplikacji.|
|/serverfarms/virtualnetworkconnections/Read|Pobrać połączeń sieci wirtualnej planów usługi aplikacji.|
|/serverfarms/virtualnetworkconnections/routes/delete|Usuń trasy połączenia wirtualnej sieci planów usługi aplikacji.|
|/serverfarms/virtualnetworkconnections/Routes/Read|Pobierz trasy połączenia wirtualnej sieci planów usługi aplikacji.|
|/serverfarms/virtualnetworkconnections/routes/write|Aktualizowanie tras połączenia wirtualnej sieci planów usługi aplikacji.|
|/serverfarms/workers/reboot/Action|Ponowny rozruch pracowników planów usługi aplikacji.|
|/serverfarms/Write|Tworzenie planu usługi aplikacji lub zaktualizuj istniejącą|
|/sites/analyzecustomhostname/read|Przeanalizuj niestandardową nazwę hosta.|
|/sites/applySlotConfig/Action|Zastosuj konfigurację sieci web aplikacji gniazda z miejsca docelowego w bieżącej aplikacji sieci web|
|/Sites/Backup/Action|Utwórz nową kopię zapasową aplikacji sieci web|
|/ lokacji/tworzenia kopii zapasowej/odczytu|Pobierz kopii zapasowej aplikacji sieci Web.|
|/Sites/Backup/Write|Update Web Apps Backup.|
|/Sites/Backups/DELETE|Usunąć kopie zapasowe aplikacji sieci Web.|
|/Sites/Backups/list/Action|Lista kopie zapasowe aplikacji sieci Web.|
|/Sites/Backups/Read|Pobierz właściwości kopii zapasowej aplikacji sieci web|
|/Sites/Backups/Restore/Action|Przywracać kopie zapasowe aplikacji sieci Web.|
|/sites/config/delete|Usuwanie konfiguracji aplikacji sieci Web.|
|/sites/config/list/Action|Wyświetl listę aplikacji sieci Web poufne ustawienia zabezpieczeń, takie jak publikowania poświadczeń, ustawienia aplikacji i parametry połączenia|
|/sites/config/Read|Pobierz ustawienia konfiguracji aplikacji sieci Web|
|/sites/config/Write|Zaktualizuj ustawienia konfiguracji aplikacji sieci Web|
|/sites/continuouswebjobs/delete|Usuń zadania ciągłego sieci Web aplikacji sieci Web.|
|/sites/continuouswebjobs/read|Pobierz zadania ciągłego sieci Web aplikacji sieci Web.|
|/Sites/continuouswebjobs/Start/Action|Rozpocznij zadania ciągłego sieci Web aplikacji sieci Web.|
|/Sites/continuouswebjobs/Stop/Action|Zatrzymanie zadania ciągłego sieci Web aplikacji sieci Web.|
|/ lokacji/Delete|Usuń istniejącą aplikację sieci Web|
|/Sites/Deployments/DELETE|Usunięcie wdrożenia aplikacji sieci Web.|
|/Sites/Deployments/log/Read|Pobierz dziennik wdrożenia aplikacji sieci Web.|
|/Sites/Deployments/Read|Pobierz wdrożenia aplikacji sieci Web.|
|/Sites/Deployments/Write|Aktualizacji wdrożenia aplikacji sieci Web.|
|/sites/diagnostics/analyses/execute/Action|Uruchom analizę diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/analyses/Read|Pobierz analizy diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/aspnetcore/Read|Pobierz diagnostyki aplikacji sieci Web dla aplikacji platformy ASP.NET Core.|
|/Sites/Diagnostics/autoheal/Read|Pobierz funkcja Autoheal diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/Deployment/Read|Pobierz wdrożenia diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/Deployments/Read|Pobierz wdrożeń diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/detectors/EXECUTE/Action|Uruchom detektora diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/detectors/Read|Pobierz detektora diagnostyki aplikacji sieci Web.|
|/sites/diagnostics/failedrequestsperuri/read|Pobierz żądań sieci Web diagnostyki aplikacji nie powiodło się dla identyfikatora Uri.|
|/sites/diagnostics/frebanalysis/read|Pobierz analizy FREB diagnostyki aplikacji sieci Web.|
|/sites/diagnostics/loganalyzer/read|Pobierz analizatora dziennika diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/Read|Pobierz kategorii diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/runtimeavailability/Read|Pobierz dostępności środowiska uruchomieniowego diagnostyki aplikacji sieci Web.|
|/sites/diagnostics/servicehealth/read|Pobierz kondycję usługi sieci Web diagnostyki aplikacji.|
|/Sites/Diagnostics/sitecpuanalysis/Read|Pobierz analizy Procesora witryny diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/sitecrashes/Read|Pobierz awarii lokacji diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/sitelatency/Read|Pobierz czas oczekiwania witryny diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/sitememoryanalysis/Read|Pobierz analizy pamięci lokacji diagnostyki aplikacji sieci Web.|
|/sites/diagnostics/siterestartsettingupdate/read|Uzyskiwanie aktualizacji ustawienia ponownego uruchomienia witryny diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/siterestartuserinitiated/Read|Pobierz sieci Web diagnostyki aplikacji witryny inicjowane przez ponowne uruchomienie użytkownika.|
|/Sites/Diagnostics/siteswap/Read|Pobierz wymiany lokacji diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/ThreadCount/Read|Pobierz liczbę wątków diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/workeravailability/Read|Pobierz Workeravailability diagnostyki aplikacji sieci Web.|
|/Sites/Diagnostics/workerprocessrecycle/Read|Pobierz odtworzenia procesu roboczego diagnostyki aplikacji sieci Web.|
|/Sites/domainownershipidentifiers/Read|Pobierz identyfikatory własność domeny aplikacji sieci Web.|
|/sites/domainownershipidentifiers/write|Zaktualizuj identyfikatory własność domeny aplikacji sieci Web.|
|/Sites/Functions/Action|Funkcje aplikacji sieci Web.|
|/Sites/Functions/DELETE|Usuń funkcje aplikacji sieci Web.|
|/Sites/Functions/listsecrets/Action|Listy kluczy tajnych funkcje aplikacji sieci Web.|
|/Sites/Functions/masterkey/Read|Pobierz umożliwia funkcje aplikacji sieci Web.|
|/Sites/Functions/Read|Pobierz funkcje aplikacji sieci Web.|
|/Sites/Functions/token/Read|Token funkcje aplikacji sieci Web GET.|
|/Sites/Functions/Write|Zaktualizuj funkcje aplikacji sieci Web.|
|/sites/hostnamebindings/delete|Usuń powiązania nazwę hosta aplikacji sieci Web.|
|/Sites/hostnamebindings/Read|Pobierz powiązania nazwę hosta aplikacji sieci Web.|
|/sites/hostnamebindings/write|Zaktualizuj powiązania nazwę hosta aplikacji sieci Web.|
|/sites/hybridconnection/delete|Usuwanie połączenia hybrydowego aplikacji sieci Web.|
|/Sites/hybridconnection/Read|Pobierz dane o połączeniu hybrydowych aplikacji sieci Web.|
|/Sites/hybridconnection/Write|Zaktualizuj połączenie hybrydowe aplikacji sieci Web.|
|/Sites/hybridconnectionnamespaces/relays/DELETE|Usuń przekaźników przestrzenie nazw połączeń hybrydowych aplikacji sieci Web.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Lista kluczy sieci Web aplikacji hybrydowych połączenia przestrzenie nazw przekaźników.|
|/Sites/hybridconnectionnamespaces/relays/Read|Pobierz przekaźników przestrzenie nazw połączeń hybrydowych aplikacji sieci Web.|
|/Sites/hybridconnectionnamespaces/relays/Write|Zaktualizuj przekaźników przestrzenie nazw połączeń hybrydowych aplikacji sieci Web.|
|/Sites/hybridconnectionrelays/Read|Pobierz przekaźników połączeń hybrydowych aplikacji sieci Web.|
|/Sites/instances/Deployments/DELETE|Usunięcie wdrożeń wystąpienia aplikacji sieci Web.|
|/Sites/instances/Deployments/Read|Pobierz wdrożeń wystąpienia aplikacji sieci Web.|
|/sites/instances/extensions/log/read|Pobierz dziennik rozszerzeń wystąpienia aplikacji sieci Web.|
|/Sites/instances/Extensions/Read|Pobierz rozszerzenia wystąpienia aplikacji sieci Web.|
|/sites/instances/processes/delete|Usuń procesów wystąpienia aplikacji sieci Web.|
|/sites/instances/processes/read|Pobierz procesów wystąpienia aplikacji sieci Web.|
|/Sites/instances/Read|Pobierz wystąpienia aplikacji sieci Web.|
|/Sites/listsyncfunctiontriggerstatus/Action|Lista synchronizacji funkcja wyzwalacz stanu aplikacji sieci Web.|
|/Sites/metricdefinitions/Read|Pobierz definicje Metryka aplikacji sieci Web.|
|/Sites/Metrics/Read|Pobieraj metryki aplikacji sieci Web.|
|/Sites/metricsdefinitions/Read|Pobierz definicje metryk aplikacji sieci Web.|
|/sites/migratemysql/action|Przeprowadź migrację aplikacji sieci Web MySql.|
|/sites/migratemysql/read|Get Web Apps Migrate MySql.|
|/sites/networktrace/action|Aplikacje sieci Web śledzenia sieci.|
|/Sites/newpassword/Action|Aplikacje sieci Web NoweHasło.|
|/sites/operationresults/read|Pobierz wyniki operacji aplikacji sieci Web.|
|/Sites/Operations/Read|Pobierz operacje aplikacji sieci Web.|
|/Sites/perfcounters/Read|Pobierz liczniki wydajności aplikacji sieci Web.|
|/sites/premieraddons/delete|Usuń dodatków Premier aplikacji sieci Web.|
|/sites/premieraddons/read|Pobierz dodatki Premier aplikacji sieci Web.|
|/sites/premieraddons/write|Aktualizowanie dodatków Premier aplikacji sieci Web.|
|/Sites/processes/Read|Pobierz procesów aplikacji sieci Web.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Pobiera dostępne metryki dla aplikacji sieci Web|
|/sites/publiccertificates/delete|Usuń certyfikaty publiczne aplikacji sieci Web.|
|/sites/publiccertificates/read|Pobierz certyfikaty publiczne aplikacji sieci Web.|
|/sites/publiccertificates/write|Aktualizuj certyfikaty publiczne aplikacji sieci Web.|
|/sites/publish/Action|Publikowanie aplikacji sieci Web|
|/sites/publishxml/Action|Pobierz publikowania format xml profilu dla aplikacji sieci Web|
|/sites/publishxml/read|Pobierz aplikacje sieci Web publikowanie XML.|
|/ lokacji/odczytu|Pobierz właściwości aplikacji sieci Web|
|/Sites/recommendationhistory/Read|Pobierz historię zalecenie aplikacji sieci Web.|
|/Sites/recommendations/disable/Action|Wyłącz zalecenia dotyczące aplikacji sieci Web.|
|/Sites/recommendations/Read|Pobierz listę zaleceń dotyczących aplikacji sieci web.|
|/Sites/Recover/Action|Odzyskiwanie aplikacji sieci Web.|
|/sites/resetSlotConfig/Action|Zresetuj konfigurację aplikacji sieci web|
|/sites/resourcehealthmetadata/read|Pobranie metadanych kondycji zasobów aplikacji sieci Web.|
|/Sites/restart/Action|Ponowne uruchomienie aplikacji sieci Web|
|/Sites/Restore/Read|Pobierz przywracania aplikacji sieci Web.|
|/Sites/Restore/Write|Przywrócenie aplikacji sieci Web.|
|/Sites/siteextensions/DELETE|Usuwanie rozszerzenia aplikacji witryny sieci Web.|
|/Sites/siteextensions/Read|Pobierz rozszerzenia aplikacji witryny sieci Web.|
|/Sites/siteextensions/Write|Należy zaktualizować rozszerzenia aplikacji witryny sieci Web.|
|/sites/slots/analyzecustomhostname/read|Pobierz Web miejsc aplikacji analizowanie niestandardową nazwę hosta.|
|/sites/slots/applySlotConfig/Action|Zastosuj konfigurację sieci web aplikacji gniazda z miejsca docelowego do bieżącego miejsca.|
|/sites/slots/backup/Action|Utwórz nową kopię zapasową miejsca aplikacji sieci Web.|
|/sites/slots/backup/read|Pobierz kopię zapasową miejsc aplikacji sieci Web.|
|/sites/slots/backup/write|Zaktualizuj kopii zapasowej miejsc aplikacji sieci Web.|
|/sites/slots/backups/delete|Usuwa miejsc aplikacji sieci Web.|
|/Sites/Slots/Backups/list/Action|Lista sieci Web aplikacji miejsc wykonywanie kopii zapasowych.|
|/sites/slots/backups/Read|Pobierz właściwości kopii zapasowej miejsc aplikacji sieci web|
|/Sites/Slots/Backups/Restore/Action|Przywracanie kopii zapasowych miejsc aplikacji sieci Web.|
|/sites/slots/config/delete|Usuwanie konfiguracji miejsc aplikacji sieci Web.|
|/sites/slots/config/list/Action|Lista miejsca aplikacji sieci Web poufne ustawienia zabezpieczeń, takie jak publikowania poświadczeń, ustawienia aplikacji i parametry połączenia|
|/sites/slots/config/Read|Pobierz ustawienia konfiguracji miejsca aplikacji sieci Web|
|/sites/slots/config/Write|Aktualizowanie ustawień konfiguracji miejsca aplikacji sieci Web|
|/sites/slots/continuouswebjobs/delete|Usuń zadania ciągłego Web miejsc aplikacji sieci Web.|
|/sites/slots/continuouswebjobs/read|Pobierz zadania ciągłego Web miejsc aplikacji sieci Web.|
|/sites/slots/continuouswebjobs/start/action|Uruchom zadania ciągłego Web miejsc aplikacji sieci Web.|
|/Sites/Slots/continuouswebjobs/Stop/Action|Zatrzymanie zadania ciągłego Web miejsc aplikacji sieci Web.|
|/Sites/Slots/DELETE|Usuwanie istniejącego miejsca aplikacji sieci Web|
|/Sites/Slots/Deployments/DELETE|Usunięcie wdrożeń miejsc aplikacji sieci Web.|
|/Sites/Slots/Deployments/log/Read|Pobierz dziennik wdrożeń miejsc aplikacji sieci Web.|
|/Sites/Slots/Deployments/Read|Pobierz wdrożeń miejsc aplikacji sieci Web.|
|/Sites/Slots/Deployments/Write|Zaktualizuj wdrożeń miejsc aplikacji sieci Web.|
|/sites/slots/diagnostics/analyses/execute/Action|Uruchom analizę diagnostyki miejsc aplikacji sieci Web.|
|/Sites/Slots/Diagnostics/analyses/Read|Pobierz analizy diagnostyki miejsc aplikacji sieci Web.|
|/sites/slots/diagnostics/aspnetcore/read|Pobierz diagnostyki miejsc aplikacji sieci Web dla aplikacji platformy ASP.NET Core.|
|/sites/slots/diagnostics/autoheal/read|Pobierz funkcja Web Apps miejsc diagnostyki Autoheal.|
|/Sites/Slots/Diagnostics/Deployment/Read|Pobierz wdrażaniem diagnostyki miejsc aplikacji sieci Web.|
|/Sites/Slots/Diagnostics/Deployments/Read|Pobierz wdrożeń diagnostyki miejsc aplikacji sieci Web.|
|/sites/slots/diagnostics/detectors/execute/Action|Uruchom detektora diagnostyki miejsc aplikacji sieci Web.|
|/sites/slots/diagnostics/detectors/read|Pobierz detektora diagnostyki miejsc aplikacji sieci Web.|
|/sites/slots/diagnostics/frebanalysis/read|Pobierz Web Apps miejsc diagnostyki FREB analizy.|
|/sites/slots/diagnostics/loganalyzer/read|Pobierz sieci Web Apps miejsc diagnostyki dziennika analizatora.|
|/Sites/Slots/Diagnostics/Read|Pobierz diagnostyki miejsc aplikacji sieci Web.|
|/Sites/Slots/Diagnostics/runtimeavailability/Read|Pobierz sieci Web Apps miejsc diagnostyki środowiska uruchomieniowego dostępności.|
|/sites/slots/diagnostics/servicehealth/read|Pobierz kondycja usługi diagnostyki miejsc aplikacji sieci Web.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Pobierz analizy Procesora witryny diagnostyki miejsc aplikacji sieci Web.|
|/Sites/Slots/Diagnostics/sitecrashes/Read|Pobierz sieci Web Apps miejsc diagnostyki lokacji awarii.|
|/Sites/Slots/Diagnostics/sitelatency/Read|Pobierz czas oczekiwania witryny diagnostyki miejsc aplikacji sieci Web.|
|/Sites/Slots/Diagnostics/sitememoryanalysis/Read|Pobierz analizy pamięci lokacji diagnostyki miejsc aplikacji sieci Web.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Pobierz miejsc aplikacji sieci Web diagnostyki aktualizacji ustawienie ponownego uruchomienia witryny.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Pobierz inicjowane przez sieci Web aplikacji miejsc diagnostyki lokacji ponownego uruchomienia komputera użytkownika.|
|/sites/slots/diagnostics/siteswap/read|Pobierz zamiany diagnostyki gniazda sieci Web aplikacji w lokacji.|
|/sites/slots/diagnostics/threadcount/read|Pobierz liczbę wątków diagnostyki miejsc aplikacji sieci Web.|
|/Sites/Slots/Diagnostics/workeravailability/Read|Pobierz Workeravailability diagnostyki miejsc aplikacji sieci Web.|
|/Sites/Slots/Diagnostics/workerprocessrecycle/Read|Pobierz odtworzenia procesu roboczego diagnostyki miejsc aplikacji sieci Web.|
|/sites/slots/domainownershipidentifiers/read|Pobierz sieci Web Apps miejsc domeny własność identyfikatorów.|
|/sites/slots/hostnamebindings/delete|Usuń powiązań z nazwami hostów miejsc aplikacji sieci Web.|
|/sites/slots/hostnamebindings/read|Pobierz powiązań z nazwami hostów miejsc aplikacji sieci Web.|
|/sites/slots/hostnamebindings/write|Zaktualizować powiązań z nazwami hostów miejsc aplikacji sieci Web.|
|/sites/slots/hybridconnection/delete|Usuwanie połączenia hybrydowego miejsc aplikacji sieci Web.|
|/sites/slots/hybridconnection/read|Pobierz połączenia hybrydowego miejsc aplikacji sieci Web.|
|/sites/slots/hybridconnection/write|Zaktualizuj połączenie hybrydowe miejsc aplikacji sieci Web.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Usuwanie sieci Web Apps miejsc hybrydowego połączenia przestrzenie nazw przekaźników.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Zaktualizuj sieci Web Apps miejsc hybrydowego połączenia przestrzenie nazw przekaźników.|
|/Sites/Slots/hybridconnectionrelays/Read|Pobierz sieci Web Apps miejsc hybrydowego połączenia przekaźników.|
|/Sites/Slots/instances/Deployments/Read|Pobierz wdrożeń wystąpień miejsc aplikacji sieci Web.|
|/sites/slots/instances/processes/delete|Usuń procesów wystąpień miejsc aplikacji sieci Web.|
|/sites/slots/instances/processes/read|Pobierz procesów wystąpień miejsc aplikacji sieci Web.|
|/Sites/Slots/instances/Read|Pobierz wystąpienia miejsc aplikacji sieci Web.|
|/Sites/Slots/metricdefinitions/Read|Pobierz definicje metryk miejsc aplikacji sieci Web.|
|/Sites/Slots/Metrics/Read|Pobieraj metryki miejsc aplikacji sieci Web.|
|/sites/slots/migratemysql/read|Pobierz Web miejsc aplikacji migracji MySql.|
|/sites/slots/networktrace/action|Miejsc aplikacji sieci Web śledzenia sieci.|
|/Sites/Slots/newpassword/Action|Miejsc aplikacji sieci Web NoweHasło.|
|/sites/slots/operationresults/read|Pobierz wyniki operacji miejsc aplikacji sieci Web.|
|/Sites/Slots/Operations/Read|Pobierz operacje miejsc aplikacji sieci Web.|
|/sites/slots/perfcounters/read|Pobierz liczniki wydajności miejsc aplikacji sieci Web.|
|/sites/slots/phplogging/read|Pobierz Phplogging miejsc aplikacji sieci Web.|
|/sites/slots/premieraddons/delete|Usuń dodatków Premier miejsc aplikacji sieci Web.|
|/sites/slots/premieraddons/read|Pobierz dodatki Premier miejsc aplikacji sieci Web.|
|/sites/slots/premieraddons/write|Zaktualizuj dodatków Premier miejsc aplikacji sieci Web.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Pobiera ustawienie diagnostyczne dla zasobu|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Tworzy lub aktualizuje ustawienie diagnostyczne dla zasobu|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Pobiera dostępne metryki dla miejsca aplikacji sieci Web|
|/sites/slots/publiccertificates/read|Pobierz certyfikaty publiczne miejsc aplikacji sieci Web.|
|/sites/slots/publiccertificates/write|Utwórz lub zaktualizuj certyfikaty publiczne miejsc aplikacji sieci Web.|
|/sites/slots/publish/Action|Publikowanie miejsca aplikacji sieci Web|
|/sites/slots/publishxml/Action|Pobierz publikowanie format xml profilu dla miejsca aplikacji sieci Web|
|/Sites/Slots/Read|Pobierz właściwości miejsce wdrożenia aplikacji sieci Web|
|/sites/slots/resetSlotConfig/Action|Zresetuj konfigurację miejsca aplikacji sieci web|
|/sites/slots/resourcehealthmetadata/read|Pobierz metadane kondycji zasobu miejsc aplikacji sieci Web.|
|/sites/slots/restart/Action|Uruchom ponownie miejsca aplikacji sieci Web|
|/Sites/Slots/Restore/Read|Pobierz przywracania miejsc aplikacji sieci Web.|
|/sites/slots/restore/write|Przywrócić miejsc aplikacji sieci Web.|
|/Sites/Slots/siteextensions/DELETE|Usuwanie rozszerzenia lokacji miejsc aplikacji sieci Web.|
|/sites/slots/siteextensions/read|Pobierz rozszerzenia lokacji miejsc aplikacji sieci Web.|
|/Sites/Slots/siteextensions/Write|Aktualizacja rozszerzeń witryny miejsc aplikacji sieci Web.|
|/sites/slots/slotsdiffs/Action|Pobierz różnic w konfiguracji aplikacji sieci web i gniazd|
|/sites/slots/slotsswap/Action|Zamienić miejsc wdrażania aplikacji sieci Web|
|/sites/slots/snapshots/read|Pobrać migawek miejsc aplikacji sieci Web.|
|/Sites/Slots/sourcecontrols/DELETE|Usuń ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web|
|/Sites/Slots/sourcecontrols/Read|Pobierz ustawienia konfiguracji miejsca aplikacji sieci Web do kontroli źródła|
|/sites/slots/sourcecontrols/Write|Zaktualizuj ustawienia konfiguracji kontroli źródła miejsca aplikacji sieci Web|
|/sites/slots/start/Action|Uruchom miejsca aplikacji sieci Web|
|/sites/slots/stop/Action|Zatrzymaj miejsca aplikacji sieci Web|
|/Sites/Slots/Sync/Action|Synchronizacja miejsc aplikacji sieci Web.|
|/sites/slots/triggeredwebjobs/delete|Usuwanie zadań Webjob wyzwalanych miejsc aplikacji sieci Web.|
|/sites/slots/triggeredwebjobs/read|Pobrania zadań Webjob wyzwalanych miejsc aplikacji sieci Web.|
|/sites/slots/triggeredwebjobs/run/action|Uruchamianie zadań Webjob wyzwalanych miejsc aplikacji sieci Web.|
|/Sites/Slots/Usages/Read|Pobierz użycia miejsc aplikacji sieci Web.|
|/sites/slots/virtualnetworkconnections/delete|Usuwanie połączenia wirtualnej sieci miejsc aplikacji sieci Web.|
|/Sites/Slots/virtualnetworkconnections/Gateways/Write|Zaktualizuj bramy połączeń sieci wirtualnej miejsc aplikacji sieci Web.|
|/Sites/Slots/virtualnetworkconnections/Read|Pobierz połączenia wirtualnej sieci miejsc aplikacji sieci Web.|
|/Sites/Slots/virtualnetworkconnections/Write|Zaktualizuj połączenia wirtualnych sieci miejsc aplikacji sieci Web.|
|/sites/slots/webjobs/read|Uzyskać zadań Webjob miejsc aplikacji sieci Web.|
|/Sites/Slots/Write|Utwórz nowe miejsce aplikacji sieci Web lub zaktualizuj istniejącą|
|/sites/slotsdiffs/Action|Pobierz różnic w konfiguracji aplikacji sieci web i gniazd|
|/sites/slotsswap/Action|Zamienić miejsc wdrażania aplikacji sieci Web|
|/sites/snapshots/read|Pobrać migawek aplikacji sieci Web.|
|/Sites/sourcecontrols/DELETE|Usuń ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web|
|/Sites/sourcecontrols/Read|Pobierz ustawienia konfiguracji aplikacji sieci Web do kontroli źródła|
|/sites/sourcecontrols/Write|Zaktualizuj ustawienia konfiguracji kontroli źródła dla aplikacji sieci Web|
|/Sites/Start/Action|Uruchamiają aplikację sieci Web|
|/sites/stop/Action|Zatrzymać aplikacji sieci Web|
|/Sites/Sync/Action|Sync Web Apps.|
|/Sites/syncfunctiontriggers/Action|Wyzwalacze funkcja synchronizacji dla aplikacji sieci Web.|
|/sites/triggeredwebjobs/delete|Usuń wyzwalanych zadań Webjob aplikacji sieci Web.|
|/sites/triggeredwebjobs/history/read|Pobierz historię zadań Webjob wyzwalanych aplikacji sieci Web.|
|/sites/triggeredwebjobs/read|Pobierz wyzwalanych zadań Webjob aplikacji sieci Web.|
|/sites/triggeredwebjobs/run/action|Uruchom wyzwalanych zadań Webjob aplikacji sieci Web.|
|/Sites/Usages/Read|Pobierz użycia aplikacji sieci Web.|
|/Sites/virtualnetworkconnections/DELETE|Usuwanie połączenia wirtualnej sieci aplikacji sieci Web.|
|/Sites/virtualnetworkconnections/Gateways/Read|Pobierz bram połączeń sieci wirtualnej aplikacji sieci Web.|
|/Sites/virtualnetworkconnections/Gateways/Write|Zaktualizuj bramy połączenia wirtualnej sieci aplikacji sieci Web.|
|/Sites/virtualnetworkconnections/Read|Pobrać połączeń sieci wirtualnej aplikacji sieci Web.|
|/Sites/virtualnetworkconnections/Write|Zaktualizuj połączenia wirtualnej sieci aplikacji sieci Web.|
|/Sites/webjobs/Read|Pobrania zadań Webjob aplikacji sieci Web.|
|/ lokacji/zapisu|Utwórz nową aplikację sieci Web lub zaktualizuj istniejącą|
|/skus/read|Pobierz jednostki SKU.|
|/sourcecontrols/read|Pobierz kontroli źródła.|
|/ sourcecontrols/zapisu|Zaktualizuj kontrolki źródła.|
|/ unregister/działania|Wyrejestruj Microsoft.Web dostawcy zasobów dla subskrypcji.|
|Zweryfikuj/działania|Sprawdzanie poprawności.|
|/verifyhostingenvironmentvnet/action|Sprawdź Hosting sieci wirtualnej środowiska.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Operacja | Opis |
|---|---|
|/Components/Read|Zasoby operacje odczytu|
|/healthInstances/read|Zasoby operacje odczytu|
|Operacje/odczytu|Zasoby operacje odczytu|
|/workloads/DELETE|Usuwa zasób obciążenia|
|/workloads/Read|Odczytuje zasób obciążenia|
|/ obciążeń/zapisu|Zapisuje zasobów obciążenia|

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć niestandardową rolę](role-based-access-control-custom-roles.md).
- Przegląd [wbudowane role RBAC](role-based-access-built-in-roles.md).
- Informacje o sposobie zarządzania przypisywaniem dostępu [przez użytkownika](role-based-access-control-manage-assignments.md) lub [przez zasób](role-based-access-control-configure.md) 
- Dowiedz się, jak [wyświetlać dzienniki aktywności inspekcji akcje zasobów](~/articles/azure-resource-manager/resource-group-audit.md)
