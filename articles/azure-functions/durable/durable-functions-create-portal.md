---
title: Tworzenie trwałych funkcji przy użyciu portalu Azure
description: Dowiedz się, jak zainstalować rozszerzenie funkcje trwałe dla usługi Azure Functions dla tworzenia portalu.
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769646"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Tworzenie trwałych funkcji przy użyciu portalu Azure

Rozszerzenie [funkcje trwałe](durable-functions-overview.md) dla usług Azure Functions znajduje się w pakiecie NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). To rozszerzenie musi być zainstalowane w aplikacji funkcji. W tym artykule pokazano, jak zainstalować ten pakiet, dzięki czemu można tworzyć funkcje trwałe w witrynie Azure portal.

> [!NOTE]
> 
> * Jeśli tworzysz trwałe funkcje w języku C#, zamiast tego należy rozważyć [program Visual Studio 2019 rozwoju.](durable-functions-create-first-csharp.md)
> * Jeśli tworzysz trwałe funkcje w języku JavaScript, należy rozważyć [tworzenie kodu programu Visual Studio.](./quickstart-js-vscode.md)

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Musisz mieć aplikację funkcji do obsługi wykonywania dowolnej funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów. Możesz utworzyć aplikację .NET lub JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Domyślnie utworzona aplikacja funkcji używa wersji 2.x środowiska wykonawczego usługi Azure Functions. Rozszerzenie Funkcje trwałe działa w obu wersjach 1.x i 2.x środowiska wykonawczego usługi Azure Functions w języku C#i w wersji 2.x w języku JavaScript. Jednak szablony są dostępne tylko wtedy, gdy kierowanie wersji 2.x środowiska wykonawczego, niezależnie od wybranego języka.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Zainstaluj pakiet npm funkcji trwałych (tylko JavaScript)

Jeśli tworzysz funkcje trwałe JavaScript, musisz zainstalować [ `durable-functions` pakiet npm](https://www.npmjs.com/package/durable-functions).

1. Wybierz nazwę aplikacji funkcji, a następnie **funkcje platformy**, a następnie **zaawansowane narzędzia (Kudu)**.

   ![Funkcje platformy funkcji wybrać Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. Wewnątrz konsoli Kudu wybierz **konsolę debugowania,** a następnie **CMD**.

   ![Konsola debugowania Kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Struktura katalogów plików aplikacji funkcji powinna być wyświetlana. Przejdź do folderu `site/wwwroot`. Stamtąd można przekazać `package.json` plik, przeciągając go i upuszczając do okna katalogu plików. Przykład `package.json` znajduje się poniżej:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu upload package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Po `package.json` przesłaniu uruchom `npm install` polecenie z Konsoli zdalnego wykonania Kudu.

   ![Kudu uruchomić npm zainstalować](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Tworzenie funkcji orkiestratora

1. Rozwiń aplikację funkcji **+** i kliknij przycisk obok **pozycji Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **W portalu**, a następnie opcję **Kontynuuj**. W przeciwnym razie przejdź do kroku trzeciego.

   ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Wybierz pozycję **Więcej szablonów**, a następnie pozycję **Zakończ i wyświetl szablony**.

    ![Wybieranie pozycji Więcej szablonów w przewodniku Szybki start usługi Functions](./media/durable-functions-create-portal/add-first-function.png)

1. W polu wyszukiwania `durable` wpisz, a następnie wybierz szablon **startowy HTTP funkcji trwałych.**

1. Po wyświetleniu monitu wybierz pozycję **Zainstaluj,** aby zainstalować rozszerzenie Azure DurableTask i wszelkie zależności w aplikacji funkcji. Dla danej aplikacji funkcji wystarczy zainstalować rozszerzenie tylko raz. Po pomyślnym zakończeniu instalacji wybierz pozycję **Kontynuuj**.

    ![Instalowanie rozszerzeń powiązania](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Po zakończeniu instalacji nazwij `HttpStart` nową funkcję i wybierz pozycję **Utwórz**. Utworzona funkcja służy do uruchamiania aranżacji.

1. Utwórz inną funkcję w aplikacji funkcji, tym razem przy użyciu szablonu **koordynatora funkcji trwałych.** Nazwij nową `HelloSequence`funkcję aranżacji .

1. Utwórz trzecią `Hello` funkcję o nazwie przy użyciu szablonu **działania trwałe funkcje.**

## <a name="test-the-durable-function-orchestration"></a>Przetestuj trwałą aranżację funkcji

1. Wróć do funkcji **HttpStart,** wybierz **</> Pobierz adres URL funkcji** i **skopiuj** adres URL. Ten adres URL służy do uruchamiania funkcji **HelloSequence.**

1. Użyj narzędzia HTTP, takiego jak Postman lub cURL, aby wysłać żądanie POST do skopiowanego adresu URL. Poniższy przykład jest polecenie cURL, który wysyła żądanie POST do funkcji trwałe:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    W tym `{your-function-app-name}` przykładzie jest domeną, która jest nazwą aplikacji funkcji. Komunikat odpowiedzi zawiera zestaw punktów końcowych identyfikatora URI, które umożliwiają monitorowanie wykonywania i zarządzanie nim, co wygląda podobnie jak w poniższym przykładzie:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Wywołanie `statusQueryGetUri` identyfikatora URI punktu końcowego i zobaczysz bieżący stan funkcji trwałe, które mogą wyglądać w tym przykładzie:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Kontynuuj wywoływanie punktu końcowego, `statusQueryGetUri` dopóki stan nie zmieni się na **Zakończone,** a zostanie wyświetlona odpowiedź, jak w poniższym przykładzie:

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

Twoja pierwsza trwała funkcja jest teraz uruchomiona na platformie Azure.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)
