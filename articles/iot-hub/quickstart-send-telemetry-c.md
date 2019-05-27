---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (C) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka C wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 92575f2fc8e6dbcfc5767a179ddf60df1bce0c83
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872598"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT oraz odczytywanie ich przy użyciu aplikacji zaplecza (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

W tym przewodniku Szybki start do wysyłania danych telemetrycznych do centrum IoT służy przykładowa aplikacja w języku C z [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Zestawy SDK urządzeń Azure IoT są zapisywane w formacie [ANSI C (C99)](https://wikipedia.org/wiki/C99) zapewniającym przenośność i zgodność na wielu platformach. Zanim uruchomisz przykładowy kod, utworzysz centrum IoT i zarejestrujesz w nim urządzenie symulowane.

Ten artykuł został napisany z myślą o systemie Windows, ale ten przewodnik Szybki start możesz również ukończyć w systemie Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Visual Studio 2019](https://www.visualstudio.com/vs/) z ["programowanie aplikacji klasycznych przy użyciu C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) włączony pakiet roboczy.
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).
* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki Start będziesz korzystać z [zestaw SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). 

Zestawu SDK możesz używać, instalując pakiety i biblioteki dla następujących środowisk:

* **Linux**: polecenia apt-get pakiety są dostępne dla Ubuntu 16.04 i 18.04 przy użyciu następujące architektury Procesora: amd64, arm64, armhf i i386. Aby uzyskać więcej informacji, zobacz [Tworzenie projektu klienta urządzenia w języku C przy użyciu pakietu apt-get w systemie Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**. Dla deweloperów, tworzenie aplikacji urządzenie na platformie mbed opublikowaliśmy, biblioteki i przykłady, które umożliwią Ci rozpoczęcie pracy w ciągu kilku minut za pomocą usługi Azure IoT Hub. Aby uzyskać więcej informacji, zobacz [Korzystanie z biblioteki mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**. Jeśli tworzysz na Arduino, mogą korzystać z biblioteki usługi Azure IoT, dostępne w środowisku IDE Arduino library manager. Aby uzyskać więcej informacji, zobacz [Biblioteka usługi Azure IoT Hub dla środowiska Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**. Zestaw SDK urządzeń centrum IoT na potrzeby programowania urządzeń na komputerach Mac i w systemie iOS jest dostępny w postaci aplikacji CocoaPods. Aby uzyskać więcej informacji, zobacz [Przykłady z systemu iOS dotyczące usługi Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

Jednak w tym przewodniku Szybki Start umożliwią przygotowanie środowiska programowania, używany do klonowania i twórz [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start. 

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, wymagania wstępne programu Visual Studio (Visual Studio i "programowanie aplikacji klasycznych przy użyciu C++" obciążenia) są zainstalowane na tym komputerze **przed** od `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.


3. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Uruchom poniższe polecenie, które utworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`. 

    ```cmd
    cmake ..
    ```
    
    Jeśli `cmake` nie może znaleźć Twoje C++ kompilatora, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Gdy kompilacja zakończy się powodzeniem, kilka ostatnich wierszy danych wyjściowych będzie wyglądać podobnie do następujących danych wyjściowych:

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


## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz usługi Azure Cloud Shell przy użyciu [rozszerzenia IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyCDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj MyCDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla danego urządzenia, również należy używać tej nazwy, w tym artykule, a nazwa urządzenia w przykładowych aplikacji przed ich uruchomieniem aktualizacji.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla tego urządzenia została zarejestrowana:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="send-simulated-telemetry"></a>Wysyłanie symulowanych danych telemetrycznych

Aplikacja urządzenia symulowanego łączy się z punktem końcowym specyficznym dla urządzenia w centrum IoT i wysyła parametry połączenia jako symulowane dane telemetryczne.

1. Używając edytora tekstu, otwórz plik źródłowy iothub_convenience_sample.c i przejrzyj przykładowy kod umożliwiający wysyłanie danych telemetrycznych. Plik znajduje się w następującej lokalizacji:

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Znajdź deklarację stałej `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    Zastąp wartość stałej `connectionString` parametrami połączenia urządzenia zanotowanymi w poprzednim kroku. Następnie zapisz zmiany w pliku **iothub_convenience_sample.c**.

3. W lokalnym oknie terminalu przejdź do katalogu projektu *iothub_convenience_sample* w katalogu narzędzia CMake, który został utworzony w zestawie SDK usługi Azure IoT dla języka C.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Uruchom narzędzie CMake w lokalnym oknie terminalu, aby skompilować przykład za pomocą zaktualizowanej wartości `connectionString`:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. W lokalnym oknie terminalu uruchom następujące polecenia, aby uruchomić aplikację urządzenia symulowanego:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Poniższy zrzut ekranu przedstawia dane wyjściowe w momencie wysyłania przez aplikację urządzenia symulowanego danych telemetrycznych do centrum IoT:

    ![Uruchamianie urządzenia symulowanego](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Odczytywanie danych telemetrycznych z centrum


W tej sekcji użyjesz usługi Azure Cloud Shell przy użyciu [rozszerzenia IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) do monitorowania komunikatów wysyłanych z urządzenia, które są wysyłane przez urządzenie symulowane.

1. Używając usługi Azure Cloud Shell, uruchom następujące polecenie, aby nawiązać połączenie i odczytać komunikaty z centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Czytanie komunikatów urządzenia za pomocą interfejsu wiersza polecenia platformy Azure](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT, zarejestrowano urządzenie, wysłano dane telemetryczne do centrum przy użyciu aplikacji C oraz odczytano dane telemetryczne z centrum przy użyciu usługi Azure Cloud Shell.

Aby dowiedzieć się więcej na temat programowania za pomocą zestawu SDK usługi Azure IoT Hub dla języka C, przejdź do następującego przewodnika z instrukcjami:

> [!div class="nextstepaction"]
> [Programowanie przy użyciu zestawu SDK usługi Azure IoT Hub dla języka C](iot-hub-devguide-develop-for-constrained-devices.md)