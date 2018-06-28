---
title: Kontrolowanie urządzenia z poziomu usługi Azure IoT Hub — Szybki start (Java) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje Java. Jedna z aplikacji to aplikacja zaplecza, która może zdalnie kontrolować urządzenia podłączone do centrum. Druga z aplikacji symuluje urządzenie podłączone do centrum, które można kontrolować zdalnie.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/22/2018
ms.author: dobett
ms.openlocfilehash: 5da4248f0b0a72c3614b4c3e5ea042c4341f4e03
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330547"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-java"></a>Szybki start: kontrolowanie urządzenia podłączonego do centrum IoT (Java)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury i zarządzanie urządzeniami z poziomu chmury. W tym przewodniku Szybki start użyjesz *metody bezpośredniej* do kontrolowania urządzenia symulowanego podłączonego do centrum IoT. Możesz użyć metod bezpośrednich, aby zdalnie zmieniać zachowanie urządzenia podłączonego do centrum IoT.

Przewodnik Szybki start używa dwóch wstępnie napisanych aplikacji Java:

* Aplikacja urządzenia symulowanego, która reaguje na metody bezpośrednie wywoływane z aplikacji zaplecza. Aby odbierać wywołania metod bezpośrednich, ta aplikacja łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT.
* Aplikacja zaplecza, która wywołuje metody bezpośrednie na urządzeniu symulowanym. Aby wywoływać metody bezpośrednie na urządzeniu, ta aplikacja łączy się z punktem końcowym po stronie usługi w centrum IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku Java. Potrzebujesz środowiska Java w wersji SE 8 lub nowszej na komputerze deweloperskim.

Możesz pobrać środowisko Java dla wielu platform ze strony [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).

Możesz sprawdzić bieżącą wersję języka Java na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
java --version
```

Aby utworzyć przykłady, musisz zainstalować narzędzie Maven 3. Możesz pobrać narzędzie Maven dla wielu platform ze strony [Apache Maven](https://maven.apache.org/download.cgi).

Możesz sprawdzić bieżącą wersję narzędzia Maven na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
mvn --version
```

Jeśli nie zostało to jeszcze zrobione, pobierz przykładowy projekt Java z https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip i wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-java.md), możesz pominąć ten krok.

[!INCLUDE [iot-hub-quickstarts-create-hub](../../includes/iot-hub-quickstarts-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Jeśli ukończono poprzedni przewodnik [Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-java.md), możesz pominąć ten krok.

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start urządzenie symulowane jest rejestrowane przy użyciu interfejsu wiersza polecenia platformy Azure.

1. Dodaj rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utwórz tożsamość urządzenia. Zastąp ciąg `{YourIoTHubName}` nazwą wybraną dla centrum IoT:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyJavaDevice
    ```

    Jeśli wybierzesz inną nazwę dla swojego urządzenia, zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

2. Uruchom następujące polecenie, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyJavaDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco: `Hostname=...=`. Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="retrieve-the-service-connection-string"></a>Pobieranie parametrów połączenia usługi

Potrzebne będą także _parametry połączenia usługi_ centrum IoT, aby umożliwić aplikacji zaplecza nawiązywanie połączenia z centrum i pobieranie komunikatów. Następujące polecenie pobiera parametry połączenia usługi dla centrum IoT:

```azurecli-interactive
az iot hub show-connection-string --hub-name {YourIoTHubName} --output table
```

Zanotuj parametry połączenia usługi, które wyglądają następująco: `Hostname=...=`. Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="listen-for-direct-method-calls"></a>Nasłuchiwanie wywołań metod bezpośrednich

Aplikacja urządzenia symulowanego łączy się z punktem końcowym właściwym dla urządzenia w centrum IoT, wysyła symulowane dane telemetryczne i nasłuchuje wywołań metod bezpośrednich z centrum. W tym przewodniku Szybki start wywołanie metody bezpośredniej z centrum nakazuje urządzeniu zmienić interwał wysyłania danych telemetrycznych. Urządzenie symulowane wysyła potwierdzenie z powrotem do centrum po wykonaniu metody bezpośredniej.

1. W oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\simulated-device-2**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `connString` parametrami połączenia urządzenia zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **SimulatedDevice.java**.

3. W oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i utworzyć aplikację urządzenia symulowanego:

    ```cmd/sh
    mvn clean package
    ```

4. W oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-control-device-java/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Wywoływanie metody bezpośredniej

Aplikacja zaplecza łączy się z punktem końcowym po stronie usługi w usłudze IoT Hub. Aplikacja wykonuje wywołania metod bezpośrednich do urządzenia za pośrednictwem centrum IoT oraz nasłuchuje potwierdzeń. Aplikacja zaplecza usługi IoT Hub zwykle działa w chmurze.

1. W innym oknie terminalu przejdź do folderu głównego przykładowego projektu Java. Następnie przejdź do folderu **iot-hub\Quickstarts\back-end-application**.

2. Otwórz plik **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** w wybranym edytorze tekstów.

    Zastąp wartość zmiennej `iotHubConnectionString` parametrami połączenia usługi zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **BackEndApplication.java**.

3. W oknie terminalu uruchom następujące polecenia, aby zainstalować wymagane biblioteki i utworzyć aplikację zaplecza:

    ```cmd/sh
    mvn clean package
    ```

4. W oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację zaplecza:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w sytuacji, w której aplikacja wykonuje wywołanie metody bezpośredniej do urządzenia i odbiera potwierdzenie:

    ![Uruchamianie aplikacji zaplecza](media/quickstart-control-device-java/BackEndApplication.png)

    Po uruchomieniu aplikacji zaplecza zobaczysz komunikat w oknie konsoli uruchomionym na urządzeniu symulowanym, a także zobaczysz, że zmienia się prędkość wysyłania komunikatów:

    ![Zmiana w kliencie symulowanym](media/quickstart-control-device-java/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wywołano metodę bezpośrednią na urządzeniu z aplikacji zaplecza oraz odpowiedziano na wywołanie metody bezpośredniej w aplikacji urządzenia symulowanego.

Aby dowiedzieć się, jak przekierowywać komunikaty urządzenie-chmura do innych miejsc docelowych w chmurze, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Samouczek: przekierowywanie danych telemetrycznych do innych punktów końcowych w celu przetwarzania](tutorial-routing.md)