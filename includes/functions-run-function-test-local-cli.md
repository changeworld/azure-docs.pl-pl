---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190875"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom funkcję, uruchamiając lokalnego hosta środowiska uruchomieniowego usługi Azure Functions z folderu *LocalFunctionProj:*

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

Pod koniec danych wyjściowych powinny pojawić się następujące wiersze: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Jeśli httpExample nie jest wyświetlany, jak pokazano poniżej, prawdopodobnie uruchomiono hosta z poziomu folderu *HttpExample.* W takim przypadku użyj **klawisza Ctrl**+**C,** aby zatrzymać hosta, przejdź do nadrzędnego folderu *LocalFunctionProj* i ponownie uruchom poprzednie polecenie.

Skopiuj `HttpExample` adres URL funkcji z tego danych `?name=<your-name>`wyjściowych do przeglądarki `http://localhost:7071/api/HttpExample?name=Functions`i dołącz ciąg zapytania, tworząc pełny adres URL, taki jak . Przeglądarka powinna wyświetlać `Hello Functions`komunikat w stylu:

![Wynik uruchomienia funkcji lokalnie w przeglądarce](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminal, w którym `func start` uruchomiono również pokazuje dane wyjściowe dziennika podczas składania żądań.

Gdy będziesz gotowy, użyj **klawisza Ctrl**+**C** i zatrzymaj `y` hosta funkcji.