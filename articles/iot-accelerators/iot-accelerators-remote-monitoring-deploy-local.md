---
title: Wdrażanie rozwiązania do zdalnego monitorowania lokalnie — VS IDE — Azure | Dokumenty firmy Microsoft
description: W tym przewodniku przedstawiono sposób wdrażania akceleratora rozwiązania zdalnego monitorowania na komputerze lokalnym przy użyciu programu Visual Studio do testowania i tworzenia.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: a1eba1fceb959bd475d205176c2c53f6409fdc77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890887"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio"></a>Wdrażanie akceleratora rozwiązań do zdalnego monitorowania lokalnie — Visual Studio

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań zdalnego monitorowania na komputerze lokalnym w celu testowania i tworzenia. Dowiesz się, jak uruchomić mikrousługi w programie Visual Studio. Wdrożenie mikrousług lokalnych korzysta z następujących usług w chmurze: Usługi IoT Hub, Usługa Cosmos DB, usługa Azure Streaming Analytics i usługi Azure Time Series Insights w chmurze.

Jeśli chcesz uruchomić akcelerator rozwiązań do monitorowania zdalnego w programie Docker na komputerze lokalnym, zobacz [Lokalne wdrażanie akceleratora rozwiązań do monitorowania zdalnego — Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązań zdalnego monitorowania, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja maszyny

Aby ukończyć wdrożenie lokalne, na lokalnym komputerze deweloperskim są zainstalowane następujące narzędzia:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest warunkiem wstępnym interfejsu wiersza polecenia komputera PCS używanego do tworzenia zasobów platformy Azure. Nie używaj pliku Node.js w wersji 10.

> [!NOTE]
> Program Visual Studio jest dostępny dla systemów Windows i Mac.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji należy uruchomić mikrousług monitorowania zdalnego. Natywnie uruchamiasz internetowy interfejs użytkownika, usługę symulacji urządzeń w programie Docker i mikrousług w programie Visual Studio.

### <a name="run-the-device-simulation-service"></a>Uruchamianie usługi symulacji urządzeń

Otwórz nowe okno wiersza polecenia, aby mieć pewność, że masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **start.cmd** w poprzedniej sekcji.

Uruchom następujące polecenie, aby uruchomić kontener platformy Docker dla usługi symulacji urządzeń. Usługa symuluje urządzenia dla rozwiązania do zdalnego monitorowania.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Wdrażanie wszystkich innych mikrousług na komputerze lokalnym

W poniższych krokach pokazano, jak uruchomić mikrousług monitorowania zdalnego w programie Visual Studio:

1. Uruchom program Visual Studio.
1. Otwórz rozwiązanie **remote-monitoring.sln** w folderze **usług** w lokalnej kopii repozytorium.
1. W **Eksploratorze rozwiązań**kliknij prawym przyciskiem myszy rozwiązanie i kliknij polecenie **Właściwości**.
1. Wybierz **opcję Wspólne właściwości > projektu uruchamiania**.
1. Wybierz **pozycję Wiele projektów startowych** i ustaw opcję **Akcja** na **Początek** dla następujących projektów:
    * WebService (asa-manager\WebService)
    * Usługa webservice (auth\WebService)
    * WebService (config\WebService)
    * WebService (device-telemetry\WebService)
    * Usługa WebService (iothub-manager\WebService)
    * WebService (adapter magazynu\WebService)
1. Kliknij **przycisk OK,** aby zapisać swoje wybory.
1. Kliknij **przycisk Debugowanie > Rozpocznij debugowanie,** aby skompilować i uruchomić usługi sieci web na komputerze lokalnym.

Każda usługa sieci web otwiera wiersz polecenia i okno przeglądarki internetowej. W wierszu polecenia zostanie wyświetlenia danych wyjściowych z uruchomionej usługi, a okno przeglądarki umożliwia monitorowanie stanu. Nie zamykaj wierszy polecenia ani stron internetowych, ta akcja zatrzymuje usługę sieci web.

### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie usługi Stream Analytics:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do **grupy zasobów** utworzonej dla rozwiązania. Nazwa grupy zasobów to nazwa wybrana dla rozwiązania podczas uruchamiania skryptu **start.cmd.**
1. Kliknij **zadanie usługi Stream Analytics** na liście zasobów.
1. Na stronie **Przegląd** zadań usługi Stream Analytics kliknij przycisk **Start.** Następnie kliknij przycisk **Start,** aby rozpocząć zadanie teraz.

### <a name="run-the-web-ui"></a>Uruchamianie interfejsu użytkownika sieci Web

W tym kroku należy uruchomić interfejs użytkownika sieci Web. Otwórz nowe okno wiersza polecenia, aby mieć pewność, że masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **start.cmd.** Przejdź do **folderu webui** w lokalnej kopii repozytorium i uruchom następujące polecenia:

```cmd
npm install
npm start
```

Po zakończeniu uruchamiania przeglądarka wyświetla stronę **\/http: /localhost:3000/dashboard**. Błędy na tej stronie są oczekiwane. Aby wyświetlić aplikację bez błędów, wykonaj następujący krok.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie NGINX

Skonfiguruj odwrotny serwer proxy, aby połączyć aplikację sieci web i mikrousług uruchomionych na komputerze lokalnym:

* Skopiuj plik **nginx.conf** z **folderu webui\scripts\localhost** w lokalnej kopii repozytorium do katalogu **nginx\conf** install.
* Uruchom **nginx**.

Aby uzyskać więcej informacji na temat uruchamiania **nginx**, zobacz [nginx dla systemu Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Łączenie się z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania zdalnego monitorowania, przejdź do http:\//localhost:9000 w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania usunąć usługi w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [witryny Azure Portal](https://ms.portal.azure.com) i usuń grupę zasobów utworzoną przez skrypt **start.cmd.**

Można również usunąć lokalną kopię repozytorium zdalnego monitorowania utworzonego podczas klonowania kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do zdalnego monitorowania, następnym krokiem jest [zbadanie możliwości pulpitu nawigacyjnego rozwiązania.](quickstart-remote-monitoring-deploy.md)
