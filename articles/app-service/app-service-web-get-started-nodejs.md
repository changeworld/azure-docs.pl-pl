---
title: 'Szybki start: tworzenie aplikacji sieci Web Node.js'
description: Wdrożenie pierwszego node.js Hello World w usłudze Azure App Service w ciągu kilku minut. Wdrażanie przy użyciu programu Visual Studio Code, który jest jednym z wielu sposobów wdrażania w usłudze App Service.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: mvc, devcenter, seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: bcb0ae2457175e7511c3539187d72d48e80baa5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80047133"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Tworzenie aplikacji internetowej Node.js na platformie Azure 

Rozpocznij pracę z usługą Azure App Service, tworząc aplikację Node.js/Express lokalnie przy użyciu programu Visual Studio Code, a następnie wdrażając aplikację w chmurze. Ponieważ korzystasz z bezpłatnej warstwy usługi App Service, nie ponosisz żadnych kosztów, aby ukończyć ten szybki start.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- [Node.js i npm](https://nodejs.org). Uruchom polecenie, `node --version` aby sprawdzić, czy plik Node.js jest zainstalowany.
- [Program Visual Studio Code](https://code.visualstudio.com/)
- [Rozszerzenie usługi Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice) dla kodu programu Visual Studio.

## <a name="clone-and-run-a-local-nodejs-application"></a>Klonowanie i uruchamianie lokalnej aplikacji Node.js

1. Na komputerze lokalnym otwórz terminal i sklonuj przykładowe repozytorium:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Przejdź do nowego folderu aplikacji:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Uruchom aplikację, aby przetestować ją lokalnie:

    ```bash
    npm start
    ```
    
1. Otwórz przeglądarkę i [http://localhost:1337](http://localhost:1337)przejdź do pliku . Przeglądarka powinna wyświetlać "Hello World!".

1. Naciśnij **klawisz Ctrl**+**C** w terminalu, aby zatrzymać serwer.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tej sekcji można wdrożyć aplikację Node.js na platformie Azure przy użyciu programu VS Code i rozszerzenia usługi Azure App Service.

1. W terminalu upewnij się, że znajdujesz się w folderze *nodejs-docs-hello-world,* a następnie uruchom program Visual Studio Code za pomocą następującego polecenia:

    ```bash
    code .
    ```

1. Na pasku aktywności vs kod wybierz logo platformy Azure, aby wyświetlić eksploratora **usługi AZURE APP SERVICE.** Wybierz **pozycję Zaloguj się na platformie Azure...** i postępuj zgodnie z instrukcjami. (Zobacz [rozwiązywanie problemów z logowaniem platformy Azure](#troubleshooting-azure-sign-in) poniżej, jeśli napotkasz błędy). Po zalogowaniu eksplorator powinien wyświetlić nazwę subskrypcji platformy Azure.

    ![Logowanie do platformy Azure](containers/media/quickstart-nodejs/sign-in.png)

1. W eksploratorze **usługi AZURE APP SERVICE** programu VS Code wybierz niebieską ikonę strzałki w górę, aby wdrożyć aplikację na platformie Azure. (Można również wywołać to samo polecenie z **palety poleceń** **(Ctrl**+**Shift**+**P),** wpisując "wdrażanie w aplikacji sieci Web" i wybierając pozycję Azure App **Service: Deploy to Web App**).

    ![Wdrażanie w aplikacji sieci Web](containers/media/quickstart-nodejs/deploy.png)
        
1. Wybierz folder *nodejs-docs-hello-world.*

1. Wybierz opcję tworzenia na podstawie systemu operacyjnego, do którego chcesz wdrożyć:

    - Linux: wybierz pozycję **Utwórz nową aplikację sieci Web**
    - Windows: wybierz pozycję **Utwórz nową aplikację sieci Web... Zaawansowane**

1. Wpisz globalnie unikatową nazwę aplikacji internetowej i naciśnij **klawisz Enter**. Nazwa musi być unikatowa na całej platformie Azure i używać tylko znaków alfanumerycznych ('A-Z', 'a-z', '0-9') i łączników ('-').

1. Jeśli kierowanie na system Linux, po wyświetleniu monitu wybierz wersję node.js. Zalecana jest wersja **LTS.**

1. Jeśli kierowanie na system Windows, postępuj zgodnie z dodatkowymi monitami:
    1. Wybierz **pozycję Utwórz nową grupę zasobów,** a następnie `AppServiceQS-rg`wprowadź nazwę grupy zasobów, taką jak .
    1. Wybierz **pozycję Windows** dla systemu operacyjnego.
    1. Wybierz **pozycję Utwórz nowy plan usługi app service** `AppServiceQS-plan`, a następnie wprowadź nazwę planu (na przykład ), a następnie wybierz **pozycję F1 Free** dla warstwy cenowej.
    1. Wybierz **pozycję Pomiń teraz** po wyświetleniu monitu o wgląd w aplikację.
    1. Wybierz region w pobliżu lub w pobliżu zasobów, do których chcesz uzyskać dostęp.

1. Po odpowiedzi na wszystkie monity, VS Code pokazuje zasoby platformy Azure, które są tworzone dla aplikacji w wyskakującym okienku powiadomień.

    Podczas wdrażania w systemie Linux wybierz **opcję Tak** po `npm install` wyświetleniu monitu o zaktualizowanie konfiguracji w celu uruchomienia na docelowym serwerze systemu Linux.

    ![Monitowanie o zaktualizowanie konfiguracji na docelowym serwerze systemu Linux](containers/media/quickstart-nodejs/server-build.png)

1. Wybierz **pozycję Tak** po wyświetleniu monitu z **użyciem opcji Zawsze wdrażaj obszar roboczy "nodejs-docs-hello-world" do (nazwa aplikacji)"**. Wybranie **opcji Tak** powoduje, że program VS Code automatycznie kieruje tę samą aplikację sieci Web usługi App Service z kolejnymi wdrożeniami.

1. Jeśli wdrożenie w systemie Linux wybierz **pozycję Przeglądaj** witrynę sieci Web w wierszu polecenia wyświetlenia świeżo wdrożonej aplikacji sieci web po zakończeniu wdrażania. Przeglądarka powinna wyświetlać "Hello World!"

1. W przypadku wdrażania w systemie Windows należy najpierw ustawić numer wersji node.js dla aplikacji sieci web:

    1. W programie VS Code rozwiń węzeł dla nowej usługi aplikacji, kliknij prawym przyciskiem myszy **pozycję Ustawienia aplikacji**i wybierz polecenie Dodaj nowe **ustawienie...**:

        ![Polecenie Dodaj ustawienie aplikacji](containers/media/quickstart-nodejs/add-setting.png)

    1. Wprowadź `WEBSITE_NODE_DEFAULT_VERSION` klucz ustawień.
    1. Wprowadź `10.15.2` wartość ustawienia.
    1. Kliknij prawym przyciskiem myszy węzeł usługi aplikacji i wybierz polecenie **Uruchom ponownie**

        ![Uruchom ponownie polecenie usługi aplikacji](containers/media/quickstart-nodejs/restart.png)

    1. Kliknij prawym przyciskiem myszy węzeł usługi aplikacji jeszcze raz i wybierz pozycję **Przeglądaj witrynę sieci Web**.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Rozwiązywanie problemów z logowaniem na platformie Azure

Jeśli podczas logowania na platformie Azure zostanie wyświetlony błąd **"Nie można znaleźć subskrypcji o nazwie [identyfikator subskrypcji]"** może to być spowodowane tym, że użytkownik znajduje się za serwerem proxy i nie może uzyskać dostępu do interfejsu API platformy Azure. Konfigurowanie `HTTP_PROXY` `HTTPS_PROXY` zmiennych środowiskowych i zmiennych środowiskowych `export`z informacjami proxy w terminalu za pomocą .

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Jeśli ustawienie zmiennych środowiskowych nie rozwiąże problemu, skontaktuj się z nami, wybierając przycisk **Napotkałem** problem powyżej.

### <a name="update-the-app"></a>Aktualizowanie aplikacji

Zmiany w tej aplikacji można wdrożyć, wnosząc zmiany w programie VS Code, zapisując pliki, a następnie używając tego samego procesu, co przed wybraniem istniejącej aplikacji, a nie utworzeniem nowej.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

Można wyświetlić dane wyjściowe dziennika `console.log`(wywołania) z aplikacji bezpośrednio w oknie wyjściowym kodu VS.

1. W Eksploratorze **USŁUGI APLIKACJI AZURE** kliknij prawym przyciskiem myszy węzeł aplikacji i wybierz polecenie **Rozpocznij dzienniki przesyłania strumieniowego**.

    ![Uruchom przesyłanie strumieniowe dzienników
](containers/media/quickstart-nodejs/view-logs.png)

1. Po wyświetleniu monitu wybierz opcję włączenia rejestrowania i uruchom ponownie aplikację. Po ponownym uruchomieniu aplikacji zostanie otwarte okno wyjściowe kodu VS z połączeniem ze strumieniem dziennika. 

    ![Włączanie rejestrowania i ponownego uruchamiania](containers/media/quickstart-nodejs/enable-restart.png)

1. Po kilku sekundach okno wyjściowe wyświetla komunikat wskazujący, że masz połączenie z usługą przesyłania strumieniowego dziennika. Można wygenerować więcej aktywności wyjściowej, odświeżając stronę w przeglądarce.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Następne kroki

Gratulacje, pomyślnie ukończyłeś ten szybki start!

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia usługi Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Możesz też uzyskać je wszystkie, instalując pakiet [node pack dla platformy Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
