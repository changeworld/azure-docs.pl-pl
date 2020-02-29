---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190875"
---
## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Uruchom funkcję przez uruchomienie lokalnego hosta Azure Functions środowiska uruchomieniowego z folderu *LocalFunctionProj* :

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

W kierunku końca danych wyjściowych powinny być wyświetlane następujące wiersze: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Jeśli HttpExample nie pojawia się, jak pokazano poniżej, prawdopodobnie uruchomiono hosta z poziomu folderu *HttpExample* . W takim przypadku użyj **klawiszy Ctrl**+**C** , aby zatrzymać hosta, przejdź do folderu nadrzędnego *LocalFunctionProj* i ponownie uruchom poprzednie polecenie.

Skopiuj adres URL funkcji `HttpExample` z tego danych wyjściowych do przeglądarki i dołącz ciąg zapytania `?name=<your-name>`, wprowadzając pełny adres URL, taki jak `http://localhost:7071/api/HttpExample?name=Functions`. W przeglądarce powinien zostać wyświetlony komunikat podobny do `Hello Functions`:

![Wynik funkcji uruchomionej lokalnie w przeglądarce](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Na terminalu, w którym uruchomiono `func start`, są także wyświetlane dane wyjściowe dziennika podczas wykonywania żądania.

Gdy wszystko będzie gotowe, użyj **klawiszy Ctrl**+**C** i wybierz `y`, aby zatrzymać hosta funkcji.