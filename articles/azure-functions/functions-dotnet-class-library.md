---
title: Odwołanie do dewelopera usług Azure Functions C#
description: Dowiedz się, jak tworzyć usługi Azure Functions przy użyciu języka C#.
ms.topic: reference
ms.date: 09/12/2018
ms.openlocfilehash: cfa53fe2defca768196af595c1d088d41bc60f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277065"
---
# <a name="azure-functions-c-developer-reference"></a>Odwołanie do dewelopera usług Azure Functions C#

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

Ten artykuł jest wprowadzeniem do tworzenia usług Azure Functions przy użyciu języka C# w bibliotekach klas .NET.

Usługa Azure Functions obsługuje języki programowania skryptów języka C# i C#. Jeśli szukasz wskazówek dotyczących [używania języka C# w witrynie Azure portal,](functions-create-function-app-portal.md)zobacz [odwołanie do dewelopera skryptu C#(csx).](functions-reference-csharp.md)

W tym artykule założono, że zostały już przeczytane następujące artykuły:

* [Przewodnik dla deweloperów usług Azure Functions](functions-reference.md)
* [Narzędzia programu Azure Functions Visual Studio 2019](functions-develop-vs.md)

## <a name="supported-versions"></a>Obsługiwane wersje

Wersje środowiska wykonawczego funkcji działają z określonymi wersjami platformy .NET. W poniższej tabeli przedstawiono najwyższy poziom platformy .NET Core i .NET Framework oraz .NET Core, które mogą być używane z określoną wersją funkcji w projekcie. 

| Wersja środowiska wykonawczego funkcji | Maksymalna wersja .NET |
| ---- | ---- |
| Funkcje 3.x | .NET Rdzeń 3.1 |
| Functions w wersji 2.x | .NET Core 2.2 |
| Functions w wersji 1.x | Program .NET Framework 4.6 |

Aby dowiedzieć się więcej, zobacz [Omówienie wersji środowiska wykonawczego usługi Azure Functions](functions-versions.md)

## <a name="functions-class-library-project"></a>Projekt biblioteki klas functions

W programie Visual Studio szablon projektu **usługi Azure Functions** tworzy projekt biblioteki klas języka C#, który zawiera następujące pliki:

