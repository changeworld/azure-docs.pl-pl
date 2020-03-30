---
title: Wyzwalacz magazynu kolejki platformy Azure dla funkcji platformy Azure
description: Dowiedz się, jak uruchomić funkcję platformy Azure w miarę zmian danych magazynu usługi Azure Queue.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 74ca984232bef979062221a451d0ee10a6965bc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277377"
---
# <a name="azure-queue-storage-trigger-for-azure-functions"></a>Wyzwalacz magazynu kolejki platformy Azure dla funkcji platformy Azure

Wyzwalacz magazynu kolejki uruchamia funkcję, ponieważ wiadomości są dodawane do magazynu kolejki platformy Azure.

## <a name="encoding"></a>Kodowanie

Funkcje oczekują *base64* zakodowany ciąg. Wszelkie zmiany typu kodowania (w celu przygotowania danych jako ciąg zakodowany *base64)* muszą być zaimplementowane w usłudze wywołującej.

## <a name="example"></a>Przykład

Użyj wyzwalacza kolejki, aby uruchomić funkcję po odebraniu nowego elementu w kolejce. Komunikat w kolejce jest przekazywany do funkcji jako dane wejściowe.

# <a name="c"></a>[C #](#tab/csharp)

Poniższy przykład pokazuje funkcję Języka `myqueue-items` [C#,](functions-dotnet-class-library.md) która sonduje kolejkę i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

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

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

W poniższym przykładzie pokazano powiązanie wyzwalacza kolejki w pliku *function.json* i kod [skryptu C#(csx),](functions-reference-csharp.md) który używa powiązania. Funkcja sonduje `myqueue-items` kolejki i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

Oto kod skryptu języka C#:

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

Sekcja [użycia](#usage) `myQueueItem`wyjaśnia , który `name` jest nazwany przez właściwość w function.json.  [Sekcja metadanych wiadomości](#message-metadata) wyjaśnia wszystkie inne wyświetlane zmienne.

# <a name="javascript"></a>[Javascript](#tab/javascript)

W poniższym przykładzie pokazano powiązanie wyzwalacza kolejki w pliku *function.json* i [funkcję JavaScript,](functions-reference-node.md) która używa powiązania. Funkcja sonduje `myqueue-items` kolejki i zapisuje dziennik za każdym razem, gdy element kolejki jest przetwarzany.

Oto plik *function.json:*

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

W sekcji [konfiguracji](#configuration) opisano te właściwości.

> [!NOTE]
> Parametr name odzwierciedla `context.bindings.<name>` się tak, jak w kodzie JavaScript, który zawiera ładunek elementu kolejki. Ten ładunek jest również przekazywany jako drugi parametr do funkcji.

Oto kod JavaScript:

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

Sekcja [użycia](#usage) `myQueueItem`wyjaśnia , który `name` jest nazwany przez właściwość w function.json.  [Sekcja metadanych wiadomości](#message-metadata) wyjaśnia wszystkie inne wyświetlane zmienne.

# <a name="python"></a>[Python](#tab/python)

W poniższym przykładzie pokazano, jak odczytać komunikat kolejki przekazany do funkcji za pośrednictwem wyzwalacza.

Wyzwalacz kolejki magazynu jest zdefiniowany w pliku `queueTrigger` *function.json,* gdzie *typ* jest ustawiony na .

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

Kod `func.ServiceBusMessage`init *\_.py deklaruje parametr jako , który umożliwia odczytanie komunikatu kolejki w funkcji. _ \__*

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

# <a name="java"></a>[Java](#tab/java)

W poniższym przykładzie języka Java przedstawiono funkcję wyzwalania kolejki `myqueuename`magazynu, która rejestruje wyzwalany komunikat umieszczony w kolejce .

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

 ---

## <a name="attributes-and-annotations"></a>Atrybuty i adnotacje

# <a name="c"></a>[C #](#tab/csharp)

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)użyj następujących atrybutów, aby skonfigurować wyzwalacz kolejki:

* [Atrybut QueueTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueTriggerAttribute.cs)

  Konstruktor atrybutu przyjmuje nazwę kolejki do monitorowania, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items")] string myQueueItem, 
      ILogger log)
  {
      ...
  }
  ```

  Można ustawić `Connection` właściwość, aby określić ustawienie aplikacji, które zawiera ciąg połączenia konta magazynu do użycia, jak pokazano w poniższym przykładzie:

  ```csharp
  [FunctionName("QueueTrigger")]
  public static void Run(
      [QueueTrigger("myqueue-items", Connection = "StorageConnectionAppSetting")] string myQueueItem, 
      ILogger log)
  {
      ....
  }
  ```

  Pełny przykład można znaleźć na [przykładzie](#example).

* [Atrybut StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Umożliwia inny sposób określania konta magazynu, którego chcesz użyć. Konstruktor przyjmuje nazwę ustawienia aplikacji, który zawiera ciąg połączenia magazynu. Atrybut można zastosować na poziomie parametru, metody lub klasy. Poniższy przykład przedstawia poziom klasy i poziom metody:

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

* Właściwość `QueueTrigger` atrybutu. `Connection`
* Atrybut `StorageAccount` zastosowany do tego samego `QueueTrigger` parametru co atrybut.
* Atrybut `StorageAccount` zastosowany do funkcji.
* Atrybut `StorageAccount` stosowany do klasy.
* Ustawienie aplikacji "AzureWebJobsStorage".

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Atrybuty nie są obsługiwane przez skrypt języka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atrybuty nie są obsługiwane przez javascript.

# <a name="python"></a>[Python](#tab/python)

Atrybuty nie są obsługiwane przez Pythona.

# <a name="java"></a>[Java](#tab/java)

Adnotacja `QueueTrigger` daje dostęp do kolejki, która wyzwala funkcję. Poniższy przykład udostępnia komunikat kolejki do `message` funkcji za pośrednictwem parametru.

```java
package com.function;
import com.microsoft.azure.functions.annotation.*;
import java.util.Queue;
import com.microsoft.azure.functions.*;

public class QueueTriggerDemo {
    @FunctionName("QueueTriggerDemo")
    public void run(
        @QueueTrigger(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") String message,
        final ExecutionContext context
    ) {
        context.getLogger().info("Queue message: " + message);
    }
}
```

| Właściwość    | Opis |
|-------------|-----------------------------|
|`name`       | Deklaruje nazwę parametru w podpisie funkcji. Po wyzwoleniu funkcji wartość tego parametru ma zawartość komunikatu kolejki. |
|`queueName`  | Deklaruje nazwę kolejki na koncie magazynu. |
|`connection` | Wskazuje parametry połączenia konta magazynu. |

---

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można `QueueTrigger` ustawić w pliku *function.json* i atrybut.

|właściwość function.json | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Typu** | Nie dotyczy| Musi być `queueTrigger`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal.|
|**Kierunku**| Nie dotyczy | Tylko w pliku *function.json.* Musi być `in`ustawiona na . Ta właściwość jest ustawiana automatycznie podczas tworzenia wyzwalacza w witrynie Azure portal. |
|**Nazwa** | Nie dotyczy |Nazwa zmiennej, która zawiera ładunek elementu kolejki w kodzie funkcji.  |
|**Queuename** | **Queuename**| Nazwa kolejki do sondowania. |
|**Połączenia** | **Połączenia** |Nazwa ustawienia aplikacji, która zawiera ciąg połączenia magazynu do użycia dla tego powiązania. Jeśli nazwa ustawienia aplikacji zaczyna się od "AzureWebJobs", można określić tylko pozostałą część nazwy tutaj. Na przykład jeśli `connection` ustawisz "MyStorage", środowisko wykonawcze Functions wyszukuje ustawienie aplikacji o nazwie "MyStorage". Jeśli pozostawisz `connection` puste, środowisko wykonawcze Functions używa domyślnego ciągu połączenia `AzureWebJobsStorage`Magazyn w ustawieniu aplikacji o nazwie .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Sposób użycia

# <a name="c"></a>[C #](#tab/csharp)

Dostęp do danych wiadomości przy użyciu `string paramName`parametru metody, takiego jak . Można powiązać z dowolnym z następujących typów:

* Obiekt — funkcja środowiska uruchomieniowego deserializes ładunku JSON do wystąpienia dowolnej klasy zdefiniowane w kodzie. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Jeśli spróbujesz `CloudQueueMessage` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="c-script"></a>[Skrypt języka C#](#tab/csharp-script)

Dostęp do danych wiadomości przy użyciu `string paramName`parametru metody, takiego jak . Jest `paramName` to wartość określona `name` we właściwości *function.json*. Można powiązać z dowolnym z następujących typów:

* Obiekt — funkcja środowiska uruchomieniowego deserializes ładunku JSON do wystąpienia dowolnej klasy zdefiniowane w kodzie. 
* `string`
* `byte[]`
* [CloudQueueMessage]

Jeśli spróbujesz `CloudQueueMessage` powiązać i pojawi się komunikat o błędzie, upewnij się, że masz odwołanie do [poprawnej wersji SDK magazynu](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

# <a name="javascript"></a>[Javascript](#tab/javascript)

Ładunek elementu kolejki `context.bindings.<NAME>` jest `<NAME>` dostępny w miejscu, w którym odpowiada nazwie zdefiniowanej w *pliku function.json*. Jeśli ładunek jest JSON, wartość jest deserialized do obiektu.

# <a name="python"></a>[Python](#tab/python)

Dostęp do wiadomości kolejki za pośrednictwem parametru wpisanego jako [QueueMessage](https://docs.microsoft.com/python/api/azure-functions/azure.functions.queuemessage?view=azure-python).

# <a name="java"></a>[Java](#tab/java)

Adnotacja [QueueTrigger](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.queuetrigger?view=azure-java-stable) daje dostęp do komunikatu kolejki, który wyzwolił funkcję.

---

## <a name="message-metadata"></a>Metadane wiadomości

Wyzwalacz kolejki udostępnia kilka [właściwości metadanych](./functions-bindings-expressions-patterns.md#trigger-metadata). Te właściwości mogą służyć jako część wyrażeń wiązania w innych powiązań lub jako parametry w kodzie. Właściwości są członkami [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) klasy.

|Właściwość|Typ|Opis|
|--------|----|-----------|
|`QueueTrigger`|`string`|Ładunek kolejki (jeśli prawidłowy ciąg). Jeśli ładunek wiadomości kolejki `QueueTrigger` jest ciągiem, ma taką `name` samą wartość jak zmienna nazwana przez właściwość w *pliku function.json*.|
|`DequeueCount`|`int`|Liczba desuazji tej wiadomości.|
|`ExpirationTime`|`DateTimeOffset`|Czas wygaśnięcia wiadomości.|
|`Id`|`string`|Identyfikator wiadomości kolejki.|
|`InsertionTime`|`DateTimeOffset`|Czas, w której wiadomość została dodana do kolejki.|
|`NextVisibleTime`|`DateTimeOffset`|Czas, w której wiadomość będzie widoczna.|
|`PopReceipt`|`string`|Potwierdzenie pop wiadomości.|

## <a name="poison-messages"></a>Wiadomości o truciznach

Gdy funkcja wyzwalania kolejki nie powiedzie się, usługa Azure Functions ponawia próbę uruchomienia funkcji do pięciu razy dla danego komunikatu kolejki, w tym pierwszej próby. Jeśli wszystkie pięć prób zakończy się niepowodzeniem, środowisko wykonawcze funkcji dodaje komunikat do kolejki o nazwie * &lt;originalqueuename>-poison*. Można napisać funkcję przetwarzania wiadomości z kolejki trucizny, rejestrując je lub wysyłając powiadomienie, że wymagana jest ręczna uwaga.

Aby ręcznie obsługiwać trujące wiadomości, sprawdź [dequeueCount](#message-metadata) wiadomości kolejki.

## <a name="polling-algorithm"></a>Algorytm sondowania

Wyzwalacz kolejki implementuje algorytm losowego wykładniczego wycofywania, aby zmniejszyć wpływ sondowania w kolejkach bezczynności na koszty transakcji magazynu.

Algorytm używa następującej logiki:

- Po znalezieniu wiadomości środowisko wykonawcze odczekuje dwie sekundy, a następnie sprawdza, czy nie ma innej wiadomości
- Gdy nie zostanie znaleziony żaden komunikat, odczeka około czterech sekund przed ponowną próbą.
- Po kolejnych nieudanych próbach uzyskania wiadomości kolejki czas oczekiwania nadal wzrasta, aż osiągnie maksymalny czas oczekiwania, który jest domyślnie wyświetlany jedną minutę.
- Maksymalny czas oczekiwania można `maxPollingInterval` skonfigurować za pomocą właściwości w [pliku host.json](functions-host-json.md#queues).

Dla rozwoju lokalnego maksymalny interwał sondowania domyślnie dwie sekundy.

W odniesieniu do rozliczeń, czas spędzony na sondowaniu przez środowisko wykonawcze jest "wolny" i nie jest wliczany do twojego konta.

## <a name="concurrency"></a>Współbieżność

Gdy istnieje wiele oczekujących komunikatów kolejki, wyzwalacz kolejki pobiera partię wiadomości i wywołuje wystąpienia funkcji jednocześnie, aby je przetworzyć. Domyślnie rozmiar partii wynosi 16. Gdy liczba przetwarzana pobiera w dół do 8, środowisko wykonawcze pobiera inną partię i rozpoczyna przetwarzanie tych komunikatów. Tak więc maksymalna liczba równoczesnych komunikatów przetwarzanych na funkcję na jednej maszynie wirtualnej (VM) wynosi 24. Ten limit ma zastosowanie oddzielnie do każdej funkcji wyzwalane w kolejce na każdej maszynie wirtualnej. Jeśli aplikacja funkcji skaluje się do wielu maszyn wirtualnych, każda maszyna wirtualna będzie czekać na wyzwalacze i próbować uruchomić funkcje. Na przykład jeśli aplikacja funkcji skaluje się do 3 maszyn wirtualnych, domyślna maksymalna liczba równoczesnych wystąpień jednej funkcji wyzwalanej kolejką wynosi 72.

Rozmiar partii i próg uzyskiwania nowej partii można konfigurować w [pliku host.json](functions-host-json.md#queues). Jeśli chcesz zminimalizować równoległe wykonywanie funkcji wyzwalanych kolejką w aplikacji funkcji, można ustawić rozmiar partii na 1. To ustawienie eliminuje współbieżność tylko tak długo, jak aplikacja funkcji działa na jednej maszynie wirtualnej (VM). 

Wyzwalacz kolejki automatycznie uniemożliwia funkcji przetwarzania wiadomości kolejki wiele razy; funkcje nie muszą być zapisywane, aby były idempotentne.

## <a name="hostjson-properties"></a>właściwości host.json

Plik [host.json](functions-host-json.md#queues) zawiera ustawienia sterujące zachowaniem wyzwalacza kolejki. Szczegółowe informacje dotyczące dostępnych ustawień można znaleźć w sekcji [ustawień host.json.](functions-bindings-storage-queue-output.md#hostjson-settings)

## <a name="next-steps"></a>Następne kroki

- [Komunikaty magazynu kolejki zapisu (powiązanie wyjściowe)](./functions-bindings-storage-blob-output.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
