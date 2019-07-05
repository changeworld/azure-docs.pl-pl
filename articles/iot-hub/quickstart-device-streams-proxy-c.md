---
title: Usługa IoT Hub urządzenia strumieni C szybkiego startu platformy Azure dla protokołu SSH i RDP (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start możesz uruchomić przykładowej C aplikacji, która działa jako serwer proxy w celu włączenia protokołu SSH i RDP scenariuszy za pośrednictwem usługi IoT Hub strumieni urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: b958711c498f0826f2a48d92d4892eb43ec8d18a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446077"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki start: Włącz protokół SSH i RDP przez strumień urządzenie usługi IoT Hub przy użyciu języka C aplikację serwera proxy (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Usługa Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Omówienie konfiguracji, zobacz [lokalny serwer Proxy przykładową stronę](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Ten przewodnik Szybki Start opisano instalację na potrzeby tunelowania ruchu protokołu Secure Shell (SSH) (Korzystanie z portu 22) za pośrednictwem urządzenia strumieni. Ustawienia dla ruchu protokołu RDP (Remote Desktop) są podobne i wymaga zmiany prostej konfiguracji. Ponieważ strumieni urządzenia — i protokołu — niezależnie od aplikacji, można zmodyfikować tego przewodnika Szybki Start, aby pomieścić inne rodzaje ruchu aplikacji.

## <a name="how-it-works"></a>Jak to działa

Na poniższym rysunku przedstawiono, jak włączyć end-to-end łączności między klientem SSH i procesy demona SSH przez programy proxy lokalnych urządzeń i usług. W publicznej wersji zapoznawczej zestawu SDK języka C obsługuje strumienie urządzenia tylko na stronie urządzenia. W wyniku tego przewodnika Szybki Start omówiono instrukcjami, aby uruchomić tylko urządzenie lokalne w serwera proxy aplikacji. Należy uruchomić jeden z następujących przewodników Szybki Start po stronie usługi:

* [SSH/RDP, za pośrednictwem usługi IoT Hub device strumieni, za pomocą C# serwera proxy](./quickstart-device-streams-proxy-csharp.md)
* [SSH/RDP, za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu serwera proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Ustawienia serwera proxy w lokalnych](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Serwer proxy usługi lokalnej łączy się z Centrum IoT i uruchamia strumienia urządzenia na urządzeniu docelowym.

2. Proxy urządzenia lokalnego kończy uzgadnianie inicjowania strumienia i ustanawia tunel przesyłania strumieniowego typu end-to-end za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT hub po stronie usługi.

3. Serwer proxy lokalne urządzenie łączy się demon SSH, który nasłuchuje na porcie 22 na urządzeniu. To ustawienie jest konfigurowane, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy urządzenia lokalnego".

4. Serwer proxy usługi lokalnej oczekuje na nowe połączenia SSH z użytkownikiem przez nasłuchiwanie na wyznaczonym porcie, w tym przypadku jest port 2222. To ustawienie jest konfigurowane, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy urządzenia lokalnego". Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunelu umożliwia ruch aplikacji SSH, należy dokonać między programami klient i serwer SSH.

> [!NOTE]
> Ruch SSH, który jest przesyłany przez strumień urządzeń jest tunel za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT hub, a nie wysyłane bezpośrednio między usługą i urządzeniem. Aby uzyskać więcej informacji, zobacz [korzyści z używania strumieni urządzenia usługi Iot Hub](iot-hub-device-streams-overview.md#benefits). Ponadto na rysunku przedstawiono demon SSH, który działa na tym samym urządzeniu (lub komputera) jako serwer proxy urządzenia lokalnego. W tym przewodniku Szybki Start podając adres IP demon SSH umożliwia proxy lokalnych urządzeń i demonów do uruchamiania na różnych maszynach również.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT, które są tworzone w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP

* Zainstaluj [Visual Studio 2019](https://www.visualstudio.com/vs/) z [programowanie aplikacji klasycznych przy użyciu C++ ](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) włączony pakiet roboczy.
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Dodaje rozszerzenia IOT, IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS)-konkretnych poleceń interfejsu wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki Start należy użyć [zestaw SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotuj Środowisko deweloperskie, używany do klonowania i twórz [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod, który jest używany w tym przewodniku Szybki Start.

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, wymagania wstępne programu Visual Studio (Visual Studio i *programowanie aplikacji klasycznych przy użyciu C++*  obciążenia) są zainstalowane na tym komputerze *przed* rozpocząć instalację narzędzia CMake. Po wymagania wstępne zostały spełnione i pliki do pobrania jest weryfikowana, można zainstalować system kompilacji CMake.

1. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):

    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Należy się spodziewać tej operacji zajmie kilka minut.

1. Tworzenie *cmake* podkatalogów w katalogu głównym repozytorium Git, jak pokazano w poniższym poleceniu i przejdź do tego folderu.

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Uruchom następujące polecenia z *cmake* katalogu, aby skompilować wersję zestawu SDK, które są specyficzne dla platformy klienta deweloperskiego.

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

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz usługi Azure Cloud Shell przy użyciu [rozszerzenia IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamości urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.
   > * Użyj *MyDevice*, jak pokazano. Jest to nazwa podana dla zarejestrowanych urządzeń. Jeśli wybierzesz inną nazwę dla danego urządzenia, używać tej nazwy, w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacji, można uruchomić.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Aby uzyskać *parametry połączenia urządzenia* dla urządzenia, które właśnie zostało zarejestrowane, uruchom następujące polecenia w usłudze Cloud Shell:

   > [!NOTE]
   > Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Należy pamiętać, parametry połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji możesz ustanowić strumień end-to-end do tunelowania ruchu protokołu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

1. Edytuj plik źródłowy *iothub_client_c2d_streaming_sample.c* w folderze *iothub_client/samples/iothub_client_c2d_streaming_sample*i Podaj parametry połączenia urządzenia, miejscem docelowym urządzenia IP/nazwa hosta i port 22 protokołu SSH:

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. Skompilować przykład:

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

Zgodnie z opisem w sekcji "Jak działa", ustanowienie strumień end-to-end do tunelowania ruchu SSH wymaga lokalnego serwera proxy na każdym końcu (w usłudze i stronach urządzenia). W publicznej wersji zapoznawczej zestawu SDK C usługi IoT Hub obsługuje strumienie urządzenia tylko na stronie urządzenia. Aby skompilować i uruchomić lokalnej usługi Serwer proxy, wykonaj instrukcje w jeden z następujących przewodników Szybki Start:

   * [SSH/RDP, za pośrednictwem usługi IoT Hub device strumieni, za pomocą C# aplikacje serwera proxy](./quickstart-device-streams-proxy-csharp.md)
   * [SSH/RDP, za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu aplikacji Node.js z serwera proxy](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>Tworzenie sesji SSH

Po uruchomieniu proxy lokalnych urządzeń i usług za pomocą programu klienta SSH, a następnie nawiązać połączenie z lokalnej usługi Serwer proxy do portu 2222 (zamiast demon SSH bezpośrednio).

```cmd/sh
ssh <username>@localhost -p 2222
```

W tym momencie okno logowania SSH wyświetli monit o wprowadzenie poświadczeń.

Na poniższej ilustracji przedstawiono dane wyjściowe konsoli na serwerze proxy lokalnego urządzenia i nawiązanie połączenia z demon SSH na `IP_address:22`:

![Dane wyjściowe urządzenia lokalnego serwera proxy](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Na poniższej ilustracji przedstawiono dane wyjściowe z konsoli programu klienta SSH. Klient SSH komunikuje się demon SSH łącząc się z portu 22, którego nasłuchuje serwer proxy usługi lokalnej:

![Dane wyjściowe z klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start zostało Konfigurowanie usługi IoT hub, zarejestrowane urządzenia, wdrożonych urządzeń — i programu lokalnej usługi serwera proxy do ustanowienia strumienia urządzenia za pomocą usługi IoT Hub i używane serwery proxy do tunelowania ruchu protokołu SSH.

Aby dowiedzieć się więcej na temat urządzeń, strumieni, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
