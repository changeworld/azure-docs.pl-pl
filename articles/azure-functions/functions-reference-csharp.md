---
title: Odwołanie do dewelopera skryptów usługi Azure Functions C#
description: Dowiedz się, jak tworzyć usługi Azure Functions przy użyciu skryptu języka C#.
author: craigshoemaker
ms.topic: reference
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 76af1f51c83e9554a51e6c17266fac739e6bd6b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276818"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Odwołanie do dewelopera skryptu usługi Azure Functions C# (csx)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ten artykuł jest wprowadzeniem do tworzenia usług Azure Functions przy użyciu skryptu C# (*csx*).

Usługa Azure Functions obsługuje języki programowania skryptów języka C# i C#. Jeśli szukasz wskazówek dotyczących [używania języka C# w projekcie biblioteki klas programu Visual Studio,](functions-develop-vs.md)zobacz [odwołanie do dewelopera w języku C#.](functions-dotnet-class-library.md)

W tym artykule przyjęto założenie, że został już przeczytany [przewodnik deweloperów usługi Azure Functions.](functions-reference.md)

## <a name="how-csx-works"></a>Jak działa .csx

Środowisko skryptu języka C# dla usług Azure Functions jest oparte na [sdk azure WebJobs.](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction) Dane przepływa do funkcji Języka C# za pomocą argumentów metody. Nazwy argumentów są `function.json` określone w pliku i istnieją wstępnie zdefiniowane nazwy dostępu do rzeczy, takich jak rejestrator funkcji i tokeny anulowania.

Format *.csx* pozwala na pisanie mniej "boilerplate" i skupić się na pisaniu tylko funkcji C#. Zamiast zawijać wszystko w obszarze nazw `Run` i klasy, po prostu zdefiniuj metodę. Uwzględnij wszystkie odwołania do zestawu i przestrzenie nazw na początku pliku, jak zwykle.

Pliki *csx* aplikacji funkcji są kompilowane podczas inicjowania wystąpienia. Ten krok kompilacji oznacza, że takie rzeczy jak zimny start może trwać dłużej dla funkcji skryptu Języka C# w porównaniu do bibliotek klasy języka C#. Ten krok kompilacji jest również dlaczego funkcje skryptu języka C# są edytowalne w witrynie Azure portal, podczas gdy biblioteki klas języka C# nie są.

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu skryptu języka C# wygląda następująco:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Istnieje udostępniony plik [host.json,](functions-host-json.md) który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma swój własny plik kodu (csx) i plik konfiguracji powiązania (function.json).

