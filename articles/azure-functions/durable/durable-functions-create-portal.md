---
title: Utwórz trwałe funkcje przy użyciu witryny Azure portal
description: Dowiedz się, jak zainstalować rozszerzenia funkcji trwałych dla usługi Azure Functions do tworzenia aplikacji w portalu.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf, glenga
ms.openlocfilehash: 705a43f1ef35f953d1b87c7c44bbc45fcb4334be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65872858"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Utwórz trwałe funkcje przy użyciu witryny Azure portal

[Funkcje trwałe](durable-functions-overview.md) rozszerzenia dla usługi Azure Functions jest podawany jako pakiet NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). To rozszerzenie musi być zainstalowany w aplikacji funkcji. W tym artykule przedstawiono sposób instalowania tego pakietu, dzięki czemu można tworzyć niezawodne funkcje w witrynie Azure portal.

> [!NOTE]
> 
> * Jeśli tworzysz funkcje trwałe w C#, zamiast tego należy rozważyć [rozwoju Visual Studio 2019](durable-functions-create-first-csharp.md).
> * Jeśli tworzysz trwałe funkcje w języku JavaScript, zamiast tego należy rozważyć [rozwoju Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Musi mieć aplikację funkcji do obsługi wykonywania żadnej funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, ułatwić zarządzanie, wdrażanie i udostępnianie zasobów. Można utworzyć aplikację platformy .NET lub JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Domyślnie aplikacja funkcji utworzona korzysta z wersji 2.x środowisko uruchomieniowe usługi Azure Functions. Rozszerzenia funkcji trwałych działa na obu wersji 1.x i 2.x środowisko uruchomieniowe usługi Azure Functions w C#i w wersji 2.x w języku JavaScript. Jednak szablony są dostępne tylko podczas określania wartości docelowej wersji 2.x środowiska uruchomieniowego, niezależnie od tego, w wybranym języku.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Zainstaluj pakiet npm durable functions (tylko kod JavaScript)

Jeśli tworzysz niezawodne funkcje języka JavaScript, będą musieli zainstalować [ `durable-functions` pakietu npm](https://www.npmjs.com/package/durable-functions).

1. Wybierz nazwę aplikacji funkcji, a następnie **funkcje platformy**, następnie **Zaawansowane (Kudu) narzędzia**.

   ![Funkcje platformy funkcji wybierz Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. W konsoli Kudu, wybierz **konsoli debugowania** następnie **CMD**.

   ![Konsoli debugowania aparatu kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Struktury katalogów plików aplikację funkcji powinna być wyświetlana. Przejdź do folderu `site/wwwroot`. Z tego miejsca możesz przekazać `package.json` pliku, przeciągając i upuszczając go w oknie katalogu plików. Przykład `package.json` znajduje się poniżej:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Aparat kudu przekazywanie pliku package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Raz swoje `package.json` zostanie przekazany, uruchom `npm install` polecenie z poziomu konsoli Kudu zdalnego wykonywania.

   ![Uruchom instalację npm kudu](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Tworzenie funkcji programu orchestrator

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **W portalu**, a następnie opcję **Kontynuuj**. W przeciwnym razie przejdź do kroku trzeciego.

   ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Wybierz pozycję **Więcej szablonów**, a następnie pozycję **Zakończ i wyświetl szablony**.

    ![Wybieranie pozycji Więcej szablonów w przewodniku Szybki start usługi Functions](./media/durable-functions-create-portal/add-first-function.png)

1. W polu wyszukiwania wpisz `durable` , a następnie wybierz **niezawodne funkcje HTTP starter** szablonu.

1. Po wyświetleniu monitu wybierz **zainstalować** można zainstalować rozszerzenia Azure DurableTask wszelkie zależności w aplikacji funkcji. Musisz zainstalować rozszerzenie jeden raz dla aplikacji funkcji zapewniają. Po pomyślnym zakończeniu instalacji wybierz pozycję **Kontynuuj**.

    ![Instalowanie rozszerzeń powiązania](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Po zakończeniu instalacji należy nazwać nową funkcję `HttpStart` i wybierz polecenie **Utwórz**. Do utworzonej funkcji jest używany do uruchomienia aranżacji.

1. Utworzyć inną funkcję w aplikacji funkcji w tej chwili za pomocą **Durable Functions — Orkiestrator** szablonu. Nadaj nazwę nowej funkcji aranżacji `HelloSequence`.

1. Utwórz trzecią funkcję o nazwie `Hello` przy użyciu **Durable Functions — działanie** szablonu.

## <a name="test-the-durable-function-orchestration"></a>Testowanie aranżacji trwałe — funkcja

1. Wróć do **HttpStart** funkcji, wybierz **<> / Pobierz adres URL funkcji** i **kopiowania** adresu URL. Użyj tego adresu URL, aby uruchomić **HelloSequence** funkcji.

1. Użyj narzędzia HTTP, takiego jak Postman lub programu cURL do wysłania żądania POST na adres URL, który został skopiowany. Poniższy przykład jest polecenia cURL, który wysyła żądanie POST do trwałego funkcji:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    W tym przykładzie `{your-function-app-name}` domenę, nazwę aplikacji funkcji. Komunikat odpowiedzi zawiera zestaw punktów końcowych z identyfikatora URI, które umożliwia monitorowanie i zarządzanie nimi wykonywania, który wygląda podobnie jak w poniższym przykładzie:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Wywołaj `statusQueryGetUri` identyfikator URI punktu końcowego i wyświetlić bieżący stan trwały funkcji, która może wyglądać następująco:

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Nadal wywoływania `statusQueryGetUri` punktu końcowego, aż stan zmieni się na **Ukończono**, i pojawić się odpowiedź podobna następująco:

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

Pierwszą funkcję trwałe jest uruchomiona i działa na platformie Azure.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-concepts.md)
