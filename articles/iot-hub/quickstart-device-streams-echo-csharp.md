---
title: Strumienie urządzeń usługi Azure IoT Hub w języku C# — Szybki start (wersja zapoznawcza) | Microsoft Docs
description: W tym przewodniku Szybki start uruchomisz dwie przykładowe aplikacje w języku C#, które komunikują się za pomocą strumienia urządzeń ustanowionego przy użyciu usługi IoT Hub.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: edd3912b3674f3a80a81fd47ed490479f663852c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830828"
---
# <a name="quickstart-communicate-to-device-applications-in-c-via-iot-hub-device-streams-preview"></a>Szybki start: komunikacja z aplikacjami urządzenia w języku C# za pomocą strumieni urządzeń usługi IoT Hub (wersja zapoznawcza)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

[Strumienie urządzeń usługi IoT Hub](./iot-hub-device-streams-overview.md) umożliwiają aplikacjom usług i urządzeń bezpieczną komunikację w sposób przyjazny dla zapory. Ten przewodnik Szybki start obejmuje dwa programy w języku C#, które wykorzystują strumienie urządzeń do przesyłania danych między sobą (funkcja echo).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dwie przykładowe aplikacje uruchamiane w tym przewodniku Szybki start zostały napisane w języku C#. Potrzebujesz zestawu SDK .NET Core w wersji 2.1.0 lub nowszej na komputerze deweloperskim.

Możesz pobrać zestaw SDK .NET Core dla wielu platform z repozytorium [.NET](https://www.microsoft.com/net/download/all).

Możesz sprawdzić bieżącą wersję języka C# na komputerze deweloperskim przy użyciu następującego polecenia:

```
dotnet --version
```

Pobierz przykładowy projekt C# z https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip i wyodrębnij archiwum ZIP.


## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Rejestrowanie urządzenia

Zanim urządzenie będzie mogło nawiązać połączenie, należy je najpierw zarejestrować w centrum IoT. W tym przewodniku Szybki start opisano rejestrowanie urządzenia symulowanego przy użyciu usługi Azure Cloud Shell.

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

3. Potrzebne są też _parametry połączenia usługi_ z centrum IoT, aby umożliwić aplikacji po stronie usługi nawiązanie połączenia z centrum IoT i utworzenie strumienia urządzeń. Następujące polecenie umożliwia pobranie tej wartości dla centrum IoT:

   **YourIoTHubName**: zamień ten symbol zastępczy poniżej na wybraną nazwę centrum IoT Hub.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --hub-name YourIoTHubName
    ```

    Zanotuj zwróconą wartość, która wygląda następująco:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`
    

## <a name="communicate-between-device-and-service-via-device-streams"></a>Komunikacja między urządzeniem i usługą za pomocą strumieni urządzeń

### <a name="run-the-service-side-application"></a>Uruchamianie aplikacji po stronie usługi

Przejdź do elementu `device-streams-echo/service` w folderze rozpakowanego projektu. Przydatne będą następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `ServiceConnectionString` | Parametry połączenia usługi IoT Hub. |
| `DeviceId` | Utworzony wcześniej identyfikator urządzenia. |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $ServiceConnectionString MyDevice

# In Windows
dotnet run %ServiceConnectionString% MyDevice
```

### <a name="run-the-device-side-application"></a>Uruchamianie aplikacji po stronie urządzenia

Przejdź do katalogu `device-streams-echo/device` w folderze rozpakowanego projektu. Przydatne będą następujące informacje:

| Nazwa parametru | Wartość parametru |
|----------------|-----------------|
| `DeviceConnectionString` | Utworzone wcześniej parametry połączenia urządzenia. |

Skompiluj i uruchom kod w następujący sposób:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run $DeviceConnectionString

# In Windows
dotnet run %DeviceConnectionString%
```

Na końcu ostatniego kroku program po stronie usługi zainicjuje strumień do urządzenia, a po utworzeniu strumienia wyśle w nim bufor ciągu. W tym przykładzie program po stronie usługi po prostu przesyła z powrotem te same dane do urządzenia, demonstrując pomyślną dwukierunkową komunikację między dwiema aplikacjami. Zobacz rysunek poniżej.

Dane wyjściowe konsoli po stronie urządzenia: ![tekst alternatywny](./media/quickstart-device-streams-echo-csharp/device-console-output.png "Dane wyjściowe konsoli po stronie urządzenia")


Dane wyjściowe konsoli po stronie usługi: ![tekst alternatywny](./media/quickstart-device-streams-echo-csharp/service-console-output.png "Dane wyjściowe konsoli po stronie usługi")



Ruch wysyłany przez strumień będzie tunelowany przez centrum IoT Hub, a nie wysyłany bezpośrednio. Daje to [następujące korzyści](./iot-hub-device-streams-overview.md#benefits).


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start skonfigurowano centrum IoT Hub, zarejestrowano urządzenie, utworzono strumień urządzenia między aplikacjami w języku C# po stronie urządzenia i usługi oraz przesłano za pomocą strumienia dane między aplikacjami.

Aby dowiedzieć się więcej na temat strumieni urządzeń, użyj poniższych linków:

> [!div class="nextstepaction"]
> [Omówienie strumieni urządzeń](./iot-hub-device-streams-overview.md)
