---
title: Usługa Azure Functions dla deweloperów odwołanie w C#
description: Dowiedz się, jak tworzenie usługi Azure Functions przy użyciu języka C#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: 71ba1266c3a6a1f063f1af4ab37a5f29752c62f0
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896163"
---
# <a name="azure-functions-c-developer-reference"></a>Usługa Azure Functions dla deweloperów odwołanie w C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ten artykuł stanowi wprowadzenie do projektowania usługi Azure Functions przy użyciu języka C# w bibliotekach klas .NET.

Usługa Azure Functions obsługuje C# i C# script języków programowania. Jeśli szukasz wskazówek [przy użyciu języka C# w witrynie Azure portal](functions-create-function-app-portal.md), zobacz [odwołanie dla deweloperów w C# (csx) skrypt](functions-reference-csharp.md).

W tym artykule założono, że użytkownik przeczytał już następujące artykuły:

* [Przewodnik dla deweloperów usługi Azure Functions](functions-reference.md)
* [Azure Functions Visual Studio 2017 Tools](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Projekt biblioteki klas funkcji

W programie Visual Studio **usługi Azure Functions** szablon projektu umożliwia utworzenie C# projekt biblioteki klas zawierający następujące pliki:

* [Host.JSON](functions-host-json.md) — przechowuje ustawienia konfiguracji, które mają wpływ na wszystkich funkcji w projekcie, gdy uruchomiona lokalnie lub na platformie Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) — przechowuje ustawienia aplikacji i parametry połączenia, które są używane podczas uruchamiania lokalnego. Ten plik zawiera wpisy tajne i nie będą publikowane do aplikacji funkcji na platformie Azure. Zamiast tego należy [Dodawanie ustawień aplikacji do aplikacji funkcji](functions-develop-vs.md#function-app-settings).

Podczas tworzenia projektu w katalogu wyjściowego strukturę folderów, która wygląda tak, że jest generowany w kompilacji:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Jest to katalog, co zostanie wdrożona do aplikacji funkcji na platformie Azure. Rozszerzenia powiązania wymagane w [wersji 2.x](functions-versions.md) funkcji środowiska uruchomieniowego są [dodane do projektu jako pakiety NuGet](./functions-bindings-register.md#c-class-library-with-visual-studio-2017).

> [!IMPORTANT]
> Proces kompilacji tworzy *function.json* pliku dla każdej funkcji. To *function.json* pliku nie jest przeznaczona do można edytować bezpośrednio. Nie można zmienić konfiguracji powiązania lub wyłączyć tę funkcję, edytując ten plik. Aby dowiedzieć się, jak wyłączyć funkcję, zobacz [wyłączania funkcji](disable-function.md#functions-2x---c-class-libraries).

## <a name="methods-recognized-as-functions"></a>Metody został rozpoznany jako funkcje

W bibliotece klas, funkcja jest metody statycznej z `FunctionName` i atrybutu wyzwalacza, jak pokazano w poniższym przykładzie:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` Atrybut oznacza metodę jako punktu wejścia funkcji. Nazwa musi być unikatowa w obrębie projektu, zaczynać się literą i zawierać tylko litery, cyfry oraz `_` i `-`, maksymalnie 127 znaków. Szablony projektów często utworzyć metodę o nazwie `Run`, ale nazwa metody może być Dowolna prawidłowa nazwa metody języka C#.

Atrybut wyzwalacza Określa typ wyzwalacz i powiązania danych wejściowych do parametru metody. Przykład funkcja jest wyzwalana przez komunikatu w kolejce i komunikatu w kolejce jest przekazywany do metody w `myQueueItem` parametru.

## <a name="method-signature-parameters"></a>Parametry podpis metody

Podpis metody może zawierać parametrów innych niż używana przez atrybut wyzwalacza. Poniżej przedstawiono dodatkowe parametry, które mogą obejmować:

* [Dane wejściowe i wyjściowe powiązania](functions-triggers-bindings.md) odpowiednio oznaczone przez urządzanie je za pomocą atrybutów.  
* `ILogger` Lub `TraceWriter` ([wersji 1.x — tylko](functions-versions.md#creating-1x-apps)) parametr [rejestrowania](#logging).
* A `CancellationToken` parametr [łagodne zamykanie](#cancellation-tokens).
* [Powiązania wyrażeń](./functions-bindings-expressions-patterns.md) parametry, aby przygotować wyzwolić metadanych.

Kolejność parametrów w sygnaturze funkcji nie ma znaczenia. Na przykład można umieścić parametry wyzwalacza, przed lub po innych powiązań i można umieścić parametr rejestratora przed lub po wyzwalaczem lub powiązaniem parametrów.

### <a name="output-binding-example"></a>Przykład powiązania danych wyjściowych

Poniższy przykład modyfikuje poprzedni, dodając powiązanie danych wyjściowych kolejki. Funkcja zapisuje komunikat w kolejce, która wyzwala funkcję, aby w innej kolejki nowego komunikatu w kolejce.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Powiązanie gama artykułów ([kolejek magazynu](functions-bindings-storage-queue.md), na przykład) wyjaśniono, jakie typy parametrów, za pomocą wyzwalacza, dane wejściowe lub atrybutów powiązania danych wyjściowych.

### <a name="binding-expressions-example"></a>Przykład wyrażenia wiązania

Poniższy kod pobiera nazwę kolejki można monitorować z poziomu ustawień aplikacji i pobiera czasu utworzenia kolejki komunikatów `insertionTime` parametru.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Wygenerowany automatycznie function.json

Proces kompilacji tworzy *function.json* pliku w folderze funkcji w folderze kompilacji. Jak wspomniano wcześniej, ten plik nie jest przeznaczona do można edytować bezpośrednio. Nie można zmienić konfiguracji powiązania lub wyłączyć tę funkcję, edytując ten plik. 

Ten plik ma na celu Podaj informacje, aby kontroler skalowania dla [skalowanie decyzje w planie zużycie](functions-scale.md#how-the-consumption-and-premium-plans-work). Z tego powodu plik ma tylko informacje o wyzwalacz, nie wejścia lub wyjścia powiązania.

Wygenerowany *function.json* plik zawiera `configurationSource` właściwość, która informuje środowisko uruchomieniowe używane są atrybuty .NET dla powiązania, zamiast *function.json* konfiguracji. Oto przykład:

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

*Function.json* Generowanie pliku jest wykonywane przez pakiet NuGet [Microsoft\.NET\.Sdk\.funkcji](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Jest używany ten sam pakiet, dla obu wersji 1.x i 2.x środowisko uruchomieniowe usługi Functions. Platforma docelowa to, co odróżnia 1.x projektu z projektu 2.x. Oto odpowiednie części *.csproj* pliki, przedstawiający różne docelowych struktur i tym samym `Sdk` pakietu:

**Functions w wersji 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions w wersji 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Wśród `Sdk` zależności pakietów są wyzwalaczy i powiązań. Projekt 1.x odnosi się do 1.x wyzwalaczy i powiązań, ponieważ te dla środowiska .NET Framework, podczas gdy 2.x wyzwalaczy i powiązań docelowej platformy .NET Core.

`Sdk` Pakietu zależy również od [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json), a następnie pośrednio na [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Te zależności, upewnij się, że projekt korzysta z wersji tych pakietów, które działają z wersją środowiska uruchomieniowego funkcji, cele projektu. Na przykład `Newtonsoft.Json` ma wersji 11 dla programu .NET Framework 4.6.1, ale środowisko uruchomieniowe funkcji, który jest przeznaczony dla platformy .NET Framework 4.6.1 jest zgodna tylko z `Newtonsoft.Json` 9.0.1. Dlatego ma również kod funkcji w tym projekcie do użycia `Newtonsoft.Json` 9.0.1.

Kod źródłowy `Microsoft.NET.Sdk.Functions` jest dostępny w repozytorium GitHub [azure\-funkcje\-vs\-kompilacji\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Wersja środowiska uruchomieniowego

Program Visual Studio używa [podstawowych narzędzi usługi Azure Functions](functions-run-local.md#install-the-azure-functions-core-tools) do uruchamiania projektów związanych z funkcji. Podstawowe narzędzia jest interfejs wiersza polecenia, aby uzyskać środowisko uruchomieniowe usługi Functions.

Po zainstalowaniu narzędzia Core za pomocą Menedżera npm, który nie ma wpływu na wersji podstawowe narzędzia używane przez program Visual Studio. Funkcje wersji środowiska uruchomieniowego 1.x, Visual Studio przechowuje wersje podstawowych narzędzi w *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* i używa najnowszej wersji, które są przechowywane. Dla funkcji 2.x, podstawowe narzędzia są wyświetlane w **usługi Azure Functions i narzędzia Web Job** rozszerzenia. Dla wersji 1.x i 2.x możesz zobaczyć, jakie jest używana wersja w danych wyjściowych konsoli po uruchomieniu projektu usługi Functions:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Obsługiwane typy dla powiązania

Każde powiązanie ma swój własny obsługiwanych typów; na przykład można zastosować atrybutu wyzwalacza obiektu blob do jako parametr ciągu parametrem POCO `CloudBlockBlob` parametr lub dowolnego z kilku innych typów obsługiwanych. [Artykule informacyjnym na temat wiązania dla powiązania obiektu blob](functions-bindings-storage-blob.md#trigger---usage) Wyświetla wszystkie obsługiwane typy parametrów. Aby uzyskać więcej informacji, zobacz [wyzwalaczy i powiązań](functions-triggers-bindings.md) i [powiązania dokumenty referencyjne dotyczące każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Powiązanie z wartość zwracaną metody

Wartość zwracaną metody można użyć dla powiązania danych wyjściowych, stosując atrybut na wartość zwracaną metody. Aby uzyskać przykłady, zobacz [wyzwalaczy i powiązań](./functions-bindings-return-value.md). 

Użyj wartości zwracanej tylko wtedy, gdy wykonanie funkcji pomyślne, który jest zawsze skutkuje zwracana wartość do przekazania do powiązania danych wyjściowych. W przeciwnym razie użyj `ICollector` lub `IAsyncCollector`, jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości w danych wyjściowych

Aby zapisać wiele wartości powiązania danych wyjściowych lub jeśli wywołanie pomyślne funkcji nie może prowadzić do niczego do przekazania do powiązania danych wyjściowych, należy użyć [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typów. Te typy są kolekcji tylko do zapisu, które są zapisywane do powiązania danych wyjściowych, po zakończeniu działania metody.

Ten przykład Przepisuje wielu komunikatów w kolejce do tej samej kolejki przy użyciu `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Rejestrowanie

Aby rejestrować dane wyjściowe do przesyłania strumieniowego dzienników w C#, obejmują argumentu typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Firma Microsoft zaleca, nadaj mu `log`, jak w poniższym przykładzie:  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Unikaj używania `Console.Write` w usłudze Azure Functions. Aby uzyskać więcej informacji, zobacz [loguje się do niego zapisu C# funkcje](functions-monitoring.md#write-logs-in-c-functions) w **monitora usługi Azure Functions** artykułu.

## <a name="async"></a>asynchroniczne

Aby funkcja [asynchronicznego](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), użyj `async` — słowo kluczowe i zwrócenie `Task` obiektu.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

Nie można użyć `out` parametrów w funkcji asynchronicznych. W przypadku powiązania danych wyjściowych, użyj [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiekt moduł zbierający](#writing-multiple-output-values) zamiast tego.

## <a name="cancellation-tokens"></a>Anulowanie tokenów

Funkcja może akceptować [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametr, który umożliwia systemowi operacyjnemu Powiadamiaj swój kod, gdy funkcja ma zostać zakończone. Skorzystaj z tego powiadomienia, aby upewnić się, że funkcja nie nieoczekiwanego zakończenia działania w sposób powodujący, że dane w stanie niespójnym.

Jak sprawdzić, czy zbliżającym się zakończeniu funkcji można znaleźć w poniższym przykładzie.

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>Zmienne środowiskowe

Aby uzyskać zmiennej środowiskowej lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable`, jak pokazano w poniższym przykładzie kodu:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Ustawienia aplikacji mogą być odczytywane ze zmiennych środowiskowych, zarówno podczas tworzenia lokalnie, jak i podczas uruchamiania na platformie Azure. Podczas tworzenia lokalnie, ustawienia aplikacji pochodzą `Values` kolekcji w *local.settings.json* pliku. W obu środowiskach lokalnych i platformy Azure, `GetEnvironmentVariable("<app setting name>")` pobiera wartość ustawienia aplikacji o nazwie. Na przykład, gdy uruchomiono lokalnie, "Moja nazwa witryny" zostaną zwrócone, jeśli Twoja *local.settings.json* plik zawiera `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

[System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) właściwość jest alternatywnych interfejsu API w celu uzyskania wartości ustawień aplikacji, ale zaleca się, że używasz `GetEnvironmentVariable` jak pokazano poniżej.

## <a name="binding-at-runtime"></a>Powiązania w czasie wykonywania

W języku C# i innych językach .NET, można użyć [imperatywne](https://en.wikipedia.org/wiki/Imperative_programming) powiązanie wzorca, w przeciwieństwie do [ *deklaratywne* ](https://en.wikipedia.org/wiki/Declarative_programming) powiązania w atrybutach. Imperatywne powiązania jest przydatne, gdy Parametry wiążące muszą być obliczane w czasie wykonywania, a nie projekt. W ramach tego wzorca można powiązać obsługiwanych danych wejściowych i danych wyjściowych powiązania na bieżąco w kodzie funkcji.

Zdefiniuj imperatywne powiązania w następujący sposób:

- **Nie** dołączyć atrybut w sygnaturze funkcji dla żądanego imperatywne wiązania.
- Przekaż parametr wejściowy [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) lub [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Użyj następującego wzorca C# do przeprowadzenia powiązania danych.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` jest atrybut .NET, który definiuje Twoje powiązanie i `T` jest typem danych wejściowych lub wyjściowych, który jest obsługiwany przez ten typ powiązania. `T` nie może być `out` typ parametru (takie jak `out JObject`). Na przykład obsługuje powiązanie danych wyjściowych na tabeli funkcji Mobile Apps [sześć typów w danych wyjściowych](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale można używać tylko [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)z powiązaniem imperatywnego.

### <a name="single-attribute-example"></a>Przykład jeden atrybut

Poniższy przykład kodu tworzy [powiązania danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob.md#output) przy użyciu obiektu blob ścieżki, która jest zdefiniowana w czasie wykonywania, następnie zapisuje dane w ciągu obiektu blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definiuje [obiektu blob magazynu](functions-bindings-storage-blob.md) wejścia lub wyjścia, powiązania i [TextWriter](/dotnet/api/system.io.textwriter) jest typ powiązania obsługiwanych danych wyjściowych.

### <a name="multiple-attribute-example"></a>Wiele przykład atrybutu

Poprzedni przykład pobiera ustawienia aplikacji dla aplikacji funkcji parametry połączenia dla głównego konta magazynu (czyli `AzureWebJobsStorage`). Można określić ustawienia aplikacji niestandardowej do użycia dla konta magazynu, dodając [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()`. Użyj `Binder` parametru nie `IBinder`.  Na przykład:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>Wyzwalacze i powiązania 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat wyzwalaczy i powiązań](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Więcej informacji na temat najlepszych rozwiązań dla usługi Azure Functions](functions-best-practices.md)
