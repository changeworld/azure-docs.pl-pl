---
title: Azure kolejki magazynu powiązania dla usługi Azure Functions
description: Zrozumienie, jak używać magazynu kolejek Azure wyzwalacza i powiązania w usługi Azure Functions wyjściowego.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: funkcje usługi Azure, funkcje, przetwarzania zdarzeń, dynamiczne obliczeń niekorzystającą architektury
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/23/2017
ms.author: tdykstra
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71adccabc0778e2765c574f3714aab0ed0179deb
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724473"
---
# <a name="azure-queue-storage-bindings-for-azure-functions"></a>Azure kolejki magazynu powiązania dla usługi Azure Functions

W tym artykule opisano sposób pracy z usługi Azure Functions powiązania magazynu kolejek Azure. Usługi Azure Functions obsługuje uruchomić i dane wyjściowe powiązania dla kolejki.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakiety — funkcje 1.x

Powiązania kolejki magazynu jest dostępny w [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet w wersji 2.x. Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Queue) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Pakiety — funkcje 2.x

Powiązania kolejki magazynu jest dostępny w [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) pakietu NuGet w wersji 3.x. Kod źródłowy dla pakietu jest w [zestaw sdk zadań webjob azure](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Storage/Queue) repozytorium GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="trigger"></a>Wyzwalacz

Użyj wyzwalacza kolejki, aby uruchomić funkcję, po odebraniu nowego elementu w kolejce. Komunikat z kolejki jest podana jako dane wejściowe do funkcji.

## <a name="trigger---example"></a>Wyzwalacz — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#trigger---c-example)
* [Skryptu C# (csx)](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Wyzwalacz — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) który sonduje `myqueue-items` kolejki i zapisuje dziennik za każdym razem, jest przetwarzany element kolejki.

```csharp
public static class QueueFunctions
{
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

### <a name="trigger---c-script-example"></a>Wyzwalacz — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacz kolejki powiązanie w *function.json* pliku i [skryptu C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja sond `myqueue-items` kolejki i zapisuje dziennik za każdym razem, jest przetwarzany element kolejki.

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

Oto kod skryptu C#:

```csharp
#r "Microsoft.WindowsAzure.Storage"

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
    TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem.AsString}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

