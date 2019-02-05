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
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: d0fc8d68b3412c2c43a88e3a9484dab3a150b811
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886275"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>Szybki start: Obsługa protokołu SSH/RDP w strumieniach urządzeń usługi IoT Hub za pomocą aplikacji serwera proxy C (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Omówienie konfiguracji można znaleźć na [tej stronie](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

W tym dokumencie opisano konfigurację tunelowania ruchu SSH (przy użyciu portu 22) przez strumienie urządzeń. Konfiguracja ruchu RDP przebiega podobnie i wymaga prostej zmiany konfiguracji. Strumienie urządzeń nie wymagają protokołu i aplikacji, dlatego można zmodyfikować obecny przewodnik Szybki start (przez zmianę portów komunikacyjnych) w celu obsługi innych typów ruchu aplikacji.

## <a name="how-it-works"></a>Jak to działa?
Na poniższej ilustracji pokazano, jak skonfigurować programy serwera proxy urządzeń i usług lokalnych, aby umożliwić pełne połączenie między procesami klienta SSH i demona SSH. W publicznej wersji zapoznawczej zestaw C SDK obsługuje tylko strumienie urządzeń po stronie urządzenia. Dlatego ten przewodnik Szybki start zawiera tylko instrukcje uruchamiania aplikacji serwera proxy urządzenia lokalnego. Musisz uruchomić towarzyszącą aplikację serwera proxy usługi lokalnej, która jest dostępna w przewodniku [Szybki start języka C#](./quickstart-device-streams-proxy-csharp.md) lub [Szybki start środowiska Node.js](./quickstart-device-streams-proxy-nodejs.md).

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

* Zainstaluj program [Visual Studio 2017](https://www.visualstudio.com/vs/) z włączonym pakietem roboczym [„Programowanie aplikacji klasycznych w języku C++”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/).
* Zainstaluj najnowszą wersję usługi [Git](https://git-scm.com/download/).

## <a name="prepare-the-development-environment"></a>Przygotowywanie środowiska deweloperskiego

W tym przewodniku Szybki start będziesz używać [zestawu SDK urządzeń Azure IoT dla języka C](iot-hub-device-sdk-c-intro.md). Przygotujesz środowisko deweloperskie używane do sklonowania i opracowania [zestawu SDK usługi Azure IoT dla języka C](https://github.com/Azure/azure-iot-sdk-c) z usługi GitHub. Zestaw SDK w witrynie GitHub zawiera przykładowy kod używany w tym przewodniku Szybki start. 


1. Pobierz wersję 3.11.4 [systemu kompilacji CMake](https://cmake.org/download/). Sprawdź pobrane dane binarne przy użyciu odpowiedniej wartości skrótu kryptograficznego. W poniższym przykładzie użyto programu Windows PowerShell do sprawdzenia wartości skrótu kryptograficznego dla wersji dystrybucji MSI 3.11.4 x64:

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    Następujące wartości skrótu dla wersji 3.11.4 były wymienione w witrynie narzędzia CMake w momencie pisania tego dokumentu:

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    Ważne jest, aby wstępnie wymagane składniki (program Visual Studio oraz pakiet roboczy „Programowanie aplikacji klasycznych w języku C++”) były zainstalowane na tym komputerze **przed** uruchomieniem `CMake` instalacji. Gdy wymagania wstępne zostaną spełnione, a pobrane pliki zweryfikowane, zainstaluj system kompilacji CMake.

2. Otwórz wiersz polecenia lub powłokę Git Bash. Wykonaj następujące polecenie, aby sklonować repozytorium GitHub [zestawu SDK języka C usługi IoT Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Rozmiar tego repozytorium wynosi obecnie około 220 MB. Należy się spodziewać, że ukończenie operacji potrwa kilka minut.


3. Utwórz podkatalog `cmake` w katalogu głównym repozytorium Git, a następnie przejdź do tego folderu. 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. Uruchom poniższe polecenie, które utworzy wersję zestawu SDK specyficzną dla platformy klienta deweloperskiego. W systemie Windows rozwiązanie programu Visual Studio dla urządzenia symulowanego zostanie wygenerowane w katalogu `cmake`. 

```
    # In Linux
    cmake ..
    make -j
```

W systemie Windows uruchom następujące polecenia w wierszu polecenia dla deweloperów dla programu Visual Studio 2015 lub 2017:

```
    # In Windows
    # For VS2015
    $ cmake .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    $ cmake .. -G "Visual Studio 15 2017

    # Then build the project
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


## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

- Edytuj plik źródłowy `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c` i podaj parametry połączenia urządzenia, adres IP/nazwę hosta urządzenia docelowego oraz port RDP 22:
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[Connection string of IoT Hub]";
  static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
  static const size_t localPort = 22; // Port of the local server to connect to.
```

- Skompiluj przykład w następujący sposób:

```
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    $ make -j


    # In Windows
    # Go to cmake at root of repository
    cmake --build . -- /m /p:Configuration=Release
```

- Uruchom skompilowany program w urządzeniu:
```
    # In Linux
    # Go to sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    $ ./iothub_client_c2d_streaming_proxy_sample

    # In Windows
    # Go to sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
    iothub_client_c2d_streaming_proxy_sample.exe
```

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

Zgodnie z [powyższym](#how-it-works) opisem utworzenie pełnego strumienia tunelowania ruchu SSH wymaga lokalnego serwera proxy na każdym końcu (po stronie usługi i urządzenia). Jednak w publicznej wersji zapoznawczej zestaw C SDK usługi IoT Hub obsługuje tylko strumienie urządzeń po stronie urządzenia. W przypadku serwera proxy usługi lokalnej użyj towarzyszącego przewodnika [Szybki start języka C#](./quickstart-device-streams-proxy-csharp.md) lub [Szybki start środowiska Node.js](./quickstart-device-streams-proxy-nodejs.md).


### <a name="establish-an-ssh-session"></a>Tworzenie sesji SSH

Przy założeniu, że serwery proxy urządzenia lokalnego i usługi lokalnej są uruchomione, użyj programu klienta SSH i nawiąż połączenie z serwerem proxy usługi lokalnej na porcie 2222 (zamiast bezpośrednio z demonem SSH). 

```
ssh <username>@localhost -p 2222
```

W tym momencie zostanie wyświetlony monit logowania protokołu SSH o wprowadzenie poświadczeń.


Dane wyjściowe konsoli na serwerze proxy urządzenia lokalnego, który łączy się z demonem SSH pod adresem `IP_address:22`: ![Tekst alternatywny](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "Dane wyjściowe serwera proxy urządzenia lokalnego")

Dane wyjściowe konsoli programu klienta SSH (klient SSH komunikuje się z demonem SSH przez połączenie z portem 22, na którym nasłuchuje serwer proxy usługi lokalnej): ![Tekst alternatywny](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "Dane wyjściowe klienta SSH")

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT Hub, zarejestrowano urządzenie, wdrożono program serwera proxy urządzenia lokalnego i usługi lokalnej w celu utworzenia strumienia urządzenia za pomocą usługi IoT Hub oraz użyto serwerów proxy do tunelowania ruchu SSH.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
