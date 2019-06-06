---
title: Komunikować się z aplikacją urządzenia w C# za pośrednictwem strumieni urządzenia Azure IoT Hub (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: W tym przewodniku Szybki Start Uruchom dwa przykładowe C# aplikacje, które komunikują się za pomocą strumienia urządzenia nawiązane za pośrednictwem usługi IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 74a8fc40cff12070f7cea99981eb4e8321d7c1ef
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735151"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: Komunikacji z aplikacją urządzenia w C# za pośrednictwem strumieni urządzenia usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Usługa Azure IoT Hub obsługuje obecnie strumieni urządzenia jako [funkcja w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki Start obejmuje dwa C# aplikacje, które wykorzystują strumienie urządzenia do przesyłania danych i z powrotem (echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Strumienie urządzenia w wersji zapoznawczej jest obecnie obsługiwane tylko w przypadku centrów IoT, które są tworzone w następujących regionach:
  * Środkowe stany USA
  * Środkowe stany USA — EUAP

* Dwa przykładowe aplikacje, które są uruchamiane w tym przewodniku Szybki Start zostały napisane przy użyciu C#. Konieczne jest zestaw .NET Core SDK 2.1.0, lub później na komputerze deweloperskim.
  * Pobierz [zestawu .NET Core SDK dla wielu platform z .NET](https://www.microsoft.com/net/download/all).
  * Sprawdź bieżącą wersję C# na komputerze deweloperskim za pomocą następującego polecenia:

   ```
   dotnet --version
   ```

* Dodaj rozszerzenia usługi Azure IoT dla interfejsu wiersza polecenia platformy Azure do swojego wystąpienia usługi Cloud Shell, uruchamiając następujące polecenie. Dodaje rozszerzenia IOT, IoT Hub, IoT Edge i usługi aprowizacji urządzeń IoT (DPS)-konkretnych poleceń interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Pobierz przykład C# projektu](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) i Wyodrębnij archiwum ZIP. Należy ją zarówno po stronie urządzenia, jak i po stronie usługi.

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tej sekcji użyjesz usługi Azure Cloud Shell zarejestrować symulowane urządzenie.

1. Aby utworzyć tożsamości urządzenia, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > * Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.
   > * Użyj *MyDevice*, jak pokazano. Jest to nazwa podana dla zarejestrowanych urządzeń. Jeśli wybierzesz inną nazwę dla danego urządzenia, używać tej nazwy, w tym artykule i zaktualizuj nazwę urządzenia w przykładowych aplikacji, można uruchomić.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

1. Aby uzyskać *parametry połączenia urządzenia* dla urządzenia, które właśnie zostało zarejestrowane, uruchom następujące polecenie w usłudze Cloud Shell:

   > [!NOTE]
   > Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    Należy pamiętać, parametry połączenia urządzenia do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Potrzebne są też *parametry połączenia usługi* z centrum IoT, aby umożliwić aplikacji po stronie usługi nawiązanie połączenia z centrum IoT i utworzenie strumienia urządzeń. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   > [!NOTE]
   > Zastąp *YourIoTHubName* symbol zastępczy wybranej nazwy Centrum IoT.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    Należy pamiętać, zwrócona wartość do późniejszego użycia w tym przewodniku Szybki Start. Wygląda to następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Komunikację między urządzeniem i usługą za pomocą urządzeń, strumieni

W tej sekcji uruchamiania aplikacji po stronie urządzenia i aplikacji po stronie usługi i komunikować się między nimi.

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

Przejdź do *iot hub/przewodników Szybki Start/urządzenia — strumieni — echo/usługa* katalogu w folderze rozpakowanego projektu. Przechowuje pod ręką następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Podaj parametry połączenia usługi IoT hub. |
| `DeviceId` | Podaj identyfikator urządzenia utworzonego wcześniej (na przykład *MyDevice*). |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> Przekroczenie limitu czasu występuje, gdy aplikacja po stronie urządzenia przestaje odpowiadać na czas.

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Przejdź do *-/ przewodników Szybki Start/urządzeń — strumienie echo/urządzeń usługi iot hub* katalogu w folderze rozpakowanego projektu. Przechowuje pod ręką następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Podaj parametry połączenia urządzenia Twojego centrum IoT. |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

Na końcu ostatniego kroku aplikacji po stronie usługi inicjuje strumienia do Twojego urządzenia. Po ustanowieniu strumienia, aplikacja wysyła buforu ciągu do usługi w strumieniu. W tym przykładzie aplikacji po stronie usługi po prostu informujące ponownie te same dane do urządzenia, który pokazuje pomyślne dwukierunkowej komunikacji między dwiema aplikacjami.

Konsola danych wyjściowych po stronie urządzenia:

![Dane wyjściowe konsoli po stronie urządzenia](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Konsola danych wyjściowych po stronie usługi:

![Dane wyjściowe konsoli po stronie usługi](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Ruch, są wysyłane w strumieniu jest tunel za pośrednictwem usługi IoT hub, a nie wysyłane bezpośrednio. Korzyści zapewniane są szczegółowo opisane w [urządzenia strumieni korzyści](./iot-hub-device-streams-overview.md#benefits).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Kolejne kroki

W tym przewodniku Szybki Start zostało Konfigurowanie usługi IoT hub, zarejestrowane urządzenia, ustanowione strumień urządzeń, między C# aplikacje na stronach usług i urządzeń i umożliwia wysyłanie danych i z powrotem między aplikacjami przez strumień.

Aby dowiedzieć się więcej na temat urządzeń, strumieni, zobacz:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
