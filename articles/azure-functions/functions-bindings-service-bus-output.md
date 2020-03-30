---
title: Powiązania usługi Azure Service Bus dla funkcji platformy Azure
description: Dowiedz się, jak wysyłać wiadomości usługi Azure Service Bus z usługi Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 7e00d03a8b3ec7ef56935ff7714fd932bc343cd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277442"
---
# <a name="azure-service-bus-output-binding-for-azure-functions"></a>Powiązanie danych wyjściowych usługi Azure Service Bus dla funkcji platformy Azure

Użyj powiązania danych wyjściowych usługi Azure Service Bus do wysyłania wiadomości kolejki lub tematu.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](functions-bindings-service-bus-output.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję Języka C#,](functions-dotnet-class-library.md) która wysyła komunikat kolejki usługi Service Bus:

```cs
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string ServiceBusOutput([HttpTrigger] dynamic input, ILogger log)
{
    log.LogInformation($"C# function processed: {input.Text}");
    return input.Text;
}
```

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie danych wyjściowych usługi Service Bus w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja używa wyzwalacza czasomierza do wysyłania wiadomości kolejki co 15 sekund.

Oto dane powiązania w pliku *function.json:*

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

Oto kod skryptu języka C#, który tworzy pojedynczy komunikat:

```cs
public static void Run(TimerInfo myTimer, ILogger log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.LogInformation(message); 
    outputSbQueue = message;
}
```

Oto kod skryptu Języka C#, który tworzy wiele komunikatów:

```cs
public static async Task Run(TimerInfo myTimer, ILogger log, IAsyncCollector<string> outputSbQueue)
{
    string message = $"Service Bus queue messages created at: {DateTime.Now}";
    log.LogInformation(message); 
    await outputSbQueue.AddAsync("1 " + message);
    await outputSbQueue.AddAsync("2 " + message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie danych wyjściowych usługi Service Bus w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja używa wyzwalacza czasomierza do wysyłania wiadomości kolejki co 15 sekund.

Oto dane powiązania w pliku *function.json:*

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

Oto kod skryptu JavaScript, który tworzy jedną wiadomość:

```javascript
module.exports = function (context, myTimer) {
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueue = message;
    context.done();
};
```

Oto kod skryptu JavaScript, który tworzy wiele wiadomości:

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

W poniższym przykładzie pokazano, jak zapisać do kolejki usługi Service Bus w Pythonie.

Definicja powiązania usługi Service Bus jest zdefiniowana w `serviceBus`pliku *function.json,* gdzie *typ* jest ustawiony na .

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

W `set` pliku *\_init .py można zapisać wiadomość do kolejki, przekazując wartość do metody. _ \__*

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

W poniższym przykładzie przedstawiono funkcję Java, `myqueue` która wysyła wiadomość do kolejki usługi Service Bus po wyzwoleniu przez żądanie HTTP.

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

 W [bibliotece środowiska wykonawczego](/java/api/overview/azure/functions/runtime)funkcji `@QueueOutput` Java użyj adnotacji na temat parametrów funkcji, których wartość zostanie zapisana w kolejce usługi Service Bus.  Typ parametru `OutputBinding<T>`powinien być , gdzie T jest dowolnym natywnym typem Java POJO.

Funkcje java można również zapisywać w temacie usługi Service Bus. W poniższym przykładzie `@ServiceBusTopicOutput` użyto adnotacji do opisania konfiguracji powiązania danych wyjściowych. 

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

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj [atrybutu ServiceBusAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAttribute.cs).

Konstruktor atrybutu przyjmuje nazwę kolejki lub tematu i subskrypcji. Można również określić prawa dostępu do połączenia. Jak wybrać ustawienie praw dostępu jest wyjaśnione w [output — konfiguracji](#configuration) sekcji. Oto przykład, który pokazuje atrybut stosowany do zwracanej wartości funkcji:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Można ustawić `Connection` właściwość, aby określić nazwę ustawienia aplikacji, który zawiera ciąg połączenia usługi Service Bus do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("ServiceBusOutput")]
[return: ServiceBus("myqueue", Connection = "ServiceBusConnection")]
public static string Run([HttpTrigger] dynamic input, ILogger log)
{
    ...
}
```

Na przykład kompletny zobacz [Dane wyjściowe — przykład](#example).

Za pomocą `ServiceBusAccount` tego atrybutu można określić konto usługi Service Bus, które ma być używane na poziomie klasy, metody lub parametru.  Aby uzyskać więcej informacji, zobacz [Trigger - atrybuty](functions-bindings-service-bus-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacje `ServiceBusQueueOutput` i `ServiceBusTopicOutput` adnotacje są dostępne do zapisu wiadomości jako dane wyjściowe funkcji. Parametr ozdobiony tymi adnotacjami musi `OutputBinding<T>` być `T` zadeklarowany jako typ, w którym jest typem odpowiadającym typowi wiadomości.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `ServiceBus` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być ustawiona na "serviceBus". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być ustawiona na "out". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej komunikat kolejki lub tematu w kodzie funkcji. Ustaw wartość "$return", aby odwołać się do wartości zwracanej funkcji. |
|**Queuename**|**Queuename**|Nazwa kolejki.  Ustaw tylko wtedy, gdy wysyłasz wiadomości kolejki, a nie dla tematu.
|**topicName**|**Nazwa tematu**|Nazwa tematu. Ustaw tylko wtedy, gdy wysyłaszydy tematu, a nie dla kolejki.|
|**Połączenia**|**Połączenia**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Service Bus do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy. Na przykład jeśli `connection` ustawisz "MyServiceBus", funkcja runtime szuka ustawienia aplikacji o nazwie "AzureWebJobsMyServiceBus". Jeśli pozostawisz `connection` puste, funkcja środowiska wykonawczego używa domyślnego ciągu połączenia usługi Service Bus w ustawieniach aplikacji o nazwie "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj czynności opisane w [punkcie Pobierz poświadczenia zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą znajdować się w obszarze nazw usługi Service Bus, a nie ograniczone do określonej kolejki lub tematu.|
|**prawa dostępu**|**Dostęp**|Prawa dostępu dla ciągu połączenia. Dostępne wartości `manage` `listen`to i . Wartość domyślna to `manage`, `connection` co oznacza, że ma **uprawnienie Zarządzanie.** Jeśli używasz ciągu połączenia, który nie ma `accessRights` **uprawnień Zarządzanie,** ustaw na "nasłuchiwać". W przeciwnym razie środowisko wykonawcze Functions może zakończyć się niepowodzeniem podczas wykonywania operacji wymagających zarządzania prawami. W usłudze Azure Functions w wersji 2.x lub nowszej ta właściwość nie jest dostępna, ponieważ najnowsza wersja zestawu SDK usługi Service Bus nie obsługuje operacji zarządzania.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

W usłudze Azure Functions 1.x środowisko wykonawcze tworzy kolejkę, `accessRights` `manage`jeśli nie istnieje i ustawiono . W funkcji w wersji 2.x lub nowszej kolejka lub temat musi już istnieć; Jeśli określisz kolejkę lub temat, który nie istnieje, funkcja zakończy się niepowodzeniem. 

# <a name="c"></a>[C #](#tab/csharp)

Użyj następujących typów parametrów dla powiązania wyjściowego:

* `out T paramName` - `T`może być dowolny typ serializowalny JSON. Jeśli wartość parametru jest null po zamknięciu funkcji, Functions tworzy komunikat z obiektu null.
* `out string`- Jeśli wartość parametru jest null po zamknięciu funkcji, Functions nie tworzy komunikatu.
* `out byte[]`- Jeśli wartość parametru jest null po zamknięciu funkcji, Functions nie tworzy komunikatu.
* `out BrokeredMessage`- Jeśli wartość parametru jest null po zamknięciu funkcji, Funkcje nie tworzy komunikat (dla funkcji 1.x)
* `out Message`- Jeśli wartość parametru jest null po zamknięciu funkcji, Funkcje nie tworzy komunikat (dla funkcji 2.x i nowszych)
* `ICollector<T>`lub `IAsyncCollector<T>` - Do tworzenia wielu wiadomości. Komunikat jest tworzony podczas `Add` wywoływania metody.

Podczas pracy z funkcjami języka C#:

* Funkcje asynchronizowe `IAsyncCollector` wymagają wartości `out` zwracanej lub zamiast parametru.

* Aby uzyskać dostęp do identyfikatora [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sesji, `sessionId` należy powiązać z typem i użyć właściwości.

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Użyj następujących typów parametrów dla powiązania wyjściowego:

* `out T paramName` - `T`może być dowolny typ serializowalny JSON. Jeśli wartość parametru jest null po zamknięciu funkcji, Functions tworzy komunikat z obiektu null.
* `out string`- Jeśli wartość parametru jest null po zamknięciu funkcji, Functions nie tworzy komunikatu.
* `out byte[]`- Jeśli wartość parametru jest null po zamknięciu funkcji, Functions nie tworzy komunikatu.
* `out BrokeredMessage`- Jeśli wartość parametru jest null po zamknięciu funkcji, Funkcje nie tworzy komunikat (dla funkcji 1.x)
* `out Message`- Jeśli wartość parametru jest null po zamknięciu funkcji, Funkcje nie tworzy komunikat (dla funkcji 2.x i nowszych)
* `ICollector<T>`lub `IAsyncCollector<T>` - Do tworzenia wielu wiadomości. Komunikat jest tworzony podczas `Add` wywoływania metody.

Podczas pracy z funkcjami języka C#:

* Funkcje asynchronizowe `IAsyncCollector` wymagają wartości `out` zwracanej lub zamiast parametru.

* Aby uzyskać dostęp do identyfikatora [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) sesji, `sessionId` należy powiązać z typem i użyć właściwości.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Dostęp do kolejki lub `context.bindings.<name from function.json>`tematu za pomocą programu . Do pliku `context.binding.<name>`.

# <a name="python"></a>[Python](#tab/python)

Użyj [zestawu SDK usługi Azure Service Bus,](https://docs.microsoft.com/azure/service-bus-messaging) a nie wbudowanego powiązania danych wyjściowych.

# <a name="java"></a>[Java](#tab/java)

Użyj [zestawu SDK usługi Azure Service Bus,](https://docs.microsoft.com/azure/service-bus-messaging) a nie wbudowanego powiązania danych wyjściowych.

---

## <a name="exceptions-and-return-codes"></a>Wyjątki i kody zwrotne

| Wiązanie | Tematy pomocy |
|---|---|
| Service Bus | [Kody błędów magistrali usług](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-exceptions) |
| Service Bus | [Limity magistrali usług](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-quotas) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>ustawienia host.json

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersjach 2.x lub nowszych. Przykładowy plik host.json poniżej zawiera tylko ustawienia dla tego powiązania. Aby uzyskać więcej informacji na temat globalnych ustawień konfiguracji, zobacz [odwołanie host.json dla wersji usługi Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host.json w usługach 1.x, zobacz [odwołanie do host.json dla usługi Azure Functions 1.x](functions-host-json-v1.md).

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
|maxAutoRenewOderzenie|00:05:00|Maksymalny czas trwania, w którym blokada wiadomości zostanie automatycznie odnowiona.|
|Autouzupełniania|true|Czy wyzwalacz powinien natychmiast oznaczyć wiadomość jako ukończoną (autouzupełnianie) lub czekać na zakończenie funkcji, aby zakończyć połączenie.|
|Maxconcurrentcalls|16|Maksymalna liczba równoczesnych wywołań wywołania zwrotnego, które pompa wiadomości powinna zainicjować. Domyślnie środowisko wykonawcze Functions przetwarza wiele komunikatów jednocześnie. Aby skierować środowisko uruchomieniowe do przetwarzania tylko jednej `maxConcurrentCalls` kolejki lub komunikatu tematu w czasie, należy ustawić na 1. |

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji podczas tworzenia kolejki lub komunikatu tematu usługi Service Bus (wyzwalacz)](./functions-bindings-service-bus-trigger.md)
