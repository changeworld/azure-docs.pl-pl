---
title: Śledzenie operacji niestandardowych za pomocą sdk usługi Azure Application Insights .NET
description: Śledzenie operacji niestandardowych za pomocą sdk usługi Azure Application Insights .NET
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 31c1fb366e7b109ea1fa4977d8e2f908e766e0f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276103"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Śledzenie operacji niestandardowych za pomocą sdk usługi Application Insights .NET

Zestaw SDK usługi Azure Application Insights automatycznie śledzi przychodzące żądania HTTP i wywołania do usług zależnych, takich jak żądania HTTP i zapytania SQL. Śledzenie i korelacja żądań i zależności zapewniają wgląd w szybkość reakcji i niezawodność całej aplikacji we wszystkich mikrousługach łączących tę aplikację. 

Istnieje klasa wzorców aplikacji, które nie mogą być obsługiwane ogólnie. Prawidłowe monitorowanie takich wzorców wymaga ręcznego oprzyrządowania kodu. W tym artykule opisano kilka wzorców, które mogą wymagać ręcznego instrumentacji, takich jak niestandardowe przetwarzanie kolejek i uruchamianie długotrwałych zadań w tle.

Ten dokument zawiera wskazówki dotyczące śledzenia operacji niestandardowych za pomocą sdk usługi Application Insights. Niniejsza dokumentacja jest odpowiednia dla:

- Usługa Application Insights dla platformy .NET (znanej również jako podstawowy sdk) w wersji 2.4+.
- Usługa Application Insights dla aplikacji sieci web (z systemem ASP.NET) w wersji 2.4+.
- Usługa Application Insights dla ASP.NET Core w wersji 2.1+.

## <a name="overview"></a>Omówienie
Operacja jest logicznym elementem pracy uruchamianym przez aplikację. Ma nazwę, czas rozpoczęcia, czas trwania, wynik i kontekst wykonywania, takich jak nazwa użytkownika, właściwości i wynik. Jeśli operacja A została zainicjowana przez operację B, operacja B jest ustawiana jako element nadrzędny dla A. Operacja może mieć tylko jeden element nadrzędny, ale może mieć wiele operacji podrzędnych. Aby uzyskać więcej informacji na temat operacji i korelacji telemetrii, zobacz [Korelacja danych telemetrycznych usługi Azure Application Insights](correlation.md).

