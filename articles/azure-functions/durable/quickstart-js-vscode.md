---
title: Tworzenie pierwszej funkcji trwałej na platformie Azure przy użyciu języka JavaScript
description: Utwórz i opublikuj funkcję trwałą platformy Azure przy użyciu programu Visual Studio Code.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: 94ba2830824c4a918e9451a9fc5140d422110370
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231311"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Tworzenie pierwszej funkcji trwałej w języku Java Script

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/download).

* Make sure you have the latest version of the [Azure Functions Core Tools](../functions-run-local.md).

* Na komputerze z systemem Windows sprawdź, czy zainstalowano i uruchomiono [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md). Na komputerze Mac lub komputerze z systemem Linux należy użyć rzeczywistego konta usługi Azure Storage.

* Upewnij się, że zainstalowano środowisko [Node.js](https://nodejs.org/) w wersji 8.0 lub nowszej.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Instalowanie pakietu npm rozszerzenia Durable Functions

1. Zainstaluj pakiet npm `durable-functions`, uruchamiając polecenie `npm install durable-functions` w katalogu głównym aplikacji funkcji.

## <a name="creating-your-functions"></a>Creating your functions

We'll now create the three functions you need to get started with Durable Functions: an HTTP starter, an orchestrator, and an activity function. The HTTP starter will initiate your entire solution, and the orchestrator will dispatch work to various activity functions.

### <a name="http-starter"></a>HTTP starter

Najpierw należy utworzyć funkcję wyzwalaną przez protokół HTTP, która rozpoczyna aranżację funkcji trwałej.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions HTTP Starter** function template.

    ![Choose the HTTP starter template](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Leave the default name as `DurableFunctionsHttpStart` and press ****Enter**, then select **Anonymous** authentication.

    ![Wybieranie uwierzytelniania anonimowego](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Teraz utworzyliśmy punkt wejścia do naszej funkcji trwałej. Dodajmy orkiestratora.

### <a name="orchestrator"></a>Orchestrator

Now, we'll create an orchestrator to coordinate activity functions.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions orchestrator** function template. Leave the name as the default "DurableFunctionsOrchestrator"

    ![Choose the orchestrator template](./media/quickstart-js-vscode/create-function-choose-template.png)

Dodaliśmy orkiestrator w celu koordynowania funkcji działania. Dodajmy teraz funkcję przywoływanego działania.

### <a name="activity"></a>Działanie

Now, we'll create an activity function to actually carry out the work of the solution.

1. From *Azure: Functions*, choose the **Create Function** icon.

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Select the folder with your function app project and select the **Durable Functions activity** function template. Leave the name as the default "Hello".

    ![Choose the activity template](./media/quickstart-js-vscode/create-function-choose-template.png)

Dodaliśmy już wszystkie składniki potrzebne do rozpoczęcia aranżacji i utworzenia łańcucha funkcji działań.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio Code.

1. On a Windows computer, start the Azure Storage Emulator and make sure that the **AzureWebJobsStorage** property of *local.settings.json* is set to `UseDevelopmentStorage=true`.

    For Storage Emulator 5.8 make sure that the **AzureWebJobsSecretStorageType** property of local.settings.json is set to `files`. On     a Mac or Linux computer, you must set the **AzureWebJobsStorage** property to the connection string of an existing Azure storage         account. W dalszej części tego artykułu utworzysz konto magazynu.

2. Aby przetestować funkcję, ustaw punkt przerwania w kodzie funkcji, a następnie naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Jeśli po raz pierwszy używasz funkcji trwałych, jest instalowane rozszerzenie Durable Functions, a kompilacja może potrwać kilka sekund.

    > [!NOTE]
    > Rozszerzenie Durable Functions języka JavaScript wymaga wersji **1.7.0** lub większej rozszerzenia **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Run the following command from the root folder of your Azure Functions app to install the Durable Functions extension `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Zastąp element `{functionName}` pytaniem `DurableFunctionsOrchestrator`.

5. Using a tool like [Postman](https://www.getpostman.com/) or [cURL](https://curl.haxx.se/), send an HTTP POST request to the URL endpoint.

   Odpowiedź to początkowy wynik z funkcji HTTP informujący o pomyślnym uruchomieniu aranżacji trwałej. Nie jest to jeszcze końcowy wynik aranżacji. Odpowiedź zawiera kilka przydatnych adresów URL. Na razie wykonajmy zapytanie o stan aranżacji.

6. Copy the URL value for `statusQueryGetUri` and paste it in the browser's address bar and execute the request. Alternatively you can also continue to use Postman to issue the GET request.

   Żądanie wykona zapytanie o stan wystąpienia aranżacji. You should get an eventual response, which shows us the instance has completed, and includes the outputs or results of the durable function. It looks like: 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. To stop debugging, press **Shift + F5** in VS Code.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Utworzono i opublikowano aplikację funkcji trwałej w języku JavaScript za pomocą narzędzia Visual Studio Code.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)