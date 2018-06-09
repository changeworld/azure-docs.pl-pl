---
title: Jak używać zestawu Azure WebJobs SDK
description: Dowiedz się więcej o tym, jak napisać kod dla tego zestawu SDK zadań Webjob. Utwórz sterowane zdarzeniami tła zadania przetwarzania, które uzyskują dostęp do danych w usługach Azure i usług innych firm.
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: 08272ba7d828f744336723f25b482bf06b9e43dc
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234654"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Jak używać zestawu Azure WebJobs SDK do przetwarzania w tle sterowane zdarzeniami

Ten artykuł zawiera wskazówki dotyczące pisania kodu dla [zestaw SDK zadań Webjob Azure](webjobs-sdk-get-started.md). Dokumentację dotyczy wersji 2.x i 3.x, z wyjątkiem przypadków, gdy zaznaczono inaczej. Główne zmiany wprowadzone przez 3.x jest korzystanie z platformy .NET Core zamiast .NET Framework.

>[!NOTE]
> [Środowisko Azure Functions](../azure-functions/functions-overview.md) jest zbudowany na zestaw SDK zadań Webjob i ten artykuł zawiera łącza do dokumentacji usługi Azure Functions niektóre tematy. Należy zwrócić uwagę na następujące różnice między funkcje i zestaw SDK zadań Webjob:
> * Wersja funkcji Azure 1.x odpowiada wersji zestawu SDK zadań Webjob 2.x i usługę Azure Functions 2.x odpowiada zestaw SDK zadań Webjob 3.x. Repozytoriach kodów źródłowych Wykonaj zestaw SDK zadań Webjob numerowanie, a wiele ma v2.x gałęzi, z aktualnie mające kod 3.x gałęzi głównej.
> * Przykładowy kod dla bibliotek klas Azure funkcje C# działa jak zestaw SDK zadań Webjob kodu z tym nie ma potrzeby `FunctionName` atrybutu w projekcie zestaw SDK zadań Webjob.
> * Niektóre typy powiązania są obsługiwane tylko w przypadku funkcji, takich jak HTTP elementu webhook i siatki zdarzeń, (która jest oparta na HTTP). 
> 
> Aby uzyskać więcej informacji, zobacz [porównanie zestaw SDK zadań Webjob i usługi Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs). 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, zapoznaj się [wprowadzenie do zestawu SDK WebJobs](webjobs-sdk-get-started.md).

## <a name="jobhost"></a>JobHost

`JobHost` Obiekt jest kontenerem środowiska uruchomieniowego dla funkcji: nasłuchuje wyzwalaczy i wywołania funkcji. Możesz utworzyć `JobHost` w kodzie i kod zapisu, aby dostosować zachowanie.

Jest to Najważniejsza różnica między przy użyciu zestawu SDK WebJobs bezpośrednio i on pośrednio przy użyciu usługi Azure Functions. W przypadku funkcji Azure service formanty `JobHost`, i nie można go dostosować pisanie kodu. Środowisko Azure Functions pozwala dostosować zachowanie hosta za pomocą ustawień *host.json* pliku. Te ustawienia są ciągami, nie kod, który ogranicza rodzajów dostosowań, które może wykonywać.

### <a name="jobhost-connection-strings"></a>Parametry połączenia JobHost

Zestaw SDK zadań Webjob szuka parametry połączenia magazynu i usługi Service Bus w *local.settings.json* po uruchomieniu lokalnie lub w środowisku zadania WebJob po uruchomieniu na platformie Azure. Jeśli chcesz użyć nazwy dla tych parametrów połączenia lub przechowywać je w innym miejscu, można ustawić je w kodzie, jak pokazano poniżej:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="jobhost-development-settings"></a>Ustawienia środowiska deweloperskiego JobHost

`JobHostConfiguration` Klasa ma `UseDevelopmentSettings` — metoda, którą można wywołać, aby lokalne działania projektowe efektywniejsze. Oto kilka ustawień, które zmienia tej metody:

