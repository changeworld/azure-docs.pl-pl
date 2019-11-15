---
title: Usługa Azure powiązania usługi Service Bus dla usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Service Bus wyzwalaczy i powiązań w usłudze Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.service: azure-functions
ms.topic: reference
ms.date: 04/01/2017
ms.author: cshoe
ms.openlocfilehash: cf78712515ab91c66161d04dac0df601c5dcb625
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082783"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Usługa Azure powiązania usługi Service Bus dla usługi Azure Functions

W tym artykule wyjaśniono, jak pracować z usługą Azure Service Bus powiązań w usłudze Azure Functions. Usługi Azure Functions obsługuje wyzwalanie i danych wyjściowych powiązania z tematów i kolejek usługi Service Bus.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania usługi Service Bus znajdują się w [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) pakietu NuGet w wersji 2.x. 

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania usługi Service Bus znajdują się w [Microsoft.Azure.WebJobs.Extensions.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus) pakietu NuGet w wersji 3.x. Kod źródłowy pakietu znajduje się w repozytorium [Azure-Functions-ServiceBus-Extension](https://github.com/Azure/azure-functions-servicebus-extension) GitHub.

> [!NOTE]
> Wersja 2. x nie tworzy tematu ani subskrypcji skonfigurowanej w wystąpieniu `ServiceBusTrigger`. Wersja 2. x jest oparta na [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) i nie obsługuje zarządzania kolejki.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Wyzwalacz

Użyj wyzwalacza usługi Service Bus, aby odpowiadać na komunikaty z kolejki usługi Service Bus lub temat. 

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-example)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , odczytuje [metadanych komunikat](#trigger---message-metadata) i rejestruje komunikatu w kolejce usługi Service Bus:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie pokazano wyzwalaczem usługi Service Bus, powiązanie w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja odczytuje [metadanych komunikat](#trigger---message-metadata) i rejestruje komunikatu w kolejce usługi Service Bus.

Oto powiązanie danych w *function.json* pliku:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Poniżej przedstawiono kod skryptu języka C#:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

### <a name="trigger---f-example"></a>Wyzwalacz - F# przykład

W poniższym przykładzie pokazano wyzwalaczem usługi Service Bus, powiązanie w *function.json* pliku i [ F# funkcja](functions-reference-fsharp.md) powiązania, który używa. Funkcja rejestruje komunikatu w kolejce usługi Service Bus. 

Oto powiązanie danych w *function.json* pliku:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Oto F# kod skryptu:

```fsharp
let Run(myQueueItem: string, log: ILogger) =
    log.LogInformation(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

Następująca funkcja języka Java używa adnotacji `@ServiceBusQueueTrigger` z [biblioteki środowiska uruchomieniowego usługi Java Functions](/java/api/overview/azure/functions/runtime) do opisywania konfiguracji dla wyzwalacza Service Bus kolejki. Funkcja poniesie komunikat umieszczony w kolejce i dodaje ją do dzienników.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Funkcje języka Java mogą być również wyzwalane, gdy komunikat zostanie dodany do tematu Service Bus. Poniższy przykład używa adnotacji `@ServiceBusTopicTrigger`, aby opisać konfigurację wyzwalacza.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano wyzwalaczem usługi Service Bus, powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja odczytuje [metadanych komunikat](#trigger---message-metadata) i rejestruje komunikatu w kolejce usługi Service Bus. 

Oto powiązanie danych w *function.json* pliku:

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Poniżej przedstawiono kod skryptu języka JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

### <a name="trigger---python-example"></a>Wyzwalacz — przykład w języku Python

Poniższy przykład ilustruje sposób odczytywania komunikatu kolejki ServiceBus za pośrednictwem wyzwalacza.

Powiązanie ServiceBus jest zdefiniowane w *funkcji Function. JSON* , w której *typ* ma wartość `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

Kod w  *_\_init_\_. PR* deklaruje parametr jako `func.ServiceBusMessage`, który umożliwia odczytywanie komunikatu w kolejce w funkcji.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyć następujących atrybutów, aby skonfigurować wyzwalacz usługi Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atrybutu ma nazwę kolejki lub tematu i subskrypcji. W usłudze Azure Functions w wersji 1.x, można również określić, że prawa dostępu do tego połączenia. Jeśli nie określisz prawa dostępu, wartością domyślną jest `Manage`. Aby uzyskać więcej informacji, zobacz [wyzwalacza — Konfiguracja](#trigger---configuration) sekcji.

  Oto przykład pokazujący atrybut używany z parametrem ciągu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Możesz ustawić `Connection` właściwości w celu określenia konta usługi Service Bus do użycia, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Udostępnia inny sposób, aby określić konto usługi Service Bus do użycia. Konstruktor przyjmuje nazwę ustawienia aplikacji zawierającego parametry połączenia usługi Service Bus. Ten atrybut można zastosować na parametrów, metody lub poziomie klasy. Poniższy przykład pokazuje poziom klasy i metody:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

Konto usługi Service Bus do użycia, jest określana w następującej kolejności:

* `ServiceBusTrigger` Atrybutu `Connection` właściwości.
* `ServiceBusAccount` Zastosowany do tego samego parametru jako `ServiceBusTrigger` atrybutu.
* `ServiceBusAccount` Zastosowany do funkcji.
* `ServiceBusAccount` Zastosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsServiceBus".

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `ServiceBusTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "serviceBusTrigger". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa "in". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**name** | Nie dotyczy | Nazwa zmiennej, która reprezentuje komunikat kolejki lub tematu w kodzie funkcji. Należy ustawić "$return", aby odwoływać się do wartości zwracanej funkcji. |
|**queueName**|**QueueName**|Nazwa kolejki do monitorowania.  Ustaw tylko wtedy, gdy kolejki, tematu dla monitorowania.
|**topicName**|**topicName**|Nazwa tematu do monitorowania. Ustaw tylko wtedy, gdy monitorowanie tematu, nie dla kolejki.|
|**subscriptionName**|**subscriptionName**|Nazwa subskrypcji do monitorowania. Ustaw tylko wtedy, gdy monitorowanie tematu, nie dla kolejki.|
|**połączenia**|**połączenia**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Service Bus do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy. Na przykład jeśli ustawisz `connection` do "MyServiceBus", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyServiceBus." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia usługi Service Bus w ustawieniu aplikacji, który nosi nazwę "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj kroki opisane w sekcji [pobieranie poświadczeń zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą mieć dla przestrzeni nazw usługi Service Bus, nie są ograniczone do określonej kolejki lub tematu. |
|**accessRights**|**Dostęp**|Prawa dostępu parametrów połączenia. Dostępne wartości to `manage` i `listen`. Wartość domyślna to `manage`, który wskazuje, że `connection` ma **Zarządzaj** uprawnień. Jeśli używasz parametry połączenia, który nie ma **Zarządzaj** zestawu uprawnień, `accessRights` "nasłuchiwanie". W przeciwnym razie funkcje środowiska uruchomieniowego może zakończyć się niepowodzeniem próby wykonania operacji wymagających zarządzania prawami. W usłudze Azure Functions w wersji 2.x, ta właściwość nie jest dostępna, ponieważ najnowszą wersję zestawu SDK usługi Storage nie obsługuje zarządzania operacjami.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W języku C# i skrypt języka C# można użyć następujących typów parametrów dla kolejki lub tematu wiadomości:

* `string` -Jeśli ten komunikat jest tekst.
* `byte[]` -Przydatne dla danych binarnych.
* Niestandardowy typ — Jeśli wiadomość zawiera JSON, usługi Azure Functions próbuje deserializowanie danych JSON.
* `BrokeredMessage` — zawiera deserializowany komunikat z metodą [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Parametry te dotyczą usługi Azure Functions w wersji 1.x; w przypadku 2.x, użyj [ `Message` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) zamiast `BrokeredMessage`.

W języku JavaScript, dostęp do kolejki lub tematu wiadomości przy użyciu `context.bindings.<name from function.json>`. Komunikat usługi Service Bus jest przekazywany do funkcji jako ciąg lub obiekt JSON.

## <a name="trigger---poison-messages"></a>Wyzwalacz - skażone wiadomości

Obsługa skażonych komunikatów nie kontrolowany lub skonfigurowane w usłudze Azure Functions. Usługa Service Bus obsługuje komunikaty zanieczyszczone sam.

## <a name="trigger---peeklock-behavior"></a>Wyzwalacz - PeekLock zachowanie

Środowisko uruchomieniowe usługi Functions odbiera komunikat w [tryb PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Wywołuje `Complete` na komunikat, jeśli funkcja kończy się pomyślnie lub wywołań `Abandon` Jeśli funkcja kończy się niepowodzeniem. Jeśli funkcja działa dłużej niż `PeekLock` tak długo, jak działa funkcja limitu czasu blokady będzie automatycznie odnawiana. 

`maxAutoRenewDuration` można skonfigurować w pliku *host. JSON*, który jest mapowany na [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maksymalny dozwolony dla tego ustawienia jest 5 minut zgodnie z dokumentacją usługi Service Bus, podczas gdy można zwiększyć limit czasu funkcji z domyślnej wartości 5 minut do 10 minut. Dla funkcji usługi Service Bus w takich sytuacjach przydałaby chcesz to zrobić w efekcie ponieważ przekroczyłby limit odnawiania usługi Service Bus.

## <a name="trigger---message-metadata"></a>Wyzwalacz — komunikat metadanych

Wyzwalacz usługi Service Bus udostępnia wiele [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Są to właściwości [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Liczba dostarczeń.|
|`DeadLetterSource`|`string`|Źródło utraconych wiadomości.|
|`ExpiresAtUtc`|`DateTime`|Czas wygaśnięcia w formacie UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Godzina umieszczonych w kolejce w formacie UTC.|
|`MessageId`|`string`|Wartość zdefiniowana przez użytkownika magistrala usług może używać do identyfikowania zduplikowanych komunikatów, jeśli włączona.|
|`ContentType`|`string`|Identyfikator typu zawartości, wykorzystywany przez nadawcę i odbiorcę dla logikę specyficzną dla aplikacji.|
|`ReplyTo`|`string`|Odpowiedzi na adres kolejki.|
|`SequenceNumber`|`Int64`|Unikatowy numer przypisany do komunikatu przez magistralę usług.|
|`To`|`string`|Wyślij adres.|
|`Label`|`string`|Etykieta specyficzna dla aplikacji.|
|`CorrelationId`|`string`|Identyfikator korelacji.|

> [!NOTE]
> Obecnie wyzwalacz usługi Service Bus działający z kolejkami i subskrypcjami z włączoną obsługą sesji jest dostępny w wersji zapoznawczej. Śledź [ten element](https://github.com/Azure/azure-webjobs-sdk/issues/529#issuecomment-491113458) , aby znaleźć wszelkie dalsze aktualizacje dotyczące tego elementu. 

Zobacz [przykłady kodu](#trigger---example) używające tych właściwości we wcześniejszej części tego artykułu.

## <a name="output"></a>Dane wyjściowe

Użyj powiązania danych wyjściowych usługi Azure Service Bus, aby wysyłać komunikaty do kolejki lub tematu.

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [F#](#output---f-example)
* [Java](#output---java-example)
* [JavaScript](#output---javascript-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) , wysyła komunikat z kolejki usługi Service Bus:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

Poniższy przykład pokazuje usługi Service Bus powiązania danych wyjściowych usługi w *function.json* pliku i [funkcji skryptu w języku C#](functions-reference-csharp.md) powiązania, który używa. Funkcja korzysta z wyzwalacza czasomierza, aby wysłać komunikatu w kolejce, co 15 sekund.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Oto C# script kod, który tworzy pojedynczy komunikat:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

W tym C# script kod, który tworzy wiele wiadomości:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

### <a name="output---f-example"></a>Dane wyjściowe — F# przykład

Poniższy przykład pokazuje usługi Service Bus powiązania danych wyjściowych usługi w *function.json* pliku i [ F# skryptu funkcja](functions-reference-fsharp.md) powiązania, który używa. Funkcja korzysta z wyzwalacza czasomierza, aby wysłać komunikatu w kolejce, co 15 sekund.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Oto F# kod, który tworzy jeden komunikat o skryptu:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.LogInformation(message)
    outputSbQueue = message
```

### <a name="output---java-example"></a>Dane wyjściowe — przykładzie w języku Java

W poniższym przykładzie pokazano funkcję języka Java, która wysyła komunikat do kolejki usługi Service Bus `myqueue` po wyzwoleniu przez żądanie HTTP.

```java
@FunctionName("httpToServiceBusQueue")
@ServiceBusQueueOutput(name = "message", queueName = "myqueue", connection = "AzureServiceBusConnection")
public String pushToQueue(
  @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
  final String message,
  @HttpOutput(name = "response") final OutputBinding<T> result ) {
      result.setValue(message + " has been sent.");
      return message;
 }
```

 W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@QueueOutput` adnotacja dla parametrów funkcji, której wartość będzie zapisany do kolejki usługi Service Bus.  Typ parametru musi być `OutputBinding<T>`, gdzie T jest dowolnego natywnego języka Java typu obiektu typu POJO.

Funkcje języka Java mogą również zapisywać w temacie Service Bus. Poniższy przykład używa adnotacji `@ServiceBusTopicOutput`, aby opisać konfigurację dla powiązania danych wyjściowych. 

```java
@FunctionName("sbtopicsend")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @ServiceBusTopicOutput(name = "message", topicName = "mytopicname", subscriptionName = "mysubscription", connection = "ServiceBusConnection") OutputBinding<String> message,
            final ExecutionContext context) {
        
        String name = request.getBody().orElse("Azure Functions");

        message.setValue(name);
        return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + name).build();
        
    }
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

Poniższy przykład pokazuje usługi Service Bus powiązania danych wyjściowych usługi w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja korzysta z wyzwalacza czasomierza, aby wysłać komunikatu w kolejce, co 15 sekund.

Oto powiązanie danych w *function.json* pliku:

```json
{
    "bindings": [
        {
            "schedule": "0/15 * * * * *",
            "name": "myTimer",
            "runsOnStartup": true,
            "type": "timerTrigger",
            "direction": "in"
        },
        {
            "name": "outputSbQueue",
            "type": "serviceBus",
            "queueName": "testqueue",
            "connection": "MyServiceBusConnection",
            "direction": "out"
        }
    ],
    "disabled": false
}
```

Poniżej przedstawiono kod skryptu języka JavaScript, który tworzy pojedynczy komunikat:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Poniżej przedstawiono kod skryptu języka JavaScript, który tworzy wiele wiadomości:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = [];
    context.bindings.outputSbQueue.push("1 " + message);
    context.bindings.outputSbQueue.push("2 " + message);
    context.done();
};
```

### <a name="output---python-example"></a>Dane wyjściowe — przykład w języku Python

Poniższy przykład ilustruje sposób zapisywania do kolejki ServiceBus w języku Python.

Definicja powiązania ServiceBue jest zdefiniowana w *funkcji Function. JSON* , w której *typ* ma wartość `serviceBus`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "serviceBus",
      "direction": "out",
      "connection": "AzureServiceBusConnectionString",
      "name": "msg",
      "queueName": "outqueue"
    }
  ]
}
```

