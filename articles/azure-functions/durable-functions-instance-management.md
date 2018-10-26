---
title: Zarządzanie wystąpieniami w funkcje trwałe - Azure
description: Informacje o sposobie zarządzania jego wystąpieniami w rozszerzenia funkcji trwałych dla usługi Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: azfuncdf
ms.openlocfilehash: bcb87b3030eb673dd3c9a8b93a045c75fae964a9
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087036"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Zarządzanie wystąpieniami w funkcje trwałe (usługi Azure Functions)

[Trwałe funkcje](durable-functions-overview.md) aranżacji wystąpień może być pracę, zakończone, zapytań i wysyłane zdarzenia powiadomień. Wszystkie wystąpienia Zarządzanie odbywa się przy użyciu [klient orkiestracji powiązanie](durable-functions-bindings.md). W tym artykule przechodzi do szczegółów każdej operacji zarządzania wystąpienia.

## <a name="starting-instances"></a>Uruchamianie wystąpienia

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) uruchamia nowe wystąpienie funkcji orkiestratora. Wystąpienia tej klasy można kupić przy użyciu `orchestrationClient` powiązania. Wewnętrznie ta metoda umieszczeniu komunikat do kolejki kontroli, a następnie uruchamiają początku funkcji o podanej nazwie, która używa `orchestrationTrigger` wyzwolić powiązania. 

Zadanie kończy się po uruchomieniu procesu aranżacji. Na początek procesu aranżacji w ciągu 30 sekund. Jeśli zajmuje więcej czasu, `TimeoutException` zgłaszany. 

Parametry [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) są następujące:

* **Nazwa**: Nazwa funkcji programu orchestrator do zaplanowania.
* **Wejściowy**: wszystkie dane możliwe do serializacji JSON powinien być przekazywany jako dane wejściowe do funkcji programu orchestrator.
* **Identyfikator InstanceId**: (opcjonalnie) to unikatowy identyfikator wystąpienia. Jeśli nie zostanie określony, zostanie wygenerowany identyfikator wystąpienia losowej.

Poniżej przedstawiono prosty przykład C#:

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

W przypadku języków — .NET, powiązanie w danych wyjściowych funkcji można uruchomić także nowe wystąpienia. W takim przypadku można dowolnego obiektu podlegającego serializacji JSON, z trzech powyższych parametrów jako pola. Na przykład rozważmy następującą funkcję języka JavaScript:

```js
module.exports = function (context, input) {
    var id = generateSomeUniqueId();
    context.bindings.starter = [{
        FunctionName: "HelloWorld",
        Input: input,
        InstanceId: id
    }];

    context.done(null);
};
```
Powyższy kod zakłada, że w pliku function.json zdefiniowane poza powiązanie o nazwie jako "starter" i typ jako "orchestrationClient". Jeśli powiązanie nie jest zdefiniowany, wystąpienie trwałe funkcji nie można utworzyć.

Trwałe funkcji do wywołania function.json należy zmodyfikować, aby mieć powiązania dla klienta aranżacji, zgodnie z poniższym opisem

```js
{
    "bindings": [{
        "name":"starter",
        "type":"orchestrationClient",
        "direction":"out"
    }]
}
```

> [!NOTE]
> Zalecamy użycie losowy identyfikator identyfikatora wystąpienia. Ułatwi to zapewnienie dystrybucji obciążenia z działaniem równe, podczas skalowania funkcje programu orchestrator na wielu maszynach wirtualnych. Prawidłowego czasu, aby korzystać identyfikatorów wystąpień nielosową jest identyfikator musi pochodzić ze źródła zewnętrznego lub podczas implementowania [programu orchestrator z pojedynczego wystąpienia](durable-functions-singletons.md) wzorca.

## <a name="querying-instances"></a>Tworzenie zapytań wystąpień

[GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy zapytanie o stan wystąpienia aranżacji. Trwa `instanceId` (wymagane) i `showHistory` (opcjonalnie) i `showHistoryOutput` (opcjonalnie) jako parametry. Jeśli `showHistory` ustawiono `true`, odpowiedź będzie zawierać historii wykonywania. Jeśli `showHistoryOutput` ustawiono `true` również historię wykonywania będzie zawierać danych wyjściowych działania. Metoda ta zwraca obiekt z następującymi właściwościami:

* **Nazwa**: Nazwa funkcji programu orchestrator.
* **Identyfikator InstanceId**: identyfikator wystąpienia aranżacji (powinna być taka sama jak `instanceId` wejściowego).
* **Wartością CreatedTime**: czasu, jaką funkcja orkiestratora został uruchomiony.
* **LastUpdatedTime**: czas, w którym aranżacji ostatni utworzono punkt kontrolny.
* **Wejściowy**: dane wejściowe funkcji jako wartości JSON.
* **CustomStatus**: niestandardowy stan aranżacji w formacie JSON. 
* **Dane wyjściowe**: dane wyjściowe funkcji jako wartości JSON (Jeśli funkcja została ukończona). Funkcja programu orchestrator nie powiodło się, ta właściwość będzie zawierać szczegóły błędu. Jeśli funkcja orkiestratora został zakończony, ta właściwość będzie zawierać podana Przyczyna zakończenia (jeśli istnieje).
* **RuntimeStatus**: jeden z następujących wartości:
    * **Oczekujące**: wystąpienie zostało zaplanowane, ale nie zostało jeszcze uruchomione uruchomiona.
    * **Uruchamianie**: wystąpienie rozpoczęło działanie.
    * **Ukończono**: wystąpienie zostało zakończone normalnie.
    * **ContinuedAsNew**: wystąpienie ponownym uruchomieniu się z historią nowe. Jest to stan przejściowy.
    * **Nie powiodło się**: wystąpienie nie powiodło się z powodu błędu.
    * **Zakończony**: wystąpienie zostało nagle przerwane.
* **Historia**: historię wykonywania aranżacji. To pole jest wypełniane tylko, jeśli `showHistory` ustawiono `true`.
    
Ta metoda zwraca `null` Jeśli wystąpienie nie istnieje lub nie zostało jeszcze uruchomione uruchomiona.

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```
## <a name="querying-all-instances"></a>Wykonywanie zapytań, wszystkie wystąpienia

Możesz użyć `GetStatusAsync` metodę, aby zbadać stanami wszystkich wystąpień aranżacji. Go nie przyjmuje żadnych parametrów, lub możesz przekazać `CancellationToken` obiektu w przypadku, gdy chcesz anulować. Metoda ta zwraca obiekty z tymi samymi właściwościami co `GetStatusAsync` metody z parametrami, z wyjątkiem sytuacji kiedy nie zwraca historii. 

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```
## <a name="querying-instances-with-filters"></a>Wystąpienia zapytań z filtrami

Można również użyć `GetStatusAsync` metodę, aby uzyskać listę wystąpień aranżacji, które pasują zestaw wstępnie zdefiniowanych filtrów. Opcje filtru można zawierają czas tworzenia aranżacji i stan czasu wykonywania aranżacji.

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

## <a name="terminating-instances"></a>Trwa przerywanie działania wystąpień

Uruchomione wystąpienie orchestration można przerywać działanie przy użyciu [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy. Są dwa parametry `instanceId` i `reason` parametry, które będą zapisywane w dziennikach i stan wystąpienia. Wystąpienie zakończone zatrzyma się zaraz po osiągnięciu następnego `await` punktu lub jest on utracą ważność natychmiast, jeśli jest już włączone `await`. 

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

> [!NOTE]
> Zakończenie wystąpienia nie obecnie propaguje. Działanie funkcji i mechanizmów podrzędne będą uruchamiane do zakończenia, niezależnie od tego, czy wystąpienie aranżacji, które je wywołuje zostało zakończone.

## <a name="sending-events-to-instances"></a>Wysyłanie zdarzeń do wystąpień

Powiadomienia dotyczące zdarzenia mogą być wysyłane do uruchamiania wystąpień przy użyciu [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy. Wystąpienia, obsługujące te zdarzenia są te, które oczekują na wywołanie [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_). 

Parametry [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) są następujące:

* **Identyfikator InstanceId**: Unikatowy identyfikator wystąpienia.
* **EventName**: Nazwa zdarzenia do wysłania.
* **EventData**: ładunek do serializacji JSON do wysłania do wystąpienia.

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

> [!WARNING]
> Jeśli żadne wystąpienie aranżacji z określonym *identyfikator wystąpienia* lub jeśli wystąpienie nie oczekuje na określonym *Nazwa zdarzenia*, komunikat o zdarzeniu zostanie odrzucony. Aby uzyskać więcej informacji na temat tego zachowania, zobacz [problem w usłudze GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="wait-for-orchestration-completion"></a>Czekaj na ukończenie aranżacji

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klasy ujawnia [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) interfejsu API, którego można uzyskać synchronicznie rzeczywiste dane wyjściowe z wystąpienia aranżacji. Metoda używa domyślnie wynosi 10 sekund na `timeout` i 1 sekundę `retryInterval` gdy nie są ustawione.  

Oto przykład funkcji wyzwalacza HTTP, który pokazuje, jak używać tego interfejsu API:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

Funkcja może zostać wywołana w następującym wierszem przy użyciu 2 sekund limitu czasu i interwału ponawiania 0,5 sekundy:

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

Zależnie od godziny, do uzyskania odpowiedzi z wystąpienia aranżacji wymagane są dwa przypadki:

1. Wystąpienia aranżacji zakończyć w ciągu zdefiniowanego limitu czasu (w tym przypadku 2 sekundy), odpowiedź aranżacji rzeczywistego wystąpienia przekazywane są dostarczane synchronicznie:

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

2. Wystąpienia orchestration nie może zakończyć w ciągu zdefiniowanego limitu czasu (w tym przypadku 2 sekund), odpowiedź jest domyślna jednego opisanego w **Odnajdywanie adresu URL interfejsu API HTTP**:

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> Format adresu URL elementu webhook mogą się różnić w zależności od posiadanej wersji programu host usługi Azure Functions. Powyższy przykład dotyczy dla hosta usługi Azure Functions w wersji 2.0.

## <a name="retrieving-http-management-webhook-urls"></a>Trwa pobieranie adresów URL elementu Webhook zarządzania HTTP

Systemy zewnętrzne mogą się komunikować trwałe funkcje za pomocą adresów URL elementu webhook, będące częścią odpowiedzi domyślnej opisanego w [Odnajdywanie adresu URL interfejsu API HTTP](durable-functions-http-api.md). Jednak adresy URL elementu webhook również można uzyskać programistycznie w klient orkiestracji lub w funkcji działania za pośrednictwem [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) metody [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html)klasy. 

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) ma jeden parametr:

* **Identyfikator InstanceId**: Unikatowy identyfikator wystąpienia.

Metoda ta zwraca wystąpienie [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) z następującymi właściwościami ciąg:

* **Identyfikator**: identyfikator wystąpienia aranżacji (powinna być taka sama jak `InstanceId` wejściowego).
* **StatusQueryGetUri**: adres URL stanu wystąpienia aranżacji.
* **SendEventPostUri**: adres URL "Zgłoś zdarzenie" wystąpienia aranżacji.
* **TerminatePostUri**: adres URL wystąpienia aranżacji "terminate".
* **RewindPostUri**: adres URL "przewijanie" wystąpienia aranżacji.

Działanie funkcji można wysłać wystąpienia [HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) z systemami zewnętrznymi, do monitorowania lub wywoływać zdarzenia do organizowania:

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

## <a name="rewinding-instances-preview"></a>Przewijanie instances (wersja zapoznawcza)

Wystąpienie aranżacji zakończonych niepowodzeniem może być *przewinięta* w uprzednio dobrej kondycji, używając [RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) interfejsu API. To działa, umieszczając aranżacji do *systemem* stanu i ponowne uruchomienie działania i/lub podrzędną aranżacji błędy wykonania, które spowodowały błąd aranżacji.

> [!NOTE]
> Ten interfejs API nie jest przeznaczony do zastąpienia obsługi błędów właściwe i zasad ponawiania. Przeciwnie jest przeznaczona do użycia tylko w przypadkach, gdzie wystąpień aranżacji się nie powieść nieoczekiwany powodów. Aby uzyskać szczegółowe informacje na temat zasad ponawiania prób i obsługi błędów, zobacz [obsługi błędów](durable-functions-error-handling.md) tematu.

Przykładem użycia w przypadku *rewind* jest przepływ pracy obejmujące serię [ludzi zatwierdzenia](durable-functions-overview.md#pattern-5-human-interaction). Załóżmy, że istnieje szereg funkcji działań, które powiadamiają o kogoś, że zatwierdzenie jest wymagane i zaczekaj na poziomie odpowiedzi w czasie rzeczywistym. Gdy wszystkie zatwierdzenia działań otrzymane odpowiedzi lub przekroczyło limit czasu, innego działania zakończy się niepowodzeniem z powodu błędnej konfiguracji aplikacji (np. nieprawidłowe parametry połączenia bazy danych). Wynikiem jest błąd aranżacji szczegółowo przepływ pracy. Za pomocą `RewindAsync` interfejsu API, administrator aplikacji może naprawić ten błąd konfiguracji i *rewind* orchestration nie powiodło się z powrotem do stanu bezpośrednio przed awarią. Żaden z kroków interakcji człowieka muszą być ponownie zatwierdzone i organizowania może teraz zostać pomyślnie ukończony.

> [!NOTE]
> *Rewind* funkcja nie obsługuje przetwarzania aranżacji wystąpieniom używaj czasomierzy trwałe.

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać interfejsów API protokołu HTTP dla wystąpienia zarządzania](durable-functions-http-api.md)
