---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (C#) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka C# wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: c8bfb159dc56ff701f8d3c7eff00f04e28f8704a
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667817"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-net"></a>Szybki start: Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie ich z użyciem aplikacji zaplecza (.NET)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji języka C#, jednej do wysyłania danych telemetrycznych oraz jednej do odczytywania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku C#. Potrzebujesz zestawu SDK .NET Core w wersji 2.1.0 lub nowszej na komputerze deweloperskim.

Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://www.microsoft.com/net/download/all).

Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
dotnet --version
```

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pobierz przykładowy projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip i wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDotnetDevice**: nazwa rejestrowanego urządzenia. Użyj nazwy **MyDotnetDevice** w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDotnetDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDotnetDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

3. Wymagany jest również _Event Hubs punkt końcowy zgodny_ze standardem _Event Hubs_i _klucz podstawowy usługi_ z Centrum IoT, aby umożliwić aplikacji zaplecza łączenie się z Centrum IoT Hub i pobieranie komunikatów. Następujące polecenie pobiera te wartości dla Twojego centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name YourIoTHubName

    az iot hub show --query properties.eventHubEndpoints.events.path --name YourIoTHubName

    az iot hub policy show --name service --query primaryKey --hub-name YourIoTHubName
    ```

    Zanotuj te trzy wartości, których użyjesz w dalszej części tego przewodnika Szybki start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

2. Otwórz plik **SimulatedDevice.cs** w wybranym edytorze.

    Zastąp wartość zmiennej `s_connectionString` parametrami połączenia urządzenia zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **SimulatedDevice.cs**.

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane pakiety dla aplikacji urządzenia symulowanego:

    ```cmd/sh
    dotnet restore
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-dotnet/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu C#. Następnie przejdź do folderu **iot-hub\Quickstarts\read-d2c-messages**.

2. Otwórz plik **ReadDeviceToCloudMessages.cs** w wybranym edytorze. Zaktualizuj następujące zmienne i zapisz zmiany w pliku.

    | Zmienna | Wartość |
    | -------- | ----------- |
    | `s_eventHubsCompatibleEndpoint` | Zastąp wartość tej zmiennej punktem końcowym zgodnym z usługą Event Hubs zanotowanym wcześniej. |
    | `s_eventHubsCompatiblePath`     | Zastąp wartość tej zmiennej ścieżką zgodną z usługą Event Hubs zanotowaną wcześniej. |
    | `s_iotHubSasKey`                | Zastąp wartość zmiennej kluczem podstawowym usługi, dla którego wykonano wcześniej adnotację. |

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki dla aplikacji zaplecza:

    ```cmd/sh
    dotnet restore
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby utworzyć i uruchomić aplikację zaplecza:

    ```cmd/sh
    dotnet run
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez aplikację zaplecza danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-dotnet/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT, zarejestrowano urządzenie, wysłano dane telemetryczne do centrum przy użyciu aplikacji C# oraz odczytano dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: Sterowanie urządzeniem połączonym z centrum IoT Hub](quickstart-control-device-dotnet.md)