---
title: Azure IoT Hub urządzenia strumieni C# Przewodnik Szybki start dotyczący protokołu SSH i RDP (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start Uruchom dwa przykładowe C# aplikacji, które umożliwiają scenariusze SSH i RDP przez strumień urządzenie usługi IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 1d5fbb410a61419f6f6d2e80cdb1a16c07672fe9
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733338"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Szybki start: Włącz protokół SSH i RDP przez strumień urządzenie usługi IoT Hub przy użyciu C# aplikację serwera proxy (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki Start obejmuje dwa C# aplikacji, które umożliwiają ruchu aplikacji klient serwer (na przykład Secure Shell [SSH] i protokołu Remote Desktop Protocol [RDP] do wysłania przez strumień z urządzeń, które zostało nawiązane za pośrednictwem usługi IoT hub. Omówienie konfiguracji, zobacz [przykładowej aplikacji lokalnego serwera proxy dla protokołu SSH lub RDP](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

W tym artykule najpierw w tym artykule opisano ustawienia dla protokołu SSH (Korzystanie z portu 22), a następnie w tym artykule opisano sposób modyfikowania ustawień portu dla protokołu RDP. Ponieważ strumieni urządzenia — i protokołu — niezależnie od aplikacji, można zmodyfikować tej samej próbki, aby pomieścić inne rodzaje ruchu aplikacji. Ta modyfikacja zwykle obejmuje tylko zmiany portu komunikacji na taki, który jest używany przez planowanej aplikacji.

## <a name="how-it-works"></a>Jak to działa

Na poniższym rysunku przedstawiono, jak aplikacje proxy lokalnych urządzeń i usług lokalnych, w tym przykładzie włączyć end-to-end łączności między klientem SSH i procesy demona SSH. W tym miejscu przyjęto założenie, że demon jest uruchomiony na tym samym urządzeniu, co aplikacja urządzenia lokalnego serwera proxy.

![Ustawienia aplikacji lokalnego serwera proxy](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg)

1. Aplikację serwera proxy usługi lokalnej łączy się z Centrum IoT i inicjuje strumienia urządzenia na urządzeniu docelowym.

1. Aplikację serwera proxy urządzenia lokalnego kończy uzgadnianie inicjowania strumienia i ustanawia tunel przesyłania strumieniowego typu end-to-end za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT hub po stronie usługi.

1. Łączy się demon SSH, który nasłuchuje na porcie 22 na urządzeniu z urządzenia lokalnego serwera proxy aplikacji. To ustawienie jest konfigurowane, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy urządzenia lokalnego".

1. Lokalne usługi serwera proxy aplikacji oczekuje na nowe połączenia SSH z użytkownikiem przez nasłuchiwanie na wyznaczonym porcie, w tym przypadku jest port 2222. To ustawienie jest konfigurowane, zgodnie z opisem w sekcji "Uruchom aplikację serwera proxy usługi lokalnego". Gdy użytkownik nawiązuje połączenie za pośrednictwem klienta SSH, tunelu umożliwia ruch aplikacji SSH, należy dokonać między aplikacją klienta i serwera SSH.

