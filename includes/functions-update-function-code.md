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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183004"
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
