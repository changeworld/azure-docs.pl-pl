---
title: Jak używać zestawu SDK zadań WebJob
description: Dowiedz się więcej na temat pisania kodu dla zestawu SDK usługi WebJobs. Twórz oparte na zdarzeniach zadania przetwarzania w tle, które uzyskują dostęp do danych na platformie Azure i w usługach innych firm.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 8e29c632ff3920c77a757fe45475a12c212cf579
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684000"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Jak używać zestawu SDK Azure WebJobs na potrzeby przetwarzania w tle opartego na zdarzeniach

Ten artykuł zawiera wskazówki dotyczące sposobu pracy z zestawem SDK Azure WebJobs. Aby od razu rozpocząć pracę z usługą WebJobs, zobacz Wprowadzenie do [zestawu SDK Azure WebJobs na potrzeby przetwarzania w tle opartego na zdarzeniach](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>Wersje zestawu SDK zadań WebJob

Są to kluczowe różnice między wersją 3. *x* i wersja 2. *x* zestawu SDK zadań WebJob:

* Wersja 3. *x* dodaje obsługę programu .NET Core.
* W wersji 3. *x*należy jawnie zainstalować rozszerzenie powiązania magazynu wymagane przez zestaw SDK zadań WebJob. W wersji 2. *x*, powiązania magazynu zostały uwzględnione w zestawie SDK.
* Narzędzia programu Visual Studio dla platformy .NET Core (3. *x*) różni się od narzędzi dla .NET Framework (2. *x*). Aby dowiedzieć się więcej, zobacz [Tworzenie i wdrażanie zadań WebJob za pomocą programu Visual Studio — Azure App Service](webjobs-dotnet-deploy-vs.md).

Jeśli to możliwe, przykłady są dostępne dla obu wersji 3. *x* i wersja 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) jest oparty na zestawie SDK usługi WebJobs, a w tym artykule znajdują się linki do dokumentacji Azure Functions dla niektórych tematów. Należy pamiętać o następujących różnicach między funkcjami i zestawem SDK zadań WebJob:
> * Azure Functions wersja 2. *x* odpowiada zestawowi SDK zadań WebJob w wersji 3. *x*i Azure Functions 1. *x* odpowiada zestawowi WebJobs SDK 2. *x*. Repozytoria kodu źródłowego używają numeracji zestawu SDK zadań WebJob.
> * Przykładowy kod dla bibliotek C# klas Azure Functions jest podobny do kodu zestawu SDK usługi WebJobs, z wyjątkiem tego, że atrybut `FunctionName` nie jest wymagany w projekcie zestawu SDK zadań WebJob.
> * Niektóre typy powiązań są obsługiwane tylko w funkcjach, takich jak HTTP (webhook) i Event Grid (które są oparte na protokole HTTP).
>
> Aby uzyskać więcej informacji, zobacz [porównanie zestawu SDK WebJobs i Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>Host zadań WebJob

Host jest kontenerem środowiska uruchomieniowego dla funkcji.  Nasłuchuje dla wyzwalaczy i wywołań funkcji. W wersji 3. *x*, host jest implementacją `IHost`. W wersji 2. *x*, używasz obiektu `JobHost`. Tworzysz wystąpienie hosta w kodzie i napisz kod, aby dostosować jego zachowanie.

Jest to kluczowa różnica między używaniem zestawu SDK usługi WebJobs i bezpośrednio za pośrednictwem Azure Functions. W Azure Functions usługa kontroluje hosta i nie można dostosować hosta przez napisanie kodu. Azure Functions pozwala dostosować zachowanie hosta za pomocą ustawień w pliku host. JSON. Te ustawienia są ciągami, nie kodem i ograniczają typy dostosowań, które można wykonać.

### <a name="host-connection-strings"></a>Parametry połączenia hosta

Zestaw SDK zadań WebJob szuka usługi Azure Storage i Azure Service Bus parametry połączenia w pliku Local. Settings. JSON podczas uruchamiania lokalnego lub w środowisku Zadania WebJob podczas uruchamiania na platformie Azure. Domyślnie jest wymagane ustawienie parametrów połączenia magazynu o nazwie `AzureWebJobsStorage`.  

Wersja 2. *x* zestawu SDK umożliwia korzystanie z własnych nazw dla tych parametrów połączenia lub przechowywanie ich w innym miejscu. Możesz ustawić nazwy w kodzie przy użyciu [`JobHostConfiguration`], jak pokazano poniżej:

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

Ponieważ wersja 3. *x* używa domyślnych interfejsów API konfiguracji platformy .NET Core, nie istnieje interfejs API służący do zmiany nazw parametrów połączenia.

### <a name="host-development-settings"></a>Ustawienia programowania hosta

Możesz uruchomić hosta w trybie programistycznym, aby zwiększyć efektywność tworzenia lokalnego. Poniżej przedstawiono niektóre ustawienia, które są zmieniane po uruchomieniu w trybie deweloperskim:

| Właściwość | Ustawienie deweloperskie |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` w celu zmaksymalizowania danych wyjściowych dziennika. |
| `Queues.MaxPollingInterval`  | Niska wartość, aby zapewnić natychmiastowe wyzwolenie metod kolejki.  |
| `Singleton.ListenerLockPeriod` | 15 sekund, aby pomóc w szybkim tworzeniu iteracyjnym. |

Proces włączania trybu deweloperskiego zależy od wersji zestawu SDK. 

#### <a name="version-3x"></a>Wersja 3. *x*

Wersja 3. *x* używa standardowych interfejsów API ASP.NET Core. Wywołaj metodę [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) w wystąpieniu [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . Przekaż ciąg o nazwie `development`, jak w poniższym przykładzie:

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

#### <a name="version-2x"></a>Wersja 2. *x*

Klasa `JobHostConfiguration` ma metodę `UseDevelopmentSettings`, która umożliwia tryb opracowywania.  Poniższy przykład pokazuje, jak używać ustawień deweloperskich. Aby `config.IsDevelopment` zwracać `true` uruchomione lokalnie, należy ustawić zmienną środowiskową Local o nazwie `AzureWebJobsEnv` z wartością `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Zarządzanie jednoczesnymi połączeniami (wersja 2). *x*)

