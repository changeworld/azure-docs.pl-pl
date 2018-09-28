---
title: 'Szybki start: aplikacja bezserwerowa w usłudze Azure SignalR Service — JavaScript | Microsoft Docs'
description: Przewodnik Szybki start pokazujący, jak za pomocą usług Azure SignalR Service i Azure Functions utworzyć pokój czatu.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.tgt_pltfrm: Node.js
ms.workload: tbd
ms.date: 09/23/2018
ms.author: zhshang
ms.openlocfilehash: 12ac64a89f27744e1e731633d784135c97c423e0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991032"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-javascript"></a>Szybki start: tworzenie pokoju czatu za pomocą usług Azure Functions i SignalR Service przy użyciu języka JavaScript

Usługa Azure SignalR Service umożliwia łatwe dodawanie funkcji czasu rzeczywistego do aplikacji. Usługa Azure Functions to bezserwerowa platforma, która pozwala uruchamiać kod bez zarządzania jakąkolwiek infrastrukturą. W tym przewodniku Szybki start dowiesz się, jak utworzyć bezserwerową aplikację czatu w czasie rzeczywistym za pomocą usług SignalR Service i Functions.


## <a name="prerequisites"></a>Wymagania wstępne

Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

Upewnij się, że masz zainstalowany edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/).

Zainstaluj [podstawowe narzędzia usługi Azure Functions (w wersji 2)](https://github.com/Azure/azure-functions-core-tools#installing), aby móc lokalnie uruchamiać aplikacje funkcji platformy Azure.

Aby możliwe było instalowanie rozszerzeń, podstawowe narzędzia usługi Azure Functions wymagają zainstalowanego [zestawu .NET Core SDK](https://www.microsoft.com/net/download). Do tworzenia aplikacji funkcji platformy Azure dla w języku JavaScript nie jest jednak wymagana jakakolwiek wiedza dotycząca platformy .NET.

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

1. W edytorze kodu otwórz folder *chat/src/javascript* w sklonowanym repozytorium.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Funkcje języka JavaScript są organizowane w foldery. W każdym folderze znajdują się dwa pliki: plik *function.json* definiuje powiązania używane w funkcji, a plik *index.js* to treść funkcji. W tej aplikacji funkcji znajdują się dwie funkcje wyzwalane przez protokół HTTP:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **messages** — ta funkcja otrzymuje wiadomość czatu w treści żądania i używa powiązania danych wyjściowych *SignalR* do rozgłoszenia wiadomości do wszystkich połączonych aplikacji klienckich.

1. Z poziomu terminalu upewnij się, że znajdujesz się w folderze *chat/src/javascript*. Użyj podstawowych narzędzi usługi Azure Functions, aby zainstalować rozszerzenia wymagane do uruchomienia aplikacji.

    ```bash
    func extensions install
    ```

1. Uruchom aplikację funkcji.

    ```bash
    func start
    ```

    ![Tworzenie usługi SignalR Service](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)


[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono i uruchomiono bezserwerową aplikację czasu rzeczywistego w programie VS Code. W dalszej kolejności zapoznaj się ze sposobem opracowywania i wdrażania funkcji usługi Azure Functions za pomocą programu Visual Studio.

> [!div class="nextstepaction"]
> [Opracowywanie funkcji usługi Azure Functions przy użyciu programu Visual Studio](../azure-functions/functions-develop-vs.md)