---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (Python) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz przykładową aplikację języka Python wysyłającą symulowane dane telemetryczne do centrum IoT oraz używającą narzędzia do odczytywania danych telemetrycznych z centrum IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/28/2019
ms.openlocfilehash: a08719d322f044bbf1ced8103af5e4e23ed948c9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998479"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-python"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT oraz odczytywanie ich przy użyciu aplikacji zaplecza (Python)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

W tym przewodniku Szybki start używana jest wstępnie napisana aplikacja języka Python wysyłająca dane telemetryczne oraz narzędzie interfejsu wiersza polecenia odczytujące dane telemetryczne z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pobierz przykładowy projekt Python z https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip i wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    **MyPythonDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj elementu MyPythonDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyPythonDevice
    ```

1. Uruchom następujące polecenia w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyPythonDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

1. Otwórz plik **SimulatedDevice.py** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `CONNECTION_STRING` parametrami połączenia urządzenia zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **SimulatedDevice.py**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-python/SimulatedDevice.png)


## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Rozszerzenie interfejsu wiersza polecenia usługi IoT Hub może połączyć się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Rozszerzenie odbiera komunikaty z urządzenia do chmury wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

Uruchom następujące polecenia w usłudze Azure Cloud Shell, zastępując ciąg `YourIoTHubName` nazwą swojego centrum IoT:

```azurecli-interactive
az iot hub monitor-events --hub-name YourIoTHubName --device-id MyPythonDevice 
```

Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez rozszerzenie danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-python/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT, zarejestrowano urządzenie, wysłano dane telemetryczne do centrum przy użyciu aplikacji Python oraz odczytano dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: Sterowanie urządzeniem połączonym z centrum IoT Hub](quickstart-control-device-python.md)
