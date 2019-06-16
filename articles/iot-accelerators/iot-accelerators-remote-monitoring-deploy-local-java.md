---
title: Wdrażanie rozwiązania do monitorowania zdalnego z lokalnie (przy użyciu środowiska IntelliJ IDE) — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak wdrożyć zdalnego akcelerator rozwiązań do monitorowania na komputerze lokalnym przy użyciu środowiska IntelliJ na potrzeby projektowania i testowania.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66015439"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie — IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań monitorowania zdalnego na komputerze lokalnym na potrzeby projektowania i testowania. Dowiesz się, jak uruchomić mikrousługi w programie IntelliJ. Wdrożenie lokalne mikrousług używa następujących usług w chmurze: Usługi IoT Hub, Cosmos DB, usługi Azure Stream Analytics i Azure Time Series Insights w chmurze.

Jeśli chcesz uruchomić akcelerator rozwiązania monitorowania zdalnego na platformie Docker na komputerze lokalnym, zobacz [wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie — Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązania monitorowania zdalnego, należy aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Ustawienia komputera

Aby wykonać wdrożenie lokalne, potrzebne są następujące narzędzia, które są zainstalowane na lokalnej maszynie do programowania:

* [Usługa Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Wersja Community środowiska IntelliJ](https://www.jetbrains.com/idea/download/)
* [Scala wtyczkę IntelliJ](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ Plugin SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Wtyczka IntelliJ SBT wykonawcy](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest wymagane w przypadku komputerów z systemem interfejsu wiersza polecenia, które skrypty umożliwia tworzenie zasobów platformy Azure. Nie używaj Node.js v10.

> [!NOTE]
> IntelliJ IDE jest dostępna dla Windows i Mac.

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Zdalne monitorowanie repozytoriów kodu źródłowego zawierają kod źródłowy i pliki konfiguracji platformy Docker, należy uruchomić mikrousługi obrazów platformy Docker.

Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia można przejść do odpowiedniego folderu na komputerze lokalnym. Następnie uruchom jedno z następujących zestawów poleceń, aby sklonować repozytorium java:

Aby pobrać najnowszą wersję implementacji mikrousług java, uruchom polecenie:


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Te polecenia Pobierz kod źródłowy wszystkie mikrousługi oprócz skryptów, których można używać do uruchamiania mikrousługi lokalnie. Mimo że nie ma potrzeby kodu źródłowego, aby uruchomić mikrousług na platformie Docker, kod źródłowy jest przydatne, jeśli planujesz później zmodyfikować akcelerator rozwiązań i lokalne testowanie zmian.

## <a name="deploy-the-azure-services"></a>Wdrażaj usługi platformy Azure

Mimo że w tym artykule pokazano, jak uruchomić mikrousługi lokalnie, są one zależne od usług platformy Azure działające w chmurze. Użyj poniższego skryptu wdrażania usług platformy Azure. W poniższych przykładach skryptów przyjęto założenie, że używasz repozytorium java na komputerze Windows. Jeśli pracujesz w innym środowisku, Dostosuj ścieżek, rozszerzeń plików i separatorami ścieżki odpowiednio.

### <a name="create-new-azure-resources"></a>Tworzenie nowych zasobów platformy Azure

Jeśli jeszcze nie utworzono wymaganych zasobów platformy Azure, wykonaj następujące kroki:

1. W środowisku wiersza polecenia i przejdź do **\services\scripts\local\launch** folderu w sklonowanej kopii repozytorium.

1. Uruchom następujące polecenia, aby zainstalować **komputerów** interfejsu wiersza polecenia narzędzia i zaloguj się do konta platformy Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Uruchom **start.cmd** skryptu. Skrypt wyświetli monit o podanie następujących informacji:
   * Nazwa rozwiązania.
   * Subskrypcja platformy Azure, która ma być używana.
   * Lokalizacja centrum danych platformy Azure do użycia.

     Skrypt tworzy grupę zasobów na platformie Azure, nazwą rozwiązania. Ta grupa zasobów zawiera zasoby platformy Azure, który korzysta z akceleratora rozwiązań. Można usunąć tej grupy zasobów, gdy nie są już potrzebne odpowiednich zasobów.

     Skrypt ten dodaje również zestaw zmiennych środowiskowych z prefiksem **komputerów** na komputer lokalny. Te zmienne środowiskowe zawierają szczegółowe informacje dotyczące monitorowania zdalnego był możliwy odczyt z zasobem usługi Azure Key Vault. Ten zasób usługi Key Vault jest, gdzie zdalne monitorowanie odczyta jego wartości konfiguracji z.

     > [!TIP]
     > Po ukończeniu działania skryptu, zapisuje również zmiennych środowiskowych w pliku o nazwie  **\<folderu macierzystego\>\\.pcs\\\<Nazwa rozwiązania\>ENV** . Można je dla wdrożenia akcelerator rozwiązań w przyszłości. Należy pamiętać, że wszelkie zmienne środowiskowe, ustaw na komputerze lokalnym zastąpić wartości w **usług\\skrypty\\lokalnego\\ENV** plików po uruchomieniu **docker-compose**.

1. Zamknij ze środowiska wiersza polecenia.

### <a name="use-existing-azure-resources"></a>Użyj istniejących zasobów platformy Azure

Jeśli już utworzono wymaganych zasobów platformy Azure, utwórz odpowiednie zmienne środowiskowe na komputerze lokalnym.
Ustaw zmienne środowiskowe do wykonania poniższych czynności:
* **PCS_KEYVAULT_NAME** — Nazwa zasobu usługi Azure Key Vault
* **PCS_AAD_APPID** — identyfikator aplikacji usługi AAD
* **PCS_AAD_APPSECRET** — klucz tajny aplikacji usługi AAD

Wartości konfiguracji będą odczytywane z tego zasobu usługi Azure Key Vault. Te zmienne środowiskowe mogą być zapisane w  **\<folderu macierzystego\>\\.pcs\\\<Nazwa rozwiązania\>ENV** pliku z wdrożenia. Należy pamiętać, że zmienne środowiskowe ustawione na komputerze lokalnym, Zastąp wartości w **usług\\skrypty\\lokalnego\\ENV** plików po uruchomieniu **narzędzia docker compose**.

Niektórych elementów konfiguracji wymaganych przez mikrousługi są przechowywane w wystąpieniu **usługi Key Vault** utworzony na początkowym wdrożeniu. Odpowiednie zmienne w magazynie keyvault powinien być modyfikowany, zgodnie z potrzebami.

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji uruchamiasz mikrousług monitorowania zdalnego. Działania interfejsu użytkownika sieci web, symulacji urządzenia, uwierzytelniania i ASA Menedżera usługi na platformie Docker i mikrousług w programie IntelliJ.

### <a name="run-the-device-simulation-service"></a>Uruchom usługę symulacji urządzenia

Otwórz nowe okno wiersza polecenia, należy upewnić się, że masz dostęp do zmiennych środowiskowych, ustawiane przez **start.cmd** skryptu w poprzedniej sekcji.

Uruchom następujące polecenie, aby uruchomić kontener platformy Docker dla usługi symulacji urządzenia. Usługa symuluje urządzeń dla rozwiązania do monitorowania zdalnego.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Uruchom usługę uwierzytelniania

Otwórz nowe okno wiersza polecenia i uruchom następujące polecenie, aby uruchomić kontener platformy Docker dla usługi uwierzytelniania. Umożliwia zarządzanie użytkownikami autoryzacji do dostępu do rozwiązań IoT platformy Azure.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Uruchom usługę Menedżera ASA

Otwórz nowe okno wiersza polecenia i uruchom następujące polecenie, aby uruchomić kontener platformy Docker dla usługi Menedżer ASA. Umożliwia zarządzanie zadań usługi Azure Stream Analytics (ASA), w tym ustawienia konfiguracji i uruchamianie, zatrzymywanie oraz monitorowanie ich Stany.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Wdrażaj wszystkie mikrousługi na komputerze lokalnym

Poniższe kroki pokazują, jak uruchomić mikrousług zdalnego monitorowania w programie IntelliJ:

#### <a name="import-project"></a>Importowanie projektu

1. Uruchom środowisko IDE IntelliJ
1. Wybierz **importowania projektu** i wybierz polecenie **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Utwórz konfiguracje wykonywania

1. Wybierz **Uruchom > Edytuj konfiguracje**
1. Wybierz **Dodawanie nowej konfiguracji > sbt zadań** 
1. Wprowadź **nazwa** i wprowadź **zadania** podczas uruchamiania 
1. Wybierz **katalog roboczy** opartego na usłudze, aby uruchomić
1. Kliknij przycisk **Zastosuj > Ok** Aby zapisać wybrane opcje.
1. Utwórz konfiguracje wykonywania dla następujących usług:
    * Usługa sieci Web (services\config)
    * Usługa sieci Web (danych telemetrycznych services\device)
    * Usługa sieci Web (services\iothub manager)
    * Usługa sieci Web (services\storage karta)

Na przykład na poniższej ilustracji przedstawiono Dodawanie konfiguracji dla usługi:

[![Dodawanie konfiguracji](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Tworzenie złożonych konfiguracji

1. Aby uruchomić wszystkie usługi, wybierz razem **Dodawanie nowej konfiguracji > złożona (c)**
1. Wprowadź **nazwa** i **Dodaj sbt zadania**
1. Kliknij przycisk **Zastosuj > Ok** Aby zapisać wybrane opcje.

Na przykład na poniższej ilustracji przedstawiono, dodając wszystkie zadania sbt do jednej konfiguracji:

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Kliknij przycisk **Uruchom** Aby skompilować i uruchomić usługi sieci web na komputerze lokalnym.

Każdą usługę sieci web zostanie otwarte okno przeglądarki sieci web i wiersza polecenia. W wierszu polecenia zostaną wyświetlone dane wyjściowe z uruchomioną usługę i okna przeglądarki umożliwia monitorowanie stanu. Nie zamykaj wiersz polecenia lub strony sieci web, ta akcja zatrzymuje usługę sieci web.


Aby uzyskać dostęp do stanu usługi, możesz przejść do następujących adresów URL:
* Menedżer usług IoT Hub [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Danych Telemetrycznych z urządzenia  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adapter magazynu [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie usługi Stream Analytics:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Przejdź do **grupy zasobów** tworzony dla rozwiązania. Nazwa grupy zasobów jest nazwa została wybrana opcja rozwiązania jest wyświetlany po uruchomieniu **start.cmd** skryptu.
1. Kliknij przycisk **zadania usługi Stream Analytics** na liście zasobów.
1. W zadaniu Stream Analytics **Przegląd** kliknij **Start** przycisku. Następnie kliknij przycisk **Start** można teraz uruchomić zadania.

### <a name="run-the-web-ui"></a>Uruchom interfejs użytkownika sieci web

W tym kroku należy uruchomić interfejs użytkownika sieci web. Otwórz nowe okno wiersza polecenia, należy upewnić się, że masz dostęp do zmiennych środowiskowych, ustawiane przez **start.cmd** skryptu. Przejdź do **interfejsem sieci Web** folderu w lokalnym kopię repozytorium i uruchom następujące polecenia:

```cmd
npm install
npm start
```

Po zakończeniu początkowego przeglądarki zostanie wyświetlona strona **http:\//localhost:3000 / pulpit nawigacyjny**. Oczekiwane są błędy na tej stronie. Aby wyświetlić aplikację bez błędów, wykonaj poniższe czynności.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie serwera NGINX

Konfigurowanie zwrotnego serwera proxy do łączenia aplikacji sieci web i mikrousług uruchomiona na komputerze lokalnym:

* Kopiuj **nginx.conf** plik wchodzącej w skład **webui\scripts\localhost** folder w lokalnej kopii repozytorium na potrzeby **nginx\conf** katalog_instalacji.
* Uruchom **nginx**.

Aby uzyskać więcej informacji o uruchamianiu **nginx**, zobacz [nginx dla Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Łączenie do pulpitu nawigacyjnego

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania monitorowania zdalnego, przejdź do protokołu http:\//localhost:9000 w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania Usuń usług w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [witryny Azure portal](https://ms.portal.azure.com) i Usuń zasób grupy, która **start.cmd** skrypt utworzony.

Możesz także usunąć lokalną kopię repozytorium zdalne monitorowanie utworzone podczas klonowania kodu źródłowego z repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki

Teraz, że udało Ci się wdrożyć rozwiązania do zdalnego monitorowania, następnym krokiem jest [zapoznaj się z możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).
