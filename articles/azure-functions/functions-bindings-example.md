---
title: Przykład wyzwalania i powiązania usług Azure Functions
description: Dowiedz się, jak skonfigurować powiązania funkcji platformy Azure
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227242"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Przykład wyzwalania i powiązania usług Azure Functions

W tym artykule pokazano, jak skonfigurować [wyzwalacz i powiązania](./functions-triggers-bindings.md) w funkcji platformy Azure.

Załóżmy, że chcesz napisać nowy wiersz do magazynu tabel platformy Azure za każdym razem, gdy w magazynie kolejki platformy Azure pojawi się nowa wiadomość. Taki scenariusz można zaimplementować przy użyciu wyzwalacza usługi Azure Queue Storage i powiązania danych wyjściowych usługi Azure Table Storage. 

Oto plik *function.json* dla tego scenariusza. 

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

Pierwszym elementem `bindings` w tablicy jest wyzwalacz magazynu kolejki. Właściwości `type` `direction` i zidentyfikować wyzwalacz. Właściwość `name` identyfikuje parametr funkcji, który odbiera zawartość komunikatu kolejki. Nazwa kolejki do monitorowania znajduje `queueName`się w , a parametry połączenia `connection`są w ustawieniach aplikacji identyfikowanych przez program .

Drugim elementem `bindings` w tablicy jest powiązanie danych wyjściowych usługi Azure Table Storage. Właściwości `type` `direction` i zidentyfikować powiązanie. Właściwość `name` określa, jak funkcja zapewnia nowy wiersz tabeli, w tym przypadku przy użyciu funkcji zwracanej wartości. Nazwa tabeli znajduje `tableName`się w , a ciąg połączenia znajduje `connection`się w ustawieniu aplikacji identyfikowanym przez program .

Aby wyświetlić i edytować zawartość *pliku function.json* w witrynie Azure portal, kliknij opcję **Edytor zaawansowany** na karcie **Integruj** funkcję.

> [!NOTE]
> Wartość `connection` jest nazwa ustawienia aplikacji, która zawiera parametry połączenia, a nie samego ciągu połączenia. Powiązania używają ciągów połączeń przechowywanych w ustawieniach aplikacji, aby wymusić najlepsze rozwiązania, które *function.json* nie zawiera wpisów tajnych usługi.

## <a name="c-script-example"></a>Przykład skryptu języka C#

Oto kod skryptu języka C#, który współpracuje z tym wyzwalaczem i powiązaniem. Należy zauważyć, że nazwa parametru, który `order`udostępnia zawartość komunikatu kolejki jest ; ta nazwa jest wymagana, `name` ponieważ wartość właściwości w pliku *function.json* jest`order` 

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

## <a name="javascript-example"></a>Przykład języka JavaScript

Ten sam plik *function.json* może być używany z funkcją JavaScript:

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

W bibliotece klas ta sama &mdash; kolejka wyzwalacza i powiązania informacji oraz &mdash; nazwy tabel, konta magazynu, parametry funkcji dla danych wejściowych i wyjściowych są dostarczane przez atrybuty zamiast pliku function.json. Oto przykład:

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

Masz teraz działającą funkcję, która jest wyzwalana przez kolejkę platformy Azure i wyprowadza dane do magazynu tabel platformy Azure.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wzorce wyrażeń wiązania usługi Azure Functions](./functions-bindings-expressions-patterns.md)
