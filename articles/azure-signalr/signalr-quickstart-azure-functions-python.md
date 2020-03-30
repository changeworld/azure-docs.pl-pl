---
title: Szybki start bezserwerowy usługi Azure SignalR — Python
description: Przewodnik Szybki start pokazujący, jak za pomocą usług Azure SignalR Service i Azure Functions utworzyć pokój czatu.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.openlocfilehash: 0cf8705cf2567a60129681c2db41b0868f8fe182
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75392151"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Szybki start: tworzenie pokoju rozmów z usługą Azure Functions i SignalR Service przy użyciu języka Python

Usługa Azure SignalR Service umożliwia łatwe dodawanie funkcji czasu rzeczywistego do aplikacji. Usługa Azure Functions to bezserwerowa platforma, która pozwala uruchamiać kod bez zarządzania jakąkolwiek infrastrukturą. W tym przewodniku Szybki start dowiesz się, jak utworzyć bezserwerową aplikację czatu w czasie rzeczywistym za pomocą usług SignalR Service i Functions.

## <a name="prerequisites"></a>Wymagania wstępne

Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

Upewnij się, że masz zainstalowany edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/).

Zainstaluj [podstawowe narzędzia usługi Azure Functions (w](https://github.com/Azure/azure-functions-core-tools#installing) wersji 2.7.1505 lub nowszej), aby uruchamiać aplikacje usługi Python Azure Function lokalnie.

Usługa Azure Functions wymaga [języka Python 3.6 lub 3.7](https://www.python.org/downloads/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal pod adresem <https://portal.azure.com/> przy użyciu danych konta Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Konfigurowanie i uruchamianie aplikacji funkcji platformy Azure

1. W przeglądarce z otwartą witryną Azure Portal potwierdź, że wdrożone wcześniej wystąpienie usługi SignalR Service zostało pomyślnie utworzone, wyszukując jego nazwę w polu wyszukiwania w górnej części witryny. Wybierz wystąpienie, aby je otworzyć.

    ![Wyszukiwanie wystąpienia usługi SignalR Service](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Wybierz pozycję **Klucze**, aby wyświetlić parametry połączenia dla wystąpienia usługi SignalR Service.

1. Zaznacz i skopiuj podstawowe parametry połączenia.

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. W edytorze kodu otwórz folder *src/chat/python* w sklonowanym repozytorium.

1. Aby lokalnie rozwijać i testować funkcje języka Python, należy pracować w środowisku Python 3.6 lub 3.7. Uruchom następujące polecenia, aby utworzyć i aktywować środowisko wirtualne o nazwie `.venv`.

    **Linux lub macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Funkcje Języka Python są zorganizowane w foldery. W każdym folderze znajdują się dwa pliki: *function.json* definiuje powiązania, które są używane w funkcji, a * \_ \_\_\_init .py* jest treścią funkcji. W tej aplikacji funkcji znajdują się dwie funkcje wyzwalane przez protokół HTTP:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **messages** — ta funkcja otrzymuje wiadomość czatu w treści żądania i używa powiązania danych wyjściowych *SignalR* do rozgłoszenia wiadomości do wszystkich połączonych aplikacji klienckich.

1. W terminalu z włączonym środowiskiem wirtualnym upewnij się, że znajdujesz się w folderze *src/chat/python.* Zainstaluj niezbędne pakiety Pythona za pomocą PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Uruchamianie aplikacji funkcji](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony i uruchomiony w czasie rzeczywistym bezserwerowej aplikacji w programie VS Code. Teraz dowiedz się więcej o sposobie wdrażania usługi Azure Functions z programu VS Code.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Functions za pomocą programu VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