* [host.json](functions-host-json.md) — przechowuje ustawienia konfiguracji, które wpływają na wszystkie funkcje w projekcie podczas uruchamiania lokalnie lub na platformie Azure.
* [local.settings.json](functions-run-local.md#local-settings-file) — przechowuje ustawienia aplikacji i parametry połączenia, które są używane podczas uruchamiania lokalnie. Ten plik zawiera wpisy tajne i nie jest publikowany w aplikacji funkcji na platformie Azure. Zamiast tego [dodaj ustawienia aplikacji do aplikacji funkcyjnej](functions-develop-vs.md#function-app-settings).

Podczas tworzenia projektu struktura folderów, która wygląda jak w poniższym przykładzie jest generowany w katalogu danych wyjściowych kompilacji:

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

Ten katalog jest tym, co zostanie wdrożone w aplikacji funkcji na platformie Azure. Rozszerzenia powiązania wymagane w [wersji 2.x](functions-versions.md) środowiska wykonawczego Functions są [dodawane do projektu jako pakiety NuGet.](./functions-bindings-register.md#vs)

> [!IMPORTANT]
> Proces kompilacji tworzy plik *function.json* dla każdej funkcji. Ten plik *function.json* nie jest przeznaczony do edycji bezpośrednio. Nie można zmienić konfiguracji powiązania ani wyłączyć funkcji, edytując ten plik. Aby dowiedzieć się, jak wyłączyć funkcję, zobacz [Jak wyłączyć funkcje](disable-function.md).


## <a name="methods-recognized-as-functions"></a>Metody rozpoznawane jako funkcje

W bibliotece klas funkcja jest metodą `FunctionName` statyczną z atrybutem a i wyzwalaczem, jak pokazano w poniższym przykładzie:

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

Atrybut `FunctionName` oznacza metodę jako punkt wejścia funkcji. Nazwa musi być unikatowa w projekcie, zaczynać się od `_`litery `-`i zawierać tylko litery, cyfry i , do 127 znaków długości. Szablony projektu często tworzą `Run`metodę o nazwie , ale nazwa metody może być dowolną prawidłową nazwą metody Języka C#.

Atrybut wyzwalacza określa typ wyzwalacza i wiąże dane wejściowe z parametrem metody. Przykładowa funkcja jest wyzwalana przez komunikat kolejki, a komunikat kolejki `myQueueItem` jest przekazywany do metody w parametrze.

## <a name="method-signature-parameters"></a>Parametry podpisu metody

Podpis metody może zawierać parametry inne niż te używane z atrybutem wyzwalacza. Oto niektóre z dodatkowych parametrów, które można uwzględnić:

* [Powiązania wejściowe i wyjściowe](functions-triggers-bindings.md) oznaczone jako takie przez dekorowanie ich atrybutami.  
* Parametr `ILogger` `TraceWriter` [(tylko wersja 1.x)](functions-versions.md#creating-1x-apps)do [rejestrowania](#logging).
* Parametr `CancellationToken` dla [wdzięku zamknięcia](#cancellation-tokens).
* [Parametry wyrażeń wiązania,](./functions-bindings-expressions-patterns.md) aby uzyskać metadane wyzwalacza.

Kolejność parametrów w podpisie funkcji nie ma znaczenia. Na przykład można umieścić parametry wyzwalacza przed lub po innych powiązań i można umieścić logger parametr przed lub po wyzwalacza lub parametrów powiązania.

### <a name="output-binding-example"></a>Przykład wiązania danych wyjściowych

Poniższy przykład modyfikuje poprzedni, dodając powiązanie kolejki wyjściowej. Funkcja zapisuje komunikat kolejki, który wyzwala funkcję do nowej wiadomości kolejki w innej kolejce.

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

Artykuły odwołania wiązania[(kolejki magazynu](functions-bindings-storage-queue.md), na przykład) wyjaśniają, których typów parametrów można używać z atrybutami wiązania wyzwalacza, danych wejściowych lub wyjściowych.

### <a name="binding-expressions-example"></a>Przykład wyrażeń wiążących

Poniższy kod pobiera nazwę kolejki do monitorowania z ustawienia aplikacji i pobiera czas `insertionTime` tworzenia wiadomości kolejki w parametrze.

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

## <a name="autogenerated-functionjson"></a>Funkcja autogenerowana.json

Proces kompilacji tworzy plik *function.json* w folderze funkcji w folderze kompilacji. Jak wspomniano wcześniej, ten plik nie jest przeznaczony do edycji bezpośrednio. Nie można zmienić konfiguracji powiązania ani wyłączyć funkcji, edytując ten plik. 

Celem tego pliku jest dostarczenie informacji do kontrolera skalowania do wykorzystania do [skalowania decyzji w planie zużycia](functions-scale.md#how-the-consumption-and-premium-plans-work). Z tego powodu plik ma tylko informacje wyzwalacza, a nie powiązania wejściowe lub wyjściowe.

Wygenerowany plik *function.json* zawiera `configurationSource` właściwość, która informuje środowisko wykonawcze, aby używać atrybutów .NET dla powiązań, a nie konfiguracji *function.json.* Oto przykład:

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

Generowanie pliku *function.json* jest wykonywane przez pakiet NuGet [Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions). 

Ten sam pakiet jest używany zarówno dla wersji 1.x i 2.x środowiska wykonawczego Functions. Struktura docelowa jest tym, co odróżnia projekt 1.x od projektu 2.x. Oto odpowiednie części plików *.csproj,* pokazujące różne struktury `Sdk` docelowe i ten sam pakiet:

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

Wśród `Sdk` zależności pakietu są wyzwalacze i powiązania. Projekt 1.x odnosi się do wyzwalaczy 1.x i powiązań, ponieważ te wyzwalacze i powiązania są przeznaczone dla platformy .NET Framework, podczas gdy 2.x wyzwala i powiązania docelowe .NET Core.

Pakiet `Sdk` zależy również od [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json), a pośrednio na [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage). Te zależności upewnij się, że projekt używa wersji tych pakietów, które działają z wersji środowiska wykonawczego funkcji, które są przeznaczone dla projektu. Na przykład `Newtonsoft.Json` ma wersję 11 dla platformy .NET Framework 4.6.1, ale środowisko wykonawcze functions, które `Newtonsoft.Json` są przeznaczone dla platformy .NET Framework 4.6.1 jest zgodne tylko z 9.0.1. Więc kod funkcji w tym projekcie `Newtonsoft.Json` również musi używać 9.0.1.

Kod źródłowy `Microsoft.NET.Sdk.Functions` jest dostępny w funkcji [\-\-azure\-\-](https://github.com/Azure/azure-functions-vs-build-sdk)repozytorium GitHub vs kompilacji sdk.

## <a name="runtime-version"></a>Wersja środowiska wykonawczego

Visual Studio używa [podstawowe narzędzia azure functions](functions-run-local.md#install-the-azure-functions-core-tools) do uruchamiania projektów funkcji. Narzędzia podstawowe to interfejs wiersza polecenia dla środowiska wykonawczego Functions.

Jeśli zainstalujesz narzędzia podstawowe przy użyciu npm, nie ma to wpływu na wersję podstawowych narzędzi używane przez program Visual Studio. W przypadku środowiska wykonawczego Functions w wersji 1.x program Visual Studio przechowuje wersje podstawowych narzędzi w *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* i używa najnowszej wersji tam przechowywanej. W przypadku funkcji 2.x podstawowe narzędzia znajdują się w rozszerzeniu **Narzędzia funkcji platformy Azure i zadania sieci Web.** Zarówno w wersji 1.x, jak i 2.x można zobaczyć, jaka wersja jest używana w danych wyjściowych konsoli po uruchomieniu projektu funkcji:

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>Obsługiwane typy powiązań

Każde powiązanie ma swoje własne obsługiwane typy; na przykład atrybut wyzwalacza obiektu blob można zastosować do parametru ciągu, parametru POCO, parametru `CloudBlockBlob` lub dowolnego z kilku innych obsługiwanych typów. [Artykuł odwołania wiązania dla powiązań obiektów blob](functions-bindings-storage-blob-trigger.md#usage) zawiera listę wszystkich obsługiwanych typów parametrów. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md) oraz [dokumenty odwołania do powiązania dla każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>Powiązanie z wartością zwracaną metody

Można użyć wartości zwracanej metody dla powiązania danych wyjściowych, stosując atrybut do wartości zwracanej metody. Przykłady można znaleźć [w plikach Wyzwalaczy i powiązaniach](./functions-bindings-return-value.md). 

Użyj zwracanej wartości tylko wtedy, gdy pomyślne wykonanie funkcji zawsze powoduje wartość zwracaną do przekazania do powiązania danych wyjściowych. W przeciwnym `ICollector` `IAsyncCollector`razie należy użyć lub , jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości wyjściowych

Aby zapisać wiele wartości do powiązania wyjściowego lub jeśli pomyślne wywołanie funkcji może nie [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) spowodować [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) niczego do przekazania do powiązania wyjściowego, należy użyć lub typów. Te typy są tylko do zapisu kolekcje, które są zapisywane do wiązania danych wyjściowych po zakończeniu metody.

W tym przykładzie zapisuje wiele `ICollector`wiadomości kolejki do tej samej kolejki przy użyciu:

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

Aby zarejestrować dane wyjściowe do dzienników przesyłania strumieniowego w języku C#, dołącz argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Zaleca się nadawanie jej nazwy `log`, tak jak w poniższym przykładzie:  

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

Unikaj `Console.Write` używania w usłudze Azure Functions. Aby uzyskać więcej informacji, zobacz [Pisanie dzienników w językach C# w](functions-monitoring.md#write-logs-in-c-functions) **monitorze usługi Azure** artykuł.

## <a name="async"></a>Async

Aby funkcja [była asynchroniza](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/) `async` , użyj `Task` słowa kluczowego i zwróć obiekt.

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

Nie można używać `out` parametrów w funkcjach asynchronizacyjnych. W przypadku powiązań danych wyjściowych należy użyć [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiektu modułu zbierającego.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Tokeny anulowania

Funkcja może zaakceptować [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametr, który umożliwia systemowi operacyjnemu powiadamianie kodu, gdy funkcja ma zostać zakończona. Tego powiadomienia można użyć, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który pozostawia dane w niespójnym stanie.

W poniższym przykładzie pokazano, jak sprawdzić zbliżające się zakończenie funkcji.

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

Aby uzyskać zmienną środowiskową lub `System.Environment.GetEnvironmentVariable`wartość ustawienia aplikacji, użyj, jak pokazano w poniższym przykładzie kodu:

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

Ustawienia aplikacji można odczytać ze zmiennych środowiskowych zarówno podczas tworzenia lokalnie, jak i podczas uruchamiania na platformie Azure. Podczas tworzenia lokalnie ustawienia aplikacji `Values` pochodzą z kolekcji w pliku *local.settings.json.* W obu środowiskach, lokalnych `GetEnvironmentVariable("<app setting name>")` i platformy Azure, pobiera wartość nazwanego ustawienia aplikacji. Na przykład podczas lokalnego uruchamiania "Moja nazwa witryny" zostanie zwrócona, jeśli `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }`plik *local.settings.json* zawiera plik .

[Właściwość System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) jest alternatywnym interfejsem API do uzyskiwania `GetEnvironmentVariable` wartości ustawień aplikacji, ale zaleca się użycie w sposób pokazany poniżej.

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W języku C# i innych językach .NET można użyć [wzorca wiązania imperatywu,](https://en.wikipedia.org/wiki/Imperative_programming) w przeciwieństwie do [*deklaratywne*](https://en.wikipedia.org/wiki/Declarative_programming) powiązania w atrybuty. Imperatywne powiązanie jest przydatne, gdy parametry wiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Za pomocą tego wzorca można powiązać z obsługiwanych powiązań wejściowych i wyjściowych on-the-fly w kodzie funkcji.

Zdefiniuj imperatywne powiązanie w następujący sposób:

- **Nie** należy dołączać atrybutu w podpisie funkcji dla żądanych powiązań imperatywów.
- Przekazać parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) wejściowy [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)lub .
- Użyj następującego wzorca języka C#, aby wykonać powiązanie danych.

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute`jest atrybutem .NET, który definiuje `T` powiązanie i jest typem danych wejściowych lub wyjściowych obsługiwanych przez ten typ powiązania. `T`nie może `out` być typem `out JObject`parametru (np. Na przykład powiązanie wyjścia tabeli aplikacji mobilnych obsługuje [sześć typów danych wyjściowych,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)ale można używać tylko [\<ICollector T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) z imperatywem powiązania.

### <a name="single-attribute-example"></a>Przykład pojedynczego atrybutu

Poniższy przykładowy kod tworzy [powiązanie danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob-output.md) ze ścieżką obiektu blob, która jest zdefiniowana w czasie wykonywania, a następnie zapisuje ciąg do obiektu blob.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definiuje powiązania wejściowego lub wyjściowego [obiektu magazynowania,](functions-bindings-storage-blob.md) a [TextWriter](/dotnet/api/system.io.textwriter) jest obsługiwanym typem wiązania wyjściowego.

### <a name="multiple-attribute-example"></a>Przykład z wieloma atrybutami

W poprzednim przykładzie pobiera ustawienie aplikacji dla głównego ciągu połączenia konta `AzureWebJobsStorage`magazynu funkcji (który jest ). Można określić niestandardowe ustawienie aplikacji do użycia dla konta Magazyn, dodając [atrybut StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()`programu . Użyj `Binder` parametru, `IBinder`a nie .  Przykład:

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o sprawdzonych rozwiązaniach dotyczących usług Azure Functions](functions-best-practices.md)
