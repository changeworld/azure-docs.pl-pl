---
title: 'Samouczek: Uwierzytelnianie usługi Azure SignalR Service za pomocą usługi Azure Functions'
description: Z tego samouczka dowiesz się, jak uwierzytelnić klientów usługi Azure SignalR Service
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: 34cbb4d2c8a1e84499961802ca7bd07408375345
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409397"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Samouczek: Uwierzytelnianie usługi Azure SignalR Service za pomocą usługi Azure Functions

Samouczek krok po kroku z informacjami dotyczącymi tworzenia pokoju czatu z uwierzytelnianiem i obsługą prywatnych wiadomości przy użyciu usług Azure Functions i SignalR Service oraz uwierzytelniania usługi App Service.

## <a name="introduction"></a>Wprowadzenie

### <a name="technologies-used"></a>Użyte technologie

* [Usługa Azure Functions](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu) — interfejs API zaplecza służący do uwierzytelniania użytkowników i wysyłania wiadomości czatu
* [Usługa Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu) — służy do emisji nowych wiadomości do połączonych klientów czatu
* [Usługa Azure Storage](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu) — służy do hostowania statycznej witryny internetowej na potrzeby interfejsu użytkownika klienta czatu

### <a name="prerequisites"></a>Wymagania wstępne

Do wykonania instrukcji znajdujących się w tym samouczku wymagane jest następujące oprogramowanie.

