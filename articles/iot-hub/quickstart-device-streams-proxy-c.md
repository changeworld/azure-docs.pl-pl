---
title: Urządzenie usługi Azure IoT Hub strumieniuje przewodnikA C dla usług SSH i RDP
description: W tym przewodniku Szybki start uruchom przykładową aplikację C, która działa jako serwer proxy, aby włączyć scenariusze SSH i RDP za pomocą strumieni urządzeń Usługi IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: af303928490d2570fa9430bd4b9d30c4a49df304
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675491"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki start: włączanie protokołu SSH i protokołu RDP za pośrednictwem strumienia urządzeń Usługi IoT Hub przy użyciu aplikacji proxy języka C (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Usługa Azure IoT Hub obsługuje obecnie strumienie urządzeń jako [funkcję w wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Aby zapoznać się z omówieniem konfiguracji, zobacz [stronę Przykładowy serwer proxy lokalnego](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Ten przewodnik Szybki start opisuje konfigurację tunelowania ruchu secure shell (SSH) (przy użyciu portu 22) za pośrednictwem strumieni urządzeń. Konfiguracja ruchu RDP (Remote Desktop Protocol) jest podobna i wymaga prostej zmiany konfiguracji. Ponieważ strumienie urządzeń są niezależne od aplikacji i protokołu, można zmodyfikować ten szybki start, aby uwzględnić inne typy ruchu aplikacji.

## <a name="how-it-works"></a>Jak to działa

Na poniższym rysunku przedstawiono, w jaki sposób lokalne programy proxy urządzenia i usługi umożliwiają łączność typu end-to-end między klientem SSH a procesami demonów SSH. Podczas publicznej wersji zapoznawczej zestaw C SDK obsługuje strumienie urządzeń tylko po stronie urządzenia. W rezultacie ten szybki start obejmuje instrukcje uruchamiania tylko aplikacji serwera proxy lokalnego urządzenia. Aby utworzyć i uruchomić towarzyszącą aplikację po stronie usługi, postępuj zgodnie z instrukcjami w jednym z następujących przewodników Szybki start:

