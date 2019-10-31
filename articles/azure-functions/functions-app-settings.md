---
title: Informacje o ustawieniach aplikacji dla Azure Functions
description: Dokumentacja referencyjna dla ustawień aplikacji Azure Functions lub zmiennych środowiskowych.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/22/2018
ms.author: glenga
ms.openlocfilehash: 896179a393b870390991a8e9942f6e7287ec5c90
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063303"
---
# <a name="app-settings-reference-for-azure-functions"></a>Informacje o ustawieniach aplikacji dla Azure Functions

Ustawienia aplikacji w aplikacji funkcji zawierają globalne opcje konfiguracji, które mają wpływ na wszystkie funkcje tej aplikacji funkcji. Po uruchomieniu lokalnie te ustawienia są dostępne jako [zmienne środowisk](functions-run-local.md#local-settings-file)lokalnych. W tym artykule wymieniono ustawienia aplikacji, które są dostępne w aplikacjach funkcji.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

W pliku [host. JSON](functions-host-json.md) znajdują się inne opcje konfiguracji globalnej oraz plik [Local. Settings. JSON](functions-run-local.md#local-settings-file) .

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Application Insights klucz instrumentacji, jeśli używasz Application Insights. Zobacz [Monitor Azure Functions](functions-monitoring.md).

|Klucz|Wartość przykładowa|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|5dbdd5e9-af77-484b-9032-64f83bb83bb|

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

W wersji 2. x środowiska uruchomieniowego funkcji Program konfiguruje zachowanie aplikacji na podstawie środowiska uruchomieniowego. Ta wartość jest [odczytywana podczas inicjalizacji](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/Program.cs#L43). Można ustawić `AZURE_FUNCTIONS_ENVIRONMENT` na dowolną wartość, ale obsługiwane są [trzy wartości](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) : [programowanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [przemieszczanie](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging)i [produkcja](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Gdy `AZURE_FUNCTIONS_ENVIRONMENT` nie jest ustawiona, domyślnie `Development` w środowisku lokalnym i `Production` na platformie Azure. To ustawienie powinno być używane zamiast `ASPNETCORE_ENVIRONMENT` do ustawiania środowiska uruchomieniowego. 

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Opcjonalne parametry połączenia konta magazynu służące do przechowywania dzienników i wyświetlania ich na karcie **monitor** w portalu. Konto magazynu musi być kontem ogólnego przeznaczenia, które obsługuje obiekty blob, kolejki i tabele. Zobacz wymagania dotyczące [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDashboard|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klucz]|

> [!TIP]
> Aby uzyskać wydajność i środowisko, zaleca się używanie usługi APPINSIGHTS_INSTRUMENTATIONKEY i App Insights do monitorowania zamiast AzureWebJobsDashboard

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` oznacza wyłączenie domyślnej strony docelowej, która jest wyświetlana dla głównego adresu URL aplikacji funkcji. Wartość domyślna to `false`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDisableHomepage|true|

Gdy to ustawienie aplikacji zostanie pominięte lub ustawione na `false`, w odpowiedzi na adres URL `<functionappname>.azurewebsites.net`zostanie wyświetlona strona podobna do poniższego przykładu.

![Strona docelowa aplikacji funkcji](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` oznacza użycie trybu wydania podczas kompilowania kodu platformy .NET; `false` oznacza użycie trybu debugowania. Wartość domyślna to `true`.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|true|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Rozdzielana przecinkami lista funkcji beta do włączenia. Funkcje beta włączone przez te flagi nie są gotowe do użycia w środowisku produkcyjnym, ale można je włączyć do eksperymentalnego użytku, zanim staną się aktywne.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsFeatureFlags|feature1,feature2|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Określa repozytorium lub dostawcę, który ma być używany na potrzeby magazynu kluczy. Obecnie obsługiwane repozytoria to magazyn obiektów BLOB ("BLOB") i lokalny system plików ("pliki"). Wartością domyślną jest obiekt BLOB w wersji 2 i system plików w wersji 1.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsSecretStorageType|Pliki|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Środowisko uruchomieniowe Azure Functions używa tych parametrów połączenia konta magazynu dla wszystkich funkcji z wyjątkiem funkcji wyzwalanych przez protokół HTTP. Konto magazynu musi być kontem ogólnego przeznaczenia, które obsługuje obiekty blob, kolejki i tabele. Zobacz wymagania dotyczące [konta magazynu](functions-infrastructure-as-code.md#storage-account) i [konta magazynu](functions-create-function-app-portal.md#storage-account-requirements).

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobsStorage|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klucz]|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Ścieżka do kompilatora używanego na potrzeby języka TypeScript. Umożliwia przesłonięcie ustawienia domyślnego, jeśli jest to konieczne.

|Klucz|Wartość przykładowa|
|---|------------|
|AzureWebJobs_TypeScriptPath|%HOME%\typescript|

## <a name="function_app_edit_mode"></a>Funkcja\_aplikacji\_edytowanie trybu\_

Określa, czy Edycja w Azure Portal jest włączona. Prawidłowe wartości to "ReadWrite" i "ReadOnly".

|Klucz|Wartość przykładowa|
|---|------------|
|Funkcja\_aplikacji\_edytowanie trybu\_|trybie|

## <a name="functions_extension_version"></a>FUNKCJE\_rozszerzenia\_wersja

Wersja środowiska uruchomieniowego funkcji, która ma być używana w tej aplikacji funkcji. Tylda z wersją główną oznacza użycie najnowszej wersji tej wersji głównej (na przykład "~ 2"). Gdy dostępne są nowe wersje dla tej samej wersji głównej, są one automatycznie instalowane w aplikacji funkcji. Aby przypiąć aplikację do określonej wersji, użyj pełnego numeru wersji (na przykład "2.0.12345"). Wartość domyślna to "~ 2". Wartość `~1` Przypinanie aplikacji do wersji 1. x środowiska uruchomieniowego.

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_rozszerzenia\_wersja|~ 2|

## <a name="functions_worker_process_count"></a>FUNKCJE\_proces\_procesu roboczego\_liczba

Określa maksymalną liczbę procesów roboczych języka z wartością domyślną `1`. Maksymalna dozwolona wartość to `10`. Wywołania funkcji są równomiernie dystrybuowane między procesami roboczymi języka. Procesy robocze języka są duplikowane co 10 sekund, dopóki nie zostanie osiągnięta liczba określona przez funkcje\_proces\_\_procesu roboczego. Używanie wielu procesów roboczych z wieloma językami nie jest takie samo jak [skalowanie](functions-scale.md). Należy rozważyć użycie tego ustawienia, jeśli obciążenie ma połączenie z wywołaniami związanymi z PROCESORem i we/wy. To ustawienie ma zastosowanie do wszystkich języków non-.NET.

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_proces\_procesu roboczego\_liczba|2|


## <a name="functions_worker_runtime"></a>FUNKCJE\_środowiska uruchomieniowego\_procesu roboczego

Środowisko uruchomieniowe procesów roboczych języka do załadowania w aplikacji funkcji.  Będzie to zgodne z językiem używanym w aplikacji (na przykład "dotnet"). W przypadku funkcji w wielu językach należy opublikować je w wielu aplikacjach z odpowiednimi wartościami środowiska wykonawczego procesu roboczego.  Prawidłowe wartości to `dotnet` (C#/F#), `node` (JavaScript/TypeScript), `java` (Java), `powershell` (PowerShell) i `python` (Python).

|Klucz|Wartość przykładowa|
|---|------------|
|FUNKCJE\_środowiska uruchomieniowego\_procesu roboczego|dotnet|

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE_CONTENTAZUREFILECONNECTIONSTRING

Do użycia tylko w planach Premium &. Parametry połączenia dla konta magazynu, w którym są przechowywane kod i konfiguracja aplikacji funkcji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|DefaultEndpointsProtocol = https; AccountName = [name]; AccountKey = [klucz]|

## <a name="website_contentshare"></a>Witryna sieci Web\_CONTENTSHARE

Do użycia tylko w planach Premium &. Ścieżka pliku do kodu i konfiguracji aplikacji funkcji. Używany z WEBSITE_CONTENTAZUREFILECONNECTIONSTRING. Wartość domyślna to unikatowy ciąg, który rozpoczyna się od nazwy aplikacji funkcji. Zobacz [Tworzenie aplikacji funkcji](functions-infrastructure-as-code.md#create-a-function-app).

|Klucz|Wartość przykładowa|
|---|------------|
|WEBSITE_CONTENTSHARE|functionapp091999e2|

## <a name="website_max_dynamic_application_scale_out"></a>Witryna sieci Web\_MAX\_dynamicznej aplikacji\_\_SKALOWANIe\_

Maksymalna liczba wystąpień, do których aplikacja funkcji może skalować w poziomie. Wartość domyślna nie jest ograniczona.

> [!NOTE]
> To ustawienie jest funkcją w wersji zapoznawczej i jest niezawodne tylko wtedy, gdy ustawiono wartość < = 5

|Klucz|Wartość przykładowa|
|---|------------|
|Witryna sieci Web\_MAX\_dynamicznej aplikacji\_\_SKALOWANIe\_|5|

## <a name="website_node_default_version"></a>\_węzła witryny sieci Web\_DEFAULT_VERSION

_Tylko system Windows._  
Ustawia wersję środowiska Node. js do użycia podczas uruchamiania aplikacji funkcji w systemie Windows. Należy użyć tyldy (~), aby środowisko uruchomieniowe używało najnowszej dostępnej wersji wersji głównej. Na przykład po ustawieniu na `~10`jest używana najnowsza wersja środowiska Node. js 10. W przypadku wersji głównej, której celem jest tylda, nie trzeba ręcznie aktualizować wersji pomocniczej. 

|Klucz|Wartość przykładowa|
|---|------------|
|\_węzła witryny sieci Web\_DEFAULT_VERSION|~ 10|

## <a name="website_run_from_package"></a>Witryna sieci Web\_URUCHAMIAna\_z pakietu\_

Umożliwia uruchamianie aplikacji funkcji z zainstalowanego pliku pakietu.

|Klucz|Wartość przykładowa|
|---|------------|
|Witryna sieci Web\_URUCHAMIAna\_z pakietu\_|1|

Prawidłowe wartości to adres URL, który jest rozpoznawany jako lokalizacja pliku pakietu wdrożenia lub `1`. Po ustawieniu na `1`pakiet musi znajdować się w folderze `d:\home\data\SitePackages`. W przypadku korzystania z tego ustawienia podczas wdrażania zip pakiet jest automatycznie przekazywany do tej lokalizacji. W wersji zapoznawczej to ustawienie miało nazwę `WEBSITE_RUN_FROM_ZIP`. Aby uzyskać więcej informacji, zobacz [Uruchamianie funkcji z pliku pakietu](run-functions-from-deployment-package.md).

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Domyślnie serwery proxy funkcji będą używać skrótu do wysyłania wywołań interfejsu API z serwerów proxy bezpośrednio do funkcji w tym samym aplikacja funkcji, zamiast tworzyć nowe żądanie HTTP. To ustawienie umożliwia wyłączenie tego zachowania.

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|true|Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnym aplikacja funkcji nie będą już wysyłane bezpośrednio do funkcji i będą kierowane z powrotem do frontonu HTTP dla aplikacja funkcji|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|false|Jest to wartość domyślna. Wywołania z adresem URL zaplecza wskazujące funkcję w lokalnym aplikacja funkcji będą przekazywane bezpośrednio do tej funkcji|


## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

To ustawienie określa, czy% 2F jest dekodowane jako ukośniki w parametrach trasy, gdy są one wstawiane do adresu URL zaplecza. 

|Klucz|Wartość|Opis|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|true|Parametry trasy z zakodowanymi ukośnikami zostaną zdekodowane. `example.com/api%2ftest` stanie się `example.com/api/test`|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|false|Jest to zachowanie domyślne. Wszystkie parametry tras zostaną przesłane bez zmian|

### <a name="example"></a>Przykład

Oto przykład proxy. JSON w aplikacji funkcji pod adresem URL myfunction.com

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

[Zobacz ustawienia globalne w pliku host. JSON](functions-host-json.md)

[Zobacz inne ustawienia aplikacji dla aplikacji App Service](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