W SDK usługi Application Insights .NET operacja jest opisana przez klasę abstrakcyjną [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) i jej elementy [podrzędne RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) i [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Śledzenie operacji przychodzących 
Webowy sdk usługi Application Insights automatycznie zbiera żądania HTTP dla ASP.NET aplikacji uruchamianych w potoku usług IIS i wszystkich aplikacji ASP.NET Core. Istnieją rozwiązania obsługiwane przez społeczność dla innych platform i struktur. Jeśli jednak aplikacja nie jest obsługiwana przez żadne z rozwiązań standardowych lub obsługiwanych przez społeczność, można ją przyrządzyć ręcznie.

Innym przykładem, który wymaga śledzenia niestandardowego jest pracownik, który odbiera elementy z kolejki. W przypadku niektórych kolejek wywołanie dodania wiadomości do tej kolejki jest śledzone jako zależność. Jednak operacja wysokiego poziomu, która opisuje przetwarzanie wiadomości nie jest automatycznie zbierane.

Zobaczmy, jak takie operacje mogą być śledzone.

Na wysokim poziomie zadaniem jest `RequestTelemetry` tworzenie i ustawianie znanych właściwości. Po zakończeniu operacji można śledzić dane telemetryczne. W poniższym przykładzie przedstawiono to zadanie.

### <a name="http-request-in-owin-self-hosted-app"></a>Żądanie HTTP w aplikacji hostowane samodzielnie owin
W tym przykładzie kontekst śledzenia jest propagowany zgodnie z [protokołem HTTP dla korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Należy oczekiwać, aby otrzymać nagłówki, które są tam opisane.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

Protokół HTTP dla korelacji również `Correlation-Context` deklaruje nagłówek. Jednak jest to pominięte tutaj dla uproszczenia.

## <a name="queue-instrumentation"></a>Instrumentacja kolejki
Chociaż istnieją [kontekst śledzenia W3C](https://www.w3.org/TR/trace-context/) i [protokół HTTP dla korelacji,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) aby przekazać szczegóły korelacji z żądaniem HTTP, każdy protokół kolejki musi zdefiniować, jak te same szczegóły są przekazywane wzdłuż komunikatu kolejki. Niektóre protokoły kolejki (takie jak AMQP) umożliwiają przekazywanie dodatkowych metadanych, a niektóre inne (takie jak Kolejka usługi Azure Storage) wymagają zakodowania kontekstu w ładunku wiadomości.

> [!NOTE]
> * **Śledzenie między składnikami nie jest jeszcze obsługiwane w przypadku kolejek** Za pomocą protokołu HTTP, jeśli producent i konsument wysyłają dane telemetryczne do różnych zasobów usługi Application Insights, środowisko diagnostyki transakcji i mapa aplikacji pokazują transakcje i mapę end-to-end. W przypadku kolejek nie jest to jeszcze obsługiwane. 

### <a name="service-bus-queue"></a>Kolejka usługi Service Bus
Usługa Application Insights śledzi wywołania usługi Service Bus Messaging za pomocą nowego [klienta usługi Microsoft Azure ServiceBus dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) w wersji 3.0.0 lub nowszej.
Jeśli używasz [wzorzec obsługi wiadomości](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania wiadomości, gotowe: wszystkie wywołania usługi Service Bus wykonywane przez usługę są automatycznie śledzone i skorelowane z innymi elementami telemetrii. Jeśli ręcznie przetwarzasz komunikaty, zapoznaj się [z śledzeniem klienta usługi Service Bus za pomocą usługi Microsoft Application Insights.](../../service-bus-messaging/service-bus-end-to-end-tracing.md)

Jeśli używasz pakietu [WindowsAzure.ServiceBus,](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) przeczytaj dalej — poniższe przykłady pokazują, jak śledzić (i skorelować) wywołania z magistrali usług owej, ponieważ kolejka usługi Service Bus używa protokołu AMQP, a usługa Application Insights nie śledzi automatycznie operacji kolejek.
Identyfikatory korelacji są przekazywane we właściwościach wiadomości.

#### <a name="enqueue"></a>Kolejka kolejka

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Proces
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Kolejka usługi Azure Storage
W poniższym przykładzie pokazano, jak śledzić operacje [kolejki usługi Azure Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md) i skorelować dane telemetryczne między producentem, konsumentem i usługą Azure Storage. 

Kolejka magazynowania ma interfejs API HTTP. Wszystkie wywołania kolejki są śledzone przez moduł zbierający zależności usługi Application Insights dla żądań HTTP.
Jest skonfigurowany domyślnie w aplikacjach ASP.NET i ASP.NET Core, z innymi rodzajami aplikacji, można zapoznać się z [dokumentacją aplikacji konsoli](../../azure-monitor/app/console.md)

Można również skorelować identyfikator operacji usługi Application Insights z identyfikatorem żądania magazynu. Aby uzyskać informacje na temat ustawiania i przynajmowania klienta żądania magazynu i identyfikatora żądania serwera, zobacz [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Kolejka kolejka
Ponieważ kolejki magazynu obsługują interfejs API HTTP, wszystkie operacje z kolejką są automatycznie śledzone przez usługa Application Insights. W wielu przypadkach to oprzyrządowanie powinno wystarczyć. Jednak aby skorelować ślady po stronie konsumenta ze śladami producenta, należy przekazać kontekst korelacji podobnie jak robimy to w protokole HTTP dla korelacji. 

W tym przykładzie `Enqueue` pokazano, jak śledzić operację. Możesz:

 - **Skorelować ponownych prób (jeśli istnieje)**: Wszystkie one `Enqueue` mają jeden wspólny element nadrzędny, który jest operacją. W przeciwnym razie są śledzone jako dziewki przychodzącego żądania. Jeśli istnieje wiele żądań logicznych do kolejki, może być trudne do znalezienia, które wywołanie spowodowało ponownych prób.
 - **Skorelować dzienniki magazynu (jeśli i kiedy jest to potrzebne)**: są one skorelowane z telemetrią usługi Application Insights.

Operacja `Enqueue` jest elementem podrzędnym operacji nadrzędnej (na przykład przychodzącego żądania HTTP). Wywołanie zależności HTTP jest dziewcym `Enqueue` operacji i wnukiem żądania przychodzącego:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Aby zmniejszyć ilość danych telemetrycznych raportów aplikacji lub jeśli `Enqueue` nie chcesz śledzić `Activity` operacji z innych powodów, należy użyć interfejsu API bezpośrednio:

- Utwórz (i uruchom) nowy `Activity` zamiast uruchamiania operacji usługi Application Insights. *Nie* trzeba przypisywać żadnych właściwości na nim z wyjątkiem nazwy operacji.
- Serializuj `yourActivity.Id` do ładunku `operation.Telemetry.Id`wiadomości zamiast . Można również `Activity.Current.Id`użyć .


#### <a name="dequeue"></a>Dequeue (dequeue)
Podobnie jak `Enqueue`w rzeczywistości rzeczywiste żądanie HTTP do kolejki magazynu jest automatycznie śledzone przez usługa Application Insights. Jednak `Enqueue` operacja prawdopodobnie dzieje się w kontekście nadrzędnym, takich jak kontekst żądania przychodzącego. SDK usługi Application Insights automatycznie skorelować taką operację (i jej część HTTP) z żądaniem nadrzędnym i innymi datami telemetrycznymi zgłoszonymi w tym samym zakresie.

Operacja `Dequeue` jest trudna. SDK usługi Application Insights automatycznie śledzi żądania HTTP. Jednak nie zna kontekstu korelacji, dopóki wiadomość nie jest analizowana. Nie jest możliwe skorelowanie żądania HTTP, aby uzyskać wiadomość z resztą danych telemetrycznych, zwłaszcza po odebraniu więcej niż jednej wiadomości.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Proces

W poniższym przykładzie przychodząca wiadomość jest śledzona w sposób podobny do przychodzącego żądania HTTP:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

Podobnie inne operacje kolejki mogą być instrumentowane. Operacja wglądu powinna być instrumentowana w podobny sposób jak operacja dequeue. Operacje zarządzania kolejką instrumentów nie są konieczne. Usługa Application Insights śledzi operacje, takie jak HTTP, a w większości przypadków wystarczy.

Podczas usuwania komunikatów instrumentu, upewnij się, że ustawiono identyfikatory operacji (korelacji). Alternatywnie można użyć `Activity` interfejsu API. Następnie nie trzeba ustawiać identyfikatorów operacji na elementach telemetrycznych, ponieważ zestaw SDK usługi Application Insights robi to za Ciebie:

- Utwórz `Activity` nowy po tym, jak masz element z kolejki.
- Służy `Activity.SetParentId(message.ParentId)` do skorelowania dzienników konsumentów i producentów.
- Uruchom `Activity`plik .
- Śledzenie operacji usuwania, przetwarzania i usuwania za pomocą `Start/StopOperation` pomocników. Zrób to z tego samego przepływu kontroli asynchronicznego (kontekst wykonywania). W ten sposób są one prawidłowo skorelowane.
- Zatrzymaj `Activity`plik .
- Użyj `Start/StopOperation`programu `Track` telemetrii lub wywołaj je ręcznie.

### <a name="dependency-types"></a>Typy zależności

Usługa Application Insights używa typu zależności do cusomize środowiska interfejsu użytkownika. Dla kolejek rozpoznaje następujące `DependencyTelemetry` typy, które poprawiają [diagnostykę transakcji:](/azure/azure-monitor/app/transaction-diagnostics)
- `Azure queue`dla kolejek usługi Azure Storage
- `Azure Event Hubs`dla usługi Azure Event Hubs
- `Azure Service Bus`dla usługi Azure Service Bus

### <a name="batch-processing"></a>Przetwarzanie wsadowe
W przypadku niektórych kolejek można usuwać w kolejce wiele wiadomości za pomocą jednego żądania. Przetwarzanie takich komunikatów jest prawdopodobnie niezależne i należy do różnych operacji logicznych. Nie jest możliwe skorelowanie `Dequeue` operacji z określoną wiadomością, która jest przetwarzana.

Każda wiadomość powinna być przetwarzana we własnym przepływie sterowania asynchroniiowego. Aby uzyskać więcej informacji, zobacz sekcję [Śledzenie zależności wychodzących.](#outgoing-dependencies-tracking)

## <a name="long-running-background-tasks"></a>Długotrwałe zadania w tle

Niektóre aplikacje uruchamiają długotrwałe operacje, które mogą być spowodowane przez żądania użytkowników. Z punktu widzenia śledzenia/instrumentacji nie różni się od instrumentacji żądania lub zależności: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

W tym `telemetryClient.StartOperation` przykładzie tworzy `DependencyTelemetry` i wypełnia kontekst korelacji. Załóżmy, że masz operację nadrzędną, która została utworzona przez przychodzące żądania, które zaplanowały operację. Tak długo, jak `BackgroundTask` rozpoczyna się w tym samym przepływie kontroli asynchroniczną jako żądanie przychodzące, jest skorelowany z tej operacji nadrzędnej. `BackgroundTask`i wszystkie zagnieżdżone elementy telemetryczne są automatycznie skorelowane z żądaniem, które go spowodowało, nawet po zakończeniu żądania.

Gdy zadanie rozpoczyna się od wątku w tle,`Activity`który nie ma `BackgroundTask` żadnej operacji ( ) skojarzone z nim, nie ma żadnego nadrzędnego. Jednak może mieć zagnieżdżone operacje. Wszystkie elementy telemetrii zgłoszone z zadania `DependencyTelemetry` są `BackgroundTask`skorelowane z utworzonym w .

## <a name="outgoing-dependencies-tracking"></a>Śledzenie zależności wychodzących
Można śledzić swój własny rodzaj zależności lub operacji, która nie jest obsługiwana przez usługa Application Insights.

Metoda `Enqueue` w kolejce usługi Service Bus lub kolejki magazynu może służyć jako przykłady takiego śledzenia niestandardowego.

Ogólne podejście do śledzenia zależności niestandardowych jest:

- Wywołanie `TelemetryClient.StartOperation` (rozszerzenie) metoda, `DependencyTelemetry` która wypełnia właściwości, które są potrzebne do korelacji i niektóre inne właściwości (sygnatura czasu rozpoczęcia, czas trwania).
- Ustaw inne właściwości niestandardowe na `DependencyTelemetry`, takich jak nazwa i inny kontekst, którego potrzebujesz.
- Nawiązać połączenie zależności i poczekaj na to.
- Zatrzymaj operację `StopOperation` po jej zakończeniu.
- Obsługa wyjątków.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Usuwanie operacji powoduje zatrzymanie operacji, więc można to zrobić `StopOperation`zamiast wywoływania .

*Ostrzeżenie:* w niektórych przypadkach nieobsługiwana wyjątek może [uniemożliwić](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` wywołanie, więc operacje mogą nie być śledzone.

### <a name="parallel-operations-processing-and-tracking"></a>Przetwarzanie i śledzenie operacji równoległych

`StopOperation`zatrzymuje tylko uruchomiono operację. Jeśli bieżąca operacja uruchamiania nie jest zgodna `StopOperation` z operacją, którą chcesz zatrzymać, nic nie robi. Taka sytuacja może się zdarzyć, jeśli uruchomisz wiele operacji równolegle w tym samym kontekście wykonywania:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Upewnij się, `StartOperation` że zawsze wywołać i przetworzyć operację w tej samej metodzie **asynchronii,** aby wyizolować operacje uruchomione równolegle. Jeśli działanie jest synchroniczne (lub nie asynchroniczne), zawij proces i śledź za pomocą: `Task.Run`

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights operacje vs System.Diagnostics.Activity
`System.Diagnostics.Activity`reprezentuje kontekst śledzenia rozproszonego i jest używany przez struktury i biblioteki do tworzenia i propagowania kontekstu wewnątrz i na zewnątrz procesu i skorelowania elementów telemetrii. Działanie współpracuje `System.Diagnostics.DiagnosticSource` z - mechanizm powiadamiania między framework/biblioteka do powiadamiania o interesujących zdarzeń (przychodzące lub wychodzące żądania, wyjątki, itp.).

Działania są najwyższej klasy obywatelami w usłudze Application Insights i `DiagnosticSource` automatyczne zależności i zbieranie żądań opiera się w dużej mierze na nich wraz ze zdarzeniami. Jeśli utworzysz działanie w aplikacji — nie spowoduje to utworzenia danych telemetrycznych usługi Application Insights. Usługa Application Insights musi odbierać zdarzenia DiagnosticSource i znać nazwy zdarzeń i ładunki, aby przetłumaczyć działanie na dane telemetryczne.

Każda operacja usługi Application Insights (żądanie `Activity` lub `StartOperation` zależność) obejmuje — gdy jest wywoływana, tworzy działanie poniżej. `StartOperation`jest zalecanym sposobem ręcznego śledzenia telemetrie żądań lub zależności i zapewnienia, że wszystko jest skorelowane.

## <a name="next-steps"></a>Następne kroki

- Poznaj podstawy [korelacji danych telemetrycznych](correlation.md) w usłudze Application Insights.
- Sprawdź, jak skorelowane dane [zasilają diagnostykę transakcji](../../azure-monitor/app/transaction-diagnostics.md) i [mapę aplikacji](../../azure-monitor/app/app-map.md).
- Zobacz [model danych](../../azure-monitor/app/data-model.md) dla typów usługi Application Insights i modelu danych.
- Zgłoś [niestandardowe zdarzenia i metryki](../../azure-monitor/app/api-custom-events-metrics.md) do usługi Application Insights.
- Sprawdź [standardową konfigurację](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) dla kolekcji właściwości kontekstu.
- Sprawdź [System.Diagnostics.Activity User Guide,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) aby zobaczyć, jak skorelować dane telemetryczne.
