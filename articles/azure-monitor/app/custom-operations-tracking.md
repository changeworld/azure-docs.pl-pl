---
title: Śledzenie operacji niestandardowych za pomocą zestawu SDK usługi Azure Application Insights platformy .NET | Dokumentacja firmy Microsoft
description: Śledzenie niestandardowych operacji przy użyciu zestawu SDK usługi Azure Application Insights platformy .NET
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/30/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: ae6e0e186f5cc0c9e3f0cd02d45d57c079eb3539
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995544"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Śledzenie operacji niestandardowych za pomocą zestawu SDK .NET usługi Application Insights

Zestawy Azure Application Insights SDK automatycznie śledzić przychodzących żądań HTTP i wywołań usługi zależne, takie jak żądania HTTP i zapytania SQL. Śledzenie i korelacja żądań i zależności dają wgląd w całą aplikację czas reakcji i niezawodności we wszystkich mikrousług, które łączą tę aplikację. 

Brak klasy wzorców aplikacji, które nie są obsługiwane w ogólnej. Właściwe monitorowanie tych wzorców wymaga Instrumentacji kodu. W tym artykule omówiono kilka wzorców, które mogą wymagać ręcznej instrumentacji, takie jak niestandardowe kolejki przetwarzania i uruchamiania długotrwałych zadań w tle.

Ten dokument zawiera wskazówki na temat sposobu śledzenie operacji niestandardowych za pomocą zestawu SDK usługi Application Insights. Ta dokumentacja dotyczy:

- Usługa Application Insights dla platformy .NET (znany także jako podstawowy zestaw SDK) w wersji 2.4 +.
- Usługa Application Insights dla sieci web działających (ASP.NET) wersji 2.4 +.
- Usługa Application Insights dla platformy ASP.NET Core w wersji 2.1 +.

## <a name="overview"></a>Omówienie
Operacja jest logiczną pracy wykonywane przez aplikację. Ta kolumna ma nazwę, godzinę rozpoczęcia, czas trwania, wynik oraz kontekstu wykonania, takie jak nazwa użytkownika, właściwości i wynik. Jeśli operacja A zostało zainicjowane przez operację B, następnie działanie B jest ustawiony jako element nadrzędny dla A. Operacja może mieć tylko jedną jednostkę nadrzędną, ale może mieć wiele operacji podrzędnych. Aby uzyskać więcej informacji na temat operacji i korelacja telemetrii, zobacz [korelacja telemetrii usługi Azure Application Insights](correlation.md).

