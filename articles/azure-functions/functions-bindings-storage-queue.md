---
title: Powiązania magazynu dla usługi Azure Functions dla kolejki platformy Azure
description: Dowiedz się, jak używać wyzwalacz usługi Azure Queue storage i powiązanie w usłudze Azure Functions danych wyjściowych.
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: usługi Azure functions, funkcje, przetwarzanie zdarzeń, obliczanie dynamiczne, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 2f6b693e11ccbb759b59c949b24690e8a2054f94
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132406"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Powiązania magazynu dla usługi Azure Functions dla kolejki platformy Azure

W tym artykule wyjaśniono, jak pracować z usługą Azure Queue storage powiązań w usłudze Azure Functions. Usługi Azure Functions obsługuje wyzwalanie i danych wyjściowych powiązania dla kolejki.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania kolejki magazynu znajdują się w [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania kolejki magazynu znajdują się w [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu znajduje się w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues) repozytorium GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="encoding"></a>Kodowanie
Oczekiwane funkcje *base64* ciąg zakodowany w formacie. Wszelkie zmiany typu kodowania (w celu przygotowywania danych jako *base64* ciąg zakodowany w formacie) muszą być implementowane w usłudze wywoływania.

## <a name="trigger"></a>Wyzwalacz

Użyj wyzwalacza kolejki, aby uruchomić funkcję, po otrzymaniu nowego elementu w kolejce. Komunikat w kolejce jest dostarczany jako dane wejściowe funkcji.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) który sonduje `myqueue-items` kolejki i zapisuje dziennik za każdym razem, jest przetwarzany element kolejki.

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

