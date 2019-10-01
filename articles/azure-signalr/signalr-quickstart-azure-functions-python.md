---
title: Funkcja szybkiego startu usługi Azure Signal Service — Python
description: Przewodnik Szybki Start dotyczący korzystania z usługi Azure Signal Service i Azure Functions tworzenia pokoju rozmów.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 08/08/2019
ms.author: antchu
ms.openlocfilehash: 728111548176a0a3212b1677eeb192ccdc47fe88
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709498"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Szybki Start: Tworzenie pokoju rozmów z usługami Azure Functions i sygnalizującymi przy użyciu języka Python

Usługa Azure Signal Service umożliwia łatwe dodawanie funkcji w czasie rzeczywistym do aplikacji. Azure Functions jest platformą bezserwerową, która umożliwia uruchamianie kodu bez konieczności zarządzania infrastrukturą. W tym przewodniku szybki start dowiesz się, jak używać usługi sygnalizującej i funkcji do kompilowania aplikacji do rozmowy w czasie rzeczywistym.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start można uruchomić w systemie macOS, Windows lub Linux.

Upewnij się, że masz zainstalowany edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/) .

Zainstaluj [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) (wersja 2.7.1505 lub nowsza), aby uruchomić lokalnie aplikacje funkcji platformy Azure w języku Python.

Azure Functions wymaga języka [Python 3,6](https://www.python.org/downloads/).

Aby można było zainstalować rozszerzenia, Azure Functions Core Tools obecnie wymagane [zestaw .NET Core SDK](https://www.microsoft.com/net/download) zainstalowane. Niemniej jednak znajomość platformy .NET nie jest wymagana do kompilowania aplikacji funkcji platformy Azure w języku Python.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do Azure Portal pod adresem <https://portal.azure.com/> przy użyciu konta platformy Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. W przeglądarce, w której Azure Portal jest otwarty, potwierdź, że wystąpienie usługi sygnalizujące zostało wdrożone wcześniej, wyszukując jej nazwę w polu wyszukiwania w górnej części portalu. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukaj wystąpienie usługi sygnalizującej](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **klucze** , aby wyświetlić parametry połączenia dla wystąpienia usługi sygnalizującej.

1. Wybierz i skopiuj podstawowe parametry połączenia.

    ![Tworzenie usługi sygnalizującej](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. W edytorze kodu Otwórz folder *src/Chat/Python* w sklonowanym repozytorium.

1. Aby lokalnie opracowywać i testować funkcje języka Python, musisz pracować w środowisku Python 3,6. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`.

    **System Linux lub macOS:**

    ```bash
    python3.6 -m venv .venv
    source .venv/bin/activate
    ```

    **Systemy**

    ```powershell
    py -3.6 -m venv .venv
    .venv\scripts\activate
    ```

1. Zmień nazwę pliku *Local. Settings. sample. JSON* na *Local. Settings. JSON*.

1. W pliku **Local. Settings. JSON**wklej parametry połączenia do wartości ustawienia **AzureSignalRConnectionString** . Zapisz plik.

1. Funkcje języka Python są zorganizowane w foldery. W każdym folderze są dwa pliki: *Function. JSON* definiuje powiązania, które są używane w funkcji, a *\_ @ no__t-3init @ no__t-4\_.py* jest treścią funkcji. W tej aplikacji funkcji są dostępne dwie funkcje wyzwalane przez protokół HTTP:

    - **Negotiate** — używa powiązania danych wejściowych *SignalRConnectionInfo* do generowania i zwracania prawidłowych informacji o połączeniu.
    - **messages — odbiera** komunikat rozmowy w treści żądania i używa powiązania danych wyjściowych *sygnalizującego* , aby emitować komunikat do wszystkich połączonych aplikacji klienckich.

1. Upewnij się, że jesteś w folderze *src/Chat/Python* . Użyj Azure Functions Core Tools, aby zainstalować rozszerzenia wymagane do uruchomienia aplikacji.

    ```bash
    func extensions install
    ```

1. Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Uruchom aplikację funkcji](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono i uruchomiono aplikację bezserwerową w czasie rzeczywistym w VS Code. Następnie Dowiedz się więcej na temat sposobu wdrażania Azure Functions z VS Code.

> [!div class="nextstepaction"]
> [Wdróż Azure Functions z VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
