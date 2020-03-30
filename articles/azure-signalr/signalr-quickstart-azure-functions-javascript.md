---
title: Tworzenie pokoju rozmów za pomocą funkcji Azure Functions i SignalR Service za pomocą języka JavaScript
description: Przewodnik Szybki start pokazujący, jak za pomocą usług Azure SignalR Service i Azure Functions utworzyć pokój czatu.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.openlocfilehash: 2726d5da2613be4ae2065246543d206cf814f353
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77083192"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Szybki start: tworzenie pokoju rozmów z usługą Azure Functions i SignalR Service za pomocą języka JavaScript

Usługa Azure SignalR service umożliwia łatwe dodawanie funkcji w czasie rzeczywistym do aplikacji, a usługa Azure Functions to platforma bezserwerowa, która umożliwia uruchamianie kodu bez zarządzania infrastrukturą. W tym przewodniku Szybki start używasz Języka JavaScript do tworzenia aplikacji czatu bez użycia serwera w czasie rzeczywistym przy użyciu usługi SignalR i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/)
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Podstawowe narzędzia azure functions](https://github.com/Azure/azure-functions-core-tools#installing), wersja 2 lub wyższa. Służy do uruchamiania aplikacji funkcji platformy Azure lokalnie.
- [Node.js](https://nodejs.org/en/download/), wersja 10.x

   > [!NOTE]
   > Przykłady powinny współpracować z innymi wersjami node.js, zobacz [dokumentację wersji środowiska wykonawczego usługi Azure Functions,](../azure-functions/functions-versions.md#languages) aby uzyskać więcej informacji.

> [!NOTE]
> Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

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

1. W edytorze kodu otwórz folder *src/chat/javascript* w sklonowanym repozytorium.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Funkcje języka JavaScript są organizowane w foldery. W każdym folderze znajdują się dwa pliki: plik *function.json* definiuje powiązania używane w funkcji, a plik *index.js* to treść funkcji. W tej aplikacji funkcji znajdują się dwie funkcje wyzwalane przez protokół HTTP:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **messages** — ta funkcja otrzymuje wiadomość czatu w treści żądania i używa powiązania danych wyjściowych *SignalR* do rozgłoszenia wiadomości do wszystkich połączonych aplikacji klienckich.

1. W terminalu upewnij się, że znajdujesz się w folderze *src/chat/javascript.* Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony i uruchomiony w czasie rzeczywistym bezserwerowej aplikacji w programie VS Code. Teraz dowiedz się więcej o sposobie wdrażania usługi Azure Functions z programu VS Code.

> [!div class="nextstepaction"]
> [Wdrażanie usługi Azure Functions za pomocą programu VS Code](/azure/javascript/tutorial-vscode-serverless-node-01)
