---
title: 'Samouczek: Uwierzytelnianie usługi Azure SignalR Service za pomocą usługi Azure Functions'
description: W tym samouczku dowiesz się, jak uwierzytelniać klientów usługi Azure SignalR Service do usługi Azure Functions powiązania
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 28fb3295ef02d508ef04299398a61ea59828df35
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278840"
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

    ![Nowa usługa SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-quickstart-new.png)

1. Wprowadź następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Nazwa zasobu | Unikatowa nazwa wystąpienia usługi SignalR Service |
    | Grupa zasobów | Utwórz nową grupę zasobów o unikatowej nazwie |
    | Lokalizacja | Wybierz bliską Ci lokalizację |
    | Warstwa cenowa | Bezpłatna |

1. Kliknij pozycję **Utwórz**.

1. Po wdrożeniu wystąpienie otworzyć go w portalu i Znajdź jego strona ustawień. Zmień ustawienie tryb usługi na *aplikacje niewymagające użycia serwera*.

    ![Tryb usługi SignalR](media/signalr-concept-azure-functions/signalr-service-mode.png)


## <a name="initialize-the-function-app"></a>Inicjowanie aplikacji funkcji

### <a name="create-a-new-azure-functions-project"></a>Tworzenie nowego projektu usługi Azure Functions

1. W nowym oknie programu VS Code wybierz pozycję `File > Open Folder` w menu, aby utworzyć i otworzyć pusty folder w odpowiedniej lokalizacji. Będzie to główny folder projektu dla aplikacji, która zostanie przez Ciebie utworzona.

