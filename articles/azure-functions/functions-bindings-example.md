---
title: Przykład Azure Functions wyzwalacza i powiązania
description: Dowiedz się, jak skonfigurować powiązania funkcji platformy Azure
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227242"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Przykład Azure Functions wyzwalacza i powiązania

W tym artykule pokazano, jak skonfigurować [wyzwalacz i powiązania](./functions-triggers-bindings.md) w funkcji platformy Azure.

Załóżmy, że chcesz napisać nowy wiersz do usługi Azure Table Storage za każdym razem, gdy w usłudze Azure queue storage zostanie wyświetlony nowy komunikat. Ten scenariusz można zaimplementować przy użyciu wyzwalacza usługi Azure queue storage i powiązania danych wyjściowych usługi Azure Table Storage. 

Oto plik *Function. JSON* dla tego scenariusza. 

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

Pierwszy element w tablicy `bindings` jest wyzwalaczem magazynu kolejki. Właściwości `type` i `direction` identyfikują wyzwalacz. Właściwość `name` identyfikuje parametr funkcji, który odbiera zawartość komunikatu w kolejce. Nazwa kolejki do monitorowania znajduje się w `queueName`, a parametry połączenia są w ustawieniu aplikacji identyfikowanym przez `connection`.

Drugim elementem tablicy `bindings` jest powiązanie danych wyjściowych usługi Azure Table Storage. Właściwości `type` i `direction` identyfikują powiązanie. Właściwość `name` określa, jak funkcja udostępnia nowy wiersz tabeli, w tym przypadku przy użyciu wartości zwracanej funkcji. Nazwa tabeli znajduje się w `tableName`, a parametry połączenia są w ustawieniu aplikacji identyfikowanym przez `connection`.

Aby wyświetlić i edytować zawartość *funkcji Function. JSON* w Azure Portal, kliknij opcję **Edytor zaawansowany** na karcie **integracja** funkcji.

> [!NOTE]
> Wartość `connection` to nazwa ustawienia aplikacji, które zawiera parametry połączenia, a nie same parametry połączenia. Powiązania używają parametrów połączenia przechowywanych w ustawieniach aplikacji, aby wymusić najlepsze rozwiązanie, że *Funkcja Function. JSON* nie zawiera wpisów tajnych usługi.

## <a name="c-script-example"></a>C#przykład skryptu

Kod C# skryptu, który działa z tym wyzwalaczem i powiązaniem. Należy zauważyć, że nazwa parametru dostarczającego zawartość komunikatu w kolejce jest `order`; Ta nazwa jest wymagana, ponieważ wartość właściwości `name` w pliku *Function. JSON* jest `order` 

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

Ten sam plik *Function. JSON* może być używany z funkcją języka JavaScript:

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

W bibliotece klas te same wyzwalacze i informacje o powiązaniu &mdash;ją nazwy kolejek i tabel, konta magazynu i parametry funkcji dla wejściowych i wyjściowych &mdash; są udostępniane przez atrybuty zamiast pliku Function. JSON. Oto przykład:

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

Masz teraz działającą funkcję, która jest wyzwalana przez kolejkę platformy Azure i wyprowadza dane z usługi Azure Table Storage.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wzorce wyrażeń powiązań Azure Functions](./functions-bindings-expressions-patterns.md)
