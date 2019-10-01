---
title: Usługa Azure Signal Service — szybki start — JavaScript
description: Przewodnik Szybki Start dotyczący korzystania z usługi Azure Signal Service i Azure Functions tworzenia pokoju rozmów.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: fd935ffda7d16988781d5debce9333ccf2adb16f
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709752"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Szybki Start: Tworzenie pokoju rozmów z usługami Azure Functions i sygnalizacyjnymi przy użyciu języka JavaScript

Usługa Azure Signal Service umożliwia łatwe dodawanie funkcji w czasie rzeczywistym do aplikacji. Azure Functions jest platformą bezserwerową, która umożliwia uruchamianie kodu bez konieczności zarządzania infrastrukturą. W tym przewodniku szybki start dowiesz się, jak używać usługi sygnalizującej i funkcji do kompilowania aplikacji do rozmowy w czasie rzeczywistym.

## <a name="prerequisites"></a>Wymagania wstępne

Ten przewodnik Szybki Start można uruchomić w systemie macOS, Windows lub Linux.

Upewnij się, że masz zainstalowany edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/) .

Zainstaluj [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) do lokalnego uruchamiania aplikacji funkcji platformy Azure.

Azure Functions wymaga środowiska [Node. js](https://nodejs.org/en/download/) w wersji 8 lub 10.

Aby można było zainstalować rozszerzenia, Azure Functions Core Tools obecnie wymagane [zestaw .NET Core SDK](https://www.microsoft.com/net/download) zainstalowane. Niemniej jednak znajomość platformy .NET nie jest wymagana do kompilowania aplikacji funkcji JavaScript platformy Azure.

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

1. W edytorze kodu Otwórz folder *src/Chat/JavaScript* w sklonowanym repozytorium.

1. Zmień nazwę pliku *Local. Settings. sample. JSON* na *Local. Settings. JSON*.

1. W pliku **Local. Settings. JSON**wklej parametry połączenia do wartości ustawienia **AzureSignalRConnectionString** . Zapisz plik.

1. Funkcje JavaScript są zorganizowane w foldery. W każdym folderze są dwa pliki: *Function. JSON* definiuje powiązania, które są używane w funkcji, a *index. js* jest treścią funkcji. W tej aplikacji funkcji są dostępne dwie funkcje wyzwalane przez protokół HTTP:

    - **Negotiate** — używa powiązania danych wejściowych *SignalRConnectionInfo* do generowania i zwracania prawidłowych informacji o połączeniu.
    - **messages — odbiera** komunikat rozmowy w treści żądania i używa powiązania danych wyjściowych *sygnalizującego* , aby emitować komunikat do wszystkich połączonych aplikacji klienckich.

1. Upewnij się, że jesteś w folderze *src/Chat/JavaScript* . Użyj Azure Functions Core Tools, aby zainstalować rozszerzenia wymagane do uruchomienia aplikacji.

    ```bash
    func extensions install
    ```

1. Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Tworzenie usługi sygnalizującej](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono i uruchomiono aplikację bezserwerową w czasie rzeczywistym w VS Code. Następnie Dowiedz się więcej na temat sposobu wdrażania Azure Functions z VS Code.

> [!div class="nextstepaction"]
> [Wdróż Azure Functions z VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
