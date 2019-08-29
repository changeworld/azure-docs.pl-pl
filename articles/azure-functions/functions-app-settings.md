---
title: Dokumentacja ustawień aplikacji dla usługi Azure Functions
description: Dokumentacja ustawień aplikacji usługi Azure Functions lub zmienne środowiskowe.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 4426b83ee62f4a894f72e197cbe541b8b669695d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086810"
---
# <a name="app-settings-reference-for-azure-functions"></a>Dokumentacja ustawień aplikacji dla usługi Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawiera opcje konfiguracji globalne, które mają wpływ na wszystkie funkcje dla tej aplikacji funkcji. Po uruchomieniu lokalnie te ustawienia są dostępne jako [zmienne środowisk](functions-run-local.md#local-settings-file)lokalnych. W tym artykule wymieniono ustawienia aplikacji, które są dostępne w aplikacji funkcji.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Istnieją inne opcje konfiguracji globalnej w [host.json](functions-host-json.md) pliku i [local.settings.json](functions-run-local.md#local-settings-file) pliku.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klucz Instrumentacji usługi Application Insights, jeśli używasz usługi Application Insights. Zobacz [monitorowanie usługi Azure Functions](functions-monitoring.md).

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

W wersji 2. x środowiska uruchomieniowego funkcji Program konfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego. Ta wartość jest [odczytywana podczas inicjalizacji](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Można ustawić `AZURE_FUNCTIONS_ENVIRONMENT` dowolną wartość, ale obsługiwane są [trzy wartości](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [Opracowywanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [przemieszczanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)i [produkcyjne](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Gdy `AZURE_FUNCTIONS_ENVIRONMENT` nie jest ustawiona, `Development` domyślnie w środowisku lokalnym i `Production` na platformie Azure. To ustawienie powinno być używane zamiast `ASPNETCORE_ENVIRONMENT` ustawiania środowiska uruchomieniowego. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Parametry połączenia konta opcjonalne magazynu do przechowywania dzienników i wyświetlania ich w **Monitor** karty w portalu. Konto magazynu musi być jednego ogólnego przeznaczenia, który obsługuje obiekty BLOB, kolejki i tabele. Zobacz [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [wymagania dotyczące konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

> [!TIP]
> Wydajność i środowisko jest zalecane na potrzeby monitorowania zamiast AzureWebJobsDashboard APPINSIGHTS_INSTRUMENTATIONKEY i App Insights

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

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Określa repozytorium lub dostawca magazynu kluczy. Obecnie obsługiwane repozytoria są magazynu obiektów blob ("Blob") i w lokalnym systemie plików ("Files"). Wartość domyślna to obiektów blob w wersji 2 i systemu plików w wersji 1.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsSecretStorageType|Pliki|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Środowisko uruchomieniowe usługi Azure Functions używa tego parametry połączenia konta magazynu dla wszystkich funkcji, z wyjątkiem funkcji wyzwalanej przez protokół HTTP. Konto magazynu musi być jednego ogólnego przeznaczenia, który obsługuje obiekty BLOB, kolejki i tabele. Zobacz [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [wymagania dotyczące konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora, używane do TypeScript. Pozwala zastąpić domyślne, jeśli potrzebujesz.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>FUNKCJA\_APLIKACJI\_EDYTUJ\_TRYB

Określa, czy Edycja w Azure Portal jest włączona. Prawidłowe wartości to "readwrite" i "readonly".

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJA\_APLIKACJI\_EDYTUJ\_TRYB|tylko do odczytu|

## <a name="functions_extension_version"></a>FUNKCJE\_ROZSZERZENIA\_WERSJI

Wersja środowiska uruchomieniowego funkcji do użycia w tej aplikacji funkcji. Tylda za pomocą wersji głównej oznacza, użyj najnowszej wersji tej wersji głównej (na przykład, "~ 2"). Jeśli dla tej samej wersji głównej są dostępne nowe wersje, są instalowane automatycznie w aplikacji funkcji. Aby przypiąć ją do określonej wersji, należy użyć pełny numer wersji (na przykład "2.0.12345"). Domyślna to "~ 2". Wartość `~1` Przypina aplikacji do wersji 1.x środowiska uruchomieniowego.

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_ROZSZERZENIA\_WERSJI|~ 2|

## <a name="functions_worker_process_count"></a>LICZBA PROCESÓW\_\_ROBOCZYCH\_FUNKCJI

Określa maksymalną liczbę procesów roboczych języka z wartością `1`domyślną. Maksymalna dozwolona wartość to `10`. Wywołania funkcji są równomiernie dystrybuowane między procesami roboczymi języka. Procesy robocze języka są duplikowane co 10 sekund do momentu osiągnięcia liczby ustawionej\_przez\_\_liczbę procesów roboczych. Używanie wielu procesów roboczych z wieloma językami nie jest [](functions-scale.md)takie samo jak skalowanie. Należy rozważyć użycie tego ustawienia, jeśli obciążenie ma połączenie z wywołaniami związanymi z PROCESORem i we/wy. To ustawienie ma zastosowanie do wszystkich języków non-.NET.

|Klucz|Wartość przykładowa|
|---|------------|
|LICZBA PROCESÓW\_\_ROBOCZYCH\_FUNKCJI|2|


## <a name="functions_worker_runtime"></a>FUNKCJE\_PROCESU ROBOCZEGO\_ŚRODOWISKA URUCHOMIENIOWEGO

Proces roboczy CLR do załadowania w aplikacji funkcji.  Odpowiada to język używany w aplikacji (na przykład "dotnet"). Dla funkcji w wielu językach, musisz opublikować je w wiele aplikacji, z których każdy z odpowiedniej wartości środowiska uruchomieniowego procesu roboczego.  Prawidłowe wartości to `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) i `python` (Python).

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_PROCESU ROBOCZEGO\_ŚRODOWISKA URUCHOMIENIOWEGO|polecenia DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Do użycia tylko w planach Premium &. Parametry połączenia dla konta magazynu, w którym są przechowywane kod aplikacji funkcji i konfiguracji. Zobacz [tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="website_contentshare"></a>WITRYNY SIECI WEB\_CONTENTSHARE

Do użycia tylko w planach Premium &. Ścieżka pliku kodu aplikacji funkcji i konfiguracji. Używane z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Domyślna to unikatowy ciąg, który rozpoczyna się od nazwy aplikacji funkcji. Zobacz [tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>WITRYNY SIECI WEB\_MAX\_DYNAMICZNE\_APLIKACJI\_SKALOWANIA\_OUT

Maksymalna liczba wystąpień, które aplikacji funkcji można skalować do. Domyślnie nie ma żadnych ograniczeń.

> [!NOTE]
> To ustawienie jest w wersji zapoznawczej funkcja — i tylko wtedy, niezawodne Jeśli ustawiona na wartość < = 5

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_MAX\_DYNAMICZNE\_APLIKACJI\_SKALOWANIA\_OUT|5|

## <a name="website_node_default_version"></a>WITRYNY SIECI WEB\_WĘZŁA\_DEFAULT_VERSION

Wartością domyślną jest "8.11.1".

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_WĘZŁA\_DEFAULT_VERSION|8.11.1|

## <a name="website_run_from_package"></a>WITRYNY SIECI WEB\_URUCHOM\_FROM\_PAKIETU

Umożliwia aplikacji funkcji do uruchamiania z pliku zainstalowanego pakietu.

|Klucz|Wartość przykładowa|
|---|------------|
|WITRYNY SIECI WEB\_URUCHOM\_FROM\_PAKIETU|1|

Prawidłowe wartości to URL, który jest rozpoznawany jako lokalizacja pliku wdrożenia pakietu lub `1`. Po ustawieniu `1`, pakiet musi znajdować się w `d:\home\data\SitePackages` folderu. Korzystając z pliku zip wdrożenia to ustawienie, pakiet jest automatycznie przekazywana do tej lokalizacji. W wersji zapoznawczej, to ustawienie nosiła nazwę `WEBSITE_RUN_FROM_ZIP`. Aby uzyskać więcej informacji, zobacz [uruchamiać swoje funkcje na podstawie pliku pakietu](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Domyślnie skrót do wysyłania wywołania interfejsu API z serwerów proxy bezpośrednio do funkcji w tej samej aplikacji funkcji, zamiast tworzenia nowego żądania HTTP będzie korzystać z serwerów proxy usługi Functions. To ustawienie pozwala wyłączyć to zachowanie.

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnym aplikacja funkcji nie będą już wysyłane bezpośrednio do funkcji i będą kierowane z powrotem do frontonu HTTP dla aplikacja funkcji|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Jest to wartość domyślna. Wywołania z adres url wewnętrznej funkcji lokalnej aplikacji funkcji zostaną przekazane bezpośrednio do tej funkcji|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

To ustawienie określa, czy % 2F jest zdekodowany jako ukośnikami w parametrów trasy, gdy są wstawiane URL wewnętrznej bazy danych. 

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry trasy z ukośnikami zakodowany będzie je zdekodowane. `example.com/api%2ftest` staną się `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|To zachowanie domyślne. Wszystkie trasy, który zostanie przekazany parametry bez zmian|

### <a name="example"></a>Przykład

Oto przykładowy plik proxies.json w aplikacji funkcji w myfunction.com adresu URL

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```
|Dekodowanie adresu URL|Dane wejściowe|Dane wyjściowe|
|-|-|-|
|true|MyFunction.com/test%2fapi|example.com/test/API
|false|MyFunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak można zaktualizować ustawień aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)

[Zobacz globalne ustawienia w pliku host.json](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji usługi App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