* [SSH/RDP za pośrednictwem strumieni urządzeń Usługi IoT Hub przy użyciu serwera proxy języka C#](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP za pośrednictwem strumieni urządzeń Usługi IoT Hub przy użyciu serwera proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Konfiguracja lokalnego serwera proxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Lokalny serwer proxy usługi łączy się z centrum IoT hub i uruchamia strumień urządzenia do urządzenia docelowego.

2. Lokalny serwer proxy urządzenia kończy uzgadnianie inicjowania inicjowania strumienia i ustanawia tunel przesyłania strumieniowego end-to-end przez punkt końcowy przesyłania strumieniowego usługi IoT hub do strony usługi.

3. Serwer proxy lokalny dla urządzeń łączy się z demonem SSH, który nasłuchuje na porcie 22 urządzenia. To ustawienie można konfigurować, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy lokalnego urządzenia".

4. Serwer proxy lokalny usługi czeka na nowe połączenia SSH od użytkownika, nasłuchiwanie na wyznaczonym porcie, który w tym przypadku jest port 2222. To ustawienie można konfigurować, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy lokalnego urządzenia". Gdy użytkownik łączy się za pośrednictwem klienta SSH, tunel umożliwia ruch aplikacji SSH, które mają być przesyłane między klientem SSH i programów serwera.

> [!NOTE]
> Ruch SSH, który jest wysyłany za pośrednictwem strumienia urządzenia jest tunelowany przez punkt końcowy przesyłania strumieniowego usługi IoT hub, a nie wysyłany bezpośrednio między usługą a urządzeniem. Aby uzyskać więcej informacji, zobacz [zalety korzystania ze strumieni urządzeń usługi Iot Hub](iot-hub-device-streams-overview.md#benefits). Ponadto rysunek ilustruje demonA SSH, który działa na tym samym urządzeniu (lub komputerze) co serwer proxy lokalnego urządzenia. W tym przewodniku Szybki start podanie adresu IP demona SSH umożliwia uruchamianie lokalnego serwera proxy urządzenia i demona również na różnych komputerach.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko dla centrów IoT, które są tworzone w następujących regionach:

  * Środkowe stany USA
  * Centralna amerykańska EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

* Zainstaluj [program Visual Studio 2019](https://www.visualstudio.com/vs/) z włączonym programem Desktop Development z włączonym obciążeniem [c++.](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

* Uruchom następujące polecenie, aby dodać rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i IoT Device Provisioning Service (DPS) polecenia specyficzne dla interfejsu wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start można użyć [zestawu SDK urządzenia Usługi Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotować środowisko programistyczne używane do klonowania i tworzenia [zestawów SDK C usługi Azure IoT](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w usłudze GitHub zawiera przykładowy kod, który jest używany w tym przewodniku Szybki start.

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, aby wymagania wstępne programu Visual Studio (Visual Studio i program rozwoju pulpitu z obciążeniem *języka C++)* są zainstalowane na komputerze, *przed* rozpoczęciem instalacji CMake. Po wytyczek wstępnych są w miejscu i pobieranie jest weryfikowany, można zainstalować CMake systemu kompilacji.

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

   * W systemie Windows uruchom następujące polecenia w wierszu polecenia dewelopera dla programu Visual Studio 2015 lub 2017. Rozwiązanie programu Visual Studio dla symulowanego urządzenia zostanie wygenerowane w katalogu *cmake.*

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

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji używasz usługi Azure Cloud Shell z [rozszerzeniem IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) do rejestrowania symulowanego urządzenia.

1. Aby utworzyć tożsamość urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.
   > * W przypadku nazwy zarejestrowanego urządzenia zaleca się użycie urządzenia *MyDevice* w sposób pokazany na rysunku. Jeśli wybierzesz inną nazwę urządzenia, użyj tej nazwy w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Aby uzyskać *parametry połączenia urządzenia* dla właśnie zarejestrowanego urządzenia, uruchom następujące polecenia w usłudze Cloud Shell:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Zanotuj zwrócony ciąg połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji ustanawiasz strumień end-to-end do tunelu ruchu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

1. Edytuj plik źródłowy **iothub_client_c2d_streaming_proxy_sample.c** w `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample`folderze i podaj parametry połączenia urządzenia, docelową nazwę IP/host urządzenia docelowego oraz port SSH 22:

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Skompiluj przykład:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. Uruchom skompilowany program w urządzeniu:

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

Jak wspomniano w sekcji "Jak to działa", ustanowienie strumienia end-to-end do tunelu ruchu SSH wymaga lokalnego serwera proxy na każdym końcu (zarówno po stronie usługi, jak i urządzenia). Podczas publicznej wersji zapoznawczej zestaw SDK języka C usługi IoT Hub obsługuje strumienie urządzeń tylko po stronie urządzenia. Aby utworzyć i uruchomić lokalny serwer proxy usługi, postępuj zgodnie z instrukcjami w jednym z następujących przewodników Szybki start:

   * [SSH/RDP za pośrednictwem strumieni urządzeń Usługi IoT Hub przy użyciu aplikacji proxy języka C#](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP za pośrednictwem strumieni urządzeń Usługi IoT Hub przy użyciu aplikacji proxy Node.js](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Tworzenie sesji SSH

Po uruchomieniu serwerów proxy lokalnego urządzenia i usługi użyj programu klienckiego SSH i połącz się z lokalnym serwerem proxy usługi na porcie 2222 (zamiast bezpośrednio demona SSH).

```cmd/sh
ssh {username}@localhost -p 2222
```

W tym momencie okno logowania SSH monituje o wprowadzenie poświadczeń.

Na poniższej ilustracji przedstawiono wyjście konsoli na lokalnym serwerze proxy urządzenia, który łączy się z demonem SSH pod adresem: `IP_address:22`

![Lokalne dane wyjściowe serwera proxy urządzenia](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli programu klienckiego SSH. Klient SSH komunikuje się z demonem SSH, łącząc się z portem 22, na który nasłuchuje lokalny serwer proxy usługi:

![Dane wyjściowe klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT hub, zarejestrowano urządzenie, wdrożono program proxy device- i service-local proxy w celu ustanowienia strumienia urządzenia za pośrednictwem usługi IoT Hub i użyto serwerów proxy do tunelowania ruchu SSH.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