Rozszerzenia powiązania wymagane w [wersji 2.x i nowszych wersjach](functions-versions.md) `extensions.csproj` środowiska wykonawczego Functions są zdefiniowane w pliku, z rzeczywistymi plikami biblioteki w folderze. `bin` Podczas tworzenia lokalnie należy [zarejestrować rozszerzenia powiązania](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w witrynie Azure portal, ta rejestracja jest wykonywana dla Ciebie.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami

Dane wejściowe lub wyjściowe są powiązane z `name` parametrem funkcji skryptu Języka C# za pośrednictwem właściwości w pliku konfiguracyjnym *function.json.* W poniższym przykładzie przedstawiono plik *function.json* i plik *run.csx* dla funkcji wyzwalanych kolejką. Parametr, który odbiera dane z `myQueueItem` komunikatu kolejki jest nazwany, ponieważ jest to wartość `name` właściwości.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

Oświadczenie `#r` zostało wyjaśnione [w dalszej części tego artykułu](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Obsługiwane typy powiązań

Każde powiązanie ma swoje własne obsługiwane typy; na przykład wyzwalacza obiektu blob może służyć z parametrem `CloudBlockBlob` ciągu, parametrem POCO, parametrem lub dowolnym z kilku innych obsługiwanych typów. [Artykuł odwołania wiązania dla powiązań obiektów blob](functions-bindings-storage-blob-trigger.md#usage) zawiera listę wszystkich obsługiwanych typów parametrów dla wyzwalaczy obiektów blob. Aby uzyskać więcej informacji, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md) oraz [dokumenty odwołania do powiązania dla każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odwoływanie się do klas niestandardowych

Jeśli chcesz użyć niestandardowej klasy zwykłego starego obiektu CLR (POCO), możesz dołączyć definicję klasy do tego samego pliku lub umieścić ją w osobnym pliku.

W poniższym przykładzie pokazano przykład *pliku run.csx,* który zawiera definicję klasy POCO.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

Klasa POCO musi mieć getter i setter zdefiniowane dla każdej właściwości.

## <a name="reusing-csx-code"></a>Ponowne czesanie kodu csx

W pliku *run.csx* można używać klas i metod zdefiniowanych w innych plikach *csx.* Aby to zrobić, użyj `#load` dyrektyw w pliku *run.csx.* W poniższym przykładzie procedura `MyLogger` rejestrowania o nazwie jest współużytkowana w pliku `#load` *myLogger.csx* i ładowana do pliku *run.csx* przy użyciu dyrektywy:

Przykład *run.csx*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Przykład *mylogger.csx*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Korzystanie z udostępnionego pliku *csx* jest typowym wzorcem, gdy chcesz silnie wpisać dane przekazywane między funkcjami przy użyciu obiektu POCO. W poniższym uproszczonym przykładzie wyzwalacz HTTP i wyzwalacz kolejki współużytkują obiekt POCO o nazwie, `Order` aby silnie wpisywać dane zamówienia:

Przykład *run.csx* dla wyzwalacza HTTP:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Przykład *run.csx* dla wyzwalacza kolejki:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Przykładowa *kolejność.csx*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

Można użyć ścieżki względnej `#load` z dyrektywą:

* `#load "mylogger.csx"`ładuje plik znajdujący się w folderze funkcji.
* `#load "loadedfiles\mylogger.csx"`ładuje plik znajdujący się w folderze w folderze funkcji.
* `#load "..\shared\mylogger.csx"`ładuje plik znajdujący się w folderze na tym samym poziomie co folder funkcji, czyli bezpośrednio pod *wwwroot*.

Dyrektywa `#load` działa tylko z plikami *csx,* a nie z plikami *cs.*

## <a name="binding-to-method-return-value"></a>Powiązanie z wartością zwracaną metody

Można użyć wartości zwracanej metody dla powiązania wyjściowego, używając nazwy `$return` w pliku *function.json*. Przykłady można znaleźć [w plikach Wyzwalaczy i powiązaniach](./functions-bindings-return-value.md).

Użyj zwracanej wartości tylko wtedy, gdy pomyślne wykonanie funkcji zawsze powoduje wartość zwracaną do przekazania do powiązania danych wyjściowych. W przeciwnym `ICollector` `IAsyncCollector`razie należy użyć lub , jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości wyjściowych

Aby zapisać wiele wartości do powiązania wyjściowego lub jeśli pomyślne wywołanie funkcji może nie [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) spowodować [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) niczego do przekazania do powiązania wyjściowego, należy użyć lub typów. Te typy są tylko do zapisu kolekcje, które są zapisywane do wiązania danych wyjściowych po zakończeniu metody.

W tym przykładzie zapisuje wiele `ICollector`wiadomości kolejki do tej samej kolejki przy użyciu:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Rejestrowanie

Aby zarejestrować dane wyjściowe do dzienników przesyłania strumieniowego w języku C#, dołącz argument typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Zalecamy nadawanie `log`nazwy . Unikaj `Console.Write` używania w usłudze Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Aby uzyskać informacje na temat nowszej struktury `TraceWriter`rejestrowania, której można użyć zamiast , zobacz [Zapis dzienników w językach C# w](functions-monitoring.md#write-logs-in-c-functions) artykule **Monitor usługi Azure Functions.**

## <a name="async"></a>Async

Aby funkcja [była asynchroniza](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/) `async` , użyj `Task` słowa kluczowego i zwróć obiekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Nie można używać `out` parametrów w funkcjach asynchronizacyjnych. W przypadku powiązań danych wyjściowych należy użyć [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiektu modułu zbierającego.](#writing-multiple-output-values)

## <a name="cancellation-tokens"></a>Tokeny anulowania

Funkcja może zaakceptować [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametr, który umożliwia systemowi operacyjnemu powiadamianie kodu, gdy funkcja ma zostać zakończona. Tego powiadomienia można użyć, aby upewnić się, że funkcja nie kończy się nieoczekiwanie w sposób, który pozostawia dane w niespójnym stanie.

W poniższym przykładzie pokazano, jak sprawdzić zbliżające się zakończenie funkcji.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
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
```

## <a name="importing-namespaces"></a>Importowanie obszarów nazw

Jeśli chcesz zaimportować obszary nazw, można to `using` zrobić jak zwykle, z klauzuli.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące obszary nazw są importowane automatycznie i dlatego są opcjonalne:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odwoływanie się do zespołów zewnętrznych

Dla zestawów framework dodaj odwołania `#r "AssemblyName"` przy użyciu dyrektywy.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingowe usługi Azure Functions:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Następujące zestawy mogą być przywoływane przez prostą nazwę (na `#r "AssemblyName"`przykład):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odwoływanie się do zestawów niestandardowych

Aby odwołać się do zestawu niestandardowego, można użyć *zestawu udostępnionego* lub *prywatnego:*

* Udostępnione zestawy są współużytkowane przez wszystkie funkcje w aplikacji funkcji. Aby odwołać się do zestawu niestandardowego, przekaż zestaw do folderu o nazwie `bin` w [folderze głównym aplikacji funkcji](functions-reference.md#folder-structure) (wwwroot).

* Zestawy prywatne są częścią kontekstu danej funkcji i obsługujące obciążenie boczne różnych wersji. Zestawy prywatne powinny być przekazywane `bin` w folderze w katalogu funkcji. Odwoływać się do zestawów przy `#r "MyAssembly.dll"`użyciu nazwy pliku, takich jak .

Aby uzyskać informacje na temat przekazywania plików do folderu funkcji, zobacz sekcję [dotyczącą zarządzania pakietami](#using-nuget-packages).

### <a name="watched-directories"></a>Obserwowane katalogi

Katalog zawierający plik skryptu funkcji jest automatycznie obserwowany pod kątem zmian w zestawach. Aby obserwować zmiany w zestawie w innych `watchDirectories` katalogach, dodaj je do listy w [pliku host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Korzystanie z pakietów NuGet
Aby użyć pakietów NuGet w funkcji 2.x i nowszej funkcji C#, przekaż plik *function.proj* do folderu funkcji w systemie plików aplikacji funkcji. Oto przykładowy plik *function.proj,* który dodaje odwołanie do *microsoft.ProjectOxford.Face* w wersji *1.1.0:*

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Aby użyć niestandardowego źródła danych NuGet, określ źródło danych w pliku *Nuget.Config* w katalogu głównym aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zachowania NuGet](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> W funkcjach 1.x C# pakiety NuGet są odwoływane za pomocą pliku *project.json* zamiast pliku *function.proj.*

W przypadku funkcji 1.x należy użyć pliku *project.json.* Oto przykładowy plik *project.json:*

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Korzystanie z pliku function.proj

1. Otwórz funkcję w witrynie Azure portal. Na karcie dzienniki jest wyświetlane dane wyjściowe instalacji pakietu.
2. Aby przekazać plik *function.proj,* należy użyć jednej z metod opisanych w [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate) w temacie odwołania dewelopera usługi Azure Functions.
3. Po przekazaniu pliku *function.proj* w dzienniku przesyłania strumieniowego funkcji zostanie wyświetlony następujący przykład:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Zmienne środowiskowe

Aby uzyskać zmienną środowiskową lub `System.Environment.GetEnvironmentVariable`wartość ustawienia aplikacji, użyj, jak pokazano w poniższym przykładzie kodu:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
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
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

W językach C# i innych językach .NET można użyć [wzorca wiązania imperatywu,](https://en.wikipedia.org/wiki/Imperative_programming) w przeciwieństwie do [*powiązań deklaratywnych*](https://en.wikipedia.org/wiki/Declarative_programming) w *pliku function.json*. Imperatywne powiązanie jest przydatne, gdy parametry wiązania muszą być obliczane w czasie wykonywania, a nie w czasie projektowania. Za pomocą tego wzorca można powiązać z obsługiwanych powiązań wejściowych i wyjściowych on-the-fly w kodzie funkcji.

Zdefiniuj imperatywne powiązanie w następujący sposób:

- **Nie** należy dołączać wpisu w *pliku function.json* dla żądanych powiązań imperatywów.
- Przekazać parametr [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) wejściowy [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs)lub .
- Użyj następującego wzorca języka C#, aby wykonać powiązanie danych.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute`jest atrybutem .NET, który definiuje `T` powiązanie i jest typem danych wejściowych lub wyjściowych obsługiwanych przez ten typ powiązania. `T`nie może `out` być typem `out JObject`parametru (np. Na przykład powiązanie wyjścia tabeli Aplikacje mobilne obsługuje [sześć typów danych wyjściowych,](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)ale można używać tylko [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) dla `T`.

### <a name="single-attribute-example"></a>Przykład pojedynczego atrybutu

Poniższy przykładowy kod tworzy [powiązanie danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob-output.md) ze ścieżką obiektu blob, która jest zdefiniowana w czasie wykonywania, a następnie zapisuje ciąg do obiektu blob.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definiuje powiązania wejściowego lub wyjściowego [obiektu magazynowania,](functions-bindings-storage-blob.md) a [TextWriter](/dotnet/api/system.io.textwriter) jest obsługiwanym typem wiązania wyjściowego.

### <a name="multiple-attribute-example"></a>Przykład z wieloma atrybutami

W poprzednim przykładzie pobiera ustawienie aplikacji dla głównego ciągu połączenia konta `AzureWebJobsStorage`magazynu funkcji (który jest ). Można określić niestandardowe ustawienie aplikacji do użycia dla konta Magazyn, dodając [atrybut StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()`programu . Użyj `Binder` parametru, `IBinder`a nie .  Przykład:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

W poniższej tabeli wymieniono atrybuty .NET dla każdego typu powiązania i pakiety, w których są zdefiniowane.

> [!div class="mx-codeBreakAll"]
> | Wiązanie | Atrybut | Dodawanie odwołania |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Usługa Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Kolejka magazynu | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Magazyn obiektów blob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabela magazynowania | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o sprawdzonych rozwiązaniach dotyczących usług Azure Functions](functions-best-practices.md)
