---
title: Wdrażanie rozwiązania do zdalnego monitorowania lokalnie — platforma Docker — Azure | Dokumenty firmy Microsoft
description: W tym przewodniku przedstawiono sposób wdrażania akceleratora rozwiązań zdalnego monitorowania na komputerze lokalnym przy użyciu platformy Docker do testowania i tworzenia.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 32b47d9d6d45ff471961f55f8159dbe85eae2dce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888835"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>Lokalne wdrażanie akceleratora rozwiązań do zdalnego monitorowania — docker

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

W tym artykule pokazano, jak wdrożyć akcelerator rozwiązań zdalnego monitorowania na komputerze lokalnym w celu testowania i tworzenia. Dowiesz się, jak wdrożyć mikrousługi do lokalnych kontenerów platformy Docker. Wdrożenie mikrousług lokalnych korzysta z następujących usług w chmurze: Usługi IoT Hub, Usługa Cosmos DB, usługa Azure Streaming Analytics i usługi Azure Time Series Insights w chmurze.

Jeśli chcesz uruchomić akcelerator rozwiązań do zdalnego monitorowania w ide na komputerze lokalnym, zobacz [Wdrażanie akceleratora rozwiązań zdalnego monitorowania lokalnie — Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wdrożyć usługi platformy Azure używane przez akcelerator rozwiązań zdalnego monitorowania, potrzebujesz aktywnej subskrypcji platformy Azure.

Jeśli jej nie masz, możesz utworzyć bezpłatne konto próbne w zaledwie kilka minut. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Konfiguracja maszyny

Aby ukończyć wdrożenie lokalne, na lokalnym komputerze deweloperskim są zainstalowane następujące narzędzia:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Visual Studio](https://visualstudio.microsoft.com/) — jeśli planujesz wprowadzić zmiany w mikrousługach.
* [Node.js v8](https://nodejs.org/) — to oprogramowanie jest warunkiem wstępnym interfejsu wiersza polecenia komputera PCS używanego do tworzenia zasobów platformy Azure. Nie używaj pliku Node.js w wersji 10.

> [!NOTE]
> Narzędzia te są dostępne na wielu platformach, w tym Windows, Linux i iOS.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Uruchamianie mikrousług w cker

Otwórz nowy wiersz polecenia, aby mieć dostęp do zmiennych środowiskowych ustawionych przez skrypt **start.cmd.** W systemie Windows można sprawdzić, czy zmienne środowiskowe są ustawiane, uruchamiając następujące polecenie:

```cmd
set PCS
```

Polecenie pokazuje wszystkie zmienne środowiskowe ustawione przez skrypt **start.cmd.**

Upewnij się, że docker jest uruchomiony na komputerze lokalnym.
> [!NOTE]
> Docker musi być uruchomiony [kontenery systemu Linux,](https://docs.docker.com/docker-for-windows/) jeśli jest uruchomiony w systemie Windows.

Mikrousługi uruchomione w lokalnych kontenerach platformy Docker muszą uzyskiwać dostęp do usług w chmurze platformy Azure. Łączność z Internetem środowiska platformy Docker można przetestować za pomocą następującego polecenia, aby wykonać polecenie ping do adresu internetowego z wnętrza kontenera:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

Aby uruchomić akcelerator rozwiązań, przejdź do folderu **lokalnego skryptów\\\\usług** w środowisku wiersza polecenia i uruchom następujące polecenie:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Przed `docker-compose up` [uruchomieniem należy udostępnić dysk lokalny](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) programowi Docker.

Przy pierwszym uruchomieniu tego polecenia docker pobiera obrazy mikrousług z centrum platformy Docker do tworzenia kontenerów lokalnie. W następujących działach platformy Docker uruchamia kontenery natychmiast.

> [!TIP]
> Firma Microsoft często publikuje nowe obrazy platformy Docker z nowymi funkcjami. Aby wyczyścić lokalne kontenery platformy Docker i odpowiadające im obrazy, można użyć następującego zestawu poleceń, zanim wyciągniesz najnowsze:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Można użyć oddzielnej powłoki, aby wyświetlić dzienniki z kontenera. Najpierw znajdź identyfikator kontenera `docker ps` za pomocą polecenia. Następnie `docker logs {container-id} --tail 1000` użyj, aby wyświetlić ostatnie 1000 wpisów dla określonego kontenera.

### <a name="start-the-stream-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

Wykonaj następujące kroki, aby uruchomić zadanie usługi Stream Analytics:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Przejdź do **grupy zasobów** utworzonej dla rozwiązania. Nazwa grupy zasobów to nazwa wybrana dla rozwiązania podczas uruchamiania skryptu **start.cmd.**
1. Kliknij **zadanie usługi Stream Analytics** na liście zasobów.
1. Na stronie **Przegląd** zadań usługi Stream Analytics kliknij przycisk **Start.** Następnie kliknij przycisk **Start,** aby rozpocząć zadanie teraz.

### <a name="connect-to-the-dashboard"></a>Łączenie się z pulpitem nawigacyjnym

Aby uzyskać dostęp do pulpitu `http://localhost:8080` nawigacyjnego rozwiązania zdalnego monitorowania, przejdź do przeglądarki. Teraz można użyć interfejsu użytkownika sieci Web i mikrousług lokalnych.

## <a name="clean-up"></a>Czyszczenie

Aby uniknąć niepotrzebnych opłat, po zakończeniu testowania usunąć usługi w chmurze z subskrypcji platformy Azure. Aby usunąć usługi, przejdź do [witryny Azure Portal](https://ms.portal.azure.com) i usuń grupę zasobów utworzoną przez skrypt **start.cmd.**

Użyj `docker-compose down --rmi all` polecenia, aby usunąć obrazy platformy Docker i zwolnić miejsce na komputerze lokalnym. Można również usunąć lokalną kopię repozytorium zdalnego monitorowania utworzonego podczas klonowania kodu źródłowego z usługi GitHub.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wdrożono rozwiązanie do zdalnego monitorowania, następnym krokiem jest [zbadanie możliwości pulpitu nawigacyjnego rozwiązania.](quickstart-remote-monitoring-deploy.md)
