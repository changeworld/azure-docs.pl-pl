---
title: Wdrażanie rozwiązania do monitorowania zdalnego lokalnie — Azure | Dokumentacja firmy Microsoft
description: Ten poradnik pokazuje, jak wdrożyć zdalnego monitorowania akcelerator rozwiązań do maszyny lokalnej na potrzeby projektowania i testowania.
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/17/2018
ms.topic: conceptual
ms.openlocfilehash: 5853730a5e3408e33deb483f6ce6652c1c22efab
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034981"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>Wdrażanie zdalne monitorowanie akceleratora rozwiązań lokalnie

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań monitorowania zdalnego na komputerze lokalnym na potrzeby projektowania i testowania. To podejście mikrousługi są wdrażane na lokalny kontener platformy Docker i korzysta z usługi IoT Hub, Cosmos DB i Azure Time Series Insights w chmurze.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązania monitorowania zdalnego, należy aktywną subskrypcją platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](http://azure.microsoft.com/pricing/free-trial/).

Aby wykonać wdrożenie lokalne, potrzebne są następujące narzędzia, które są zainstalowane na lokalnej maszynie do programowania:

* [Usługa Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Narzędzia docker compose](https://docs.docker.com/compose/install/)
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest wymagane w przypadku komputerów z systemem interfejsu wiersza polecenia, które skrypty umożliwia tworzenie zasobów platformy Azure. Nie należy używać Node.js v10.

> [!NOTE]
> Te narzędzia są dostępne na wielu platformach, w tym Windows i Linux oraz dla systemu iOS.

### <a name="download-the-source-code"></a>Pobierz kod źródłowy

Repozytorium GitHub zdalne monitorowanie kodu źródłowego zawiera pliki konfiguracji platformy Docker, których należy pobrać, konfigurowanie i uruchamianie obrazów platformy Docker, które zawierają mikrousług. Aby sklonować i utworzyć lokalną wersję repozytorium, użyj środowiska wiersza polecenia, aby przejść do odpowiedniego folderu na komputerze lokalnym, a następnie uruchom jedno z następujących poleceń:

Aby pobrać najnowszą wersję implementacji mikrousług Java, uruchom polecenie:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

Aby pobrać najnowszą wersję implementacji mikrousługi platformy .NET, uruchom polecenie:

```cmd\sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Te polecenia Pobierz kod źródłowy wszystkie mikrousługi oprócz skryptów, których można używać do uruchamiania mikrousługi lokalnie. Mimo że nie ma potrzeby kodu źródłowego, aby uruchomić mikrousług na platformie Docker, kod źródłowy jest przydatne, jeśli planujesz później zmodyfikować akcelerator rozwiązań i lokalne testowanie zmian.

## <a name="deploy-the-azure-services"></a>Wdrażaj usługi platformy Azure

Mimo że w tym artykule pokazano, jak uruchomić mikrousługi lokalnie, są one zależne od usług platformy Azure działające w chmurze. Można wdrożyć te usługi platformy Azure [ręcznie za pomocą witryny Azure portal](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), lub użyć dostarczonego skryptu. W poniższych przykładach skryptów przyjęto założenie, że używasz repozytorium .NET na maszynie Windows. Jeśli pracujesz w innym środowisku, Dostosuj ścieżek, rozszerzeń plików i separatorami ścieżki odpowiednio. Aby użyć dostarczone skrypty do:

### <a name="create-new-azure-resources"></a>Tworzenie nowych zasobów platformy Azure

Jeśli jeszcze nie utworzono wymaganych zasobów platformy Azure, wykonaj następujące kroki:

1. W środowisku wiersza polecenia i przejdź do **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch** folderu w sklonowanej kopii repozytorium.

2. Uruchom **start.cmd** skryptu i postępuj zgodnie z monitami. Skrypt wyświetli monit o Zaloguj się do konta platformy Azure i ponownie uruchom skrypt. Skrypt następnie wyświetli monit o podanie następujących informacji:
    * Nazwa rozwiązania.
    * Subskrypcja platformy Azure, która ma być używana.
    * Lokalizacja centrum danych platformy Azure do użycia.

    Skrypt tworzy grupę zasobów na platformie Azure, nazwą rozwiązania. Ta grupa zasobów zawiera zasoby platformy Azure, który korzysta z akceleratora rozwiązań.

3. Po ukończeniu działania skryptu, wyświetla listę zmiennych środowiskowych. Postępuj zgodnie z instrukcjami, aby zapisać te zmienne **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\.env** pliku.

### <a name="use-existing-azure-resources"></a>Użyj istniejących zasobów platformy Azure

Jeśli utworzono już wymaganych zasobów platformy Azure edytować definicje zmiennych środowiskowych w **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\.env** pliku z wymaganymi wartościami. **ENV** plik zawiera szczegółowe informacje o tym, gdzie można znaleźć wymaganych wartości.

## <a name="run-the-microservices-in-docker"></a>Uruchom mikrousług na platformie Docker

Aby uruchomić akcelerator rozwiązań, przejdź do **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local** folder w środowisku wiersza polecenia i uruchom następujące polecenie:

```cmd\sh
docker-compose up
```

Podczas pierwszego uruchamiania tego polecenia Docker pobiera obrazy mikrousług z usługi Docker hub do tworzenia kontenerów lokalnie. W kolejnych uruchomień Docker uruchamia kontenery natychmiast.

Aby wyświetlić dzienniki z kontenera, można użyć oddzielnych powłoki. Najpierw Znajdź przy użyciu Identyfikatora kontenera `docker ps -a` polecenia. Następnie użyj `docker logs {container-id} --tail 1000` Aby wyświetlić ostatnie wpisy dziennika 1000 dla określonego kontenera.

Aby uzyskać dostęp do pulpitu nawigacyjnego rozwiązania monitorowania zdalnego, przejdź do [ http://localhost:8080 ](http://localhost:8080) w przeglądarce.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania, należy usunąć usługi w chmurze z subskrypcji platformy Azure. Jest najprostszym sposobem usuwania usług, aby przejść do [witryny Azure portal](https://ms.portal.azure.com) i Usuń grupę zasobów, który został utworzony po uruchomieniu **start.cmd** skryptu.

Użyj `docker-compose down --rmi all` polecenie, aby usunąć obrazy platformy Docker i wolnego miejsca na komputerze lokalnym. Możesz także usunąć lokalną kopię repozytorium zdalne monitorowanie utworzone podczas klonowania kodu źródłowego z repozytorium GitHub.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Skonfiguruj lokalne Środowisko deweloperskie
> * Konfigurowanie akceleratora rozwiązań
> * Wdrażanie akceleratora rozwiązań
> * Zaloguj się do akceleratora rozwiązań

Teraz, gdy wdrożono rozwiązanie monitorowania zdalnego, następnym krokiem jest [zapoznaj się z możliwościami pulpitu nawigacyjnego rozwiązania](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
