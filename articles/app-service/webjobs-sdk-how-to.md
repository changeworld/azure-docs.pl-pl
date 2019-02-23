---
title: Jak używać zestawu SDK usługi WebJobs — platformy Azure
description: Dowiedz się więcej na temat pisania kodu dla zestawu SDK usługi WebJobs. Tworzyć oparte na zdarzeniach tła zadania przetwarzania, uzyskujących dostęp do danych w usługach platformy Azure i usługi innych firm.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/19/2019
ms.author: glenga
ms.openlocfilehash: a2e07f9022d7404d037903fda627649918134cb7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2019
ms.locfileid: "56732742"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Jak używać zestawu Azure WebJobs SDK na potrzeby przetwarzania w tle oparte na zdarzeniach

Ten artykuł zawiera wskazówki na temat sposobu pisania kodu [zestawu Azure WebJobs SDK](webjobs-sdk-get-started.md). Dokumentacja dotyczy zarówno wersji 3.x i 2.x zestawu WebJobs SDK. W przypadku, gdy istnieją różnice interfejsu API, znajdują się oba przykłady. Główne zmiany wprowadzone w wersji 3.x polega na użyciu platformy .NET Core, a nie .NET Framework.

>[!NOTE]
> [Usługa Azure Functions](../azure-functions/functions-overview.md) jest oparta na zestaw SDK zadań Webjob, a ten artykuł zawiera łącza do dokumentacji usługi Azure Functions, aby niektóre tematy. Zwróć uwagę na następujące różnice pomiędzy funkcjami i zestaw SDK zadań Webjob:
> * Wersja usługi Azure Functions 2.x odnosi się do zestawu WebJobs SDK w wersji 3.x, a usługa Azure Functions 1.x odnosi się do zestawu WebJobs SDK 2.x. Repozytoriów kodu źródłowego postępuj zgodnie z zestawem SDK usługi WebJobs numerowania.
> * Przykładowy kod dla usługi Azure Functions C#, bibliotek klas działa jak zestaw SDK usługi WebJobs kodu z tym nie ma potrzeby `FunctionName` atrybutu w projekcie zestawu SDK usługi WebJobs.
> * Niektóre typy wiązań są obsługiwane tylko w przypadku funkcji, takich jak HTTP, element webhook i usługi Event Grid, (która zależy od protokołu HTTP).
> 
> Aby uzyskać więcej informacji, zobacz [porównanie zestawu SDK usługi WebJobs i usługi Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, przeczytał i wykonane zadania w [Rozpoczynanie pracy z zestawem SDK usługi WebJobs](webjobs-sdk-get-started.md).

## <a name="webjobs-host"></a>Host usługi WebJobs

Host jest kontener środowiska uruchomieniowego dla funkcji.  Nasłuchuje wyzwalaczy i wywołania funkcji. W wersji 3.x, host jest implementacją `IHost`i w wersji 2.x, możesz użyć `JobHost` obiektu. Utwórz wystąpienie hosta w kodzie i napisać kod, aby dostosować jego zachowanie.

Jest to Najważniejszą różnicą między bezpośrednio przy użyciu zestawu SDK usługi WebJobs i korzystania z niego pośrednio za pomocą usługi Azure Functions. W usłudze Azure Functions usługa kontroluje hosta, a nie można go dostosować przez napisanie kodu. Usługa Azure Functions umożliwia dostosowywanie zachowania dotyczącego hosta, za pomocą ustawień *host.json* pliku. Te ustawienia są ciągami, nie kodu, który ogranicza rodzajów dostosowań, które można wykonać.

### <a name="host-connection-strings"></a>Parametry połączenia hosta 

Zestaw SDK zadań Webjob wyszukuje parametry połączenia usługi Azure Storage i Azure Service Bus w *local.settings.json* plików po uruchomieniu lokalnie lub w środowisku zadania WebJob po uruchomieniu na platformie Azure. Domyślnie, ustawienie o nazwie parametrów połączenia magazynu `AzureWebJobsStorage` jest wymagana.  

W wersji 2.x zestaw SDK pozwala użyć własnych nazw dla tych parametrów połączenia lub przechowywać je w innym miejscu. Możesz je ustawić w kodzie, jak pokazano poniżej:

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

Ponieważ używa ona domyślnej konfiguracji platformy .NET Core interfejsów API, nie ma żadnych interfejsów API w wersji 3.x, aby zmienić nazwy ciągu połączenia.

### <a name="host-development-settings"></a>Ustawienia środowiska deweloperskiego hosta

W trybie projektowania, aby efektywności rozwoju lokalnych, można uruchomić hosta. Poniżej przedstawiono niektóre ustawienia, które zostały zmienione podczas pracy w trybie projektowania:

| Właściwość | Ustawienie rozwoju |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Aby zmaksymalizować dane wyjściowe dziennika. |
| `Queues.MaxPollingInterval`  | Niską wartość, aby upewnić się, że metody kolejki są natychmiast wyzwalane.  |
| `Singleton.ListenerLockPeriod` | 15 sekund, aby ułatwić szybkie, iteracyjne projektowanie. |

Sposób, aby włączyć tryb projektowania zależy od wersji zestawu SDK. 

#### <a name="version-3x"></a>W wersji 3.x

W wersji 3.x korzysta ze standardowego podstawowe interfejsy API platformy ASP.NET. Wywołaj [UseEnvironment](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metody [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) wystąpienia. Przekazywanie parametrów o nazwie `development`, jak w poniższym przykładzie:

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

#### <a name="version-2x"></a>W wersji 2.x

`JobHostConfiguration` Klasa ma `UseDevelopmentSettings` metodę, która włącza tryb projektowania.  Poniższy przykład pokazuje, jak można użyć ustawienia środowiska deweloperskiego. Zapewnienie `config.IsDevelopment` zwracają `true` uruchamianego lokalnie, należy ustawić zmienną środowiskową lokalnego o nazwie `AzureWebJobsEnv` wartością `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>Zarządzanie równoczesnych połączeń (v2.x)

W wersji 3.x, limitu połączeń wartością domyślną jest nieskończona połączeń. Jeśli z jakiegoś powodu, musisz zmienić ten limit, można użyć [MaxConnectionsPerServer](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) właściwość [WinHttpHander](/dotnet/api/system.net.http.winhttphandler) klasy.

W wersji 2.x, kontrolować liczbę jednoczesnych połączeń z hostem za pomocą [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) interfejsu API. W 2.x należy zwiększyć tę wartość z domyślnej wartości 2, przed uruchomieniem hosta usługi WebJobs.

Wszystkie wychodzące żądania HTTP, wprowadzone przez funkcję za pomocą `HttpClient` przepływać przez `ServicePointManager`. Po naciśnięciu `DefaultConnectionLimit`, `ServicePointManager` uruchamia Kolejkowanie żądania przed ich wysłaniem. Załóżmy, że Twoje `DefaultConnectionLimit` jest równa 2, a kod sprawia, że 1000 HTTP żądań. Początkowo tylko dwa żądania mogą za pomocą systemu operacyjnego. Inne 998 są umieszczane w kolejce do momentu miejsca dla nich. Oznacza to, że Twoje `HttpClient` może zostać przekroczony limit czasu, ponieważ jego *uzna za* podjęła żądania, ale żądanie nigdy nie zostało wysłane przez system operacyjny na serwerze docelowym. Dlatego może zostać wyświetlony zachowanie, które prawdopodobnie nie ma sensu: lokalnej `HttpClient` trwa 10 sekund na ukończenie żądania przez, ale zwraca każdego żądania usługi w 200 ms. 

Wartość domyślna dla aplikacji platformy ASP.NET to `Int32.MaxValue`, i który może działać dobrze w przypadku zadań Webjob, uruchomiony w ramach planu usługi App Service w warstwie podstawowa lub wyższej. Zadania Webjob zazwyczaj konieczne ustawienie zawsze włączone i który jest obsługiwany tylko przez podstawowa i wyższych planów usługi App Service. 

Jeśli zadania WebJob działa w warstwie bezpłatna lub udostępnionych planów usługi App Service, aplikacja jest ograniczony przez piaskownicy usługi App Service, który aktualnie ma [limit połączeń 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Z limitem niepowiązanych połączenia w `ServicePointManager`, jest bardziej prawdopodobne, że będzie można połączyć się z piaskownicy próg połączeń i zamknięcia lokacji. W takim przypadku ustawienie `DefaultConnectionLimit` coś o nim małe, takich jak 50 lub 100, można temu zapobiec i nadal umożliwiają wystarczającą przepustowość.

Ustawienie musi być skonfigurowany, zanim wszystkie żądania HTTP zostały wprowadzone. Z tego powodu hosta zadania Webjob nie powinna próbować dostosować ustawienie automatycznie. może to być żądań HTTP, występujących przed uruchamiania hosta i może to prowadzić do nieoczekiwanego zachowania. Najlepszym rozwiązaniem jest wartość bezpośrednio w Twojej `Main` metody przed inicjowanie `JobHost`, jak pokazano w poniższym przykładzie

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it is used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Wyzwalacze

Funkcje muszą być metody publiczne i musi mieć jeden atrybut wyzwalacza lub [NoAutomaticTrigger](#manual-trigger) atrybutu.

### <a name="automatic-trigger"></a>Automatyczny wyzwalacz

Automatycznych wyzwalaczy wywołać funkcję w odpowiedzi na zdarzenie. Aby uzyskać przykład, zobacz wyzwalacz kolejki w [artykule Get](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Ręczne wyzwalacza

Aby ręcznie wyzwolić funkcję, należy użyć `NoAutomaticTrigger` atrybutu, jak pokazano w poniższym przykładzie:

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

Sposób ręcznego wyzwalania funkcji zależy od wersji zestawu SDK.

#### <a name="version-3x"></a>W wersji 3.x

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

#### <a name="version-2x"></a>W wersji 2.x

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Dane wejściowe i wyjściowe powiązania

Powiązania danych wejściowych zapewniają deklaratywną metodę udostępniania danych na platformie Azure lub usług innych firm w kodzie. Powiązania danych wyjściowych zapewniają sposób, aby zaktualizować dane. [Artykule Get](webjobs-sdk-get-started.md) przedstawiono przykład każdego z nich.

Wartość zwracaną metody można użyć dla powiązania danych wyjściowych, stosując atrybut na wartość zwracaną metody. Zobacz przykład w usłudze Azure Functions [wyzwalaczy i powiązań](../azure-functions/functions-bindings-return-value.md) artykułu.

## <a name="binding-types"></a>Typ powiązania

Sposób, że typ powiązania są zainstalowane i zarządzane różni się od wersji 3.x i 2.x zestawu SDK. Można znaleźć pakietu do zainstalowania dla typu określonego powiązania w **pakietów** sekcji tego typu powiązania [artykule informacyjnym na temat](#binding-reference-information) dla usługi Azure Functions. Wyjątek stanowi pliki wyzwalacz i powiązania (w przypadku lokalnego systemu plików), który nie jest obsługiwany przez usługi Azure Functions.

#### <a name="version-3x"></a>W wersji 3.x

W wersji 3.x, powiązania magazynu są uwzględnione w `Microsoft.Azure.WebJobs.Extensions.Storage` pakietu. Wywołaj `AddAzureStorage` metody rozszerzenia w `ConfigureWebJobs` metody, jak pokazano w poniższym przykładzie:

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

Aby użyć innych wyzwalacza i typ powiązania, zainstaluj pakiet NuGet, który zawiera ich, a następnie wywołać `Add<binding>` — metoda rozszerzenia zaimplementowane w rozszerzeniu. Na przykład, jeśli chcesz użyć powiązania usługi Azure Cosmos DB, zainstaluj `Microsoft.Azure.WebJobs.Extensions.CosmosDB` i wywołać `AddCosmosDB`, jak w poniższym przykładzie:

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

#### <a name="version-2x"></a>W wersji 2.x

Następujące typy wyzwalaczy i powiązań są uwzględnione w wersji 2.x `Microsoft.Azure.WebJobs` pakietu:

* Blob Storage
* Queue Storage
* Magazyn tabel

Aby użyć innych wyzwalacza i typ powiązania, zainstaluj pakiet NuGet, który zawiera ich, a następnie wywołać `Use<binding>` metody `JobHostConfiguration` obiektu. Na przykład, jeśli chcesz użyć wyzwalacza czasomierza, zainstaluj `Microsoft.Azure.WebJobs.Extensions` i wywołać `UseTimers` w `Main` metody, jak w poniższym przykładzie:

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

Sposób, który możesz powiązać [ `ExecutionContext` ] zależy od używanej wersji zestawu SDK.

#### <a name="version-3x"></a>W wersji 3.x

Wywołaj `AddExecutionContextBinding` metody rozszerzenia w `ConfigureWebJobs` metody, jak pokazano w poniższym przykładzie:

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

#### <a name="version-2x"></a>W wersji 2.x

`Microsoft.Azure.WebJobs.Extensions` Pakietu, o których wspomniano wcześniej udostępnia również typ powiązania specjalne, który można zarejestrować, wywołując `UseCore` metody. To powiązanie pozwala zdefiniować [ `ExecutionContext` ] parametru w sygnatura funkcji, która jest włączona w następujący sposób:

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

Niektóre wyzwalacz i powiązania pozwalają konfigurować ich zachowania. Sposób ich konfiguracji, zależy od wersji zestawu SDK.

* **W wersji 3.x:** Konfiguracja jest ustawiona, gdy `Add<Binding>` metoda jest wywoływana w `ConfigureWebJobs`.
* **W wersji 2.x:** Przez ustawienie właściwości w obiekcie konfiguracji są przekazywane w celu `JobHost`.

### <a name="queue-trigger-configuration"></a>Konfiguracja wyzwalacza kolejki

Ustawienia możesz skonfigurować wyzwalacz kolejki magazynu zostały wyjaśnione w usłudze Azure Functions [dokumentacja pliku host.JSON](../azure-functions/functions-host-json.md#queues). W poniższych przykładach pokazano, jak je ustawić w konfiguracji:

#### <a name="version-3x"></a>W wersji 3.x

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

#### <a name="version-2x"></a>W wersji 2.x

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

### <a name="configuration-for-other-bindings"></a>Konfiguracja w przypadku innych powiązań

Niektóre typy wyzwalaczy i powiązań Definiowanie własnych typu konfiguracji niestandardowej. Na przykład wyzwalacz plik pozwala określić ścieżkę katalogu głównego, aby monitorować, jak w następujących przykładach:

#### <a name="version-3x"></a>W wersji 3.x

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

#### <a name="version-2x"></a>W wersji 2.x

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

Czasami trzeba określić nazwę kolejki, nazwa obiektu blob lub kontenera lub tabeli w kodzie zamiast trwale kodować nadaj mu nazwę. Na przykład możesz chcieć określić nazwę kolejki `QueueTrigger` atrybutu w zmiennej środowisku lub pliku konfiguracji.

Możesz to zrobić, przekazując `NameResolver` obiekt `JobHostConfiguration` obiektu. Zawierają symbole zastępcze w wyzwalacza i parametry konstruktora atrybutów powiązania, a `NameResolver` kod zawiera rzeczywiste wartości, które będą używane zamiast te symbole zastępcze. Symbole zastępcze są identyfikowane za pomocą otaczającego je za pomocą procentu (%) znaki, jak pokazano w poniższym przykładzie:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ten kod pozwala użyć kolejkę o nazwie `logqueuetest` w środowisku testowym i jedną o nazwie `logqueueprod` w środowisku produkcyjnym. Zamiast nazwy kolejki zakodowane, należy określić nazwę wpisu w `appSettings` kolekcji.

Brak domyślnego NameResolver, który zaczyna obowiązywać, jeśli nie podasz niestandardowego. Wartość domyślna pobiera wartości z ustawień aplikacji lub zmienne środowiskowe.

Twoje `NameResolver` klasa otrzymuje nazwę kolejki z `appSettings` jak pokazano w poniższym przykładzie:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>W wersji 3.x

Program rozpoznawania nazw jest skonfigurowany z użyciem iniekcji zależności. Te przykłady wymagają następującej `using` instrukcji:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Program rozpoznawania nazw jest dodawany przez wywołanie [ `ConfigureServices` ] metody rozszerzenia [HostBuilder](/dotnet/api/microsoft.extensions.hosting.hostbuilder), jak w poniższym przykładzie:

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

#### <a name="version-2x"></a>W wersji 2.x

Przekaż swoje `NameResolver` klasy w celu `JobHost` obiektu, jak pokazano w poniższym przykładzie:

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

Jeśli potrzeba wykonania dodatkowych czynności w funkcji przed rozpoczęciem korzystania z takich jak atrybut wiązania `Queue`, `Blob`, lub `Table`, możesz użyć `IBinder` interfejsu.

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

Informacje na temat poszczególnych typów powiązań znajduje się w dokumentacji usługi Azure Functions. Przy użyciu kolejki magazynu, na przykład, znajdziesz następujące informacje w artykule informacyjnym na temat każdego powiązania:

* [Pakiety](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) — co pakiet do zainstalowania w celu obejmują pomoc techniczną dla powiązania w projekcie zestawu SDK usługi WebJobs.
* [Przykłady](../azure-functions/functions-bindings-storage-queue.md#trigger---example) — przykład biblioteki klasy C# ma zastosowanie do zestawu SDK usługi WebJobs; po prostu pominąć `FunctionName` atrybutu.
* [Atrybuty](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) -atrybuty do użycia w przypadku typu powiązania.
* [Konfiguracja](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) — objaśnienia właściwości atrybutów i parametry konstruktora.
* [Użycie](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) — co typów można powiązać i uzyskać informacje na temat wiązania działa. Na przykład: sondowanie algorytmu, zanieczyszczonych kolejki przetwarzania.
  
Aby uzyskać listę powiązania gama artykułów, zobacz **obsługiwane powiązania** w [wyzwalaczy i powiązań](../azure-functions/functions-triggers-bindings.md#supported-bindings) artykułu dla usługi Azure Functions. Na tej liście powiązania protokołu HTTP, element webhook i usługi Event Grid są obsługiwane tylko przez usługi Azure Functions, a nie przez zestaw SDK zadań Webjob.

## <a name="disable-attribute"></a>Wyłącz atrybutu 

[Wyłączyć](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atrybut umożliwia kontrolowanie, czy funkcja może być uruchamiane. 

W poniższym przykładzie Jeśli ustawienie aplikacji `Disable_TestJob` ma wartość "1" lub "True" (wielkich liter), funkcja nie będzie działać. W takim przypadku środowisko uruchomieniowe tworzy komunikat dziennika *jest wyłączona funkcja "Functions.TestJob"*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Po zmianie wartości ustawień aplikacji w witrynie Azure portal, powoduje zadania WebJob, należy ponownie uruchomić pobieranie nowe ustawienie.

Ten atrybut może być zadeklarowana w poziomie parametrów, metody lub klasy. Nazwa ustawienia może również zawierać wyrażenia wiązania.

## <a name="timeout-attribute"></a>Atrybut limitu czasu

[Limitu czasu](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atrybutu powoduje, że funkcja zostaną anulowane, jeśli nie wykona w określonym czasie. W poniższym przykładzie funkcja może działać bez limitu czasu przez jeden dzień. Z przekroczeniem limitu czasu funkcja zostanie anulowane po 15 sekundach.

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

Można zastosować atrybut limitu czasu na poziomie klasy lub metody i globalnego limitu czasu można określić za pomocą `JobHostConfiguration.FunctionTimeout`. Klasy lub metody poziomu przekroczeń limitu czasu musi zostać zastąpiona globalnego limitu czasu.

## <a name="singleton-attribute"></a>Atrybut pojedynczego wystąpienia

Użyj [pojedyncze](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atrybutu, aby upewnić się, że tylko jedno wystąpienie funkcji przebiegów, nawet w przypadku wielu wystąpień aplikacji sieci web hosta. Jest to realizowane poprzez implementację [rozproszonych blokowania](#viewing-lease-blobs).

W poniższym przykładzie, tylko jedno wystąpienie `ProcessImage` funkcja działa w danym momencie:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Niektóre wyzwalacze mają wbudowaną obsługę współbieżności zarządzania:

* **QueueTrigger** — ustaw `JobHostConfiguration.Queues.BatchSize` na 1.
* **ServiceBusTrigger** — ustaw `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` na 1.
* **FileTrigger** — ustaw `FileProcessor.MaxDegreeOfParallelism` na 1.

Te ustawienia można użyć, aby upewnić się, że uruchomieniu funkcji jako pojedynczą w pojedynczym wystąpieniu. Aby upewnić się, tylko jedno wystąpienie funkcji jest uruchomiona, gdy aplikacja sieci web, którą można skalować się do wielu wystąpień, należy zastosować blokadę pojedyncze poziomu odbiornika dla funkcji (`[Singleton(Mode = SingletonMode.Listener)]`). Odbiornik blokady są nabywane przy uruchamianiu JobHost. Jeśli wszystkie trzy wystąpienia skalowanego uruchamia się w tym samym czasie, tylko jedno wystąpienie uzyskuje blokadę, a następnie uruchamia tylko jeden odbiornik.

### <a name="scope-values"></a>Wartości zakresu

Można określić **zakres wartości dlawyrażenia/** na pojedyncze wystąpienie, które gwarantuje, że będzie serializowana wszystkich wykonań funkcji w tym zakresie. Implementowanie bardziej szczegółowego blokowania w ten sposób umożliwia pewien stopień równoległości dla funkcji, podczas serializowania innych wywołań, zgodnie z ustawieniem wymagań. Na przykład w poniższym przykładzie wyrażenie zakresu wiąże `Region` wartość komunikatu przychodzącego. Gdy kolejka zawiera trzy komunikaty w regionach "Wschód", "Wschód" i "Zachodnie" odpowiednio następnie wiadomości, które mają regionie, w którym "Wschód" są wykonywane szeregowo podczas wiadomości z regionem "Zachodnie" są wykonywane równolegle z programami znajdującymi się na "Wschód".

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item
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

Domyślny zakres blokady jest `SingletonScope.Function` oznacza zakres blokady (ścieżka obiektu blob dzierżawy) jest powiązana z w pełni kwalifikowana nazwa funkcji. Aby zablokować różnych funkcji, należy określić `SingletonScope.Host` i użyj nazwy Identyfikatora zakresu, która jest taka sama dla wszystkich funkcji, które nie mają być uruchomione jednocześnie. W poniższym przykładzie, tylko jedno wystąpienie `AddItem` lub `RemoveItem` działa w czasie:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation
}
```

### <a name="viewing-lease-blobs"></a>Wyświetlanie obiektów blob z dzierżawy

Zestaw SDK zadań Webjob używa [dzierżawy usługi Azure blob](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) dzieje się w tle do zaimplementowania rozproszonych blokowania. Obiekty BLOB dzierżawy, używane przez pojedynczego wystąpienia można znaleźć w `azure-webjobs-host` kontenera w `AzureWebJobsStorage` konta magazynu w ścieżce "blokady". Na przykład ścieżka obiektu blob dzierżawy w pierwszym `ProcessImage` przedstawionym wcześniej przykładzie może być `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Wszystkie ścieżki obejmują identyfikator JobHost, w tym 061851c758f04938a4426aa9ab3869c0 wielkości liter.

## <a name="async-functions"></a>Funkcje asynchroniczne

Informacje dotyczące funkcji asynchronicznych w kodzie, w dokumentacji usługi Azure Functions na [funkcje asynchroniczne](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Anulowanie tokenów

Aby informacji na temat sposobu obsługi tokenów anulowania, zobacz dokumentację usługi Azure Functions w [anulowanie tokenów i łagodne zamykanie](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Wiele wystąpień

Jeśli aplikacja sieci web działa w wielu wystąpieniach, ciągłe zadanie WebJob działa na każdym wystąpieniu nasłuchiwanie wyzwalaczy i wywoływanie funkcji. Różne powiązania wyzwalacza są przeznaczone do wydajnego udostępnianie pracy wspólnie w wystąpieniach, umożliwiając skalowanie w poziomie do większej liczby wystąpień pozwala obsługiwać większe obciążenie.

Wyzwalacze kolejek i obiektów blob automatycznie uniemożliwić funkcji przetwarzania komunikatu w kolejce lub więcej niż raz, a obiekt blob funkcje nie trzeba być idempotentne.

Wyzwalacz czasomierza zapewni tego tylko jedno wystąpienie uruchamia czasomierz, dzięki czemu nie uzyskasz więcej niż jedno wystąpienie funkcji uruchamiania w danym momencie zaplanowane.

Jeśli chcesz upewnić się, że tylko jedno wystąpienie funkcji jest uruchamiany nawet wtedy, gdy istnieje wiele wystąpień hosta aplikacji sieci web, można użyć [pojedynczego atrybutu](#singleton-attribute).

## <a name="filters"></a>Filtry

Funkcja filtry (wersja zapoznawcza) umożliwiają dostosowywanie potoku wykonywania zadań Webjob za pomocą własnej logiki. Filtry są podobne do [platformy ASP.NET Core filtry](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Mogą one wdrożone jako deklaratywne atrybuty, które są stosowane do funkcji lub klasy. Aby uzyskać więcej informacji, zobacz [filtry funkcja](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Firma Microsoft zaleca struktury rejestrowania, który został opracowany dla programu ASP.NET, i [wprowadzenie](webjobs-sdk-get-started.md) artykule pokazano, jak z niego korzystać. 

### <a name="log-filtering"></a>Filtrowanie dziennika

Każdy dziennik utworzony przez `ILogger` wystąpienia ma skojarzoną `Category` i `Level`. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) jest wyliczeniem, a kod liczby całkowitej wskazuje względne znaczenie:

|LogLevel    |Kod|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczny    | 5 |
|Brak        | 6 |

Każdej kategorii, które mogą być niezależne filtrowane do określonego [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel). Na przykład możesz chcieć wyświetlić wszystkie dzienniki obiektów blob wyzwalacza przetwarzania jednak tylko `Error` lub nowszy dla wszystkich innych.

#### <a name="version-3x"></a>W wersji 3.x

W wersji 3.x zestaw SDK zależy od tego, filtrowanie, wbudowane w platformy .NET Core. `LogCategories` Klasy pozwala zdefiniować kategorie dla określonych funkcji, wyzwalacze bądź użytkowników. Umożliwia on również Definiowanie filtrów dla stanów określonego hosta, takich jak `Startup` i `Results`. W ten sposób można dostosować rejestrowania danych wyjściowych. Jeśli nie znajdzie dopasowania w zdefiniowanych kategoriach, filtr powraca do `Default` wartość przy podejmowaniu decyzji do filtrowania wiadomości.

`LogCategories` wymaga następujących instrukcję using:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

Poniższy przykład tworzy domyślne filtry umożliwiające wszystkie dzienniki w `Warning` poziom. Kategorie `Function` lub `results` (równoważne `Host.Results` w wersji 2.x) są filtrowane w `Error` poziom. Filtr porównuje bieżącej kategorii na wszystkich poziomach zarejestrowanego w `LogCategories` wystąpienia i wybiera najdłuższego dopasowania. Oznacza to, że `Debug` poziom zarejestrowany dla `Host.Triggers` będzie odpowiadał `Host.Triggers.Queue` lub `Host.Triggers.Blob`. Dzięki temu można kontrolować szerszych kategorii bez konieczności dodawania każdej z nich.

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

#### <a name="version-2x"></a>W wersji 2.x

W wersji 2.x zestawu SDK, `LogCategoryFilter` klasa jest używana do sterowania filtrowania. `LogCategoryFilter` Ma `Default` właściwość o wartości początkowej `Information`, co oznacza, że wszystkie komunikaty o poziomie `Information`, `Warning`, `Error`, lub `Critical` są rejestrowane, ale wszystkie komunikaty z poziomami `Debug` lub `Trace` są filtrowane natychmiast.

Podobnie jak w przypadku `LogCategories` w wersji 23.x, `CategoryLevels` Właściwość pozwala określić poziomy dziennika dla poszczególnych kategorii, więc można dostosować, rejestrowanie danych wyjściowych. Jeśli nie zostanie znalezione dopasowanie w ciągu `CategoryLevels` słownika, filtr powraca do `Default` wartość przy podejmowaniu decyzji do filtrowania wiadomości.

Poniższy przykład tworzy domyślne filtry umożliwiające wszystkie dzienniki w `Warning` poziom. Kategorie `Function` lub `Host.Results` są filtrowane w `Error` poziom. `LogCategoryFilter` Porównuje bieżącej kategorii do wszystkich zarejestrowanych `CategoryLevels` i wybiera najdłuższego dopasowania. Oznacza to, że `Debug` poziom zarejestrowany dla `Host.Triggers` będzie odpowiadał `Host.Triggers.Queue` lub `Host.Triggers.Blob`. Dzięki temu można kontrolować szerszych kategorii bez konieczności dodawania każdej z nich.

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

Sposób, który implementuje niestandardową telemetrię dla [usługi Application Insights](../azure-monitor/app/app-insights-overview.md) zależy od wersję zestawu SDK, którego używasz. Aby dowiedzieć się, jak skonfigurować usługę Application Insights, zobacz [rejestrowania Dodaj usługę Application Insights](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>W wersji 3.x

Od wersji 3.x zestaw SDK zadań Webjob opiera się na hoście ogólnego platformy .NET Core, nie ma już fabryki niestandardowej telemetrii pod warunkiem. Jednakże można dodać niestandardowych danych telemetrycznych do potoku przy użyciu iniekcji zależności. Przykłady w tej sekcji wymagają następującej `using` instrukcji:

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
        // Add Logging Providers
        b.AddConsole();

        // If this key exists in any config, use it to enable App Insights
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

Gdy [ `TelemetryConfiguration` ] jest konstruowany, wszystkie zarejestrowane typy [ `ITelemetryInitializer` ] są uwzględniane. Aby dowiedzieć się więcej na temat pracy z zobacz [interfejsu API usługi Application Insights dla niestandardowych zdarzeń i metryk](../azure-monitor/app/api-custom-events-metrics.md).

W wersji 3.x, nie musisz już opróżnić [ `TelemetryClient` ] zatrzymania hosta. System iniekcji zależności platformy .NET Core automatycznie usuwa zarejestrowaną `ApplicationInsightsLoggerProvider`, które opróżnienia [ `TelemetryClient` ].

#### <a name="version-2x"></a>W wersji 2.x

W wersji 2.x, [ `TelemetryClient` ] utworzone wewnętrznie przez dostawcę usługi Application Insights dla korzysta z zestawem SDK usługi WebJobs [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Gdy punkt końcowy usługi Application Insights jest niedostępny lub ograniczania żądań przychodzących, ten kanał [zapisuje żądań w systemie plików aplikacji sieci web i przesyła je ponownie później](https://apmtips.com/blog/2015/09/03/more-telemetry-channels).

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

        // change the default from 30 seconds to 15 seconds
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

Określa obiekt SamplingPercentageEstimatorSettings [próbkowanie adaptacyjne](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Oznacza to, że w niektórych scenariuszach mocno obciążające App Insights wysyła wybrany podzbiór danych telemetrycznych do serwera.

Po utworzeniu fabryki danych telemetrycznych przekażesz go do dostawcy logowania usługi Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Następne kroki

Ten przewodnik udostępnia fragmenty kodu, które pokazują, jak obsługiwać typowe scenariusze dotyczące pracy z zestawem SDK usługi WebJobs. Aby uzyskać pełne przykładów, zobacz [azure-webjobs-sdk-samples](https://github.com/Azure/azure-webjobs-sdk-samples).

[`ExecutionContext`]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[`TelemetryClient`]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[`ConfigureServices`]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
[`ITelemetryInitializer`]: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
[`TelemetryConfiguration`]: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
