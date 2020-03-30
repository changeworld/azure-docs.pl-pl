---
title: Tworzenie pierwszej funkcji trwałej na platformie Azure przy użyciu języka C#
description: Tworzenie i publikowanie funkcji trwałe platformy Azure przy użyciu programu Visual Studio lub Visual Studio Code.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: eda3afdf8deb3336cd0c5293c2422e694caa69c8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132755"
---
# <a name="create-your-first-durable-function-in-c"></a>Tworzenie pierwszej funkcji trwałej w języku C\#

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

::: zone pivot="code-editor-vscode"

W tym artykule dowiesz się, jak używać programu Visual Studio Code do lokalnego tworzenia i testowania funkcji trwałej "hello world".  Ta funkcja aranżuje i łączy w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure. Te narzędzia są dostępne jako część rozszerzenia vs code [usługi Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

![Uruchamianie funkcji trwałej na platformie Azure](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj narzędzie [Visual Studio Code](https://code.visualstudio.com/download).

* Zainstaluj następujące rozszerzenia kodu VS:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C #](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Upewnij się, że masz najnowszą wersję [narzędzi Azure Functions Core Tools](../functions-run-local.md).

* Funkcje trwałe wymagają konta magazynu platformy Azure. Potrzebujesz subskrypcji platformy Azure.

* Upewnij się, że masz zainstalowaną wersję 3.1 lub nowszą wersję [sdk .NET Core.](https://dotnet.microsoft.com/download)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Tworzenie lokalnego projektu 

W tej sekcji używasz Visual Studio Code do utworzenia lokalnego projektu usługi Azure Functions. 

1. W programie Visual Studio Code naciśnij klawiszE F1 (lub Ctrl/Cmd+Shift+P), aby otworzyć paletę poleceń. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Create New Project...`.

    ![Tworzenie projektu funkcji](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Wybierz pustą lokalizację folderu dla projektu i wybierz pozycję **Wybierz**.

1. Po monitach podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie języka dla projektu aplikacji funkcji | C# | Utwórz lokalny projekt funkcji języka C#. |
    | Wybieranie wersji | Usługi Azure w wersji 3 | Ta opcja jest widoczna tylko wtedy, gdy narzędzia podstawowe nie są jeszcze zainstalowane. W takim przypadku narzędzia podstawowe są instalowane przy pierwszym uruchomieniu aplikacji. |
    | Wybieranie szablonu dla pierwszej funkcji projektu | Pomiń na razie | |
    | Wybierz sposób otwierania projektu | Otwórz w bieżącym oknie | Ponownie otwiera kod vs w wybranym folderze. |

Kod programu Visual Studio instaluje podstawowe narzędzia usługi Azure Functions Core Tools, jeśli to konieczne. Tworzy również projekt aplikacji funkcji w folderze. Ten projekt zawiera pliki konfiguracyjne [host.json](../functions-host-json.md) i [local.settings.json.](../functions-run-local.md#local-settings-file)

## <a name="add-functions-to-the-app"></a>Dodawanie funkcji do aplikacji

Poniższe kroki używają szablonu do tworzenia trwałego kodu funkcji w projekcie.

1. W palecie poleceń wyszukaj i wybierz opcję `Azure Functions: Create Function...`.

1. Po monitach podaj następujące informacje:

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie szablonu dla funkcji | DurableFunctionsOrchestration (Funkcje) | Tworzenie aranżacji funkcji trwałych |
    | Podaj nazwę funkcji | HelloOrchestration (Wygłędnienie) | Nazwa klasy, w której tworzone są funkcje |
    | Podaj obszar nazw | Firma.Funkcja | Obszar nazw dla wygenerowanej klasy |

1. Gdy program VS Code wyświetli monit o wybranie konta magazynu, wybierz pozycję **Wybierz konto magazynu**. Po monitach podaj następujące informacje, aby utworzyć nowe konto magazynu na platformie Azure.

    | Monit | Wartość | Opis |
    | ------ | ----- | ----------- |
    | Wybieranie subskrypcji | *nazwa subskrypcji* | Wybierz subskrypcję platformy Azure |
    | Wybieranie konta magazynu | Tworzenie nowego konta magazynu |  |
    | Wprowadź nazwę nowego konta magazynu | *unikatowa nazwa* | Nazwa konta magazynu do utworzenia |
    | Wybieranie grupy zasobów | *unikatowa nazwa* | Nazwa grupy zasobów do utworzenia |
    | Wybieranie lokalizacji | *region* | Wybierz region blisko ciebie |

Klasa zawierająca nowe funkcje jest dodawany do projektu. Vs Code dodaje również ciąg połączenia konta magazynu do *local.settings.json* i odwołanie do pakietu [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) NuGet do pliku projektu *csproj.*

Otwórz nowy plik *HelloOrchestration.cs,* aby wyświetlić zawartość. Ta trwała funkcja jest prostym przykładem łączenia funkcji w łańcuch przy użyciu następujących metod:  

| Metoda | FunctionName | Opis |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Zarządza trwałą aranżacją. W tym przypadku aranżacja się rozpoczyna, tworzy listę i dodaje wynik trzech wywołań funkcji do listy.  Po ukończeniu trzech wywołań funkcji zwraca listę. |
| **`SayHello`** | `HelloOrchestration_Hello` | Funkcja zwraca hello. Jest to funkcja, która zawiera logikę biznesową, która jest aranżowana. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | [Funkcja wyzwalana przez protokół HTTP](../functions-bindings-http-webhook.md), która uruchamia wystąpienie aranżacji i zwraca odpowiedź stanu kontroli. |

Po utworzeniu projektu funkcji i funkcji trwałej można ją przetestować na komputerze lokalnym.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio Code.

1. Aby przetestować funkcję, ustaw punkt `SayHello` przerwania w kodzie funkcji działania i naciśnij klawisz F5, aby uruchomić projekt aplikacji funkcji. Dane wyjściowe z pakietu Core Tools są wyświetlane na panelu **terminalu**.

    > [!NOTE]
    > Więcej informacji na temat debugowania można znaleźć w [diagnostyce funkcji trwałych.](durable-functions-diagnostics.md#debugging)

1. W panelu **terminalu** skopiuj punkt końcowy adresu URL funkcji wyzwalanej przez protokół HTTP.

    ![Lokalne dane wyjściowe platformy Azure](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Za pomocą narzędzia, takiego jak [Listonosz](https://www.getpostman.com/) lub [cURL,](https://curl.haxx.se/)wyślij żądanie HTTP POST do punktu końcowego adresu URL.

   Odpowiedź to początkowy wynik z funkcji HTTP informujący o pomyślnym uruchomieniu aranżacji trwałej. Nie jest to jeszcze końcowy wynik aranżacji. Odpowiedź zawiera kilka przydatnych adresów URL. Na razie wykonajmy zapytanie o stan aranżacji.

1. Skopiuj `statusQueryGetUri` wartość adresu URL i wklej ją na pasku adresu przeglądarki i wykonaj żądanie. Alternatywnie można również kontynuować korzystanie z listonosza do wystawiania żądania GET.

   Żądanie wykona zapytanie o stan wystąpienia aranżacji. Należy uzyskać ostateczną odpowiedź, która pokazuje nam wystąpienie zostało zakończone i zawiera dane wyjściowe lub wyniki funkcji trwałe. Wygląda na to, że: 

    ```json
    {
        "name": "HelloOrchestration",
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

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj adres URL wyzwalacza HTTP z panelu **Dane wyjściowe**. Adres URL, który wywołuje funkcję wyzwalaną przez protokół HTTP, powinien mieć następujący format:

        https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart

1. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Użyto programu Visual Studio Code do tworzenia i publikowania aplikacji funkcji trwałe C#.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

W tym artykule dowiesz się, jak Visual Studio 2019 lokalnie utworzyć i przetestować "hello world" trwałe funkcji.  Ta funkcja aranżuje i łączy w łańcuchy wywołania do innych funkcji. Kod funkcji zostanie następnie opublikowany na platformie Azure. Te narzędzia są dostępne jako część obciążenia dewelopera platformy Azure w programie Visual Studio 2019.

![Uruchamianie funkcji trwałej na platformie Azure](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

* Zainstaluj program [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Upewnij się, że obciążenie **programowanie na platformie Azure** jest również instalowane. Visual Studio 2017 obsługuje również tworzenie funkcji trwałych, ale interfejs użytkownika i kroki różnią się.

* Sprawdź, czy zainstalowano i uruchomiono [emulator magazynu Azure](../../storage/common/storage-use-emulator.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Tworzenie projektu aplikacji funkcji

Szablon projektu usługi Azure Functions umożliwia utworzenie projektu, który można opublikować w aplikacji funkcji na platformie Azure. Aplikacja funkcji umożliwia grupowanie funkcji jako jednostki logicznej w celu łatwiejszego zarządzania, wdrażania, skalowania i udostępniania zasobów.

1. W programie Visual Studio wybierz polecenie **Nowy** > **projekt** z menu **Plik.**

1. W oknie **dialogowym Tworzenie** nowego `functions`projektu wyszukaj polecenie , wybierz szablon **Usługi Azure** i wybierz pozycję **Dalej**. 

    ![Okno dialogowe Nowy projekt umożliwiające utworzenie funkcji w programie Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Wpisz **nazwę projektu** dla projektu i wybierz przycisk **OK**. Nazwa projektu musi być prawidłowa jako obszar nazw języka C#, więc nie należy używać podkreśleń, łączników ani innych znaków niealphanumeric.

1. W **obszarze Tworzenie nowej aplikacji usługi Azure Functions**użyj ustawień określonych w tabeli, która następuje po obrazie.

    ![Tworzenie nowego okna dialogowego aplikacji funkcji platformy Azure w programie Visual Studio](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Ustawienie      | Sugerowana wartość  | Opis                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Wersja** | Funkcja Azure 3.0 <br />(.NET Core) | Tworzy projekt funkcji, który używa środowiska uruchomieniowego w wersji 3.0 usługi Azure Functions, która obsługuje .NET Core 3.1. Aby uzyskać więcej informacji, zobacz [How to target Azure Functions runtime version (Wybieranie wersji środowiska uruchomieniowego usługi Azure Functions)](../functions-versions.md).   |
    | **Szablonu** | Pusty | Tworzy pustą aplikację funkcji. |
    | **Konto magazynu**  | Emulator magazynu | Konto magazynu jest wymagane do zarządzania stanem funkcji trwałej. |

4. Wybierz **pozycję Utwórz,** aby utworzyć pusty projekt funkcji. Ten projekt zawiera pliki konfiguracji podstawowej wymagane do uruchomienia Twoich funkcji.

## <a name="add-functions-to-the-app"></a>Dodawanie funkcji do aplikacji

Poniższe kroki używają szablonu do tworzenia trwałego kodu funkcji w projekcie.

1. Kliknij prawym przyciskiem myszy projekt w programie Visual Studio i wybierz polecenie **Dodaj** > **nową funkcję platformy Azure**.

    ![Dodawanie nowej funkcji](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Sprawdź, czy **funkcja platformy Azure** jest zaznaczona z menu dodawania, wpisz nazwę pliku C#, a następnie wybierz pozycję **Dodaj**.

1. Wybierz szablon **Aranżacja funkcji trwałych,** a następnie wybierz pozycję **Ok**

    ![Wybór szablonu trwałego](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Nowa trwała funkcja jest dodawana do aplikacji.  Otwórz nowy plik cs, aby wyświetlić jego zawartość. Ta trwała funkcja jest prostym przykładem łączenia funkcji w łańcuch przy użyciu następujących metod:  

| Metoda | FunctionName | Opis |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Zarządza trwałą aranżacją. W tym przypadku aranżacja się rozpoczyna, tworzy listę i dodaje wynik trzech wywołań funkcji do listy.  Po ukończeniu trzech wywołań funkcji zwraca listę. |
| **`SayHello`** | `<file-name>_Hello` | Funkcja zwraca hello. Jest to funkcja, która zawiera logikę biznesową, która jest aranżowana. |
| **`HttpStart`** | `<file-name>_HttpStart` | [Funkcja wyzwalana przez protokół HTTP](../functions-bindings-http-webhook.md), która uruchamia wystąpienie aranżacji i zwraca odpowiedź stanu kontroli. |

Po utworzeniu projektu funkcji i funkcji trwałej można ją przetestować na komputerze lokalnym.

## <a name="test-the-function-locally"></a>Lokalne testowanie funkcji

Podstawowe narzędzia usługi Azure Functions umożliwiają uruchamianie projektu usługi Azure Functions na lokalnym komputerze deweloperskim. Monit o zainstalowanie tych narzędzi pojawia się przy pierwszym uruchomieniu funkcji w programie Visual Studio.

1. Aby przetestować funkcję, naciśnij klawisz F5. Po wyświetleniu monitu zaakceptuj żądanie programu Visual Studio dotyczące pobrania i zainstalowania podstawowych narzędzi usługi Azure Functions (CLI). Może także być konieczne włączenie wyjątku zapory, aby umożliwić narzędziom obsługę żądań HTTP.

2. Skopiuj adres URL funkcji z danych wyjściowych środowiska uruchomieniowego usługi Azure Functions.

    ![Lokalne środowisko uruchomieniowe platformy Azure](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Wklej adres URL żądania HTTP w pasku adresu przeglądarki i wykonaj żądanie. Na poniższym obrazie przedstawiono wyświetloną w przeglądarce odpowiedź na lokalne żądanie GET zwróconą przez funkcję:

    ![Odpowiedź hosta localhost funkcji wyświetlona w przeglądarce](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    Odpowiedź to początkowy wynik z funkcji HTTP informujący o pomyślnym uruchomieniu aranżacji trwałej.  Nie jest to jeszcze końcowy wynik aranżacji.  Odpowiedź zawiera kilka przydatnych adresów URL.  Na razie wykonajmy zapytanie o stan aranżacji.

4. Skopiuj wartość adresu URL z właściwości `statusQueryGetUri`, wklej ją w pasku adresu przeglądarki i wykonaj żądanie.

    Żądanie wykona zapytanie o stan wystąpienia aranżacji. W końcu powinna zostać zwrócona odpowiedź podobna do następującej.  To dane wyjściowe pokazuje nam wystąpienie zostało zakończone i zawiera wyjścia lub wyniki funkcji trwałe.

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
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Aby zatrzymać debugowanie, naciśnij **klawisze Shift + F5**.

Gdy będziesz mieć pewność, że funkcja działa poprawnie na komputerze lokalnym, możesz opublikować projekt na platformie Azure.

## <a name="publish-the-project-to-azure"></a>Publikowanie projektu na platformie Azure

Aby opublikować projekt, musisz mieć aplikację funkcji w swojej subskrypcji platformy Azure. Aplikację funkcji możesz utworzyć bezpośrednio w programie Visual Studio.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Testowanie funkcji na platformie Azure

1. Skopiuj podstawowy adres URL aplikacji funkcji ze strony profilu publikowania. Część `localhost:port` adresu URL używaną podczas lokalnego testowania funkcji zastąp nowym podstawowym adresem URL.

    Adres URL, który wywołuje wyzwalacz HTTP funkcji trwałej, powinien mieć następujący format:

        https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. Wklej nowy adres URL żądania HTTP na pasku adresu przeglądarki. Podczas korzystania z opublikowanej aplikacji powinna zostać zwrócona taka sama odpowiedź dotycząca stanu.

## <a name="next-steps"></a>Następne kroki

Utworzono i opublikowano aplikację funkcji trwałej w języku C# za pomocą programu Visual Studio.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat typowych wzorców funkcji trwałej](durable-functions-overview.md#application-patterns)

::: zone-end
