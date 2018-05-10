---
title: Śledzenie działań niestandardowych z zestawu .NET SDK usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Śledzenie działań niestandardowych z zestawu .NET SDK usługi Azure Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/30/2017
ms.author: sergkanz;mbullwin
ms.openlocfilehash: e66d5e8674d6564d3910daddb1119c22a8b8bd9f
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Śledzenie działań niestandardowych z zestawu SDK .NET usługi Application Insights

Zestawy Azure SDK Insights aplikacji automatycznie śledzić przychodzących żądań HTTP i wywołania usługi zależne, takie jak żądań HTTP i zapytania SQL. Śledzenie i korelacja żądań i zależności zapewniają wgląd w całą aplikację elastyczność i niezawodność we wszystkich mikrousług, które łączą tę aplikację. 

Brak klasy wzorców aplikacji, których nie można używać objęty. Zapewnienia odpowiedniego monitorowania tych wzorców wymaga ręcznego kodu instrumentacji. W tym artykule opisano kilka wzorców, które mogą wymagać ręcznego instrumentacji, takie jak niestandardowe kolejki przetwarzania i uruchamianie długotrwałe zadania w tle.

Ten dokument zawiera wskazówki dotyczące sposobu śledzenia działań niestandardowych z zestawu SDK usługi Application Insights. Niniejsza dokumentacja jest odpowiednie dla:

- Application Insights dla platformy .NET (znanej także jako podstawowy zestaw SDK) w wersji 2.4 +.
- Application Insights dla usługi sieci web aplikacji (ASP.NET działających) wersji 2.4.
- Application Insights dla platformy ASP.NET Core w wersji 2.1 +.

## <a name="overview"></a>Przegląd
Operacja jest logiczną pracy wykonywane przez aplikację. Ma on nazwę, godzinę rozpoczęcia, czas trwania, wynik oraz kontekst wykonywania, takie jak nazwa użytkownika, właściwości i wyników. Jeśli operacja A została zainicjowana przez operację B, następnie operacji B ustawiono jako element nadrzędny a. Operacja może mieć tylko jeden obiekt nadrzędny, ale może mieć wiele działań podrzędnych. Aby uzyskać więcej informacji o operacjach i dane telemetryczne korelacji, zobacz [korelacji telemetrii usługi Application Insights Azure](application-insights-correlation.md).