W wersji 3. *x*, połączenie jest ograniczone do nieskończonych połączeń. Jeśli z jakiegoś powodu trzeba zmienić ten limit, można użyć właściwości [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) klasy [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) .

W wersji 2. *x*, można kontrolować liczbę jednoczesnych połączeń z hostem przy użyciu interfejsu API [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . W 2. *x*, należy zwiększyć tę wartość z wartości domyślnej 2 przed uruchomieniem hosta zadań WebJob.

Wszystkie wychodzące żądania HTTP wprowadzane z funkcji za pomocą przepływu `HttpClient` przez `ServicePointManager`. Po osiągnięciu wartości ustawionej w `DefaultConnectionLimit``ServicePointManager` zacznie kolejkować żądania przed ich wysłaniem. Załóżmy, że `DefaultConnectionLimit` jest ustawiona na 2, a kod wykonuje żądania HTTP 1 000. Początkowo do systemu operacyjnego są dozwolone tylko dwa żądania. Pozostałe 998 są umieszczane w kolejce do momentu. Oznacza to, że `HttpClient` może przekroczyć limit czasu, ponieważ wydaje się to żądanie, ale żądanie nigdy nie zostało wysłane przez system operacyjny do serwera docelowego. Może być widoczne zachowanie, które nie ma sensu: lokalny `HttpClient` trwa 10 sekund, aby zakończyć żądanie, ale usługa zwraca każde żądanie w 200 ms. 

Wartość domyślna dla aplikacji ASP.NET jest `Int32.MaxValue`i prawdopodobnie dobrze działa w przypadku zadań WebJob uruchomionych w planie App Service w warstwie Podstawowa lub wyższa. Zadania WebJob zazwyczaj potrzebują ustawienia zawsze włączone i są obsługiwane tylko przez plany App Service w warstwach Podstawowa i wyższa.

Jeśli zadanie WebJob działa w ramach planu App Service bezpłatna lub współdzielona, aplikacja zostanie ograniczona przez piaskownicę App Service, która ma [limit połączeń 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Z niezwiązanym limitem połączeń w `ServicePointManager`jest to bardziej możliwe, że próg połączenia piaskownicy zostanie osiągnięty, a lokacja zostanie zamknięta. W takim przypadku ustawienie `DefaultConnectionLimit` na wartość niższą, na przykład 50 lub 100, może uniemożliwić takie działanie i nadal zapewniać wystarczającą przepływność.

Ustawienie musi być skonfigurowane przed wykonaniem jakichkolwiek żądań HTTP. Z tego powodu Host zadań WebJob nie powinien automatycznie zmieniać ustawienia. Mogą istnieć żądania HTTP, które wystąpiły przed uruchomieniem hosta, co może prowadzić do nieoczekiwanego zachowania. Najlepszym rozwiązaniem jest ustawienie wartości bezpośrednio w metodzie `Main` przed zainicjowaniem `JobHost`, jak pokazano poniżej:

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

Funkcje muszą być metodami publicznymi i muszą mieć jeden atrybut wyzwalacza lub atrybut [`NoAutomaticTrigger`](#manual-triggers) .

### <a name="automatic-triggers"></a>Automatyczne wyzwalacze

Automatyczne wyzwalacze wywołują funkcję w odpowiedzi na zdarzenie. Rozważmy ten przykład funkcji wyzwalanej przez komunikat dodany do usługi Azure queue storage. Reaguje na odczytywanie obiektu BLOB z usługi Azure Blob Storage:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{myQueueItem}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Atrybut `QueueTrigger` informuje środowisko uruchomieniowe o wywołaniu funkcji za każdym razem, gdy w kolejce `myqueue-items` zostanie wyświetlony komunikat kolejki. Atrybut `Blob` informuje środowisko uruchomieniowe, aby używało komunikatu kolejki do odczytu obiektu BLOB w kontenerze *przykładowych elementów roboczych* . Zawartość komunikatu w kolejce, która została przeniesiona do funkcji w `myQueueItem` parametr, jest nazwą obiektu BLOB.

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Wyzwalacze ręczne

Aby ręcznie wyzwolić funkcję, Użyj atrybutu `NoAutomaticTrigger`, jak pokazano poniżej:

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

#### <a name="version-3x"></a>Wersja 3. *x*

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

#### <a name="version-2x"></a>Wersja 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Powiązania wejściowe i wyjściowe

Powiązania wejściowe zapewniają deklaratywny sposób udostępniania danych z platformy Azure lub usług innych firm do kodu. Powiązania wyjściowe umożliwiają aktualizację danych. W artykule [wprowadzenie](webjobs-sdk-get-started.md) przedstawiono przykład każdego z nich.

Można użyć wartości zwracanej metody dla powiązania danych wyjściowych przez zastosowanie atrybutu do wartości zwracanej przez metodę. Zapoznaj się z przykładem [przy użyciu wartości zwracanej przez funkcję platformy Azure](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Typy powiązań

Proces instalowania typów powiązań i zarządzania nimi zależy od tego, czy jest używana wersja 3. *x* lub wersja 2. *x* zestawu SDK. Pakiet do zainstalowania dla określonego typu powiązania można znaleźć w sekcji "Packages" tego typu powiązania Azure Functions [artykuł referencyjny](#binding-reference-information). Wyjątkiem są wyzwalacze i powiązania plików (dla lokalnego systemu plików), które nie są obsługiwane przez Azure Functions.

#### <a name="version-3x"></a>Wersja 3. *x*

W wersji 3. *x*, powiązania magazynu są zawarte w pakiecie `Microsoft.Azure.WebJobs.Extensions.Storage`. Wywołaj metodę rozszerzenia `AddAzureStorage` w metodzie `ConfigureWebJobs`, jak pokazano poniżej:

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

Aby użyć innych typów wyzwalacza i powiązania, zainstaluj pakiet NuGet, który go zawiera, i Wywołaj metodę rozszerzenia `Add<binding>` zaimplementowaną w rozszerzeniu. Na przykład jeśli chcesz użyć powiązania Azure Cosmos DB, zainstaluj `Microsoft.Azure.WebJobs.Extensions.CosmosDB` i Wywołaj `AddCosmosDB`w następujący sposób:

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

Aby użyć wyzwalacza czasomierza lub powiązania plików, które są częścią podstawowych usług, należy odpowiednio wywołać metody rozszerzenia `AddTimers` lub `AddFiles`.

#### <a name="version-2x"></a>Wersja 2. *x*

Te wyzwalacze i typy powiązań są zawarte w wersji 2. *x* pakietu `Microsoft.Azure.WebJobs`:

* Magazyn obiektów Blob
* Queue Storage
* Table Storage

Aby użyć innych typów wyzwalacza i powiązania, zainstaluj pakiet NuGet, który go zawiera, i Wywołaj metodę `Use<binding>` na obiekcie `JobHostConfiguration`. Na przykład jeśli chcesz użyć wyzwalacza czasomierza, zainstaluj `Microsoft.Azure.WebJobs.Extensions` i Wywołaj `UseTimers` w metodzie `Main`, jak pokazano poniżej:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Aby użyć powiązania plików, zainstaluj `Microsoft.Azure.WebJobs.Extensions` i Wywołaj `UseFiles`.

### <a name="executioncontext"></a>Kontekście wykonywania

Zadania WebJob umożliwiają powiązanie z [`ExecutionContext`]. Za pomocą tego powiązania możesz uzyskać dostęp do [`ExecutionContext`] jako parametru w podpisie funkcji. Na przykład poniższy kod używa obiektu kontekstu, aby uzyskać dostęp do identyfikatora wywołania, którego można użyć do skorelowania wszystkich dzienników utworzonych przez daną wywołanie funkcji.  

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

Proces tworzenia powiązania z [`ExecutionContext`] zależy od wersji zestawu SDK.

#### <a name="version-3x"></a>Wersja 3. *x*

Wywołaj metodę rozszerzenia `AddExecutionContextBinding` w metodzie `ConfigureWebJobs`, jak pokazano poniżej:

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

#### <a name="version-2x"></a>Wersja 2. *x*

Wymieniony wcześniej pakiet `Microsoft.Azure.WebJobs.Extensions` zawiera również specjalny typ powiązania, który można zarejestrować, wywołując metodę `UseCore`. To powiązanie pozwala zdefiniować parametr [`ExecutionContext`] w podpisie funkcji, który jest włączony w następujący sposób:

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

Można skonfigurować zachowanie niektórych wyzwalaczy i powiązań. Proces konfigurowania ich zależy od wersji zestawu SDK.

* **Wersja 3. *x*:** Ustaw konfigurację w przypadku wywołania metody `Add<Binding>` w `ConfigureWebJobs`.
* **Wersja 2. *x*:** Ustaw konfigurację przez ustawienie właściwości w obiekcie konfiguracji, który został przekazany do `JobHost`.

Te ustawienia specyficzne dla powiązania są równoważne z ustawieniami w [pliku projektu host. JSON](../azure-functions/functions-host-json.md) w Azure Functions.

Można skonfigurować następujące powiązania:

* [Wyzwalacz usługi Azure CosmosDB](#azure-cosmosdb-trigger-configuration-version-3x)
* [Wyzwalacz Event Hubs](#event-hubs-trigger-configuration-version-3x)
* [Wyzwalacz magazynu kolejki](#queue-storage-trigger-configuration)
* [Powiązanie SendGrid](#sendgrid-binding-configuration-version-3x)
* [Wyzwalacz Service Bus](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfiguracja wyzwalacza usługi Azure CosmosDB (wersja 3). *x*)

Ten przykład pokazuje, jak skonfigurować wyzwalacz Azure Cosmos DB:

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

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [powiązań usługi Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings) .

### <a name="event-hubs-trigger-configuration-version-3x"></a>Konfiguracja wyzwalacza Event Hubs (wersja 3. *x*)

Ten przykład pokazuje, jak skonfigurować wyzwalacz Event Hubs:

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

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [powiązań Event Hubs](../azure-functions/functions-bindings-event-hubs.md#hostjson-settings) .

### <a name="queue-storage-trigger-configuration"></a>Konfiguracja wyzwalacza magazynu kolejki

W poniższych przykładach pokazano, jak skonfigurować wyzwalacz magazynu kolejki:

#### <a name="version-3x"></a>Wersja 3. *x*

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

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [powiązania usługi queue storage](../azure-functions/functions-bindings-storage-queue.md#hostjson-settings) .

#### <a name="version-2x"></a>Wersja 2. *x*

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

Aby uzyskać więcej informacji, zobacz [odwołanie do pliku host. JSON v1. x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>Konfiguracja powiązania SendGrid (wersja 3. *x*)

Ten przykład pokazuje, jak skonfigurować powiązanie danych wyjściowych SendGrid:

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

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [powiązań SendGrid](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) .

### <a name="service-bus-trigger-configuration-version-3x"></a>Konfiguracja wyzwalacza Service Bus (wersja 3. *x*)

Ten przykład pokazuje, jak skonfigurować wyzwalacz Service Bus:

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

Aby uzyskać więcej informacji, zobacz artykuł dotyczący [powiązań Service Bus](../azure-functions/functions-bindings-service-bus.md#hostjson-settings) .

### <a name="configuration-for-other-bindings"></a>Konfiguracja innych powiązań

Niektóre wyzwalacze i typy powiązań definiują własne niestandardowe typy konfiguracji. Na przykład wyzwalacz pliku pozwala określić ścieżkę katalogu głównego do monitorowania, jak w następujących przykładach:

#### <a name="version-3x"></a>Wersja 3. *x*

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

#### <a name="version-2x"></a>Wersja 2. *x*

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

## <a name="binding-expressions"></a>Wyrażenia powiązań

W parametrach konstruktora atrybutów można używać wyrażeń, które są rozpoznawane jako wartości z różnych źródeł. Na przykład, w poniższym kodzie, ścieżka `BlobTrigger` atrybutu tworzy wyrażenie o nazwie `filename`. Gdy jest używany dla powiązania danych wyjściowych, `filename` jest rozpoznawana jako nazwa wyzwalającego obiektu BLOB.

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

Aby uzyskać więcej informacji na temat powiązań wyrażeń, zobacz temat [powiązania wyrażeń i wzorców](../azure-functions/functions-bindings-expressions-patterns.md) w dokumentacji Azure Functions.

### <a name="custom-binding-expressions"></a>Niestandardowe wyrażenia powiązań

Czasami chcesz określić nazwę kolejki, nazwę obiektu BLOB lub kontener albo nazwę tabeli w kodzie, a nie kodowanie twarde. Na przykład możesz chcieć określić nazwę kolejki dla atrybutu `QueueTrigger` w pliku konfiguracyjnym lub zmiennej środowiskowej.

Można to zrobić, przekazując obiekt `NameResolver` w do obiektu `JobHostConfiguration`. Należy uwzględnić symbole zastępcze w wyzwalaczu lub parametrach konstruktora atrybutu powiązania, a kod `NameResolver` udostępnia rzeczywiste wartości, które będą używane zamiast tych symboli zastępczych. Symbole zastępcze można identyfikować, otaczając je z wartością procentową (%) znaki, jak pokazano poniżej:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ten kod umożliwia używanie kolejki o nazwie `logqueuetest` w środowisku testowym i jednego o nazwie `logqueueprod` w produkcji. Zamiast kodowanej nazwy kolejki należy określić nazwę wpisu w kolekcji `appSettings`.

Istnieje domyślna `NameResolver`, która obowiązuje, jeśli nie podano niestandardowego. Wartość domyślna pobiera wartości z ustawień aplikacji lub zmiennych środowiskowych.

Klasa `NameResolver` Pobiera nazwę kolejki z `appSettings`, jak pokazano poniżej:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Wersja 3. *x*

Program rozpoznawania nazw można skonfigurować przy użyciu iniekcji zależności. Te przykłady wymagają następującej instrukcji `using`:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Aby dodać program rozpoznawania nazw, należy wywołać metodę [`ConfigureServices`] extension na [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder), jak w poniższym przykładzie:

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

#### <a name="version-2x"></a>Wersja 2. *x*

Przekaż klasę `NameResolver` do obiektu `JobHost`, jak pokazano poniżej:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementuje `INameResolver`, aby uzyskać wartości z ustawień aplikacji, jak pokazano w przykładzie. Korzystając z zestawu SDK usługi WebJobs bezpośrednio, można napisać implementację niestandardową, która pobiera zastępcze wartości zamienne z dowolnego źródła, którego wolisz.

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

Jeśli przed użyciem atrybutu powiązania, takiego jak `Queue`, `Blob`lub `Table`, należy wykonać pewne czynności w funkcji, możesz użyć interfejsu `IBinder`.

Poniższy przykład przyjmuje komunikat w kolejce wejściowej i tworzy nowy komunikat z tą samą zawartością w kolejce wyjściowej. Nazwa kolejki wyjściowej jest ustawiana przez kod w treści funkcji.

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

Aby uzyskać więcej informacji, zobacz temat [Powiązywanie w środowisku uruchomieniowym](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) w dokumentacji Azure Functions.

## <a name="binding-reference-information"></a>Informacje o odwołaniach powiązania

Dokumentacja Azure Functions zawiera informacje referencyjne dotyczące każdego typu powiązania. Poniższe informacje znajdują się w każdym artykule referencyjnym powiązania. (Ten przykład jest oparty na kolejce magazynu).

* [Pakiety](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x). Pakiet, który należy zainstalować w celu uwzględnienia obsługi powiązania w projekcie zestawu SDK zadań WebJob.
* [Przykłady](../azure-functions/functions-bindings-storage-queue.md#trigger---example). Przykłady kodu. Przykład C# biblioteki klas dotyczy zestawu SDK zadań WebJob. Po prostu Pomiń atrybut `FunctionName`.
* [Atrybuty](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes). Atrybuty, które mają być używane dla typu powiązania.
* [Konfiguracja](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration). Wyjaśnienia właściwości atrybutów i parametrów konstruktorów.
* [Użycie](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Typy, do których można powiązać i informacje o sposobie działania powiązania. Na przykład: algorytm sondowania, przetwarzanie w kolejce trującej.
  
Aby uzyskać listę artykułów referencyjnych dotyczących powiązań, zobacz "obsługiwane powiązania" w artykule [wyzwalacze i powiązania](../azure-functions/functions-triggers-bindings.md#supported-bindings) Azure Functions. Na tej liście powiązania HTTP, webhook i Event Grid są obsługiwane tylko przez Azure Functions, a nie przez zestaw SDK WebJobs.

## <a name="disable-attribute"></a>Wyłącz atrybut 

Atrybut [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) pozwala kontrolować, czy funkcja może być wyzwalana. 

W poniższym przykładzie, jeśli ustawienie aplikacji `Disable_TestJob` ma wartość `1` lub `True` (bez uwzględniania wielkości liter), funkcja nie zostanie uruchomiona. W takim przypadku środowisko uruchomieniowe tworzy funkcję komunikatu dziennika *"Functions. TestJob" jest wyłączona*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Po zmianie wartości ustawień aplikacji w Azure Portal, zadanie WebJob zostanie ponownie uruchomione w celu pobrania nowego ustawienia.

Ten atrybut może być zadeklarowany na poziomie parametru, metody lub klasy. Nazwa ustawienia może również zawierać wyrażenia powiązań.

## <a name="timeout-attribute"></a>Atrybut timeout

Atrybut [`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) powoduje anulowanie funkcji, jeśli nie zakończy się w określonym czasie. W poniższym przykładzie funkcja będzie uruchamiana przez jeden dzień bez atrybutu timeout. Limit czasu powoduje, że funkcja jest anulowana po 15 sekundach.

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

Można zastosować atrybut timeout na poziomie klasy lub metody, a także określić globalny limit czasu przy użyciu `JobHostConfiguration.FunctionTimeout`. Limity czasu na poziomie klasy lub metodzie przesłaniają globalne limity czasu.

## <a name="singleton-attribute"></a>Atrybut singleton

Atrybut [`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) zapewnia, że tylko jedno wystąpienie funkcji działa nawet wtedy, gdy istnieje wiele wystąpień aplikacji sieci Web hosta. Robi to za pomocą [blokady rozproszonej](#viewing-lease-blobs).

W tym przykładzie tylko jedno wystąpienie funkcji `ProcessImage` jest uruchamiane w dowolnym momencie:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>Singletonmode. Listener

Niektóre wyzwalacze mają wbudowaną obsługę zarządzania współbieżnością:

* **QueueTrigger**. Ustaw `JobHostConfiguration.Queues.BatchSize` na `1`.
* **ServiceBusTrigger**. Ustaw `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` na `1`.
* **FileTrigger**. Ustaw `FileProcessor.MaxDegreeOfParallelism` na `1`.

Możesz użyć tych ustawień, aby upewnić się, że funkcja działa jako pojedyncza w jednym wystąpieniu. Aby upewnić się, że tylko jedno wystąpienie funkcji jest uruchomione, gdy aplikacja sieci Web jest skalowana do wielu wystąpień, Zastosuj jednokrotną blokadę na poziomie odbiornika dla funkcji (`[Singleton(Mode = SingletonMode.Listener)]`). Blokady odbiorników są uzyskiwane po rozpoczęciu JobHost. Jeśli trzy wystąpienia skalowania w poziomie są uruchamiane w tym samym czasie, tylko jedno z wystąpień uzyskuje blokadę i rozpocznie się tylko jeden odbiornik.

### <a name="scope-values"></a>Wartości zakresu

Możesz określić *wyrażenie zakresu/wartość* dla pojedynczego elementu. Wyrażenie/wartość zapewnia, że wszystkie wykonania funkcji w określonym zakresie zostaną serializowane. Zaimplementowanie bardziej szczegółowego blokowania w ten sposób pozwala na pewien poziom równoległości funkcji podczas serializacji innych wywołań zgodnie z wymaganiami. Na przykład w poniższym kodzie wyrażenie Scope wiąże się z wartością `Region` komunikatu przychodzącego. Gdy kolejka zawiera trzy komunikaty w regionach Wschodnie, wschodnie i zachodnie, wiadomości, które mają region wschód, są uruchamiane sekwencyjnie, podczas gdy komunikat z regionem zachodnim jest uruchamiany równolegle z tymi na wschód.

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

### <a name="singletonscopehost"></a>SingletonScope. host

Domyślnym zakresem blokady jest `SingletonScope.Function`, co oznacza, że zakres blokady (ścieżka dzierżawy obiektu BLOB) jest powiązany z w pełni kwalifikowaną nazwą funkcji. Aby zablokować funkcje, określ `SingletonScope.Host` i użyj nazwy identyfikatora zakresu, która jest taka sama we wszystkich funkcjach, których nie chcesz uruchamiać jednocześnie. W poniższym przykładzie tylko jedno wystąpienie `AddItem` lub `RemoveItem` jest uruchamiane jednocześnie:

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

### <a name="viewing-lease-blobs"></a>Wyświetlanie obiektów BLOB dzierżawy

Zestaw SDK zadań WebJob używa [dzierżawy obiektów blob platformy Azure](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) w obszarze okładki do wdrożenia blokowania rozproszonego. Obiekty blob dzierżawy używane przez funkcję singleton można znaleźć w kontenerze `azure-webjobs-host` na koncie magazynu `AzureWebJobsStorage` pod ścieżką "blokady". Na przykład ścieżka obiektu BLOB dzierżawy dla pierwszego przykładu `ProcessImage` pokazana wcześniej może być `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Wszystkie ścieżki zawierają identyfikator JobHost, w tym przypadku 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Funkcje asynchroniczne

Informacje o sposobach kodowania funkcji asynchronicznych znajdują się w [dokumentacji Azure Functions](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Tokeny anulowania

Aby uzyskać informacje o sposobie obsługi tokenów anulowania, zobacz dokumentację Azure Functions dotyczącą [tokenów anulowania i bezpiecznego zamykania](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Wiele wystąpień

Jeśli aplikacja sieci Web działa w wielu wystąpieniach, ciągłe zadanie WebJob działa w każdym wystąpieniu, nasłuchiwanie wyzwalaczy i wywoływanie funkcji. Różne powiązania wyzwalacza zaprojektowano w celu wydajnego udostępniania współpracy między wystąpieniami, dzięki czemu skalowanie do większej liczby wystąpień pozwala na obsługę większej liczby obciążeń.

Chociaż niektóre wyzwalacze mogą spowodować podwójne przetwarzanie, wyzwalacze usługi Queue i BLOB Storage automatycznie uniemożliwiają funkcji przetwarzanie komunikatu kolejki lub obiektu BLOB więcej niż raz. Aby uzyskać więcej informacji, zobacz [projektowanie dla identycznych danych wejściowych](../azure-functions/functions-idempotent.md) w dokumentacji Azure Functions.

Wyzwalacz Timer automatycznie zapewnia, że uruchamiane jest tylko jedno wystąpienie czasomierza, przez co nie można uzyskać więcej niż jednego wystąpienia funkcji uruchomionego w określonym zaplanowanym czasie.

Jeśli chcesz mieć pewność, że tylko jedno wystąpienie funkcji działa nawet wtedy, gdy istnieje wiele wystąpień aplikacji sieci Web hosta, można użyć atrybutu [`Singleton`](#singleton-attribute) .

## <a name="filters"></a>Filtry

Filtry funkcji (wersja zapoznawcza) umożliwiają dostosowanie potoku wykonywania zadań WebJob przy użyciu własnej logiki. Filtry są podobne do [filtrów ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Można zaimplementować je jako atrybuty deklaracyjne, które są stosowane do funkcji lub klas. Aby uzyskać więcej informacji, zobacz [filtry funkcji](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Zalecamy platformę rejestrowania opracowaną dla ASP.NET. W artykule [wprowadzenie](webjobs-sdk-get-started.md) pokazano, jak z niego korzystać. 

### <a name="log-filtering"></a>Filtrowanie dzienników

Każdy dziennik utworzony przez wystąpienie `ILogger` ma skojarzoną `Category` i `Level`. [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) jest wyliczeniem, a kod liczby całkowitej wskazuje na ważność względną:

|logLevel    |Kod|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczna    | 5 |
|Brak        | 6 |

Można niezależnie filtrować każdą kategorię do określonego [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel). Na przykład możesz chcieć zobaczyć wszystkie dzienniki przetwarzania wyzwalacza obiektów blob, ale tylko `Error` i nowsze dla wszystkiego innego.

#### <a name="version-3x"></a>Wersja 3. *x*

Wersja 3. *x* zestawu SDK opiera się na filtrowaniu opartym na platformie .NET Core. Klasa `LogCategories` umożliwia definiowanie kategorii dla określonych funkcji, wyzwalaczy lub użytkowników. Definiuje również filtry dla określonych stanów hosta, takich jak `Startup` i `Results`. Dzięki temu można dostosować dane wyjściowe rejestrowania. Jeśli nie odnaleziono dopasowania w określonych kategoriach, filtr powraca do wartości `Default` podczas decydowania, czy należy filtrować komunikat.

`LogCategories` wymaga następującej instrukcji using:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Poniższy przykład konstruuje filtr, który domyślnie filtruje wszystkie dzienniki na poziomie `Warning`. Kategorie `Function` i `results` (równoważne `Host.Results` w wersji 2. *x*) są filtrowane na poziomie `Error`. Filtr porównuje bieżącą kategorię ze wszystkimi zarejestrowanymi poziomami w wystąpieniu `LogCategories` i wybiera najdłuższe dopasowanie. Oznacza to, że poziom `Debug` zarejestrowany dla `Host.Triggers` pasuje do `Host.Triggers.Queue` lub `Host.Triggers.Blob`. Dzięki temu można kontrolować szersze kategorie bez konieczności dodawania każdej z nich.

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

#### <a name="version-2x"></a>Wersja 2. *x*

W wersji 2. *x* zestawu SDK, użyj klasy `LogCategoryFilter` do kontrolowania filtrowania. `LogCategoryFilter` ma właściwość `Default` z początkową wartością `Information`, co oznacza, że wszystkie komunikaty na poziomach `Information`, `Warning`, `Error`lub `Critical` są rejestrowane, ale wszystkie komunikaty na poziomie `Debug` lub `Trace` są odfiltrowane.

Tak jak w przypadku `LogCategories` w wersji 3. *x*, właściwość `CategoryLevels` umożliwia określenie poziomów dzienników dla określonych kategorii, dzięki czemu można dostosować rejestrowanie danych wyjściowych. Jeśli w słowniku `CategoryLevels` nie zostanie znaleziony żaden odpowiednik, filtr powróci do wartości `Default` podczas decydowania o tym, czy ma być filtrowany komunikat.

Poniższy przykład tworzy filtr, który domyślnie filtruje wszystkie dzienniki na poziomie `Warning`. Kategorie `Function` i `Host.Results` są filtrowane na poziomie `Error`. `LogCategoryFilter` porównuje bieżącą kategorię ze wszystkimi zarejestrowanymi `CategoryLevels` i wybiera najdłuższe dopasowanie. Tak więc poziom `Debug` zarejestrowany dla `Host.Triggers` będzie pasował do `Host.Triggers.Queue` lub `Host.Triggers.Blob`. Dzięki temu można kontrolować szersze kategorie bez konieczności dodawania każdej z nich.

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

### <a name="custom-telemetry-for-application-insights"></a>Niestandardowa Telemetria dla Application Insights

Proces implementowania niestandardowych danych telemetrycznych dla [Application Insights](../azure-monitor/app/app-insights-overview.md) zależy od wersji zestawu SDK. Aby dowiedzieć się, jak skonfigurować Application Insights, zobacz [dodawanie Application Insights rejestrowania](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Wersja 3. *x*

Ponieważ wersja 3. *x* zestawu SDK zadań WebJob jest oparty na hoście ogólnym programu .NET Core, ale nie jest już dostarczany niestandardowa fabryka telemetrii. Można jednak dodać do potoku niestandardową telemetrię przy użyciu iniekcji zależności. Przykłady w tej sekcji wymagają następujących instrukcji `using`:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Następująca niestandardowa implementacja [`ITelemetryInitializer`] umożliwia dodanie własnych [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) do [`TelemetryConfiguration`]domyślnej.

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Wywołaj [`ConfigureServices`] w konstruktorze, aby dodać niestandardowy [`ITelemetryInitializer`] do potoku.

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

Podczas konstruowania [`TelemetryConfiguration`] są uwzględniane wszystkie zarejestrowane typy [`ITelemetryInitializer`] . Aby dowiedzieć się więcej, zobacz [Application INSIGHTS API dla niestandardowych zdarzeń i metryk](../azure-monitor/app/api-custom-events-metrics.md).

W wersji 3. *x*, nie trzeba już opróżniać [`TelemetryClient`] po zatrzymaniu hosta. System wstrzykiwania zależności .NET Core automatycznie usuwa zarejestrowany `ApplicationInsightsLoggerProvider`, który opróżnia [`TelemetryClient`].

#### <a name="version-2x"></a>Wersja 2. *x*

W wersji 2. *x* [`TelemetryClient`] utworzone wewnętrznie przez dostawcę Application Insights dla zestawu SDK usługi WebJobs używają [`ServerTelemetryChannel`](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Gdy punkt końcowy Application Insights jest niedostępny lub ogranicza żądania przychodzące, ten kanał [zapisuje żądania w systemie plików aplikacji sieci Web i ponownie przesyła je później](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

[`TelemetryClient`] jest tworzony przez klasę implementującą `ITelemetryClientFactory`. Domyślnie jest to [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Jeśli chcesz zmodyfikować dowolną część potoku Application Insights, możesz podać swój własny `ITelemetryClientFactory`, a host będzie używać klasy do konstruowania [`TelemetryClient`]. Na przykład ten kod zastępuje `DefaultTelemetryClientFactory`, aby zmodyfikować właściwość `ServerTelemetryChannel`:

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

Obiekt `SamplingPercentageEstimatorSettings` konfiguruje [próbkowanie adaptacyjne](https://docs.microsoft.com/azure/application-insights/app-insights-sampling). Oznacza to, że w niektórych scenariuszach o dużej ilości usługi Application Insights wysyłają wybrany podzestaw danych telemetrycznych do serwera.

Po utworzeniu fabryki danych telemetrycznych należy przekazać ją do dostawcy rejestrowania Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Następne kroki

W tym artykule przedstawiono fragmenty kodu, które pokazują, jak obsługiwać typowe scenariusze pracy z zestawem SDK usługi WebJobs. Aby zapoznać się z kompletnymi przykładami, zobacz artykuł [Azure-WebJobs-SDK-Samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[Kontekście wykonywania]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetryConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs
