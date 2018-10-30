---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/16/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d264477693458ff4132c1f69704a480eed2756e0
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49987996"
---
## <a name="update-the-function"></a>Aktualizowanie funkcji

Domyślnie szablon tworzy funkcję, która wymaga klucza funkcji do wysyłania żądań. Aby ułatwić testowanie funkcji na platformie Azure, należy zaktualizować funkcję, aby zezwolić na dostęp anonimowy. Sposób wprowadzania tej zmiany zależy od języka projektu funkcji.

### <a name="c"></a>C\#

Otwórz plik kodu MyHttpTrigger.cs, który jest Twoją nową funkcją, i zmień atrybut **AuthorizationLevel** w definicji funkcji na wartość `anonymous`, a następnie zapisz zmiany.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

Otwórz plik function.json nowej funkcji, otwórz go w edytorze tekstów, zmień właściwość **authLevel** w sekcji **bindings.httpTrigger** na wartość `anonymous` i zapisz zmiany.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
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
    }
  ]
```

Teraz możesz wywoływać funkcję na platformie Azure bez konieczności podawania klucza funkcji. Klucz funkcji nigdy nie jest wymagany podczas pracy lokalnej.
