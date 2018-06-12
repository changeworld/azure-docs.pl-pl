---
title: Azure dokumentacja dla deweloperów funkcje C#
description: Zrozumienie sposobu tworzenia usługi Azure Functions przy użyciu języka C#.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.service: functions
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/12/2017
ms.author: tdykstra
ms.openlocfilehash: e521ef29a338d0c7d80493f92acff4758a091359
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261291"
---
# <a name="azure-functions-c-developer-reference"></a>Azure dokumentacja dla deweloperów funkcje C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ten artykuł zawiera wprowadzenie do tworzenia usługi Azure Functions przy użyciu języka C# w bibliotekach klas .NET.

Środowisko Azure Functions obsługuje C# i C# skrypt języków programowania. Jeśli szukasz wskazówki [przy użyciu języka C# w portalu Azure](functions-create-function-app-portal.md), zobacz [dokumentacja dla deweloperów (csx) skryptu C#](functions-reference-csharp.md).

W tym artykule przyjęto, że został już przeczytany następujące artykuły:

* [Przewodnik dla deweloperów funkcji platformy Azure](functions-reference.md)
* [Azure Functions narzędzia programu Visual Studio 2017](functions-develop-vs.md)

## <a name="functions-class-library-project"></a>Projektu biblioteki klas funkcji

W programie Visual Studio **usługi Azure Functions** szablonu projektu tworzy C# klasy biblioteki projekt, który zawiera następujące pliki:

