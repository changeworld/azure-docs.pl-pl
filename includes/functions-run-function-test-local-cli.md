---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 749b733039e89421ac33ef76a11f3291b296e718
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673176"
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

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Jeśli httpExample nie jest wyświetlany, jak pokazano poniżej, prawdopodobnie uruchomiono hosta spoza folderu głównego projektu. W takim przypadku użyj **klawisza Ctrl**+**C,** aby zatrzymać hosta, przejść do folderu głównego projektu i ponownie uruchomić poprzednie polecenie.

Skopiuj `HttpExample` adres URL funkcji z tego danych `?name=<your-name>`wyjściowych do przeglądarki `http://localhost:7071/api/HttpExample?name=Functions`i dołącz ciąg zapytania, tworząc pełny adres URL, taki jak . Przeglądarka powinna wyświetlać `Hello Functions`komunikat w stylu:

![Wynik uruchomienia funkcji lokalnie w przeglądarce](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminal, w którym uruchomiono projekt, pokazuje również dane wyjściowe dziennika podczas składania żądań.

Gdy będziesz gotowy, użyj **klawisza Ctrl**+**C** i zatrzymaj `y` hosta funkcji.