1. Przy użyciu rozszerzenia usługi Azure Functions w programie VS Code zainicjuj aplikację funkcji w głównym folderze projektu.
   1. Otwórz paletę poleceń w programie VS Code, wybierając z menu pozycję **View (Widok) > Command Palette (Paleta poleceń)** (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).
   1. Wyszukaj polecenie **Azure Functions: Create New Project** i wybierz je.
   1. Powinien zostać wyświetlony główny folder projektu. Zaznacz go lub użyj przycisku „Browse” („Przeglądaj”), aby go zlokalizować.
   1. Po wyświetleniu monitu dotyczącego języka wybierz pozycję **JavaScript**.

      ![Tworzenie aplikacji funkcji](media/signalr-tutorial-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Instalowanie rozszerzeń aplikacji funkcji

W tym samouczku powiązania usługi Azure Functions są używane do interakcji z usługą Azure SignalR Service. Podobnie jak w większości innych przypadków, powiązania usługi SignalR Service są dostępne jako rozszerzenie, które należy zainstalować przy użyciu interfejsu wiersza polecenia podstawowych narzędzi usługi Azure Functions, zanim będzie można ich użyć.

1. Otwórz terminal w programie VS Code, wybierając **Widok > terminalu** menu (Ctrl -\`).

1. Upewnij się, że bieżącym katalogiem jest główny folder projektu.

1. Zainstaluj rozszerzenie aplikacji funkcji usługi SignalR Service.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0
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
            "WEBSITE_NODE_DEFAULT_VERSION": "10.14.1",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "http://127.0.0.1:5500",
            "CORSCredentials": true
        }
    }
    ```

   * Wprowadź parametry połączenia usługi Azure SignalR Service w ustawieniu o nazwie `AzureSignalRConnectionString`. Uzyskaj wartość ze strony **Klucze** w ramach zasobu usługi Azure SignalR Service w witrynie Azure Portal; można użyć podstawowych lub pomocniczych parametrów połączenia.
   * Ustawienie `WEBSITE_NODE_DEFAULT_VERSION` nie jest używane lokalnie, ale jest wymagane podczas wdrażania na platformie Azure.
   * W sekcji `Host` konfigurowany jest port i ustawienia mechanizmu CORS na potrzeby lokalnego hosta usługi Functions (to ustawienie nie obowiązuje podczas uruchamiania na platformie Azure).

       > [!NOTE]
       > Serwera produkcyjnego jest zazwyczaj skonfigurowany, aby obsługiwać zawartość z `http://127.0.0.1:5500`. Jeśli okaże się, korzysta ona z innym adresem URL lub korzystania z innego serwera HTTP, zmień `CORS` ustawienie, aby odzwierciedlać poprawne źródło.

     ![Uzyskiwanie klucza usługi SignalR Service](media/signalr-tutorial-authenticate-azure-functions/signalr-get-key.png)

1. Zapisz plik.

    

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Tworzenie funkcji do uwierzytelniania użytkowników w ramach usługi SignalR Service

Gdy aplikacja czatu zostanie po razy pierwszy otwarta w przeglądarce, wymaga ona ważnych poświadczeń połączenia w celu nawiązania połączenia z usługą Azure SignalR Service. Utworzysz funkcję wyzwalaną przez protokół HTTP o nazwie *negocjowania* w Twojej aplikacji funkcji, aby zwrócić informacje o połączeniu.

> [!NOTE]
> Ta funkcja musi mieć nazwę *negocjowania* jako SignalR klient wymaga punktu końcowego, który kończy się `/negotiate`.

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz polecenie **Azure Functions: Create Function**.

1. Po wyświetleniu monitu podaj następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Folder aplikacji funkcji | Wybierz główny folder projektu |
    | Szablon | Wyzwalacz HTTP |
    | Name (Nazwa) | Negocjowania |
    | Poziom autoryzacji | Anonimowe |

    Folder o nazwie **negocjowania** utworzeniu zawiera nową funkcję.

1. Otwórz **negotiate/function.json** można skonfigurować powiązania funkcji. Zmodyfikuj zawartość pliku na następującą. Spowoduje to dodanie powiązania danych wejściowych, które generuje ważne poświadczenia dla klienta w celu nawiązania połączenia z centrum usługi Azure SignalR Service o nazwie `chat`.

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

1. Otwórz **negotiate/index.js** do wyświetlenia treści funkcji. Zmodyfikuj zawartość pliku na następującą.

    ```javascript
    module.exports = async function (context, req, connectionInfo) {
        context.res.body = connectionInfo;
    };
    ```

    Ta funkcja przyjmuje informacje dotyczące połączenia usługi SignalR z powiązania danych wejściowych i zwraca je do klienta w treści odpowiedzi HTTP. Klienta SignalR użyje tych informacji, aby nawiązać połączenie z wystąpieniem usługi SignalR.

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
                "name": "$return",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Spowoduje to wprowadzenie dwóch zmian do oryginalnej funkcji:
    * Zmienia trasę na wartość `messages` oraz ogranicza wyzwalacz HTTP do metody HTTP **POST**.
    * Dodaje usługi SignalR wyjściowych powiązania, który jest wysyłany komunikat zwrócony przez funkcję do wszystkich klientów podłączone do koncentratora SignalR Service o nazwie `chat`.

1. Zapisz plik.

1. Otwórz plik **SendMessage/index.js**, aby wyświetlić treść funkcji. Zmodyfikuj zawartość pliku na następującą.

    ```javascript
    module.exports = async function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        return {
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        };
    };
    ```

    Ta funkcja przyjmuje treść z żądania HTTP i wysyła ją do klientów połączonych z usługą SignalR Service, wywołując funkcję o nazwie `newMessage` w każdym z nich.

    Funkcja może odczytywać tożsamość nadawcy i akceptować wartość *recipient* w treści wiadomości, aby zezwolić na prywatne wysyłanie wiadomości do danego użytkownika. Takie funkcje będą używane w dalszej części tego samouczka.

1. Zapisz plik.

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Tworzenie i uruchamianie internetowego interfejsu użytkownika klienta czatu

Interfejs użytkownika aplikacji czatu to prosta aplikacja jednostronicowa utworzona za pomocą platformy Vue JavaScript. Będzie ona hostowana oddzielnie od aplikacji funkcji. Pracując lokalnie, uruchomisz interfejs internetowy przy użyciu rozszerzenia Live Server programu VS Code.

1. W programie VS Code utwórz nowy folder o nazwie **content** w katalogu głównym głównego folderu projektu.

1. W folderze **content** utwórz nowy plik o nazwie **index.html**.

1. Skopiuj i wklej zawartość pliku **[index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/2720a9a565e925db09ef972505e1c5a7a3765be4/docs/demo/chat-with-auth/index.html)**.

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

### <a name="create-a-storage-account"></a>Tworzenie konta usługi Storage

Konto usługi Azure Storage jest wymagane przez aplikację funkcji, działające na platformie Azure. Możesz również hostować strony sieci web w celu prowadzenia rozmowy interfejsu użytkownika za pomocą funkcji statycznych witryn internetowych w usłudze Azure Storage.

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** (**+**) przycisk tworzenia nowego zasobu platformy Azure.

1. Wybierz **magazynu** kategorii, następnie wybierz pozycję **konta magazynu**.

1. Wprowadź następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Subskrypcja | Wybierz subskrypcję zawierającą wystąpienia usługi SignalR |
    | Grupa zasobów | Wybierz grupę zasobów |
    | Nazwa zasobu | Unikatową nazwę konta magazynu |
    | Lokalizacja | Wybierz lokalizację, jak inne zasoby |
    | Wydajność | Standardowa (Standard) |
    | Rodzaj konta | StorageV2 (ogólnego przeznaczenia wersja 2) |
    | Replikacja | Magazyn lokalnie nadmiarowy (LRS) |
    | Warstwa dostępu | Gorąca |

1. Kliknij przycisk **przeglądu + Utwórz**, następnie **Utwórz**.

### <a name="configure-static-websites"></a>Konfigurowanie statycznych witryn sieci Web

1. Po utworzeniu konta magazynu, otwórz go w witrynie Azure portal.

1. Wybierz **statycznej witryny internetowej**.

1. Wybierz **włączone** do włączenia tej funkcji statycznej witryny internetowej.

1. W **nazwa dokumentu indeksu**, wprowadź *index.html*.

1. Kliknij pozycję **Zapisz**.

1. A **podstawowego punktu końcowego** pojawia się. Zanotuj tę wartość. Jego będą musieli skonfigurować aplikację funkcji.

### <a name="configure-function-app-for-authentication"></a>Konfigurowanie aplikacji funkcji na potrzeby uwierzytelniania

Do tej pory aplikacja czatu działa w sposób anonimowy. Na platformie Azure użyjesz [uwierzytelnianie usługi App Service](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) do uwierzytelnienia użytkownika. Identyfikator lub nazwa uwierzytelnionego użytkownika może zostać przekazana do powiązania *SignalRConnectionInfo* w celu wygenerowania informacji o połączeniu uwierzytelnianym jako użytkownik.

Podczas wysyłania wiadomości aplikacja może zdecydować, czy wysłać ją do wszystkich połączonych klientów, czy tylko do klientów, które zostały uwierzytelnione danemu użytkownikowi.

1. Otwórz w programie VS Code **negotiate/function.json**.

1. Wstaw [wyrażenie powiązania](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings) do właściwości *userId* powiązania *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. Spowoduje to ustawienie wartości na nazwę uwierzytelnionego użytkownika. Ten atrybut powinien teraz wyglądać następująco.

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


### <a name="deploy-function-app-to-azure"></a>Wdrażanie aplikacji funkcji na platformie Azure

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`), a następnie wybierz polecenie **Azure Functions: Deploy to Function App**.

1. Po wyświetleniu monitu podaj następujące informacje.

    | Name (Nazwa) | Wartość |
    |---|---|
    | Folder do wdrożenia | Wybierz główny folder projektu |
    | Subskrypcja | Wybierz swoją subskrypcję |
    | Aplikacja funkcji | Wybierz pozycję **Utwórz nową aplikację funkcji** |
    | Nazwa aplikacji funkcji | Wprowadź unikatową nazwę |
    | Grupa zasobów | Wybierz tę samą grupę zasobów, co w przypadku wystąpienia usługi SignalR Service |
    | Konto magazynu | Wybierz konto magazynu, która została utworzona wcześniej |

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

Ustawienia lokalne są przekazywane do aplikacji funkcji na platformie Azure. Jeśli zostanie wyświetlony monit o zastąpienie istniejących ustawień, wybierz pozycję **Tak na wszystko**.


### <a name="enable-app-service-authentication"></a>Włączanie uwierzytelniania usługi App Service

Uwierzytelnianie usługi App Service obsługuje uwierzytelnianie za pomocą kont Microsoft i Google oraz usług Azure Active Directory, Facebook i Twitter.

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz polecenie **Azure Functions: Open in portal**.

1. Wybierz subskrypcję i nazwę aplikacji funkcji, aby otworzyć aplikację funkcji w witrynie Azure Portal.

1. W aplikacji funkcji, która została otwarta w portalu, zlokalizuj **funkcje platformy** zaznacz **uwierzytelniania/autoryzacji**.

1. **Włącz** uwierzytelniania usługi App Service.

1. Z listy **Akcja do wykonania w przypadku nieuwierzytelnionego żądania** wybierz pozycję „Zaloguj się za pomocą wcześniej wybranego dostawcy uwierzytelniania”.

1. W polu**Dozwolone zewnętrzne adresy URL przekierowań** wprowadź zapisany wcześniej adres URL podstawowego internetowego punktu końcowego konta magazynu.

1. Postępuj zgodnie z dokumentacją wybranego dostawcy logowania, aby ukończyć konfigurację.

    - [Usługa Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Konto Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="update-the-web-app"></a>Aktualizowanie aplikacji internetowej

1. W witrynie Azure Portal przejdź do strony przeglądu aplikacji funkcji.

1. Skopiuj adres URL aplikacji funkcji.

    ![Uzyskiwanie adresu URL](media/signalr-tutorial-authenticate-azure-functions/signalr-get-url.png)

1. W programie VS Code otwórz plik **index.html** i zastąp wartość parametru `apiBaseUrl` adresem URL aplikacji funkcji.

1. Aplikację można skonfigurować przy użyciu uwierzytelniania za pomocą kont Microsoft i Google oraz usług Azure Active Directory, Facebook i Twitter. Wybierz dostawcę uwierzytelniania, który zostanie użyty, ustawiając wartość parametru `authProvider`.

1. Zapisz plik.

### <a name="deploy-the-web-application-to-blob-storage"></a>Wdrażanie aplikacji internetowej w magazynie obiektów blob

Aplikacja internetowa będzie hostowana za pomocą funkcji statycznych witryn internetowych usługi Azure Blob Storage.

1. Otwórz paletę poleceń programu VS Code (skrót: `Ctrl-Shift-P`, w systemie macOS: `Cmd-Shift-P`).

1. Wyszukaj i wybierz pozycję **usługi Azure Storage: Wdrażanie na statyczna witryna internetowa** polecenia.

1. Wprowadź następujące wartości:

    | Name (Nazwa) | Wartość |
    |---|---|
    | Subskrypcja | Wybierz swoją subskrypcję |
    | Konto magazynu | Wybierz konto magazynu, która została utworzona wcześniej |
    | Folder do wdrożenia | Wybierz **Przeglądaj** i wybierz *zawartości* folderu |

Pliki w *zawartości* folderu teraz powinny być wdrażane do statycznej witryny internetowej.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Włączanie mechanizmu współużytkowania zasobów między źródłami (CORS, Cross Origin Resource Sharing) aplikacji funkcji

Mimo że ustawienie mechanizmu CORS istnieje w pliku **local.settings.json**, nie jest ono propagowane do aplikacji funkcji na platformie Azure. Należy ustawić je osobno.

1. Otwórz aplikację funkcji w witrynie Azure portal.

1. W obszarze **funkcje platformy** zaznacz **CORS**.

    ![Wyszukiwanie mechanizmu CORS](media/signalr-tutorial-authenticate-azure-functions/signalr-find-cors.png)

1. W *dozwolone źródła* Dodaj wpis z statycznej witryny internetowej *podstawowego punktu końcowego* jako wartość (Usuń końcowe */*).

1. Aby zestaw SDK JavaScript SignalR wywołanie aplikacji funkcji z poziomu przeglądarki, musi być włączona obsługa poświadczenia w specyfikacji CORS. Zaznacz pole wyboru "Włącz dostęp — kontrola-Allow-Credentials".

    ![Włącz nagłówek Access-Control-Allow-Credentials](media/signalr-tutorial-authenticate-azure-functions/signalr-cors-credentials.png)

1. Kliknij pozycję **Zapisz**, aby utrwalić ustawienia mechanizmu CORS.

### <a name="try-the-application"></a>Sprawdzanie działania aplikacji

1. W przeglądarce przejdź do podstawowego internetowego punktu końcowego konta magazynu.

1. Wybierz pozycję **Zaloguj się**, aby uwierzytelnić się przy użyciu wybranego dostawcy uwierzytelniania.

1. Wysyłaj wiadomości publiczne, wprowadzając je do głównego okna czatu.

1. Wysyłaj wiadomości prywatne, klikając nazwę użytkownika w historii czatu. Otrzyma je tylko wybrany odbiorca.

Gratulacje! Bezserwerowa aplikacja czatu w czasie rzeczywistym została wdrożona!

![Demonstracja](media/signalr-tutorial-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, usuń grupę zasobów za pomocą witryny Azure Portal.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat sposobu używania usługi Azure Functions z usługą Azure SignalR Service. Dowiedz się więcej o tworzeniu bezserwerowych aplikacji w czasie rzeczywistym przy użyciu powiązań usługi SignalR dla usługi Azure Functions.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji czasu rzeczywistego za pomocą usługi Azure Functions](signalr-concept-azure-functions.md)
