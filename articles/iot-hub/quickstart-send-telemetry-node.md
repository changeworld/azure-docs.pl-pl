---
title: 'Szybki Start: wysyłanie danych telemetrycznych do usługi Azure IoT (Node. js)'
description: W tym przewodniku szybki start uruchomiono dwie przykładowe aplikacje Node. js umożliwiające wysyłanie symulowanych danych telemetrycznych do usługi IoT Hub oraz odczytywanie danych telemetrycznych z usługi IoT Hub na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 06/21/2019
ms.openlocfilehash: 92d6af41e55429f1b788de68940bc9b033c51ad6
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72167029"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-nodejs"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie ich z użyciem aplikacji zaplecza (Node. js)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT w chmurze w celu przechowania lub przetworzenia. W tym przewodniku szybki start wyślesz dane telemetryczne z aplikacji symulowanego urządzenia za pośrednictwem IoT Hub do aplikacji zaplecza do przetwarzania.

Przewodnik Szybki Start używa dwóch wstępnie zapisanych aplikacji node. js, jednej do wysłania telemetrii i jednego do odczytu danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć Centrum IoT Hub i zarejestrować urządzenie w centrum.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki Start są zapisywane w języku Node. js. Potrzebujesz środowiska Node. js v10. x. x lub nowszego na komputerze deweloperskim.

W programie Node. js można pobrać wiele platform z [NodeJS.org](https://nodejs.org).

Bieżącą wersję środowiska Node. js można sprawdzić na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pobierz przykładowy projekt node. js z https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip i Wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, urządzenie musi zostać zarejestrowane w usłudze IoT Hub. W tym przewodniku szybki start użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

   **MyNodeDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyNodeDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyNodeDevice
    ```

1. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyNodeDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

1. Potrzebne są również _Parametry połączenia usługi_ , aby umożliwić aplikacji zaplecza łączenie się z Centrum IoT Hub i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla Centrum IoT Hub:

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --name {YourIoTHubName} --policy-name service --output table
    ```

    Zanotuj parametry połączenia usługi, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start. Te parametry połączenia z usługą różnią się od parametrów połączenia urządzenia zanotowanych w poprzednim kroku.

## <a name="send-simulated-telemetry"></a>Wyślij symulowane dane telemetryczne

Aplikacja symulowanego urządzenia nawiązuje połączenie z punktem końcowym specyficznym dla urządzenia w centrum IoT Hub i wysyła symulowaną telemetrię temperatury i wilgotności.

1. Otwórz okno terminalu lokalnego, przejdź do folderu głównego przykładowego projektu Node. js. Następnie przejdź do folderu **IoT-hub\Quickstarts\simulated-Device** .

1. Otwórz plik **SimulatedDevice. js** w wybranym edytorze tekstu.

    Zastąp wartość zmiennej `connectionString` parametrami połączenia urządzenia, które zostały wykonane wcześniej. Następnie Zapisz zmiany w **SimulatedDevice. js**.

1. W oknie terminalu lokalnego Uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić aplikację symulowanego urządzenia:

    ```cmd/sh
    npm install
    node SimulatedDevice.js
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w postaci symulowanej aplikacji urządzenia, która wysyła telemetrię do centrum IoT:

    ![Uruchamianie symulowanego urządzenia](media/quickstart-send-telemetry-node/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **zdarzeń** po stronie usługi w IoT Hub. Aplikacja odbiera komunikaty z urządzenia do chmury wysyłane z symulowanego urządzenia. Aplikacja zaplecza IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty z urządzenia do chmury.

1. Otwórz inne lokalne okno terminalu, przejdź do folderu głównego przykładowego projektu Node. js. Następnie przejdź do folderu **IoT-hub\Quickstarts\read-D2C-messages** .

1. Otwórz plik **ReadDeviceToCloudMessages. js** w wybranym edytorze tekstu.

    Zastąp wartość zmiennej `connectionString` parametrami połączenia usługi, które zostały wykonane wcześniej w notatce. Następnie Zapisz zmiany w **ReadDeviceToCloudMessages. js**.

1. W oknie terminalu lokalnego Uruchom następujące polecenia, aby zainstalować wymagane biblioteki i uruchomić aplikację zaplecza:

    ```cmd/sh
    npm install
    node ReadDeviceToCloudMessages.js
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe, gdy aplikacja zaplecza odbiera telemetrię wysłaną przez symulowane urządzenie do centrum:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-node/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do koncentratora przy użyciu aplikacji node. js i odczytuje dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak sterować urządzeniem symulowanym z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki Start.

> [!div class="nextstepaction"]
> [Szybki Start: sterowanie urządzeniem podłączonym do centrum IoT Hub](quickstart-control-device-node.md)
