---
title: Powiązania usługi Azure queue storage dla Azure Functions
description: Zapoznaj się z tematem jak używać wyzwalacza usługi Azure queue storage i powiązania danych wyjściowych w Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 6c708bfd0f8e49e9a857b9f77fab6224354ff06a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097182"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Powiązania usługi Azure queue storage dla Azure Functions

W tym artykule wyjaśniono, jak korzystać z powiązań usługi Azure queue storage w Azure Functions. Azure Functions obsługuje powiązania wyzwalacza i wyjścia dla kolejek.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania magazynu kolejki są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) w wersji 2. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania magazynu kolejek są dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) w wersji 3. x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Kodowanie
Funkcja oczekuje ciągu zakodowanego w *formacie base64* . Wszelkie zmiany typu kodowania (w celu przygotowania danych jako ciągu zakodowanego *algorytmem Base64* ) muszą zostać zaimplementowane w usłudze wywołującej.

## <a name="trigger"></a>Wyzwalacz

Użyj wyzwalacza kolejki, aby uruchomić funkcję po odebraniu nowego elementu w kolejce. Komunikat kolejki jest dostarczany jako dane wejściowe do funkcji.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która `myqueue-items` sonduje kolejkę i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

Poniższy przykład przedstawia powiązanie wyzwalacza kolejki w pliku *Function. JSON* i [ C# kodzie skryptu (. CSX)](functions-reference-csharp.md) , który używa powiązania. Funkcja sonduje `myqueue-items` kolejkę i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

Oto *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod skryptu języka C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

W sekcji [użycie](#trigger---usage) objaśniono `myQueueItem`, że nazwa `name` jest określana przez właściwość w Function. JSON.  W [sekcji metadane wiadomości](#trigger---message-metadata) objaśniono wszystkie inne wyświetlane zmienne.

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano powiązanie wyzwalacza kolejki w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja sonduje `myqueue-items` kolejkę i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

Oto *function.json* pliku:

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

[Konfiguracji](#trigger---configuration) sekcji opisano te właściwości.

> [!NOTE]
> Parametr name odzwierciedla jak `context.bindings.<name>` w kodzie JavaScript, który zawiera ładunek elementu kolejki. Ten ładunek jest również przenoszona jako drugi parametr do funkcji.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = async function (context, message) {
    context.log('Node.js queue trigger function processed work item', message);
    // OR access using context.bindings.<name>
    // context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

W sekcji [użycie](#trigger---usage) objaśniono `myQueueItem`, że nazwa `name` jest określana przez właściwość w Function. JSON.  W [sekcji metadane wiadomości](#trigger---message-metadata) objaśniono wszystkie inne wyświetlane zmienne.

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

Poniższy przykład języka Java przedstawia funkcje wyzwalacza kolejki magazynu, które rejestrują wyzwolony komunikat umieszczony `myqueuename`w kolejce.

 ```java
 @FunctionName("queueprocessor")
 public void run(
    @QueueTrigger(name = "msg",
                   queueName = "myqueuename",
                   connection = "myconnvarname") String message,
     final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
 ```

### <a name="trigger---python-example"></a>Wyzwalacz — przykład w języku Python

Poniższy przykład ilustruje sposób odczytywania komunikatu kolejki przekazanego do funkcji za pośrednictwem wyzwalacza.

Wyzwalacz kolejki magazynu jest zdefiniowany w *funkcji Function. JSON* , w której *Typ* jest `queueTrigger`ustawiony na.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Kod `func.ServiceBusMessage`   *_init\__ .PRdeklarujeparametr,którypozwalaodczytać\_* komunikat w kolejce w funkcji.

```python
import logging
import json

import azure.functions as func

def main(msg: func.QueueMessage):
    logging.info('Python queue trigger function processed a queue item.')

    result = json.dumps({
        'id': msg.id,
        'body': msg.get_body().decode('utf-8'),
        'expiration_time': (msg.expiration_time.isoformat()
                            if msg.expiration_time else None),
        'insertion_time': (msg.insertion_time.isoformat()
                           if msg.insertion_time else None),
        'time_next_visible': (msg.time_next_visible.isoformat()
                              if msg.time_next_visible else None),
        'pop_receipt': msg.pop_receipt,
        'dequeue_count': msg.dequeue_count
    })

    logging.info(result)
```

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj następujących atrybutów, aby skonfigurować wyzwalacz kolejki:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę kolejki do monitorowania, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Możesz ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Aby uzyskać kompletny przykład, zobacz [wyzwalacza — przykład w języku C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Zapewnia inny sposób określania konta magazynu, które ma być używane. Konstruktor przyjmuje nazwę ustawienia aplikacji, które zawiera parametry połączenia magazynu. Ten atrybut można zastosować na parametrów, metody lub poziomie klasy. Poniższy przykład pokazuje poziom klasy i metody:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("QueueTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Konto magazynu do użycia jest określane w następującej kolejności:

* `QueueTrigger` Atrybutu `Connection` właściwości.
* `StorageAccount` Zastosowany do tego samego parametru jako `QueueTrigger` atrybutu.
* `StorageAccount` Zastosowany do funkcji.
* `StorageAccount` Zastosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `QueueTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy| Musi być równa `queueTrigger`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction**| Nie dotyczy | Tylko w pliku *Function. JSON* . Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy |Nazwa zmiennej, która zawiera ładunek elementu kolejki w kodzie funkcji.  |
|**queueName** | **QueueName**| Nazwa kolejki do sondowania. |
|**połączenia** | **połączenia** |Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz `connection` opcję "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W C# skryptach i C# Uzyskuj dostęp do danych komunikatów przy użyciu parametru metody, takiego `string paramName`jak. W C# skrypcie `paramName` jest `name` wartością określoną we właściwości *Function. JSON*. Można powiązać z dowolnym z następujących typów:

* Object — środowisko uruchomieniowe funkcji deserializacji ładunek JSON do wystąpienia dowolnej klasy zdefiniowanej w kodzie. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

W języku JavaScript Użyj `context.bindings.<name>` , aby uzyskać dostęp do ładunku elementu kolejki. Jeśli ładunek jest w formacie JSON, jest deserializowany do obiektu.

## <a name="trigger---message-metadata"></a>Wyzwalacz — komunikat metadanych

Wyzwalacz kolejki zawiera kilka [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Są to właściwości klasy [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) .

|Właściwość|Type|Opis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Ładunek kolejki (w przypadku prawidłowego ciągu). Jeśli ładunek komunikatu w kolejce jako ciąg, `QueueTrigger` ma taką samą wartość jak zmienna o nazwie `name` w *funkcji Function. JSON*.|
|`DequeueCount`|`int`|Liczba przypadków, w których ten komunikat został usunięty z kolejki.|
|`ExpirationTime`|`DateTimeOffset`|Godzina wygaśnięcia komunikatu.|
|`Id`|`string`|Identyfikator komunikatu kolejki.|
|`InsertionTime`|`DateTimeOffset`|Godzina, o której wiadomość została dodana do kolejki.|
|`NextVisibleTime`|`DateTimeOffset`|Godzina, o której komunikat będzie dalej widoczny.|
|`PopReceipt`|`string`|Potwierdzenie wyskakujące komunikatu.|

## <a name="trigger---poison-messages"></a>Wyzwalacz - skażone wiadomości

Gdy funkcja wyzwalacza kolejki kończy się niepowodzeniem, Azure Functions ponawia próbę wykonania funkcji maksymalnie pięć razy dla danego komunikatu kolejki, łącznie z pierwszą próbą. Jeśli wszystkie pięć prób nie powiedzie się, środowisko uruchomieniowe funkcji dodaje komunikat do kolejki o nazwie  *&lt;originalqueuename >-Trująca*. Funkcję można napisać, aby przetwarzać komunikaty z kolejki trującej, rejestrując je lub wysyłając powiadomienie, które wymaga ręcznej uwagi.

Aby ręcznie obsłużyć trujące komunikaty, sprawdź [dequeueCount](#trigger---message-metadata) komunikatu w kolejce.

## <a name="trigger---polling-algorithm"></a>Algorytm sondowania wyzwalacza

Wyzwalacz kolejki implementuje losowy wykładniczy algorytm wycofywania, aby zmniejszyć wpływ sondowania w kolejce na koszty transakcji magazynu.  Po znalezieniu komunikatu środowisko uruchomieniowe czeka dwa sekundy, a następnie sprawdza inny komunikat; gdy nie zostanie znaleziony żaden komunikat, czeka około czterech sekund przed ponowieniem próby. Po kolejnych nieudanych próbach pobrania komunikatu w kolejce czas oczekiwania będzie się zwiększać do momentu osiągnięcia maksymalnego czasu oczekiwania, który jest wartością domyślną jednej minuty. Maksymalny czas oczekiwania można skonfigurować za pomocą `maxPollingInterval` właściwości w [pliku host. JSON](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Wyzwalacz — współbieżność

Gdy oczekuje wiele komunikatów w kolejce, wyzwalacz kolejki pobiera partię komunikatów i wywołuje wystąpienia funkcji współbieżnie, aby je przetworzyć. Domyślnie rozmiar wsadu wynosi 16. Gdy przetworzona liczba jest równa 8, środowisko uruchomieniowe pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję na jednej maszynie wirtualnej wynosi 24. Ten limit dotyczy osobno każdej funkcji wyzwalanej przez kolejkę na każdej maszynie wirtualnej. Jeśli aplikacja funkcji jest skalowana do wielu maszyn wirtualnych, każda maszyna wirtualna będzie czekać na wyzwalacze i próbować uruchamiać funkcje. Na przykład jeśli aplikacja funkcji jest skalowana do 3 maszyn wirtualnych, domyślna maksymalna liczba równoczesnych wystąpień jednej funkcji wyzwalanej przez kolejkę to 72.

Rozmiar partii i próg pobierania nowej partii można skonfigurować w [pliku host. JSON](functions-host-json.md#queues). Jeśli chcesz zminimalizować wykonywanie równoległe dla funkcji wyzwalanych przez kolejki w aplikacji funkcji, możesz ustawić rozmiar wsadu na 1. To ustawienie eliminuje współbieżność tylko wtedy, gdy aplikacja funkcji działa na jednej maszynie wirtualnej (VM). 

Wyzwalacz kolejki automatycznie zapobiega przetwarzaniu komunikatów w kolejce przez funkcję wielokrotnie; funkcje nie muszą być zapisywane w taki sposób, aby były idempotentne.

## <a name="trigger---hostjson-properties"></a>Wyzwalacz — właściwości host.json

Plik [host. JSON](functions-host-json.md#queues) zawiera ustawienia sterujące zachowaniem wyzwalacza kolejki. Szczegóły dotyczące dostępnych ustawień można znaleźć w sekcji [Ustawienia hosta. JSON](#hostjson-settings) .

## <a name="output"></a>Output

Użyj powiązania danych wyjściowych usługi Azure queue storage do zapisu komunikatów w kolejce.

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)
* [Python](#output---python-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

Poniższy przykład pokazuje [ C# funkcję](functions-dotnet-class-library.md) , która tworzy komunikat kolejki dla każdego odebranego żądania HTTP.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

Poniższy przykład pokazuje powiązanie wyzwalacza http w pliku *Function. JSON* i [ C# kodzie skryptu (. CSX)](functions-reference-csharp.md) , który używa powiązania. Funkcja tworzy element kolejki z ładunkiem obiektu **CustomQueueMessage** dla każdego odebranego żądania HTTP.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Kod C# skryptu, który tworzy pojedynczy komunikat w kolejce:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Jednocześnie można wysłać wiele komunikatów przy użyciu `ICollector` parametru lub. `IAsyncCollector` Tutaj znajduje C# się kod skryptu, który wysyła wiele komunikatów, jeden z danymi żądania HTTP i jedną z ustalonymi wartościami:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie pokazano powiązanie wyzwalacza HTTP w pliku *Function. JSON* oraz [funkcja języka JavaScript](functions-reference-node.md) , która używa powiązania. Funkcja tworzy element kolejki dla każdego odebranego żądania HTTP.

Oto *function.json* pliku:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Poniżej przedstawiono kod JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Można wysłać wiele komunikatów jednocześnie, definiując tablicę komunikatów dla `myQueueItem` powiązania danych wyjściowych. Poniższy kod JavaScript wysyła dwie komunikaty kolejki z ustalonymi wartościami dla każdego odebranego żądania HTTP.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Dane wyjściowe — przykładzie w języku Java

 Poniższy przykład pokazuje funkcję języka Java, która tworzy komunikat w kolejce dla wyzwalane przez żądanie HTTP.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "AzureWebJobsStorage")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding&lt;String&gt; result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

W [bibliotece środowiska uruchomieniowego funkcji Java](/java/api/overview/azure/functions/runtime)Użyj `@QueueOutput` adnotacji w parametrach, których wartość zostanie zapisywana w usłudze queue storage.  Typ parametru musi być `OutputBinding<T>`, gdzie T jest dowolnego natywnego języka Java typu obiektu typu POJO.

### <a name="output---python-example"></a>Dane wyjściowe — przykład w języku Python

Poniższy przykład ilustruje sposób wyprowadzania pojedynczych i wielu wartości do kolejek magazynu. Konfiguracja wymagana dla *funkcji Function. JSON* jest taka sama jak w obu kierunkach.

Powiązanie kolejki magazynu jest zdefiniowane w *funkcji Function. JSON* , w której *Typ* jest `queue`ustawiony na.

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
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Aby ustawić pojedynczy komunikat w kolejce, należy przekazać jedną wartość do `set` metody.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Aby utworzyć wiele komunikatów w kolejce, zadeklaruj parametr jako odpowiedni typ listy i przekaż tablicę wartości (które pasują do typu listy) do `set` metody.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [ C# bibliotekach klas](functions-dotnet-class-library.md)Użyj klasy [queueattribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Ten atrybut ma zastosowanie do `out` parametru lub wartości zwracanej funkcji. Konstruktor atrybutu przyjmuje nazwę kolejki, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Możesz ustawić `Connection` właściwość, aby określić konto magazynu do użycia, jak pokazano w następującym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz wyzwalacz-atrybuty.

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Queue` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `queue`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa `out`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. Ustaw, `$return` aby odwoływać się do zwracanej wartości funkcji.|
|**queueName** |**QueueName** | Nazwa kolejki. |
|**połączenia** | **połączenia** |Nazwa ustawienia aplikacji, które zawiera parametry połączenia magazynu, które będą używane dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", w tym miejscu możesz określić tylko resztę nazwy. Jeśli na przykład ustawisz `connection` opcję "Moja magazyn", środowisko uruchomieniowe funkcji wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage". Jeśli pozostawisz `connection` puste, środowisko uruchomieniowe funkcji używa domyślnych parametrów połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W C# skryptach i C# Napisz pojedynczy komunikat z kolejki przy użyciu parametru metody, takiego jak `out T paramName`. W C# skrypcie `paramName` jest `name` wartością określoną we właściwości *Function. JSON*. Można użyć typu zwracanego metody zamiast `out` parametru i `T` może być dowolny z następujących typów:

* Obiekt możliwy do serializacji jako plik JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli próbujesz powiązać z `CloudQueueMessage` i otrzymać komunikat o błędzie, upewnij się, że masz odwołanie do [odpowiedniej wersji zestawu SDK magazynu](#azure-storage-sdk-version-in-functions-1x).

W C# programie C# i skrypcie Napisz wiele komunikatów w kolejce przy użyciu jednego z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

W funkcjach języka JavaScript `context.bindings.<name>` służy do uzyskiwania dostępu do komunikatu kolejki wyjściowej. Dla ładunku elementu kolejki można użyć ciągu lub obiektu możliwego do serializacji w formacie JSON.


## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie |  Tematy pomocy |
|---|---|
| Kolejka | [Kody błędów kolejki](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Obiekt BLOB, tabela, kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt BLOB, tabela, kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>Ustawienia host.JSON

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersji 2.x. Przykład pliku host.json poniżej zawiera tylko ustawienia 2.x wersji dla tego powiązania. Aby uzyskać więcej informacji na temat globalne ustawienia konfiguracji w wersji 2.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions w wersji 2.x](functions-host-json.md).

> [!NOTE]
> Odwołanie host.json w funkcjach 1.x, zobacz [dokumentacja pliku host.JSON dla usługi Azure Functions 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8
        }
    }
}
```


|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Maksymalny interwał między sondami kolejki. Wartość minimalna to 00:00:00.100 (100 ms) i zwiększa się do 00:01:00 (1 min). |
|visibilityTimeout|00:00:00|Przedział czasu między ponownymi próbami, gdy przetwarzanie komunikatu kończy się niepowodzeniem. |
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe funkcji jednocześnie pobiera i przetwarza równolegle. Gdy przetwarzana liczba jest w dół `newBatchThreshold`, środowisko uruchomieniowe pobiera kolejną partię i uruchamia przetwarzanie tych komunikatów. Dlatego Maksymalna liczba współbieżnych komunikatów przetwarzanych na funkcję to `batchSize` Plus. `newBatchThreshold` Ten limit dotyczy osobno każdej funkcji wyzwalanej przez kolejkę. <br><br>Aby zapobiec równoległemu wykonywaniu komunikatów odebranych w jednej kolejce, można ustawić wartość `batchSize` 1. To ustawienie eliminuje jednak współbieżność tylko wtedy, gdy aplikacja funkcji działa na jednej maszynie wirtualnej. Jeśli aplikacja funkcji jest skalowana do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Wartość maksymalna `batchSize` to 32. |
|maxDequeueCount|5|Liczba prób przetworzenia komunikatu przed przeniesieniem go do kolejki trującej.|
|newBatchThreshold|batchSize/2|Gdy liczba przetwarzanych komunikatów współbieżnie przyjdzie do tego numeru, środowisko uruchomieniowe pobiera kolejną partię.|

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Przejdź do samouczka korzystającego z powiązania wyjściowego magazynu kolejki](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
