---
title: Usługa Azure powiązania usługi Service Bus dla usługi Azure Functions
description: Dowiedz się, jak uruchamiać funkcję platformy Azure, gdy są tworzone Azure Service Bus komunikaty.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273565"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Wyzwalacz Azure Service Bus dla Azure Functions

Użyj wyzwalacza usługi Service Bus, aby odpowiadać na komunikaty z kolejki usługi Service Bus lub temat.

Aby uzyskać informacje na temat konfiguracji i szczegółów konfiguracji, zobacz [Omówienie](functions-bindings-service-bus-output.md).

## <a name="example"></a>Przykład

# <a name="c"></a>[C#](#tab/csharp)

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która odczytuje [metadane komunikatów](#message-metadata) i rejestruje komunikat Service Bus kolejki:

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

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Poniższy przykład przedstawia powiązanie wyzwalacza Service Bus w pliku *Function. JSON* i [ C# funkcji skryptu](functions-reference-csharp.md) , która używa powiązania. Funkcja odczytuje [metadane komunikatów](#message-metadata) i rejestruje komunikat Service Bus kolejki.

Oto dane powiązania w pliku *Function. JSON* :

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza Service Bus w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja odczytuje [metadane komunikatów](#message-metadata) i rejestruje komunikat Service Bus kolejki. 

Oto dane powiązania w pliku *Function. JSON* :

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

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład ilustruje sposób odczytywania Service Bus komunikatu kolejki za pośrednictwem wyzwalacza.

Powiązanie Service Bus jest zdefiniowane w *funkcji Function. JSON* , w której *Typ* jest ustawiony na `serviceBusTrigger`.

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

# <a name="java"></a>[Java](#tab/java)

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

---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C#](#tab/csharp)

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować wyzwalacz Service Bus:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  Konstruktor atrybutu ma nazwę kolejki lub tematu i subskrypcji. W usłudze Azure Functions w wersji 1.x, można również określić, że prawa dostępu do tego połączenia. Jeśli nie określisz praw dostępu, wartość domyślna to `Manage`. Aby uzyskać więcej informacji, zobacz sekcję [wyzwalacz-konfiguracja](#configuration) .

  Oto przykład pokazujący atrybut używany z parametrem ciągu:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Można ustawić właściwość `Connection`, aby określić nazwę ustawienia aplikacji, która zawiera Service Bus parametry połączenia do użycia, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Aby zapoznać się z kompletnym przykładem, zobacz [wyzwalacz-example](#example).

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

* Właściwość `Connection` atrybutu `ServiceBusTrigger`.
* Atrybut `ServiceBusAccount` stosowany do tego samego parametru, który jest atrybutem `ServiceBusTrigger`.
* Atrybut `ServiceBusAccount` stosowany do funkcji.
* Atrybut `ServiceBusAccount` stosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsServiceBus".

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Atrybuty nie są obsługiwane przez C# skrypt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atrybuty nie są obsługiwane przez język JavaScript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez język Python.

# <a name="java"></a>[Java](#tab/java)

Adnotacja `ServiceBusQueueTrigger` pozwala utworzyć funkcję, która jest uruchamiana, gdy zostanie utworzony komunikat Service Bus Queue. Dostępne opcje konfiguracji obejmują nazwę kolejki i nazwę parametrów połączenia.

Adnotacja `ServiceBusTopicTrigger` pozwala wyznaczyć temat i subskrypcję, aby wskazać, jakie dane są wyzwalane przez funkcję.

Zobacz [przykład](#example) wyzwalacza, aby uzyskać więcej szczegółów.

---

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań ustawiane w pliku *Function. JSON* i `ServiceBusTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa "serviceBusTrigger". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa "in". Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwij** | Nie dotyczy | Nazwa zmiennej, która reprezentuje komunikat kolejki lub tematu w kodzie funkcji. |
|**Zmienną QueueName**|**Zmienną QueueName**|Nazwa kolejki do monitorowania.  Ustaw tylko wtedy, gdy kolejki, tematu dla monitorowania.
|**temat**|**Temat**|Nazwa tematu do monitorowania. Ustaw tylko wtedy, gdy monitorowanie tematu, nie dla kolejki.|
|**subscriptionName**|**SubscriptionName**|Nazwa subskrypcji do monitorowania. Ustaw tylko wtedy, gdy monitorowanie tematu, nie dla kolejki.|
|**połączenia**|**Połączenie**|Nazwa ustawienia aplikacji zawierającego parametry połączenia usługi Service Bus do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy. Na przykład jeśli ustawisz `connection` na "MyServiceBus", środowisko uruchomieniowe funkcji szuka ustawienia aplikacji o nazwie "AzureWebJobsMyServiceBus". W przypadku pozostawienia `connection` pustego środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia Service Bus w ustawieniu aplikacji o nazwie "AzureWebJobsServiceBus".<br><br>Aby uzyskać parametry połączenia, wykonaj kroki opisane w sekcji [pobieranie poświadczeń zarządzania](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string). Parametry połączenia muszą mieć dla przestrzeni nazw usługi Service Bus, nie są ograniczone do określonej kolejki lub tematu. |
|**accessRights**|**Dostęp**|Prawa dostępu parametrów połączenia. Dostępne wartości to `manage` i `listen`. Wartość domyślna to `manage`, co oznacza, że `connection` ma uprawnienie do **zarządzania** . W przypadku używania parametrów połączenia, które nie mają uprawnienia do **zarządzania** , ustaw `accessRights` na "nasłuchuj". W przeciwnym razie funkcje środowiska uruchomieniowego może zakończyć się niepowodzeniem próby wykonania operacji wymagających zarządzania prawami. W Azure Functions w wersji 2. x lub nowszej ta właściwość nie jest dostępna, ponieważ Najnowsza wersja zestawu Service Bus SDK nie obsługuje operacji zarządzania.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` w przypadku nawiązywania połączenia z kolejką lub subskrypcją [obsługującą sesję](../service-bus-messaging/message-sessions.md) . `false` w przeciwnym razie jest to wartość domyślna.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C#](#tab/csharp)

Następujące typy parametrów są dostępne dla komunikatu kolejki lub tematu:

* `string` — Jeśli komunikat jest tekstem.
* `byte[]` — przydatne w przypadku danych binarnych.
* Niestandardowy typ — Jeśli wiadomość zawiera JSON, usługi Azure Functions próbuje deserializowanie danych JSON.
* `BrokeredMessage` — zawiera deserializowany komunikat z metodą [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Te typy parametrów są przeznaczone dla Azure Functions wersja 1. x; dla 2. x i wyższych Użyj [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) zamiast `BrokeredMessage`.

# <a name="c-script"></a>[C#Napisy](#tab/csharp-script)

Następujące typy parametrów są dostępne dla komunikatu kolejki lub tematu:

* `string` — Jeśli komunikat jest tekstem.
* `byte[]` — przydatne w przypadku danych binarnych.
* Niestandardowy typ — Jeśli wiadomość zawiera JSON, usługi Azure Functions próbuje deserializowanie danych JSON.
* `BrokeredMessage` — zawiera deserializowany komunikat z metodą [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Te parametry dotyczą Azure Functions wersji 1. x; dla 2. x i wyższych Użyj [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) zamiast `BrokeredMessage`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Uzyskaj dostęp do kolejki lub komunikatu tematu za pomocą `context.bindings.<name from function.json>`. Komunikat usługi Service Bus jest przekazywany do funkcji jako ciąg lub obiekt JSON.

# <a name="python"></a>[Python](#tab/python)

Komunikat kolejki jest dostępny dla funkcji za pośrednictwem parametru, który został określony jako `func.ServiceBusMessage`. Komunikat usługi Service Bus jest przekazywany do funkcji jako ciąg lub obiekt JSON.

# <a name="java"></a>[Java](#tab/java)

Service Bus komunikat przychodzący jest dostępny za pośrednictwem `ServiceBusQueueMessage` lub `ServiceBusTopicMessage` parametru.

[Zobacz przykład, aby uzyskać szczegółowe informacje](#example).

---

## <a name="poison-messages"></a>Trujące komunikaty

Obsługa skażonych komunikatów nie kontrolowany lub skonfigurowane w usłudze Azure Functions. Usługa Service Bus obsługuje komunikaty zanieczyszczone sam.

## <a name="peeklock-behavior"></a>Zachowanie PeekLock

Środowisko uruchomieniowe funkcji odbiera komunikat w [trybie PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Wywołuje `Complete` w komunikacie, jeśli funkcja zakończy się pomyślnie, lub wywołuje `Abandon`, jeśli funkcja się nie powiedzie. Jeśli funkcja działa dłużej niż limit czasu `PeekLock`, blokada zostanie automatycznie odnowiona, o ile funkcja jest uruchomiona. 

`maxAutoRenewDuration` można skonfigurować w pliku *host. JSON*, który jest mapowany na [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Maksymalny dozwolony dla tego ustawienia jest 5 minut zgodnie z dokumentacją usługi Service Bus, podczas gdy można zwiększyć limit czasu funkcji z domyślnej wartości 5 minut do 10 minut. Dla funkcji usługi Service Bus w takich sytuacjach przydałaby chcesz to zrobić w efekcie ponieważ przekroczyłby limit odnawiania usługi Service Bus.

## <a name="message-metadata"></a>Metadane komunikatów

Wyzwalacz Service Bus zawiera kilka [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Te właściwości są elementami członkowskimi klasy [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Liczba dostarczeń.|
|`DeadLetterSource`|`string`|Źródło utraconych wiadomości.|
|`ExpiresAtUtc`|`DateTime`|Czas wygaśnięcia w formacie UTC.|
|`EnqueuedTimeUtc`|`DateTime`|Godzina umieszczonych w kolejce w formacie UTC.|
|`MessageId`|`string`|Wartość zdefiniowana przez użytkownika magistrala usług może używać do identyfikowania zduplikowanych komunikatów, jeśli włączona.|
|`ContentType`|`string`|Identyfikator typu zawartości używany przez nadawcę i odbiorcę dla logiki specyficznej dla aplikacji.|
|`ReplyTo`|`string`|Odpowiedzi na adres kolejki.|
|`SequenceNumber`|`Int64`|Unikatowy numer przypisany do komunikatu przez magistralę usług.|
|`To`|`string`|Wyślij adres.|
|`Label`|`string`|Etykieta specyficzna dla aplikacji.|
|`CorrelationId`|`string`|Identyfikator korelacji.|

Zobacz [przykłady kodu](#example) , które używają tych właściwości wcześniej w tym artykule.

## <a name="next-steps"></a>Następne kroki

- [Wysyłanie komunikatów Azure Service Bus z Azure Functions (powiązanie danych wyjściowych)](./functions-bindings-service-bus-output.md)