> [!NOTE]
> Ruch SSH, który jest przesyłany przez strumień urządzeń jest tunel za pośrednictwem punktu końcowego przesyłania strumieniowego usługi IoT hub, a nie wysyłane bezpośrednio między usługą i urządzeniem. Aby uzyskać więcej informacji, zobacz [korzyści z używania strumieni urządzenia usługi Iot Hub](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT, które są tworzone w następujących regionach:

  * Środkowe stany USA
  * Środkowe stany USA — EUAP

* Dwa przykładowe aplikacje, które są uruchamiane w tym przewodniku Szybki Start zostały napisane przy użyciu C#. Konieczne jest zestaw .NET Core SDK 2.1.0, lub później na komputerze deweloperskim.

  Możesz pobrać [zestawu .NET Core SDK dla wielu platform z .NET](https://www.microsoft.com/net/download/all).

* Sprawdź bieżącą wersję C# na komputerze deweloperskim za pomocą następującego polecenia:

    ```
    dotnet --version
    ```

* Uruchom następujące polecenie, aby dodać rozszerzenia usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell. Dodaje rozszerzenia IOT, IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS)-konkretnych poleceń interfejsu wiersza polecenia platformy Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* [Pobierz przykład C# projektu](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)i Wyodrębnij archiwum ZIP.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki Start używasz usługi Azure Cloud Shell można zarejestrować symulowane urządzenie.

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

1. Aby nawiązać połączenie z Centrum IoT hub i ustanowić strumień urządzeń, należy również *parametry połączenia usługi* z usługi IoT hub, aby umożliwić aplikacji po stronie usługi. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Należy pamiętać, zwrócona wartość do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Połączenie SSH z urządzeniem za pośrednictwem strumieni urządzeń

W tej sekcji możesz ustanowić strumień end-to-end do tunelowania ruchu protokołu SSH.

### <a name="run-the-device-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy urządzenia lokalnego

Przejdź do *-strumieni — serwer proxy/urządzenia* katalogu w folderze rozpakowanego projektu. Przechowuje pod ręką następujące informacje:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `deviceConnectionString` | Parametry połączenia urządzenia, który został utworzony wcześniej. |
| `targetServiceHostName` | Adres IP, na którym nasłuchuje serwer SSH. Adres będzie `localhost` gdyby ten sam adres IP, którym jest uruchomiona aplikacja urządzenia lokalnego serwera proxy. |
| `targetServicePort` | Port, który jest używany przez protokół Twojej aplikacji (dla protokołu SSH, domyślnie jest to port 22).  |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $deviceConnectionString localhost 22

# In Windows
dotnet run %deviceConnectionString% localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Uruchamianie aplikacji serwera proxy usługi lokalnej

Przejdź do elementu `device-streams-proxy/service` w folderze rozpakowanego projektu. Przydatne będą następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Parametry połączenia usługi IoT Hub. |
| `deviceId` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, którą Twój klient SSH, będą nawiązywać połączenie. Używamy port 2222 w tym przykładzie, ale można użyć innych dowolnego liczb. |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-the-ssh-client"></a>Uruchom klienta SSH

Teraz za pomocą aplikacji klienta SSH i nawiązać połączenie z lokalnej usługi serwera proxy aplikacji do portu 2222 (zamiast demon SSH bezpośrednio).

```
ssh <username>@localhost -p 2222
```

W tym momencie okno logowania SSH wyświetli monit o wprowadzenie poświadczeń.

Dane wyjściowe konsoli po stronie usługi (aplikacji lokalnej usługi Serwer proxy nasłuchuje na porcie 2222):

![Dane wyjściowe aplikacji lokalnej usługi serwera proxy](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

Dane wyjściowe w aplikacji urządzenia lokalnego serwera proxy, który łączy się demon SSH w konsoli *IP_address:22*:

![Dane wyjściowe z aplikacji urządzenia lokalnego serwera proxy](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

Dane wyjściowe konsoli aplikacji klienta SSH. Klient SSH komunikuje się demon SSH łącząc się z portu 22, której aplikacja lokalnej usługi Serwer proxy nasłuchuje na:

![Dane wyjściowe aplikacji klienta SSH](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Połączenie RDP z urządzeniem za pośrednictwem strumieni urządzeń

Instalator dla protokołu RDP jest bardzo podobna do konfiguracji protokołu SSH (opisane powyżej). Użyj docelowy adres IP protokołu RDP i zamiast tego portu 3389 i za pomocą klienta RDP (zamiast z klientem SSH).

### <a name="run-the-device-local-proxy-application-rdp"></a>Uruchom aplikację urządzenia lokalnego serwera proxy (RDP)

Przejdź do *-strumieni — serwer proxy/urządzenia* katalogu w folderze rozpakowanego projektu. Przechowuje pod ręką następujące informacje:

| Nazwa argumentu | Wartość argumentu |
|----------------|-----------------|
| `DeviceConnectionString` | Parametry połączenia urządzenia, który został utworzony wcześniej. |
| `targetServiceHostName` | Nazwa hosta lub adres IP, na którym działa serwer protokołu RDP. Adres będzie `localhost` gdyby ten sam adres IP, którym jest uruchomiona aplikacja urządzenia lokalnego serwera proxy. |
| `targetServicePort` | Port używany przez protokół Twojej aplikacji (dla protokołu RDP, domyślnie jest to port 3389).  |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run $DeviceConnectionString localhost 3389

# In Windows
dotnet run %DeviceConnectionString% localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Uruchom aplikację serwera proxy usługi lokalnej (RDP)

Przejdź do elementu `device-streams-proxy/service` w folderze rozpakowanego projektu. Przydatne będą następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `iotHubConnectionString` | Parametry połączenia usługi IoT Hub. |
| `deviceId` | Utworzony wcześniej identyfikator urządzenia. |
| `localPortNumber` | Port lokalny, którą Twój klient SSH, będą nawiązywać połączenie. W tym przykładzie użyto portu 2222, ale można go zmienić na inny. |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run $serviceConnectionString MyDevice 2222

# In Windows
dotnet run %serviceConnectionString% MyDevice 2222
```

### <a name="run-rdp-client"></a>Uruchamianie klienta RDP

Teraz za pomocą aplikacji klienta RDP i połączyć się z aplikacją usługi lokalnego serwera proxy, port 2222 (była to dowolny dostępny port, którą wybrano wcześniej).

![Protokół RDP nawiązanie połączenia z lokalnej usługi serwera proxy aplikacji](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start zostało Konfigurowanie usługi IoT hub, zarejestrowane urządzenia, wdrożonych aplikacji lokalnych urządzeń i lokalne usługi Serwer proxy można ustanowić strumienia urządzenia za pomocą usługi IoT hub i używane aplikacje serwera proxy do tunelowania ruchu SSH lub RDP. Tym samym modelu może pomieścić inne protokoły klient serwer, w której serwer jest uruchamiany na urządzeniu (na przykład demon SSH).

Aby dowiedzieć się więcej na temat urządzeń, strumieni, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
