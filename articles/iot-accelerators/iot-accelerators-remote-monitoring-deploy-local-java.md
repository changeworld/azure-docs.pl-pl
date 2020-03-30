---
title: Wdrażanie rozwiązania do zdalnego monitorowania lokalnie — IntelliJ IDE — Azure | Dokumenty firmy Microsoft
description: W tym przewodniku przedstawiono sposób wdrażania akceleratora rozwiązań zdalnego monitorowania na komputerze lokalnym przy użyciu technologii IntelliJ do testowania i tworzenia.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888807"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>Wdrażanie akceleratora rozwiązań do zdalnego monitorowania lokalnie — IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań zdalnego monitorowania na komputerze lokalnym w celu testowania i tworzenia. Dowiesz się, jak uruchomić mikrousług w IntelliJ. Wdrożenie mikrousług lokalnych będzie korzystać z następujących usług w chmurze: Usługa IoT Hub, usługa Azure Cosmos DB, usługa Azure Streaming Analytics i usługa Azure Time Series Insights.

Jeśli chcesz uruchomić akcelerator rozwiązań do monitorowania zdalnego w programie Docker na komputerze lokalnym, zobacz [Lokalne wdrażanie akceleratora rozwiązań do monitorowania zdalnego — Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązań zdalnego monitorowania, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja maszyny

Aby ukończyć wdrożenie lokalne, na lokalnym komputerze deweloperskim są zainstalowane następujące narzędzia:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Wersja społecznościowa](https://www.jetbrains.com/idea/download/)
* [Wtyczka IntelliJ Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [Wtyczka IntelliJ SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Wtyczka IntelliJ SBT Executor](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Plik Node.js w wersji 8](https://nodejs.org/)

Node.js v8 jest warunkiem wstępnym interfejsu wiersza polecenia komputera PCS, który skrypty używają do tworzenia zasobów platformy Azure. Nie używaj pliku Node.js w wersji 10.

> [!NOTE]
> IntelliJ IDE jest dostępny dla systemów Windows i Mac.

## <a name="download-the-source-code"></a>Pobierz kod źródłowy

Repozytoria kodu źródłowego zdalnego monitorowania zawierają kod źródłowy i pliki konfiguracyjne platformy Docker, które są potrzebne do uruchamiania obrazów platformy Docker mikrousług.

Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia, aby przejść do odpowiedniego folderu na komputerze lokalnym. Następnie uruchom jeden z następujących zestawów poleceń, aby sklonować repozytorium Java:

* Aby pobrać najnowszą wersję implementacji mikrousług java, uruchom następujące polecenie:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* Aby pobrać najnowsze podmodule, uruchom następujące polecenia:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Te polecenia pobrać kod źródłowy dla wszystkich mikrousług oprócz skryptów używanych do uruchamiania mikrousług lokalnie. Nie potrzebujesz kodu źródłowego do uruchamiania mikrousług w docker. Ale kod źródłowy jest przydatne, jeśli później planujesz zmodyfikować akcelerator rozwiązań i przetestować zmiany lokalnie.

## <a name="deploy-the-azure-services"></a>Wdrażanie usług platformy Azure

Chociaż w tym artykule pokazano, jak uruchomić mikrousług lokalnie, zależą one od usług platformy Azure uruchomionych w chmurze. Użyj następującego skryptu, aby wdrożyć usługi platformy Azure. Przykłady skryptu zakładają, że używasz repozytorium Java na komputerze z systemem Windows. Jeśli pracujesz w innym środowisku, dostosuj odpowiednio ścieżki, rozszerzenia plików i separatory ścieżek.

### <a name="create-new-azure-resources"></a>Tworzenie nowych zasobów platformy Azure

Jeśli nie utworzono jeszcze wymaganych zasobów platformy Azure, wykonaj następujące kroki:

1. W środowisku wiersza polecenia przejdź do folderu **\services\scripts\local\launch** w sklonowanej kopii repozytorium.

1. Uruchom następujące polecenia, aby zainstalować narzędzie **interfejsu wiersza** polecenia pcs i zalogować się na swoje konto platformy Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Uruchom skrypt **start.cmd.** Skrypt monituje o następujące informacje:

   * Nazwa rozwiązania.
   * Subskrypcja platformy Azure, która ma być używana.
   * Lokalizacja centrum danych platformy Azure do użycia.

   Skrypt tworzy grupę zasobów na platformie Azure, która ma nazwę rozwiązania. Ta grupa zasobów zawiera zasoby platformy Azure używane przez akcelerator rozwiązań. Tę grupę zasobów można usunąć po tym, jak nie są już potrzebne odpowiednie zasoby.

   Skrypt dodaje również zestaw zmiennych środowiskowych do komputera lokalnego. Każda nazwa zmiennej ma prefiks **PCS**. Te zmienne środowiskowe zawierają szczegółowe informacje, które umożliwiają zdalnemu monitorowaniu odczytywanie jego wartości konfiguracji z zasobu usługi Azure Key Vault.

   > [!TIP]
   > Po zakończeniu skryptu zapisuje zmienne środowiskowe w pliku o nazwie ** \<\>\\folder macierzysty .pcs\\\<nazwa\>rozwiązania .env**. Można ich używać do przyszłych wdrożeń akceleratora rozwiązań. Należy zauważyć, że wszystkie zmienne środowiskowe ustawione na komputerze lokalnym zastępują wartości w **skryptach\\\\usług\\lokalnego** pliku env po uruchomieniu **docker-compose**.

1. Zamknij środowisko wiersza polecenia.

### <a name="use-existing-azure-resources"></a>Korzystanie z istniejących zasobów platformy Azure

Jeśli utworzono już wymagane zasoby platformy Azure, ustaw odpowiednie zmienne środowiskowe na komputerze lokalnym:
* **PCS_KEYVAULT_NAME**: Nazwa zasobu Magazynu kluczy.
* **PCS_AAD_APPID:** Identyfikator aplikacji usługi Azure Active Directory (Azure AD).
* **PCS_AAD_APPSECRET:** klucz tajny aplikacji usługi Azure AD.

Wartości konfiguracji będą odczytywane z tego zasobu usługi Key Vault. Te zmienne środowiskowe można zapisać w ** \<pliku\\\<\>.env\>\\folderu macierzystego** z wdrożenia. Należy zauważyć, że zmienne środowiskowe ustawione na komputerze lokalnym zastępują wartości w **skryptach\\\\usług\\lokalnego** pliku env po uruchomieniu **docker-compose**.

Niektóre konfiguracje wymagane przez mikrousługi są przechowywane w wystąpieniu usługi Key Vault, który został utworzony podczas początkowego wdrażania. Odpowiednie zmienne w magazynie kluczy powinny być modyfikowane w razie potrzeby.

## <a name="run-the-microservices"></a>Uruchamianie mikrousług

W tej sekcji należy uruchomić mikrousług monitorowania zdalnego. Uruchom:

* Interfejs użytkownika sieci Web natywnie.
* Usługi Azure IoT Device Simulation, Auth i Azure Stream Analytics Manager w platformie Docker.
* Mikrousługi w IntelliJ.

### <a name="run-the-device-simulation-service"></a>Uruchamianie usługi symulacji urządzeń

Otwórz nowe okno wiersza polecenia. Sprawdź, czy masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **start.cmd** w poprzedniej sekcji.

Uruchom następujące polecenie, aby otworzyć kontener platformy Docker dla usługi Symulacja urządzeń. Usługa symuluje urządzenia dla rozwiązania zdalnego monitorowania.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Uruchamianie usługi Auth

Otwórz nowe okno wiersza polecenia, a następnie uruchom następujące polecenie, aby otworzyć kontener platformy Docker dla usługi Eru. Korzystając z tej usługi, można zarządzać użytkownikami, którzy są autoryzowani do uzyskiwania dostępu do rozwiązań Azure IoT.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>Uruchamianie usługi Menedżer analizy strumienia

Otwórz nowe okno wiersza polecenia, a następnie uruchom następujące polecenie, aby otworzyć kontener platformy Docker dla usługi Menedżer usługi Usługi Stream Analytics. Dzięki tej usłudze możesz zarządzać zadaniami usługi Stream Analytics. Takie zarządzanie obejmuje ustawianie konfiguracji zadania oraz uruchamianie, zatrzymywanie i monitorowanie stanu zadania.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Wdrażanie wszystkich innych mikrousług na komputerze lokalnym

Poniższe kroki pokazują, jak uruchomić mikrousług monitorowania zdalnego w IntelliJ.

#### <a name="import-a-project"></a>Importowanie projektu

1. Otwórz ide IntelliJ.
1. Wybierz **pozycję Importuj projekt**.
1. Wybierz **azure-iot-pcs-remote-monitoring-java\services\build.sbt**.

#### <a name="create-run-configurations"></a>Tworzenie konfiguracji uruchamiania

1. Wybierz **pozycję Uruchom** > **konfiguracje edycji**.
1. Wybierz **pozycję Dodaj nowe** > zadanie**konfiguracji .**
1. Wprowadź **nazwę**, a następnie wprowadź **zadania** jako **uruchomione**.
1. Wybierz **katalog roboczy** na podstawie usługi, którą chcesz uruchomić.
1. Wybierz **pozycję Zastosuj** > **OK,** aby zapisać swoje wybory.
1. Tworzenie konfiguracji uruchamiania dla następujących usług sieci web:
    * WebService (usługi\config)
    * WebService (usługi\telemetria urządzenia)
    * WebService (usługi\iothub-manager)
    * WebService (usługi\pamięć masowa-karta)

Na przykład na poniższej ilustracji pokazano, jak dodać konfigurację usługi:

[![Zrzut ekranu przedstawiający okno IntelliJ IDE Run/Debug Configurations, przedstawiające wyróżnioną opcję storageAdapter na liście zadań sbt w lewym okienku oraz wpisy w polach Nazwa, Zadania, Katalog roboczy i Parametry maszyny Wirtualnej w prawym okienku.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Tworzenie konfiguracji złożonej

1. Aby uruchomić wszystkie usługi razem, wybierz **dodaj nowy związek konfiguracji** > **Compound**.
1. Wprowadź **nazwę**, a następnie wybierz **dodaj zadania sbt**.
1. Wybierz **pozycję Zastosuj** > **OK,** aby zapisać swoje wybory.

Na przykład na poniższej ilustracji pokazano, jak dodać wszystkie zadania sbt do jednej konfiguracji:

[![Zrzut ekranu przedstawiający okno IntelliJ IDE Run/Debug Configurations, przedstawiające wyróżnioną opcję AllServices na liście Złożone w lewym okienku oraz wyróżnioną w prawym okienku opcję "deviceTelemetry" zadania sbt.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Wybierz **uruchom,** aby skompilować i uruchomić usługi sieci web na komputerze lokalnym.

Każda usługa sieci Web otwiera okno wiersza polecenia i okno przeglądarki sieci Web. W wierszu polecenia zostanie wyświetlenia danych wyjściowych z uruchomionej usługi. Okno przeglądarki umożliwia monitorowanie stanu. Nie zamykaj okien wiersza polecenia ani stron sieci Web, ponieważ te akcje zatrzymują usługę sieci web.

Aby uzyskać dostęp do stanu usług, przejdź do następujących adresów URL:

* Menedżer centrum IoT:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Telemetria urządzenia:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Config:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* adapter pamięci masowej:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie usługi Stream Analytics:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do **grupy Zasobów** utworzonej dla rozwiązania. Nazwa grupy zasobów to nazwa wybrana dla rozwiązania podczas uruchamiania skryptu **start.cmd.**
1. Wybierz **zadanie usługi Stream Analytics** na liście zasobów.
1. Na stronie **Omówienie** zadania usługi Stream Analytics wybierz przycisk **Start,** a następnie wybierz pozycję **Start,** aby rozpocząć zadanie.

### <a name="run-the-web-ui"></a>Uruchamianie interfejsu użytkownika sieci Web

W tym kroku należy uruchomić interfejs użytkownika sieci Web. Otwórz nowe okno wiersza polecenia. Sprawdź, czy masz dostęp do zmiennych środowiskowych ustawionych przez skrypt **start.cmd.** Przejdź do **folderu webui** w lokalnej kopii repozytorium, a następnie uruchom następujące polecenia:

```cmd
npm install
npm start
```

Po zakończeniu polecenia **start** przeglądarka wyświetli stronę [http://localhost:3000/dashboard](http://localhost:3000/dashboard)pod adresem . Błędy na tej stronie są oczekiwane. Aby wyświetlić aplikację bez błędów, wykonaj następujące kroki.

### <a name="configure-and-run-nginx"></a>Konfigurowanie i uruchamianie nginx

Skonfiguruj serwer odwrotnego serwera proxy, który łączy aplikację sieci web z mikrousługami uruchomionymi na komputerze lokalnym:

1. Skopiuj plik **nginx.conf** z **folderu webui\scripts\localhost** w lokalnej kopii repozytorium do katalogu instalacji **nginx\conf.**
1. Uruchom Nginx.

Aby uzyskać więcej informacji na temat uruchamiania nginx, zobacz [nginx dla systemu Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Łączenie się z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu http://localhost:9000 nawigacyjnego rozwiązania do zdalnego monitorowania, przejdź do przeglądarki.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, usuń usługi w chmurze z subskrypcji platformy Azure po zakończeniu testowania. Aby usunąć usługi, przejdź do [witryny Azure portal](https://ms.portal.azure.com)i usuń grupę zasobów utworzoną przez skrypt **start.cmd.**

Można również usunąć lokalną kopię repozytorium zdalnego monitorowania, który został utworzony podczas klonowania kodu źródłowego z gitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do zdalnego monitorowania, następnym krokiem jest [zbadanie możliwości pulpitu nawigacyjnego rozwiązania.](quickstart-remote-monitoring-deploy.md)