W zestawie SDK platformy .NET Application Insights operacji jest opisana przez klasy abstrakcyjnej [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) i jego elementy potomne [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) i [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Przychodzące operacje śledzenia 
Zestaw SDK sieci web usługi Application Insights automatycznie zbiera żądania HTTP dla aplikacji ASP.NET, które są uruchamiane w potoku usługi IIS i wszystkie aplikacje platformy ASP.NET Core. Są obsługiwane przez społeczność rozwiązania dla innych platform i struktur. Jednakże jeśli aplikacja nie jest obsługiwana przez żaden z rozwiązań standardowe lub obsługiwane przez społeczność, możliwe jest instrumentowanie go ręcznie.

Inny przykład, która wymaga śledzenia niestandardowego to proces roboczy, który odbiera elementy z kolejki. W przypadku niektórych kolejek wywołanie do dodania komunikatu do tej kolejki jest śledzona jako zależność. Jednak operacji wysokiego poziomu, która opisuje przetwarzanie komunikatu nie są automatycznie zbierane.

Zobaczmy, jak takich operacji może być śledzone.

Na wysokim poziomie, zadanie jest utworzenie `RequestTelemetry` i ustaw znanych właściwości. Po zakończeniu tej operacji możesz śledzić dane telemetryczne. W poniższym przykładzie pokazano to zadanie.

### <a name="http-request-in-owin-self-hosted-app"></a>Żądania HTTP w aplikacji Self-Hosted Owin
W tym przykładzie kontekst śledzenia są propagowane zgodnie z opisem w [protokołu HTTP na potrzeby korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Należy się spodziewać otrzymywać nagłówki, które są opisane istnieje.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    private readonly TelemetryClient telemetryClient = new TelemetryClient(TelemetryConfiguration.Active);
    
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

Protokół HTTP dla korelacji deklaruje również `Correlation-Context` nagłówka. Jednak zostanie pominięty w tym miejscu dla uproszczenia.

## <a name="queue-instrumentation"></a>Instrumentacja kolejki
Choć [protokołu HTTP na potrzeby korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) do przekazania szczegóły korelacji z żądania HTTP, każdy protokół kolejki ma do zdefiniowania, jak ten sam szczegółowe informacje są przekazywane komunikatu w kolejce. Niektóre protokoły kolejki (na przykład protokół AMQP) zezwala na przekazywanie dodatkowych metadanych i inne (takie usługi Azure Storage Queue) wymaga kontekstu, który ma być zdekodowany do ładunku komunikatu.

### <a name="service-bus-queue"></a>Kolejka usługi Service Bus
Usługa Application Insights śledzi wywołania komunikatów usługi Service Bus przy użyciu nowego [klienta magistrali usług Microsoft Azure dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) wersji 3.0.0 lub nowszej.
Jeśli używasz [wzorzec procedury obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania komunikatów, wszystko będzie gotowe: wszystkie wywołania usługi Service Bus, wykonywane przez usługę są automatycznie śledzone i skorelowane z innych elementów telemetrii. Zapoznaj się [śledzenie za pomocą Microsoft Application Insights klienta usługi Service Bus](../../service-bus-messaging/service-bus-end-to-end-tracing.md) Jeśli ręcznie przetwarzać komunikatów.

Jeśli używasz [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) pakietu, zapoznaj się z — następujące przykłady przedstawiają sposób śledzenia (i korelowanie) wywołań do usługi Service Bus, kolejki usługi Service Bus używa protokołu AMQP, a nie usługi Application Insights automatyczne śledzenie operacji kolejki.
Identyfikatory korelacji są przekazywane we właściwościach komunikatu.

#### <a name="enqueue"></a>Umieścić w kolejce

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    operation.Telemetry.Type = "Queue";
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

#### <a name="process"></a>Przetwórz
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };

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

### <a name="azure-storage-queue"></a>Azure Storage queue
Poniższy przykład pokazuje, jak śledzić [kolejki usługi Azure Storage](../../storage/queues/storage-dotnet-how-to-use-queues.md) operacje i korelowanie danych telemetrycznych od producenta, klienta i usługi Azure Storage. 

Kolejka magazynu ma interfejsu API protokołu HTTP. Wszystkie wywołania do kolejki są śledzone przez moduł zbierający Application Insights zależności żądań HTTP.
Upewnij się, że masz `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` w `applicationInsights.config`. Jeśli nie masz, dodać je programowo, zgodnie z opisem w [filtrowanie i wstępne przetwarzanie w zestaw SDK usługi Azure Application Insights](../../azure-monitor/app/api-filtering-sampling.md).

Jeśli ręcznie skonfigurować usługi Application Insights, upewnij się, Utwórz i zainicjuj `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` podobnie do:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Możesz również chcieć skorelować identyfikator operacji usługi Application Insights za pomocą identyfikatora magazynu żądania. Aby uzyskać informacje na temat sposobu ustawiania i pobierania magazynu żądania klienta, jak i identyfikator żądania serwera, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z magazynem Azure](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Umieścić w kolejce
Ponieważ magazyn kolejek obsługuje interfejsu API protokołu HTTP, wszystkie operacje kolejki są automatycznie śledzone przez usługę Application Insights. W wielu przypadkach ta Instrumentacji powinny wystarczyć. Jednak do skorelowania ślady po stronie klienta za pomocą producenta ślady, musi przekazać określony kontekst korelacji podobnie jak możemy to zrobić za pomocą protokołu HTTP dla korelacji. 

W tym przykładzie pokazano, jak śledzić `Enqueue` operacji. Możesz:

 - **Korelowanie ponownych prób (jeśli istnieje)**: Wszystkie one mają jednej wspólnej nadrzędnego, który ma `Enqueue` operacji. W przeciwnym razie jest śledzona jako elementy podrzędne żądania przychodzącego. W przypadku wielu żądań logiczne do kolejki, może być trudne do znalezienia, która wywołuje metodę spowodowało ponownych prób.
 - **Korelowanie dzienniki magazynu (Jeśli wymagane)**: Są one powiązane z telemetrii usługi Application Insights.

`Enqueue` Operacji jest elementem podrzędnym elementu operacji nadrzędnej (na przykład z przychodzącym żądaniem HTTP). Wywołania zależności HTTP jest elementem podrzędnym elementu `Enqueue` operacji i podwójnym postać przychodzącego żądania:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Queue";
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

Aby zmniejszyć ilość danych telemetrycznych aplikacji raportów lub jeśli nie chcesz śledzić `Enqueue` operacji z innych przyczyn, użyj `Activity` API bezpośrednio:

- Tworzenie (i uruchamianie) nową `Activity` zamiast uruchamiania operacji usługi Application Insights. Możesz zrobić *nie* trzeba przypisać wszystkie właściwości w nim, z wyjątkiem nazwy operacji.
- Serializowanie `yourActivity.Id` do ładunku komunikatu zamiast `operation.Telemetry.Id`. Można również użyć `Activity.Current.Id`.


#### <a name="dequeue"></a>Usuń z kolejki
Podobnie jak `Enqueue`, rzeczywistego żądania HTTP do kolejki usługi Storage są automatycznie śledzone przez usługę Application Insights. Jednak `Enqueue` Operacja prawdopodobnie odbywa się w kontekście nadrzędny, np. przychodzące kontekst żądania. Zestawy SDK usługi Application Insights automatycznie skorelować taka operacja (i jej części HTTP) z żądania nadrzędnego i inne dane telemetryczne zgłoszone w tym samym zakresie.

`Dequeue` Operacja jest trudne. Zestaw SDK usługi Application Insights automatycznie śledzi żądania HTTP. Jednak nie ma informacji kontekstu korelacji, dopóki nie zostanie przeanalizowany komunikat. Nie jest możliwe do skorelowania żądania HTTP, jest wyświetlany komunikat z pozostałą częścią danych telemetrycznych.

W wielu przypadkach może być przydatne do skorelowania żądania HTTP do kolejki za pomocą innych śladów, jak również. Poniższy przykład pokazuje, jak to zrobić:

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var telemetry = new DependencyTelemetry
    {
        Type = "Queue",
        Name = "Dequeue " + queue.Name
    };

    telemetry.Start();

    try
    {
        var message = await queue.GetMessageAsync();

        if (message != null)
        {
            var payload = JsonConvert.DeserializeObject<MessagePayload>(message.AsString);

            // If there is a message, we want to correlate the Dequeue operation with processing.
            // However, we will only know what correlation ID to use after we get it from the message,
            // so we will report telemetry after we know the IDs.
            telemetry.Context.Operation.Id = payload.RootId;
            telemetry.Context.Operation.ParentId = payload.ParentId;

            // Delete the message.
            return payload;
        }
    }
    catch (StorageException e)
    {
        telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        telemetry.Success = false;
        telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetry.Stop();
        telemetryClient.Track(telemetry);
    }

    return null;
}
```

#### <a name="process"></a>Przetwórz

W poniższym przykładzie wiadomości przychodzących jest śledzona w sposób podobny sposób na przychodzące żądania HTTP:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "Dequeue " + queueName };
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

Podobnie inne operacje kolejkowania można go zinstrumentować. Operacja podglądu powinien Instrumentacji w podobny sposób, jako operacja usuwania z kolejki. Instrumentacja kolejki operacje zarządzania nie jest konieczne. Usługa Application Insights śledzi operacje, takie jak HTTP i w większości przypadków jest wystarczająca.

Podczas usuwania komunikatów instrumentacji, upewnij się, że ustawisz operacji identyfikatorów (korelacja). Alternatywnie, można użyć `Activity` interfejsu API. Nie musisz ustawić identyfikatory operacji na elementach danych telemetrycznych, ponieważ zestaw SDK usługi Application Insights zrobi to za Ciebie:

- Utwórz nową `Activity` po skonfigurowaniu elementu z kolejki.
- Użyj `Activity.SetParentId(message.ParentId)` do skorelowania dzienników konsumentów i producentów.
- Rozpocznij `Activity`.
- Śledź usuwania z kolejki, przetwarzania i operacje usuwania przy użyciu `Start/StopOperation` pomocników. To robić z tego samego asynchronicznego przepływu sterowania (kontekstu wykonania). W ten sposób są one skorelowane prawidłowo.
- Zatrzymaj `Activity`.
- Użyj `Start/StopOperation`, lub zadzwoń `Track` telemetrii ręcznie.

### <a name="batch-processing"></a>Przetwarzanie wsadowe
W przypadku niektórych kolejek można kolejki wielu komunikatów za pomocą jednego żądania. Przetwarzanie takich komunikatów jest prawdopodobnie niezależna i należy do różnych operacji logicznej. W tym przypadku nie jest możliwe do skorelowania `Dequeue` na przetwarzanie komunikatów określonej operacji.

Każdy komunikat powinien zostać przetworzony w swój własny przepływ sterowania asynchronicznego. Aby uzyskać więcej informacji, zobacz [wychodzące zależności śledzenia](#outgoing-dependencies-tracking) sekcji.

## <a name="long-running-background-tasks"></a>Długotrwałe zadania w tle

Niektóre aplikacje uruchomić operacji długotrwałych, które mogą być spowodowane przez żądania użytkownika. Z punktu widzenia śledzenia/Instrumentacji nie jest inny niż Instrumentacji żądania lub zależności: 

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

W tym przykładzie `telemetryClient.StartOperation` tworzy `DependencyTelemetry` i wypełnień kontekstu korelacji. Załóżmy, że masz operacji nadrzędnej, która została utworzona przez żądań przychodzących, które zaplanowanych operacji. Tak długo, jak `BackgroundTask` rozpoczyna się w tym samym asynchronicznego sterowania przepływem jako przychodzącego żądania, jest skorelowana z tym operacja nadrzędnej. `BackgroundTask` i wszystkie elementy zagnieżdżone telemetrii automatycznie są powiązane z tym żądaniem, który spowodował nieoczekiwane, nawet po zakończeniu żądania.

Podczas uruchamiania zadania z wątku w tle, który nie ma żadnych operacji (`Activity`) skojarzony z nim, `BackgroundTask` nie ma żadnych nadrzędnej. Jednak go można zagnieżdżać operacji. Wszystkie elementy dane telemetryczne zgłoszone zadanie skorelowanych z `DependencyTelemetry` utworzone w `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Wychodzące zależności śledzenia
Możesz śledzić własne rodzaju zależności lub operacją, która nie jest obsługiwana przez usługę Application Insights.

`Enqueue` Metody w kolejce usługi Service Bus lub kolejki magazynu może służyć jako przykłady takich niestandardowe śledzenia.

Ogólne podejście do śledzenia zależności niestandardowej jest:

- Wywołaj `TelemetryClient.StartOperation` — metoda (rozszerzenia), który wypełnia `DependencyTelemetry` właściwości, które są wymagane do korelacji i niektóre inne właściwości (czas rozpoczęcia sygnatury, czas trwania).
- Ustaw właściwości niestandardowe na `DependencyTelemetry`, takie jak nazwa i wszystkich innych kontekstach, które należy.
- Należy zależności wywołań i poczekaj na jej.
- Zatrzymaj operację, podając `StopOperation` po zakończeniu.
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

Operacja usuwania powoduje, że operacja ma zostać zatrzymany, więc może zrobić, zamiast wywoływać metodę `StopOperation`.

*Ostrzeżenie*: w niektórych przypadkach może być unhanded wyjątek [zapobiec](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` wywołane, dzięki czemu operacje nie mogą być śledzone.

### <a name="parallel-operations-processing-and-tracking"></a>Równoległe przetwarzanie operacji i śledzenie

`StopOperation` tylko zatrzymuje operację, która została uruchomiona. Jeśli bieżąca operacja uruchomionej nie pasować do którego ma zostać zatrzymane, `StopOperation` nic nie robi. Taka sytuacja może się zdarzyć, jeśli uruchamia się wiele operacji równolegle w tym samym kontekście wykonania:

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

Upewnij się, że zawsze wywołuj `StartOperation` i przetwarzanie operacji w tym samym **async** metodę, aby odizolować działań uruchamianych równolegle. W przypadku operacji synchronicznych (lub nie async), proces opakowywania i śledzenie za pomocą `Task.Run`:

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

## <a name="next-steps"></a>Kolejne kroki

- Poznaj podstawy [korelacja telemetrii](correlation.md) w usłudze Application Insights.
- Zobacz [modelu danych](../../azure-monitor/app/data-model.md) dla usługi Application Insights typów i danych modelu.
- Raport niestandardowy [zdarzeń i metryk](../../azure-monitor/app/api-custom-events-metrics.md) do usługi Application Insights.
- Zapoznaj się z standard [konfiguracji](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) dla kontekstu właściwości kolekcji.
- Sprawdź [Podręcznik użytkownika System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) aby zobaczyć, jak firma Microsoft korelowanie danych telemetrycznych.
