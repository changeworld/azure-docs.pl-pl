---
title: Tworzenie pierwszej funkcji trwałej na platformie Azure przy użyciu języka JavaScript
description: Utwórz i opublikuj funkcję trwałą platformy Azure przy użyciu programu Visual Studio Code.
services: functions
documentationcenter: na
author: ColbyTresness
manager: jeconnoc
keywords: azure functions, funkcje, przetwarzanie zdarzeń, obliczenia, architektura bez serwera
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf, cotresne, glenga
ms.openlocfilehash: 7dceed4d81f1e1767cbf91804573043d1204beee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838909"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Tworzenie pierwszej funkcji trwałej w języku Java Script

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/download).

* Upewnij się, że masz [najnowsze narzędzia usługi Azure Functions](../functions-develop-vs.md#check-your-tools-version).

* Na komputerze z systemem Windows sprawdź, czy zainstalowano i uruchomiono [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md). Na komputerze Mac lub komputerze z systemem Linux należy użyć rzeczywistego konta usługi Azure Storage.

* Upewnij się, że zainstalowano środowisko [Node.js](https://nodejs.org/) w wersji 8.0 lub nowszej.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="create-a-starter-function"></a>Tworzenie funkcji Starter

Najpierw należy utworzyć funkcję wyzwalaną przez protokół HTTP, która rozpoczyna aranżację funkcji trwałej.

1. Z obszaru **Azure: Functions** wybierz ikonę tworzenia funkcji.

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

1. Wybierz folder z projektem aplikacji funkcji i wybierz szablon funkcji **wyzwalacza HTTP**.

    ![Wybieranie szablonu wyzwalacza HTTP](./media/quickstart-js-vscode/create-function-choose-template.png)

1. Wpisz `HttpStart` jako nazwę funkcji i naciśnij klawisz Enter, a następnie wybierz uwierzytelnianie **anonimowe**.

    ![Wybieranie uwierzytelniania anonimowego](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

    Funkcja zostanie utworzona w wybranym języku i przy użyciu szablonu funkcji wyzwalanej przez protokół HTTP.

1. Zastąp plik index.js poniższym kodem języka JavaScript:

    ```javascript
    const df = require("durable-functions");
    
    module.exports = async function (context, req) {
        const client = df.getClient(context);
        const instanceId = await client.startNew(req.params.functionName, undefined, req.body);
    
        context.log(`Started orchestration with ID = '${instanceId}'.`);
    
        return client.createCheckStatusResponse(context.bindingData.req, instanceId);
    };
    ```

1. Zastąp plik function.json poniższym kodem JSON:

    ```JSON
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "name": "req",
          "type": "httpTrigger",
          "direction": "in",
          "route": "orchestrators/{functionName}",
          "methods": ["post"]
        },
        {
          "name": "$return",
          "type": "http",
          "direction": "out"
        },
        {
          "name": "starter",
          "type": "orchestrationClient",
          "direction": "in"
        }
      ],
      "disabled": false
    }
    ```

Teraz utworzyliśmy punkt wejścia do naszej funkcji trwałej. Dodajmy orkiestratora.

## <a name="create-an-orchestrator-function"></a>Tworzenie funkcji orkiestratora

Następnie należy utworzyć kolejną funkcję, która będzie pełnić rolę orkiestratora. Dla wygody użyjemy szablonu funkcji wyzwalacza HTTP. Sam kod funkcji jest zastępowany kodem orkiestratora.

1. Powtórz kroki z poprzedniej sekcji, aby utworzyć drugą funkcję przy użyciu szablonu wyzwalacza HTTP. Tym razem nadaj funkcji nazwę `OrchestratorFunction`.

1. Otwórz plik index.js dla nowej funkcji i zastąp zawartość następującym kodem:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

1. Otwórz plik function.json i zastąp go następującym kodem JSON:

    [!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Dodaliśmy orkiestrator w celu koordynowania funkcji działania. Dodajmy teraz funkcję przywoływanego działania.

## <a name="create-an-activity-function"></a>Tworzenie funkcji działania

1. Powtórz kroki z poprzednich sekcji, aby utworzyć trzecią funkcję przy użyciu szablonu wyzwalacza HTTP. Ale tym razem nadaj funkcji nazwę `SayHello`.

1. Otwórz plik index.js dla nowej funkcji i zastąp zawartość następującym kodem:

    [!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

1. Zastąp plik function.json poniższym kodem JSON:

    [!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

Dodaliśmy już wszystkie składniki potrzebne do rozpoczęcia aranżacji i utworzenia łańcucha funkcji działań.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio Code.  

1. Zainstaluj pakiet npm durable-functions, uruchamiając polecenie `npm install durable-functions` w katalogu głównym aplikacji funkcji.

1. Na komputerze z systemem Windows uruchom emulator usługi Azure Storage, a następnie upewnij się, że właściwość **AzureWebJobsStorage** pliku local.settings.json została ustawiona na `UseDevelopmentStorage=true`. Na komputerze Mac lub komputerze z system Linux ustaw właściwość **AzureWebJobsStorage** na parametry połączenia istniejącego konta usługi Azure Storage. W dalszej części tego artykułu utworzysz konto magazynu.

1. Aby przetestować funkcję, ustaw punkt przerwania w kodzie funkcji, a następnie naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Jeśli po raz pierwszy używasz funkcji trwałych, jest instalowane rozszerzenie Durable Functions, a kompilacja może potrwać kilka sekund.

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Wklej adres URL żądania HTTP na pasku adresu przeglądarki i sprawdź stan aranżacji.

1. Aby zatrzymać debugowanie, naciśnij klawisze Shift+F1.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

        http://<functionappname>.azurewebsites.net/api/<functionname>

1. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Utworzono i opublikowano aplikację funkcji trwałej w języku JavaScript za pomocą narzędzia Visual Studio Code.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md)