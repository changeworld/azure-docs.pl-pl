---
title: Tworzenie aplikacji internetowej Node.js w usłudze Azure App Service | Microsoft Docs
description: Wdróż swoją pierwszą aplikację Hello World w środowisku Node.js w usłudze Azure App Service Web Apps w ciągu kilku minut.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: cephalin
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 380e587fc8c921b395d63d1dbca10e2f5fb1b9ba
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433201"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Tworzenie aplikacji internetowej Node.js na platformie Azure 

Usługa Azure App Service oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. Ten samouczek Szybki start przedstawia sposób wdrażania aplikacji Node.js w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz konta platformy Azure, [zarejestruj się już dziś](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) , aby skorzystać z bezpłatnego konta z $200 na kredyty na korzystanie z platformy Azure w celu wypróbowania dowolnej kombinacji usług.

Wymagane [Visual Studio Code](https://code.visualstudio.com/) zainstalowane wraz z [Node. js i npm](https://nodejs.org/en/download), Menedżer pakietów środowiska Node. js.

Konieczne będzie również zainstalowanie [rozszerzenia Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice), za pomocą którego można tworzyć i wdrażać Web Apps systemu Linux na platformie Azure jako usługa (PaaS) oraz zarządzać nimi.

### <a name="sign-in"></a>Zaloguj

Po zainstalowaniu rozszerzenia Zaloguj się do konta platformy Azure. Na pasku działania wybierz pozycję logo platformy Azure, aby wyświetlić Eksplorator **usługi Azure App Service** . Wybierz pozycję **Zaloguj się do platformy Azure...** i postępuj zgodnie z instrukcjami.

![Zaloguj się do platformy Azure](containers/media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony komunikat o błędzie **"nie można odnaleźć subskrypcji o nazwie [Identyfikator subskrypcji]"** , może to być spowodowane faktem, że jesteś za serwerem proxy i nie można skontaktować się z interfejsem API platformy Azure. Skonfiguruj zmienne środowiskowe `HTTP_PROXY` i `HTTPS_PROXY` za pomocą informacji o serwerze proxy w terminalu przy użyciu `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Jeśli ustawienie zmiennych środowiskowych nie rozwiąże problemu, skontaktuj się z nami, wybierając poniższy przycisk **został uruchomiony na przycisku problemu** .

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Przed kontynuowaniem upewnij się, że wszystkie wymagania wstępne zostały zainstalowane i skonfigurowane.

W VS Code adres e-mail platformy Azure powinien zostać wyświetlony na pasku stanu i w ramach subskrypcji w Eksploratorze **usługi Azure App Service** .

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Tworzenie aplikacji node. js

Następnie Utwórz aplikację Node. js, którą można wdrożyć w chmurze. Ten przewodnik Szybki Start używa generatora aplikacji w celu szybkiego tworzenia szkieletu aplikacji z poziomu terminalu.

> [!TIP]
> Jeśli zakończysz już [samouczek środowiska Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), możesz przejść do wdrożenia na [platformie Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Tworzenie szkieletu nowej aplikacji przy użyciu generatora ekspresowego

[Express](https://www.expressjs.com) to popularna platforma do kompilowania i uruchamiania aplikacji node. js. Można tworzyć szkieletowo nowe aplikacje ekspresowe przy użyciu narzędzia [Generator ekspres](https://expressjs.com/en/starter/generator.html) . Generator ekspresowy jest dostarczany jako moduł npm i może być uruchamiany bezpośrednio (bez instalacji) za pomocą narzędzia wiersza polecenia npm `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

Parametry `--view pug --git` informują generatora, aby korzystał z aparatu szablonu [Pug](https://pugjs.org/api/getting-started.html) (wcześniej znanego jako `jade`) i aby utworzyć plik `.gitignore`.

Aby zainstalować wszystkie zależności aplikacji, przejdź do nowego folderu i uruchom `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Następnie upewnij się, że aplikacja działa. W terminalu uruchom aplikację za pomocą polecenia `npm start`, aby uruchomić serwer.

```bash
npm start
```

Teraz otwórz przeglądarkę i przejdź do [http://localhost:3000](http://localhost:3000), gdzie powinien wyglądać w następujący sposób:

![Uruchamianie aplikacji Express](containers/media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Wdrażanie na platformie Azure

W tej sekcji wdrażasz aplikację Node. js przy użyciu VS Code i Azure App Service rozszerzenia. W tym przewodniku Szybki Start jest używany Najpopularniejszy model wdrażania, w którym aplikacja jest spakowana i wdrażana w Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Wdróż przy użyciu Azure App Service

Najpierw otwórz folder aplikacji w VS Code.

```bash
code .
```

W Eksploratorze **usługi Azure App Service** wybierz ikonę strzałki w górę, aby wdrożyć aplikację na platformie Azure.

![Wdróż w aplikacji sieci Web](containers/media/quickstart-nodejs/deploy.png)

> [!TIP]
> Możesz również wdrożyć z **palety poleceń** (Ctrl + Shift + P), wpisując polecenie "Deploy to Web App" i uruchamiając **Azure App Service: Deploy to Web App** .

1. Wybierz katalog, który jest aktualnie otwarty, `myExpressApp`.

1. Wybierz opcję tworzenia opartą na systemie operacyjnym, w którym chcesz wdrożyć:

    - Linux: wybierz pozycję **Utwórz nową aplikację sieci Web**.
    - System Windows: wybierz pozycję **Utwórz nową aplikację sieci Web... Zaawansowane**.

1. Wpisz globalnie unikatową nazwę aplikacji sieci Web, a następnie naciśnij klawisz ENTER. Prawidłowymi znakami dla nazwy aplikacji są "a-z", "0-9" i "-".

1. W przypadku określania systemu Linux wybierz wersję środowiska Node. js po wyświetleniu monitu. Zalecana jest wersja **LTS** .

1. W przypadku systemu Windows przy użyciu opcji *Advanced** postępuj zgodnie z dodatkowymi monitami:
    1. Wybierz pozycję **Utwórz nową grupę zasobów**, a następnie wprowadź nazwę grupy zasobów.
    1. Wybierz **system Windows** dla systemu operacyjnego.
    1. Wybierz istniejący plan App Service lub Utwórz nowy. Możesz wybrać warstwę cenową podczas tworzenia nowego planu.
    1. Wybierz pozycję **Pomiń teraz** po wyświetleniu monitu o Application Insights.
    1. Wybierz region w sąsiedztwie lub blisko zasobów, do których chcesz uzyskać dostęp.

1. Po udzieleniu odpowiedzi na wszystkie monity kanał powiadomień pokazuje zasoby platformy Azure, które są tworzone dla aplikacji.

1. Wybierz opcję **tak** po wyświetleniu monitu, aby zaktualizować konfigurację w celu uruchomienia `npm install` na serwerze docelowym. Aplikacja zostanie wdrożona.

    ![Skonfigurowane wdrożenie](containers/media/quickstart-nodejs/server-build.png)

1. Po uruchomieniu wdrożenia zostanie wyświetlony monit o zaktualizowanie obszaru roboczego, tak aby późniejsze wdrożenia były automatycznie ukierunkowane na tę samą App Service aplikacji sieci Web. Wybierz opcję **tak** , aby upewnić się, że zmiany zostaną wdrożone w odpowiedniej aplikacji.

    ![Skonfigurowane wdrożenie](containers/media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Upewnij się, że aplikacja nasłuchuje na porcie dostarczonym przez zmienną środowiskową portów: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Przeglądanie aplikacji na platformie Azure

Po zakończeniu wdrażania wybierz pozycję **Przeglądaj witrynę sieci** Web w wierszu polecenia, aby wyświetlić świeżą wdrożoną aplikację sieci Web.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony komunikat o błędzie **"nie masz uprawnień do wyświetlenia tego katalogu lub strony"** , uruchomienie aplikacji prawdopodobnie nie powiodło się. Przejdź do następnej sekcji i Przejrzyj dane wyjściowe dziennika, aby znaleźć i naprawić błąd. Jeśli nie możesz rozwiązać tego problemu, skontaktuj się z nami, wybierając Poniższy **problem** . Mamy przyjemność pomóc!

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Aktualizowanie aplikacji

Zmiany w tej aplikacji można wdrożyć przy użyciu tego samego procesu i wybierając istniejącą aplikację zamiast tworzenia nowej.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

W tej sekcji dowiesz się, jak wyświetlać dzienniki (lub "ogon") z działającej aplikacji App Service. Wszystkie wywołania `console.log` w aplikacji są wyświetlane w oknie dane wyjściowe w Visual Studio Code.

Znajdź aplikację w Eksploratorze **usługi Azure App Service** , kliknij prawym przyciskiem myszy aplikację, a następnie wybierz polecenie **Wyświetl dzienniki przesyłania strumieniowego**.

Po wyświetleniu monitu wybierz włączenie rejestrowania i ponownie uruchom aplikację. Po ponownym uruchomieniu aplikacji zostanie otwarte okno dane wyjściowe VS Code z połączeniem ze strumieniem dziennika.

![Wyświetlanie dzienników przesyłania strumieniowego](containers/media/quickstart-nodejs/view-logs.png)

![Włącz rejestrowanie i ponowne uruchamianie](containers/media/quickstart-nodejs/enable-restart.png)

Po kilku sekundach zobaczysz komunikat informujący o tym, że masz połączenie z usługą przesyłania strumieniowego dziennika. Odśwież stronę kilka razy, aby zobaczyć więcej działań.

    ```bash
    2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    ```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Następne kroki

Gratulacje, udało Ci się ukończyć ten przewodnik Szybki Start.

Następnie zapoznaj się z innymi rozszerzeniami platformy Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Narzędzia platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Narzędzia interfejsu wiersza polecenia platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Narzędzia Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Lub uzyskać wszystkie te elementy, instalując [pakiet Node Pack dla platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
