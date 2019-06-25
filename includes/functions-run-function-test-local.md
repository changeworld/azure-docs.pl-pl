---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183005"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Następujące polecenie uruchamia aplikację funkcji. Aplikacja jest uruchamiana przy użyciu tego samego środowiska uruchomieniowego usługi Azure Functions, które znajduje się na platformie Azure.

```bash
func host start --build
```

Opcja `--build` jest wymagana do kompilowania projektów w języku C#. Nie jest ona potrzebna w przypadku projektu w języku JavaScript.

Podczas uruchamiania hosta funkcji zwróci on dane wyjściowe podobne do następujących (zostały one skrócone, aby zwiększyć czytelność):

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Skopiuj adres URL funkcji `HttpTrigger` z danych wyjściowych środowiska uruchomieniowego i wklej go w pasku adresu swojej przeglądarki. Dołącz ciąg zapytania `?name=<yourname>` do tego adresu URL i wykonaj żądanie. Poniżej pokazano wyświetloną w przeglądarce odpowiedź na żądanie GET zwróconą przez funkcję lokalną:

![Testowanie lokalne w przeglądarce](./media/functions-run-function-test-local/functions-test-local-browser.png)

Teraz, gdy funkcja została uruchomiona lokalnie, możesz utworzyć aplikację funkcji i inne wymagane zasoby na platformie Azure.