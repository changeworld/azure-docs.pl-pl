---
title: Wdrażanie niestandardowego obrazu symulacji urządzenia — Azure| Dokumenty firmy Microsoft
description: W tym przewodniku instrukcja, dowiesz się, jak wdrożyć niestandardowy obraz platformy Docker rozwiązania symulacji urządzeń na platformie Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448417"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Wdrażanie niestandardowego obrazu narzędzia dokującego Symulacja urządzenia

Można zmodyfikować rozwiązanie symulacji urządzenia, aby dodać funkcje niestandardowe. Na przykład [serializować dane telemetryczne przy użyciu buforów protokołu](iot-accelerators-device-simulation-protobuf.md) artykuł pokazuje, jak dodać niestandardowe urządzenie do rozwiązania, które używa buforów protokołu (Protobuf) do wysyłania danych telemetrycznych. Po przetestowaniu zmian lokalnie następnym krokiem jest wdrożenie zmian w wystąpieniu symulacji urządzenia na platformie Azure. Aby wykonać to zadanie, należy utworzyć i wdrożyć obraz platformy Docker, który zawiera zmodyfikowaną usługę.

Kroki opisane w tym przewodniku pokazują, jak:

1. Przygotowanie środowiska programistycznego
1. Generowanie nowego obrazu platformy Docker
1. Konfigurowanie symulacji urządzenia do używania nowego obrazu platformy Docker
1. Uruchamianie symulacji przy użyciu nowego obrazu

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, należy:

* Wdrożone [wystąpienie symulacji urządzenia.](quickstart-device-simulation-deploy.md)
* Oknie dokowanym. Pobierz wersję [Społeczności Platformy Docker](https://www.docker.com/products/docker-engine#/download) dla swojej platformy.
* [Konto Docker Hub,](https://hub.docker.com/) na którym można przesyłać obrazy platformy Docker. Na koncie Usługi Docker Hub utwórz publiczne repozytorium o nazwie **device-simulation**.
* Zmodyfikowane i przetestowane [rozwiązanie device simulation](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) na komputerze lokalnym. Na przykład można zmodyfikować rozwiązanie do [serializacji danych telemetrycznych przy użyciu buforów protokołu](iot-accelerators-device-simulation-protobuf.md).
* Powłoka, która może uruchomić SSH. Jeśli zainstalujesz Git Dla Windows, możesz użyć powłoki **bash,** która jest częścią instalacji th. Można również użyć usługi [Azure Cloud Shell](https://shell.azure.com/).

W instrukcjach w tym artykule założono, że używasz systemu Windows. Jeśli używasz innego systemu operacyjnego, może być konieczne dostosowanie niektórych ścieżek plików i poleceń do środowiska.

## <a name="create-a-new-docker-image"></a>Tworzenie nowego obrazu platformy Docker

Aby wdrożyć własne zmiany w usłudze Symulacja urządzeń, należy edytować skrypty kompilacji i wdrażania w **folderze scripts\docker,** aby przekazać kontenery do konta centrum docker

### <a name="modify-the-docker-scripts"></a>Modyfikowanie skryptów docker

Zmodyfikuj **plik Docker build.cmd**, **publish.cmd**i uruchom skrypty **cmd** w folderze **scripts\docker** za pomocą informacji o repozytorium Usługi Docker Hub. W tych krokach założono utworzenie publicznego repozytorium o nazwie **device-simulation:**

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Zaktualizuj plik **docker-compose.yml** w następujący sposób:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Skonfiguruj rozwiązanie tak, aby zawierało nowe pliki

Jeśli dodano nowe pliki modelu urządzenia, należy jawnie dołączyć je do rozwiązania. Dodaj wpis do **services/services.csproj** dla każdego dodatkowego pliku do uwzględnienia. Na przykład po zakończeniu [serializacji danych telemetrycznych przy użyciu buforów protokołu,](iot-accelerators-device-simulation-protobuf.md) należy dodać następujące wpisy:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generowanie nowych obrazów platformy Docker i wypychanie do centrum platformy Docker

Opublikuj nowy obraz platformy Docker w centrum platformy Docker przy użyciu konta centrum docker:

1. Otwórz wiersz polecenia i przejdź do lokalnej kopii repozytorium symulacji urządzenia.

1. Przejdź do folderu **docker:**

    ```cmd
    cd scripts\docker
    ```

1. Uruchom następujące polecenie, aby utworzyć obraz platformy Docker:

    ```cmd
    build.cmd
    ```

1. Uruchom następujące polecenie, aby opublikować obraz platformy Docker w repozytorium Usługi Docker Hub. Zaloguj się do platformy Docker przy użyciu poświadczeń usługi Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aktualizowanie usługi

Aby zaktualizować kontener Symulacja urządzenia w celu użycia obrazu niestandardowego, wykonaj następujące czynności:

* Użyj SSH, aby połączyć się z maszyną wirtualną obsługującą wystąpienie symulacji urządzenia. Użyj adresu IP i hasła, które zanotowałeś w poprzedniej sekcji:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Przejdź do katalogu **/app:**

    ```sh
    cd /app
    ```

* Edytuj plik **docker-compose.yml:**

    ```sh
    sudo nano docker-compose.yml
    ```

    Zmodyfikuj **obraz,** aby wskazać niestandardowy obraz **symulacji urządzenia** przesłany do repozytorium usługi Docker Hub:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Zapisz zmiany.

* Uruchom następujące polecenie, aby ponownie uruchomić mikrousługi:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Uruchamianie symulacji

Teraz można przeprowadzić symulację przy użyciu dostosowanego rozwiązania Device Simulation:

1. Uruchom internetowy interfejs użytkownika symulacji urządzeń z [akceleratorów rozwiązań IoT platformy Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Użyj interfejsu użytkownika sieci Web, aby skonfigurować i uruchomić symulację. Jeśli wcześniej zakończono [serializację danych telemetrycznych przy użyciu buforów protokołu,](iot-accelerators-device-simulation-protobuf.md)można użyć niestandardowego modelu urządzenia.

## <a name="next-steps"></a>Następne kroki

Teraz dowiesz się, jak wdrożyć niestandardowy obraz symulacji urządzenia, warto dowiedzieć się, jak [korzystać z istniejącego centrum IoT z akceleratorem rozwiązań Device Simulation](iot-accelerators-device-simulation-choose-hub.md).