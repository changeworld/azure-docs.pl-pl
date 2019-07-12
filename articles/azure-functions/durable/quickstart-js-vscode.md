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
ms.author: glenga
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: c54a5631222a6de261e9805f284a4dfa2801750f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612924"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Tworzenie pierwszej funkcji trwałej w języku Java Script

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/download).

* Upewnij się, że masz najnowszą wersję [podstawowych narzędzi usługi Azure Functions](../functions-run-local.md).

* Na komputerze z systemem Windows sprawdź, czy zainstalowano i uruchomiono [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md). Na komputerze Mac lub komputerze z systemem Linux należy użyć rzeczywistego konta usługi Azure Storage.

* Upewnij się, że zainstalowano środowisko [Node.js](https://nodejs.org/) w wersji 8.0 lub nowszej.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-create-function-app-vs-code.md)]

## <a name="install-the-durable-functions-npm-package"></a>Instalowanie pakietu npm rozszerzenia Durable Functions

1. Zainstaluj pakiet npm `durable-functions`, uruchamiając polecenie `npm install durable-functions` w katalogu głównym aplikacji funkcji.

## <a name="creating-your-functions"></a>Tworzenie funkcji

Teraz utworzymy trzy funkcje konieczne wprowadzenie funkcje trwałe: szablon HTTP starter, orchestrator i funkcję działania. Szablon HTTP starter zainicjuje całego rozwiązania, a koordynatora będzie wysyłać zadania do różnych funkcji działania.

### <a name="http-starter"></a>Szablon HTTP starter

Najpierw należy utworzyć funkcję wyzwalaną przez protokół HTTP, która rozpoczyna aranżację funkcji trwałej.

1. Z obszaru *Azure: Funkcje*, wybierz **Create Function** ikony.

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Wybierz folder z projektu aplikacji funkcji i wybierz **niezawodne funkcje HTTP Starter** szablonu funkcji.

    ![Wybierz szablon HTTP starter](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Pozostaw nazwę domyślną `DurableFunctionsHttpStart` i naciśnij klawisz *** Enter **, następnie wybierz pozycję **anonimowe** uwierzytelniania.

    ![Wybieranie uwierzytelniania anonimowego](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Teraz utworzyliśmy punkt wejścia do naszej funkcji trwałej. Dodajmy orkiestratora.

### <a name="orchestrator"></a>Orchestrator

Teraz utworzymy orchestrator w celu koordynowania działania funkcji.

1. Z obszaru *Azure: Funkcje*, wybierz **Create Function** ikony.

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Wybierz folder z projektu aplikacji funkcji i wybierz **orchestrator funkcje trwałe** szablonu funkcji. Pozostaw nazwę domyślną "DurableFunctionsOrchestrator"

    ![Wybierz szablon programu orchestrator](./media/quickstart-js-vscode/create-function-choose-template.png)

Dodaliśmy orkiestrator w celu koordynowania funkcji działania. Dodajmy teraz funkcję przywoływanego działania.

### <a name="activity"></a>Działanie

Teraz utworzymy funkcję działania w taki sposób, aby faktycznie wykonać czynności rozwiązania.

1. Z obszaru *Azure: Funkcje*, wybierz **Create Function** ikony.

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Wybierz folder z projektu aplikacji funkcji i wybierz **działania funkcje trwałe** szablonu funkcji. Pozostaw nazwę domyślną "Hello".

    ![Wybierz szablon działania](./media/quickstart-js-vscode/create-function-choose-template.png)

Dodaliśmy już wszystkie składniki potrzebne do rozpoczęcia aranżacji i utworzenia łańcucha funkcji działań.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio Code.

1. Na komputerze Windows uruchom Emulator usługi Azure Storage, a następnie upewnij się, że **AzureWebJobsStorage** właściwość *local.settings.json* ustawiono `UseDevelopmentStorage=true`.

    Upewnij się, że dla 5.8 emulatora magazynu **AzureWebJobsSecretStorageType** właściwości local.settings.json ustawiono `files`. Na komputerze Mac lub Linux, należy ustawić **AzureWebJobsStorage** właściwości parametry połączenia, istniejącego konta usługi Azure storage. W dalszej części tego artykułu utworzysz konto magazynu.

2. Aby przetestować funkcję, ustaw punkt przerwania w kodzie funkcji, a następnie naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Jeśli po raz pierwszy używasz funkcji trwałych, jest instalowane rozszerzenie Durable Functions, a kompilacja może potrwać kilka sekund.

    > [!NOTE]
    > Rozszerzenie Durable Functions języka JavaScript wymaga wersji **1.7.0** lub większej rozszerzenia **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Uruchom następujące polecenie w folderze głównym aplikacji usługi Azure Functions, aby zainstalować rozszerzenia funkcji trwałych `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Zastąp element `{functionName}` pytaniem `DurableFunctionsOrchestrator`.

5. Za pomocą narzędzia, takiego jak [Postman](https://www.getpostman.com/) lub [cURL](https://curl.haxx.se/), Wyślij żądanie HTTP POST do punktu końcowego adresu URL.

   Odpowiedź to początkowy wynik z funkcji HTTP informujący o pomyślnym uruchomieniu aranżacji trwałej. Nie jest to jeszcze końcowy wynik aranżacji. Odpowiedź zawiera kilka przydatnych adresów URL. Na razie wykonajmy zapytanie o stan aranżacji.

6. Skopiuj wartość adresu URL dla `statusQueryGetUri` i wklej go w pasku adresu przeglądarki i wykonaj żądanie. Można również nadal m ożna również wysłać żądanie GET za pomocą narzędzia Postman.

   Żądanie wykona zapytanie o stan wystąpienia aranżacji. Powinna pojawić się odpowiedź ostatecznej pokaże nam wystąpienie zostało ukończone i obejmuje dane wyjściowe i wyniki funkcji trwałe. Wygląda ona następująco: 

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

7. Aby zatrzymać debugowanie, naciśnij klawisz **Shift + F5** w programie VS Code.

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
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-concepts.md)
