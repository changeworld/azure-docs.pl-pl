---
title: Komunikowanie się z aplikacją urządzenia w języku C# za pomocą strumieni urządzeń usługi Azure IoT Hub
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje języka C#, które komunikują się za pośrednictwem strumienia urządzenia ustanowionego za pośrednictwem usługi IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 64af62cb6c2c56ca8c7e67e2f1467d4a7e8335a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78675524"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: komunikowanie się z aplikacją urządzenia w języku C# za pośrednictwem strumieni urządzeń Usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Usługa Azure IoT Hub obsługuje obecnie strumienie urządzeń jako [funkcję w wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki start obejmuje dwie aplikacje języka C#, które wykorzystują strumienie urządzeń do wysyłania danych tam iz powrotem (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

* Podgląd strumieni urządzeń jest obecnie obsługiwany tylko dla centrów IoT, które są tworzone w następujących regionach:
  * Środkowe stany USA
  * Centralna amerykańska EUAP
  * Europa Północna
  * Azja Południowo-Wschodnia

* Dwie przykładowe aplikacje uruchomione w tym przewodniku Szybki start są zapisywane w języku C#. Potrzebujesz .NET Core SDK 2.1.0 lub nowszej na komputerze deweloperskim.
  * Pobierz zestaw [.NET Core SDK dla wielu platform z platformy .NET](https://www.microsoft.com/net/download/all).
  * Sprawdź bieżącą wersję języka C# na komputerze deweloperskim za pomocą następującego polecenia:

   ```
   dotnet --version
   ```

* Dodaj rozszerzenie Usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do wystąpienia usługi Cloud Shell, uruchamiając następujące polecenie. Rozszerzenie IOT dodaje IoT Hub, IoT Edge i IoT Device Provisioning Service (DPS) polecenia specyficzne dla interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* [Pobierz przykłady usługi Azure IoT C#](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i wyodrębnij archiwum ZIP. Jest potrzebny zarówno po stronie urządzenia, jak i po stronie serwisowej.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji używasz usługi Azure Cloud Shell do zarejestrowania symulowanego urządzenia.

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

3. Potrzebne są też *parametry połączenia usługi* z centrum IoT, aby umożliwić aplikacji po stronie usługi nawiązanie połączenia z centrum IoT i utworzenie strumienia urządzeń. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp symbol zastępczy *YourIoTHubName* nazwą wybraną dla centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Zanotuj zwrócony ciąg połączenia usługi do późniejszego użycia w tym przewodniku Szybki start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikacja między urządzeniem a usługą za pośrednictwem strumieni urządzeń

W tej sekcji należy uruchomić zarówno aplikacji po stronie urządzenia i aplikacji po stronie usługi i komunikować się między nimi.

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

W oknie terminala lokalnego `iot-hub/Quickstarts/device-streams-echo/service` przejdź do katalogu w rozpakowanym folderze projektu. Podtrzymuj następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Ciąg połączenia usługi centrum IoT hub. |
| `MyDevice` | Utworzony wcześniej identyfikator urządzenia. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Aplikacja będzie czekać na aplikację urządzenia, aby stać się dostępne.

> [!NOTE]
> Przekroczenie limitu czasu występuje, gdy aplikacja po stronie urządzenia przestaje odpowiadać na czas.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

W innym oknie terminala `iot-hub/Quickstarts/device-streams-echo/device` lokalnego przejdź do katalogu w rozpakowanym folderze projektu. Podtrzymuj następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Ciąg połączenia urządzenia usługi IoT Hub. |

Skompiluj i uruchom kod za pomocą następujących poleceń:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Po zakończeniu ostatniego kroku aplikacja po stronie usługi inicjuje strumień do urządzenia. Po ustanowieniu strumienia aplikacja wysyła bufor ciągów do usługi za cały strumień. W tym przykładzie aplikacji po stronie usługi po prostu echa z powrotem te same dane do urządzenia, który pokazuje pomyślne komunikacji dwukierunkowej między dwiema aplikacjami.

Wyjście konsoli po stronie urządzenia:

![Wyjście konsoli po stronie urządzenia](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Wyjście konsoli po stronie usługi:

![Wyjście konsoli po stronie usługi](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Ruch wysyłany przez strumień jest tunelowany przez centrum IoT hub, a nie wysyłany bezpośrednio. Korzyści są szczegółowo opisane w [korzyściach płynących ze strumieni urządzenia.](./iot-hub-device-streams-overview.md#benefits)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT hub, zarejestrowano urządzenie, ustanowiono strumień urządzeń między aplikacjami języka C# po stronie urządzenia i usługi oraz użyto strumienia do wysyłania danych między aplikacjami.

Aby dowiedzieć się więcej o strumieniach urządzeń, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
