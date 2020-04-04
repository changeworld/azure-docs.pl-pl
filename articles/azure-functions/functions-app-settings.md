---
title: Dokumentacja ustawień aplikacji dla usługi Azure Functions
description: Dokumentacja dotycząca ustawień aplikacji usługi Azure Functions lub zmiennych środowiskowych.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: e2d168d8828d17e13f875e3b2555c7db0d4ba32d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656799"
---
# <a name="app-settings-reference-for-azure-functions"></a>Dokumentacja ustawień aplikacji dla usługi Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawierają globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje dla tej aplikacji funkcji. Po uruchomieniu lokalnie te ustawienia są dostępne jako [lokalne zmienne środowiskowe](functions-run-local.md#local-settings-file). W tym artykule wymieniono ustawienia aplikacji dostępne w aplikacjach funkcji.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Istnieją inne opcje konfiguracji globalnej w pliku [host.json](functions-host-json.md) i pliku [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klucz instrumentacji dla usługi Application Insights. Używaj tylko `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTIONSTRING`jednego z . Aby uzyskać więcej informacji, zobacz [Monitorowanie funkcji platformy Azure](functions-monitoring.md). 

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|55555555-af77-484b-9032-64f83bb83bb|

## <a name="applicationinsights_connectionstring"></a>APPLICATIONINSIGHTS_CONNECTIONSTRING

Parametry połączenia dla usługi Application Insights. Użyj `APPLICATIONINSIGHTS_CONNECTIONSTRING` zamiast `APPINSIGHTS_INSTRUMENTATIONKEY` gdy aplikacja funkcji wymaga dodanych dostosowań obsługiwanych przy użyciu ciągu połączenia. Aby uzyskać więcej informacji, zobacz [Parametry połączenia](../azure-monitor/app/sdk-connection-string.md). 

|Klucz|Wartość przykładowa|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTIONSTRING|InstrumentationKey=[key];IngestionEndpoint=[url]; LiveEndpoint=[url]; ProfilerEndpoint=[url]; SnapshotEndpoint=[url];|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