| Właściwość | Ustawienie programowanie |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` Aby zmaksymalizować wpisu w dzienniku. |
| `Queues.MaxPollingInterval`  | Niską wartość, aby upewnić się, że metody kolejki są wyzwalane natychmiast.  |
| `Singleton.ListenerLockPeriod` | 15 sekund ułatwiających szybkie opracowywanie iteracji. |

Poniższy przykład przedstawia użycie ustawienia środowiska deweloperskiego. Aby `config.IsDevelopment` zwracać `true` podczas uruchamiania lokalnego, ustaw zmienną środowiska lokalnego o nazwie `AzureWebJobsEnv` o wartości `Development`.

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

### <a name="jobhost-servicepointmanager-settings"></a>ServicePointManager JobHost — ustawienia

.NET Framework zawiera interfejs API o nazwie [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) steruje liczba jednoczesnych połączeń z hostem. Zaleca się zwiększyć wartość tego parametru wartość domyślną 2 przed rozpoczęciem hosta zadań Webjob.

Wszystkie wychodzących żądań HTTP, wprowadzone przez funkcję za pomocą `HttpClient` przepływać przez `ServicePointManager`. Gdy naciśniesz `DefaultConnectionLimit`, `ServicePointManager` uruchamia żądań Kolejkowanie przed ich wysłaniem. Załóżmy, że Twoje `DefaultConnectionLimit` ma ustawioną wartość 2 i kod sprawia, że 1000 HTTP żądań. Początkowo tylko 2 żądań faktycznie mogą za pośrednictwem systemu operacyjnego. Inne 998 są umieszczane w kolejce do momentu miejsca dla nich. Oznacza to, że Twoje `HttpClient` może upłynął limit czasu, ponieważ jego *sądzi* ma zgłosił żądanie, ale żądanie nie zostało wysłane przez system operacyjny na serwerze docelowym. Można napotkać zachowanie, które wydaje się być uzasadniona: lokalne `HttpClient` trwa 10 sekund, aby ukończyć żądanie, ale usługa zwraca każde żądanie w 200 ms. 

Wartość domyślna dla aplikacji ASP.NET to `Int32.MaxValue`, i jest zwykle działa dobrze w przypadku zadań Webjob uruchomione w planie usługi aplikacji — warstwa podstawowa lub nowszej. Zadania Webjob zazwyczaj konieczne ustawienie zawsze włączone i która jest obsługiwana tylko przez planów usługi aplikacji — warstwa podstawowa i wyższych. 

Jeśli WebJob działa w wolnych lub udostępnione planu usługi App Service, aplikacja jest ograniczony przez piaskownicę usługi aplikacji, które obecnie ma [limit połączeń 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Z limitem niezwiązanego połączenia w `ServicePointManager`, jest bardziej prawdopodobne, że próg połączenia piaskownicy zostanie osiągnięta i zamknąć lokacji. W takim przypadku ustawienie `DefaultConnectionLimit` inny małe, takich jak 50 lub 100, można zapobiec i nadal umożliwiają wystarczającą przepustowość.

Ustawienie musi być skonfigurowany przed żadnych żądań HTTP. Z tego powodu hosta zadań Webjob nie należy próbować dostosować ustawienie automatycznie; może to być żądania HTTP, które wystąpić, zanim host uruchamia i może to prowadzić do nieoczekiwanego zachowania. Najlepszym rozwiązaniem jest można ustawić wartości bezpośrednio w Twojej `Main` metody przed inicjowanie `JobHost`, jak pokazano w poniższym przykładzie

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

Funkcje muszą być prawidłowymi metodami publicznego i musi mieć jeden atrybut wyzwalacza lub [NoAutomaticTrigger](#manual-trigger) atrybutu.

### <a name="automatic-trigger"></a>Automatycznie wyzwalana

Wyzwalacze automatyczne wywoływanie funkcji w odpowiedzi na zdarzenia. Na przykład zobacz wyzwalacza kolejki w [artykułu Uruchomiono Get](webjobs-sdk-get-started.md).

### <a name="manual-trigger"></a>Ręczne wyzwalacza

Aby ręcznie zainicjuje funkcję, użyj `NoAutomaticTrigger` atrybutu, jak pokazano w poniższym przykładzie:

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
    TextWriter logger,
    string value,
    [Queue("outputqueue")] out string message)
{
    message = value;
    logger.WriteLine("Creating queue message: ", message);
}
```

