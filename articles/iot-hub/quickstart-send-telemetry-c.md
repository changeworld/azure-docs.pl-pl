---
title: Wysyłanie danych telemetrycznych do platformy Azure IoT Hub szybki start (C) | Microsoft Docs
description: W tym przewodniku szybki start uruchomisz dwie przykładowe aplikacje C do wysyłania symulowanych danych telemetrycznych do centrum IoT i odczytywanie danych telemetrycznych z usługi IoT Hub na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 4ccfa45c56a7e59024ce0639f218861054e32395
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166939"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Szybki Start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub i odczytywanie ich z użyciem aplikacji zaplecza (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT w chmurze w celu przechowania lub przetworzenia. W tym przewodniku szybki start wyślesz dane telemetryczne z aplikacji symulowanego urządzenia za pośrednictwem IoT Hub do aplikacji zaplecza do przetwarzania.

Przewodnik Szybki Start używa przykładowej aplikacji C z [zestawu SDK urządzeń Azure IoT dla języka c](iot-hub-device-sdk-c-intro.md) , aby wysyłać dane telemetryczne do centrum IoT. Zestawy SDK urządzeń usługi Azure IoT są zapisywane w [standardzie ANSI C (C99)](https://wikipedia.org/wiki/C99) w celu przeportowania i zapewnienia zgodności z szeroką platformą. Przed uruchomieniem przykładowego kodu należy utworzyć Centrum IoT Hub i zarejestrować symulowane urządzenie w tym centrum.

Ten artykuł jest przeznaczony dla systemu Windows, ale można również wykonać ten przewodnik Szybki Start w systemie Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) z włączonym obciążeniem ["Programowanie aplikacji klasycznych C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .
* Zainstaluj najnowszą wersję usługi [git](https://git-scm.com/download/).
* Uruchom następujące polecenie, aby dodać rozszerzenie IoT Microsoft Azure dla interfejsu wiersza polecenia platformy Azure do wystąpienia Cloud Shell. Rozszerzenie IOT dodaje do interfejsu wiersza polecenia platformy Azure IoT Hub, IoT Edge i usługi IoT Device Provisioning Service (DPS).

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Przygotuj środowisko programistyczne

W tym przewodniku szybki start będziesz używać [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). 

W przypadku następujących środowisk można użyć zestawu SDK, instalując następujące pakiety i biblioteki:

* **Linux**: apt-get Packages są dostępne dla Ubuntu 16,04 i 18,04 przy użyciu następujących architektur procesora CPU: amd64, arm64, armhf i i386. Aby uzyskać więcej informacji, zobacz [używanie apt-get do tworzenia projektu klienta urządzenia C w Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: dla deweloperów tworzących aplikacje dla urządzeń na platformie mbed opublikowano bibliotekę i przykłady, które pomogą Ci rozpocząć pracę w ciągu kilku minut za pomocą usługi Azure IoT Hub. Aby uzyskać więcej informacji, zobacz [Korzystanie z biblioteki mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**.: Jeśli opracowujesz na Arduino, możesz skorzystać z biblioteki Azure IoT dostępnej w programie Arduino IDE Library Manager. Aby uzyskać więcej informacji, zobacz [biblioteka IoT Hub platformy Azure dla Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: zestaw SDK urządzeń IoT Hub jest dostępny jako CocoaPods dla komputerów Mac i iOS. Aby uzyskać więcej informacji, zobacz [przykłady dla systemu iOS dotyczące Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

Jednak w tym przewodniku szybki start utworzysz środowisko programistyczne służące do klonowania i kompilowania [zestawu SDK języka C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) w witrynie GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki Start.

1. Pobierz [system kompilacji CMAKE](https://cmake.org/download/).

    Przed rozpoczęciem instalacji `CMake` należy pamiętać, że wymagania wstępne programu Visual Studio ( C++Visual Studio i "Programowanie aplikacji klasycznych" ) są zainstalowane na maszynie. Gdy są spełnione wymagania wstępne, a pobieranie zostanie zweryfikowane, zainstaluj system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę git bash i przejdź do katalogu roboczego, w którym chcesz sklonować zestaw SDK usługi Azure IoT. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK usługi Azure IoT C](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    Należy się spodziewać, że wykonanie tej operacji zajmie kilka minut.

3. Utwórz podkatalog `cmake` w katalogu głównym repozytorium git i przejdź do tego folderu. Wprowadź następujące polecenia w katalogu roboczym:

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Uruchom następujące polecenie, aby skompilować wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`.

    ```cmd
    cmake ..
    ```

    Jeśli `cmake` nie może znaleźć C++ kompilatora, można uzyskać błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Po pomyślnym zakończeniu kompilacji, Ostatnia część wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>Tworzenie Centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, urządzenie musi zostać zarejestrowane w usłudze IoT Hub. W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) , aby zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w Azure Cloud Shell, aby utworzyć tożsamość urządzenia.

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

   **MyCDevice**: jest to nazwa urządzenia, które jest rejestrowany. Zaleca się użycie **MyCDevice** , jak pokazano. W przypadku wybrania innej nazwy dla urządzenia należy również użyć tej nazwy w tym artykule i zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. Uruchom następujące polecenie w Azure Cloud Shell, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    Ta wartość zostanie użyta w dalszej części przewodnika Szybki Start.

## <a name="send-simulated-telemetry"></a>Wyślij symulowane dane telemetryczne

Aplikacja symulowanego urządzenia nawiązuje połączenie z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła ciąg jako symulowaną telemetrię.

1. Za pomocą edytora tekstów Otwórz plik źródłowy iothub_convenience_sample. c i przejrzyj przykładowy kod służący do wysyłania telemetrii. Plik znajduje się w następującej lokalizacji w katalogu roboczym, w którym Sklonowano zestaw SDK języka C usługi Azure IoT:

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Znajdź deklarację stałej `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    Zastąp wartość stałej `connectionString` parametrami połączenia urządzenia, które zostały wykonane wcześniej. Następnie Zapisz zmiany w **iothub_convenience_sample. c**.

3. W lokalnym oknie terminalu przejdź do katalogu projektu *iothub_convenience_sample* w katalogu cmake, który został utworzony w zestawie SDK języka C usługi Azure IoT. Wprowadź następujące polecenie w katalogu roboczym:

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Uruchom CMake w oknie terminalu lokalnego, aby skompilować przykład za pomocą zaktualizowanej wartości `connectionString`:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. W oknie terminalu lokalnego Uruchom następujące polecenie, aby uruchomić aplikację symulowanego urządzenia:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w postaci symulowanej aplikacji urządzenia, która wysyła telemetrię do centrum IoT:

    ![Uruchamianie symulowanego urządzenia](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum

W tej sekcji użyjesz Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) do monitorowania komunikatów urządzeń wysyłanych przez symulowane urządzenie.

1. Korzystając z Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać wiadomości z Centrum IoT Hub:

   **YourIoTHubName**: Zastąp ten symbol zastępczy poniżej nazwą wybraną dla Centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![Odczytywanie komunikatów z urządzenia przy użyciu interfejsu wiersza polecenia platformy Azure](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start skonfigurujesz Centrum IoT, zarejestrowano urządzenie, wysłało symulowane dane telemetryczne do koncentratora przy użyciu aplikacji C i odczytuje dane telemetryczne z centrum przy użyciu Azure Cloud Shell.

Aby dowiedzieć się więcej na temat programowania przy użyciu zestawu Azure IoT Hub C SDK, przejdź do następującego przewodnika:

> [!div class="nextstepaction"]
> [Programowanie przy użyciu usługi Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)