W wersji 2.x i nowszych wersjach środowiska wykonawczego Functions konfiguruje zachowanie aplikacji na podstawie środowiska wykonawczego. Ta wartość jest [odczytywana podczas inicjowania](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Można ustawić `AZURE_FUNCTIONS_ENVIRONMENT` dowolną wartość, ale obsługiwane są [trzy wartości:](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) [Programistyczne,](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development) [Przejściowe](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)i [Produkcja](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Gdy `AZURE_FUNCTIONS_ENVIRONMENT` nie jest ustawiona, `Development` domyślnie na `Production` środowisko lokalne i na platformie Azure. To ustawienie powinno być `ASPNETCORE_ENVIRONMENT` używane zamiast ustawiać środowisko wykonawcze. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcjonalny ciąg połączenia konta magazynu do przechowywania dzienników i wyświetlania ich na karcie **Monitor** w portalu. To ustawienie jest prawidłowe tylko dla aplikacji, które mają docelową wersję 1.x środowiska wykonawczego usługi Azure Functions. Konto magazynu musi być uniwersalne, które obsługuje obiekty BLOB, kolejki i tabele. Aby dowiedzieć się więcej, zobacz [Wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol=https; Nazwa konta=<name>; AccountKey=<key>|

> [!NOTE]
> Aby uzyskać lepszą wydajność i doświadczenie, środowisko wykonawcze w wersji 2.x i `AzureWebJobsDashboard`nowsze używają APPINSIGHTS_INSTRUMENTATIONKEY i app insights do monitorowania zamiast .

## <a name="azurewebjobsdisablehomepage"></a>Strona AzureWebJobsDisableHomepage

`true`oznacza wyłączenie domyślnej strony docelowej wyświetlanej dla głównego adresu URL aplikacji funkcyjnej. Wartość domyślna to `false`.

|Klucz|Wartość przykładowa|
|---|------------|
|Strona AzureWebJobsDisableHomepage|true|

Gdy to ustawienie aplikacji zostanie `false`pominięte lub ustawione na , strona podobna `<functionappname>.azurewebsites.net`do poniższego przykładu jest wyświetlana w odpowiedzi na adres URL .

![Strona docelowa aplikacji funkcji](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>Kompilacja aplikacji AzureWebJobsDotNetRelease

`true`oznacza użycie trybu wydania podczas kompilowania kodu .NET; `false` oznacza użycie trybu debugowania. Wartość domyślna to `true`.

|Klucz|Wartość przykładowa|
|---|------------|
|Kompilacja aplikacji AzureWebJobsDotNetRelease|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Lista funkcji w wersji beta rozdzielonych przecinkami do włączenia. Funkcje beta włączone przez te flagi nie są gotowe do produkcji, ale mogą być włączone do użytku eksperymentalnego, zanim zostaną otwarte.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>Usługa AzureWebJobsSecretStorageType

Określa repozytorium lub dostawcę do przechowywania kluczy. Obecnie obsługiwane repozytoria to magazyn obiektów blob ("Blob") i lokalny system plików ("Pliki"). Wartość domyślna to obiekt blob w wersji 2 i system plików w wersji 1.

|Klucz|Wartość przykładowa|
|---|------------|
|Usługa AzureWebJobsSecretStorageType|Pliki|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Środowisko wykonawcze usługi Azure Functions używa tego ciągu połączenia konta magazynu dla wszystkich funkcji z wyjątkiem funkcji wyzwalanych http. Konto magazynu musi być uniwersalne, które obsługuje obiekty BLOB, kolejki i tabele. Zobacz Wymagania [dotyczące](storage-considerations.md#storage-account-requirements)konta [magazynu](functions-infrastructure-as-code.md#storage-account) i magazynu .

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol=https; AccountName=[name]; AccountKey=[klucz]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora używanego w języku TypeScript. Umożliwia zastąpienie wartości domyślnej, jeśli zajdzie taka potrzeba.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>TRYB\_\_EDYCJI\_APLIKACJI FUNKCJI

Określa, czy edytowanie w witrynie Azure portal jest włączone. Prawidłowe wartości to "readwrite" i "readonly".

|Klucz|Wartość przykładowa|
|---|------------|
|TRYB\_\_EDYCJI\_APLIKACJI FUNKCJI|readonly|

## <a name="functions_extension_version"></a>WERSJA\_\_ROZSZERZENIA FUNKCJI

Wersja środowiska wykonawczego Functions do użycia w tej aplikacji funkcji. Tylda z wersją główną oznacza użycie najnowszej wersji głównej (na przykład "~2"). Gdy dostępne są nowe wersje dla tej samej wersji głównej, są one automatycznie instalowane w aplikacji funkcji. Aby przypiąć aplikację do określonej wersji, użyj pełnego numeru wersji (na przykład "2.0.12345"). Wartość domyślna to "~2". Wartość przypięcia `~1` aplikacji do wersji 1.x środowiska wykonawczego.

|Klucz|Wartość przykładowa|
|---|------------|
|WERSJA\_\_ROZSZERZENIA FUNKCJI|~2|

## <a name="functions_v2_compatibility_mode"></a>TRYB ZGODNOŚCI FUNKCJI\_V2\_\_

To ustawienie umożliwia uruchamianie aplikacji funkcji w trybie zgodnym z wersją 2.x w środowisku uruchomieniowym w wersji 3.x. Tego ustawienia należy używać tylko w przypadku wystąpienia problemów podczas [uaktualniania aplikacji funkcji z wersji 2.x do 3.x środowiska wykonawczego](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> To ustawienie jest przeznaczone tylko jako krótkoterminowe obejście podczas aktualizowania aplikacji, aby działała poprawnie w wersji 3.x. To ustawienie jest obsługiwane tak długo, jak [długo jest obsługiwane środowisko wykonawcze 2.x](functions-versions.md). Jeśli wystąpią problemy uniemożliwiające uruchomienie aplikacji w wersji 3.x bez użycia tego ustawienia, [zgłoś problem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Wymaga, aby funkcja `~3` [\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) była ustawiona na .

|Klucz|Wartość przykładowa|
|---|------------|
|TRYB ZGODNOŚCI FUNKCJI\_V2\_\_|true|

## <a name="functions_worker_process_count"></a>LICZBA\_\_PROCESÓW PROCESU\_ROBOCZEGO FUNKCJI

Określa maksymalną liczbę procesów procesu roboczego `1`języka, z domyślną wartością . Maksymalna dozwolona `10`wartość to . Wywołania funkcji są równomiernie rozłożone między procesami roboczymi języka. Procesy procesu roboczego języka są zduplikowane\_\_co\_10 sekund, aż do osiągnięcia liczby ustawionej przez liczbę procesów roboczych funkcji. Używanie wielu procesów procesu roboczego języka nie jest takie samo jak [skalowanie](functions-scale.md). Należy rozważyć użycie tego ustawienia, gdy obciążenie ma kombinację wywołań związanych z procesorem CPU i we/wy. To ustawienie dotyczy wszystkich języków non-.NET.

|Klucz|Wartość przykładowa|
|---|------------|
|LICZBA\_\_PROCESÓW PROCESU\_ROBOCZEGO FUNKCJI|2|


## <a name="functions_worker_runtime"></a>ŚRODOWISKO\_\_WYKONAWCZE PROCESU ROBOCZEGO FUNKCJI

Środowisko uruchomieniowe procesu roboczego języka do załadowania w aplikacji funkcji.  Będzie to odpowiadać językowi używanemu w aplikacji (na przykład "dotnet"). Dla funkcji w wielu językach należy opublikować je w wielu aplikacjach, z których każda ma odpowiednią wartość środowiska uruchomieniowego procesu roboczego.  Prawidłowe `dotnet` wartości `node` to (C#/F#), `java` (JavaScript/TypeScript), (Java), `python` `powershell` (PowerShell) i (Python).

|Klucz|Wartość przykładowa|
|---|------------|
|ŚRODOWISKO\_\_WYKONAWCZE PROCESU ROBOCZEGO FUNKCJI|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Tylko dla & do konsumpcji. Parametry połączenia dla konta magazynu, na którym przechowywany jest kod aplikacji funkcji i konfiguracja. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol=https; AccountName=[name]; AccountKey=[klucz]|

## <a name="website_contentshare"></a>CONTENTSHARE WITRYNY SIECI WEB\_

Tylko dla & do konsumpcji. Ścieżka pliku do kodu i konfiguracji aplikacji funkcji. Używany z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Domyślnie jest unikatowy ciąg, który zaczyna się od nazwy aplikacji funkcji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>SKALOWANIE\_\_APLIKACJI\_\_DYNAMICZNEJ NA STRONIE INTERNETOWEJ\_MAX W POZIOMIE

Maksymalna liczba wystąpień, które aplikacja funkcji można skalować w poziomie. Wartość domyślna to brak limitu.

> [!NOTE]
> To ustawienie jest funkcją podglądu i tylko niezawodne, jeśli jest ustawiona na wartość <= 5

|Klucz|Wartość przykładowa|
|---|------------|
|SKALOWANIE\_\_APLIKACJI\_\_DYNAMICZNEJ NA STRONIE INTERNETOWEJ\_MAX W POZIOMIE|5|

## <a name="website_node_default_version"></a>DEFAULT_VERSION\_\_WĘZŁA STRONY INTERNETOWEJ

_Tylko system Windows._  
Ustawia wersję node.js do użycia podczas uruchamiania aplikacji funkcji w systemie Windows. Należy użyć tyldy (~), aby środowisko wykonawcze używało najnowszej dostępnej wersji docelowej wersji głównej. Na przykład po `~10`ustawieniu na , używana jest najnowsza wersja node.js 10. Gdy wersja główna jest przeznaczona dla tyldy, nie trzeba ręcznie aktualizować wersji pomocniczej. 

|Klucz|Wartość przykładowa|
|---|------------|
|DEFAULT_VERSION\_\_WĘZŁA STRONY INTERNETOWEJ|~10|

## <a name="website_run_from_package"></a>STRONA\_\_INTERNETOWA\_RUN FROM PACKAGE

Umożliwia uruchamianie aplikacji funkcji z zainstalowanego pliku pakietu.

|Klucz|Wartość przykładowa|
|---|------------|
|STRONA\_\_INTERNETOWA\_RUN FROM PACKAGE|1|

Prawidłowe wartości to adres URL, który jest rozpoznawany jako `1`lokalizacja pliku pakietu wdrażania, lub . Po ustawieniu `1`, pakiet musi `d:\home\data\SitePackages` znajdować się w folderze. Podczas korzystania z wdrożenia zip z tym ustawieniem, pakiet jest automatycznie przekazywane do tej lokalizacji. W wersji zapoznawczej to ustawienie zostało nazwane `WEBSITE_RUN_FROM_ZIP`. Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji z pliku pakietu](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Domyślnie serwery proxy funkcji będą korzystać ze skrótu do wysyłania wywołań interfejsu API z serwerów proxy bezpośrednio do funkcji w tej samej aplikacji funkcji, zamiast tworzenia nowego żądania HTTP. To ustawienie umożliwia wyłączenie tego zachowania.

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Wywołania z adresem URL wewnętrznej bazy danych wskazującym funkcję w lokalnej aplikacji funkcji nie będą już wysyłane bezpośrednio do funkcji, a zamiast tego zostaną przekierowane z powrotem do frontonu HTTP dla aplikacji function|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Jest to wartość domyślna. Wywołania z adresem URL wewnętrznej bazy danych wskazującym funkcję w lokalnej aplikacji funkcji będą przekazywane bezpośrednio do tej funkcji|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

To ustawienie określa, czy %2F jest dekodowany jako ukośniki w parametrach trasy po wstawieniu ich do adresu URL wewnętrznej bazy danych. 

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry trasy z zakodowanymi ukośnikami będą ich dekodowane. `example.com/api%2ftest`stanie się`example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Jest to zachowanie domyślne. Wszystkie parametry trasy zostaną przekazane bez zmian|

### <a name="example"></a>Przykład

Oto przykład proxysies.json w aplikacji funkcji w adresie URL myfunction.com

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
|true|myfunction.com/test%2fapi|example.com/test/api
|false|myfunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zaktualizować ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)

[Wyświetlanie ustawień globalnych w pliku host.json](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji usługi App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
