---
title: Podłącz przykładowy kod urządzenia IoT Plug and Play Preview do usługi IoT Hub (Linux) | Dokumenty firmy Microsoft
description: Tworzenie i uruchamianie przykładowego kodu urządzenia IoT Plug and Play Preview w systemie Linux, który łączy się z centrum IoT hub. Użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 8134c0a97f6350cfa2cf616695c5990618455393
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75531272"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Szybki start: podłącz przykładową aplikację urządzenia IoT Plug and Play Preview działającą w systemie Linux z centrum IoT Hub (C Linux)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

Ten przewodnik Szybki start pokazuje, jak utworzyć przykładową aplikację urządzenia IoT Plug and Play w systemie Linux, połączyć ją z bub IoT i użyć interfejsu wiersza polecenia platformy Azure, aby wyświetlić informacje, które wysyła do centrum. Przykładowa aplikacja jest napisana w języku C i znajduje się w zestawie SDK urządzenia Usługi Azure IoT dla języka C. Deweloper rozwiązania można użyć interfejsu wiersza polecenia platformy Azure, aby zrozumieć możliwości urządzenia Typu IoT Plug and Play bez konieczności wyświetlania kodu urządzenia.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Ten szybki start zakłada, że używasz Ubuntu Linux. Kroki w tym samouczku zostały przetestowane przy użyciu Ubuntu 18.04.

Aby ukończyć ten szybki start, należy zainstalować następujące oprogramowanie na lokalnym komputerze z systemem Linux:

Zainstaluj **GCC**, **Git**, **cmake**i `apt-get` wszystkie zależności za pomocą polecenia:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Sprawdź, czy `cmake` wersja jest powyżej **2.8.12,** a wersja **GCC** jest powyżej **4.4.7**.

```sh
cmake --version
gcc --version
```

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start należy przygotować środowisko programistyczne, którego można użyć do klonowania i tworzenia sdk C urządzenia usługi Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [zestawY SDK C usługi Azure IoT i biblioteki](https://github.com/Azure/azure-iot-sdk-c) github w tej lokalizacji:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Użyj sdk urządzenia do tworzenia dołączonego przykładowego kodu. Aplikacja, która tworzysz symuluje urządzenie, które łączy się z centrum IoT hub. Aplikacja wysyła dane telemetryczne i właściwości i odbiera polecenia.

1. Utwórz `cmake` podkatalog w folderze głównym sdk urządzenia i przejdź do tego folderu:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby utworzyć sdk urządzenia i wygenerowany skrót kodu:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Aktualizowanie repozytorium modelu

Przed uruchomieniem przykładu dodaj model możliwości urządzenia i definicje interfejsu do repozytorium modelu firmy:

1. Zaloguj się do portalu [portalu Certyfikat platformy Azure dla IoT za](https://preview.catalog.azureiotsolutions.com) pomocą konta służbowego firmy Microsoft lub identyfikatora partnera firmy Microsoft, jeśli go masz.

1. Wybierz **repozytorium firmowe,** a następnie **modele możliwości**.

1. Wybierz **pozycję Nowy,** a następnie **pozycję Przekaż**.

1. Wybierz plik `SampleDevice.capabilitymodel.json` w `digitaltwin_client/samples` folderze w folderze głównym SDK urządzenia. Wybierz **pozycję Otwórz,** a następnie **pozycję Zapisz,** aby przekazać plik modelu do repozytorium.

1. Wybierz **repozytorium firmowe,** a następnie **pozycję Interfejsy**.

1. Wybierz **pozycję Nowy,** a następnie **pozycję Przekaż**.

1. Wybierz plik `EnvironmentalSensor.interface.json` w `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` folderze w folderze głównym SDK urządzenia. Wybierz **pozycję Otwórz,** a następnie **pozycję Zapisz,** aby przekazać plik interfejsu do repozytorium.

1. Wybierz **repozytorium firmowe,** a następnie **parametry połączenia**. Zanotuj pierwszy _ciąg połączenia repozytorium modelu firmy_, ponieważ jest on używany w dalszej części tego przewodnika Szybki start.

## <a name="run-the-device-sample"></a>Uruchamianie próbki urządzenia

Uruchom przykładową aplikację w SDK, aby symulować urządzenie Typu Plug and Play IoT, które wysyła dane telemetryczne do centrum IoT hub. Aby uruchomić przykładową aplikację:

1. Z `cmake` folderu przejdź do folderu zawierającego plik wykonywalny:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Uruchom plik wykonywalny:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do koncentratora. Zachowaj uruchomienie próbki podczas wykonywania kolejnych kroków.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą interfejsu wiersza polecenia IoT platformy Azure

Po uruchomieniu przykładu klienta urządzenia sprawdź, czy działa z interfejsu wiersza polecenia platformy Azure.

Użyj następującego polecenia, aby wyświetlić dane telemetryczne wysyłane przez przykładowe urządzenie. Może być konieczne odczekanie minuty lub dwóch, zanim zobaczysz dane telemetryczne w danych wyjściowych:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Aby wyświetlić właściwości wysyłane przez urządzenie, użyj następującego polecenia:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak podłączyć urządzenie Typu Plug and Play do centrum IoT hub. Aby dowiedzieć się więcej o tworzeniu rozwiązania współdziałanego z urządzeniami IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: łączenie się z urządzeniem i interakcję z nim](howto-develop-solution.md)
