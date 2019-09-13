---
title: Tworzenie Durable Functions przy użyciu Azure Portal
description: Dowiedz się, jak zainstalować rozszerzenie Durable Functions dla Azure Functions do tworzenia aplikacji w portalu.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: b765e7a03f84211d4a86c4242e9484b3517c95f9
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933559"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>Tworzenie Durable Functions przy użyciu Azure Portal

Rozszerzenie [Durable Functions](durable-functions-overview.md) dla Azure Functions jest dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). To rozszerzenie musi być zainstalowane w aplikacji funkcji. W tym artykule przedstawiono sposób instalowania tego pakietu, dzięki któremu można opracowywać trwałe funkcje w Azure Portal.

> [!NOTE]
> 
> * W przypadku tworzenia trwałych funkcji w C#programie należy wziąć pod uwagę [Programowanie programu Visual Studio 2019](durable-functions-create-first-csharp.md).
> * W przypadku tworzenia trwałych funkcji w języku JavaScript należy zamiast tego rozważyć [tworzenie Visual Studio Code](./quickstart-js-vscode.md).

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Aby hostować wykonywanie dowolnej funkcji, musisz mieć aplikację funkcji. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej, co ułatwia zarządzanie, wdrażanie i udostępnianie zasobów. Możesz utworzyć aplikację platformy .NET lub JavaScript.

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

Domyślnie utworzona aplikacja funkcji używa wersji 2. x środowiska uruchomieniowego Azure Functions. Rozszerzenie Durable Functions działa w wersjach 1. x i 2. x środowiska uruchomieniowego Azure Functions w C#systemach i w wersji 2. x w języku JavaScript. Jednak szablony są dostępne tylko w przypadku, gdy celem jest wersja 2. x środowiska uruchomieniowego, niezależnie od wybranego języka.

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>Instalowanie pakietu npm o trwałych funkcjach (tylko kod JavaScript)

W przypadku tworzenia Durable Functions JavaScript należy zainstalować [ `durable-functions` pakiet npm](https://www.npmjs.com/package/durable-functions).

1. Wybierz nazwę aplikacji funkcji, a następnie pozycję **funkcje platformy**, a następnie **Narzędzia zaawansowane (kudu)** .

   ![Funkcje platformy funkcji wybierz kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. W konsoli kudu wybierz pozycję **konsola debugowania** , a następnie polecenie **cmd**.

   ![Konsola debugowania kudu](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. Struktura katalogu plików aplikacji funkcji powinna być wyświetlana. Przejdź do folderu `site/wwwroot`. W tym miejscu możesz przekazać `package.json` plik, przeciągając go i upuszczając do okna katalogu plików. Poniżej przedstawiono `package.json` przykład:

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.1.2"
      }
    }
    ```

   ![Kudu Przekaż plik Package. JSON](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. Po przekazaniu należy uruchomić polecenie z konsoli wykonywania zdalnego kudu. `npm install` `package.json`

   ![Kudu Uruchom instalację npm](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>Tworzenie funkcji programu Orchestrator

1. Rozwiń aplikację funkcji i kliknij przycisk **+** obok pozycji **Funkcje**. Jeśli jest to pierwsza funkcja w aplikacji funkcji, wybierz pozycję **W portalu**, a następnie opcję **Kontynuuj**. W przeciwnym razie przejdź do kroku trzeciego.

   ![Strona szybkiego rozpoczynania pracy z usługą Functions w witrynie Azure Portal](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. Wybierz pozycję **Więcej szablonów**, a następnie pozycję **Zakończ i wyświetl szablony**.

    ![Wybieranie pozycji Więcej szablonów w przewodniku Szybki start usługi Functions](./media/durable-functions-create-portal/add-first-function.png)

1. W polu wyszukiwania wpisz `durable` , a następnie wybierz szablon **Durable Functions http Starter** .

1. Po wyświetleniu monitu wybierz pozycję **Zainstaluj** , aby zainstalować rozszerzenie Azure DurableTask wszystkie zależności w aplikacji funkcji. Wystarczy zainstalować rozszerzenie tylko raz dla aplikacji funkcji. Po pomyślnym zakończeniu instalacji wybierz pozycję **Kontynuuj**.

    ![Instalowanie rozszerzeń powiązania](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. Po zakończeniu instalacji Nazwij nową funkcję `HttpStart` i wybierz pozycję **Utwórz**. Utworzona funkcja jest używana do uruchomienia aranżacji.

1. Utwórz kolejną funkcję w aplikacji funkcji, tym razem używając szablonu **Durable Functions Orchestrator** . Nazwij nową funkcję `HelloSequence`aranżacji.

1. Utwórz trzecią funkcję o `Hello` nazwie przy użyciu szablonu **działania Durable Functions** .

## <a name="test-the-durable-function-orchestration"></a>Testowanie aranżacji funkcji trwałych

1. Wróć do funkcji **HttpStart** , wybierz pozycję **</> Pobierz adres URL funkcji** i **Skopiuj** adres URL. Ten adres URL jest używany do uruchamiania funkcji **HelloSequence** .

1. Użyj narzędzia HTTP, takiego jak Poster lub zwinięcie, aby wysłać żądanie POST do skopiowanego adresu URL. Poniższy przykład jest poleceniem zwinięcie, które wysyła żądanie POST do funkcji trwałej:

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
    ```

    W tym przykładzie `{your-function-app-name}` jest to domena, która jest nazwą aplikacji funkcji. Komunikat odpowiedzi zawiera zestaw punktów końcowych identyfikatora URI, których można użyć do monitorowania wykonywania i zarządzania nim, który wygląda podobnie do następującego przykładu:

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. Wywołaj identyfikator URI punktu końcowego i zobaczysz bieżący stan funkcji trwałej, która może wyglądać podobnie do tego przykładu: `statusQueryGetUri`

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. Kontynuuj wywoływanie `statusQueryGetUri` punktu końcowego do momentu zmiany stanu na **zakończone**i zobaczysz odpowiedź podobną do poniższego przykładu:

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
