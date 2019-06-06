---
title: Przekazywać aplikacji urządzenia w języku C za pomocą strumieni urządzenia Azure IoT Hub (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start uruchomieniu aplikacji po stronie urządzenia C, która komunikuje się z urządzeń IoT za pomocą strumienia urządzenia.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 6a2fe71a1086559d90adf5c464323f353be431aa
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733306"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: komunikacja z aplikacją urządzenia w języku C za pomocą strumieni urządzeń usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Usługa Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. W publicznej wersji zapoznawczej zestawu SDK języka C obsługuje strumienie urządzenia tylko na stronie urządzenia. W wyniku tego przewodnika Szybki Start opisano instrukcje do uruchamiania tylko aplikacji po stronie urządzenia. Aby uruchomić aplikację po stronie usługi towarzyszącego, zobacz:
 
   * [Przekazywać aplikacji dla urządzeń w C# za pośrednictwem strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-csharp.md)
   * [Przekazywać aplikacji dla urządzeń w środowisku Node.js za pomocą strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-nodejs.md)

Aplikacja C po stronie urządzenia w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.
* Odbieranie danych, które są wysyłane z aplikacji po stronie usługi i echo je z powrotem.

Kod przedstawia proces inicjowania strumień urządzeń, a także jak z niej korzystać, aby wysyłać i odbierać dane.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT, które są tworzone w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP

* Zainstaluj [programu Visual Studio 2017](https://www.visualstudio.com/vs/) z [programowanie aplikacji klasycznych przy użyciu C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) włączony pakiet roboczy.

* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Dodaje rozszerzenia IOT, IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS)-konkretnych poleceń interfejsu wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki Start należy użyć [zestaw SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotuj Środowisko deweloperskie, używany do klonowania i twórz [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod, który jest używany w tym przewodniku Szybki Start.

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Przed zainstalowaniem programu CMake, ważne jest, wymagania wstępne programu Visual Studio (Visual Studio i *programowanie aplikacji klasycznych przy użyciu C++*  obciążenia) są zainstalowane na tym komputerze. Po wymagania wstępne zostały spełnione i zweryfikowaniu pliki do pobrania, można zainstalować system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Ta operacja powinna zająć kilka minut.

3. Tworzenie *cmake* podkatalogów w katalogu głównym repozytorium Git, jak pokazano w poniższym poleceniu, a następnie przejdź do tego folderu.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Uruchom następujące polecenia z *cmake* katalogu, aby skompilować wersję zestawu SDK, które są specyficzne dla platformy klienta deweloperskiego.

   * W systemie Linux:

      ```bash
      cmake ..
      make -j
      ```

   * W Windows uruchom następujące polecenia w wierszu polecenia dla deweloperów programu Visual Studio 2015 lub 2017. Rozwiązania programu Visual Studio dla symulowanego urządzenia, zostanie wygenerowany w *cmake* katalogu.

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Należy zarejestrować urządzenie z usługi IoT hub może nawiązać połączenie. W tej sekcji użyjesz usługi Azure Cloud Shell przy użyciu [rozszerzenia IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamości urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.
   > * Użyj *MyDevice*, jak pokazano. Jest to nazwa podana dla zarejestrowanych urządzeń. Jeśli wybierzesz inną nazwę dla danego urządzenia, używać tej nazwy, w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacji, można uruchomić.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Aby uzyskać *parametry połączenia urządzenia* dla urządzenia, które właśnie zostało zarejestrowane, uruchom następujące polecenia w usłudze Cloud Shell:

   > [!NOTE]
   > Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Należy pamiętać, parametry połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikację między urządzeniem i usługą za pomocą urządzeń, strumieni

W tej sekcji uruchamiania aplikacji po stronie urządzenia i aplikacji po stronie usługi i komunikować się między nimi.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Aby uruchomić aplikację po stronie urządzenia, wykonaj następujące czynności:

1. Podaj swoje poświadczenia urządzenia, edytując *iothub_client_c2d_streaming_sample.c* pliku źródłowego w *iothub_client/samples/iothub_client_c2d_streaming_sample* folder, a następnie przekaż Parametry połączenia urządzenia.

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

Jak wspomniano wcześniej, zestawu SDK C usługi IoT Hub obsługuje strumienie urządzenia tylko na stronie urządzenia. Aby skompilować i uruchomić aplikację po stronie usługi, postępuj zgodnie z instrukcjami w jednym z następujących przewodników Szybki Start:

* [Komunikować się z aplikacją urządzenia w C# za pośrednictwem strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-csharp.md)
* [Przekazywać aplikacji urządzenia w środowisku Node.js za pomocą strumieni urządzenia usługi IoT Hub](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start zostało Konfigurowanie usługi IoT hub, zarejestrowane urządzenia, ustanowione strumień urządzeń, między aplikacją C na urządzeniu i w innej aplikacji po stronie usługi i używane strumienia do przesyłania danych i z powrotem między aplikacjami.

Aby dowiedzieć się więcej na temat urządzeń, strumieni, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
