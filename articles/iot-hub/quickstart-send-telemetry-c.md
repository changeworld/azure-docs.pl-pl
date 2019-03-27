---
title: Wysyłanie danych telemetrycznych do usługi Azure IoT Hub — Szybki start (C) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka C wysyłające symulowane dane telemetryczne do centrum IoT oraz odczytujące dane telemetryczne z centrum IoT na potrzeby przetwarzania w chmurze.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: wesmc
ms.openlocfilehash: be8418059ae80b6bcc4c86c677491b5fc9a27e12
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58481862"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Szybki start: wysyłanie danych telemetrycznych z urządzenia do centrum IoT oraz odczytywanie ich przy użyciu aplikacji zaplecza (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub to usługa platformy Azure, która umożliwia pozyskiwanie dużych ilości danych telemetrycznych z urządzeń IoT do chmury w celu magazynowania lub przetwarzania. W tym przewodniku Szybki start wyślesz dane telemetryczne z aplikacji urządzenia symulowanego za pośrednictwem usługi IoT Hub do aplikacji zaplecza w celu ich przetworzenia.

W tym przewodniku Szybki start do wysyłania danych telemetrycznych do centrum IoT służy przykładowa aplikacja w języku C z [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Zestawy SDK urządzeń Azure IoT są zapisywane w formacie [ANSI C (C99)](https://wikipedia.org/wiki/C99) zapewniającym przenośność i zgodność na wielu platformach. Zanim uruchomisz przykładowy kod, utworzysz centrum IoT i zarejestrujesz w nim urządzenie symulowane.

Ten artykuł został napisany z myślą o systemie Windows, ale ten przewodnik Szybki start możesz również ukończyć w systemie Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start będziesz używać [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). 

Zestawu SDK możesz używać, instalując pakiety i biblioteki dla następujących środowisk:

* **Linux**. Pakiety apt-get są dostępne dla systemów Ubuntu 16.04 i 18.04 korzystających z następujących architektur procesora: amd64, arm64, armhf oraz i386. Aby uzyskać więcej informacji, zobacz [Tworzenie projektu klienta urządzenia w języku C przy użyciu pakietu apt-get w systemie Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**. Dla deweloperów tworzących aplikacje urządzeń na platformie mbed opublikowaliśmy bibliotekę i przykłady, dzięki którym rozpoczniesz pracę z usługą Azure IoT Hub w ciągu kilku minut. Aby uzyskać więcej informacji, zobacz [Korzystanie z biblioteki mbed](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**. Jeśli programujesz w środowisku Arduino, możesz korzystać z biblioteki usługi Azure IoT dostępnej w menedżerze bibliotek środowiska IDE Arduino. Aby uzyskać więcej informacji, zobacz [Biblioteka usługi Azure IoT Hub dla środowiska Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**. Zestaw SDK urządzeń centrum IoT na potrzeby programowania urządzeń na komputerach Mac i w systemie iOS jest dostępny w postaci aplikacji CocoaPods. Aby uzyskać więcej informacji, zobacz [Przykłady z systemu iOS dotyczące usługi Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

Jednak w tym przewodniku Szybki start przygotujesz środowisko deweloperskie używane do sklonowania i opracowania [zestawu SDK usługi Azure IoT dla języka C](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start. 

1. Pobierz [system kompilacji CMake](https://cmake.org/download/). Sprawdź pobrane przy użyciu wartości skrótu kryptograficznego, który odpowiada wersji, którą można pobrać pliku binarnego. Wartości skrótu kryptograficznego znajdują się również z już podany link pobierania narzędzia CMake.

    Poniższy przykład używany programu Windows PowerShell do sprawdzenia, kryptograficzne wartości skrótu dla wersji 3.13.4 x64 dystrybucji MSI:

    ```powershell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.13.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "64AC7DD5411B48C2717E15738B83EA0D4347CD51B940487DFF7F99A870656C09"
    True
    ```

    Następujące wartości skrótu dla wersji 3.13.4 były wymienione w witrynie narzędzia CMake w momencie pisania tego dokumentu:

    ```
    563a39e0a7c7368f81bfa1c3aff8b590a0617cdfe51177ddc808f66cc0866c76  cmake-3.13.4-Linux-x86_64.tar.gz
    7c37235ece6ce85aab2ce169106e0e729504ad64707d56e4dbfc982cb4263847  cmake-3.13.4-win32-x86.msi
    64ac7dd5411b48c2717e15738b83ea0d4347cd51b940487dff7f99a870656c09  cmake-3.13.4-win64-x64.msi
    ```

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Rozmiar tego repozytorium wynosi obecnie około 220 MB. Należy się spodziewać, że ukończenie operacji potrwa kilka minut.


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
    
    Jeśli program `cmake` nie znajdzie kompilatora języka C++, mogą występować błędy kompilacji podczas uruchamiania powyższego polecenia. Jeśli tak się stanie, spróbuj uruchomić to polecenie w [wierszu polecenia programu Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji za pomocą usługi Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zarejestrujesz symulowane urządzenie.

1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby dodać rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utworzyć tożsamość urządzenia. 

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyCDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj MyCDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

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


W tej sekcji, używając usługi Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest), będziesz monitorować komunikaty urządzenia wysyłane przez urządzenie symulowane.

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