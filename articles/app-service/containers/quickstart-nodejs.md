---
title: Tworzenie aplikacji sieci Web w języku Node. js — Azure App Service
description: Jak wdrożyć aplikację Node. js w celu Azure App Service
author: msangapu
ms.author: msangapu
ms.date: 08/12/2019
ms.topic: quickstart
ms.service: app-service
ms.devlang: javascript
ms.openlocfilehash: 8d679a95cc89c2ae7774b7f7b51b9d0aadd89d12
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390970"
---
# <a name="create-a-nodejs-app-in-azure"></a>Tworzenie aplikacji w języku Node. js na platformie Azure

Usługa Azure App Service oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie. Ten samouczek Szybki start przedstawia sposób wdrażania aplikacji Node.js w usłudze Azure App Service.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz konta platformy Azure, [zarejestruj się już dziś](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) , aby skorzystać z bezpłatnego konta z $200 na kredyty na korzystanie z platformy Azure w celu wypróbowania dowolnej kombinacji usług.

Wymagane [Visual Studio Code](https://code.visualstudio.com/) zainstalowane wraz z [Node. js i npm](https://nodejs.org/en/download), Menedżer pakietów środowiska Node. js.

Konieczne będzie również zainstalowanie [rozszerzenia Azure App Service](vscode:extension/ms-azuretools.vscode-azureappservice), za pomocą którego można tworzyć i wdrażać Web Apps systemu Linux na platformie Azure jako usługa (PaaS) oraz zarządzać nimi.

### <a name="sign-in"></a>Logowanie

Po zainstalowaniu rozszerzenia Zaloguj się do konta platformy Azure. Na pasku działania kliknij logo platformy Azure, aby wyświetlić Eksplorator **usługi Azure App Service** . Kliknij pozycję **Zaloguj się na platformie Azure...** i postępuj zgodnie z instrukcjami.

![Zaloguj się do platformy Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony komunikat o błędzie **"nie można odnaleźć subskrypcji o nazwie [Identyfikator subskrypcji]"** , może to być spowodowane faktem, że jesteś za serwerem proxy i nie można skontaktować się z interfejsem API platformy Azure. Skonfiguruj `HTTP_PROXY` `export`i `HTTPS_PROXY` zmienne środowiskowe za pomocą informacji o serwerze proxy w terminalu przy użyciu.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Jeśli ustawienie zmiennych środowiskowych nie rozwiąże problemu, skontaktuj się z nami, klikając przycisk **mam w** poniższym przycisku problemu.

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

Przed kontynuowaniem upewnij się, że wszystkie wymagania wstępne zostały zainstalowane i skonfigurowane.

W VS Code adres e-mail platformy Azure powinien zostać wyświetlony na pasku stanu i w ramach subskrypcji w Eksploratorze **usługi Azure App Service** .

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Tworzenie aplikacji node. js

Następnie Utwórz aplikację Node. js, którą można wdrożyć w chmurze. Ten przewodnik Szybki Start używa generatora aplikacji w celu szybkiego tworzenia szkieletu aplikacji z poziomu terminalu.

> [!TIP]
> Jeśli zakończysz już [samouczek środowiska Node. js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), możesz przejść od razu do [wdrożenia witryny sieci Web](#deploy-the-website).

### <a name="install-the-express-generator"></a>Instalowanie generatora ekspresowego

[Express](https://www.expressjs.com) to popularna platforma do kompilowania i uruchamiania aplikacji node. js. Można tworzyć szkieletowo nowe aplikacje ekspresowe przy użyciu narzędzia [Generator ekspres](https://expressjs.com/en/starter/generator.html) . Generator ekspresowy jest dostarczany jako moduł npm i instalowany przy użyciu narzędzia `npm`wiersza polecenia npm.

```bash
npm install -g express-generator
```

`-g` Przełącznik instaluje globalnie Generator ekspresowy na komputerze, aby można było uruchomić go z dowolnego miejsca.

### <a name="scaffold-a-new-application"></a>Tworzenie szkieletu nowej aplikacji

Następnie tworzy szkielet nowej aplikacji ekspresowej wywoływanej `myExpressApp` przez uruchomienie:

```bash
express myExpressApp --view pug --git
```

Parametry informują generatora, aby używał aparatu szablonów Pug (dawniej znany `jade`jako) i aby utworzyć `.gitignore` plik. [](https://pugjs.org/api/getting-started.html) `--view pug --git`

Aby zainstalować wszystkie zależności aplikacji, przejdź do nowego folderu i uruchom `npm install`polecenie.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Następnie upewnij się, że aplikacja działa. W terminalu uruchom aplikację za pomocą polecenia, `npm start` aby uruchomić serwer.

```bash
npm start
```

Teraz otwórz przeglądarkę i przejdź do lokalizacji, [http://localhost:3000](http://localhost:3000)w której powinny zostać wyświetlone następujące elementy:

![Uruchamianie aplikacji Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-website"></a>Wdrażanie witryny internetowej

W tej sekcji wdrażasz witrynę sieci Web Node. js przy użyciu VS Code i Azure App Service rozszerzenia. W tym przewodniku Szybki Start jest używany Najpopularniejszy model wdrażania, w którym aplikacja jest spakowana i wdrażana w Azure Web App on Linux.

### <a name="deploy-using-azure-app-service"></a>Wdróż przy użyciu Azure App Service

Najpierw otwórz folder aplikacji w VS Code.

```bash
code .
```

W Eksploratorze **usługi Azure App Service** kliknij ikonę strzałki w górę, aby wdrożyć aplikację na platformie Azure.

![Wdróż w aplikacji sieci Web](./media/quickstart-nodejs/deploy.png)

> [!TIP]
> Możesz również wdrożyć z **palety poleceń** (Ctrl + Shift + P), wpisując "Deploy to Web App" i uruchamiając **Azure App Service: Polecenie Wdróż w aplikacji** sieci Web.

1. Wybierz katalog, `myExpressApp`który jest aktualnie otwarty.

2. Wybierz pozycję **Utwórz nową aplikację sieci Web**.

3. Wpisz globalnie unikatową nazwę aplikacji sieci Web, a następnie naciśnij klawisz ENTER. Prawidłowymi znakami dla nazwy aplikacji są "a-z", "0-9" i "-".

4. Wybierz **wersję środowiska Node. js**, zaleca się LTS.

    Kanał powiadomień przedstawia zasoby platformy Azure, które są tworzone dla aplikacji.

Kliknij przycisk **tak** po wyświetleniu monitu, aby `npm install` zaktualizować konfigurację do uruchomienia na serwerze docelowym. Aplikacja zostanie wdrożona.

![Skonfigurowane wdrożenie](./media/quickstart-nodejs/server-build.png)

Po uruchomieniu wdrożenia zostanie wyświetlony monit o zaktualizowanie obszaru roboczego, tak aby późniejsze wdrożenia były automatycznie ukierunkowane na tę samą App Service aplikacji sieci Web. Wybierz opcję **tak** , aby upewnić się, że zmiany zostaną wdrożone w odpowiedniej aplikacji.

![Skonfigurowane wdrożenie](./media/quickstart-nodejs/save-configuration.png)

> [!TIP]
> Upewnij się, że aplikacja nasłuchuje na porcie dostarczonym przez zmienną środowiskową portów `process.env.PORT`:.

### <a name="browse-the-website"></a>Przeglądanie witryny sieci Web

Po zakończeniu wdrażania kliknij pozycję **Przeglądaj witrynę sieci Web** w wierszu polecenia, aby wyświetlić nowo wdrożoną witrynę sieci Web.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli zostanie wyświetlony komunikat o błędzie **"nie masz uprawnień do wyświetlenia tego katalogu lub strony"** , uruchomienie aplikacji prawdopodobnie nie powiodło się. Przejdź do następnej sekcji i Przejrzyj dane wyjściowe dziennika, aby znaleźć i naprawić błąd. Jeśli nie możesz rozwiązać tego problemu, skontaktuj się z nami, klikając poniższy przycisk **został uruchomiony** . Mamy przyjemność pomóc!

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="updating-the-website"></a>Aktualizowanie witryny sieci Web

Zmiany w tej aplikacji można wdrożyć przy użyciu tego samego procesu i wybierając istniejącą aplikację zamiast tworzenia nowej.

## <a name="viewing-logs"></a>Wyświetlanie dzienników

W tej sekcji dowiesz się, jak wyświetlać dzienniki z działającej witryny sieci Web (lub "ogon"). Wszystkie wywołania `console.log` w lokacji są wyświetlane w oknie dane wyjściowe w Visual Studio Code.

Znajdź aplikację w Eksploratorze **usługi Azure App Service** , kliknij prawym przyciskiem myszy aplikację, a następnie wybierz polecenie **Wyświetl dzienniki przesyłania strumieniowego**.

Po wyświetleniu monitu wybierz włączenie rejestrowania i ponownie uruchom aplikację. Po ponownym uruchomieniu aplikacji zostanie otwarte okno dane wyjściowe VS Code z połączeniem ze strumieniem dziennika.

![Wyświetlanie dzienników przesyłania strumieniowego](./media/quickstart-nodejs/view-logs.png)

![Włącz rejestrowanie i ponowne uruchamianie](./media/quickstart-nodejs/enable-restart.png)

Po kilku sekundach zobaczysz komunikat informujący o tym, że masz połączenie z usługą przesyłania strumieniowego dziennika.

```bash
Connecting to log-streaming service...
2017-12-21 17:33:51.428 INFO  - Container practical-mustache_2 for site practical-mustache initialized successfully.
2017-12-21 17:33:56.500 INFO  - Container logs
```

Odśwież stronę kilka razy w przeglądarce, aby zobaczyć dane wyjściowe dziennika.

```bash
2017-12-21 17:35:17.774 INFO  - Container logs
2017-12-21T17:35:14.955412230Z GET / 304 141.798 ms - -
2017-12-21T17:35:15.248930479Z GET /stylesheets/style.css 304 3.180 ms - -
2017-12-21T17:35:15.378623115Z GET /favicon.ico 404 53.839 ms - 995
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