* [Host.JSON](functions-host-json.md) -przechowuje ustawienia konfiguracji, które mają wpływ na wszystkie funkcje w projekcie uruchomionej lokalnie lub na platformie Azure.
* [Local.Settings.JSON](functions-run-local.md#local-settings-file) -przechowuje ustawienia aplikacji i parametry połączenia, które są używane podczas uruchamiania lokalnego.

> [!IMPORTANT]
> Proces kompilacji tworzy *function.json* plik dla każdej funkcji. To *function.json* plik nie jest przeznaczony do edycji bezpośrednio. Nie można zmienić konfiguracji powiązania lub wyłączenie tej funkcji, edytując ten plik. Aby wyłączyć funkcję, użyj [wyłączyć](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atrybutu. Na przykład dodać aplikację logiczną ustawienie MY_TIMER_DISABLED i zastosować `[Disable("MY_TIMER_DISABLED")]` do funkcji. Następnie można włączyć i wyłączyć, zmieniając ustawienia aplikacji.

## <a name="methods-recognized-as-functions"></a>Metody rozpoznany jako funkcji

W bibliotece klas, funkcja jest metodą statyczną z `FunctionName` i atrybut wyzwalacza, jak pokazano w poniższym przykładzie:

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` Atrybut oznacza metodę jako punktu wejścia funkcji. Nazwa musi być unikatowa w ramach projektu, zaczynać się literą i zawierać tylko litery, cyfry, `_` i `-`, maksymalnie 127 znaków. Szablony projektów często Utwórz metodę o nazwie `Run`, ale nazwa metody może być dowolną prawidłową nazwą metody C#.

Atrybut wyzwalacza Określa typ wyzwalacza i wiąże parametr metody danych wejściowych. Funkcja przykład jest wyzwalany przez komunikatu w kolejce, a komunikat z kolejki jest przekazywany do metody w `myQueueItem` parametru.

## <a name="method-signature-parameters"></a>Parametry podpisu — metoda

Podpis metody może zawierać parametrów innego niż ten używany z atrybutem wyzwalacza. Poniżej przedstawiono dodatkowe parametry, które mogą obejmować:

* [Wejście i wyjście powiązania](functions-triggers-bindings.md) oznaczona przez dekoracji ich atrybutów.  
* `ILogger` Lub `TraceWriter` parametr [rejestrowanie](#logging).
* A `CancellationToken` parametr [łagodne zamykanie](#cancellation-tokens).
* [Wyrażenia powiązania](functions-triggers-bindings.md#binding-expressions-and-patterns) parametry, aby przygotować wyzwolenia metadanych.

Kolejność parametrów w sygnaturze funkcji nie ma znaczenia. Na przykład parametry wyzwalacza można umieścić przed lub po innych powiązań i można umieścić parametru rejestratora przed lub po wyzwalacza lub powiązania parametrów.

### <a name="output-binding-example"></a>Przykład powiązania danych wyjściowych

Poniższy przykład modyfikuje mieszanego przez dodanie wiązania kolejki danych wyjściowych. Funkcja zapisuje komunikat z kolejki wyzwala funkcji do nowego komunikatu w kolejce w innej kolejki.

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

Artykuły odwołanie powiązania ([magazynu kolejek](functions-bindings-storage-queue.md), na przykład) opisano typy parametrów można używać z wyzwalacza, danych wejściowych lub wyjściowych powiązania atrybutów.

### <a name="binding-expressions-example"></a>Przykład wyrażenia powiązania

Poniższy kod pobiera nazwę kolejki, aby monitorować z ustawień aplikacji i pobiera czas utworzenia kolejki komunikatów `insertionTime` parametru.

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        TraceWriter log)
    {
        log.Info($"Message content: {myQueueItem}");
        log.Info($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>Automatycznie wygenerowany function.json

Proces kompilacji tworzy *function.json* pliku w folderze funkcji w folderze kompilacji. Jak wspomniano wcześniej, ten plik nie jest przeznaczona do można edytować bezpośrednio. Nie można zmienić konfiguracji powiązania lub wyłączenie tej funkcji, edytując ten plik. 

Ten plik ma na celu zawierają informacje, które kontroler skalowania dla [skalowanie decyzji w planie zużycie](functions-scale.md#how-the-consumption-plan-works). Z tego powodu plik zawiera tylko informacje wyzwalacza, nie wejściowych lub wyjściowych powiązania.

Wygenerowany *function.json* plik zawiera `configurationSource` właściwość, która powoduje środowiska uruchomieniowego .NET atrybutów na potrzeby wiązania, a nie *function.json* konfiguracji. Oto przykład:

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

*Function.json* generowania plików jest wykonywane przez pakiet NuGet [Microsoft\.NET\.Sdk\.funkcji](http://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Tego samego pakietu jest używany w obu wersji 1.x i 2.x funkcji aparatu plików wykonywalnych. Platforma docelowa jest, co odróżnia 1.x projektu z projektu 2.x. Poniżej przedstawiono odpowiednich części *.csproj* docelowych pliki pokazywanie różnych platform i tym samym `Sdk` pakietu:

**Funkcje 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Funkcje 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netstandard2.0</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

Wśród `Sdk` zależności pakietów są wyzwalaczy i powiązań. Projekt 1.x odwołuje się do 1.x wyzwalaczy i powiązań, ponieważ te dla środowiska .NET Framework, podczas gdy 2.x wyzwalaczy i powiązań docelowego .NET Core.

`Sdk` Pakietu zależy również od [Newtonsoft.Json](http://www.nuget.org/packages/Newtonsoft.Json), a pośrednio na [WindowsAzure.Storage](http://www.nuget.org/packages/WindowsAzure.Storage). Te zależności, upewnij się, że projekt używa wersji tych pakietów, które korzystać z funkcji wersji środowiska uruchomieniowego który elementy docelowe projektu. Na przykład `Newtonsoft.Json` ma w wersji 11 dla programu .NET Framework 4.6.1, ale środowisko uruchomieniowe Functions, przeznaczonego dla platformy .NET Framework 4.6.1 jest zgodna tylko z `Newtonsoft.Json` 9.0.1. Dzięki funkcji kodu w tym projekcie ma również użyć `Newtonsoft.Json` 9.0.1.

Kod źródłowy `Microsoft.NET.Sdk.Functions` jest dostępny w repozytorium GitHub [azure\-funkcje\-vs\-kompilacji\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk).

## <a name="runtime-version"></a>Wersja środowiska uruchomieniowego

Visual Studio będzie korzystać [Azure funkcje podstawowe narzędzia](functions-run-local.md#install-the-azure-functions-core-tools) do uruchomienia projektów funkcji. Podstawowe narzędzia jest interfejs wiersza polecenia do obsługi funkcji.

W przypadku instalowania narzędzi Core za pomocą programu npm, który nie ma wpływu na wersji podstawowe narzędzia używane przez program Visual Studio. Funkcje wersji środowiska uruchomieniowego 1.x, Visual Studio przechowuje podstawowe narzędzia wersji w *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* i używa najnowszej wersji przechowywane. Dla funkcji 2.x, podstawowe narzędzia znajdują się w **narzędzia zadania sieci Web i usługi Azure Functions** rozszerzenia. 1.x i 2.x możesz zobaczyć, jakie jest używana wersja w danych wyjściowych konsoli po uruchomieniu projektu funkcje:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Obsługiwane typy dla powiązania

Każdego powiązania ma własną obsługiwanych typów; na przykład atrybut wyzwalacza obiektu blob można stosować na parametr typu string, a parametr POCO `CloudBlockBlob` parametr lub dowolną inne obsługiwane typy. [Artykule powiązanie dla obiekt blob powiązania](functions-bindings-storage-blob.md#trigger---usage) Wyświetla wszystkie obsługiwane typy parametrów. Aby uzyskać więcej informacji, zobacz [wyzwalaczy i powiązań](functions-triggers-bindings.md) i [docs odwołania wiązania dla każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Powiązanie z wartości zwracanej — metoda

Wartość zwracaną metody można użyć dla powiązania danych wyjściowych przez zastosowanie atrybutu na wartość zwracaną metody. Aby uzyskać przykłady, zobacz [wyzwalaczy i powiązań](functions-triggers-bindings.md#using-the-function-return-value).

## <a name="writing-multiple-output-values"></a>Trwa zapisywanie wielu wartości danych wyjściowych

Aby napisać wiele wartości do powiązania danych wyjściowych, użyj [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typów. Te typy są kolekcji tylko do zapisu, które są zapisywane w powiązaniu danych wyjściowych, po zakończeniu metody.

W tym przykładzie zapisuje wiele wiadomości w kolejce do tej samej kolejki przy użyciu `ICollector`:

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myQueueItemCopy,
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 1: {myQueueItem}");
        myQueueItemCopy.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>Rejestrowanie

Aby rejestrować dane wyjściowe do dzienników przesyłania strumieniowego w języku C#, obejmują argumentu typu `TraceWriter`. Zaleca się jej nazwa `log`. Unikaj używania `Console.Write` w funkcji platformy Azure. 

`TraceWriter` jest zdefiniowany w [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/TraceWriter.cs). Poziom dziennika `TraceWriter` można skonfigurować w [host.json](functions-host-json.md).

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
} 
```

> [!NOTE]
> Informacji o nowszej struktury rejestrowania, który można użyć zamiast `TraceWriter`, zobacz [zapisu dzienniki w języku C# funkcje](functions-monitoring.md#write-logs-in-c-functions) w **Monitor usługi Azure Functions** artykułu.

## <a name="async"></a>Asynchroniczne

Aby funkcja [asynchroniczne](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), użyj `async` — słowo kluczowe i przywracać `Task` obiektu.

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        TraceWriter log)
    {
        log.Info($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

## <a name="cancellation-tokens"></a>Anulowanie tokenów

Funkcja może akceptować [CancellationToken](https://msdn.microsoft.com/library/system.threading.cancellationtoken.aspx) parametr, który umożliwia systemowi operacyjnemu Powiadamiaj kodu, gdy funkcja ma zostać zakończone. Skorzystaj z tego powiadomienia, aby upewnić się, że funkcja nie nieoczekiwane zakończenie w sposób powodujący, że dane w niespójnym stanie.

Poniższy przykład pokazuje, jak do sprawdzenia zbliżającym się zakończeniu funkcji.

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

Aby uzyskać wartość zmiennej środowiskowej lub wartość ustawienia aplikacji, należy użyć `System.Environment.GetEnvironmentVariable`, jak pokazano w poniższym przykładzie:

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
    {
        log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
        log.Info(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.Info(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

Ustawienia aplikacji mogą być odczytywane z zmiennych środowiskowych, zarówno podczas opracowywania lokalnie, jak i podczas uruchamiania na platformie Azure. Podczas tworzenia lokalnie, ustawienia aplikacji pochodzą z `Values` kolekcji w *local.settings.json* pliku. W obu środowiskach lokalnych i platformy Azure, `GetEnvironmentVariable("<app setting name>")` pobiera wartość ustawienia aplikacji o nazwie. Na przykład, gdy uruchomiono lokalnie, "Moje nazwa witryny" będzie zwracany Jeśli Twoje *local.settings.json* plik zawiera `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`.

[System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/en-us/dotnet/api/system.configuration.configurationmanager.appsettings) właściwości to interfejs API alternatywnych do pobierania wartości ustawienia aplikacji, ale zaleca się, że używasz `GetEnvironmentVariable` w sposób pokazany poniżej.

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W języku C# i innych języków .NET, można użyć [imperatywnych](https://en.wikipedia.org/wiki/Imperative_programming) powiązanie wzorzec, w przeciwieństwie do [ *deklaratywne* ](https://en.wikipedia.org/wiki/Declarative_programming) powiązania w atrybutach. Powiązanie konieczne jest przydatne, gdy Parametry wiążące muszą ma zostać obliczony w czasie środowiska uruchomieniowego zamiast projektu. Z tego wzorca można powiązać z obsługiwanych danych wejściowych i wyjściowych powiązania na bieżąco w kodzie funkcji.

Zdefiniuj staje się niezbędna powiązania w następujący sposób:

- **Nie** dołączyć atrybut podpisu funkcji dla żądanego imperatywnych powiązania.
- Przekaż parametr wejściowy [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) lub [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Aby wykonać wiązania danych, użyj następującego wzorca C#.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` atrybut .NET, który definiuje Twoje powiązanie jest i `T` jest typem danych wejściowych lub wyjściowych, który jest obsługiwany przez ten typ powiązania. `T` nie może być `out` typ parametru (takie jak `out JObject`). Na przykład, w tabeli Mobile Apps output powiązanie obsługuje [sześć output typy](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale można używać tylko [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)z powiązaniem nadrzędnych.

### <a name="single-attribute-example"></a>Przykład pojedynczy atrybut

Poniższy przykładowy kod tworzy [powiązania wyjściowego obiektu blob magazynu](functions-bindings-storage-blob.md#output) z obiektu blob ścieżki, która jest zdefiniowana w czasie wykonywania, następnie zapisuje ciąg obiektu blob.

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        TraceWriter log)
    {
        log.Info($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definiuje [obiektu blob magazynu](functions-bindings-storage-blob.md) wejściowych lub wyjściowych powiązanie, i [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) jest typ powiązania obsługiwanych danych wyjściowych.

### <a name="multiple-attribute-example"></a>Wiele przykład atrybutu

Powyższy przykład pobiera ustawienia aplikacji dla aplikacji funkcja parametrów połączenia w głównym konta magazynu (czyli `AzureWebJobsStorage`). Można określić ustawienie niestandardowych aplikacji, aby użyć konta magazynu przez dodanie [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazanie tablicy atrybut do `BindAsync<T>()`. Użyj `Binder` parametru nie `IBinder`.  Na przykład:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            TraceWriter log)
    {
        log.Info($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
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
> [Dowiedz się więcej o wyzwalaczy i powiązań](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Więcej informacji na temat najlepszych rozwiązań dla usługi Azure Functions](functions-best-practices.md)
