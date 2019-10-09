---
title: 'Szybki Start: wysyłanie danych telemetrycznych do usługi Azure IoT Hub przy użyciu języka Java'
description: W tym przewodniku szybki start uruchomisz dwie przykładowe aplikacje Java do wysyłania symulowanych danych telemetrycznych do centrum IoT i odczytywanie danych telemetrycznych z usługi IoT Hub na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: cb115b8658850fc85f93fc7a9508a82ecee920d8
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166442"
---
# <a name="quickstart-send-telemetry-to-an-azure-iot-hub-and-read-it-with-a-java-application"></a>Szybki Start: wysyłanie danych telemetrycznych do usługi Azure IoT Hub i odczytywanie ich za pomocą aplikacji Java

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

Przewodnik Szybki Start przedstawia sposób wysyłania danych telemetrycznych do usługi Azure IoT Hub i odczytywania ich za pomocą aplikacji Java. IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT w chmurze w celu przechowania lub przetworzenia. W tym przewodniku szybki start wyślesz dane telemetryczne z aplikacji symulowanego urządzenia za pośrednictwem IoT Hub do aplikacji zaplecza do przetwarzania.

Przewodnik Szybki Start korzysta z dwóch wstępnie pisanych aplikacji Java, a następnie wysyła dane telemetryczne i jeden w celu odczytania danych telemetrycznych z centrum. Przed uruchomieniem tych dwóch aplikacji należy utworzyć Centrum IoT Hub i zarejestrować urządzenie w centrum.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki Start są zapisywane przy użyciu języka Java. Potrzebujesz języka Java SE 8 na komputerze deweloperskim.

Możesz pobrać Java SE Development Kit 8 dla wielu platform w [języku Java — długoterminowa obsługa platformy Azure i Azure Stack](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable). Upewnij się, że wybrano opcję **Java 8** w obszarze **Obsługa długoterminowa** , aby pobrać pliki do pobrania dla programu JDK 8.

Bieżącą wersję środowiska Java na komputerze deweloperskim można sprawdzić za pomocą następującego polecenia:

```cmd/sh
java -version
```

Aby skompilować przykłady, należy zainstalować Maven 3. Możesz pobrać Maven dla wielu platform z platformy [Apache Maven](https://maven.apache.org/download.cgi).

Bieżącą wersję Maven można sprawdzić na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
mvn --version
```

Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Pobierz przykładowy projekt Java z https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip i Wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, urządzenie musi zostać zarejestrowane w usłudze IoT Hub. W tym przewodniku szybki start użyjesz Azure Cloud Shell, aby zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

   **MyJavaDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyJavaDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

    **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyJavaDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

3. Wymagany jest również _Event Hubs punkt końcowy zgodny_ze standardem _Event Hubs_i _klucz podstawowy usługi_ z Centrum IoT, aby umożliwić aplikacji zaplecza łączenie się z Centrum IoT Hub i pobieranie komunikatów. Następujące polecenia pobierają te wartości dla Centrum IoT:

     **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show --query properties.eventHubEndpoints.events.endpoint --name {YourIoTHubName}

    az iot hub show --query properties.eventHubEndpoints.events.path --name {YourIoTHubName}

    az iot hub policy show --name service --query primaryKey --hub-name {YourIoTHubName}
    ```

    Zanotuj te trzy wartości, których będziesz używać w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wyślij symulowane dane telemetryczne

Aplikacja symulowanego urządzenia nawiązuje połączenie z punktem końcowym specyficznym dla urządzenia w centrum IoT Hub i wysyła symulowaną telemetrię temperatury i wilgotności.

1. W oknie terminalu lokalnego przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **IoT-hub\Quickstarts\simulated-Device** .

2. Otwórz plik **src/Main/Java/com/Microsoft/docs/iothub/Samples/SimulatedDevice. Java** w wybranym edytorze tekstu.

    Zastąp wartość zmiennej `connString` parametrami połączenia urządzenia, które zostały wykonane wcześniej. Następnie Zapisz zmiany w **SimulatedDevice. Java**.

3. W oknie terminalu lokalnego Uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację symulowanego urządzenia:

    ```cmd/sh
    mvn clean package
    ```

4. W oknie terminalu lokalnego Uruchom następujące polecenia, aby uruchomić aplikację symulowanego urządzenia:

    ```cmd/sh
    java -jar target/simulated-device-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w postaci symulowanej aplikacji urządzenia, która wysyła telemetrię do centrum IoT:

    ![Dane wyjściowe z telemetrii wysyłanej przez urządzenie do centrum IoT Hub](media/quickstart-send-telemetry-java/iot-hub-simulated-device.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

Aplikacja zaplecza łączy się z punktem końcowym **zdarzeń** po stronie usługi w IoT Hub. Aplikacja odbiera komunikaty z urządzenia do chmury wysyłane z symulowanego urządzenia. Aplikacja zaplecza IoT Hub zwykle działa w chmurze, aby odbierać i przetwarzać komunikaty z urządzenia do chmury.

1. W innym lokalnym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **IoT-hub\Quickstarts\read-D2C-messages** .

2. Otwórz plik **src/Main/Java/com/Microsoft/docs/iothub/Samples/ReadDeviceToCloudMessages. Java** w wybranym edytorze tekstu. Zaktualizuj następujące zmienne i Zapisz zmiany w pliku.

    | Zmienna | Wartość |
    | -------- | ----------- |
    | `eventHubsCompatibleEndpoint` | Zastąp wartość zmiennej tym punktem końcowym zgodnym z Event Hubs, który został wcześniej zanotowany. |
    | `eventHubsCompatiblePath`     | Zastąp wartość zmiennej ścieżką zgodną Event Hubs zanotowaną wcześniej. |
    | `iotHubSasKey`                | Zastąp wartość zmiennej kluczem podstawowym usługi sporządzonym wcześniej w notatce. |

3. W oknie terminalu lokalnego Uruchom następujące polecenia, aby zainstalować wymagane biblioteki i skompilować aplikację zaplecza:

    ```cmd/sh
    mvn clean package
    ```

4. W oknie terminalu lokalnego Uruchom następujące polecenia, aby uruchomić aplikację zaplecza:

    ```cmd/sh
    java -jar target/read-d2c-messages-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe, gdy aplikacja zaplecza odbiera telemetrię wysłaną przez symulowane urządzenie do centrum:

    ![Dane wyjściowe w postaci aplikacji zaplecza odbierają Telemetria wysyłane do centrum IoT Hub](media/quickstart-send-telemetry-java/iot-hub-read-device-to-cloud.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłałeś symulowane dane telemetryczne do centrum przy użyciu aplikacji Java i odczytujesz dane telemetryczne z centrum przy użyciu prostej aplikacji zaplecza.

Aby dowiedzieć się, jak sterować urządzeniem symulowanym z poziomu aplikacji zaplecza, przejdź do następnego przewodnika Szybki Start.

> [!div class="nextstepaction"]
> [Szybki Start: sterowanie urządzeniem podłączonym do centrum IoT Hub](quickstart-control-device-java.md)
