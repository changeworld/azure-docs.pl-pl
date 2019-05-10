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
ms.openlocfilehash: 996111fbe23000182dab774ba3bbad0cc6435824
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65412729"
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

[!INCLUDE [iot-accelerators-local-setup-java](../../includes/iot-accelerators-local-setup-java.md)]

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
