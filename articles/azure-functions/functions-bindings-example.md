---
title: Przykład usłudze Azure Functions wyzwalacz i powiązania
description: Dowiedz się, jak skonfigurować powiązania funkcji platformy Azure
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: bced6dc71063b6be68d739ef67fb5ec46c3d1be6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480435"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Przykład usłudze Azure Functions wyzwalacz i powiązania

W tym artykule przedstawiono sposób konfigurowania [wyzwalacz i powiązania](./functions-triggers-bindings.md) w funkcji platformy Azure.

Załóżmy, że chcesz napisać nowy wiersz do usługi Azure Table storage, zawsze wtedy, gdy nowy komunikat pojawi się w usłudze Azure Queue storage. W tym scenariuszu można zaimplementować przy użyciu usługi Azure Queue wyzwalacz usługi storage i Azure Table storage powiązania danych wyjściowych. 

Oto *function.json* pliku, w tym scenariuszu. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Pierwszy element w `bindings` tablica jest wyzwalacz magazynu kolejki. `type` i `direction` właściwości określenie wyzwalacza. `name` Właściwość identyfikuje parametru funkcji, który odbiera zawartość komunikatu w kolejce. Nazwa kolejki do monitorowania jest `queueName`, oraz parametry połączenia są identyfikowane za pomocą ustawienia aplikacji `connection`.

Drugi element `bindings` tablica jest usługa Azure Table Storage powiązania danych wyjściowych. `type` i `direction` właściwości identyfikacji powiązania. `name` Właściwość określa, jak funkcja zapewnia nowy wiersz w tabeli, w tym przypadku za pomocą funkcji zwraca wartość. Nazwa tabeli jest `tableName`, oraz parametry połączenia są identyfikowane za pomocą ustawienia aplikacji `connection`.

Możesz wyświetlać i edytować zawartość *function.json* w witrynie Azure portal kliknij pozycję **edytor zaawansowany** opcja **integracja** kartę funkcji.

> [!NOTE]
> Wartość `connection` to nazwa ustawienia aplikacji zawierającego parametry połączenia, a nie parametry połączenia, sam. Powiązania używanie połączenia ciągów przechowywanych w ustawieniach aplikacji, aby wymusić najlepsze rozwiązaniem, które *function.json* nie zawiera wpisy tajne usługi.

## <a name="c-script-example"></a>Przykładowy skrypt w języku C#

Oto C# kod skryptu, który współdziała z tego wyzwalacza i powiązania. Należy zauważyć, że nazwa parametru, który udostępnia zawartość komunikatu w kolejce jest `order`; ta nazwa jest wymagana, ponieważ `name` wartości właściwości w *function.json* jest `order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>Przykład JavaScript

Taki sam *function.json* plik może być używany z funkcją JavaScript:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Przykład biblioteki klas

Biblioteka klas, jeden wyzwalacz i informacje o powiązaniu &mdash; nazwy kolejki i tabeli konta magazynu funkcji parametry wejściowe i wyjściowe &mdash; są dostarczane przez atrybuty zamiast pliku function.json. Oto przykład:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

Funkcja pracy, która jest wywoływana za pomocą kolejki platformy Azure i wysyła dane do usługi Azure Table storage jest teraz dostępna.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Usługa Azure Functions powiązania wzorców wyrażeń](./functions-bindings-expressions-patterns.md)