W zestawie SDK .NET Application Insights operacji jest opisany przez klasy abstrakcyjnej [OperationTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) i jego elementy podrzędne [RequestTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) i [DependencyTelemetry](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Przychodzące operacji śledzenia 
Automatycznie zestawu SDK sieci web usługi Application Insights zbiera żądania HTTP dla aplikacji ASP.NET, które są uruchamiane w potoku usług IIS i wszystkich aplikacji platformy ASP.NET Core. Są obsługiwane przez społeczność rozwiązań dla innych platform i struktur. Jednak jeśli aplikacja nie jest obsługiwany przez żaden z rozwiązań standard lub obsługiwane społeczności, należy mogą dodawać go ręcznie.

Innym przykładem wymagającego niestandardowych śledzenia jest proces roboczy, który odbiera elementy z kolejki. W przypadku niektórych kolejek wywołanie do dodania do tej kolejki wiadomość jest śledzony jako zależność. Jednak operacja wysokiego poziomu, opisujący przetwarzanie komunikatu nie jest automatycznie zebrane.

Zobaczmy, jak można śledzić takie operacje.

Na wysokim poziomie zadania jest utworzenie `RequestTelemetry` i znanych właściwości. Po zakończeniu operacji śledzić telemetrii. W poniższym przykładzie pokazano to zadanie.

### <a name="http-request-in-owin-self-hosted-app"></a>Żądania HTTP w aplikacji siebie Owin
W tym przykładzie kontekst śledzenia jest propagowana zgodnie z [protokołu HTTP dla korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Należy oczekiwać na odbieranie nagłówków, których opisano.

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

Protokół HTTP dla korelacji również deklaruje `Correlation-Context` nagłówka. Jednak zostanie pominięty tutaj dla uproszczenia.

## <a name="queue-instrumentation"></a>Instrumentacja kolejki
Gdy istnieje [protokołu HTTP dla korelacji](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) do przekazania korelacji szczegóły żądania HTTP, każdego protokołu kolejki ma zdefiniuj, jak sam szczegółowe informacje są przekazywane komunikat z kolejki. Niektóre protokoły kolejki (na przykład protokołu AMQP) zezwala na przekazywanie dodatkowe metadane i inne (takie kolejki magazynu Azure) wymagają kontekstu, które mają być kodowane w ładunek komunikatu.

### <a name="service-bus-queue"></a>Kolejka usługi Service Bus
Usługa Application Insights śledzi wywołania usługi magistrali komunikatów z nowym [klienta magistrali usług Microsoft Azure dla platformy .NET](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) wersji 3.0.0 i wyższych.
Jeśli używasz [obsługi komunikatów](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) do przetwarzania komunikatów, wszystko będzie gotowe: wszystkie wywołania usługi Service Bus wykonywane przez usługę automatycznie są śledzone i powiązane z innych elementów telemetrii. Zapoznaj się [klienta usługi Service Bus śledzenie za pomocą usługi Microsoft Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md) Jeśli ręcznie przetwarzania komunikatów.

Jeśli używasz [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) pakietu, dalsze — przeczytaj następujące przykłady przedstawiają sposób śledzenia (i skorelowania) wywołuje do magistrali usługi jako kolejki usługi Service Bus używa protokołu AMQP, a nie usługi Application Insights automatyczne śledzenie operacji kolejki.
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

#### <a name="process"></a>Proces
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

### <a name="azure-storage-queue"></a>Kolejki magazynu systemu Azure
Poniższy przykład przedstawia sposób śledzenia [kolejki usługi Azure Storage](../storage/queues/storage-dotnet-how-to-use-queues.md) operacje oraz korelowanie telemetrii między producenta, użytkownika i usługi Azure Storage. 

Kolejki magazynu jest interfejs API protokołu HTTP. Wszystkie wywołania do kolejki są śledzone przez moduł zbierający Application Insights zależności żądań HTTP.
Upewnij się, że masz `Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer` w `applicationInsights.config`. Jeśli nie masz, dodaj ją programowo, zgodnie z opisem w [filtrowanie i przetwarzania wstępnego w zestawie SDK Azure Application Insights](app-insights-api-filtering-sampling.md).

Jeśli ręcznie skonfigurować usługi Application Insights, upewnij się, Utwórz i zainicjuj `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule` podobnie do:
 
```csharp
DependencyTrackingTelemetryModule module = new DependencyTrackingTelemetryModule();

// You can prevent correlation header injection to some domains by adding it to the excluded list.
// Make sure you add a Storage endpoint. Otherwise, you might experience request signature validation issues on the Storage service side.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
module.Initialize(TelemetryConfiguration.Active);

// Do not forget to dispose of the module during application shutdown.
```

Możesz też służące do skorelowania identyfikator operacji usługi Application Insights o identyfikatorze magazynu żądania. Aby uzyskać informacje na temat Ustawianie i pobieranie magazynu żądania klienta i identyfikator żądania serwera, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Azure Storage](../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Umieścić w kolejce
Ponieważ magazyn kolejek obsługuje API protokołu HTTP, wszystkie operacje z kolejką automatycznie są śledzone przez usługę Application Insights. W wielu przypadkach ta Instrumentacji powinny być wystarczające. Jednak aby korelowanie śladów po stronie klienta z producentów danych śledzenia, należy podać niektóre kontekstu korelacji podobnie jak możemy to zrobić w protokole HTTP dla korelacji. 

W tym przykładzie pokazano, jak śledzić `Enqueue` operacji. Możesz:

 - **Korelowanie ponownych prób (jeśli istnieją)**: wszystkie mają jeden wspólny nadrzędnego, który ma `Enqueue` operacji. W przeciwnym razie jest śledzony jako elementy podrzędne żądania przychodzącego. Jeśli istnieje wiele żądań logicznych do kolejki, może być trudne wywołania, które spowodowało ponownych prób.
 - **Skorelowania dzienników magazynu (Jeśli wymagane)**: są one powiązane z telemetrii usługi Application Insights.

`Enqueue` Operacji jest elementem podrzędnym działania nadrzędnego (na przykład przychodzące żądanie HTTP). Wywołanie zależności HTTP jest elementem podrzędnym `Enqueue` operacji i podwójnym przychodzącego żądania:

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

Aby zmniejszyć ilość danych telemetrycznych aplikacji raportów lub jeśli nie chcesz śledzić `Enqueue` operacji z innych przyczyn, użyj `Activity` bezpośrednio interfejsu API:

- Utwórz (i uruchom) nowy `Activity` zamiast uruchamiania operacji usługi Application Insights. Jak *nie* konieczne jest przypisanie żadnych właściwości na nim, oprócz nazwy operacji.
- Serializować `yourActivity.Id` do ładunku komunikatu zamiast `operation.Telemetry.Id`. Można również użyć `Activity.Current.Id`.


#### <a name="dequeue"></a>Usuwania z kolejki
Podobnie do `Enqueue`, rzeczywistego żądania HTTP do kolejki magazynu jest automatycznie śledzony przez usługę Application Insights. Jednak `Enqueue` operację prawdopodobnie dzieje się w kontekście nadrzędne, takie jak kontekst żądania przychodzące. Application Insights SDK automatycznie skorelowania takie działanie (i jego części HTTP) z żądania nadrzędnego i inne dane telemetryczne zgłoszone w tym samym zakresie.

`Dequeue` Operacja jest istotne znaczenie. Zestaw SDK usługi Application Insights automatyczne śledzenie żądań HTTP. Jednak go nie może ustalić kontekst korelacji dopóki przeanalizować komunikatu. Nie jest możliwe do skorelowania komunikat z resztą dane telemetryczne żądania HTTP.

W wielu przypadkach mogą być przydatne do skorelowania żądania HTTP do kolejki z również innych śladów. W poniższym przykładzie pokazano, jak to zrobić:

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

#### <a name="process"></a>Proces

W poniższym przykładzie wiadomości przychodzącej jest śledzony w sposób, podobnie do przychodzące żądanie HTTP:

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

Podobnie można instrumentowany innych operacji kolejki. Operacja peek powinien instrumentowany w podobny sposób jako operacja kolejki. Instrumentacja kolejki operacji zarządzania nie jest konieczne. Usługa Application Insights śledzi operacji, takich jak HTTP, i w większości przypadków jest wystarczające.

Gdy Instrumentacja usuwania komunikatów, upewnij się, że ustawisz operacji identyfikatorów (korelacji). Alternatywnie można użyć `Activity` interfejsu API. Nie należy ustawić identyfikatorów operacji na elementach telemetrii, ponieważ zestaw SDK usługi Application Insights jest dla Ciebie:

- Utwórz nową `Activity` po skonfigurowaniu elementu z kolejki.
- Użyj `Activity.SetParentId(message.ParentId)` służące do skorelowania dzienników klienta i producenta.
- Uruchom `Activity`.
- Śledź usuwania z kolejki, przetwarzania i operacji usunięcia przy użyciu `Start/StopOperation` pomocników. To robić z takim samym przepływie sterowania asynchroniczne (kontekstu wykonywania). W ten sposób ich są skorelowane poprawnie.
- Zatrzymaj `Activity`.
- Użyj `Start/StopOperation`, lub zadzwoń `Track` telemetrii ręcznie.

### <a name="batch-processing"></a>Przetwarzanie wsadowe
W przypadku niektórych kolejek można usuwania z kolejki wielu komunikatów z jednego żądania. Przetwarzanie takich wiadomości jest prawdopodobnie niezależna i należy do różnych operacji logicznej. W takim przypadku nie jest możliwe do skorelowania `Dequeue` na przetwarzanie komunikatów określonej operacji.

Każdy komunikat powinien zostać przetworzony w przepływ sterowania asynchronicznego. Aby uzyskać więcej informacji, zobacz [zależności wychodzące śledzenia](#outgoing-dependencies-tracking) sekcji.

## <a name="long-running-background-tasks"></a>Długotrwałe zadania w tle
Niektóre aplikacje uruchamiają długotrwałe operacje, które mogą być spowodowane przez żądania użytkownika. Z perspektywy śledzenie/Instrumentacja nie jest inny niż Instrumentacji żądania lub zależności: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<RequestTelemetry>(taskName);
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

W tym przykładzie `telemetryClient.StartOperation` tworzy `RequestTelemetry` i wypełnia kontekstu korelacji. Załóżmy, że masz operacji nadrzędnego, który został utworzony przez żądań przychodzących, które zaplanowanych operacji. Tak długo, jak `BackgroundTask` rozpoczyna się w tej samej asynchronicznej sterowania przepływem jako przychodzącego żądania, jest skorelowany z tym operacja nadrzędnej. `BackgroundTask` i wszystkie elementy zagnieżdżone dane telemetryczne są automatycznie skorelowane z żądania, który spowodował, nawet po zakończeniu żądania.

Po uruchomieniu zadania z wątku w tle, który nie ma żadnej operacji (`Activity`) skojarzone z nią `BackgroundTask` nie ma żadnych nadrzędnej. Jednak go można mieć zagnieżdżonych działań. Wszystkie elementy dane telemetryczne zgłoszone zadania są korelowane z `RequestTelemetry` utworzone w `BackgroundTask`.

## <a name="outgoing-dependencies-tracking"></a>Wychodzące zależności śledzenia
Możliwe jest śledzenie własnych rodzaj zależności lub operacja nie jest obsługiwana przez usługę Application Insights.

`Enqueue` Metody kolejki usługi Service Bus lub kolejek magazynu może służyć jako przykłady takich niestandardowych śledzenia.

Ogólne podejście do śledzenia niestandardowych zależności jest:

- Wywołanie `TelemetryClient.StartOperation` — metoda (rozszerzenie), które wypełnia `DependencyTelemetry` właściwości, które są potrzebne dla korelacji i niektórych innych właściwości (czas rozpoczęcia sygnatury, czas trwania).
- Ustaw właściwości niestandardowe na `DependencyTelemetry`, takie jak nazwa i innych kontekstu należy.
- Należy zależność wywołań i poczekaj na jej.
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

Operacji usuwania powoduje, że operacja ma zostać zatrzymany, więc można go wykonać zamiast wywoływać metodę `StopOperation`.

*Ostrzeżenie*: w niektórych przypadkach może wyjątek unhanded [zapobiec](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally` wywoływanie, nie mogą być śledzone operacji.

### <a name="parallel-operations-processing-and-tracking"></a>Równoległe przetwarzanie operacji i śledzenia

`StopOperation` tylko zatrzymuje operację, która została uruchomiona. Jeśli chcesz zatrzymać, nie jest zgodna bieżącej operacji uruchomione `StopOperation` nie działają. Taka sytuacja może się zdarzyć, uruchomić wiele operacji równolegle w tym samym kontekście wykonywania:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
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

Upewnij się, że wywoływanie zawsze `StartOperation` i przetwarzanie operacji w tym samym **async** metody do izolowania operacji uruchomione jednocześnie. Jeśli operacja jest synchroniczne (lub nie async), zawijać procesu i śledzenia z `Task.Run`:

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

- Poznaj podstawy z [korelacji telemetrii](application-insights-correlation.md) w usłudze Application Insights.
- Zobacz [modelu danych](application-insights-data-model.md) dla modelu danych i typów usługi Application Insights.
- Raport niestandardowy [zdarzenia i metryki](app-insights-api-custom-events-metrics.md) do usługi Application Insights.
- Zapoznaj się z standard [konfiguracji](app-insights-configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) dla zbioru właściwości kontekstu.
- Sprawdź [Podręcznik użytkownika System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) aby zobaczyć, jak firma Microsoft skorelowania danych telemetrycznych.
