---
title: Tworzenie pierwszej funkcji trwałej na platformie Azure przy użyciu języka JavaScript
description: Utwórz i opublikuj funkcję trwałą platformy Azure przy użyciu programu Visual Studio Code.
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: 431bd45763cbe24e44d47342b32c5c452a27b0f6
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210297"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Tworzenie pierwszej funkcji trwałej w języku Java Script

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/download).

* Upewnij się, że masz najnowszą wersję [Azure Functions Core Tools](../functions-run-local.md).

* Na komputerze z systemem Windows sprawdź, czy zainstalowano i uruchomiono [emulator usługi Azure Storage](../../storage/common/storage-use-emulator.md). Na komputerze Mac lub komputerze z systemem Linux należy użyć rzeczywistego konta usługi Azure Storage.

* Upewnij się, że zainstalowano środowisko [Node.js](https://nodejs.org/) w wersji 8.0 lub nowszej.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Utwórz projekt lokalny 

W tej sekcji użyto Visual Studio Code do utworzenia projektu Azure Functions lokalnego. 

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Create new project...`.

1. Wybierz lokalizację katalogu dla obszaru roboczego projektu, a następnie wybierz **pozycję Wybierz**.

    > [!NOTE]
    > Te kroki zostały zaprojektowane do ukończenia poza obszarem roboczym. W takim przypadku nie wybieraj folderu projektu, który jest częścią obszaru roboczego.

1. Postępując zgodnie z instrukcjami, podaj następujące informacje dotyczące żądanego języka:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybierz język projektu aplikacji funkcji | JavaScript | Utwórz projekt funkcji lokalnego środowiska Node. js. |
    | Wybierz wersję | Azure Functions v2 | Ta opcja jest wyświetlana tylko wtedy, gdy podstawowe narzędzia nie są jeszcze zainstalowane. W takim przypadku podstawowe narzędzia są instalowane przy pierwszym uruchomieniu aplikacji. |
    | Wybierz szablon dla pierwszej funkcji projektu | Wyzwalacz HTTP | Utwórz funkcję wyzwalaną przez protokół HTTP w nowej aplikacji funkcji. |
    | Podaj nazwę funkcji | HttpTrigger | Naciśnij klawisz ENTER, aby użyć nazwy domyślnej. |
    | Poziom autoryzacji | Funkcja | Poziom autoryzacji `function` wymaga podania klucza dostępu podczas wywoływania punktu końcowego HTTP funkcji. Utrudnia to dostęp do niezabezpieczonego punktu końcowego. Aby dowiedzieć się więcej, zobacz [klucze autoryzacji](../functions-bindings-http-webhook-trigger.md#authorization-keys).  |
    | Wybierz, w jaki sposób chcesz otworzyć projekt | Dodaj do obszaru roboczego | Tworzy aplikację funkcji w bieżącym obszarze roboczym. |

Visual Studio Code instaluje Azure Functions Core Tools, w razie konieczności. Tworzy również projekt aplikacji funkcji w nowym obszarze roboczym. Ten projekt zawiera pliki konfiguracji pliku [host. JSON](../functions-host-json.md) i [Local. Settings. JSON](../functions-run-local.md#local-settings-file) . Tworzy również folder HttpExample zawierający [plik definicji Function. JSON](../functions-reference-node.md#folder-structure) oraz [plik index. js](../functions-reference-node.md#exporting-a-function), plik Node. js, który zawiera kod funkcji.

Plik Package. JSON jest również tworzony w folderze głównym.

## <a name="install-the-durable-functions-npm-package"></a>Instalowanie pakietu npm rozszerzenia Durable Functions

1. Zainstaluj pakiet npm `durable-functions`, uruchamiając polecenie `npm install durable-functions` w katalogu głównym aplikacji funkcji.

## <a name="creating-your-functions"></a>Tworzenie funkcji

Teraz utworzysz trzy funkcje potrzebne do rozpoczęcia pracy z Durable Functions: HTTP Starter, programu Orchestrator i funkcji działania. Usługa HTTP Starter inicjuje całe rozwiązanie, a koordynator wyśle pracę do różnych funkcji działania.

### <a name="http-starter"></a>HTTP Starter

Najpierw należy utworzyć funkcję wyzwalaną przez protokół HTTP, która rozpoczyna aranżację funkcji trwałej.

1. Na *platformie Azure: funkcje*wybierz ikonę **Utwórz funkcję** .

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Wybierz folder z projektem aplikacji funkcji i wybierz szablon Durable Functions funkcji **http Starter** .

    ![Wybierz szablon HTTP Starter](./media/quickstart-js-vscode/create-function-choose-template.png)

3. Pozostaw nazwę domyślną jako `DurableFunctionsHttpStart` i naciśnij klawisze * * * * ENTER * *, a następnie wybierz opcję uwierzytelnianie **anonimowe** .

    ![Wybieranie uwierzytelniania anonimowego](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

Teraz utworzyliśmy punkt wejścia do naszej funkcji trwałej. Dodajmy orkiestratora.

### <a name="orchestrator"></a>Orchestrator

Teraz utworzymy koordynatora do współdziałania z funkcjami działania.

1. Na *platformie Azure: funkcje*wybierz ikonę **Utwórz funkcję** .

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Wybierz folder z projektem aplikacji funkcji i wybierz szablon funkcji **Durable Functions programu Orchestrator** . Pozostaw nazwę domyślną "DurableFunctionsOrchestrator"

    ![Wybieranie szablonu programu Orchestrator](./media/quickstart-js-vscode/create-function-choose-template.png)

Dodaliśmy orkiestrator w celu koordynowania funkcji działania. Dodajmy teraz funkcję przywoływanego działania.

### <a name="activity"></a>Działanie

Teraz utworzymy funkcję działania, która faktycznie przeprowadzi pracę rozwiązania.

1. Na *platformie Azure: funkcje*wybierz ikonę **Utwórz funkcję** .

    ![Tworzenie funkcji](./media/quickstart-js-vscode/create-function.png)

2. Wybierz folder z projektem aplikacji funkcji i wybierz szablon funkcji **działania Durable Functions** . Pozostaw nazwę domyślną "Hello".

    ![Wybieranie szablonu działania](./media/quickstart-js-vscode/create-function-choose-template.png)

Dodaliśmy już wszystkie składniki potrzebne do rozpoczęcia aranżacji i utworzenia łańcucha funkcji działań.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio Code.

1. Na komputerze z systemem Windows uruchom emulator usługi Azure Storage i upewnij się, że właściwość **AzureWebJobsStorage** pliku *Local. Settings. JSON* jest ustawiona na wartość `UseDevelopmentStorage=true`.

    W przypadku emulatora magazynu 5,8 upewnij się, że właściwość **AzureWebJobsSecretStorageType** lokalnego. Settings. JSON ma wartość `files`. Na komputerze Mac lub Linux należy ustawić właściwość **AzureWebJobsStorage** na parametry połączenia istniejącego konta usługi Azure Storage. W dalszej części tego artykułu utworzysz konto magazynu.

2. Aby przetestować funkcję, ustaw punkt przerwania w kodzie funkcji, a następnie naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**. Jeśli po raz pierwszy używasz funkcji trwałych, jest instalowane rozszerzenie Durable Functions, a kompilacja może potrwać kilka sekund.

    > [!NOTE]
    > Rozszerzenie Durable Functions języka JavaScript wymaga wersji **1.7.0** lub większej rozszerzenia **Microsoft.Azure.WebJobs.Extensions.DurableTask**. Uruchom następujące polecenie z folderu głównego aplikacji Azure Functions, aby zainstalować rozszerzenie Durable Functions `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. Zastąp element `{functionName}` pytaniem `DurableFunctionsOrchestrator`.

5. Za pomocą narzędzia, takiego jak [Poster](https://www.getpostman.com/) lub [zwinięcie](https://curl.haxx.se/), Wyślij żądanie HTTP POST do punktu końcowego adresu URL.

   Odpowiedź to początkowy wynik z funkcji HTTP informujący o pomyślnym uruchomieniu aranżacji trwałej. Nie jest to jeszcze końcowy wynik aranżacji. Odpowiedź zawiera kilka przydatnych adresów URL. Na razie wykonajmy zapytanie o stan aranżacji.

6. Skopiuj wartość adresu URL dla `statusQueryGetUri` i wklej ją na pasku adresu przeglądarki i wykonaj żądanie. Możesz również nadal korzystać z programu Poster w celu wystawienia żądania GET.

   Żądanie wykona zapytanie o stan wystąpienia aranżacji. Powinna zostać wyświetlona ostateczna odpowiedź, która pokazuje, że wystąpienie zostało zakończone i zawiera dane wyjściowe lub wyniki funkcji trwałej. Wygląda na to: 

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

7. Aby zatrzymać debugowanie, naciśnij klawisze **Shift + F5** w vs Code.

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