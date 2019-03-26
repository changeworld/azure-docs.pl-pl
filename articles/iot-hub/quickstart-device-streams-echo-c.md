---
title: Strumienie urządzeń usługi Azure IoT Hub w języku C — Szybki start (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz aplikację C po stronie usługi, która komunikuje się z urządzeniem IoT za pomocą strumienia urządzenia.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 026e072d6e5ac65d957b272300898de3dc9099a9
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438278"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: komunikacja z aplikacją urządzenia w języku C za pomocą strumieni urządzeń usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. W publicznej wersji zapoznawczej zestaw C SDK obsługuje tylko strumienie urządzeń po stronie urządzenia. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania aplikacji po stronie urządzenia. Należy uruchomić towarzyszący aplikacji po stronie usługi, który jest dostępny w [ C# Szybki Start](./quickstart-device-streams-echo-csharp.md) lub [szybkiego startu Node.js](./quickstart-device-streams-echo-nodejs.md).

Aplikacja C po stronie urządzenia w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.

* Odbieranie danych wysyłanych z usługi i wysyłanie z powrotem echa.

Kod zademonstruje proces inicjowania strumień urządzeń, a także jak z niej korzystać, aby wysyłać i odbierać dane.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT Hub są tworzone w następujących regionach:

  * **Środkowe stany USA**
  * **Central US EUAP**

* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start będziesz używać [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotujesz środowisko deweloperskie używane do sklonowania i opracowania [zestawu SDK usługi Azure IoT dla języka C](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start. 

1. Pobierz [system kompilacji CMake](https://cmake.org/download/). Sprawdź pobrane przy użyciu wartości skrótu kryptograficznego, który odpowiada wersji, którą można pobrać pliku binarnego. Wartości skrótu kryptograficznego znajdują się również z już podany link pobierania narzędzia CMake.

    Poniższy przykład używany programu Windows PowerShell do sprawdzenia, kryptograficzne wartości skrótu dla wersji 3.13.4 x64 dystrybucji MSI:

    ```PowerShell
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
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Rozmiar tego repozytorium wynosi obecnie około 220 MB.

3. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Uruchom następujące polecenia z `cmake` katalogu, aby skompilować wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego.

   * W systemie Linux:

      ```bash
      cmake ..
      make -j
      ```

   * W Windows uruchom następujące polecenia w wierszu polecenia dla deweloperów programu Visual Studio 2015 lub 2017. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu `cmake`.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji za pomocą usługi Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zarejestrujesz symulowane urządzenie.

1. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby dodać rozszerzenie interfejsu wiersza polecenia usługi IoT Hub i utworzyć tożsamość urządzenia. 

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj nazwy MyDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla urządzenia, które właśnie zostało zarejestrowane:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają jak w następującym przykładzie:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pomocą strumieni urządzeń

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Aby uruchomić aplikację po stronie urządzenia, wykonaj następujące czynności:

1. Podaj swoje poświadczenia urządzenia, edytując plik źródłowy `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` i podając parametry połączenia urządzenia.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[device connection string]";
   ```

2. Skompiluj kod w następujący sposób:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

3. Uruchom skompilowany program:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

Jak wspomniano wcześniej, zestawu SDK C usługi IoT Hub obsługuje tylko urządzenia strumieni po stronie urządzenia. Aby skompilować i uruchomić aplikację po stronie usługi, wykonaj kroki, które są dostępne w [ C# Szybki Start](./quickstart-device-streams-echo-csharp.md) lub [szybkiego startu Node.js](./quickstart-device-streams-echo-nodejs.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start mają Konfigurowanie usługi IoT hub, zarejestrowane urządzenia, ustanowione strumień urządzeń, między aplikacją C na urządzeniu i w innej aplikacji po stronie usługi i używane strumienia do przesyłania danych i z powrotem między aplikacjami.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)