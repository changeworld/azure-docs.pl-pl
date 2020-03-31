---
title: Powiązania usługi Azure Service Bus dla funkcji platformy Azure
description: Dowiedz się, jak uruchomić funkcję platformy Azure podczas tworzenia komunikatów usługi Azure Service Bus.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273565"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Wyzwalacz usługi Azure Service Bus dla funkcji platformy Azure

Użyj wyzwalacza usługi Service Bus, aby odpowiadać na wiadomości z kolejki lub tematu usługi Service Bus.

Aby uzyskać informacje na temat szczegółów konfiguracji i konfiguracji, zobacz [omówienie](functions-bindings-service-bus-output.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C #](#tab/csharp)

W poniższym przykładzie pokazano [funkcję języka C#,](functions-dotnet-class-library.md) która odczytuje [metadane wiadomości](#message-metadata) i rejestruje komunikat kolejki usługi Service Bus:

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza usługi Service Bus w pliku *function.json* i [funkcję skryptu Języka C#,](functions-reference-csharp.md) która używa powiązania. Funkcja odczytuje [metadane wiadomości](#message-metadata) i rejestruje komunikat kolejki usługi Service Bus.

Oto dane powiązania w pliku *function.json:*

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

Oto kod skryptu języka C#:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza usługi Service Bus w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja odczytuje [metadane wiadomości](#message-metadata) i rejestruje komunikat kolejki usługi Service Bus. 

Oto dane powiązania w pliku *function.json:*

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

Oto kod skryptu JavaScript:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak odczytać komunikat kolejki usługi Service Bus za pośrednictwem wyzwalacza.

Powiązanie usługi Service Bus jest zdefiniowane w `serviceBusTrigger` *pliku function.json,* gdzie *typ* jest ustawiony na .

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

Kod * _ \_init_\_.py* deklaruje parametr `func.ServiceBusMessage`jako , który umożliwia odczytanie komunikatu kolejki w funkcji.

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

# <a name="java"></a>[Java](#tab/java)

Następująca funkcja Java `@ServiceBusQueueTrigger` używa adnotacji z [biblioteki środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime) do opisania konfiguracji wyzwalacza kolejki usługi Service Bus. Funkcja pobiera wiadomość umieszczona w kolejce i dodaje go do dzienników.

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

Funkcje Java można również wyzwolić po dodaniu wiadomości do tematu usługi Service Bus. W poniższym przykładzie `@ServiceBusTopicTrigger` użyto adnotacji do opisania konfiguracji wyzwalacza.

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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj następujących atrybutów, aby skonfigurować wyzwalacz usługi Service Bus:

* [Atrybut ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę kolejki lub tematu i subskrypcji. W usłudze Azure Functions w wersji 1.x można również określić prawa dostępu do połączenia. Jeśli nie określisz praw dostępu, `Manage`wartością domyślną jest . Aby uzyskać więcej informacji, zobacz [trigger - konfiguracji](#configuration) sekcji.

  Oto przykład, który pokazuje atrybut używany z parametrem ciągu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Można ustawić `Connection` właściwość, aby określić nazwę ustawienia aplikacji, który zawiera ciąg połączenia usługi Service Bus do użycia, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Pełny przykład można znaleźć w [pliku Trigger — przykład](#example).

* [Atrybut ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Umożliwia inny sposób określania konta usługi Service Bus, którego chcesz użyć. Konstruktor przyjmuje nazwę ustawienia aplikacji, która zawiera ciąg połączenia usługi Service Bus. Atrybut można zastosować na poziomie parametru, metody lub klasy. Poniższy przykład przedstawia poziom klasy i poziom metody:

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

Konto usługi Service Bus do użycia jest określane w następującej kolejności:

* Właściwość `ServiceBusTrigger` atrybutu. `Connection`
* Atrybut `ServiceBusAccount` zastosowany do tego samego `ServiceBusTrigger` parametru co atrybut.
* Atrybut `ServiceBusAccount` zastosowany do funkcji.
* Atrybut `ServiceBusAccount` stosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsServiceBus".

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacja `ServiceBusQueueTrigger` umożliwia utworzenie funkcji, która jest uruchamiana podczas tworzenia komunikatu kolejki usługi Service Bus. Dostępne opcje konfiguracji obejmują nazwę kolejki i nazwę ciągu połączenia.

Adnotacja `ServiceBusTopicTrigger` umożliwia wyznaczenie tematu i subskrypcji, aby określić, jakie dane wyzwala funkcję.

Zobacz [przykład](#example) wyzwalacza, aby uzyskać więcej szczegółów.

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `ServiceBusTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy | Musi być ustawiona na "serviceBusTrigger". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal.|
|**Kierunku** | Nie dotyczy | Musi być ustawiona na "in". Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej reprezentującej komunikat kolejki lub tematu w kodzie funkcji. |
|**Queuename**|**Queuename**|Nazwa kolejki do monitorowania.  Ustaw tylko wtedy, gdy monitorowanie kolejki, a nie dla tematu.
|**topicName**|**Nazwa tematu**|Nazwa tematu do monitorowania. Ustaw tylko wtedy, gdy monitorowanie tematu, a nie dla kolejki.|
|**nazwa subskrypcji**|**SubscriptionName**|Nazwa subskrypcji do monitorowania. Ustaw tylko wtedy, gdy monitorowanie tematu, a nie dla kolejki.|
|**Połączenia**|**Połączenia**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Service Bus do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy. Na przykład jeśli `connection` ustawisz "MyServiceBus", funkcja runtime szuka ustawienia aplikacji o nazwie "AzureWebJobsMyServiceBus". Jeśli pozostawisz `connection` puste, funkcja środowiska wykonawczego używa domyślnego ciągu połączenia usługi Service Bus w ustawieniach aplikacji o nazwie "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj czynności opisane w [punkcie Pobierz poświadczenia zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą znajdować się w obszarze nazw usługi Service Bus, a nie ograniczone do określonej kolejki lub tematu. |
|**prawa dostępu**|**Dostęp**|Prawa dostępu dla ciągu połączenia. Dostępne wartości `manage` `listen`to i . Wartość domyślna to `manage`, `connection` co oznacza, że ma **uprawnienie Zarządzanie.** Jeśli używasz ciągu połączenia, który nie ma `accessRights` **uprawnień Zarządzanie,** ustaw na "nasłuchiwać". W przeciwnym razie środowisko wykonawcze Functions może zakończyć się niepowodzeniem podczas wykonywania operacji wymagających zarządzania prawami. W usłudze Azure Functions w wersji 2.x lub nowszej ta właściwość nie jest dostępna, ponieważ najnowsza wersja zestawu SDK usługi Service Bus nie obsługuje operacji zarządzania.|
|**isSessionsEnabled**|**IsSessionsEnabled (IsSessionsEnabled)**|`true`jeśli łączenie się z kolejką lub [subskrypcją obsługującą sesję.](../service-bus-messaging/message-sessions.md) `false`w przeciwnym razie, która jest wartością domyślną.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

Następujące typy parametrów są dostępne dla kolejki lub komunikatu tematu:

* `string`- Jeśli wiadomość jest tekstem.
* `byte[]`- Przydatne dla danych binarnych.
* Typ niestandardowy — jeśli komunikat zawiera JSON, usługi Azure Functions próbuje deserialize danych JSON.
* `BrokeredMessage`- Daje deserializowane wiadomości z [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metody.

Te typy parametrów są dla usługi Azure Functions w wersji 1.x; dla 2.x i [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) wyższej, `BrokeredMessage`użyj zamiast .

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Następujące typy parametrów są dostępne dla kolejki lub komunikatu tematu:

* `string`- Jeśli wiadomość jest tekstem.
* `byte[]`- Przydatne dla danych binarnych.
* Typ niestandardowy — jeśli komunikat zawiera JSON, usługi Azure Functions próbuje deserialize danych JSON.
* `BrokeredMessage`- Daje deserializowane wiadomości z [BrokeredMessage.GetBody\<T>()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) metody.

Te parametry są dla usługi Azure Functions w wersji 1.x; dla 2.x i [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) wyższej, `BrokeredMessage`użyj zamiast .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Uzyskaj dostęp do kolejki `context.bindings.<name from function.json>`lub wiadomości tematu za pomocą programu . Komunikat usługi Service Bus jest przekazywany do funkcji jako ciąg lub obiekt JSON.

# <a name="python"></a>[Python](#tab/python)

Komunikat kolejki jest dostępny dla funkcji za `func.ServiceBusMessage`pomocą parametru wpisanego jako . Komunikat usługi Service Bus jest przekazywany do funkcji jako ciąg lub obiekt JSON.

# <a name="java"></a>[Java](#tab/java)

Przychodzący komunikat usługi Service `ServiceBusQueueMessage` Bus `ServiceBusTopicMessage` jest dostępny za pośrednictwem lub parametru.

[Zobacz przykład, aby uzyskać szczegółowe informacje](#example).

---

## <a name="poison-messages"></a>Wiadomości o truciznach

Nie można kontrolować ani konfigurować obsługi zatruć w usługach Azure Functions. Usługa Service Bus obsługuje same komunikaty otrutych.

## <a name="peeklock-behavior"></a>Zachowanie PeekLock

Środowisko wykonawcze Functions odbiera komunikat w [trybie PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Wywołuje `Complete` komunikat, jeśli funkcja zakończy się pomyślnie `Abandon` lub wywołuje, jeśli funkcja nie powiedzie się. Jeśli funkcja działa dłużej niż `PeekLock` limit czasu, blokada jest automatycznie odnawiana tak długo, jak funkcja jest uruchomiona. 

Jest `maxAutoRenewDuration` konfigurowalny w *host.json*, który mapuje [onMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maksymalna dozwolona dla tego ustawienia wynosi 5 minut zgodnie z dokumentacją usługi Service Bus, podczas gdy można zwiększyć limit czasu funkcji z domyślnego 5 minut do 10 minut. W przypadku funkcji usługi Service Bus nie chcesz tego robić, ponieważ należy przekroczyć limit odnawiania usługi Service Bus.

## <a name="message-metadata"></a>Metadane wiadomości

Wyzwalacz usługi Service Bus udostępnia kilka [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości mogą służyć jako część wyrażeń wiązania w innych powiązań lub jako parametry w kodzie. Te właściwości są członkami [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Liczba dostaw.|
|`DeadLetterSource`|`string`|Źródło martwej litery.|
|`ExpiresAtUtc`|`DateTime`|Czas wygaśnięcia w czasie UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Czas w kolejce w utc.|
|`MessageId`|`string`|Wartość zdefiniowana przez użytkownika, której usługa Service Bus może używać do identyfikowania zduplikowanych wiadomości, jeśli jest włączona.|
|`ContentType`|`string`|Identyfikator typu zawartości używany przez nadawcę i odbiorcę dla logiki specyficznej dla aplikacji.|
|`ReplyTo`|`string`|Odpowiedź na adres kolejki.|
|`SequenceNumber`|`Int64`|Unikatowy numer przypisany do wiadomości przez usługę Service Bus.|
|`To`|`string`|Wyślij na adres.|
|`Label`|`string`|Etykieta specyficzna dla aplikacji.|
|`CorrelationId`|`string`|Identyfikator korelacji.|

Zobacz [przykłady kodu,](#example) które używają tych właściwości wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

- [Wysyłanie komunikatów usługi Azure Service Bus z usługi Azure Functions (powiązanie danych wyjściowych)](./functions-bindings-service-bus-output.md)
