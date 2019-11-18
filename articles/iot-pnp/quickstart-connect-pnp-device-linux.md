---
title: Podłączanie przykładowego kodu urządzenia do programu IoT Plug and Play w wersji zapoznawczej do IoT Hub (Linux) | Microsoft Docs
description: Kompiluj i uruchamiaj Podgląd Plug and Play IoT przykład kod urządzenia w systemie Linux, który nawiązuje połączenie z usługą IoT Hub. Użyj interfejsu wiersza polecenia platformy Azure, aby wyświetlić informacje wysyłane przez urządzenie do centrum.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 802578c79fa086c74a56db8d47f83ae96d6b0194
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152142"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub-c-linux"></a>Szybki Start: łączenie przykładowej aplikacji urządzenia IoT Plug and Play w wersji zapoznawczej uruchomionej w systemie Linux do IoT Hub (C Linux)

W tym przewodniku szybki start przedstawiono sposób tworzenia przykładowej aplikacji urządzenia IoT Plug and Play w systemie Linux, nawiązywania połączenia z usługą IoT BUB oraz używania interfejsu wiersza polecenia platformy Azure do wyświetlania informacji wysyłanych do centrum. Przykładowa aplikacja jest zapisywana w języku C i jest uwzględniona w zestawie SDK urządzeń Azure IoT dla języka C. Deweloperzy rozwiązań mogą korzystać z interfejsu wiersza polecenia platformy Azure, aby zrozumieć możliwości urządzenia Plug and Play IoT bez konieczności wyświetlania kodu urządzeń.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Wymagania wstępne

W tym przewodniku szybki start przyjęto założenie, że używasz Ubuntu Linux. Kroki opisane w tym samouczku zostały przetestowane przy użyciu Ubuntu 18,04.

Aby ukończyć ten przewodnik Szybki Start, musisz zainstalować następujące oprogramowanie na lokalnym komputerze z systemem Linux:

