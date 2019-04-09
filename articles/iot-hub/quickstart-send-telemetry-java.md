---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (Java) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka Java wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/22/2019
ms.openlocfilehash: 12574dd6600004175ab85eead0f837544c6e5ebf
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "59004794"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-java"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT oraz odczytywanie ich przy użyciu aplikacji zaplecza (Java)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji języka Java, jednej do wysyłania danych telemetrycznych oraz jednej do odczytywania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć centrum IoT i zarejestrować urządzenie w centrum.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku Java. Potrzebujesz środowiska Java w wersji SE 8 lub nowszej na komputerze deweloperskim.

Możesz pobrać środowisko Java dla wielu platform ze strony [Oracle](https://aka.ms/azure-jdks).

Możesz sprawdzić bieżącą wersję języka Java na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
java -version
```

Aby utworzyć przykłady, musisz zainstalować narzędzie Maven 3. Możesz pobrać narzędzie Maven dla wielu platform ze strony [Apache Maven](https://maven.apache.org/download.cgi).

Możesz sprawdzić bieżącą wersję narzędzia Maven na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
mvn --version
```

Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pobierz przykładowy projekt Java z https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip i wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyJavaDevice**: nazwa rejestrowanego urządzenia. Użyj elementu **MyJavaDevice** w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane: **YourIoTHubName: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyJavaDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

3. Potrzebujesz również _punktu końcowego kompatybilnego z usługą Event Hubs_, _ścieżki kompatybilnej z usługą Event Hubs_ oraz _klucza podstawowego iothubowner_ z centrum IoT, aby zezwolić aplikacji zaplecza na nawiązywanie połączenia z centrum IoT i odbieranie wiadomości. Następujące polecenie pobiera te wartości dla Twojego centrum IoT:

     **YourIoTHubName: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name YourIoTHubName

    az iot hub show --query properties.eventHubEndpoints.events.path --name YourIoTHubName

    az iot hub policy show --name iothubowner --query primaryKey --hub-name YourIoTHubName
    ```

    Zanotuj te trzy wartości, których użyjesz w dalszej części tego przewodnika Szybki start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła symulowane dane telemetryczne dotyczące temperatury oraz wilgotności.

1. W lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `connString` parametrami połączenia urządzenia zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **SimulatedDevice.java**.

3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację urządzenia symulowanego:

    ```cmd/sh
    mvn clean package
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-java/SimulatedDevice.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **Zdarzenia** po stronie usługi w usłudze IoT Hub. Aplikacja odbiera komunikaty urządzenie-chmura wysyłane z urządzenia symulowanego. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty urządzenie-chmura.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\read-d2c-messages**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/ReadDeviceToCloudMessages.java** w wybranym edytorze tekstów. Zaktualizuj następujące zmienne i zapisz zmiany w pliku.

    | Zmienna | Wartość |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Zastąp wartość tej zmiennej punktem końcowym zgodnym z usługą Event Hubs zanotowanym wcześniej. |
    | `eventHubsCompatiblePath`     | Zastąp wartość tej zmiennej ścieżką zgodną z usługą Event Hubs zanotowaną wcześniej. |
    | `iotHubSasKey`                | Zastąp wartość tej zmiennej kluczem podstawowym iothubowner zanotowanym wcześniej. |


3. W lokalnym oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację zaplecza:

    ```cmd/sh
    mvn clean package
    ```

4. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację zaplecza:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie odbierania przez aplikację zaplecza danych telemetrycznych wysyłanych przez urządzenie symulowane do centrum:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-send-telemetry-java/ReadDeviceToCloud.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT, zarejestrowano urządzenie, wysłano dane telemetryczne do centrum przy użyciu aplikacji Java oraz odczytano dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak kontrolować urządzenie symulowane z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki start.

> [!div class="nextstepaction"]
> [Szybki start: Kontrolowanie urządzenia podłączone do usługi IoT hub](quickstart-control-device-java.md)