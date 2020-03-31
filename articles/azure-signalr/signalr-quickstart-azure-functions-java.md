---
title: Tworzenie pokoju rozmów za pomocą funkcji Azure Functions i SignalR service za pomocą oprogramowania Java
description: Przewodnik Szybki start pokazujący, jak za pomocą usług Azure SignalR Service i Azure Functions utworzyć pokój czatu.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 890fc381afe0146e721e084e2dcd7eae9215d004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77083204"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Szybki start: tworzenie pokoju rozmów z usługą Azure Functions i SignalR Service za pomocą języka Java

Usługa Azure SignalR service umożliwia łatwe dodawanie funkcji w czasie rzeczywistym do aplikacji, a usługa Azure Functions to platforma bezserwerowa, która umożliwia uruchamianie kodu bez zarządzania infrastrukturą. W tym przewodniku Szybki start używasz oprogramowania Java do tworzenia aplikacji do czatu bez użycia serwera w czasie rzeczywistym przy użyciu usługi SignalR i funkcji.

## <a name="prerequisites"></a>Wymagania wstępne

- Edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/)
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Podstawowe narzędzia usług Azure Functions](https://github.com/Azure/azure-functions-core-tools#installing). Służy do uruchamiania aplikacji funkcji platformy Azure lokalnie.

   > [!NOTE]
   > Wymagane powiązania usługi SignalR w języku Java są obsługiwane tylko w usłudze Azure Function Core Tools w wersji 2.4.419 (wersja hosta 2.0.12332) lub wyższej.

   > [!NOTE]
   > Aby zainstalować rozszerzenia, narzędzia Podstawowe usługi Azure Functions wymagają zainstalowanego [zestawu SDK .NET Core.](https://www.microsoft.com/net/download) Do tworzenia aplikacji funkcji platformy Azure dla w języku JavaScript nie jest jednak wymagana jakakolwiek wiedza dotycząca platformy .NET.

- Zestaw [Java Developer Kit](https://www.azul.com/downloads/zulu/), wersja 8
- Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza

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

1. W edytorze kodu otwórz folder *src/chat/java* w sklonowanym repozytorium.

1. Zmień nazwę pliku *local.settings.sample.json* na *local.settings.json*.

1. W pliku **local.settings.json** wklej parametry połączenia jako wartość ustawienia **AzureSignalRConnectionString**. Zapisz plik.

1. Główny plik, który zawiera funkcje są w *src / chat / java / src / main / java / com / function/Functions.java:*

    - **negotiate** — ta funkcja generuje i zwraca ważne informacje o połączeniu przy użyciu danych wejściowych powiązania *SignalRConnectionInfo*.
    - **sendMessage** — odbiera wiadomość czatu w treści żądania i używa powiązania wyjściowego *SignalR* do emisji wiadomości do wszystkich połączonych aplikacji klienckich.

1. W terminalu upewnij się, że znajdujesz się w folderze *src/chat/java.* Tworzenie aplikacji funkcji.

    ```bash
    mvn clean package
    ```

1. Uruchom aplikację funkcji lokalnie.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start został utworzony i uruchomiony w czasie rzeczywistym bezserwerowej aplikacji przy użyciu Maven. Następnie dowiedz się, jak tworzyć funkcje Java Azure od podstaw.

> [!div class="nextstepaction"]
> [Tworzenie pierwszej funkcji przy użyciu języka Java i narzędzia Maven](../azure-functions/functions-create-first-java-maven.md)