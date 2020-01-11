---
title: Kontrolowanie urządzenia z poziomu usługi Azure IoT Hub — Szybki start (Python) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje Python. Jedna z aplikacji to aplikacja zaplecza, która może zdalnie kontrolować urządzenia podłączone do centrum. Druga z aplikacji symuluje urządzenie podłączone do centrum, które można kontrolować zdalnie.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/09/2020
ms.openlocfilehash: 11768a0d72549d917d93c0f6f7f4d0c7e8217da4
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864420"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia zarządzanie urządzeniami IoT z chmury oraz pozyskiwanie dużych ilości danych telemetrycznych urządzeń do chmury w celu przechowania lub przetworzenia. W tym przewodniku Szybki start użyjesz *metody bezpośredniej* do kontrolowania urządzenia symulowanego podłączonego do centrum IoT. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji Python:

* Aplikacja urządzenia symulowanego, która reaguje na metody bezpośrednie wywoływane z aplikacji zaplecza. Aby odbierać wywołania metod bezpośrednich, ta aplikacja łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT.

* Aplikacja zaplecza, która wywołuje metody bezpośrednie na urządzeniu symulowanym. Aby wywoływać metody bezpośrednie na urządzeniu, ta aplikacja łączy się z punktem końcowym po stronie usługi w centrum IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Jeśli nie zostało to jeszcze zrobione, pobierz przykładowy projekt Python z https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip i wyodrębnij archiwum ZIP.

Na komputerze deweloperskim jest zainstalowany język [Python w wersji 3,7 lub nowszej](https://www.python.org/downloads/) . W przypadku innych obsługiwanych wersji języka Python zapoznaj się z tematem [Azure IoT Device Features](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features) w dokumentacji zestawu SDK.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    **MyPythonDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyPythonDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

3. Potrzebne będą także _parametry połączenia usługi_, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum IoT i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

    **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Zanotuj parametry połączenia usługi, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start. Te parametry połączenia z usługą różnią się od parametrów połączenia urządzenia zanotowanych w poprzednim kroku.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Aplikacja urządzenia symulowanego łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Symulowane urządzenie wysyła potwierdzenie z powrotem do centrum po wykonaniu metody bezpośredniej.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device-2**.

1. Otwórz plik **SimulatedDevice.py** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `CONNECTION_STRING` parametrami połączenia urządzenia, które zostały wykonane wcześniej jako uwagi. Następnie Zapisz zmiany w **SimulatedDevice.py**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](./media/quickstart-control-device-python/simulated-device-1.png)

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja zaplecza łączy się z punktem końcowym po stronie usługi w usłudze IoT Hub. Aplikacja wykonuje bezpośrednie wywołania metody do urządzenia za pośrednictwem Centrum IoT Hub i nasłuchuje pod kątem potwierdzeń. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Python. Następnie przejdź do folderu **iot-hub\Quickstarts\back-end-application**.

1. Otwórz plik **BackEndApplication.py** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `CONNECTION_STRING` parametrami połączenia usługi, które zostały wykonane wcześniej w notatce. Następnie Zapisz zmiany w **BackEndApplication.py**.

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację zaplecza:

    ```cmd/sh
    python BackEndApplication.py
    ```

    Na poniższym zrzucie ekranu przedstawiono dane wyjściowe, gdy aplikacja wysyła wywołanie metody bezpośredniej do urządzenia i otrzymuje potwierdzenie:

    ![Uruchamianie aplikacji zaplecza](./media/quickstart-control-device-python/backend-application.png)

    Po uruchomieniu aplikacji zaplecza zobaczysz komunikat w oknie konsoli uruchomionym na urządzeniu symulowanym, a także zobaczysz, że zmienia się prędkość wysyłania komunikatów:

    ![Zmiana w kliencie symulowanym](./media/quickstart-control-device-python/simulated-device-2.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji zaplecza oraz odpowiedziano na wywołanie metody bezpośredniej w aplikacji urządzenia symulowanego.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](tutorial-routing.md)