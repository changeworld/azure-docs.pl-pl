---
title: Azure SignalR Service bez użycia serwera Przewodnik Szybki Start — Java
description: Przewodnik Szybki start pokazujący, jak za pomocą usług Azure SignalR Service i Azure Functions utworzyć pokój czatu.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 9e4e64b99a69e523547bae04146c7460d08bc1df
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261177"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-java"></a>Szybki start: Tworzenie pokoju rozmów przy użyciu funkcji Azure SignalR Service przy użyciu języka Java

Usługa Azure SignalR Service umożliwia łatwe dodawanie funkcji czasu rzeczywistego do aplikacji. Usługa Azure Functions to bezserwerowa platforma, która pozwala uruchamiać kod bez zarządzania jakąkolwiek infrastrukturą. W tym przewodniku Szybki start dowiesz się, jak utworzyć bezserwerową aplikację czatu w czasie rzeczywistym za pomocą usług SignalR Service i Functions.

## <a name="prerequisites"></a>Wymagania wstępne

Czynności przedstawione w tym przewodniku Szybki start można wykonywać w systemie macOS, Windows lub Linux.

Upewnij się, że masz zainstalowany edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/).

Zainstaluj [podstawowe narzędzia usługi Azure Functions (w wersji 2)](https://github.com/Azure/azure-functions-core-tools#installing), aby móc lokalnie uruchamiać aplikacje funkcji platformy Azure.

> [!NOTE]
> Aby użyć powiązania usługi SignalR w języku Java, upewnij się, że używasz wersji 2.4.419 lub nowszej podstawowe narzędzia usługi Azure Functions (wersja 2.0.12332 hosta).

Aby możliwe było instalowanie rozszerzeń, podstawowe narzędzia usługi Azure Functions wymagają zainstalowanego [zestawu .NET Core SDK](https://www.microsoft.com/net/download). Do tworzenia aplikacji funkcji platformy Azure dla w języku JavaScript nie jest jednak wymagana jakakolwiek wiedza dotycząca platformy .NET.

Aby opracowywać aplikacje funkcji przy użyciu języka Java, trzeba mieć zainstalowane następujące składniki:

* Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8.
* Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza.

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

1. W edytorze kodu, otwórz *rozmowy/src/java* folderu w repozytorium sklonowane.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Główny plik, który zawiera funkcje znajdują się w *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **SendMessage** — odbiera wiadomości rozmowy w treści żądania i używa *SignalR* danych wyjściowych powiązanie wysyłać wiadomość do wszystkich połączonych aplikacji klienckich.

1. W terminalu, upewnij się, że jesteś w *rozmowy/src/java* folderu. Tworzenie aplikacji funkcji.

    ```bash
    mvn clean package
    ```

1. Uruchom lokalnie aplikację funkcji.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start została skompilowana i uruchomienia aplikacji bez użycia serwera w czasie rzeczywistym przy użyciu narzędzia Maven. Następnie Dowiedz się więcej o sposobie tworzenia funkcji platformy Azure dla języka Java, od podstaw.

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji przy użyciu języka Java i Maven](../azure-functions/functions-create-first-java-maven.md)