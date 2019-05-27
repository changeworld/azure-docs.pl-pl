---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 03/12/2019
ms.author: glenga
ms.custom: include file, fasttrack-edit
ms.openlocfilehash: 5b009fafc818a06bdda309b3e025251cc0997e47
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132151"
---
## <a name="update-the-function"></a>Aktualizowanie funkcji

Domyślnie szablon tworzy funkcję, która wymaga klucza funkcji do wysyłania żądań. Aby ułatwić testowanie funkcji na platformie Azure, należy zaktualizować funkcję, aby zezwolić na dostęp anonimowy. Sposób wprowadzania tej zmiany zależy od języka projektu funkcji.

### <a name="c"></a>C\#

Otwórz plik kodu MyHttpTrigger.cs, który jest Twoją nową funkcją, i zmień atrybut **AuthorizationLevel** w definicji funkcji na wartość `Anonymous`, a następnie zapisz zmiany.

```csharp
[FunctionName("MyHttpTrigger")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    ILogger log)
```

### <a name="javascript"></a>JavaScript

Otwórz plik function.json nowych funkcji w edytorze tekstów, zaktualizuj **authLevel** właściwość **powiązania** do `anonymous`i Zapisz zmiany.

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
