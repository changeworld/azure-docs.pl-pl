---
title: Strumienie urządzeń usługi Azure IoT Hub w języku C — Szybki start dla protokołu SSH/RDP (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku Szybki Start uruchomisz przykładową aplikację C, który działa jako serwer proxy, która umożliwia realizację scenariuszy protokołu RDP/SSH za pośrednictwem strumieni urządzenia usługi IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: ae5db52d7ac00080c2a740820debe6384cfa8dff
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872665"
---
# <a name="quickstart-sshrdp-over-an-iot-hub-device-stream-using-a-c-proxy-application-preview"></a>Szybki start: SSH/RDP przez strumień urządzenie usługi IoT Hub przy użyciu języka C aplikację serwera proxy (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Zobacz [lokalny serwer Proxy przykładową stronę](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp) Omówienie instalacji.

W tym dokumencie opisano konfigurację tunelowania ruchu SSH (przy użyciu portu 22) przez strumienie urządzeń. Konfiguracja ruchu RDP przebiega podobnie i wymaga prostej zmiany konfiguracji. Strumienie urządzeń nie wymagają protokołu i aplikacji, dlatego można zmodyfikować obecny przewodnik Szybki start (przez zmianę portów komunikacyjnych) w celu obsługi innych typów ruchu aplikacji.

## <a name="how-it-works"></a>Jak to działa

Na poniższej ilustracji pokazano instalacji jak programy proxy lokalnych urządzeń i usług umożliwi end-to-end łączności między klientem SSH i procesy demona SSH. W publicznej wersji zapoznawczej zestaw C SDK obsługuje tylko strumienie urządzeń po stronie urządzenia. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania aplikacji serwera proxy urządzenia lokalnego. Należy uruchomić jeden z następujących przewodników Szybki Start po stronie usługi:

* [SSH/RDP, za pośrednictwem usługi IoT Hub device strumieni, za pomocą C# serwera proxy](./quickstart-device-streams-proxy-csharp.md)

* [SSH/RDP, za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu serwera proxy NodeJS](./quickstart-device-streams-proxy-nodejs.md).

![Ustawienia serwera proxy w lokalnych](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Serwer proxy usługi lokalnej łączy się z Centrum IoT i uruchamia strumienia urządzenia na urządzeniu docelowym.

2. Serwer proxy urządzenia lokalnego kończy uzgadnianie inicjowania strumienia i tworzy pełny tunel przesyłania strumieniowego przez punkt końcowy przesyłania strumieniowego usługi IoT Hub do usługi.

3. Łączy się demon SSH (SSHD), który nasłuchuje na porcie 22 na urządzeniu z urządzenia lokalnego serwera proxy (to jest konfigurowalne, pozwalają zgodnie z opisem w [ *uruchamiania urządzenia lokalnego serwera proxy aplikacji* sekcji](#run-the device-local-proxy-application)).

4. Lokalne usługi Serwer proxy czeka na nowe połączenia SSH z użytkownika przez nasłuchiwanie na wyznaczonym porcie, w tym przypadku jest port 2222 (jest to również można skonfigurować, zgodnie z opisem w [przebiegu w sekcji aplikacji urządzenia lokalnego serwera proxy](#run-the-device-local-proxy-application). Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunel umożliwia przesyłanie ruchu aplikacji SSH między programami klienta i serwera SSH.

> [!NOTE]
> Ruch SSH wysyłany w strumieniu urządzenia będzie tunelowany za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT Hub, a nie wysyłany bezpośrednio z usługi do urządzenia. Aby uzyskać więcej informacji, przeczytaj o [korzyści z używania strumieni urządzenia usługi Iot Hub](iot-hub-device-streams-overview.md#benefits). Ponadto na ilustracji przedstawiono demona SSH uruchomionego w tym samym urządzeniu (lub tej samej maszynie) jako serwer proxy urządzenia lokalnego. W tym przewodniku Szybki start założono, że adres IP demona SSH umożliwia uruchamianie demona i serwera proxy urządzenia lokalnego również na różnych maszynach.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT Hub są tworzone w następujących regionach:

   * **Środkowe stany USA**

   * **Central US EUAP**

* Zainstaluj [Visual Studio 2019](https://www.visualstudio.com/vs/) z ["programowanie aplikacji klasycznych przy użyciu C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) włączony pakiet roboczy.
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Microsoft Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Rozszerzenia IOT dodaje określone polecenia usługi IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS) do wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki Start będziesz korzystać z [zestaw SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Umożliwią przygotowanie środowiska programowania, używany do klonowania i twórz [zestawu SDK C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start. 


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

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz usługi Azure Cloud Shell przy użyciu [rozszerzenia IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) zarejestrować symulowane urządzenie.

1. Uruchom następujące polecenie w usłudze Azure Cloud Shell do tworzenia tożsamości urządzenia.

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

   **MyDevice**: jest to nazwa nadana dla zarejestrowanego urządzenia. Użyj nazwy MyDevice w pokazany sposób. Jeśli wybierzesz inną nazwę dla danego urządzenia, również należy używać tej nazwy, w tym artykule, a nazwa urządzenia w przykładowych aplikacji przed ich uruchomieniem aktualizacji.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Uruchom następujące polecenia w usłudze Azure Cloud Shell, aby uzyskać _parametry połączenia urządzenia_ dla tego urządzenia została zarejestrowana:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Zanotuj parametry połączenia urządzenia, które wyglądają jak w następującym przykładzie:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    Użyjesz tej wartości w dalszej części tego przewodnika Szybki start.

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji możesz ustanowić strumień end-to-end do tunelowania ruchu protokołu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

1. Edytuj plik źródłowy `iothub_client_c2d_streaming_proxy_sample.c` w folderze `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/` i Podaj parametry połączenia urządzenia, urządzenia docelowego adresu IP/nazwa hosta i port 22 protokołu SSH:

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

Zgodnie z opisem w [sekcji jak ona działa](#how-it-works), ustanawiania strumień end-to-end do tunelowania ruchu SSH wymaga lokalnego serwera proxy na każdym końcu (zarówno na usługę i urządzenia). W publicznej wersji zapoznawczej zestawu SDK C usługi IoT Hub obsługuje strumienie urządzenia tylko po stronie urządzenia. Aby skompilować i uruchomić lokalnej usługi Serwer proxy, wykonaj kroki, które są dostępne do uruchamiania lokalnego usługi serwera proxy w jednym z następujących przewodników Szybki Start:

   * [SSH/RDP, za pośrednictwem usługi IoT Hub device strumieni, za pomocą C# aplikacje serwera proxy](./quickstart-device-streams-proxy-csharp.md)

   * [SSH/RDP, za pośrednictwem strumieni urządzenia usługi IoT Hub przy użyciu aplikacji Node.js z serwera proxy](./quickstart-device-streams-proxy-nodejs.md).

### <a name="establish-an-ssh-session"></a>Tworzenie sesji SSH

Po uruchomieniu proxy lokalnych urządzeń i usług za pomocą programu klienta SSH, a następnie nawiązać połączenie z lokalnej usługi Serwer proxy do portu 2222 (zamiast demon SSH bezpośrednio).

```cmd/sh
ssh <username>@localhost -p 2222
```

W tym momencie zostanie wyświetlony z monitem logowania SSH, aby wprowadzić swoje poświadczenia.

Dane wyjściowe konsoli na serwerze proxy urządzenia lokalnego, który łączy się z demonem SSH pod adresem `IP_address:22`: ![Dane wyjściowe urządzenia lokalnego serwera proxy](./media/quickstart-device-streams-proxy-c/device-console-output.png)

Dane wyjściowe programu klienta SSH konsoli (klient SSH komunikuje się demon SSH, łącząc się z portu 22, którego nasłuchuje serwer proxy usługi lokalnej): ![Dane wyjściowe z klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT Hub, zarejestrowano urządzenie, wdrożono program serwera proxy urządzenia lokalnego i usługi lokalnej w celu utworzenia strumienia urządzenia za pomocą usługi IoT Hub oraz użyto serwerów proxy do tunelowania ruchu SSH.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
