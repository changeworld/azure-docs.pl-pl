---
title: Strumienie urządzeń usługi Azure IoT Hub w języku C — Szybki start dla protokołu SSH/RDP (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz przykładową aplikację C, która działa jako serwer proxy obsługujący protokół SSH/RDP w strumieniach urządzeń usługi IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1468268e407eeac6196c8e8e4db0fc5a52ca09c7
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501573"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Szybki start: Obsługa protokołu SSH/RDP w strumieniach urządzeń usługi IoT Hub za pomocą aplikacji serwera proxy C (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Omówienie konfiguracji można znaleźć na [tej stronie](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

W tym dokumencie opisano konfigurację tunelowania ruchu SSH (przy użyciu portu 22) przez strumienie urządzeń. Konfiguracja ruchu RDP przebiega podobnie i wymaga prostej zmiany konfiguracji. Strumienie urządzeń nie wymagają protokołu i aplikacji, dlatego można zmodyfikować obecny przewodnik Szybki start (przez zmianę portów komunikacyjnych) w celu obsługi innych typów ruchu aplikacji.

## <a name="how-it-works"></a>Jak to działa?

Na poniższej ilustracji pokazano instalacji jak programy proxy lokalnych urządzeń i usług umożliwi end-to-end łączności między klientem SSH i procesy demona SSH. W publicznej wersji zapoznawczej zestaw C SDK obsługuje tylko strumienie urządzeń po stronie urządzenia. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania aplikacji serwera proxy urządzenia lokalnego. Musisz uruchomić towarzyszącą aplikację serwera proxy usługi lokalnej, która jest dostępna w przewodniku [Szybki start języka C#](./quickstart-device-streams-proxy-csharp.md) lub [Szybki start środowiska Node.js](./quickstart-device-streams-proxy-nodejs.md).

![Tekst alternatywny](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "Konfiguracja lokalnego serwera proxy")

1. Serwer proxy usługi lokalnej łączy się z usługą IoT Hub i inicjuje strumień urządzenia do urządzenia docelowego.

2. Serwer proxy urządzenia lokalnego kończy uzgadnianie inicjowania strumienia i tworzy pełny tunel przesyłania strumieniowego przez punkt końcowy przesyłania strumieniowego usługi IoT Hub do usługi.

3. Serwer proxy urządzenia lokalnego łączy się z demonem SSH (SSHD), który nasłuchuje na porcie 22 w urządzeniu (można to skonfigurować zgodnie z opisem [poniżej](#run-the device-local-proxy-application)).

4. Serwer proxy usługi lokalnej czeka na nowe połączenia SSH od użytkownika przez nasłuchiwanie na wyznaczonym porcie — w tym przypadku jest to port 2222 (również można to skonfigurować zgodnie z opisem [poniżej](#run-the-device-local-proxy-application)). Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunel umożliwia przesyłanie ruchu aplikacji SSH między programami klienta i serwera SSH.

> [!NOTE]
> Ruch SSH wysyłany w strumieniu urządzenia będzie tunelowany za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT Hub, a nie wysyłany bezpośrednio z usługi do urządzenia. Daje to [następujące korzyści](./iot-hub-device-streams-overview.md#benefits). Ponadto na ilustracji przedstawiono demona SSH uruchomionego w tym samym urządzeniu (lub tej samej maszynie) jako serwer proxy urządzenia lokalnego. W tym przewodniku Szybki start założono, że adres IP demona SSH umożliwia uruchamianie demona i serwera proxy urządzenia lokalnego również na różnych maszynach.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT Hub są tworzone w następujących regionach:

  * **Środkowe stany USA**
  * **Central US EUAP**

* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).
* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start będziesz używać [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotujesz środowisko deweloperskie używane do sklonowania i opracowania [zestawu SDK usługi Azure IoT dla języka C](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start. 

1. Pobierz [system kompilacji CMake](https://cmake.org/download/).

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    Należy się spodziewać, że ukończenie operacji potrwa kilka minut.

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

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj nazwy MyDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla swojego urządzenia, musisz również używać tej nazwy w tym artykule oraz zaktualizować nazwę urządzenia w przykładowych aplikacjach przed ich uruchomieniem.

    ```azurecli-interactive
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

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

1. Edytuj plik źródłowy `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` i Podaj parametry połączenia urządzenia, urządzenia docelowego adresu IP/nazwa hosta i port 22 protokołu SSH:

   ```C
   /* Paste in the your iothub connection string  */
   static const char* connectionString = "[Connection string of IoT Hub]";
   static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

2. Skompilować przykład:

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

3. Uruchom skompilowany program w urządzeniu:

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

Zgodnie z opisem [wcześniej](#how-it-works), ustanawiania strumień end-to-end do tunelowania ruchu SSH wymaga lokalnego serwera proxy na każdym końcu (zarówno na usługę i urządzenia). W publicznej wersji zapoznawczej zestawu SDK C usługi IoT Hub obsługuje strumienie urządzenia tylko po stronie urządzenia. Aby skompilować i uruchomić lokalnej usługi Serwer proxy, wykonaj kroki, które są dostępne w [ C# Szybki Start](./quickstart-device-streams-proxy-csharp.md) lub [szybkiego startu Node.js](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Tworzenie sesji SSH

Po uruchomieniu proxy lokalnych urządzeń i usług za pomocą programu klienta SSH, a następnie nawiązać połączenie z lokalnej usługi Serwer proxy do portu 2222 (zamiast demon SSH bezpośrednio).

```cmd/sh
ssh <username>@localhost -p 2222
```

W tym momencie zostanie wyświetlony monit logowania protokołu SSH o wprowadzenie poświadczeń.

Dane wyjściowe konsoli na serwerze proxy urządzenia lokalnego, który łączy się z demonem SSH pod adresem `IP_address:22`: ![Tekst alternatywny](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "Dane wyjściowe serwera proxy urządzenia lokalnego")

Dane wyjściowe programu klienta SSH konsoli (klient SSH komunikuje się demon SSH, łącząc się z portu 22, którego nasłuchuje serwer proxy usługi lokalnej): ![Tekst alternatywny](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "Dane wyjściowe klienta SSH")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT Hub, zarejestrowano urządzenie, wdrożono program serwera proxy urządzenia lokalnego i usługi lokalnej w celu utworzenia strumienia urządzenia za pomocą usługi IoT Hub oraz użyto serwerów proxy do tunelowania ruchu SSH.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Przegląd strumieni urządzenia](./iot-hub-device-streams-overview.md)