W poniższym przykładzie pokazano wyzwalacz kolejki, powiązanie w *function.json* pliku i [skrypt języka C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Ankiety funkcja `myqueue-items` kolejki i zapisuje dziennik za każdym razem, jest przetwarzany element kolejki.

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

[Użycia](#trigger---usage) sekcji opisano `myQueueItem`, który jest nazwany przez `name` właściwość function.json.  [Komunikatu sekcji metadanych](#trigger---message-metadata) wyjaśniono wszystkie zmienne, wyświetlane.

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie pokazano wyzwalacz kolejki, powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Ankiety funkcja `myqueue-items` kolejki i zapisuje dziennik za każdym razem, jest przetwarzany element kolejki.

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
> Parametr name odzwierciedla jako `context.bindings.<name>` w kodzie JavaScript, który zawiera ładunek elementu kolejki. Ten ładunek również jest przekazywany jako drugi parametr funkcji.

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

[Użycia](#trigger---usage) sekcji opisano `myQueueItem`, który jest nazwany przez `name` właściwość function.json.  [Komunikatu sekcji metadanych](#trigger---message-metadata) wyjaśniono wszystkie zmienne, wyświetlane.

### <a name="trigger---java-example"></a>Wyzwalacz - przykładzie w języku Java

W poniższym przykładzie Java pokazano wyzwalacz kolejki magazynu funkcji, które rejestruje wyzwalane komunikat umieszczony w kolejce `myqueuename`.

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

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyć następujących atrybutów, aby skonfigurować wyzwalacz kolejki:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Konstruktor atrybutu ma nazwę kolejki, aby monitorować, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Możesz ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

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

  Udostępnia inny sposób, aby określić konto magazynu do użycia. Konstruktor przyjmuje nazwę ustawienia aplikacji zawierającego parametry połączenia magazynu. Ten atrybut można zastosować na parametrów, metody lub poziomie klasy. Poniższy przykład pokazuje poziom klasy i metody:

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

Konto magazynu do użycia, jest określana w następującej kolejności:

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
|**direction**| Nie dotyczy | W *function.json* tylko plik. Musi być równa `in`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy |Nazwa zmiennej, która zawiera ładunek elementu kolejki w kodzie funkcji.  |
|**queueName** | **QueueName**| Nazwa kolejki do sondowania. |
|**połączenia** | **połączenia** |Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "Mój_magazyn", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia magazynu w ustawieniach aplikacji, który nosi nazwę `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie

W języku C# i skrypt języka C#, uzyskiwać dostęp do wiadomości za pomocą parametru metody, takie jak `string paramName`. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. Możesz powiązać dowolny z następujących typów:

* Obiekt — środowisko uruchomieniowe usługi Functions deserializuje ładunek JSON do wystąpienia klasy dowolnego zdefiniowane w kodzie. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Jeśli próbujesz powiązać `CloudQueueMessage` i komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

W języku JavaScript, należy użyć `context.bindings.<name>` dostępu ładunek elementu kolejki do. Jeśli ładunku JSON, jest ona przeprowadzona deserializacja obiektu.

## <a name="trigger---message-metadata"></a>Wyzwalacz — komunikat metadanych

Wyzwalacz kolejki udostępnia wiele [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości może służyć jako część wyrażenia wiązania w pozostałych powiązaniach lub jako parametry w kodzie. Są to właściwości [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Ładunek kolejki (jeśli jest prawidłowym ciągiem). Jeśli kolejka komunikatów ładunek w postaci ciągu, `QueueTrigger` ma taką samą wartość jak zmienna o nazwie określonej przez `name` właściwość *function.json*.|
|`DequeueCount`|`int`|Liczba przypadków, gdy ten komunikat został usuniętej.|
|`ExpirationTime`|`DateTimeOffset`|Czas wygaśnięcia komunikatu.|
|`Id`|`string`|Identyfikator kolejki komunikatów.|
|`InsertionTime`|`DateTimeOffset`|Godzina dodania komunikatu do kolejki.|
|`NextVisibleTime`|`DateTimeOffset`|Czas, który następnie będzie widoczny komunikat.|
|`PopReceipt`|`string`|Pop otrzymaniu komunikatu.|

## <a name="trigger---poison-messages"></a>Wyzwalacz - skażone wiadomości

W przypadku niepowodzenia funkcji wyzwalacza kolejki usługi Azure Functions ponawia próbę funkcja maksymalnie pięć razy dla danej kolejki wiadomości, czyli pierwszej próby. W przypadku awarii wszystkich pięciu próbach, środowisko uruchomieniowe usługi functions dodaje komunikat do kolejki o nazwie  *&lt;originalqueuename >-skażone*. Można napisać, że funkcja przetwarzania komunikatów z kolejki zanieczyszczone przez rejestrowania ich lub wysyłania powiadomienia, że ręczne uwagi jest wymagana.

Aby obsługiwać komunikaty zanieczyszczone ręcznie, zapoznaj się z [dequeueCount](#trigger---message-metadata) z komunikatu w kolejce.

## <a name="trigger---polling-algorithm"></a>Wyzwalacz — algorytm sondowania

Wyzwalacz kolejki implementuje losowe wykładniczego wycofywania algorytmu zmniejszają efekt bezczynności kolejki sondowania kosztów transakcji magazynu.  Gdy komunikat zostanie znaleziony, środowisko uruchomieniowe czeka dwóch sekund, a następnie sprawdza, czy inny komunikat; Jeśli żaden komunikat nie zostanie znaleziony, czeka około czterech sekund przed ponowną próbą. Po kolejnych nieudanych prób można pobrać komunikatu w kolejce czas oczekiwania stale rośnie, dopóki nie osiągnie maksymalny czas oczekiwania, która domyślnie na jedną minutę. Maksymalny czas oczekiwania jest konfigurowane za pośrednictwem `maxPollingInterval` właściwość [pliku host.json](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Wyzwalacz - współbieżności

W przypadku wielu komunikatów w kolejce oczekiwania wyzwalacz kolejki pobiera partię komunikatów i wywołuje funkcję wystąpień jednocześnie w celu ich przetwarzania. Domyślnie rozmiar partii to 16. Gdy liczba przetwarzanych pobiera w dół do 8, środowisko uruchomieniowe pobiera inna partia i rozpoczyna przetwarzanie tych komunikatów. Dlatego maksymalną liczbę równoczesnych komunikatów przetwarzanych dla każdej funkcji na jednej maszynie wirtualnej (VM) to 24. Ten limit dotyczy oddzielnie poszczególnych funkcji wyzwalanej przez kolejkę na każdej maszynie Wirtualnej. Jeśli aplikacja funkcji skalowania do wielu maszyn wirtualnych, każda maszyna wirtualna będzie poczekaj, aż wyzwalacze i próba uruchomienia funkcji. Na przykład aplikacja funkcji skalowania do 3 maszyn wirtualnych, domyślne maksymalną liczbę równoczesnych wystąpień jednej funkcji wyzwalanej przez kolejkę jest 72.

Rozmiar partii i próg w celu uzyskania nowej partii są konfigurowane w [pliku host.json](functions-host-json.md#queues). Jeśli chcesz ograniczyć równoległe wykonywanie funkcji wyzwalanej przez kolejkę w aplikacji funkcji, można ustawić rozmiar partii 1. To ustawienie pozwala wyeliminować współbieżności tylko tak długo, jak aplikacja funkcji zostanie uruchomiona na jednej maszynie wirtualnej (VM). 

Wyzwalacz kolejki automatycznie zapobiega funkcji przetwarzanie komunikatu w kolejce wiele razy; funkcje nie mają do zapisania jako idempotentne.

## <a name="trigger---hostjson-properties"></a>Wyzwalacz — właściwości host.json

[Host.json](functions-host-json.md#queues) plik zawiera ustawienia, które kontrolują zachowanie wyzwalacz kolejki. Zobacz [ustawienia host.json](#hostjson-settings) sekcji, aby uzyskać szczegółowe informacje dotyczące dostępnych ustawień.

## <a name="output"></a>Dane wyjściowe

Użyj danych wyjściowych z magazynu Azure Queue, powiązanie, aby pisać wiadomości do kolejki.

## <a name="output---example"></a>Dane wyjściowe — przykład

Zobacz przykład specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład w języku C#

W poniższym przykładzie przedstawiono [funkcja języka C#](functions-dotnet-class-library.md) tworząca komunikatu w kolejce dla każdego żądania HTTP odebrane.

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

W poniższym przykładzie pokazano wyzwalacz HTTP powiązanie w *function.json* pliku i [skrypt języka C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja tworzy element kolejki o **CustomQueueMessage** obiekt ładunku dla każdego żądania HTTP odebrane.

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

Oto C# script kod, który tworzy pojedynczy komunikat z kolejki:

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

Wysłać wiele wiadomości jednocześnie przy użyciu `ICollector` lub `IAsyncCollector` parametru. W tym C# kod skryptu, który wysyła wiele wiadomości: jedna z dane żądania HTTP, a druga zakodowanych wartości:

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

W poniższym przykładzie pokazano wyzwalacz HTTP powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) powiązania, który używa. Funkcja tworzy element kolejki dla każdego żądania HTTP odebrane.

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

Wysłać wiele wiadomości jednocześnie, definiując tablica komunikatów `myQueueItem` powiązania danych wyjściowych. Następujący kod JavaScript wysyła dwóch komunikatów w kolejce za pomocą zakodowanych wartości dla każdego żądania HTTP odebrane.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

### <a name="output---java-example"></a>Dane wyjściowe — przykładzie w języku Java

 Poniższy przykład przedstawia funkcję języka Java, która tworzy komunikatu w kolejce dla po wyzwoleniu przez żądanie HTTP.

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

W [Java funkcje biblioteki środowiska uruchomieniowego](/java/api/overview/azure/functions/runtime), użyj `@QueueOutput` adnotacji w parametrach, którego wartość będzie zapisany do usługi Queue storage.  Typ parametru musi być `OutputBinding<T>`, gdzie T jest dowolnego natywnego języka Java typu obiektu typu POJO.


## <a name="output---attributes"></a>Dane wyjściowe — atrybuty

W [bibliotek klas języka C#](functions-dotnet-class-library.md), użyj [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Ten atrybut ma zastosowanie do `out` parametru lub zwracanej wartości funkcji. Konstruktor atrybutu ma nazwę kolejki, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Możesz ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Aby uzyskać kompletny przykład, zobacz [dane wyjściowe — przykład w języku C#](#output---c-example).

Możesz użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji zobacz wyzwalacza — atrybuty.

## <a name="output---configuration"></a>Dane wyjściowe — Konfiguracja

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w *function.json* pliku i `Queue` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | Musi być równa `queue`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal.|
|**direction** | Nie dotyczy | Musi być równa `out`. Ta właściwość jest ustawiana automatycznie po utworzeniu wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. Ustaw `$return` odwoływać się do wartości zwracanej funkcji.|
|**queueName** |**QueueName** | Nazwa kolejki. |
|**połączenia** | **połączenia** |Nazwa ustawienia aplikacji zawierającego parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "Mój_magazyn", środowisko uruchomieniowe usługi Functions wyszukuje ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli pozostawisz `connection` pusta, środowisko uruchomieniowe usługi Functions korzysta z domyślne parametry połączenia magazynu w ustawieniach aplikacji, który nosi nazwę `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie

W języku C# i skrypt języka C#, należy napisać pojedynczy komunikat z kolejki za pomocą parametru metody, takie jak `out T paramName`. W języku C# `paramName` jest wartością określoną w `name` właściwość *function.json*. Można użyć typ zwracany metody zamiast `out` parametru i `T` może być dowolną z następujących typów:

* Obiekt możliwy do serializacji w formacie JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli próbujesz powiązać `CloudQueueMessage` i komunikat o błędzie, upewnij się, że odwołanie do [poprawnej wersji zestawu SDK usługi Storage](#azure-storage-sdk-version-in-functions-1x).

W języku C# i skrypt języka C# należy napisać wielu komunikatów w kolejce przy użyciu jednej z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

W funkcji języka JavaScript za pomocą `context.bindings.<name>` dostępu do danych wyjściowych kolejki wiadomości. Ciąg lub obiekt serializacji JSON służy do ładunku elementu kolejki.


## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie |  Odwołanie |
|---|---|
| Kolejka | [Kody błędów kolejki](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Obiekt blob, tabela, kolejka | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt blob, tabela, kolejka |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

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
|maxPollingInterval|00:00:01|Maksymalny interwał między sondowaniami kolejki. Minimalna liczba to 00:00:00.100 (100 ms). |
|visibilityTimeout|00:00:00|Odstęp czasu między kolejnymi próbami podczas przetwarzania komunikatu nie powiedzie się. |
|batchSize|16|Liczba komunikatów w kolejce, które środowisko uruchomieniowe usługi Functions pobiera jednocześnie przetwarzane równolegle. Gdy liczba przetwarzanych wyświetlona w dół `newBatchThreshold`, środowisko uruchomieniowe inna partia pobiera i uruchamia przetwarzanie tych wiadomości. Dlatego jest maksymalna liczba współbieżnych komunikatów przetwarzanych dla każdej funkcji `batchSize` oraz `newBatchThreshold`. Ten limit dotyczy oddzielnie poszczególnych funkcji wyzwalanej przez kolejkę. <br><br>Jeśli chcesz uniknąć wykonywania równoległego dla wiadomości otrzymanych w jednej kolejki można ustawić `batchSize` 1. Jednak to ustawienie pozwala wyeliminować współbieżności tylko tak długo, jak aplikacja funkcji zostanie uruchomiona na jednej maszynie wirtualnej (VM). Jeśli aplikacja funkcji skalowania do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej przez kolejkę.<br><br>Maksymalna `batchSize` wynosi 32. |
|maxDequeueCount|5|Liczba prób przetwarzania komunikatu przed jego przeniesieniem do skażone kolejki.|
|newBatchThreshold|batchSize/2|Zawsze, gdy liczba komunikatów przetwarzanych jednocześnie uzyskuje na ten numer, środowisko uruchomieniowe pobiera inna partia.|

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat usługi Azure functions, wyzwalaczami i powiązaniami](functions-triggers-bindings.md)

<!--
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Queue storage trigger](functions-create-storage-queue-triggered-function.md)
-->

> [!div class="nextstepaction"]
> [Przejdź do samouczka, który korzysta z usługi Queue storage powiązania danych wyjściowych](functions-integrate-storage-queue-output-binding.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
