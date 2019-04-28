---
title: Usługa Azure Functions C# skrypt dokumentacja dla deweloperów
description: Dowiedz się, jak tworzenie funkcji platformy Azure przy użyciu skryptu języka C#.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: usługa Azure Functions, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 12/12/2017
ms.author: glenga
ms.openlocfilehash: 232a235cdbf9dc3934bdac14f9612d6865718823
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120844"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Azure funkcje języka C# (csx) skrypt dokumentacja dla deweloperów

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Ten artykuł stanowi wprowadzenie do projektowania usługi Azure Functions przy użyciu skryptu języka C# (*csx*).

Usługa Azure Functions obsługuje C# i C# script języków programowania. Jeśli szukasz wskazówek [przy użyciu języka C# w programie Visual Studio projekt biblioteki klas](functions-develop-vs.md), zobacz [dokumentacja dla deweloperów języka C#](functions-dotnet-class-library.md).

W tym artykule założono, że zostały już przeczytane [przewodnik dla deweloperów usługi Azure Functions](functions-reference.md).

## <a name="how-csx-works"></a>Jak działa csx

Środowisko skryptu języka C# dla usługi Azure Functions jest oparte na [zestawu Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Dane płyną do funkcji języka C# za pomocą argumenty metody. Argument nazwy zostały określone w `function.json` plików i dostępne są wstępnie zdefiniowane nazwy do uzyskiwania dostępu do elementów, takich jak funkcja rejestratora i anulowania tokeny.

*Csx* format umożliwia pisanie mniej "standardowy" i skoncentrować się na pisaniu tylko funkcji C#. Zamiast zawijania wszystko w przestrzeni nazw i klasy, wystarczy zdefiniować `Run` metody. Obejmują wszystkie odwołania do zestawu i przestrzeni nazw na początku tego pliku w zwykły sposób.

Aplikacja funkcji *csx* pliki są kompilowane podczas inicjowania wystąpienia. Ten krok kompilacji oznacza na przykład zimnego może to trwać dłużej skryptu funkcji języka C# w porównaniu do bibliotek klas języka C#. Ten krok kompilacji jest też, dlaczego funkcji skryptu języka C# są edytowalne w witrynie Azure portal, mimo że bibliotek klas języka C# nie.

## <a name="folder-structure"></a>Struktura folderów

Struktura folderów dla projektu skryptu języka C# wygląda podobnie do poniższego:

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

Brak udostępnionej [host.json](functions-host-json.md) pliku, który może służyć do konfigurowania aplikacji funkcji. Każda funkcja ma swój własny plik kodu (csx) i plik konfiguracji powiązania (function.json).