[Użycia](#trigger---usage) sekcji opisano `myQueueItem`, które nosi nazwę przez `name` właściwości w function.json.  [Komunikatu sekcji metadanych](#trigger---message-metadata) opisano wszystkie zmienne, wyświetlane.

### <a name="trigger---javascript-example"></a>Wyzwalacz — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacz kolejki powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja sond `myqueue-items` kolejki i zapisuje dziennik za każdym razem, jest przetwarzany element kolejki.

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

Oto kod JavaScript:

```javascript
module.exports = function (context) {
    context.log('Node.js queue trigger function processed work item', context.bindings.myQueueItem);
    context.log('queueTrigger =', context.bindingData.queueTrigger);
    context.log('expirationTime =', context.bindingData.expirationTime);
    context.log('insertionTime =', context.bindingData.insertionTime);
    context.log('nextVisibleTime =', context.bindingData.nextVisibleTime);
    context.log('id =', context.bindingData.id);
    context.log('popReceipt =', context.bindingData.popReceipt);
    context.log('dequeueCount =', context.bindingData.dequeueCount);
    context.done();
};
```

[Użycia](#trigger---usage) sekcji opisano `myQueueItem`, które nosi nazwę przez `name` właściwości w function.json.  [Komunikatu sekcji metadanych](#trigger---message-metadata) opisano wszystkie zmienne, wyświetlane.

## <a name="trigger---attributes"></a>Wyzwalacz — atrybuty
 
W [bibliotek klas C#](functions-dotnet-class-library.md), umożliwia skonfigurowanie wyzwalacz kolejki następujące atrybuty:

* [QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueTriggerAttribute.cs)

  Konstruktor atrybutu ma nazwę kolejki, aby monitorować, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      TraceWriter log)
  {
      ...
  }
  ```

  Można ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      TraceWriter log)
  {
      ....
  }
  ```
 
  Pełny przykład, zobacz [wyzwalacza — przykład C#](#trigger---c-example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Udostępnia innym sposobem określania konta magazynu do użycia. Konstruktor przyjmuje nazwę ustawienia aplikacji, która zawiera parametry połączenia magazynu. Ten atrybut można stosować na poziomie klasy parametrów, metody lub. W poniższym przykładzie przedstawiono poziom klasy i metody:

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

Konta magazynu do użycia jest określany w następującej kolejności:

* `QueueTrigger` Atrybutu `Connection` właściwości.
* `StorageAccount` Atrybut zastosowany do tego samego parametru jako `QueueTrigger` atrybutu.
* `StorageAccount` Atrybut zastosowany do funkcji.
* `StorageAccount` Atrybut zastosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsStorage".

## <a name="trigger---configuration"></a>Wyzwalacz — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `QueueTrigger` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy| należy wybrać opcję `queueTrigger`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**direction**| Nie dotyczy | W *function.json* tylko plików. należy wybrać opcję `in`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nie dotyczy |Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji.  | 
|**queueName** | **QueueName**| Nazwa kolejki do sondowania. | 
|**Połączenia** | **Połączenia** |Nazwa ustawienia aplikacji, która zawiera parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "MyStorage" środowisko uruchomieniowe Functions szuka ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli opuścisz `connection` pusta, środowisko uruchomieniowe Functions używa domyślnego ciągu połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>Wyzwalacz — użycie
 
W języku C# i skryptu C#, dostęp do danych komunikatów za pomocą parametru metody, takie jak `string paramName`. W języku C# skryptu `paramName` jest wartością określoną w `name` właściwość *function.json*. Można powiązać żadnego z następujących typów:

* Obiekt - funkcji środowiska uruchomieniowego deserializuje ładunek JSON do wystąpienia klasy dowolnego zdefiniowane w kodzie. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Jeśli spróbujesz powiązać `CloudQueueMessage` i komunikat o błędzie, upewnij się, że odwołanie do [poprawną wersję zestawu SDK usługi Magazyn](#azure-storage-sdk-version-in-functions-1x).

W języku JavaScript, użyj `context.bindings.<name>` do ładunku elementu kolejki. Jeśli ładunek JSON, jest deserializacji do obiektu.

## <a name="trigger---message-metadata"></a>Wyzwalacz - metadanych wiadomości

Wyzwalacz kolejki udostępnia wiele [właściwości metadanych](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Te właściwości mogą służyć jako część wyrażenia powiązania w pozostałych powiązaniach lub parametrów w kodzie. Są to właściwości [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Ładunek kolejki (jeśli prawidłowy ciąg). Jeśli kolejka komunikatów ładunek w postaci ciągu, `QueueTrigger` ma taką samą wartość jak zmienna o nazwie `name` właściwości w *function.json*.|
|`DequeueCount`|`int`|Ile razy ten komunikat został usuniętej.|
|`ExpirationTime`|`DateTimeOffset`|Czas wygaśnięcia wiadomości.|
|`Id`|`string`|Identyfikator kolejki wiadomości.|
|`InsertionTime`|`DateTimeOffset`|Godzina dodania wiadomości do kolejki.|
|`NextVisibleTime`|`DateTimeOffset`|Czas, który następnie będzie widoczny komunikat.|
|`PopReceipt`|`string`|Pop odbieranie komunikatu.|

## <a name="trigger---poison-messages"></a>Wyzwalacz - skażone wiadomości

Gdy funkcja wyzwalacza kolejki nie powiodło się, usługi Azure Functions ponowi próbę funkcji maksymalnie pięć razy dla danej kolejki wiadomości, czyli pierwszej próby. Jeśli nie wszystkie próby pięć, środowisko uruchomieniowe functions dodaje komunikat do kolejki o nazwie  *&lt;originalqueuename >-skażone*. Można wpisać funkcji do przetwarzania komunikatów z kolejki skażone przez rejestrowania ich lub wysyłania powiadomienia tej uwagi ręczne jest wymagane.

Do obsługi wiadomości, ręcznie, sprawdź [dequeueCount](#trigger---message-metadata) kolejki wiadomości.

## <a name="trigger---polling-algorithm"></a>Wyzwalacz — algorytm sondowania

Wyzwalacz kolejki zaimplementowano losowe wykładniczej wycofywania algorytmu celu ograniczenia wpływu bezczynności kolejki sondowania kosztów transakcji magazynu.  Po znalezieniu komunikatu środowisko uruchomieniowe oczekuje dwóch sekund i następnie sprawdza, czy kolejną wiadomość; Po znalezieniu żaden komunikat czeka około czterech sekund przed ponowną próbą. Po kolejnych nieudanych prób uzyskanie komunikatu w kolejce czas oczekiwania w dalszym ciągu zwiększyć, dopóki nie osiągnie maksymalny czas oczekiwania, domyślnie na jedną minutę. Maksymalny czas oczekiwania jest można konfigurować za pomocą `maxPollingInterval` właściwości w [pliku host.json](functions-host-json.md#queues).

## <a name="trigger---concurrency"></a>Wyzwalacz - współbieżności

W przypadku wielu wiadomości w kolejce oczekiwania wyzwalacza kolejki pobiera partię komunikatów i wywołuje wystąpień funkcji jednocześnie do ich przetworzenia. Domyślnie rozmiar partii to 16. Gdy liczba przetwarzanych pobiera w dół do 8, środowisko uruchomieniowe pobiera inna partia i rozpoczyna przetwarzanie tych wiadomości. Dlatego maksymalną liczbę równoczesnych komunikatów przetwarzanych dla każdej funkcji w jednej maszyny wirtualnej (VM) jest 24. Ten limit dotyczy oddzielnie każdej funkcji wyzwalanych kolejki na każdej maszynie Wirtualnej. Jeśli funkcja aplikacji może obsłużyć się wiele maszyn wirtualnych, każda maszyna wirtualna zostanie poczekaj wyzwalaczy i próba uruchomienia funkcji. Na przykład aplikacji funkcji skaluje się do 3 maszyn wirtualnych, domyślne maksymalną liczbę równoczesnych wystąpień jednej funkcji wyzwalanych kolejki jest 72.

Rozmiar partii i próg uzyskania nowej instancji są konfigurowane w [pliku host.json](functions-host-json.md#queues). Jeśli chcesz zminimalizować równoległe wykonywanie funkcji wyzwalanych przez kolejki w aplikacji funkcji, można ustawić rozmiar partii do 1. To ustawienie eliminuje współbieżności tylko tak długo, jak działa funkcja aplikacji na jednej maszynie wirtualnej (VM). 

Wyzwalacz kolejki automatycznie uniemożliwia funkcji przetwarzania komunikatu w kolejce wiele razy; nie masz funkcje do zapisania jako idempotentności.

## <a name="trigger---hostjson-properties"></a>Wyzwalacz - host.json właściwości

[Host.json](functions-host-json.md#queues) plik zawiera ustawienia, które kontrolują zachowanie wyzwalacza kolejki.

[!INCLUDE [functions-host-json-queues](../../includes/functions-host-json-queues.md)]

## <a name="output"></a>Dane wyjściowe

Za pomocą raportu magazynu kolejek Azure powiązanie do zapisywania wiadomości do kolejki.

## <a name="output---example"></a>OUTPUT — przykład

Zapoznaj się z przykładem specyficzny dla języka:

* [C#](#output---c-example)
* [Skryptu C# (csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Dane wyjściowe — przykład C#

W poniższym przykładzie przedstawiono [C# funkcja](functions-dotnet-class-library.md) tworzącą komunikatu w kolejce dla każdego żądania HTTP odebrane.

```csharp
[StorageAccount("AzureWebJobsStorage")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  TraceWriter log)
    {
        log.Info($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

### <a name="output---c-script-example"></a>Dane wyjściowe — przykładowy skrypt w języku C#

W poniższym przykładzie przedstawiono wyzwalacza HTTP powiązanie w *function.json* pliku i [skryptu C# (csx)](functions-reference-csharp.md) kodu korzystającego z powiązania. Funkcja tworzy element kolejki o **CustomQueueMessage** ładunek obiekt dla każdego żądania HTTP odebrane.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Oto C# kodu skryptu, który tworzy pojedynczy komunikat z kolejki:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, TraceWriter log)
{
    return input;
}
```

Wiele wiadomości można wysyłać jednocześnie za pomocą `ICollector` lub `IAsyncCollector` parametru. W tym kodu C# skrypt wysyłanej przez wiele komunikatów, jeden z danymi żądania HTTP i jeden z wpisaną na stałe wartości:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    TraceWriter log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

### <a name="output---javascript-example"></a>Dane wyjściowe — przykład JavaScript

W poniższym przykładzie przedstawiono wyzwalacza HTTP powiązanie w *function.json* pliku i [funkcji JavaScript](functions-reference-node.md) używającą powiązania. Funkcja tworzy element kolejki dla każdego żądania HTTP odebrane.

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
      "connection": "MyStorageConnectionAppSetting",
    }
  ]
}
``` 

[Konfiguracji](#output---configuration) sekcji opisano te właściwości.

Oto kod JavaScript:

```javascript
module.exports = function (context, input) {
    context.done(null, input.body);
};
```

Wiele wiadomości można wysyłać na raz, definiując tablicą komunikat dla `myQueueItem` powiązania wyjściowego. Następujący kod JavaScript wysyła dwa komunikaty kolejki z wpisaną na stałe wartości dla każdego żądania HTTP odebrane.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

## <a name="output---attributes"></a>Dane wyjściowe — atrybuty
 
W [bibliotek klas C#](functions-dotnet-class-library.md), użyj [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/QueueAttribute.cs).

Ten atrybut ma zastosowanie do `out` parametrów lub wartości zwracanej przez funkcję. Konstruktor atrybutu ma nazwę kolejki, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Można ustawić `Connection` właściwości w celu określenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  TraceWriter log)
{
    ...
}
```

Pełny przykład, zobacz [dane wyjściowe — przykład C#](#output---c-example).

Można użyć `StorageAccount` atrybutu, aby określić konto magazynu na poziomie klasy, metody lub parametru. Aby uzyskać więcej informacji, zobacz [wyzwalacza — atrybuty](#trigger---attribute).

## <a name="output---configuration"></a>OUTPUT — Konfiguracja

W poniższej tabeli opisano powiązania właściwości konfiguracyjne, które można ustawić w *function.json* pliku i `Queue` atrybutu.

|Właściwość Function.JSON | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**type** | Nie dotyczy | należy wybrać opcję `queue`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure.|
|**direction** | Nie dotyczy | należy wybrać opcję `out`. Ta właściwość ma wartość automatycznie, podczas tworzenia wyzwalacza w portalu Azure. |
|**Nazwa** | Nie dotyczy | Nazwa zmiennej, która reprezentuje kolejkę w kodzie funkcji. Ustaw `$return` odwoływać się do wartości zwracane funkcji.| 
|**queueName** |**QueueName** | Nazwa kolejki. | 
|**Połączenia** | **Połączenia** |Nazwa ustawienia aplikacji, która zawiera parametry połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji rozpoczyna się od "AzureWebJobs", można określić tylko w pozostałej części nazwy w tym miejscu. Na przykład jeśli ustawisz `connection` do "MyStorage" środowisko uruchomieniowe Functions szuka ustawienie aplikacji o nazwie "AzureWebJobsMyStorage." Jeśli opuścisz `connection` pusta, środowisko uruchomieniowe Functions używa domyślnego ciągu połączenia magazynu w ustawieniu aplikacji o nazwie `AzureWebJobsStorage`.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Dane wyjściowe — użycie
 
W języku C# i skryptu C#, należy zapisać pojedynczy komunikat z kolejki przy użyciu parametru metody takie jak `out T paramName`. W języku C# skryptu `paramName` jest wartością określoną w `name` właściwość *function.json*. Można użyć zwracany typ metody zamiast `out` parametru i `T` może być dowolny z następujących typów:

* Obiekt możliwy do serializacji w formacie JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Jeśli spróbujesz powiązać `CloudQueueMessage` i komunikat o błędzie, upewnij się, że odwołanie do [poprawną wersję zestawu SDK usługi Magazyn](#azure-storage-sdk-version-in-functions-1x).

W języku C# i skryptu C# zapisu wielu komunikatów z kolejki przy użyciu jednej z następujących typów: 

* `ICollector<T>` lub `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue)

W funkcji JavaScript, za pomocą `context.bindings.<name>` dostępu do danych wyjściowych kolejki wiadomości. Ciąg lub obiekt możliwy do serializacji JSON można użyć do ładunku elementu kolejki.


## <a name="exceptions-and-return-codes"></a>Wyjątki i kody powrotne

| Powiązanie |  Informacje ogólne |
|---|---|
| Kolejka | [Kody błędów kolejki](https://docs.microsoft.com/rest/api/storageservices/queue-service-error-codes) |
| Obiekt blob, tabeli, kolejki | [Kody błędów magazynu](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Obiekt blob, tabeli, kolejki |  [Rozwiązywanie problemów](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przejdź do szybkiego startu, używającej wyzwalacz kolejki magazynu](functions-create-storage-queue-triggered-function.md)

> [!div class="nextstepaction"]
> [Przejdź do samouczka, który używa magazynu kolejek powiązania wyjściowego](functions-integrate-storage-queue-output-binding.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usługę Azure functions wyzwalaczy i powiązań](functions-triggers-bindings.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage
