---
title: Tworzenie pierwszej funkcji trwałej na platformie Azure przy użyciu języka JavaScript
description: Utwórz i opublikuj funkcję trwałą platformy Azure przy użyciu programu Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 03/24/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: 55098daa69d3e878140b20095b0a3e08811269e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80257652"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Tworzenie pierwszej funkcji trwałej w języku Java Script

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

W tym artykule przedstawiono użycie rozszerzenia programu Visual Studio Code dla usługi Azure Functions w celu lokalnego utworzenia i przetestowania funkcji trwałej „hello world”.  Ta funkcja będzie aranżować i łączyć w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure.

![Uruchamianie funkcji trwałej na platformie Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/download).

* Instalowanie rozszerzenia kodu [usługi Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS

* Upewnij się, że masz najnowszą wersję [narzędzi Azure Functions Core Tools](../functions-run-local.md).

* Funkcje trwałe wymagają konta magazynu platformy Azure. Potrzebujesz subskrypcji platformy Azure.

* Upewnij się, że masz zainstalowaną wersję 10.x lub 12.x [node.js.](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Tworzenie lokalnego projektu 

W tej sekcji używasz Visual Studio Code do utworzenia lokalnego projektu usługi Azure Functions. 

1. W programie Visual Studio Code naciśnij klawiszE F1 (lub Ctrl/Cmd+Shift+P), aby otworzyć paletę poleceń. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Create New Project...`.

    ![Tworzenie funkcji](media/quickstart-js-vscode/functions-create-project.png)

1. Wybierz pustą lokalizację folderu dla projektu i wybierz pozycję **Wybierz**.

1. Po monitach podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie języka dla projektu aplikacji funkcji | JavaScript | Utwórz lokalny projekt funkcji node.js. |
    | Wybieranie wersji | Usługi Azure w wersji 3 | Ta opcja jest widoczna tylko wtedy, gdy narzędzia podstawowe nie są jeszcze zainstalowane. W takim przypadku narzędzia podstawowe są instalowane przy pierwszym uruchomieniu aplikacji. |
    | Wybieranie szablonu dla pierwszej funkcji projektu | Pomiń na razie | |
    | Wybierz sposób otwierania projektu | Otwórz w bieżącym oknie | Ponownie otwiera kod vs w wybranym folderze. |

Kod programu Visual Studio instaluje podstawowe narzędzia usługi Azure Functions Core Tools, jeśli to konieczne. Tworzy również projekt aplikacji funkcji w folderze. Ten projekt zawiera pliki konfiguracyjne [host.json](../functions-host-json.md) i [local.settings.json.](../functions-run-local.md#local-settings-file)

Plik package.json jest również tworzony w folderze głównym.

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Włącz tryb zgodności funkcji usługi Azure Functions v2

Obecnie funkcje trwałe JavaScript wymagają trybu zgodności usługi Azure Functions V2, aby włączyć.

1. Otwórz *local.settings.json,* aby edytować ustawienia używane podczas lokalnego uruchamiania aplikacji.

1. Dodaj ustawienie `FUNCTIONS_V2_COMPATIBILITY_MODE` o nazwie `true`o wartości .

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureWebJobsStorage": "",
            "FUNCTIONS_WORKER_RUNTIME": "node",
            "FUNCTIONS_V2_COMPATIBILITY_MODE": "true"
        }
    }
    ```

## <a name="install-the-durable-functions-npm-package"></a>Instalowanie pakietu npm rozszerzenia Durable Functions

Aby pracować z funkcjami trwałymi w aplikacji funkcji Node.js, należy użyć biblioteki o nazwie `durable-functions`.

1. Użyj menu *Widok* lub Ctrl+Shift+', aby otworzyć nowy terminal w programie VS Code.

1. Zainstaluj pakiet npm `durable-functions`, uruchamiając polecenie `npm install durable-functions` w katalogu głównym aplikacji funkcji.

## <a name="creating-your-functions"></a>Tworzenie funkcji

Najbardziej podstawowa aplikacja Trwałe funkcje zawiera trzy funkcje:

* *Funkcja koordynatora* - opisuje przepływ pracy, który organizuje inne funkcje.
* *Funkcja działania* - wywoływana przez funkcję koordynatora, wykonuje pracę i opcjonalnie zwraca wartość.
* *Funkcja klienta* — zwykła funkcja platformy Azure, która uruchamia funkcję koordynatora. W tym przykładzie użyto funkcji wyzwalanej przez HTTP.

### <a name="orchestrator-function"></a>Funkcja koordynatora

Szablon służy do tworzenia kodu funkcji trwałe w projekcie.

1. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Create Function...`.

1. Po monitach podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Orkiestrator trwałych funkcji | Tworzenie aranżacji funkcji trwałych |
    | Podaj nazwę funkcji | HelloOrchestrator (wygłębianie) | Nazwa twojej trwałej funkcji |

Dodano koordynatora do koordynowania funkcji działania. Otwórz *HelloOrchestrator/index.js,* aby wyświetlić funkcję koordynatora. Każde wywołanie `context.df.callActivity` wywoływania funkcji `Hello`działania o nazwie .

Następnie dodasz funkcję `Hello` działania, do której istnieje odwołanie.

### <a name="activity-function"></a>Funkcja działania

1. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Create Function...`.

1. Po monitach podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Działanie funkcji trwałych | Tworzenie funkcji działania |
    | Podaj nazwę funkcji | Hello | Nazwa funkcji aktywności |

Dodano funkcję `Hello` działania, która jest wywoływana przez koordynatora. Otwórz *Hello/index.js,* aby zobaczyć, że przyjmuje nazwę jako dane wejściowe i zwraca powitanie. Funkcja działania to funkcja, w której będziesz wykonywać akcje, takie jak wykonywanie wywołania bazy danych lub wykonywanie obliczeń.

Na koniec dodasz funkcję wyzwalaną http, która uruchamia aranżację.

### <a name="client-function-http-starter"></a>Funkcja klienta (rozrusznik HTTP)

1. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Create Function...`.

1. Po monitach podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | Trwałe funkcje ROZRUSZNIK HTTP | Tworzenie funkcji startowej HTTP |
    | Podaj nazwę funkcji | Trwałe funkcjeHttpStart | Nazwa funkcji aktywności |
    | Poziom autoryzacji | Anonimowe | W celach demonstracyjnych zezwalaj na wywoływanie funkcji bez uwierzytelniania |

Dodano funkcję wyzwalaną http, która uruchamia aranżację. Otwórz *DurableFunctionsHttpStart/index.js,* aby zobaczyć, że używa `client.startNew` do rozpoczęcia nowej aranżacji. Następnie używa `client.createCheckStatusResponse` do zwrócenia odpowiedzi HTTP zawierającej adresy URL, które mogą być używane do monitorowania i zarządzania nową aranżacją.

Masz teraz aplikację funkcje trwałe, które można uruchomić lokalnie i wdrożone na platformie Azure.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio Code.

1. Aby przetestować funkcję, ustaw punkt `Hello` przerwania w kodzie funkcji działania (*Hello/index.js*). Naciśnij klawisz F5 lub wybierz `Debug: Start Debugging` z palety poleceń, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

    > [!NOTE]
    > Więcej informacji na temat debugowania można znaleźć w [diagnostyce funkcji trwałych.](durable-functions-diagnostics.md#debugging)

1. Funkcje trwałe wymaga konta usługi Azure Storage do uruchomienia. Gdy program VS Code wyświetli monit o wybranie konta magazynu, wybierz pozycję **Wybierz konto magazynu**.

    ![Tworzenie konta magazynu](media/quickstart-js-vscode/functions-select-storage.png)

1. Po monitach podaj następujące informacje, aby utworzyć nowe konto magazynu na platformie Azure.

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie subskrypcji | *nazwa subskrypcji* | Wybierz subskrypcję platformy Azure |
    | Wybieranie konta magazynu | Tworzenie nowego konta magazynu |  |
    | Wprowadź nazwę nowego konta magazynu | *unikatowa nazwa* | Nazwa konta magazynu do utworzenia |
    | Wybieranie grupy zasobów | *unikatowa nazwa* | Nazwa grupy zasobów do utworzenia |
    | Wybieranie lokalizacji | *region* | Wybierz region blisko ciebie |

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](media/quickstart-js-vscode/functions-f5.png)

1. Za pomocą narzędzia, takiego jak [Listonosz](https://www.getpostman.com/) lub [cURL,](https://curl.haxx.se/)wyślij żądanie HTTP POST do punktu końcowego adresu URL. Zastąp ostatni segment nazwą funkcji`HelloOrchestrator`koordynatora ( ). Adres URL powinien `http://localhost:7071/api/orchestrators/HelloOrchestrator`być podobny do .

   Odpowiedź jest wynikiem początkowym z funkcji HTTP, informując, że trwała aranżacja została pomyślnie uruchomiona. Nie jest to jeszcze końcowy wynik aranżacji. Odpowiedź zawiera kilka przydatnych adresów URL. Na razie wykonajmy zapytanie o stan aranżacji.

1. Skopiuj `statusQueryGetUri` wartość adresu URL i wklej ją na pasku adresu przeglądarki i wykonaj żądanie. Alternatywnie można również kontynuować korzystanie z listonosza do wystawiania żądania GET.

   Żądanie wykona zapytanie o stan wystąpienia aranżacji. Należy uzyskać ostateczną odpowiedź, która pokazuje nam wystąpienie zostało zakończone i zawiera dane wyjściowe lub wyniki funkcji trwałe. Wygląda na to, że: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Aby zatrzymać debugowanie, naciśnij **klawisze Shift + F5** w programie VS Code.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

### <a name="enable-azure-functions-v2-compatibility-mode"></a>Włącz tryb zgodności funkcji usługi Azure Functions v2

Ta sama zgodność usługi Azure Functions V2, która została włączona lokalnie, musi być włączona w aplikacji na platformie Azure.

1. Korzystając z palety poleceń, `Azure Functions: Edit Setting...`wyszukaj i wybierz opcję .

1. Postępuj zgodnie z instrukcjami, aby zlokalizować aplikację funkcji w ramach subskrypcji platformy Azure.

1. Wybierz pozycję `Create new App Setting...`.

1. Wprowadź nowy klucz `FUNCTIONS_V2_COMPATIBILITY_MODE`ustawień .

1. Wprowadź wartość ustawienia `true`.

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Adres URL, który wywołuje funkcję wyzwalaną przez HTTP, powinien być w tym formacie:`http://<functionappname>.azurewebsites.net/orchestrators/HelloOrchestrator`

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Utworzono i opublikowano aplikację funkcji trwałej w języku JavaScript za pomocą narzędzia Visual Studio Code.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)