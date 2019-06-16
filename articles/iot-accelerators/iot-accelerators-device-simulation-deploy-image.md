---
title: Wdrażanie niestandardowych obrazów symulacji urządzenia — Azure | Dokumentacja firmy Microsoft
description: W tym przewodniku dowiesz się, jak wdrożyć niestandardowy obraz platformy Docker rozwiązania symulacji urządzenia na platformie Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61448417"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Wdrażanie niestandardowego obrazu docker symulacji urządzenia

Można zmodyfikować rozwiązania symulacji urządzenia w celu dodania funkcji niestandardowych. Na przykład [serializacji danych telemetrycznych za pomocą Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) artykułu dowiesz się, jak dodać niestandardowe urządzenie do rozwiązania, które używa formatu Protocol Buffers (Protobuf) do wysyłania telemetrii. Po przetestowaniu zmiany lokalnie, następnym krokiem jest wdrażają zmiany do swojego wystąpienia symulacji urządzenia na platformie Azure. Aby wykonać to zadanie, należy utworzyć i wdrożyć obraz platformy Docker, który zawiera Twoje zmodyfikowaną usługę.

Kroki opisane w tym jak-to-przewodniku pokazano, jak do:

1. Przygotowywanie środowiska deweloperskiego
1. Generuj nowy obraz platformy Docker
1. Konfigurowanie symulacji urządzenia, aby użyć nowy obraz platformy Docker
1. Uruchamianie symulacji za pomocą nowego obrazu

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku z instrukcjami, potrzebne są:

* Wdrożony [symulacji urządzenia](quickstart-device-simulation-deploy.md) wystąpienia.
* Docker. Pobierz [platformę Docker Community Edition](https://www.docker.com/products/docker-engine#/download) dla danej platformy.
* A [konto usługi Docker Hub](https://hub.docker.com/) gdzie możesz przekazać obrazy usługi Docker. W ramach konta usługi Docker Hub, utworzyć publicznego repozytorium o nazwie **symulacji urządzenia**.
* Element zmodyfikowany i przetestowane [symulacji urządzenia rozwiązania](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) na komputerze lokalnym. Na przykład można zmodyfikować rozwiązanie, aby [serializacji danych telemetrycznych za pomocą Protocol Buffers](iot-accelerators-device-simulation-protobuf.md).
* Powłoka, która może uruchomić SSH. Po zainstalowaniu usługi Git dla Windows, można użyć **bash** powłoka, która jest częścią tej instalacji. Można również użyć usługi [usługi Azure Cloud Shell](https://shell.azure.com/).

Instrukcje w tym artykule przyjęto założenie, że używasz Windows. Jeśli używasz innego systemu operacyjnego może być konieczne dostosowanie niektórych ścieżek plików i poleceń w zależności od środowiska.

## <a name="create-a-new-docker-image"></a>Utwórz nowy obraz platformy Docker

Aby wdrożyć tych zmian usługa symulacji urządzenia, należy edytować skrypty kompilacji i wdrażania w **scripts\docker** folderu do przekazania kontenerów na koncie usługi docker hub

### <a name="modify-the-docker-scripts"></a>Zmodyfikowanie skryptów platformy docker

Modyfikowanie platformy Docker **build.cmd**, **publish.cmd**, i **run.cmd** skrypty w **scripts\docker** folder z usługi Docker Hub informacje o repozytorium. Procedura zakłada, że utworzono publicznego repozytorium o nazwie **symulacji urządzenia**:

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Aktualizacja **docker-compose.yml** pliku w następujący sposób:

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Konfigurowanie rozwiązania, aby uwzględnić nowe pliki

Jeśli dodano nowe pliki modelu urządzenia, musisz jawnie umieszczone w rozwiązaniu. Dodaj wpis do **services/services.csproj** dla każdego dodatkowego pliku do uwzględnienia. Na przykład, jeśli została zakończona [serializacji danych telemetrycznych za pomocą Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) porad, Dodaj następujące wpisy:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Generowanie nowych obrazów platformy Docker i Wypychanie do usługi Docker Hub

Publikuj nowy obraz platformy Docker do usługi Docker Hub przy użyciu swojego konta usługi docker hub:

1. Otwórz wiersz polecenia i przejdź do swojej lokalnej kopii repozytorium symulacji urządzenia.

1. Przejdź do **docker** folderu:

    ```cmd
    cd scripts\docker
    ```

1. Uruchom następujące polecenie, aby utworzyć obraz platformy Docker:

    ```cmd
    build.cmd
    ```

1. Uruchom następujące polecenie, aby opublikować obrazu platformy Docker do repozytorium usługi Docker Hub. Zaloguj się do platformy Docker przy użyciu swoich poświadczeń usługi Docker Hub:

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Aktualizacja usługi

Aby zaktualizować kontener symulacji urządzenia, aby użyć niestandardowego obrazu, wykonaj następujące czynności:

* Łączenie z maszyną wirtualną hostingu wystąpienia symulacji urządzenia za pomocą protokołu SSH. Użyj adresu IP i hasła, które należy zanotować w poprzedniej sekcji:

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Przejdź do **App** katalogu:

    ```sh
    cd /app
    ```

* Edytuj **docker-compose.yml** pliku:

    ```sh
    sudo nano docker-compose.yml
    ```

    Modyfikowanie **obraz** aby wskazać niestandardową **symulacji urządzenia** obraz został przekazany do repozytorium usługi Docker Hub:

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Zapisz zmiany.

* Uruchom następujące polecenie, aby ponownie uruchomić mikrousługi:

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Uruchamianie symulacji

Teraz możesz uruchomić symulację za pomocą rozwiązania dostosowanego do symulacji urządzenia:

1. Uruchomienie symulacji urządzenia internetowego interfejsu użytkownika z [akceleratorów rozwiązań IoT Azure Microsoft](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Użyj interfejsu użytkownika sieci web, aby skonfigurować i uruchomić symulację. Jeśli wcześniej wykonano [serializacji danych telemetrycznych za pomocą Protocol Buffers](iot-accelerators-device-simulation-protobuf.md), można użyć modelu niestandardowego urządzenia.

## <a name="next-steps"></a>Kolejne kroki

Teraz już wiesz, jak wdrażanie niestandardowych obrazów symulacji urządzenia, możesz chcieć Dowiedz się, jak [już istniejące Centrum IoT za pomocą symulacji urządzenia akceleratora rozwiązań](iot-accelerators-device-simulation-choose-hub.md).