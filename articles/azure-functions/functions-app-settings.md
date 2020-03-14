---
title: Dokumentacja ustawień aplikacji dla usługi Azure Functions
description: Dokumentacja ustawień aplikacji usługi Azure Functions lub zmienne środowiskowe.
ms.topic: conceptual
ms.date: 09/22/2018
ms.openlocfilehash: 3853ccbfd492bfaf4a82d62e6d31ab938285ee2e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79277832"
---
# <a name="app-settings-reference-for-azure-functions"></a>Dokumentacja ustawień aplikacji dla usługi Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawiera opcje konfiguracji globalne, które mają wpływ na wszystkie funkcje dla tej aplikacji funkcji. Po uruchomieniu lokalnie te ustawienia są dostępne jako [zmienne środowisk](functions-run-local.md#local-settings-file)lokalnych. W tym artykule wymieniono ustawienia aplikacji, które są dostępne w aplikacji funkcji.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

W pliku [host. JSON](functions-host-json.md) znajdują się inne opcje konfiguracji globalnej oraz plik [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Klucz Instrumentacji usługi Application Insights, jeśli używasz usługi Application Insights. Zobacz [Monitor Azure Functions](functions-monitoring.md).

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

W wersji 2. x i nowszych wersjach środowiska uruchomieniowego funkcji program skonfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego. Ta wartość jest [odczytywana podczas inicjalizacji](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Można ustawić `AZURE_FUNCTIONS_ENVIRONMENT` na dowolną wartość, ale obsługiwane są [trzy wartości](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [programowanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [przemieszczanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)i [produkcja](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Gdy `AZURE_FUNCTIONS_ENVIRONMENT` nie jest ustawiona, domyślnie `Development` w środowisku lokalnym i `Production` na platformie Azure. To ustawienie powinno być używane zamiast `ASPNETCORE_ENVIRONMENT` do ustawiania środowiska uruchomieniowego. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcjonalne parametry połączenia konta magazynu służące do przechowywania dzienników i wyświetlania ich na karcie **monitor** w portalu. To ustawienie jest prawidłowe tylko dla aplikacji przeznaczonych dla wersji 1. x środowiska uruchomieniowego Azure Functions. Konto magazynu musi być jednego ogólnego przeznaczenia, który obsługuje obiekty BLOB, kolejki i tabele. Aby dowiedzieć się więcej, zobacz [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName =<name>; AccountKey =<key>|

> [!NOTE]
> Aby zapewnić lepszą wydajność i środowisko uruchomieniowe, wersja 2. x i nowsze wersje do monitorowania są używane APPINSIGHTS_INSTRUMENTATIONKEY i App Insights, a nie `AzureWebJobsDashboard`.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` oznacza wyłączenie domyślnej strony docelowej, która jest wyświetlana dla głównego adresu URL aplikacji funkcji. Wartość domyślna to `false`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDisableHomepage|{1&gt;true&lt;1}|

Gdy to ustawienie aplikacji zostanie pominięte lub ustawione na `false`, w odpowiedzi na adres URL `<functionappname>.azurewebsites.net`zostanie wyświetlona strona podobna do poniższego przykładu.

![Strona docelowa aplikacji — funkcja](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` oznacza użycie trybu wydania podczas kompilowania kodu platformy .NET; `false` oznacza użycie trybu debugowania. Wartość domyślna to `true`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|{1&gt;true&lt;1}|

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

Środowisko uruchomieniowe usługi Azure Functions używa tego parametry połączenia konta magazynu dla wszystkich funkcji, z wyjątkiem funkcji wyzwalanej przez protokół HTTP. Konto magazynu musi być jednego ogólnego przeznaczenia, który obsługuje obiekty BLOB, kolejki i tabele. Zobacz wymagania dotyczące [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [konta magazynu](storage-considerations.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora, używane do TypeScript. Pozwala zastąpić domyślne, jeśli potrzebujesz.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%Home%\typescript|

## <a name="function_app_edit_mode"></a>Funkcja\_aplikacji\_edytowanie trybu\_

Określa, czy Edycja w Azure Portal jest włączona. Prawidłowe wartości to "readwrite" i "readonly".

|Klucz|Wartość przykładowa|
|---|------------|
|Funkcja\_aplikacji\_edytowanie trybu\_|tylko do odczytu|

## <a name="functions_extension_version"></a>FUNKCJE\_rozszerzenia\_wersja

Wersja środowiska uruchomieniowego funkcji do użycia w tej aplikacji funkcji. Tylda za pomocą wersji głównej oznacza, użyj najnowszej wersji tej wersji głównej (na przykład, "~ 2"). Jeśli dla tej samej wersji głównej są dostępne nowe wersje, są instalowane automatycznie w aplikacji funkcji. Aby przypiąć ją do określonej wersji, należy użyć pełny numer wersji (na przykład "2.0.12345"). Domyślna to "~ 2". Wartość `~1` Przypinanie aplikacji do wersji 1. x środowiska uruchomieniowego.

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_rozszerzenia\_wersja|~ 2|

## <a name="functions_v2_compatibility_mode"></a>FUNKCJE\_w wersji 2\_\_tryb zgodności

To ustawienie umożliwia uruchamianie aplikacji funkcji w trybie zgodności w wersji 2. x w wersji 3. x środowiska uruchomieniowego. Tego ustawienia należy używać tylko w przypadku napotkania problemów podczas [uaktualniania aplikacji funkcji z wersji 2. x do 3. x środowiska uruchomieniowego](functions-versions.md#migrating-from-2x-to-3x). 

>[!IMPORTANT]
> To ustawienie jest przeznaczone tylko jako krótkoterminowe obejście podczas aktualizowania aplikacji tak, aby działała prawidłowo w wersji 3. x. To ustawienie jest obsługiwane, o ile jest [obsługiwane środowisko uruchomieniowe 2. x](functions-versions.md). Jeśli wystąpią problemy uniemożliwiające uruchomienie aplikacji w wersji 3. x bez użycia tego ustawienia, [Zgłoś problem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

Wymaga, aby [funkcje\_EXTENSION\_wersja](functions-app-settings.md#functions_extension_version) była ustawiona na `~3`.

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_w wersji 2\_\_tryb zgodności|{1&gt;true&lt;1}|

## <a name="functions_worker_process_count"></a>FUNKCJE\_proces\_procesu roboczego\_liczba

Określa maksymalną liczbę procesów roboczych języka z wartością domyślną `1`. Maksymalna dozwolona wartość to `10`. Wywołania funkcji są równomiernie dystrybuowane między procesami roboczymi języka. Procesy robocze języka są duplikowane co 10 sekund, dopóki nie zostanie osiągnięta liczba określona przez funkcje\_proces\_\_procesu roboczego. Używanie wielu procesów roboczych z wieloma językami nie jest takie samo jak [skalowanie](functions-scale.md). Należy rozważyć użycie tego ustawienia, jeśli obciążenie ma połączenie z wywołaniami związanymi z PROCESORem i we/wy. To ustawienie ma zastosowanie do wszystkich języków non-.NET.

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_proces\_procesu roboczego\_liczba|2|


## <a name="functions_worker_runtime"></a>FUNKCJE\_środowiska uruchomieniowego\_procesu roboczego

Proces roboczy CLR do załadowania w aplikacji funkcji.  Odpowiada to język używany w aplikacji (na przykład "dotnet"). Dla funkcji w wielu językach, musisz opublikować je w wiele aplikacji, z których każdy z odpowiedniej wartości środowiska uruchomieniowego procesu roboczego.  Prawidłowe wartości to `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) i `python` (Python).

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_środowiska uruchomieniowego\_procesu roboczego|polecenia DotNet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Do użycia tylko w planach Premium &. Parametry połączenia dla konta magazynu, w którym są przechowywane kod aplikacji funkcji i konfiguracji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [nazwa]; AccountKey = [klucz]|

## <a name="website_contentshare"></a>Witryna sieci Web\_CONTENTSHARE

Do użycia tylko w planach Premium &. Ścieżka pliku kodu aplikacji funkcji i konfiguracji. Używane z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Domyślna to unikatowy ciąg, który rozpoczyna się od nazwy aplikacji funkcji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>Witryna sieci Web\_MAX\_dynamicznej aplikacji\_\_SKALOWANIe\_

Maksymalna liczba wystąpień, które aplikacji funkcji można skalować do. Domyślnie nie ma żadnych ograniczeń.

> [!NOTE]
> To ustawienie jest w wersji zapoznawczej funkcja — i tylko wtedy, niezawodne Jeśli ustawiona na wartość < = 5

|Klucz|Wartość przykładowa|
|---|------------|
|Witryna sieci Web\_MAX\_dynamicznej aplikacji\_\_SKALOWANIe\_|5|

## <a name="website_node_default_version"></a>\_\_węzła witryny sieci Web DEFAULT_VERSION

_Tylko system Windows._  
Ustawia wersję środowiska Node. js do użycia podczas uruchamiania aplikacji funkcji w systemie Windows. Należy użyć tyldy (~), aby środowisko uruchomieniowe używało najnowszej dostępnej wersji wersji głównej. Na przykład po ustawieniu na `~10`jest używana najnowsza wersja środowiska Node. js 10. W przypadku wersji głównej, której celem jest tylda, nie trzeba ręcznie aktualizować wersji pomocniczej. 

|Klucz|Wartość przykładowa|
|---|------------|
|\_\_węzła witryny sieci Web DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>Witryna sieci Web\_URUCHAMIAna\_z pakietu\_

Umożliwia aplikacji funkcji do uruchamiania z pliku zainstalowanego pakietu.

|Klucz|Wartość przykładowa|
|---|------------|
|Witryna sieci Web\_URUCHAMIAna\_z pakietu\_|1|

Prawidłowe wartości to adres URL, który jest rozpoznawany jako lokalizacja pliku pakietu wdrożenia lub `1`. Po ustawieniu na `1`pakiet musi znajdować się w folderze `d:\home\data\SitePackages`. Korzystając z pliku zip wdrożenia to ustawienie, pakiet jest automatycznie przekazywana do tej lokalizacji. W wersji zapoznawczej to ustawienie miało nazwę `WEBSITE_RUN_FROM_ZIP`. Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji z pliku pakietu](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Domyślnie skrót do wysyłania wywołania interfejsu API z serwerów proxy bezpośrednio do funkcji w tej samej aplikacji funkcji, zamiast tworzenia nowego żądania HTTP będzie korzystać z serwerów proxy usługi Functions. To ustawienie pozwala wyłączyć to zachowanie.

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|{1&gt;true&lt;1}|Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnym aplikacja funkcji nie będą już wysyłane bezpośrednio do funkcji i będą kierowane z powrotem do frontonu HTTP dla aplikacja funkcji|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|{1&gt;false&lt;1}|Jest to wartość domyślna. Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnym aplikacja funkcji będą przekazywane bezpośrednio do tej funkcji|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

To ustawienie określa, czy % 2F jest zdekodowany jako ukośnikami w parametrów trasy, gdy są wstawiane URL wewnętrznej bazy danych. 

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|{1&gt;true&lt;1}|Parametry trasy z ukośnikami zakodowany będzie je zdekodowane. `example.com/api%2ftest` stanie się `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|{1&gt;false&lt;1}|To zachowanie domyślne. Wszystkie trasy, który zostanie przekazany parametry bez zmian|

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
|{1&gt;true&lt;1}|MyFunction.com/test%2fapi|example.com/test/API
|{1&gt;false&lt;1}|MyFunction.com/test%2fapi|example.com/test%2fapi|


## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak zaktualizować ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)

[Zobacz ustawienia globalne w pliku host. JSON](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
