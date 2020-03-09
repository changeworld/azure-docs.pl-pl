---
title: Usługa Azure powiązania usługi Service Bus dla usługi Azure Functions
description: Dowiedz się, jak wysyłać komunikaty Azure Service Bus z Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356180"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Azure Service Bus powiązanie danych wyjściowych dla Azure Functions

Użyj powiązania danych wyjściowych usługi Azure Service Bus, aby wysyłać komunikaty do kolejki lub tematu.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-service-bus-output.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która wysyła komunikat Service Bus kolejki:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia Service Bus powiązanie danych wyjściowych w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja korzysta z wyzwalacza czasomierza, aby wysłać komunikatu w kolejce, co 15 sekund.

Oto dane powiązania w pliku *Function. JSON* :

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia Service Bus powiązanie danych wyjściowych w pliku *Function. JSON* i [funkcję języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja korzysta z wyzwalacza czasomierza, aby wysłać komunikatu w kolejce, co 15 sekund.

Oto dane powiązania w pliku *Function. JSON* :

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

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób zapisywania do kolejki Service Bus w języku Python.

Definicja powiązania Service Bus jest zdefiniowana w *funkcji Function. JSON* , w której *Typ* jest ustawiony na `serviceBus`.

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

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład pokazuje funkcję języka Java, która wysyła komunikat do kolejki Service Bus, `myqueue` wyzwalane przez żądanie HTTP.

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

 W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj adnotacji `@QueueOutput` w przypadku parametrów funkcji, których wartość zostałaby zapisywana w kolejce Service Bus.  Typ parametru powinien być `OutputBinding<T>`, gdzie T jest dowolnym natywnym typem języka Java POJO.

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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj [ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atrybutu ma nazwę kolejki lub tematu i subskrypcji. Można również określić prawa dostępu do tego połączenia. Sposób wybierania ustawienia praw dostępu jest objaśniony w sekcji [Konfiguracja wyjściowa](#configuration) . Oto przykład pokazujący zastosowany na wartość zwracaną funkcji:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Można ustawić właściwość `Connection`, aby określić nazwę ustawienia aplikacji, która zawiera Service Bus parametry połączenia do użycia, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Aby uzyskać pełny przykład, zobacz [Output-example](#example).

Można użyć atrybutu `ServiceBusAccount`, aby określić konto Service Bus do użycia na poziomie klasy, metody lub parametru.  Aby uzyskać więcej informacji, zobacz [wyzwalacz-atrybuty](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

Adnotacje `ServiceBusQueueOutput` i `ServiceBusTopicOutput` są dostępne do zapisania komunikatu jako dane wyjściowe funkcji. Parametr z tymi adnotacjami musi być zadeklarowany jako `OutputBinding<T>`, gdzie `T` jest typem odpowiadającym typowi komunikatu.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `ServiceBus` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "magistrali usług". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa "out". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej, która reprezentuje komunikat kolejki lub tematu w kodzie funkcji. Należy ustawić "$return", aby odwoływać się do wartości zwracanej funkcji. |
|**Zmienną QueueName**|**Zmienną QueueName**|Nazwa kolejki.  Ustaw tylko wtedy, gdy wysyłanie komunikatów z kolejki, tematu dla.
|**temat**|**Temat**|Nazwa tematu. Ustaw tylko wtedy, gdy wysyłanie komunikatów z tematów, nie dla kolejki.|
|**połączenia**|**Połączenie**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Service Bus do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy. Na przykład jeśli ustawisz `connection` na "MyServiceBus", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyServiceBus". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia Service Bus w ustawieniu aplikacji o nazwie "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj kroki opisane w sekcji [pobieranie poświadczeń zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą mieć dla przestrzeni nazw usługi Service Bus, nie są ograniczone do określonej kolejki lub tematu.|
|**accessRights**|**Dostęp**|Prawa dostępu parametrów połączenia. Dostępne wartości to `manage` i `listen`. Wartość domyślna to `manage`, co oznacza, że `connection` ma uprawnienie do **zarządzania** . W przypadku używania parametrów połączenia, które nie mają uprawnienia do **zarządzania** , ustaw `accessRights` na "nasłuchuj". W przeciwnym razie funkcje środowiska uruchomieniowego może zakończyć się niepowodzeniem próby wykonania operacji wymagających zarządzania prawami. W Azure Functions w wersji 2. x lub nowszej ta właściwość nie jest dostępna, ponieważ Najnowsza wersja zestawu Service Bus SDK nie obsługuje operacji zarządzania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

W Azure Functions 1. x środowisko uruchomieniowe tworzy kolejkę, jeśli nie istnieje, i ustawiono `accessRights` do `manage`. W funkcjach w wersji 2. x i nowszych Kolejka lub temat muszą już istnieć; w przypadku określenia kolejki lub tematu, który nie istnieje, funkcja zakończy się niepowodzeniem. 

# <a name="c"></a>[C#](#tab/csharp)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `out T paramName` - `T` może być dowolnym typem możliwym do serializacji JSON. Jeśli wartość parametru ma wartość null, jeśli funkcja kończy działanie, funkcje tworzy komunikat z obiektem o wartości null.
* `out string` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out byte[]` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out BrokeredMessage` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 1. x)
* `out Message` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 2. x i wyższych)
* `ICollector<T>` lub `IAsyncCollector<T>` — do tworzenia wielu komunikatów. Tworzony jest komunikat podczas wywoływania metody `Add`.

Podczas pracy z C# funkcjami:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast parametru `out`.

* Aby uzyskać dostęp do identyfikatora sesji, powiąż z typem [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i użyj właściwości `sessionId`.

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Użyj następujących typów parametrów dla powiązania danych wyjściowych:

* `out T paramName` - `T` może być dowolnym typem możliwym do serializacji JSON. Jeśli wartość parametru ma wartość null, jeśli funkcja kończy działanie, funkcje tworzy komunikat z obiektem o wartości null.
* `out string` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out byte[]` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu.
* `out BrokeredMessage` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 1. x)
* `out Message` — Jeśli wartość parametru ma wartość null, gdy funkcja zostanie zakończona, funkcja nie tworzy komunikatu (dla funkcji 2. x i wyższych)
* `ICollector<T>` lub `IAsyncCollector<T>` — do tworzenia wielu komunikatów. Tworzony jest komunikat podczas wywoływania metody `Add`.

Podczas pracy z C# funkcjami:

* Funkcje asynchroniczne wymagają wartości zwracanej lub `IAsyncCollector` zamiast parametru `out`.

* Aby uzyskać dostęp do identyfikatora sesji, powiąż z typem [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) i użyj właściwości `sessionId`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dostęp do kolejki lub tematu za pomocą `context.bindings.<name from function.json>`. Do `context.binding.<name>`można przypisać ciąg, tablicę bajtową lub obiekt JavaScript (deserializowany w formacie JSON).

# <a name="python"></a>[Python](#tab/python)

Użyj [zestawu SDK Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging) , a nie wbudowanego powiązania danych wyjściowych.

# <a name="java"></a>[Java](#tab/java)

Użyj [zestawu SDK Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging) , a nie wbudowanego powiązania danych wyjściowych.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie | Dokumentacja |
|---|---|
| Service Bus | [Service Bus kody błędów](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limity Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2. x i nowszych. Poniższy przykładowy plik host. JSON zawiera tylko ustawienia dla tego powiązania. Aby uzyskać więcej informacji na temat ustawień konfiguracji globalnej, zobacz [Dokumentacja pliku host. JSON dla wersji Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host. JSON w funkcjach 1. x, zobacz informacje dotyczące pliku [host. JSON dla Azure Functions 1. x](functions-host-json-v1.md).

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
            },
            "sessionHandlerOptions": {
                "autoComplete": false,
                "messageWaitTimeout": "00:00:30",
                "maxAutoRenewDuration": "00:55:00",
                "maxConcurrentSessions": 16
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxAutoRenewDuration|00:05:00|Maksymalny czas trwania, w którym blokadę komunikatu zostanie odnowiony automatycznie.|
|Autouzupełnianie|true|Czy wyzwalacz powinien natychmiast oznaczyć wiadomość jako kompletną (Autouzupełnianie), czy poczekać na zakończenie wywołania funkcji w celu pomyślnego zakończenia.|
|maxConcurrentCalls|16|Maksymalna liczba równoczesnych wywołań wywołanie zwrotne, które powinno zainicjować "pompy komunikatów". Domyślnie środowisko uruchomieniowe usługi Functions przetwarza wiele wiadomości jednocześnie. Aby skierować środowisko uruchomieniowe do przetwarzania tylko pojedynczej kolejki lub komunikatu tematu, ustaw `maxConcurrentCalls` na 1. |

## <a name="next-steps"></a>Następne kroki

- [Uruchom funkcję po utworzeniu kolejki Service Bus lub komunikatu tematu (Wyzwól)](./functions-bindings-service-bus-trigger.md)
