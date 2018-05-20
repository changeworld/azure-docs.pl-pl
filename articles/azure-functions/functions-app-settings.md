---
title: Dokumentacja ustawień aplikacji dla usługi Azure Functions
description: Dokumentacja referencyjna dla ustawienia aplikacji usługi Azure Functions lub zmiennych środowiskowych.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/26/2017
ms.author: tdykstra
ms.openlocfilehash: bd5603b8f0e15eeae9dd3799d4e10952e115680f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="app-settings-reference-for-azure-functions"></a>Dokumentacja ustawień aplikacji dla usługi Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawiera opcje konfiguracji globalne, które mają wpływ na wszystkie funkcje dla danej funkcji aplikacji. Po uruchomieniu lokalnie, te ustawienia są w zmiennych środowiskowych. W tym artykule wymieniono ustawienia aplikacji, które są dostępne w aplikacjach funkcji.

Istnieją inne opcje konfiguracji globalnej w [host.json](functions-host-json.md) pliku i w [local.settings.json](functions-run-local.md#local-settings-file) pliku.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klucz Instrumentacji usługi Application Insights, jeśli używasz usługi Application Insights. Zobacz [monitorować usługę Azure Functions](functions-monitoring.md).

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Parametry połączenia konta opcjonalne magazynu do przechowywania dzienników i wyświetlania ich w **Monitor** kartę w portalu. Konto magazynu musi być jedną ogólnego przeznaczenia, która obsługuje obiekty BLOB, kolejek i tabel. Zobacz [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [wymagania dotyczące konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` oznacza wyłączenie domyślna strona jest wyświetlana dla adresu URL katalogu głównego aplikacji funkcja początkowa. Wartość domyślna to `false`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Gdy to ustawienie aplikacji jest pominięty lub ma wartość `false`, stronę podobną do poniższego przykładu jest wyświetlany w odpowiedzi na adres URL `<functionappname>.azurewebsites.net`.

![Strona początkowa aplikacji — funkcja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` oznacza, że w trybie wersji podczas kompilowania kodu platformy .NET; `false` oznacza, że w trybie debugowania. Wartość domyślna to `true`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Rozdzielana przecinkami lista funkcje w wersji beta w celu włączenia. Funkcje w wersji beta włączane przez te flagi nie są gotowe produkcji, ale można włączyć dla celów eksperymentalnych przed udostępnieniem ich na żywo.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, feature2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Ścieżka do katalogu głównego gdzie *host.json* plików i funkcja foldery znajdują się. W aplikacji funkcji, wartość domyślna to `%HOME%\site\wwwroot`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Określa repozytorium lub dostawcy magazynu kluczy. Obecnie obsługiwane repozytoria są obiektów blob ("Blob") i system plików ("wyłączone"). Wartość domyślna to system plików ("wyłączone").

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsSecretStorageType|wyłączone|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Środowisko wykonawcze usługi Azure Functions używa tych parametrów połączenia konta magazynu dla wszystkich funkcji, z wyjątkiem funkcji HTTP wyzwolone. Konto magazynu musi być jedną ogólnego przeznaczenia, która obsługuje obiekty BLOB, kolejek i tabel. Zobacz [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [wymagania dotyczące konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora używane dla języka TypeScript. Pozwala zastąpić domyślną, jeśli potrzebujesz.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNKCJA\_APLIKACJI\_EDYTUJ\_TRYB

Prawidłowe wartości to "readwrite" i "readonly".

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJA\_APLIKACJI\_EDYTUJ\_TRYB|tylko do odczytu|

## <a name="functionsextensionversion"></a>FUNKCJE\_ROZSZERZENIA\_WERSJI

Wersja środowiska uruchomieniowego usługi Azure Functions do użycia w tej funkcji aplikacji. Tylda z wersji głównej oznacza używać najnowszej wersji głównej wersji (na przykład "~ 1"). Jeśli dla tej samej wersji głównej są dostępne nowe wersje, są instalowane automatycznie w funkcji aplikacji. Aby przypiąć aplikacji do określonej wersji, użyj numeru wersji pełnej (na przykład "1.0.12345"). Wartość domyślna to "~ 1".

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_ROZSZERZENIA\_WERSJI|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Tylko planów zużycia. Parametry połączenia dla konta magazynu, w którym są przechowywane kod aplikacji funkcji i konfiguracji. Zobacz [tworzenia aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="websitecontentshare"></a>WEBSITE_CONTENTSHARE

Tylko planów zużycia. Ścieżka pliku kodu aplikacji funkcji i konfiguracji. Używane z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Domyślna to unikatowy ciąg, który rozpoczyna się od nazwy funkcji aplikacji. Zobacz [tworzenia aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WITRYNY SIECI WEB\_MAX\_DYNAMICZNE\_APLIKACJI\_SKALI\_OUT

Maksymalna liczba wystąpień, które funkcja aplikacji można skalować w poziomie do. Domyślnie nie ma żadnych ograniczeń.

> [!NOTE]
> To ustawienie dotyczy funkcji podglądu.

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_MAX\_DYNAMICZNE\_APLIKACJI\_SKALI\_OUT|10|

## <a name="websitenodedefaultversion"></a>WITRYNY SIECI WEB\_WĘZŁA\_DEFAULT_VERSION

Domyślna to "6.5.0".

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_WĘZŁA\_DEFAULT_VERSION|6.5.0|

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak można zaktualizować ustawień aplikacji](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Zobacz globalne ustawienia w pliku host.json](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji usługi aplikacji](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
