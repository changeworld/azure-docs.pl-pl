---
title: Jak używać zestawu SDK usługi WebJobs — platformy Azure
description: Dowiedz się więcej na temat pisania kodu dla zestawu SDK usługi WebJobs. Tworzyć oparte na zdarzeniach tła zadania przetwarzania, uzyskujących dostęp do danych w usługach platformy Azure i usługi innych firm.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 38d8bdfcba48d2080b434ebec192b41f3663ae6a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895211"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Jak używać zestawu Azure WebJobs SDK na potrzeby przetwarzania w tle oparte na zdarzeniach

Ten artykuł zawiera wskazówki na temat sposobu pracy z zestawu Azure WebJobs SDK. Aby rozpocząć pracę już teraz za pomocą zadań Webjob, zobacz [Rozpoczynanie pracy z usługą Azure WebJobs SDK, przetwarzanie w tle oparte na zdarzeniach](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Wersje zestawu SDK usługi WebJobs

Są to podstawowe różnice między w wersji 3. *x* i wersji 2. *x* zestawu WebJobs SDK:

* W wersji 3. *x* dodaje obsługę platformy .NET Core.
* W wersji 3. *x*, trzeba jawnie instalować rozszerzenie powiązania magazynu wymaganego przez zestaw SDK zadań Webjob. W wersji 2. *x*, powiązania magazynu zostały uwzględnione w zestawie SDK.
* Narzędzia dla programu .NET Core programu Visual Studio (3. *x*) projektów różni się od narzędzia dla programu .NET Framework (2. *x*) projektów. Aby dowiedzieć się więcej, zobacz [tworzenie i wdrażanie zadań Webjob za pomocą programu Visual Studio — usłudze Azure App Service](webjobs-dotnet-deploy-vs.md).

Jeśli to możliwe, przykłady są udostępniane dla obu wersji 3. *x* i wersji 2. *x*.

> [!NOTE]
> [Usługa Azure Functions](../azure-functions/functions-overview.md) bazuje na zestawie SDK usługi WebJobs i ten artykuł zawiera linki do dokumentacji usługi Azure Functions, aby niektóre tematy. Należy zwrócić uwagę te różnice pomiędzy funkcjami i zestaw SDK zadań Webjob:
> * Usługa Azure Functions w wersji 2. *x* odnosi się do zestawu WebJobs SDK w wersji 3. *x*, a usługa Azure Functions 1. *x* odnosi się do 2 zestawu SDK usługi WebJobs. *x*. Repozytoriów kodu źródłowego korzystanie z zestawu SDK usługi WebJobs, numerowania.
> * Przykładowy kod dla usługi Azure Functions C# bibliotek klas jest podobnie do kodu zestawu SDK usługi WebJobs, jednak nie ma potrzeby `FunctionName` atrybutu w projekcie zestawu SDK usługi WebJobs.
> * Niektóre typy powiązania są obsługiwane tylko w funkcji, takich jak HTTP (elementy Webhook) i usługi Event Grid, (która zależy od protokołu HTTP).
>
> Aby uzyskać więcej informacji, zobacz [porównanie zestawu SDK usługi WebJobs i usługi Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host usługi WebJobs

Host jest kontener środowiska uruchomieniowego dla funkcji.  Nasłuchuje wyzwalaczy i wywołania funkcji. W wersji 3. *x*, host jest implementacją `IHost`. W wersji 2. *x*, możesz użyć `JobHost` obiektu. Utwórz wystąpienie hosta w kodzie i napisać kod, aby dostosować jego zachowanie.

Jest to Najważniejszą różnicą między bezpośrednio przy użyciu zestawu SDK usługi WebJobs i korzystania z niego pośrednio za pośrednictwem usługi Azure Functions. W usłudze Azure Functions usługa kontroluje hosta, a host nie można dostosować przez napisanie kodu. Usługa Azure Functions umożliwia dostosowywanie zachowania dotyczącego hosta, za pomocą ustawień w pliku host.json. Te ustawienia są ciągami, nie kodu i ogranicza to rodzajów dostosowań, które można wykonać.

### <a name="host-connection-strings"></a>Parametry połączenia hosta

Zestaw SDK zadań Webjob wyszukuje parametry połączenia usługi Azure Storage i Azure Service Bus w pliku local.settings.json, po uruchomieniu lokalnie lub w środowisku zadania WebJob, po uruchomieniu na platformie Azure. Domyślnie, ustawienie o nazwie parametrów połączenia magazynu `AzureWebJobsStorage` jest wymagana.  

W wersji 2. *x* zestawu SDK pozwala użyć własnych nazw dla tych parametrów połączenia lub przechowywać je w innym miejscu. Można ustawić nazwy w kodzie za pomocą [ `JobHostConfiguration` ], jak pokazano poniżej:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Ponieważ w wersji 3. *x* używa domyślnej konfiguracji platformy .NET Core interfejsów API, nie ma żadnych interfejsów API można zmienić nazw parametrów połączenia.

### <a name="host-development-settings"></a>Ustawienia środowiska deweloperskiego hosta

W trybie projektowania, aby efektywności rozwoju lokalnych, można uruchomić hosta. Poniżej przedstawiono niektóre ustawienia, które zostały zmienione po uruchomieniu w trybie projektowania:

| Właściwość | Ustawienie rozwoju |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Aby zmaksymalizować dane wyjściowe dziennika. |
| `Queues.MaxPollingInterval`  | Niską wartość, aby upewnić się, że metody kolejki są natychmiast wyzwalane.  |
| `Singleton.ListenerLockPeriod` | 15 sekund, aby ułatwić szybkie, iteracyjne projektowanie. |

Proces włączania trybu opracowywania zależy od wersji zestawu SDK. 

#### <a name="version-3x"></a>W wersji 3. *x*

W wersji 3. *x* używa standardowych podstawowe interfejsy API platformy ASP.NET. Wywołaj [ `UseEnvironment` ](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metody [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) wystąpienia. Przekazywanie parametrów o nazwie `development`, jak w tym przykładzie:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>W wersji 2. *x*

`JobHostConfiguration` Klasa ma `UseDevelopmentSettings` metodę, która włącza tryb projektowania.  Poniższy przykład pokazuje, jak można użyć ustawienia środowiska deweloperskiego. Aby `config.IsDevelopment` zwracają `true` po jego uruchomieniu lokalnie, należy ustawić zmienną środowiskową lokalnego o nazwie `AzureWebJobsEnv` z wartością `Development`.

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-servicepointmanager-settings"></a>Zarządzanie równoczesnych połączeń (w wersji 2. *x*)

W wersji 3. *x*, limitu połączeń, wartość domyślna to nieskończoną połączeń. Jeśli z jakiegoś powodu, musisz zmienić ten limit, można użyć [ `MaxConnectionsPerServer` ](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) właściwość [ `WinHttpHandler` ](/dotnet/api/system.net.http.winhttphandler) klasy.

W wersji 2. *x*, kontrolować liczbę jednoczesnych połączeń z hostem za pomocą [ServicePointManager.DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) interfejsu API. W wersji 2. *x*, należy zwiększyć tę wartość z domyślnej wartości 2 przed uruchomieniem hosta usługi WebJobs.

Wszystkie wychodzące żądania HTTP, wprowadzone przez funkcję za pomocą `HttpClient` przepływać przez `ServicePointManager`. Po osiągnięciu wartości ustawionej `DefaultConnectionLimit`, `ServicePointManager` uruchamia Kolejkowanie żądania przed ich wysłaniem. Załóżmy, że Twoje `DefaultConnectionLimit` jest równa 2, a kod sprawia, że 1000 HTTP żądań. Początkowo tylko dwa żądania mogą za pomocą systemu operacyjnego. Inne 998 są umieszczane w kolejce do momentu miejsca dla nich. Oznacza to, że Twoje `HttpClient` może upłynąć limit czasu, ponieważ prawdopodobnie wprowadzono żądanie, ale żądanie nigdy nie zostało wysłane przez system operacyjny na serwerze docelowym. Dlatego może zostać wyświetlony zachowanie, które prawdopodobnie nie ma sensu: lokalnej `HttpClient` trwa 10 sekund na ukończenie żądania przez, ale zwraca każdego żądania usługi w 200 ms. 

Wartość domyślna dla aplikacji platformy ASP.NET to `Int32.MaxValue`, i który może działać dobrze w przypadku zadań Webjob, działające w warstwie podstawowa lub wyższej Plan usługi App Service. Zadania Webjob zazwyczaj konieczne ustawienie zawsze włączone i który jest obsługiwany tylko przez podstawowa i wyższych planów usługi App Service.

Jeśli zadania WebJob działa w warstwie bezpłatna lub udostępnionych planów usługi App Service, aplikacja jest ograniczony przez piaskownicy usługi App Service, który aktualnie ma [limit połączeń 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Z limitem niepowiązanych połączenia w `ServicePointManager`, jest bardziej prawdopodobne, że będzie można połączyć się z piaskownicy próg połączeń i lokacji zostanie zamknięty. W takim przypadku ustawienie `DefaultConnectionLimit` coś o nim małe, takich jak 50 lub 100, można temu zapobiec i nadal umożliwiają wystarczającą przepustowość.

Ustawienie musi być skonfigurowany, zanim wszystkie żądania HTTP zostały wprowadzone. Z tego powodu hosta zadania Webjob nie należy dostosować ustawienie automatycznie. Może to być żądań HTTP, które występują przed uruchomieniem hosta, co może prowadzić do nieoczekiwanego zachowania. Najlepszym rozwiązaniem jest wartość bezpośrednio w Twojej `Main` metody przed inicjowanie `JobHost`, jak pokazano poniżej:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Wyzwalacze

Funkcje muszą być metody publiczne i musi mieć jeden atrybut wyzwalacza lub [ `NoAutomaticTrigger` ](#manual-triggers) atrybutu.

### <a name="automatic-triggers"></a>Automatycznymi wyzwalaczami

Automatycznych wyzwalaczy wywołać funkcję w odpowiedzi na zdarzenie. Rozważmy następujący przykład funkcji, która jest wyzwalana przez komunikacie dodanym do usługi Azure Queue storage. Odpowiada za odczytywanie obiektu blob z magazynu obiektów Blob platformy Azure:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger` Środowiska uruchomieniowego, aby wywołać funkcję w każdym przypadku, gdy w kolejce zostanie wyświetlony komunikat informuje, atrybut `myqueue-items` kolejki. `Blob` Atrybut informuje środowiska uruchomieniowego do użycia komunikatu w kolejce w celu odczytania obiektu blob w *elementy robocze na przykład* kontenera. Zawartość komunikatu w kolejce przekazany do funkcji w `myQueueItem` parametr, jest nazwa obiektu blob.


### <a name="manual-triggers"></a>Wyzwalacze ręczne

Aby ręcznie wyzwolić funkcję, należy użyć `NoAutomaticTrigger` atrybutu, jak pokazano poniżej:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Proces ręcznego wyzwalania funkcji zależy od wersji zestawu SDK.

#### <a name="version-3x"></a>W wersji 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>W wersji 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Dane wejściowe i wyjściowe powiązania

Powiązania danych wejściowych zapewniają deklaratywną metodę udostępniania danych na platformie Azure lub usług innych firm w kodzie. Powiązania danych wyjściowych zapewniają sposób, aby zaktualizować dane. [Wprowadzenie](webjobs-sdk-get-started.md) artykule przedstawiono przykład każdego z nich.

Wartość zwracaną metody można użyć dla powiązania danych wyjściowych, stosując atrybut na wartość zwracaną metody. Zobacz przykład w [przy użyciu funkcji platformy Azure zwracają wartość](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Typ powiązania

Proces instalowania i zarządzania typy wiązań zależy od tego, czy używany jest w wersji 3. *x* lub w wersji 2. *x* zestawu SDK. Pakiet do zainstalowania dla typu określonego powiązania w sekcji "Packages" tego typu powiązania usługi Azure Functions można znaleźć [artykule informacyjnym na temat](#binding-reference-information). Wyjątek stanowi pliki wyzwalacz i powiązania (w przypadku lokalnego systemu plików), który nie jest obsługiwany przez usługi Azure Functions.

#### <a name="version-3x"></a>W wersji 3. *x*

W wersji 3. *x*, powiązania magazynu są uwzględnione w `Microsoft.Azure.WebJobs.Extensions.Storage` pakietu. Wywołaj `AddAzureStorage` metody rozszerzenia w `ConfigureWebJobs` metody, jak pokazano poniżej:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Aby użyć innych wyzwalacza i typ powiązania, zainstaluj pakiet NuGet, który zawiera ich, a następnie wywołać `Add<binding>` — metoda rozszerzenia zaimplementowane w rozszerzeniu. Na przykład, jeśli chcesz użyć powiązania usługi Azure Cosmos DB, zainstaluj `Microsoft.Azure.WebJobs.Extensions.CosmosDB` i wywołać `AddCosmosDB`, podobnie do następującego:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

Wyzwalacz czasomierza lub pliki powiązanie, które są częścią podstawowych usług, wywołaj `AddTimers` lub `AddFiles` metody rozszerzenia, odpowiednio.

#### <a name="version-2x"></a>W wersji 2. *x*

Te typy wyzwalaczy i powiązań są uwzględnione w wersji 2. *x* z `Microsoft.Azure.WebJobs` pakietu:

* Blob Storage
* Queue Storage
* Magazyn tabel

Aby użyć innych wyzwalacza i typ powiązania, zainstaluj pakiet NuGet, który zawiera ich, a następnie wywołać `Use<binding>` metody `JobHostConfiguration` obiektu. Na przykład, jeśli chcesz użyć wyzwalacza czasomierza, zainstaluj `Microsoft.Azure.WebJobs.Extensions` i wywołać `UseTimers` w `Main` metody, jak pokazano poniżej:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Aby korzystać z plików powiązanie, zainstaluj `Microsoft.Azure.WebJobs.Extensions` i wywołać `UseFiles`.

### <a name="executioncontext"></a>kontekście wykonywania

Zadania Webjob pozwala powiązać [ `ExecutionContext` ]. Z tego powiązania może uzyskać dostęp do [ `ExecutionContext` ] jako parametr w podpisu funkcji. Na przykład poniższy kod używa obiektu context dostępu identyfikator wywołania, które służy do skorelowania wszystkie dzienniki generowane przez wywołania danej funkcji do.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Proces wiązania [ `ExecutionContext` ] zależy od używanej wersji zestawu SDK.

#### <a name="version-3x"></a>W wersji 3. *x*

Wywołaj `AddExecutionContextBinding` metody rozszerzenia w `ConfigureWebJobs` metody, jak pokazano poniżej:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        host.Run();
    }
}
```

#### <a name="version-2x"></a>W wersji 2. *x*

`Microsoft.Azure.WebJobs.Extensions` Pakietu, o których wspomniano wcześniej udostępnia również typ powiązania specjalne, który można zarejestrować, wywołując `UseCore` metody. To powiązanie pozwala zdefiniować [ `ExecutionContext` ] parametru w sygnatura funkcji, który jest włączany w następujący sposób:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Konfiguracja powiązania

Można skonfigurować zachowanie niektórych wyzwalaczy i powiązań. Proces konfigurowania ich, zależy od wersji zestawu SDK.

* **W wersji 3. *x*:** Konfiguracja zestawu podczas `Add<Binding>` metoda jest wywoływana w `ConfigureWebJobs`.
* **W wersji 2. *x*:** Konfiguracja zestawu przez ustawienie właściwości w obiekcie konfiguracji, który jest przekazywany do `JobHost`.

Te ustawienia specyficzne dla powiązania są równoważne w ustawieniach [pliku projektu host.json](../azure-functions/functions-host-json.md) w usłudze Azure Functions.

Można skonfigurować powiązania następujących:

* [Wyzwalacz usługi Azure cosmos DB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Wyzwalanie usługi Event Hubs](#event-hubs-trigger-configuration-version-3x)
* Wyzwalacz kolejki usługi storage
* [Powiązania usługi SendGrid](#sendgrid-binding-configuration-version-3x)
* [Wyzwalacz usługi Service Bus](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfiguracja wyzwalacza usługi Azure cosmos dB (w wersji 3. *x*)

Ten przykład pokazuje, jak skonfigurować wyzwalacz usługi Azure Cosmos DB:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Aby uzyskać więcej informacji, zobacz [wiązania Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) artykułu.

### <a name="event-hubs-trigger-configuration-version-3x"></a>Usługa Event Hubs wyzwalania konfiguracji (w wersji 3. *x*)

W tym przykładzie przedstawiono sposób konfigurowania wyzwalacza usługi Event Hubs:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Aby uzyskać więcej informacji, zobacz [powiązania usługi Event Hubs](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) artykułu.

### <a name="queue-storage-trigger-configuration"></a>Konfiguracja wyzwalacza magazynu kolejki

Te przykłady pokazują, jak skonfigurować wyzwalacz kolejki magazynu:

#### <a name="version-3x"></a>W wersji 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Aby uzyskać więcej informacji, zobacz [kolejki magazynu powiązania](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) artykułu.

#### <a name="version-2x"></a>W wersji 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Aby uzyskać więcej informacji, zobacz [v1.x dokumentacja pliku host.JSON](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Konfiguracja powiązania usługi SendGrid (w wersji 3. *x*)

Ten przykład przedstawia sposób konfigurowania usługi SendGrid powiązania danych wyjściowych:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Aby uzyskać więcej informacji, zobacz [powiązania usługi SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) artykułu.

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfiguracja wyzwalacza usługi Service Bus (w wersji 3. *x*)

Ten przykład pokazuje, jak skonfigurować wyzwalacz usługi Service Bus:

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Aby uzyskać więcej informacji, zobacz [powiązania usługi Service Bus](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) artykułu.

### <a name="configuration-for-other-bindings"></a>Konfiguracja w przypadku innych powiązań

Niektóre typy wyzwalaczy i powiązań zdefiniować typy konfiguracji niestandardowej. Na przykład wyzwalacz plik pozwala określić ścieżkę katalogu głównego, aby monitorować, jak w następujących przykładach:

#### <a name="version-3x"></a>W wersji 3. *x*

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

#### <a name="version-2x"></a>W wersji 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Wyrażenia wiązania

W parametrach konstruktora atrybutu można użyć wyrażenia, które nawiązują do wartości z różnych źródeł. Na przykład w poniższym kodzie, ścieżka `BlobTrigger` atrybutu jest tworzone wyrażenie o nazwie `filename`. Gdy jest używana do powiązania danych wyjściowych `filename` jest rozpoznawany jako nazwa wyzwalająca obiektu blob.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Aby uzyskać więcej informacji na temat wyrażenia wiązania zobacz [powiązania wyrażeń i wzorce](../azure-functions/functions-bindings-expressions-patterns.md) w dokumentacji usługi Azure Functions.

### <a name="custom-binding-expressions"></a>Wyrażenia powiązania niestandardowego

Czasami trzeba określić nazwę kolejki, nazwa obiektu blob lub kontenera lub nazwę tabeli w kodzie zamiast kodować je. Na przykład możesz chcieć określić nazwę kolejki `QueueTrigger` atrybutu w zmiennej środowisku lub pliku konfiguracji.

Możesz to zrobić, przekazując `NameResolver` do obiektu w `JobHostConfiguration` obiektu. Zawierają symbole zastępcze w wyzwalacza i parametry konstruktora atrybutów powiązania, a `NameResolver` kod zawiera rzeczywiste wartości, które będą używane zamiast te symbole zastępcze. Identyfikowanie symboli zastępczych przez umieszczenie ich za pomocą procentu (%) znaki, jak pokazano poniżej:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ten kod pozwala użyć kolejkę o nazwie `logqueuetest` w środowisku testowym i jedną o nazwie `logqueueprod` w środowisku produkcyjnym. Zamiast nazwy kolejki zakodowane, należy określić nazwę wpisu w `appSettings` kolekcji.

Brak domyślnego `NameResolver` jego faktycznego wycofania, jeśli nie podasz niestandardowego. Wartość domyślna pobiera wartości z ustawień aplikacji lub zmienne środowiskowe.

Twoje `NameResolver` klasa otrzymuje nazwę kolejki z `appSettings`, jak pokazano poniżej:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>W wersji 3. *x*

Mechanizm rozpoznawania można skonfigurować za pomocą iniekcji zależności. Te przykłady wymagają następującej `using` instrukcji:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Dodaj program rozpoznawania nazw, wywołując [ `ConfigureServices` ] metody rozszerzenia [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder), jak w tym przykładzie:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>W wersji 2. *x*

Przekaż swoje `NameResolver` klasy w celu `JobHost` obiektu, jak pokazano poniżej:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Platforma Azure implementuje funkcje `INameResolver` można pobrać wartości z ustawień aplikacji, jak pokazano w przykładzie. Gdy używasz zestawu SDK usługi WebJobs bezpośrednio, możesz napisać implementację niestandardową, która symbol zastępczy wartości zastępcze z dowolnego źródła, użytkownik sobie tego życzy.

## <a name="binding-at-runtime"></a>Powiązania w czasie wykonywania

Jeśli potrzeba wykonania dodatkowych czynności, w funkcji, zanim użyjesz atrybut wiązania, takich jak `Queue`, `Blob`, lub `Table`, możesz użyć `IBinder` interfejsu.

Poniższy przykład pobiera komunikat kolejki danych wejściowych i tworzy nowy komunikat o tej samej zawartości w kolejce danych wyjściowych. Nazwa kolejki danych wyjściowych jest ustawiany przez kod w treści funkcji.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Aby uzyskać więcej informacji, zobacz [powiązania w czasie wykonywania](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) w dokumentacji usługi Azure Functions.

## <a name="binding-reference-information"></a>Informacje o powiązaniu odwołania

Dokumentacja usługi Azure Functions zapewnia informacje na temat każdego typu powiązania. Poniższe informacje znajdziesz w artykule informacyjnym na temat każdego powiązania. (Ten przykład jest oparty na kolejkę magazynu).

* [Pakiety](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Pakiet, który należy zainstalować na obejmują pomoc techniczną dla powiązania w projekcie zestawu SDK usługi WebJobs.
* [Przykłady](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Przykłady kodu. C# Przykład biblioteki klasy mają zastosowanie do zestawu SDK usługi WebJobs. Po prostu pominąć `FunctionName` atrybutu.
* [Atrybuty](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Typ atrybutów do użycia dla wiązania.
* [Konfiguracja](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Wyjaśnienia właściwości atrybutów i parametry konstruktora.
* [Użycie](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Typy, które można powiązać i informacje o tym, jak działa powiązanie. Na przykład: sondowanie algorytmu, zanieczyszczonych kolejki przetwarzania.
  
Aby uzyskać listę powiązania gama artykułów, zobacz "Obsługiwane powiązania" w [wyzwalaczy i powiązań](../azure-functions/functions-triggers-bindings.md#supported-bindings) artykułu dla usługi Azure Functions. Na tej liście powiązania protokołu HTTP, elementy Webhook i usługi Event Grid są obsługiwane tylko przez usługi Azure Functions, a nie przez zestaw SDK zadań Webjob.

## <a name="disable-attribute"></a>Wyłącz atrybutu 

[ `Disable` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) Atrybut umożliwia kontrolowanie, czy funkcja może być uruchamiane. 

W poniższym przykładzie Jeśli ustawienie aplikacji `Disable_TestJob` ma wartość `1` lub `True` (wielkich liter), funkcja nie będzie działać. W takim przypadku środowisko uruchomieniowe tworzy komunikat dziennika *jest wyłączona funkcja "Functions.TestJob"*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Po zmianie wartości ustawień aplikacji w witrynie Azure portal, aby pobrać nowe ustawienie powoduje ponowne uruchomienie zadania WebJob.

Ten atrybut może być zadeklarowana w poziomie parametrów, metody lub klasy. Nazwa ustawienia może również zawierać wyrażenia wiązania.

## <a name="timeout-attribute"></a>Atrybut limitu czasu

[ `Timeout` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) Atrybutu powoduje, że funkcję, która ma zostać anulowane, jeśli go nie zakończone w określonym czasie. W poniższym przykładzie funkcja może działać bez określonego atrybutem limitu czasu przez jeden dzień. Limit czasu powoduje, że funkcja zostaną anulowane po 15 sekundach.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Można zastosować atrybut limitu czasu na poziomie klasy lub metody i globalnego limitu czasu można określić za pomocą `JobHostConfiguration.FunctionTimeout`. Przekroczenia limitu czasu na poziomie klasy lub metody — zastąpić globalny przekroczeń limitu czasu.

## <a name="singleton-attribute"></a>Atrybut pojedynczego wystąpienia

[ `Singleton` ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) Atrybutu gwarantuje, że tylko jedno wystąpienie funkcji przebiegów, nawet wtedy, gdy istnieje wiele wystąpień aplikacji sieci web hosta. Jest to realizowane przy użyciu [rozproszonych blokowania](#viewing-lease-blobs).

W tym przykładzie, tylko jedno wystąpienie `ProcessImage` funkcja działa w danym momencie:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Niektóre wyzwalacze mają wbudowaną obsługę współbieżności zarządzania:

* **QueueTrigger**. Ustaw `JobHostConfiguration.Queues.BatchSize` do `1`.
* **ServiceBusTrigger**. Ustaw `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` do `1`.
* **FileTrigger**. Ustaw `FileProcessor.MaxDegreeOfParallelism` do `1`.

Te ustawienia można użyć, aby upewnić się, że uruchomieniu funkcji jako pojedynczą w pojedynczym wystąpieniu. Aby upewnić się, że tylko jedno wystąpienie funkcji jest uruchomiona, gdy aplikacja sieci web, którą można skalować się do wielu wystąpień, należy zastosować blokadę pojedyncze poziom odbiornika dla funkcji (`[Singleton(Mode = SingletonMode.Listener)]`). Odbiornik blokady są nabywane po uruchomieniu JobHost. Jeśli wszystkie trzy wystąpienia skalowanego uruchamia się w tym samym czasie, tylko jedno wystąpienie uzyskuje blokadę, a następnie uruchamia tylko jeden odbiornik.

### <a name="scope-values"></a>Wartości zakresu

Można określić *zakres wartości dlawyrażenia/* na wzorzec singleton. Wyrażenie/wartość zapewnia będzie serializowana wszystkich wykonań funkcji w określonym zakresie. Implementowanie bardziej szczegółowego blokowania w ten sposób umożliwia pewien stopień równoległości dla funkcji podczas serializowania innych wywołań, zgodnie z ustawieniem wymagań. Na przykład w poniższym kodzie wyrażenie zakresu wiąże `Region` wartość komunikatu przychodzącego. Gdy kolejka zawiera trzy wiadomości w regionach wschodnie, Wschód i zachód odpowiednio, wiadomości, które mają region wschodnie stany są uruchamiane pojedynczo otrzymało komunikat z regionu zachodnie jest wykonywane równolegle z programami znajdującymi się na wschodnim.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope.Host

Domyślny zakres blokady jest `SingletonScope.Function`, co oznacza zakres blokady (ścieżka obiektu blob dzierżawy) jest powiązana z w pełni kwalifikowana nazwa funkcji. Aby zablokować różnych funkcji, należy określić `SingletonScope.Host` i użyj nazwy Identyfikatora zakresu, która jest taka sama we wszystkich funkcji, które nie mają być uruchomione jednocześnie. W poniższym przykładzie, tylko jedno wystąpienie `AddItem` lub `RemoveItem` działa w czasie:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Wyświetlanie obiektów blob z dzierżawy

Zestaw SDK zadań Webjob używa [dzierżawy usługi Azure blob](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) dzieje się w tle do zaimplementowania rozproszonych blokowania. Obiekty BLOB dzierżawy, używane przez pojedynczego wystąpienia można znaleźć w `azure-webjobs-host` kontenera w `AzureWebJobsStorage` konta magazynu w ścieżce "blokady". Na przykład ścieżka obiektu blob dzierżawy w pierwszym `ProcessImage` przedstawionym wcześniej przykładzie może być `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Wszystkie ścieżki obejmują identyfikator JobHost, w tym 061851c758f04938a4426aa9ab3869c0 wielkości liter.

## <a name="async-functions"></a>Funkcje asynchroniczne

Aby dowiedzieć się, jak kod funkcji asynchronicznych, zobacz [dokumentacji usługi Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Anulowanie tokenów

Aby informacji na temat sposobu obsługi tokenów anulowania, zobacz dokumentację usługi Azure Functions w [anulowanie tokenów i łagodne zamykanie](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Wiele wystąpień

Jeśli aplikacja sieci web działa w wielu wystąpieniach, ciągłe zadanie WebJob działa na każdym wystąpieniu nasłuchiwanie wyzwalaczy i wywoływanie funkcji. Różne powiązania wyzwalacza są przeznaczone do wydajnego udostępnianie pracy wspólnie w wystąpieniach, umożliwiając skalowanie w poziomie do większej liczby wystąpień pozwala obsługiwać większe obciążenie.

Wyzwalacze kolejek i obiektów blob automatycznie uniemożliwić funkcji przetwarzania komunikatu w kolejce lub więcej niż raz, a obiekt blob funkcje nie mają być idempotentne.

Wyzwalacz czasomierza zapewni tego tylko jedno wystąpienie uruchamia czasomierz, dzięki czemu nie uzyskasz więcej niż jedno wystąpienie funkcji uruchamiania w danym momencie zaplanowane.

Jeśli chcesz upewnić się, że tylko jedno wystąpienie funkcji jest uruchamiany nawet wtedy, gdy istnieje wiele wystąpień hosta aplikacji sieci web, można użyć [ `Singleton` ](#singleton-attribute) atrybutu.

## <a name="filters"></a>Filtry

Funkcja filtry (wersja zapoznawcza) umożliwiają dostosowywanie potoku wykonywania zadań Webjob za pomocą własnej logiki. Filtry są podobne do [filtrów platformy ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Można je zaimplementować jako deklaratywne atrybuty, które są stosowane do funkcji lub klasy. Aby uzyskać więcej informacji, zobacz [filtry funkcja](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Firma Microsoft zaleca struktury rejestrowania, który został opracowany dla programu ASP.NET. [Wprowadzenie](webjobs-sdk-get-started.md) artykule pokazano, jak z niego korzystać. 

### <a name="log-filtering"></a>Filtrowanie dziennika

Każdy dziennik utworzony przez `ILogger` wystąpienia ma skojarzoną `Category` i `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) to wyliczenie, a kod liczby całkowitej wskazuje względne znaczenie:

|LogLevel    |Kod|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczny    | 5 |
|Brak        | 6 |

Można filtrować niezależnie każdej kategorii do określonego [ `LogLevel` ](/dotnet/api/microsoft.extensions.logging.loglevel). Na przykład możesz chcieć wyświetlić wszystkie dzienniki obiektów blob wyzwalacza przetwarzania jednak tylko `Error` lub nowszy dla wszystkich innych.

#### <a name="version-3x"></a>W wersji 3. *x*

W wersji 3. *x* zestawu SDK opiera się na filtrowanie wbudowanych w platformy .NET Core. `LogCategories` Klasy pozwala zdefiniować kategorie dla określonych funkcji, wyzwalacze bądź użytkowników. Umożliwia on również Definiowanie filtrów dla stanów określonego hosta, takie jak `Startup` i `Results`. Dzięki temu można dostosowywać rejestrowania danych wyjściowych. Jeśli nie znajdzie dopasowania w zdefiniowanych kategoriach, filtr powraca do `Default` wartość przy podejmowaniu decyzji do filtrowania wiadomości.

`LogCategories` wymaga następujących instrukcję using:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Poniższy przykład tworzy filtr, domyślnie wszystkie dzienniki w `Warning` poziom. `Function` i `results` kategorii (równoważne `Host.Results` w wersji 2. *x*) są filtrowane w `Error` poziom. Filtr porównuje bieżącej kategorii na wszystkich poziomach zarejestrowanego w `LogCategories` wystąpienia i wybiera najdłuższego dopasowania. Oznacza to, że `Debug` poziom zarejestrowany dla `Host.Triggers` odpowiada `Host.Triggers.Queue` lub `Host.Triggers.Blob`. Dzięki temu można kontrolować szerszych kategorii bez konieczności dodawania każdej z nich.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>W wersji 2. *x*

W wersji 2. *x* zestawu SDK, możesz użyć `LogCategoryFilter` klasy do kontrolowania filtrowania. `LogCategoryFilter` Ma `Default` właściwość o wartości początkowej `Information`, co oznacza, że komunikaty w `Information`, `Warning`, `Error`, lub `Critical` poziomy są rejestrowane, ale komunikaty w `Debug` lub `Trace` poziomy są filtrowane natychmiast.

Podobnie jak w przypadku `LogCategories` w wersji 3. *x*, `CategoryLevels` Właściwość pozwala określić poziomy dziennika dla poszczególnych kategorii, więc można dostosować, rejestrowanie danych wyjściowych. Jeśli nie zostanie znalezione dopasowanie w ciągu `CategoryLevels` słownika, filtr powraca do `Default` wartość przy podejmowaniu decyzji do filtrowania wiadomości.

Poniższy przykład tworzy domyślne filtry umożliwiające wszystkie dzienniki w `Warning` poziom. `Function` i `Host.Results` kategorie są filtrowane w `Error` poziom. `LogCategoryFilter` Porównuje bieżącej kategorii do wszystkich zarejestrowanych `CategoryLevels` i wybiera najdłuższego dopasowania. Więc `Debug` poziom zarejestrowany dla `Host.Triggers` będzie odpowiadał `Host.Triggers.Queue` lub `Host.Triggers.Blob`. Dzięki temu można kontrolować szerszych kategorii bez konieczności dodawania każdej z nich.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Telemetria niestandardowa usługi Application Insights

Proces implementacji niestandardowych danych telemetrycznych dla [usługi Application Insights](../azure-monitor/app/app-insights-overview.md) zależy od wersji zestawu SDK. Aby dowiedzieć się, jak skonfigurować usługę Application Insights, zobacz [rejestrowania Dodaj usługę Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>W wersji 3. *x*

Ponieważ w wersji 3. *x* zestawu WebJobs SDK zależy od ogólnych hosta, fabrykę niestandardowej telemetrii nie jest dostępny w programie .NET Core. Ale można dodać niestandardowych danych telemetrycznych do potoku za pomocą iniekcji zależności. Przykłady w tej sekcji wymagają następującej `using` instrukcji:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Następujące niestandardowych implementacji [ `ITelemetryInitializer` ] umożliwia dodanie własnych [ `ITelemetry` ](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) domyślną [ `TelemetryConfiguration` ].

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Wywołaj [ `ConfigureServices` ] w konstruktorze, aby dodać niestandardowe [ `ITelemetryInitializer` ] do potoku.

```cs
static void Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {

        host.Run();
    }
}
```

Gdy [ `TelemetryConfiguration` ] jest konstruowany, wszystkie zarejestrowane typy [ `ITelemetryInitializer` ] są uwzględniane. Aby dowiedzieć się więcej, zobacz [interfejsu API usługi Application Insights dla niestandardowych zdarzeń i metryk](../azure-monitor/app/api-custom-events-metrics.md).

W wersji 3. *x*, nie musisz już opróżnić [ `TelemetryClient` ] zatrzymania hosta. System iniekcji zależności platformy .NET Core automatycznie usuwa zarejestrowaną `ApplicationInsightsLoggerProvider`, które opróżnienia [ `TelemetryClient` ].

#### <a name="version-2x"></a>W wersji 2. *x*

W wersji 2. *x*, [ `TelemetryClient` ] utworzone wewnętrznie przez dostawcę usługi Application Insights dla korzysta z zestawem SDK usługi WebJobs [ `ServerTelemetryChannel` ](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Gdy punkt końcowy usługi Application Insights jest niedostępny lub ograniczania żądań przychodzących, ten kanał [zapisuje żądań w systemie plików aplikacji sieci web i przesyła je ponownie później](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[ `TelemetryClient` ] Jest tworzony przez klasę, która implementuje `ITelemetryClientFactory`. Domyślnie jest to [ `DefaultTelemetryClientFactory` ](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Jeśli chcesz zmodyfikować dowolnej części potoku usługi Application Insights, możesz podać własne `ITelemetryClientFactory`, a host będzie używać klasy do konstruowania [ `TelemetryClient` ]. Na przykład, ten kod zastępuje `DefaultTelemetryClientFactory` zmodyfikować właściwość `ServerTelemetryChannel`:

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings` Konfiguruje obiektu [próbkowanie adaptacyjne](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Oznacza to, że w niektórych scenariuszach mocno obciążające wgląd w aplikacje wysyła wybrany podzbiór danych telemetrycznych do serwera.

Po utworzeniu fabryki danych telemetrycznych, przekaż go do dostawcy logowania usługi Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Następne kroki

W tym artykule udostępnił fragmenty kodu pokazujące sposób obsługi typowe scenariusze dotyczące pracy z zestawem SDK usługi WebJobs. Aby uzyskać pełne przykładów, zobacz [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
[`JobHostConfiguration`]: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