W  *_\_init_\_. PR*można napisać komunikat do kolejki, przekazując wartość do metody `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atrybutu ma nazwę kolejki lub tematu i subskrypcji. Można również określić prawa dostępu do tego połączenia. Jak wybrać prawa dostępu, ustawienie zostało wyjaśnione w [dane wyjściowe — Konfiguracja](#output---configuration) sekcji. Oto przykład pokazujący zastosowany na wartość zwracaną funkcji:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Możesz ustawić `Connection` właściwości w celu określenia konta usługi Service Bus do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

Możesz użyć `ServiceBusAccount` atrybutu, aby określić konto usługi Service Bus do użycia na poziomie klasy, metody lub parametru.  Aby uzyskać więcej informacji, zobacz [wyzwalacza — atrybuty](#trigger---attributes).

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `ServiceBus` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "magistrali usług". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa "out". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**name** | Nie dotyczy | Nazwa zmiennej, która reprezentuje kolejki lub tematu w kodzie funkcji. Należy ustawić "$return", aby odwoływać się do wartości zwracanej funkcji. |
|**queueName**|**QueueName**|Nazwa kolejki.  Ustaw tylko wtedy, gdy wysyłanie komunikatów z kolejki, tematu dla.
|**topicName**|**topicName**|Nazwa tematu do monitorowania. Ustaw tylko wtedy, gdy wysyłanie komunikatów z tematów, nie dla kolejki.|
|**połączenia**|**połączenia**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Service Bus do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy. Na przykład jeśli ustawisz `connection` do "MyServiceBus", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyServiceBus." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia usługi Service Bus w ustawieniu aplikacji, który nosi nazwę "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj kroki opisane w sekcji [pobieranie poświadczeń zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą mieć dla przestrzeni nazw usługi Service Bus, nie są ograniczone do określonej kolejki lub tematu.|
|**accessRights**|**Dostęp**|Prawa dostępu parametrów połączenia. Dostępne wartości to `manage` i `listen`. Wartość domyślna to `manage`, który wskazuje, że `connection` ma **Zarządzaj** uprawnień. Jeśli używasz parametry połączenia, który nie ma **Zarządzaj** zestawu uprawnień, `accessRights` "nasłuchiwanie". W przeciwnym razie funkcje środowiska uruchomieniowego może zakończyć się niepowodzeniem próby wykonania operacji wymagających zarządzania prawami. W usłudze Azure Functions w wersji 2.x, ta właściwość nie jest dostępna, ponieważ najnowszą wersję zestawu SDK usługi Storage nie obsługuje zarządzania operacjami.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W usłudze Azure Functions 1.x, środowisko uruchomieniowe tworzy kolejkę, jeśli nie istnieje, i zostały ustawione `accessRights` do `manage`. W funkcji w wersji 2.x, kolejki lub tematu musi już istnieć; Jeśli określisz, kolejki lub tematu, który nie istnieje, funkcja zakończy się niepowodzeniem. 

W języku C# i skrypt języka C# można użyć następujących typów parametrów dla powiązania danych wyjściowych:

* `out T paramName` - `T` mogą być dowolnego typu serializacji JSON. Jeśli wartość parametru ma wartość null, jeśli funkcja kończy działanie, funkcje tworzy komunikat z obiektem o wartości null.
* `out string` — Jeśli wartość parametru ma wartość null, jeśli funkcja kończy działanie, funkcje nie powoduje utworzenia wiadomości.
* `out byte[]` — Jeśli wartość parametru ma wartość null, jeśli funkcja kończy działanie, funkcje nie powoduje utworzenia wiadomości.
* `out BrokeredMessage` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 1. x)
* `out Message` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 2. x)
* `ICollector<T>` lub `IAsyncCollector<T>` — w przypadku tworzenia wielu wiadomości. Komunikat jest tworzone, gdy wywołujesz `Add` metody.

Podczas pracy z C# funkcjami:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast parametru `out`.

* Aby uzyskać dostęp do identyfikatora sesji, powiąż z typem [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i użyj właściwości `sessionId`.

W języku JavaScript, dostęp do kolejki lub tematu przy użyciu `context.bindings.<name from function.json>`. Do `context.binding.<name>`można przypisać ciąg, tablicę bajtową lub obiekt JavaScript (deserializowany w formacie JSON).

Aby wysłać komunikat do kolejki z obsługą sesji wC# języku innym niż język, użyj [zestawu SDK Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging) , a nie wbudowanego powiązania danych wyjściowych.

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Informacje ogólne |
|---|---|
| Service Bus | [Kody błędów usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limity usługi Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Przykład pliku host.json poniżej zawiera tylko ustawienia 2.x wersji dla tego powiązania. Aby uzyskać więcej informacji na temat globalne ustawienia konfiguracji w wersji 2.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions w wersji 2.x](functions-host-json.md).

> [!NOTE]
> Odwołanie host.json w funkcjach 1.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "serviceBus": {
            "prefetchCount": 100,
            "messageHandlerOptions": {
                "autoComplete": false,
                "maxConcurrentCalls": 32,
                "maxAutoRenewDuration": "00:55:00"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Maksymalny czas trwania, w którym blokadę komunikatu zostanie odnowiony automatycznie.|
|Autouzupełnianie|true|Czy wyzwalacz powinien natychmiast Oznacz jako ukończone (automatycznego uzupełniania) lub poczekaj przetwarzania rozmowę.|
|maxConcurrentCalls|16|Maksymalna liczba równoczesnych wywołań wywołanie zwrotne, które powinno zainicjować "pompy komunikatów". Domyślnie środowisko uruchomieniowe usługi Functions przetwarza wiele wiadomości jednocześnie. Aby skierowania czasu przetwarzania tylko jednej kolejki lub tematu wiadomości w czasie, należy ustawić `maxConcurrentCalls` 1. |
|prefetchCount|Nie dotyczy|Domyślnie PrefetchCount używanej przez MessageReceiver bazowego.|


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)
