---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/05/2019
ms.author: glenga
ms.openlocfilehash: 4db460a5dcefb49de3ad2b594d3957cbcf7445c3
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592814"
---
W projekcie C# biblioteki klas powiązania są zdefiniowane jako atrybuty powiązania w metodzie funkcji. Plik *Function. JSON* jest następnie generowany automatycznie na podstawie tych atrybutów.

Otwórz plik projektu *HttpTrigger.cs* i Dodaj następującą `using` instrukcję:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Dodaj następujący parametr do `Run` definicji metody:

```cs
[Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg
```

`msg` Parametr`ICollector<T>` jest typem, który reprezentuje kolekcję komunikatów, które są zapisywane do powiązania danych wyjściowych po zakończeniu działania funkcji. W takim przypadku dane wyjściowe są kolejki magazynu o nazwie `outqueue`. Parametry połączenia dla konta magazynu są ustawiane przez `StorageAccountAttribute`. Ten atrybut wskazuje ustawienie, które zawiera parametry połączenia konta magazynu i może być stosowane na poziomie klasy, metody lub parametru. W takim przypadku można pominąć `StorageAccountAttribute` , ponieważ jest już używane domyślne konto magazynu.

Definicja metody Run powinna teraz wyglądać następująco:  

```cs
[FunctionName("HttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, 
    [Queue("outqueue"),StorageAccount("AzureWebJobsStorage")] ICollector<string> msg, ILogger log)
```
