---
title: Kontrolowanie urządzenia z poziomu usługi Azure IoT Hub — Szybki start (Python) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje Python. Jedna z aplikacji to aplikacja zaplecza, która może zdalnie kontrolować urządzenia podłączone do centrum. Druga z aplikacji symuluje urządzenie podłączone do centrum, które można kontrolować zdalnie.
services: iot-hub
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: ns
ms.date: 04/30/2018
ms.author: dobett
ms.openlocfilehash: b6b8eb7150f276c754e45113b81b4e7fb61cb7ee
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury i zarządzanie urządzeniami z poziomu chmury. W tym przewodniku Szybki start użyjesz *metody bezpośredniej* do kontrolowania urządzenia symulowanego podłączonego do centrum IoT. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji Python:

* Aplikacja urządzenia symulowanego, która reaguje na metody bezpośrednie wywoływane z aplikacji zaplecza. Aby odbierać wywołania metod bezpośrednich, ta aplikacja łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT.
* Aplikacja zaplecza, która wywołuje metody bezpośrednie na urządzeniu symulowanym. Aby wywoływać metody bezpośrednie na urządzeniu, ta aplikacja łączy się z punktem końcowym po stronie usługi w centrum IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku Python. Potrzebujesz języka Python w wersji 2.7.x lub 3.5.x na komputerze deweloperskim.

Możesz pobrać język Python dla wielu platform ze strony [Python.org](https://www.python.org/downloads/).

Możesz sprawdzić bieżącą wersję języka Python na komputerze deweloperskim przy użyciu jednego z następujących poleceń:

```python
python --version
```

```python
python3 --version
```

Jeśli nie zostało to jeszcze zrobione, pobierz przykładowy projekt Python z https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip i wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start urządzenie symulowane jest rejestrowane przy użyciu interfejsu wiersza polecenia platformy Azure.

1. Dodaj rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utwórz tożsamość urządzenia. Zastąp ciąg `{YourIoTHubName}` nazwą centrum IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

    Jeśli wybierzesz inną nazwę dla swojego urządzenia, zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

1. Uruchom następujące polecenie, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco: `Hostname=...=`. Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

1. Potrzebne będą także _parametry połączenia usługi_, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

    ```azurecli-interactive
    az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
    ```

    Zanotuj parametry połączenia usługi, które wyglądają następująco: `Hostname=...=`. Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Parametry połączenia usługi różnią się od parametrów połączenia urządzenia.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Aplikacja urządzenia symulowanego łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Urządzenie symulowane wysyła potwierdzenie z powrotem do centrum po wykonaniu metody bezpośredniej.

1. W oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **Quickstarts\simulated-device-2**.

1. Otwórz plik **SimulatedDevice.py** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `CONNECTION_STRING` parametrami połączenia urządzenia zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **SimulatedDevice.py**.

1. W oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. W oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja zaplecza łączy się z punktem końcowym po stronie usługi w usłudze IoT Hub. Aplikacja wykonuje wywołania metod bezpośrednich do urządzenia za pośrednictwem centrum IoT oraz nasłuchuje potwierdzeń. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze.

1. W innym oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **Quickstarts\back-end-application**.

1. Otwórz plik **BackEndApplication.py** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `CONNECTION_STRING` parametrami połączenia usługi zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **BackEndApplication.py**.

1. W oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. W oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację zaplecza:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w sytuacji, w której aplikacja wykonuje wywołanie metody bezpośredniej do urządzenia i odbiera potwierdzenie:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-control-device-python/BackEndApplication.png)

    Po uruchomieniu aplikacji zaplecza zobaczysz komunikat w oknie konsoli uruchomionym na urządzeniu symulowanym, a także zobaczysz, że zmienia się prędkość wysyłania komunikatów:

    ![Zmiana w kliencie symulowanym](media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz przejść do samouczków, pozostaw grupę zasobów i centrum IoT, aby użyć ich później.

Jeśli nie potrzebujesz już tego centrum IoT, usuń je oraz grupę zasobów z poziomu portalu. Aby to zrobić, wybierz grupę zasobów zawierającą centrum IoT, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji zaplecza oraz odpowiedziano na wywołanie metody bezpośredniej w aplikacji urządzenia symulowanego.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](iot-hub-python-python-process-d2c.md)