Zainstaluj usługi w **zatoce**, **git**, **CMAKE**i wszystkie zależności przy użyciu polecenia `apt-get`:

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Upewnij się, że wersja `cmake` jest wyższa niż **2.8.12** , a wersja programu w **zatoce** jest większa niż **4.4.7**.

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>Przygotowywanie Centrum IoT Hub

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz również usługi Azure IoT Hub w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Jeśli nie masz Centrum IoT Hub, postępuj zgodnie [z tymi instrukcjami, aby je utworzyć](../iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> W publicznej wersji zapoznawczej funkcje Plug and Play IoT są dostępne tylko w centrach IoT, które zostały utworzone w regionach **środkowe stany USA**, **Europa Północna**i **Japonia Wschodnia** .

Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, wersja `az` powinna być **2.0.73** lub nowsza; Azure Cloud Shell używa najnowszej wersji. Aby sprawdzić wersję zainstalowaną na komputerze, użyj polecenia `az --version`.

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell:
```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Kroki w tym przewodniku szybki start wymagają wersji **0.8.5** lub nowszej rozszerzenia. Użyj `az extension list` polecenia, aby sprawdzić zainstalowaną wersję programu, i polecenie `az extension update` do zaktualizowania w razie potrzeby.

Jeśli używasz interfejsu wiersza polecenia lokalnie, zaloguj się do subskrypcji platformy Azure za pomocą następującego polecenia:

```bash
az login
```

Jeśli używasz Azure Cloud Shell, użytkownik jest już automatycznie zalogowany.

Uruchom następujące polecenie, aby utworzyć tożsamość urządzenia w centrum IoT Hub. Zastąp symbole zastępcze **YourIoTHubName** i **YourDeviceID** własną _nazwą IoT Hub_ i wybranym przez siebie _identyfikatorem urządzenia_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Uruchom następujące polecenie, aby pobrać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia (Uwaga do użycia później):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku szybki start przygotowano środowisko programistyczne, którego można użyć do klonowania i kompilowania zestawu SDK języka C dla systemu Azure IoT Hub.

Otwórz wiersz polecenia w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować repozytorium [usługi Azure IoT C SDK i biblioteki](https://github.com/Azure/azure-iot-sdk-c) GitHub do tej lokalizacji:

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

## <a name="build-the-code"></a>Kompilowanie kod

Zestaw SDK urządzenia służy do tworzenia dołączonego przykładowego kodu. Utworzona Aplikacja symuluje urządzenie, które nawiązuje połączenie z usługą IoT Hub. Aplikacja wysyła dane telemetryczne i właściwości oraz odbiera polecenia.

1. Utwórz podkatalog `cmake` w folderze głównym zestawu SDK urządzeń i przejdź do tego folderu:

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia, aby skompilować zestaw SDK urządzeń i wygenerowany skrót kodu:

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>Aktualizowanie repozytorium modelu

Przed uruchomieniem przykładu Dodaj model możliwości urządzenia i definicje interfejsów do repozytorium modelu firmy:

1. Zaloguj się do portalu [Azure Certified for IoT Portal](https://preview.catalog.azureiotsolutions.com) przy użyciu konta służbowego firmy Microsoft lub swojego identyfikatora partnera firmy Microsoft, jeśli go masz.

1. Wybierz opcję **repozytorium firmowe** , a następnie pozycję **modele możliwości**.

1. Wybierz pozycję **Nowy** , a następnie **Przekaż**.

1. Wybierz plik `SampleDevice.capabilitymodel.json` w folderze `digitaltwin_client/samples` w folderze głównym zestawu SDK urządzeń. Wybierz pozycję **Otwórz** , a następnie **Zapisz** , aby przekazać plik modelu do repozytorium.

1. Wybierz opcję **repozytorium firmowe** , a następnie pozycję **interfejsy**.

1. Wybierz pozycję **Nowy** , a następnie **Przekaż**.

1. Wybierz plik `EnvironmentalSensor.interface.json` w folderze `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` w folderze głównym zestawu SDK urządzeń. Wybierz pozycję **Otwórz** , a następnie **Zapisz** , aby przekazać plik interfejsu do repozytorium.

1. Wybierz pozycję **repozytorium firmowe** , a następnie **Parametry połączenia**. Zanotuj pierwsze _Parametry połączenia repozytorium modelu firmy_, korzystając z niego w dalszej części tego przewodnika Szybki Start.

## <a name="run-the-device-sample"></a>Uruchamianie przykładu urządzenia

Uruchom przykładową aplikację w zestawie SDK, aby symulować urządzenie Plug and Play IoT wysyłające dane telemetryczne do centrum IoT. Aby uruchomić przykładową aplikację:

1. W folderze `cmake` przejdź do folderu, który zawiera plik wykonywalny:

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. Uruchom plik wykonywalny:

    ```bash
    ./digitaltwin_sample_device "<YourDeviceConnectionString>"
    ```

Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do centrum. Kontynuuj działanie przykładu w przypadku wykonywania następnych kroków.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Sprawdzanie poprawności kodu za pomocą interfejsu wiersza polecenia usługi Azure IoT

Po uruchomieniu przykładowego klienta urządzenia sprawdź, czy pracuje on z interfejsem wiersza polecenia platformy Azure.

Użyj poniższego polecenia, aby wyświetlić dane telemetryczne wysyłane przez przykładowe urządzenie. Może być konieczne poczekanie minutę lub dwie, zanim zobaczysz dane telemetryczne w danych wyjściowych:

```azurecli-interactive
az iot dt monitor-events --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Użyj następującego polecenia, aby wyświetlić właściwości wysyłane przez urządzenie:

```azurecli-interactive
az iot dt list-properties --hub-name <YourIoTHubName> --device-id <YourDeviceID> --interface sensor --source private --repo-login "<YourCompanyModelRepositoryConnectionString>"
```
[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób nawiązywania połączenia z urządzeniem IoT Plug and Play w usłudze IoT Hub. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