Rozszerzenia powiązania wymagane w [wersji 2.x](functions-versions.md) funkcji środowiska uruchomieniowego są zdefiniowane w `extensions.csproj` pliku z plikami rzeczywistej biblioteki w `bin` folderu. Podczas tworzenia lokalnie, należy najpierw [zarejestrować rozszerzeń powiązania](./functions-bindings-register.md#local-development-azure-functions-core-tools). Podczas tworzenia funkcji w witrynie Azure portal, rejestracja odbywa się za Ciebie.

## <a name="binding-to-arguments"></a>Powiązanie z argumentami

Dane wejściowe lub wyjściowe jest powiązany z C# script parametru funkcji za pośrednictwem `name` właściwość *function.json* pliku konfiguracji. W poniższym przykładzie przedstawiono *function.json* pliku i *run.csx* plików dla funkcji wyzwalanej przez kolejkę. Ten parametr, który odbiera dane z komunikatu w kolejce nosi `myQueueItem` ponieważ jest to wartość `name` właściwości.

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

`#r` Instrukcji zostało wyjaśnione [w dalszej części tego artykułu](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Obsługiwane typy dla powiązania

Każde powiązanie ma swój własny obsługiwanych typów; na przykład wyzwalacz obiektu blob może służyć jako parametr ciągu parametrem POCO `CloudBlockBlob` parametr lub dowolnego z kilku innych typów obsługiwanych. [Artykule informacyjnym na temat wiązania dla powiązania obiektu blob](functions-bindings-storage-blob.md#trigger---usage) Wyświetla wszystkie obsługiwane typy parametru dla wyzwalacze obiektów blob. Aby uzyskać więcej informacji, zobacz [wyzwalaczy i powiązań](functions-triggers-bindings.md) i [powiązania dokumenty referencyjne dotyczące każdego typu powiązania](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Odwoływanie się do niestandardowych klas

Jeśli musisz użyć niestandardowej klasy zwykłe stare CLR obiektu (— POCO), możesz włączyć definicji klasy, w tym samym pliku lub umieścić go w oddzielnym pliku.

W poniższym przykładzie przedstawiono *run.csx* przykład, który zawiera definicję klasy POCO.

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

Klasa POCO musi mieć metodę getter i setter zdefiniowane dla każdej właściwości.

## <a name="reusing-csx-code"></a>Ponowne użycie kodu csx

Można użyć klasy i metody zdefiniowane w innych *csx* plików w Twojej *run.csx* pliku. Aby to zrobić, należy użyć `#load` dyrektywy w swojej *run.csx* pliku. W poniższym przykładzie procedura logowania o nazwie `MyLogger` są udostępniane w *myLogger.csx* i ładowany do *run.csx* przy użyciu `#load` dyrektywy:

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

Za pomocą udostępnionego *csx* pliku jest to typowy wzorzec, gdy użytkownik chce silnie typizuj dane przekazywane między funkcjami, przy użyciu obiektów POCO. W poniższym przykładzie uproszczona wyzwalacza HTTP i wyzwalacz kolejki udziału obiektów POCO, o nazwie `Order` do silnie typizuj dane zamówień:

Przykład *run.csx* wyzwalacza HTTP:

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

Przykład *run.csx* wyzwalacz kolejki:

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

Przykład *order.csx*:

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

Można użyć ścieżki względnej z `#load` dyrektywy:

* `#load "mylogger.csx"` ładuje plik znajdujący się w folderze funkcji.
* `#load "loadedfiles\mylogger.csx"` ładuje plik znajdujący się w folderze w folderze funkcji.
* `#load "..\shared\mylogger.csx"` ładuje plik znajdujący się w folderze na tym samym poziomie jak folderze funkcji, znajdującą się pod *wwwroot*.

`#load` Dyrektywa działa tylko w przypadku *csx* plików, nie za pomocą *.cs* plików.

## <a name="binding-to-method-return-value"></a>Powiązanie z wartość zwracaną metody

Można użyć wartość zwracaną metody dla powiązania danych wyjściowych przy użyciu nazwy `$return` w *function.json*. Aby uzyskać przykłady, zobacz [wyzwalaczy i powiązań](./functions-bindings-return-value.md).

Użyj wartości zwracanej tylko wtedy, gdy wykonanie funkcji pomyślne, który jest zawsze skutkuje zwracana wartość do przekazania do powiązania danych wyjściowych. W przeciwnym razie użyj `ICollector` lub `IAsyncCollector`, jak pokazano w poniższej sekcji.

## <a name="writing-multiple-output-values"></a>Zapisywanie wielu wartości w danych wyjściowych

Aby zapisać wiele wartości powiązania danych wyjściowych lub jeśli wywołanie pomyślne funkcji nie może prowadzić do niczego do przekazania do powiązania danych wyjściowych, należy użyć [ `ICollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [ `IAsyncCollector` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typów. Te typy są kolekcji tylko do zapisu, które są zapisywane do powiązania danych wyjściowych, po zakończeniu działania metody.

Ten przykład Przepisuje wielu komunikatów w kolejce do tej samej kolejki przy użyciu `ICollector`:

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Rejestrowanie

Aby rejestrować dane wyjściowe do przesyłania strumieniowego dzienników w C#, obejmują argumentu typu [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger). Firma Microsoft zaleca, nadaj mu `log`. Unikaj używania `Console.Write` w usłudze Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Aby o nowszych struktury rejestrowania, który można użyć zamiast `TraceWriter`, zobacz [zapisu rejestruje się w funkcji języka C#](functions-monitoring.md#write-logs-in-c-functions) w **monitora usługi Azure Functions** artykułu.

## <a name="async"></a>asynchroniczne

Aby funkcja [asynchronicznego](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/), użyj `async` — słowo kluczowe i zwrócenie `Task` obiektu.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Nie można użyć `out` parametrów w funkcji asynchronicznych. W przypadku powiązania danych wyjściowych, użyj [wartości zwracanej funkcji](#binding-to-method-return-value) lub [obiekt moduł zbierający](#writing-multiple-output-values) zamiast tego.

## <a name="cancellation-tokens"></a>Anulowanie tokenów

Funkcja może akceptować [CancellationToken](/dotnet/api/system.threading.cancellationtoken) parametr, który umożliwia systemowi operacyjnemu Powiadamiaj swój kod, gdy funkcja ma zostać zakończone. Skorzystaj z tego powiadomienia, aby upewnić się, że funkcja nie nieoczekiwanego zakończenia działania w sposób powodujący, że dane w stanie niespójnym.

Jak sprawdzić, czy zbliżającym się zakończeniu funkcji można znaleźć w poniższym przykładzie.

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

## <a name="importing-namespaces"></a>Importowanie przestrzeni nazw

Jeśli zachodzi potrzeba zaimportuj przestrzenie nazw, możesz to zrobić tak jak zwykle, za pomocą `using` klauzuli.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące przestrzenie nazw są automatycznie importowane i dlatego są opcjonalne:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Odwoływanie się do zestawów zewnętrznych

Dla zestawów framework, należy dodać odwołania za pomocą `#r "AssemblyName"` dyrektywy.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Następujące zestawy są automatycznie dodawane przez środowisko hostingu usługi Azure Functions:

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

Następujące zestawy mogą być przywoływane przez prostą nazwę (na przykład `#r "AssemblyName"`):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Odwoływanie się do zestawów niestandardowych

Aby odwoływać się do niestandardowego zestawu, można użyć albo *udostępnionego* zestawu lub *prywatnej* zestawu:

* Zestawy udostępnione są współdzielone przez wszystkie funkcje w obrębie aplikacji funkcji. Odwołanie do zestawu niestandardowego, należy przekazać do folderu o nazwie zestawu `bin` w swojej [folder główny aplikacji funkcji](functions-reference.md#folder-structure) (wwwroot).

* Zestawy prywatne są dostępne w ramach kontekstu danej funkcji i obsługuje ładowanie bezpośrednie aplikacji w różnych wersjach. Zestawy prywatne powinny zostać przekazane w `bin` folderu w katalogu funkcji. Odwołuje się do zestawów przy użyciu nazwy pliku, takich jak `#r "MyAssembly.dll"`.

Aby uzyskać informacje na temat przekazywania plików do folderu funkcji, zobacz sekcję na [Zarządzanie pakietami](#using-nuget-packages).

### <a name="watched-directories"></a>Katalogi obserwowanych

Katalog, który zawiera plik skryptu funkcji jest automatycznie obserwowanych zmian do zestawów. Aby obejrzeć zestawu zmian w innych katalogach, dodaj je do `watchDirectories` listy w [host.json](functions-host-json.md).

## <a name="using-nuget-packages"></a>Za pomocą pakietów NuGet
Do korzystania z pakietów NuGet 2.x C# funkcji, a następnie przekaż *function.proj* plik do folderu funkcji w systemie plików aplikacji funkcji. Oto przykład *function.proj* pliku, który dodaje odwołanie do *Microsoft.ProjectOxford.Face* wersji *1.1.0*:

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

Aby użyć NuGet niestandardowego źródła danych, określ źródła danych w *Nuget.Config* plik w folderze głównym aplikacji funkcji. Aby uzyskać więcej informacji, zobacz [zachowania programu NuGet Konfigurowanie](/nuget/consume-packages/configuring-nuget-behavior). 

> [!NOTE]
> W 1.x C# funkcje i pakiety NuGet są przywoływane z *project.json* pliku zamiast *function.proj* pliku.

Funkcje 1.x, można użyć *project.json* zamiast tego pliku. Oto przykład *project.json* pliku: 

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

### <a name="using-a-functionproj-file"></a>Przy użyciu pliku function.proj

1. Otwarcie funkcji w witrynie Azure portal. Karta dzienniki są wyświetlane dane wyjściowe instalacji pakietu.
2. Można przekazać *function.proj* pliku, użyj jednej z metod opisanych w [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate) w temacie Dokumentacja dla deweloperów usługi Azure Functions.
3. Po *function.proj* plik zostanie przekazany, zobaczysz, że dane wyjściowe podobne do poniższego przykładu w funkcji użytkownika przesyłania strumieniowego dziennika:

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

Aby uzyskać zmiennej środowiskowej lub wartość ustawienia aplikacji, użyj `System.Environment.GetEnvironmentVariable`, jak pokazano w poniższym przykładzie kodu:

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

## <a name="binding-at-runtime"></a>Powiązania w czasie wykonywania

W języku C# i innych językach .NET, można użyć [imperatywne](https://en.wikipedia.org/wiki/Imperative_programming) powiązanie wzorca, w przeciwieństwie do [ *deklaratywne* ](https://en.wikipedia.org/wiki/Declarative_programming) powiązania w *function.json*. Imperatywne powiązania jest przydatne, gdy Parametry wiążące muszą być obliczane w czasie wykonywania, a nie projekt. W ramach tego wzorca można powiązać obsługiwanych danych wejściowych i danych wyjściowych powiązania na bieżąco w kodzie funkcji.

Zdefiniuj imperatywne powiązania w następujący sposób:

- **Nie** dołączyć wpis w *function.json* dla żądanego imperatywne wiązania.
- Przekaż parametr wejściowy [ `Binder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) lub [ `IBinder binder` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs).
- Użyj następującego wzorca C# do przeprowadzenia powiązania danych.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` jest atrybut .NET, który definiuje wiązania i `T` jest typem danych wejściowych lub wyjściowych, który jest obsługiwany przez ten typ powiązania. `T` nie może być `out` typ parametru (takie jak `out JObject`). Na przykład obsługuje powiązanie danych wyjściowych na tabeli funkcji Mobile Apps [sześć typów w danych wyjściowych](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), ale można używać tylko [ICollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) lub [IAsyncCollector<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs)dla `T`.

### <a name="single-attribute-example"></a>Przykład jeden atrybut

Poniższy przykład kodu tworzy [powiązania danych wyjściowych obiektu blob magazynu](functions-bindings-storage-blob.md#output) przy użyciu obiektu blob ścieżki, która jest zdefiniowana w czasie wykonywania, następnie zapisuje dane w ciągu obiektu blob.

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

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) definiuje [obiektu blob magazynu](functions-bindings-storage-blob.md) wejścia lub wyjścia, powiązania i [TextWriter](/dotnet/api/system.io.textwriter) jest typ powiązania obsługiwanych danych wyjściowych.

### <a name="multiple-attribute-example"></a>Wiele przykład atrybutu

Poprzedni przykład pobiera ustawienia aplikacji dla aplikacji funkcji parametry połączenia dla głównego konta magazynu (czyli `AzureWebJobsStorage`). Można określić ustawienia aplikacji niestandardowej do użycia dla konta magazynu, dodając [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) i przekazując tablicę atrybutów do `BindAsync<T>()`. Użyj `Binder` parametru nie `IBinder`.  Na przykład:

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

W poniższej tabeli przedstawiono atrybuty .NET dla każdego typu powiązania i pakiety, w których są zdefiniowane.

> [!div class="mx-codeBreakAll"]
> | Powiązanie | Atrybut | Dodawanie odwołania |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/ServiceBusAccountAttribute.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Kolejka magazynu | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Obiekt blob magazynu | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Tabela magazynu | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TableAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat wyzwalaczy i powiązań](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Więcej informacji na temat najlepszych rozwiązań dla usługi Azure Functions](functions-best-practices.md)
