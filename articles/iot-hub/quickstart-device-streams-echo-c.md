---
title: Komunikowanie się z aplikacją urządzenia w języku C za pomocą strumieni urządzeń usługi Azure IoT Hub
description: W tym przewodniku Szybki start uruchomisz aplikację po stronie urządzenia C, która komunikuje się z urządzeniem IoT za pośrednictwem strumienia urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 52f9e6529329c5bb1abb176082294dc26e64baa3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675541"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: komunikowanie się z aplikacją urządzenia w języku C za pośrednictwem strumieni urządzeń Usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Usługa Azure IoT Hub obsługuje obecnie strumienie urządzeń jako [funkcję w wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Podczas publicznej wersji zapoznawczej zestaw C SDK obsługuje strumienie urządzeń tylko po stronie urządzenia. W rezultacie ten szybki start obejmuje instrukcje uruchamiania tylko aplikacji po stronie urządzenia. Aby uruchomić odpowiednią aplikację po stronie usługi, zobacz następujące artykuły:

* [Komunikowanie się z aplikacjami urządzeń w języku C# za pośrednictwem strumieni urządzeń Usługi IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Komunikowanie się z aplikacjami urządzeń w pliku Node.js za pośrednictwem strumieni urządzeń usługi IoT Hub](./quickstart-device-streams-echo-nodejs.md)

Aplikacja C po stronie urządzenia w tym przewodniku Szybki start zawiera następujące funkcje:

* Tworzenie strumienia urządzenia do urządzenia IoT.

* Odbieraj dane, które są wysyłane z aplikacji po stronie usługi i echo go z powrotem.

Kod demonstruje proces inicjacji strumienia urządzenia, a także jak go używać do wysyłania i odbierania danych.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebne są następujące wymagania wstępne:

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) z włączonym programem Desktop Development z włączonym obciążeniem **c++.**

* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

* Uruchom następujące polecenie, aby dodać rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i IoT Device Provisioning Service (DPS) polecenia specyficzne dla interfejsu wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Podgląd strumieni urządzeń jest obecnie obsługiwany tylko dla centrów IoT, które są tworzone w następujących regionach:

  * Środkowe stany USA
  * Centralna amerykańska EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start można użyć [zestawu SDK urządzenia Usługi Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotować środowisko programistyczne używane do klonowania i tworzenia [zestawów SDK C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w usłudze GitHub zawiera przykładowy kod, który jest używany w tym przewodniku Szybki start.

   > [!NOTE]
   > Przed rozpoczęciem tej procedury upewnij się, że program Visual Studio jest zainstalowany z programem Desktop development z obciążeniem **języka C++.**

1. Zainstaluj [system kompilacji CMake](https://cmake.org/download/) zgodnie z opisem na stronie pobierania.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Uruchom następujące polecenia, aby sklonować repozytorium GitHub [SDK usługi Azure IoT C:](https://github.com/Azure/azure-iot-sdk-c)

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ta operacja powinna potrwać kilka minut.

1. Utwórz *podkatalog cmake* w katalogu głównym repozytorium git i przejdź do tego folderu. Uruchom następujące polecenia z katalogu *azure-iot-sdk-c:*

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia z katalogu *cmake,* aby utworzyć wersję zestawu SDK, która jest specyficzna dla platformy klienta deweloperskiego.

   * W systemie Linux:

      ```bash
      cmake ..
      make -j
      ```

   * W systemie Windows otwórz [wiersz polecenia dewelopera dla programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). Uruchom polecenie dla swojej wersji programu Visual Studio. Ten przewodnik Szybki start korzysta z programu Visual Studio 2019. Te polecenia tworzą rozwiązanie programu Visual Studio dla symulowanego urządzenia w katalogu *cmake.*

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Aby można było nawiązać połączenie, należy zarejestrować urządzenie w centrum IoT Hub. W tej sekcji używasz usługi Azure Cloud Shell z [rozszerzeniem IoT,](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) aby zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.
   > * W przypadku nazwy zarejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice* w sposób pokazany na rysunku. Jeśli wybierzesz inną nazwę urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *parametry połączenia urządzenia* dla właśnie zarejestrowanego urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zanotuj zwrócony ciąg połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikacja między urządzeniem a usługą za pośrednictwem strumieni urządzeń

W tej sekcji należy uruchomić zarówno aplikacji po stronie urządzenia i aplikacji po stronie usługi i komunikować się między nimi.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Aby uruchomić aplikację po stronie urządzenia, wykonaj następujące kroki:

1. Podaj poświadczenia urządzenia, edytując plik źródłowy `iothub_client/samples/iothub_client_c2d_streaming_sample` **iothub_client_c2d_streaming_sample.c** w folderze i dodając parametry połączenia urządzenia.

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. Skompiluj kod za pomocą następujących poleceń:

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

1. Uruchom skompilowany program:

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

Jak wspomniano wcześniej, zestaw SDK języka C usługi IoT Hub obsługuje strumienie urządzeń tylko po stronie urządzenia. Aby utworzyć i uruchomić towarzyszącą aplikację po stronie usługi, postępuj zgodnie z instrukcjami w jednym z następujących przewodników Szybki start:

* [Komunikowanie się z aplikacją urządzenia w języku C# za pośrednictwem strumieni urządzeń usługi IoT Hub](./quickstart-device-streams-echo-csharp.md)

* [Komunikowanie się z aplikacją urządzenia w pliku Node.js za pośrednictwem strumieni urządzeń usługi IoT Hub](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT hub, zarejestrowano urządzenie, ustanowiono strumień urządzenia między aplikacją C na urządzeniu a inną aplikacją po stronie usługi i użyto strumienia do wysyłania danych między aplikacjami.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
