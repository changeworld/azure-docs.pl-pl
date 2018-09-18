---
title: Dokumentacja ustawień aplikacji dla usługi Azure Functions
description: Dokumentacja ustawień aplikacji usługi Azure Functions lub zmienne środowiskowe.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/16/2018
ms.author: glenga
ms.openlocfilehash: f7299b9193c5ab24431feb9c73a0a3cf97596da3
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734945"
---
# <a name="app-settings-reference-for-azure-functions"></a>Dokumentacja ustawień aplikacji dla usługi Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawiera opcje konfiguracji globalne, które mają wpływ na wszystkie funkcje dla tej aplikacji funkcji. Po uruchomieniu lokalnie, te ustawienia dotyczą w zmiennych środowiskowych. W tym artykule wymieniono ustawienia aplikacji, które są dostępne w aplikacji funkcji.

[! Dołącz [ustawień aplikacji funkcji] (.. /.. /includes/Functions-App-Settings.MD]

Istnieją inne opcje konfiguracji globalnej w [host.json](functions-host-json.md) pliku i [local.settings.json](functions-run-local.md#local-settings-file) pliku.

## <a name="appinsightsinstrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klucz Instrumentacji usługi Application Insights, jeśli używasz usługi Application Insights. Zobacz [monitorowanie usługi Azure Functions](functions-monitoring.md).

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Parametry połączenia konta opcjonalne magazynu do przechowywania dzienników i wyświetlania ich w **Monitor** karty w portalu. Konto magazynu musi być jednego ogólnego przeznaczenia, który obsługuje obiekty BLOB, kolejki i tabele. Zobacz [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [wymagania dotyczące konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` oznacza, że należy wyłączyć domyślne docelowa strona, która jest wyświetlany adres URL katalogu głównego aplikacji funkcji. Wartość domyślna to `false`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Gdy to ustawienie aplikacji jest pominięty lub ustawiony jako `false`, stronę podobną do poniższego przykładu jest wyświetlany w odpowiedzi na adres URL `<functionappname>.azurewebsites.net`.

![Strona docelowa aplikacji — funkcja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` oznacza, że w trybie wersji podczas kompilowania kodu platformy .NET; `false` oznacza, że w trybie debugowania. Wartość domyślna to `true`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Rozdzielana przecinkami lista funkcje w wersji beta w celu włączenia. Funkcje w wersji beta włączane przez te flagi nie są już gotowe do produkcji, ale można włączyć do użycia eksperymentalnych, zanim zostaną wdrożone.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsFeatureFlags|feature1, funkcja2|

## <a name="azurewebjobsscriptroot"></a>AzureWebJobsScriptRoot

Ścieżka do katalogu głównego gdzie *host.json* znajdują się foldery plików i funkcji. W aplikacji funkcji, wartość domyślna to `%HOME%\site\wwwroot`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsScriptRoot|%Home%\site\wwwroot|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Określa repozytorium lub dostawca magazynu kluczy. Obecnie obsługiwane repozytoria są obiektów blob ("Blob") i system plików ("wyłączone"). Wartość domyślna to system plików ("wyłączone").

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsSecretStorageType|wyłączone|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Środowisko uruchomieniowe usługi Azure Functions używa tego parametry połączenia konta magazynu dla wszystkich funkcji, z wyjątkiem funkcji wyzwalanej przez protokół HTTP. Konto magazynu musi być jednego ogólnego przeznaczenia, który obsługuje obiekty BLOB, kolejki i tabele. Zobacz [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [wymagania dotyczące konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="azurewebjobstypescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora, używane do TypeScript. Pozwala zastąpić domyślne, jeśli potrzebujesz.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="functionappeditmode"></a>FUNKCJA\_APLIKACJI\_EDYTUJ\_TRYB

Prawidłowe wartości to "readwrite" i "readonly".

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJA\_APLIKACJI\_EDYTUJ\_TRYB|tylko do odczytu|

## <a name="functionsextensionversion"></a>FUNKCJE\_ROZSZERZENIA\_WERSJI

Wersja środowiska uruchomieniowego usługi Azure Functions do użycia w tej aplikacji funkcji. Tylda za pomocą wersji głównej oznacza, użyj najnowszej wersji tej wersji głównej (na przykład, "~ 1"). Jeśli dla tej samej wersji głównej są dostępne nowe wersje, są instalowane automatycznie w aplikacji funkcji. Aby przypiąć ją do określonej wersji, należy użyć pełny numer wersji (na przykład "1.0.12345"). Domyślna to "~ 1".

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_ROZSZERZENIA\_WERSJI|~1|

## <a name="websitecontentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

W przypadku planów zużycie tylko. Parametry połączenia dla konta magazynu, w którym są przechowywane kod aplikacji funkcji i konfiguracji. Zobacz [tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="websitecontentshare"></a>WITRYNY SIECI WEB\_CONTENTSHARE

W przypadku planów zużycie tylko. Ścieżka pliku kodu aplikacji funkcji i konfiguracji. Używane z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Domyślna to unikatowy ciąg, który rozpoczyna się od nazwy aplikacji funkcji. Zobacz [tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="websitemaxdynamicapplicationscaleout"></a>WITRYNY SIECI WEB\_MAX\_DYNAMICZNE\_APLIKACJI\_SKALOWANIA\_OUT

Maksymalna liczba wystąpień, które aplikacji funkcji można skalować do. Domyślnie nie ma żadnych ograniczeń.

> [!NOTE]
> To ustawienie dotyczy funkcja w wersji zapoznawczej.

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_MAX\_DYNAMICZNE\_APLIKACJI\_SKALOWANIA\_OUT|10|

## <a name="websitenodedefaultversion"></a>WITRYNY SIECI WEB\_WĘZŁA\_DEFAULT_VERSION

Wartością domyślną jest "6.5.0".

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_WĘZŁA\_DEFAULT_VERSION|6.5.0|

## <a name="websiterunfrompackage"></a>WITRYNY SIECI WEB\_URUCHOM\_FROM\_PAKIETU

Umożliwia aplikacji funkcji do uruchamiania z pliku zainstalowanego pakietu.

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_URUCHOM\_FROM\_PAKIETU|1|

Prawidłowe wartości to URL, który jest rozpoznawany jako lokalizacja pliku wdrożenia pakietu lub `1`. Po ustawieniu `1`, pakiet musi znajdować się w `d:\home\data\SitePackages` folderu. Korzystając z pliku zip wdrożenia to ustawienie, pakiet jest automatycznie przekazywana do tej lokalizacji. W wersji zapoznawczej, to ustawienie nosiła nazwę `WEBSITE_RUN_FROM_ZIP`. Aby uzyskać więcej informacji, zobacz [uruchamiać swoje funkcje na podstawie pliku pakietu](run-functions-from-deployment-package.md).

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak można zaktualizować ustawień aplikacji](functions-how-to-use-azure-function-app-settings.md#manage-app-service-settings)

[Zobacz globalne ustawienia w pliku host.json](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji usługi App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