* [Usługa Git](https://git-scm.com/downloads)
* [Platforma Node.js](https://nodejs.org/en/download/) (wersja 10.x)
* [Zestaw SDK .NET](https://www.microsoft.com/net/download) (wersja 2.x, wymagana na potrzeby rozszerzeń usługi Functions)
* [Podstawowe narzędzia usługi Azure Functions](https://github.com/Azure/azure-functions-core-tools) (wersja 2)
* [Program Visual Studio Code](https://code.visualstudio.com/) (VS Code) z następującymi rozszerzeniami
  * [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) — umożliwia pracę z usługą Azure Functions w programie VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) — udostępnia lokalnie strony internetowe na potrzeby testowania

## <a name="sign-into-the-azure-portal"></a>Logowanie do witryny Azure Portal

Przejdź do witryny [ Azure Portal](https://portal.azure.com/) i zaloguj się przy użyciu swoich poświadczeń.

## <a name="create-an-azure-signalr-service-instance"></a>Tworzenie wystąpienia usługi Azure SignalR Service

Aplikację usługi Azure Functions utworzysz i przetestujesz lokalnie. Uzyska ona dostęp do wystąpienia usługi SignalR Service na platformie Azure, które należy wcześniej utworzyć.

1. Kliknij przycisk **Utwórz zasób** (**+**), aby utworzyć nowy zasób platformy Azure.

1. Wyszukaj usługę **SignalR Service** i wybierz ją. Kliknij pozycję **Utwórz**.

    ![Nowa usługa SignalR Service](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Wprowadź następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Nazwa zasobu | Unikatowa nazwa wystąpienia usługi SignalR Service |
    | Grupa zasobów | Utworzenie nowej grupy zasobów |
    | Lokalizacja | Wybierz bliską Ci lokalizację |
    | Warstwa cenowa | Bezpłatna |

1. Kliknij pozycję **Utwórz**.

## <a name="initialize-the-function-app"></a>Inicjowanie aplikacji funkcji

### <a name="create-a-new-azure-functions-project"></a>Tworzenie nowego projektu usługi Azure Functions

1. W nowym oknie programu VS Code wybierz pozycję `File > Open Folder` w menu, aby utworzyć i otworzyć pusty folder w odpowiedniej lokalizacji. Będzie to główny folder projektu dla aplikacji, która zostanie przez Ciebie utworzona.

1. Przy użyciu rozszerzenia usługi Azure Functions w programie VS Code zainicjuj aplikację funkcji w głównym folderze projektu.
    1. Otwórz paletę poleceń w programie VS Code, wybierając z menu pozycję **View (Widok) > Command Palette (Paleta poleceń)** (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).
    1. Wyszukaj polecenie **Azure Functions: Create New Project** i wybierz je.
    1. Powinien zostać wyświetlony główny folder projektu. Zaznacz go lub użyj przycisku „Browse” („Przeglądaj”), aby go zlokalizować.
    1. Po wyświetleniu monitu dotyczącego języka wybierz pozycję **JavaScript**.

    ![Tworzenie aplikacji funkcji](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Instalowanie rozszerzeń aplikacji funkcji

W tym samouczku powiązania usługi Azure Functions są używane do interakcji z usługą Azure SignalR Service. Podobnie jak w większości innych przypadków, powiązania usługi SignalR Service są dostępne jako rozszerzenie, które należy zainstalować przy użyciu interfejsu wiersza polecenia podstawowych narzędzi usługi Azure Functions, zanim będzie można ich użyć.

1. Otwórz terminal w programie VS Code, wybierając z menu pozycję **View (Widok) > Integrated Terminal (Zintegrowany terminal)** (Ctrl-\`).

1. Upewnij się, że bieżącym katalogiem jest główny folder projektu.

1. Zainstaluj rozszerzenie aplikacji funkcji usługi SignalR Service.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Konfigurowanie ustawień aplikacji

Podczas lokalnego uruchamiania i debugowania środowiska uruchomieniowego usługi Azure Functions ustawienia aplikacji są odczytywane z pliku **local.settings.json**. Zaktualizuj ten plik przy użyciu parametrów połączenia utworzonego wcześniej wystąpienia usługi SignalR Service.

1. W programie VS Code wybierz plik **local.settings.json** w okienku eksploratora, aby go otworzyć.

1. Zastąp jego zawartość następującym kodem.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Wprowadź parametry połączenia usługi Azure SignalR Service w ustawieniu o nazwie `AzureSignalRConnectionString`. Uzyskaj wartość ze strony **Klucze** w ramach zasobu usługi Azure SignalR Service w witrynie Azure Portal; można użyć podstawowych lub pomocniczych parametrów połączenia.
    * Ustawienie `WEBSITE_NODE_DEFAULT_VERSION` nie jest używane lokalnie, ale jest wymagane podczas wdrażania na platformie Azure.
    * W sekcji `Host` konfigurowany jest port i ustawienia mechanizmu CORS na potrzeby lokalnego hosta usługi Functions (to ustawienie nie obowiązuje podczas uruchamiania na platformie Azure).

    ![Uzyskiwanie klucza usługi SignalR Service](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Zapisz plik.

    ![Aktualizowanie ustawień lokalnych](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Tworzenie funkcji do uwierzytelniania użytkowników w ramach usługi SignalR Service

Gdy aplikacja czatu zostanie po razy pierwszy otwarta w przeglądarce, wymaga ona ważnych poświadczeń połączenia w celu nawiązania połączenia z usługą Azure SignalR Service. Aby zwrócić te informacje o połączeniu, utworzysz funkcję wyzwalaną przez protokół HTTP o nazwie *SignalRInfo* w aplikacji funkcji.

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz polecenie **Azure Functions: Create Function**.

1. Po wyświetleniu monitu podaj następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Folder aplikacji funkcji | Wybierz główny folder projektu |
    | Szablon | Wyzwalacz HTTP |
    | Name (Nazwa) | SignalRInfo |
    | Poziom autoryzacji | Anonimowe |

    Zostanie utworzony folder o nazwie **SignalRInfo**, który zawiera nową funkcję.

1. Otwórz plik **SignalRInfo/function.json**, aby skonfigurować powiązania dla funkcji. Zmodyfikuj zawartość pliku na następującą. Spowoduje to dodanie powiązania danych wejściowych, które generuje ważne poświadczenia dla klienta w celu nawiązania połączenia z centrum usługi Azure SignalR Service o nazwie `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    Właściwość `userId` w powiązaniu `signalRConnectionInfo` jest używana do tworzenia uwierzytelnionego połączenia usługi SignalR Service. Pozostaw właściwość pustą na potrzeby programowania w środowisku lokalnym. Zostanie ona użyta po wdrożeniu aplikacji funkcji na platformie Azure.

1. Otwórz plik **SignalRInfo/index.js**, aby wyświetlić treść funkcji. Zmodyfikuj zawartość pliku na następującą.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    Ta funkcja przyjmuje informacje dotyczące połączenia usługi SignalR z powiązania danych wejściowych i zwraca je do klienta w treści odpowiedzi HTTP.

## <a name="create-a-function-to-send-chat-messages"></a>Tworzenie funkcji do wysyłania wiadomości czatu

Aplikacja internetowa wymaga także interfejsu API protokołu HTTP do wysyłania wiadomości. Utworzysz funkcję wyzwalaną przez protokół HTTP o nazwie *SignalRInfo*, która wysyła wiadomości do wszystkich połączonych klientów przy użyciu usługi SignalR Service.

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz polecenie **Azure Functions: Create Function**.

1. Po wyświetleniu monitu podaj następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Folder aplikacji funkcji | Wybierz główny folder projektu |
    | Szablon | Wyzwalacz HTTP |
    | Name (Nazwa) | SendMessage |
    | Poziom autoryzacji | Anonimowe |

    Zostanie utworzony folder o nazwie **SendMessage**, który zawiera nową funkcję.

1. Otwórz plik **SendMessage/function.json**, aby skonfigurować powiązania dla funkcji. Zmodyfikuj zawartość pliku na następującą.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Spowoduje to wprowadzenie dwóch zmian do oryginalnej funkcji:
    * Zmienia trasę na wartość `messages` oraz ogranicza wyzwalacz HTTP do metody HTTP **POST**.
    * Dodaje powiązanie danych wyjściowych usługi SignalR Service, które wysyła wiadomości do wszystkich klientów połączonych z centrum usługi SignalR Service o nazwie `chat`.

1. Zapisz plik.

1. Otwórz plik **SendMessage/index.js**, aby wyświetlić treść funkcji. Zmodyfikuj zawartość pliku na następującą.

    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```

    Ta funkcja przyjmuje treść z żądania HTTP i wysyła ją do klientów połączonych z usługą SignalR Service, wywołując funkcję o nazwie `newMessage` w każdym z nich.

    Funkcja może odczytywać tożsamość nadawcy i akceptować wartość *recipient* w treści wiadomości, aby zezwolić na prywatne wysyłanie wiadomości do danego użytkownika. Takie funkcje będą używane w dalszej części tego samouczka.

1. Zapisz plik.

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Tworzenie i uruchamianie internetowego interfejsu użytkownika klienta czatu

Interfejs użytkownika aplikacji czatu to prosta aplikacja jednostronicowa utworzona za pomocą platformy Vue JavaScript. Będzie ona hostowana oddzielnie od aplikacji funkcji. Pracując lokalnie, uruchomisz interfejs internetowy przy użyciu rozszerzenia Live Server programu VS Code.

1. W programie VS Code utwórz nowy folder o nazwie **content** w katalogu głównym głównego folderu projektu.

1. W folderze **content** utwórz nowy plik o nazwie **index.html**.

1. Skopiuj i wklej zawartość pliku **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)**.

1. Zapisz plik.

1. Naciśnij klawisz **F5**, aby lokalnie uruchomić aplikację funkcji i dołączyć debuger.

1. Pozostawiając plik **index.html** otwarty, uruchom rozszerzenie Live Server, otwierając paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`) i wybierając polecenie **Live Server: Open with Live Server**. Rozszerzenie Live Server otworzy aplikację w przeglądarce.

1. Zostanie otwarta aplikacja. Wprowadź wiadomość w polu czatu, a następnie naciśnij klawisz Enter. Odśwież aplikację, aby wyświetlić nowe wiadomości. Ponieważ uwierzytelnianie nie zostało skonfigurowane, wszystkie wiadomości zostaną wysłane jako użytkownik „anonimowy”.

## <a name="deploy-to-azure-and-enable-authentication"></a>Wdrażanie na platformie Azure i włączanie uwierzytelniania

Aplikacje funkcji i czatu były uruchamiana lokalnie. Teraz wdrożysz je na platformie Azure i włączysz uwierzytelniania oraz obsługę prywatnych wiadomości w aplikacji.

### <a name="log-into-azure-with-vs-code"></a>Zaloguj się na platformie Azure za pomocą programu VS Code

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz polecenie **Azure: Sign in**.

1. Postępuj zgodnie z instrukcjami, aby ukończyć proces logowania w przeglądarce.

### <a name="configure-function-app-for-authentication"></a>Konfigurowanie aplikacji funkcji na potrzeby uwierzytelniania

Do tej pory aplikacja czatu działa w sposób anonimowy. Na platformie Azure użyjesz [uwierzytelnianie usługi App Service](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) do uwierzytelnienia użytkownika. Identyfikator lub nazwa uwierzytelnionego użytkownika może zostać przekazana do powiązania *SignalRConnectionInfo* w celu wygenerowania informacji o połączeniu uwierzytelnianym jako użytkownik.

Podczas wysyłania wiadomości aplikacja może zdecydować, czy wysłać ją do wszystkich połączonych klientów, czy tylko do klientów, które zostały uwierzytelnione danemu użytkownikowi.

1. W programie VS Code otwórz plik **SendMessage/function.json**.

1. Wstaw [wyrażenie powiązania](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns) do właściwości *userId* powiązania *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. Spowoduje to ustawienie wartości na nazwę uwierzytelnionego użytkownika. Ten atrybut powinien teraz wyglądać następująco.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Zapisz plik.

### <a name="deploy-function-app"></a>Wdrażanie aplikacji funkcji

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`), a następnie wybierz polecenie **Azure Functions: Deploy to Function App**.

1. Po wyświetleniu monitu podaj następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Folder do wdrożenia | Wybierz główny folder projektu |
    | Subskrypcja | Wybierz swoją subskrypcję |
    | Aplikacja funkcji | Wybierz pozycję **Utwórz nową aplikację funkcji** |
    | Nazwa aplikacji funkcji | Wprowadź unikatową nazwę |
    | Grupa zasobów | Wybierz tę samą grupę zasobów, co w przypadku wystąpienia usługi SignalR Service |
    | Konto magazynu | Wybierz pozycję **Utwórz nowe konto magazynu** |
    | Nazwa konta magazynu | Wprowadź unikatową nazwę (zawierającą od 3 do 24 znaków alfanumerycznych) |
    | Lokalizacja | Wybierz bliską Ci lokalizację |

    Na platformie Azure zostanie utworzona nowa aplikacja funkcji i rozpocznie się proces wdrażania. Zaczekaj na zakończenie wdrożenia.

### <a name="upload-function-app-local-settings"></a>Przekazywanie lokalnych ustawień aplikacji funkcji

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz polecenie **Azure Functions: Upload local settings**.

1. Po wyświetleniu monitu podaj następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Plik ustawień lokalnych | local.settings.json |
    | Subskrypcja | Wybierz swoją subskrypcję |
    | Aplikacja funkcji | Wybierz wcześniej wdrożoną aplikację funkcji |
    | Nazwa aplikacji funkcji | Wprowadź unikatową nazwę |

Ustawienia lokalne są przekazywane do aplikacji funkcji na platformie Azure. Jeśli zostanie wyświetlony monit o zastąpienie istniejących ustawień, wybierz pozycję **Tak na wszystko**.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Włączanie mechanizmu współużytkowania zasobów między źródłami (CORS, Cross Origin Resource Sharing) aplikacji funkcji

Mimo że ustawienie mechanizmu CORS istnieje w pliku **local.settings.json**, nie jest ono propagowane do aplikacji funkcji na platformie Azure. Należy ustawić je osobno.

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz polecenie **Azure Functions: Open in portal**.

1. Wybierz subskrypcję i nazwę aplikacji funkcji, aby otworzyć aplikację funkcji w witrynie Azure Portal.

1. W otworzonej witrynie na karcie **Funkcje platformy** wybierz pozycję **CORS**.

    ![Wyszukiwanie mechanizmu CORS](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Dodaj wpis z wartością `*`.

1. Usuń wszystkie istniejące wpisy.

1. Kliknij pozycję **Zapisz**, aby utrwalić ustawienia mechanizmu CORS.

    ![Ustawianie mechanizmu CORS](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> W rzeczywistych aplikacjach zamiast zezwalać na działanie mechanizmu CORS w ramach wszystkich źródeł (`*`), bezpieczniejszą metodą jest wprowadzenie określonych wpisów CORS dla każdej domeny, która ich wymaga.

### <a name="update-the-web-app"></a>Aktualizowanie aplikacji internetowej

1. W witrynie Azure Portal przejdź do strony przeglądu aplikacji funkcji.

1. Skopiuj adres URL aplikacji funkcji.

    ![Uzyskiwanie adresu URL](media/signalr-authenticate-azure-functions/signalr-get-url.png)

1. W programie VS Code otwórz plik **index.html** i zastąp wartość parametru `apiBaseUrl` adresem URL aplikacji funkcji.

1. Aplikację można skonfigurować przy użyciu uwierzytelniania za pomocą kont Microsoft i Google oraz usług Azure Active Directory, Facebook i Twitter. Wybierz dostawcę uwierzytelniania, który zostanie użyty, ustawiając wartość parametru `authProvider`.

1. Zapisz plik.

### <a name="deploy-the-web-application-to-blob-storage"></a>Wdrażanie aplikacji internetowej w magazynie obiektów blob

Aplikacja internetowa będzie hostowana za pomocą funkcji statycznych witryn internetowych usługi Azure Blob Storage.

1. Kliknij przycisk **Nowy** (**+**), aby utworzyć nowy zasób platformy Azure.

1. W obszarze **Magazyn** wybierz pozycję **Konto magazynu**.

1. Wprowadź następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Name (Nazwa) | Unikatowa nazwa konta magazynu |
    | Rodzaj konta | StorageV2 (ogólnego przeznaczenia wersja 2) |
    | Lokalizacja | Wybierz ten sam region, co w przypadku innych zasobów |
    | Replikacja | Magazyn lokalnie nadmiarowy (LRS) |
    | Wydajność | Standardowa (Standard) |
    | Warstwa dostępu | Gorąca |
    | Grupa zasobów | Wybierz tę samą grupę zasobów, co w przypadku innych zasobów w tym samouczku |

1. Kliknij pozycję **Utwórz**.

    ![Tworzenie magazynu](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. Po utworzeniu konta magazynu otwórz je w witrynie Azure Portal.

1. Wybierz pozycję **Statyczna witryna internetowa (wersja zapoznawcza)** w obszarze nawigacji po lewej stronie.

1. Wybierz pozycję **Włącz**.

1. Wprowadź wartość `index.html` jako **nazwę dokumentu indeksu**.

1. Kliknij pozycję **Zapisz**.

    ![Konfigurowanie witryn statycznych](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. Kliknij link **$web** na stronie, aby otworzyć kontener obiektów blob.

1. Kliknij pozycję **Przekaż** i przekaż plik **index.html** znajdujący się w folderze **content**.

1. Wróć do strony **Statyczna witryna internetowa**. Zapisz wartość z pola **Podstawowy punkt końcowy**. Jest to adres URL aplikacji internetowej.

### <a name="enable-app-service-authentication"></a>Włączanie uwierzytelniania usługi App Service

Uwierzytelnianie usługi App Service obsługuje uwierzytelnianie za pomocą kont Microsoft i Google oraz usług Azure Active Directory, Facebook i Twitter.

1. Pozostawiając aplikację funkcji wciąż otwartą w portalu, znajdź kartę **Funkcje platformy** i wybierz pozycję **Uwierzytelnianie/autoryzacja**.

1. **Włącz** uwierzytelniania usługi App Service.

1. Z listy **Akcja do wykonania w przypadku nieuwierzytelnionego żądania** wybierz pozycję „Zaloguj się za pomocą wcześniej wybranego dostawcy uwierzytelniania”.

1. W polu**Dozwolone zewnętrzne adresy URL przekierowań** wprowadź zapisany wcześniej adres URL podstawowego internetowego punktu końcowego konta magazynu.

1. Postępuj zgodnie z dokumentacją wybranego dostawcy logowania, aby ukończyć konfigurację.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Konto Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="try-the-application"></a>Sprawdzanie działania aplikacji

1. W przeglądarce przejdź do podstawowego internetowego punktu końcowego konta magazynu.

1. Wybierz pozycję **Zaloguj się**, aby uwierzytelnić się przy użyciu wybranego dostawcy uwierzytelniania.

1. Wysyłaj wiadomości publiczne, wprowadzając je do głównego okna czatu.

1. Wysyłaj wiadomości prywatne, klikając nazwę użytkownika w historii czatu. Otrzyma je tylko wybrany odbiorca.

Gratulacje! Bezserwerowa aplikacja czatu w czasie rzeczywistym została wdrożona!

![Demonstracja](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, usuń grupę zasobów za pomocą witryny Azure Portal.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat sposobu używania usługi Azure Functions z usługą Azure SignalR Service. Dowiedz się więcej o tworzeniu bezserwerowych aplikacji w czasie rzeczywistym przy użyciu powiązań usługi SignalR dla usługi Azure Functions.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji w czasie rzeczywistym za pomocą usługi Azure Functions](signalr-overview-azure-functions.md)