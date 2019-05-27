---
title: Wdrażanie rozwiązania do monitorowania zdalnego z lokalnie (przy użyciu programu Visual Studio IDE) — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak wdrożyć zdalnego akcelerator rozwiązań do monitorowania na komputerze lokalnym przy użyciu programu Visual Studio na potrzeby projektowania i testowania.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 1adf59feca7db4c5903b04c59e1bd23290c1855e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967510"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie — Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań monitorowania zdalnego na komputerze lokalnym na potrzeby projektowania i testowania. Dowiesz się, jak uruchomić mikrousługi w programie Visual Studio. Wdrożenie lokalne mikrousług używa następujących usług w chmurze: Usługi IoT Hub, Cosmos DB, usługi Azure Stream Analytics i Azure Time Series Insights w chmurze.

Jeśli chcesz uruchomić akcelerator rozwiązania monitorowania zdalnego na platformie Docker na komputerze lokalnym, zobacz [wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie — Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązania monitorowania zdalnego, należy aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Ustawienia komputera

Aby wykonać wdrożenie lokalne, potrzebne są następujące narzędzia, które są zainstalowane na lokalnej maszynie do programowania:

* [Usługa Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Program Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest wymagane w przypadku komputerów z systemem interfejsu wiersza polecenia, które skrypty umożliwia tworzenie zasobów platformy Azure. Nie używaj Node.js v10.

> [!NOTE]
> Program Visual Studio jest dostępna dla Windows i Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji uruchamiasz mikrousług monitorowania zdalnego. Działania interfejsu użytkownika sieci web, usługi symulacji urządzenia na platformie Docker i mikrousług w programie Visual Studio.

### <a name="run-the-device-simulation-service"></a>Uruchom usługę symulacji urządzenia

Otwórz nowe okno wiersza polecenia, należy upewnić się, że masz dostęp do zmiennych środowiskowych, ustawiane przez **start.cmd** skryptu w poprzedniej sekcji.

Uruchom następujące polecenie, aby uruchomić kontener platformy Docker dla usługi symulacji urządzenia. Usługa symuluje urządzeń dla rozwiązania do monitorowania zdalnego.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Wdrażaj wszystkie mikrousługi na komputerze lokalnym

Poniższe kroki pokazują, jak uruchomić mikrousług zdalnego monitorowania w programie Visual Studio:

1. Uruchom program Visual Studio.
1. Otwórz **monitoring.sln zdalnego** rozwiązania **usług** folderu w lokalnej kopii repozytorium.
1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy rozwiązanie, a następnie kliknij **właściwości**.
1. Wybierz **typowe właściwości > Projekt startowy**.
1. Wybierz **wiele projektów startowych** i ustaw **akcji** do **Start** dla następujących projektów:
    * Usługa sieci Web (asa manager\WebService)
    * Usługa sieci Web (auth\WebService)
    * Usługa sieci Web (config\WebService)
    * Usługa sieci Web (urządzenie telemetry\WebService)
    * Usługa sieci Web (iothub-manager\WebService)
    * Usługa sieci Web (magazynu adapter\WebService)
1. Kliknij przycisk **OK** Aby zapisać wybrane opcje.
1. Kliknij przycisk **Debuguj > Rozpocznij debugowanie** Aby skompilować i uruchomić usługi sieci web na komputerze lokalnym.

Każdą usługę sieci web zostanie otwarte okno przeglądarki sieci web i wiersza polecenia. W wierszu polecenia zostaną wyświetlone dane wyjściowe z uruchomioną usługę i okna przeglądarki umożliwia monitorowanie stanu. Nie zamykaj wiersz polecenia lub strony sieci web, ta akcja zatrzymuje usługę sieci web.

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