## <a name="input-and-output-bindings"></a>Wejście i wyjście powiązania

Powiązania wejściowe zawierają deklaratywne udostępnić dane z platformy Azure lub usług innych firm w kodzie. Powiązania danych wyjściowych zapewniają sposób, aby zaktualizować dane. [Artykułu Uruchomiono Get](webjobs-sdk-get-started.md) przedstawiono przykład każdego z nich.

Wartość zwracaną metody można użyć dla powiązania danych wyjściowych przez zastosowanie atrybutu na wartość zwracaną metody. Zapoznaj się z przykładem w usługi Azure Functions [wyzwalaczy i powiązań](../azure-functions/functions-triggers-bindings.md#using-the-function-return-value) artykułu.

## <a name="binding-types"></a>Typ powiązania

Następujące typy wyzwalaczy i powiązań są uwzględniane w `Microsoft.Azure.WebJobs` pakietu:

* Blob Storage
* Queue Storage
* Magazyn tabel

Aby korzystać z innych wyzwalacza i typ powiązania, zainstaluj pakiet NuGet, który je zawiera i Wywołaj `Use<binding>` metoda `JobHostConfiguration` obiektu. Na przykład, jeśli chcesz użyć wyzwalacza bazującego na czasomierzu zainstalować `Microsoft.Azure.WebJobs.Extensions` i Wywołaj `UseTimers` w `Main` metody, jak w poniższym przykładzie:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Można znaleźć pakietu do zainstalowania typu określonego powiązania w **pakiety** sekcji tego typu powiązania [artykule](#binding-reference-information) dla usługi Azure Functions. Wyjątkiem jest wyzwalacz plików i powiązania (w przypadku lokalnego systemu plików), który nie jest obsługiwany przez usługi Azure Functions. Aby korzystać z plików powiązanie, zainstaluj `Microsoft.Azure.WebJobs.Extensions` i Wywołaj `UseFiles`.

### <a name="usecore"></a>UseCore

`Microsoft.Azure.WebJobs.Extensions` Pakietów wymienionych poniżej dostępne są także specjalne powiązania typu, który można zarejestrować przez wywołanie metody `UseCore` metody. To powiązanie pozwala zdefiniować [ExecutionContext](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs) parametru w podpisu funkcji. Obiekt kontekstu umożliwia dostęp do Identyfikatora wywołania, które służy do skorelowania wszystkie dzienniki utworzonej przez wywołanie danej funkcji. Oto przykład:

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

## <a name="binding-configuration"></a>Konfiguracja powiązania

Wiele wyzwolić i powiązanie typy pozwalają skonfigurować ich zachowanie przez ustawienie właściwości w obiekcie konfiguracji, który jest przekazywany do `JobHost`.

### <a name="queue-trigger-configuration"></a>Konfiguracja wyzwalaczy kolejki

Ustawienia można skonfigurować dla wyzwalacza kolejki magazynu opisano szczegółowo w usługi Azure Functions [odwołania host.json](../azure-functions/functions-host-json.md#queues). W poniższym przykładzie pokazano sposób ustawiania ich w projekcie zestaw SDK zadań Webjob:

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

### <a name="configuration-for-other-bindings"></a>Konfiguracja dla pozostałych powiązaniach

Niektóre typy wyzwalaczy i powiązań Definiowanie własnych typu konfiguracji niestandardowej. Na przykład wyzwalacz pliku pozwala określić ścieżkę katalogu głównego do monitorowania:

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

W parametrach konstruktora atrybut można użyć wyrażenia, które rozpoznają wartości z różnych źródeł. Na przykład w poniższym kodzie, ścieżka `BlobTrigger` atrybutu tworzy wyrażenie o nazwie `filename`. Gdy jest używany do wiązania danych wyjściowych `filename` jest rozpoznawany jako nazwa wyzwalająca obiektu blob.
 
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

Aby uzyskać więcej informacji na temat wyrażenia wiązania, zobacz [powiązania wyrażeń i wzorce](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns) w dokumentacji usługi Azure Functions.

### <a name="custom-binding-expressions"></a>Wyrażenia wiązania niestandardowego

Czasami chcesz określić nazwę kolejki, nazwa obiektu blob lub kontener lub tabeli nazw w kodzie, a nie kodowane. Na przykład można określić nazwę kolejki `QueueTrigger` atrybutu w zmiennej środowisku i pliku konfiguracji.

Możesz to zrobić przez przekazywanie `NameResolver` do obiektu `JobHostConfiguration` obiektu. Zawierają symbole zastępcze w wyzwalacza lub parametrów konstruktora atrybut powiązania i `NameResolver` kod zawiera rzeczywiste wartości do użycia zamiast te symbole zastępcze. Symbole zastępcze są identyfikowane przez umieszczenie ich w znaki procentu (%), jak pokazano w poniższym przykładzie:
 
```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Ten kod umożliwia używanie kolejki o nazwie logqueuetest w środowisku testowym i jedną o nazwie logqueueprod w środowisku produkcyjnym. Zamiast nazwy kolejki wpisaną na stałe, należy określić nazwę wpisu `appSettings` kolekcji. 

Brak wartości domyślnej NameResolver, która zostanie uwzględniona, jeśli nie podasz niestandardowego. Wartość domyślna pobiera wartości z ustawień aplikacji lub zmiennych środowiskowych.

Twoje `NameResolver` klasy pobiera nazwę kolejki z `appSettings` jak pokazano w poniższym przykładzie:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Przekaż Twojej `NameResolver` klasy w celu `JobHost` obiektów, jak pokazano w poniższym przykładzie:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure implementuje funkcje `INameResolver` można pobrać wartości z ustawień aplikacji, jak pokazano w przykładzie. Gdy używasz zestawu SDK WebJobs bezpośrednio, można pisać niestandardowe implementację, która pobiera symbol zastępczy wartości zastępcze z dowolnego źródła preferowane. 

## <a name="binding-at-runtime"></a>Powiązanie w czasie wykonywania

Jeśli zachodzi konieczność wykonania dodatkowych czynności w funkcji przed użyciem takich jak atrybut wiązania `Queue`, `Blob`, lub `Table`, można użyć `IBinder` interfejsu.

Poniższy przykład pobiera wiadomość z kolejki wejściowej i tworzy nowy komunikat o tej samej zawartości w kolejki wyjściowej. Nazwa kolejki danych wyjściowych jest ustawiana przez kod w treści funkcji.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

Aby uzyskać więcej informacji, zobacz [powiązania w czasie wykonywania](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) w dokumentacji usługi Azure Functions.

## <a name="binding-reference-information"></a>Informacje o odwołaniu powiązania

Informacje na temat poszczególnych typów powiązania jest dostępny w dokumentacji usługi Azure Functions. Na przykład za pomocą magazynu kolejek, znajdziesz następujące informacje w każdym artykule powiązania:

* [Pakiety](../azure-functions/functions-bindings-storage-queue.md#packages---functions-1x) — co pakiet do zainstalowania w celu uwzględnienia obsługi powiązania w projekcie zestaw SDK zadań Webjob.
* [Przykłady](../azure-functions/functions-bindings-storage-queue.md#trigger---example) — przykład biblioteki klas C# ma zastosowanie do zestawu SDK WebJobs; Pomiń tylko `FunctionName` atrybutu.
* [Atrybuty](../azure-functions/functions-bindings-storage-queue.md#trigger---attributes) — atrybuty do użycia na potrzeby typ powiązania.
* [Konfiguracja](../azure-functions/functions-bindings-storage-queue.md#trigger---configuration) -objaśnienia właściwości atrybutów i parametrami konstruktora.
* [Użycie](../azure-functions/functions-bindings-storage-queue.md#trigger---usage) — co typów można powiązać i uzyskać informacje na temat wiązania działa. Na przykład: sondowanie algorytmu, zanieczyszczonych przetwarzania kolejki.
  
Lista powiązanie artykuły odwołania, zobacz **obsługiwane powiązania** w [wyzwalaczy i powiązań](../azure-functions/functions-triggers-bindings.md#supported-bindings) artykułu dla usługi Azure Functions. Na tej liście powiązania HTTP elementu webhook i siatki zdarzeń są obsługiwane tylko przez usługi Azure Functions, a nie przez zestaw SDK zadań Webjob.

## <a name="disable-attribute"></a>Wyłącz atrybutu 

[Wyłączyć](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) atrybutu umożliwia kontrolowanie, czy funkcja może być uruchomiona. 

W poniższym przykładzie Jeśli ustawienia aplikacji `Disable_TestJob` ma wartość "1" lub "Prawda" (bez uwzględniania wielkości liter), funkcja nie będzie działać. W takim przypadku środowiska uruchomieniowego tworzy komunikat dziennika *jest wyłączona funkcja "Functions.TestJob"*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

Po zmianie wartości ustawień aplikacji w portalu Azure powoduje zadania WebJob, należy ponownie uruchomić pobieranie nowe ustawienie.

Ten atrybut można zadeklarować jako poziomie parametrów, metody lub klasy. Nazwa ustawienia może również zawierać wyrażenia powiązania.

## <a name="timeout-attribute"></a>Atrybut limitu czasu

[Limitu czasu](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs) atrybut powoduje funkcję, która ma zostać anulowane, jeśli nie wykona w określonym czasie. W poniższym przykładzie funkcja może działać bez limitu czasu przez jeden dzień. Limit czasu funkcja została anulowana po 15 sekund.

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

Możesz zastosować atrybut limitu czasu na poziomie klasy lub metoda i globalnego limitu czasu można określić za pomocą `JobHostConfiguration.FunctionTimeout`. Klasa lub metoda poziomu limitów czasu zastąpić globalnego limitu czasu.

## <a name="singleton-attribute"></a>Atrybut pojedynczego wystąpienia

Użyj [pojedyncze](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs) atrybutu upewnij się, że tylko jedno wystąpienie funkcji działa nawet jeśli istnieje wiele wystąpień hosta aplikacji sieci web. Robi to zaimplementowanie [rozproszonych blokowania](#viewing-lease-blobs).

W poniższym przykładzie, tylko jedno wystąpienie `ProcessImage` funkcja jest uruchamiana w danym momencie:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image
}
```

### <a name="singletonmodelistener"></a>SingletonMode.Listener

Niektóre wyzwalacze ma wbudowaną obsługę zarządzania współbieżności:

* **QueueTrigger** — skonfiguruj `JobHostConfiguration.Queues.BatchSize` do 1.
* **ServiceBusTrigger** — skonfiguruj `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` do 1.
* **FileTrigger** — skonfiguruj `FileProcessor.MaxDegreeOfParallelism` do 1.

Te ustawienia służy do sprawdzenia, czy funkcja działa jako pojedynczą w pojedynczym wystąpieniu. W celu zapewnienia tylko jedno wystąpienie funkcji jest uruchomiony, gdy aplikacja sieci web może obsłużyć się wiele wystąpień, zastosuj blokady odbiornika z poziomu pojedynczego wystąpienia w funkcji (`[Singleton(Mode = SingletonMode.Listener)]`). Odbiornik blokad są uzyskiwane przy uruchamianiu JobHost. Jeśli wszystkie trzy wystąpienia skalowalnych w poziomie można uruchomić w tym samym czasie, tylko jedno wystąpienie uzyskuje blokadę i rozpoczyna się tylko jeden odbiornik.

### <a name="scope-values"></a>Wartości zakresu

Można określić **zakres wyrażenie i wartości** na pojedyncze, który zapewni będą serializowane wykonaniami wszystkich funkcji w tym zakresie. Implementowanie bardziej szczegółowego blokowania w ten sposób można zezwolić na pewien stopień równoległości dla funkcji, podczas serializowania innych wywołań zgodnie z wymaganiami. Na przykład w poniższym przykładzie wyrażenia zakres wiąże `Region` wartość komunikatu przychodzącego. Jeśli kolejka zawiera 3 wiadomości w regionach "Wschód", "Wschód" i "Zachód" odpowiednio wiadomości, które mają region "Wschód" zostanie wykonane szeregowe podczas wiadomości z regionu "Zachód" będą wykonywane równolegle z tymi.

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

Domyślny zakres dla blokady jest `SingletonScope.Function` oznacza zakres blokady (ścieżka dzierżawy obiektów blob) jest powiązany z nazwą FQDN funkcji. Aby zablokować przez funkcje, określ `SingletonScope.Host` i użyj nazwy Identyfikatora zakresu, która jest taka sama we wszystkich funkcji, które nie mają być uruchomione jednocześnie. W poniższym przykładzie, tylko jedno wystąpienie `AddItem` lub `RemoveItem` działa jednocześnie:

```charp
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

Zestaw SDK zadań Webjob używa [dzierżawy usługi Azure blob](../storage/common/storage-concurrency.md#pessimistic-concurrency-for-blobs) w obszarze obejmuje do zaimplementowania rozproszonej blokowania. Używane przez pojedyncze obiekty BLOB dzierżawy znajdują się w `azure-webjobs-host` kontenera w `AzureWebJobsStorage` konta magazynu w ścieżce "blokady". Na przykład ścieżka obiektu blob dzierżawy w pierwszym `ProcessImage` może być wcześniej przykładzie `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage`. Wszystkie ścieżki obejmować identyfikator JobHost, w tym wielkość 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Funkcje asynchroniczne

Informacje dotyczące funkcji asynchronicznych kodu, w dokumentacji usługi Azure Functions na [funkcji asynchronicznych](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Anulowanie tokenów

Aby uzyskać informacje o sposobie obsługi anulowanie tokenów, zobacz dokumentację usługi Azure Functions na [anulowanie tokenów i łagodne zamykanie](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Wiele wystąpień

Aplikacja sieci web jest uruchamiana na wiele wystąpień, ciągłe zadanie WebJob uruchamiane w każde wystąpienie nasłuchiwanie wyzwalaczy i wywoływanie funkcji. Różne powiązania wyzwalacza mają na celu wydajnie udostępniania pracy wspólnie w wystąpieniach, dzięki czemu skalowanie w poziomie więcej wystąpień umożliwia obsługę większe obciążenie.

Wyzwalacze kolejek i obiektów blob automatycznie uniemożliwić przetwarzania komunikatu w kolejce funkcji lub obiektu blob więcej niż raz; Funkcje, nie trzeba być idempotentności.

Wyzwalacz czasomierza zapewni tego tylko jedno wystąpienie uruchamia czasomierz, nie otrzymasz więcej niż jedno wystąpienie funkcji uruchomiona w danym momencie zaplanowane.

Jeśli chcesz upewnić się, że tylko jedno wystąpienie funkcji jest uruchamiany nawet wtedy, gdy istnieje wiele wystąpień aplikacji sieci web hosta, możesz użyć [pojedyncze](#singleton) atrybutu.
    
## <a name="filters"></a>Filtry 

Funkcja filtry (wersja zapoznawcza) umożliwiają dostosowanie potoku wykonywania zadań Webjob na własną logikę. Filtry są podobne do [platformy ASP.NET Core filtry](https://docs.microsoft.com/aspnet/core/mvc/controllers/filters). Mogą one być wdrożone jako deklaratywne atrybuty, które są stosowane do funkcji lub klasy. Aby uzyskać więcej informacji, zobacz [filtry funkcja](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Rejestrowanie i monitorowanie

Firma Microsoft zaleca struktury rejestrowania, który został opracowany dla platformy ASP.NET, oraz [wprowadzenie](webjobs-sdk-get-started.md) artykule pokazano, jak z niego korzystać. 

### <a name="log-filtering"></a>Filtrowanie dziennika

Każdy dziennik `ILogger` wystąpienie ma skojarzoną `Category` i `Level`. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) jest wyliczeniem i kod całkowitą wskazuje znaczenie:

|LogLevel    |Kod|
|------------|---|
|Ślad       | 0 |
|Debugowanie       | 1 |
|Informacje | 2 |
|Ostrzeżenie     | 3 |
|Błąd       | 4 |
|Krytyczne    | 5 |
|Brak        | 6 |

Każda kategoria może niezależnie filtrowane do określonego [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel). Na przykład można wyświetlić wszystkie dzienniki dla obiekt blob wyzwalacza przetwarzania ale tylko `Error` i wyższych dla wszystkich innych urządzeń.

Aby ułatwić określenie reguł filtrowania, zawiera zestaw SDK zadań Webjob `LogCategoryFilter` mogą zostać przekazane do wielu istniejących dostawców logowania, w tym Application Insights i konsoli.

`LogCategoryFilter` Ma `Default` właściwość o początkowej wartości `Information`, co oznacza, że wszystkie komunikaty z poziomami `Information`, `Warning`, `Error`, lub `Critical` są rejestrowane, ale wszystkie komunikaty z poziomami `Debug` lub `Trace` są filtrowane optymalizacji.

`CategoryLevels` Właściwość pozwala określić poziomy dziennika dla poszczególnych kategorii, tak aby precyzyjnie zdefiniować dane wyjściowe rejestrowania. Jeśli nie znaleziono w `CategoryLevels` słownika, filtr powraca do `Default` wartość przy podejmowaniu decyzji do filtrowania wiadomości.

Poniższy przykład tworzy domyślnie filtry umożliwiające wszystkie dzienniki w `Warning` poziom. Kategorie `Function` lub `Host.Results` są filtrowane w `Error` poziom. `LogCategoryFilter` Porównuje bieżącej kategorii do wszystkich zarejestrowanych `CategoryLevels` i wybiera najdłuższe dopasowanie. Oznacza to, że `Debug` poziom zarejestrowany dla `Host.Triggers` będzie odpowiadała `Host.Triggers.Queue` lub `Host.Triggers.Blob`. Pozwala na kontrolowanie szerszych kategorii bez konieczności dodawania każdej z nich.

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

### <a name="custom-telemetry-for-application-insights"></a>Telemetria niestandardowa dla usługi Application Insights

Wewnętrznie `TelemetryClient` utworzony przez dostawcę usługi Application Insights dla korzysta z zestawu SDK WebJobs [ServerTelemetryChannel](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/ServerTelemetryChannel/ServerTelemetryChannel.cs). Gdy punkt końcowy usługi Application Insights jest niedostępna lub ograniczanie żądań przychodzących, ten kanał [żądań jest zapisywany w aplikacji sieci web systemu plików i przesyła je później ponownie](http://apmtips.com/blog/2015/09/03/more-telemetry-channels).

`TelemetryClient` Jest tworzony przez klasę, która implementuje `ITelemetryClientFactory`. Domyślnie jest to [DefaultTelemetryClientFactory](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/DefaultTelemetryClientFactory.cs).

Jeśli chcesz zmodyfikować dowolną część procesu usługi Application Insights, możesz podać własne `ITelemetryClientFactory`, a host będzie używać klasy do konstruowania `TelemetryClient`. Na przykład ten kod zastępuje `DefaultTelemetryClientFactory` można zmodyfikować właściwości `ServerTelemetryChannel`:

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

Konfiguruje obiektu SamplingPercentageEstimatorSettings [adaptacyjną próbkowania](https://docs.microsoft.com/azure/application-insights/app-insights-sampling#adaptive-sampling-at-your-web-server). Oznacza to, że w niektórych scenariuszach dużych App Insights wysyła wybrany podzbiór danych telemetrycznych do serwera.

Po utworzeniu fabryki danych telemetrycznych przekazujesz ją w dostawcy rejestrowania usługi Application Insights:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a id="nextsteps"></a> Następne kroki

W tym przewodniku udostępnił wstawki kodu, które przedstawiają sposób obsługi typowe scenariusze dotyczące pracy z zestawem SDK zadań Webjob. Do ukończenia próbek, zobacz [azure-zadań webjob sdk przykłady](https://github.com/Azure/azure-webjobs-sdk-samples).