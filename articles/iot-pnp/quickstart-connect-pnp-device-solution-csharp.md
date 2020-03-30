---
title: Interakcja z urządzeniem IoT Plug and Play Preview połączonym z twoim rozwiązaniem Azure IoT | Dokumenty firmy Microsoft
description: Użyj języka C# (.NET), aby połączyć się z urządzeniem IoT Plug and Play Preview i wchodzić w interakcje z urządzeniem IoT Preview, które jest połączone z twoim rozwiązaniem Azure IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 0953f68839217c1c75eb86f8399ce023f3863ab4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76963975"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-c"></a>Szybki start: interakcja z urządzeniem IoT Plug and Play Preview podłączonym do rozwiązania (C#)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Podgląd ioT Plug and Play upraszcza IoT, umożliwiając interakcję z możliwościami urządzenia bez znajomości implementacji urządzenia źródłowego. Ten przewodnik Szybki start pokazuje, jak używać języka C# (z .NET) do łączenia się z urządzeniem Typu Plug and Play IoT, które jest podłączone do rozwiązania, i sterować nim.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, musisz zainstalować program .NET Core (2.x.x lub 3.x.x.x) na komputerze deweloperskim. Możesz pobrać preferowaną wersję .NET Core SDK dla wielu platform z [download .NET Core](https://dotnet.microsoft.com/download/dotnet-core/).

Wersję platformy .NET, która znajduje się na komputerze deweloperskim, można zweryfikować, uruchamiając następujące polecenie w oknie terminala lokalnego: 

```cmd/sh
dotnet --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _parametry połączenia centrum IoT_ dla koncentratora (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku Szybki start należy użyć przykładowego czujnika środowiska, który jest napisany w języku C# jako urządzenie Typu Plug and Play IoT. Poniższe instrukcje pokazują, jak zainstalować i uruchomić urządzenie:

1. Otwórz okno terminala w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować przykłady Usługi Azure IoT dla repozytorium GitHub [w języku C# (.NET)](https://github.com/Azure-Samples/azure-iot-samples-csharp) w tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
    ```

1. To okno terminalu będzie teraz używane jako terminal _urządzenia._ Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** folder.

1. Konfigurowanie _ciągu połączenia urządzenia:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Skompiluj niezbędne pakiety i uruchom próbkę za pomocą następującego polecenia:

    ```cmd\sh
        dotnet run
    ```

1. Są widoczne komunikaty informujące, że urządzenie zostało pomyślnie zarejestrowane i oczekuje na aktualizacje z chmury. Oznacza to, że urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do koncentratora. Nie zamykaj tego terminalu, będziesz go potrzebować później, aby potwierdzić, że próbki usług również zadziałały.

## <a name="run-the-sample-solution"></a>Uruchamianie roztworu próbki

W tym przewodniku Szybki start można użyć przykładowego rozwiązania IoT w języku C# do interakcji z przykładowym urządzeniem.

1. Otwórz kolejne okno terminala (będzie to twój terminal _serwisowy)._ Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-samples-csharp/digitaltwin/Samples/service** folder.

1. Skonfiguruj parametry połączenia i _identyfikator urządzenia_ _koncentratora IoT,_ aby umożliwić usłudze łączenie się z obydwoma z nich:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Odczytanie właściwości

1. Po podłączeniu _urządzenia_ do jego terminala został wyświetlony następujący komunikat wskazujący jego stan online:

    ```cmd/sh
    Waiting to receive updates from cloud...
    ```

1. Przejdź do terminalu _serwisowego_ i użyj następujących poleceń, aby uruchomić próbkę do odczytu informacji o urządzeniu:

    ```cmd/sh
    cd GetDigitalTwin
    dotnet run
    ```

1. W wyjściu terminala _serwisowego_ przewiń do składnika. `environmentalSensor` Widać, że `state` właściwość, która jest używana do wskazania, czy urządzenie jest w trybie online, została zgłoszona jako _prawdziwa:_

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aktualizowanie właściwości zapisywalnej

1. Przejdź do terminalu _serwisowego_ i ustaw następujące zmienne, aby zdefiniować właściwość, którą należy zaktualizować:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set PROPERTY_NAME=brightness
    set PROPERTY_VALUE=42
    ```

1. Użyj następujących poleceń, aby uruchomić przykład do aktualizacji właściwości:

    ```cmd/sh
    cd ..\UpdateProperty
    dotnet run
    ```

1. Dane wyjściowe terminala _serwisowego_ pokazują zaktualizowane informacje o urządzeniu. Przewiń `environmentalSensor` do komponentu, aby zobaczyć nową wartość jasności 42.

    ```json
        "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    }
    ```

1. Przejdź do _terminalu urządzenia,_ zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    Received updates for property 'brightness'
    Desired brightness = '"42"'.
    Reported brightness = ''.
    Version is '2'.
    Sent pending status for brightness property.
    Sent completed status for brightness property.
    ```
2. Wróć do terminalu _serwisowego_ i uruchom poniższe polecenia, aby ponownie uzyskać informacje o urządzeniu, aby potwierdzić, że właściwość została zaktualizowana.
    
    ```cmd/sh
    cd ..\GetDigitalTwin
    dotnet run
    ```
3. W wyjściu terminala `environmentalSensor` _serwisowego_ pod komponentem zostanie wyświetlona zaktualizowana wartość jasności. Uwaga: może upłynąć trochę czasu, aby urządzenie ukończyło aktualizację. Można powtórzyć ten krok, dopóki urządzenie faktycznie przetworzy aktualizację właściwości.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "desired": {
            "value": "42"
          },
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "description": "Request completed",
              "version": 2
            }
          }
        },
        "state": {
          "reported": {
            "value": true
          }
        }
      }
    },
    ```

### <a name="invoke-a-command"></a>Wywoływanie polecenia

1. Przejdź do terminalu _serwisowego_ i ustaw następujące zmienne, aby zdefiniować, które polecenie ma wywołać:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    ```

1. Użyj następujących poleceń, aby uruchomić próbkę do wywoływania polecenia:

    ```cmd/sh
    cd ..\InvokeCommand
    dotnet run
    ```

1. Wyjście w terminalu _serwisowym_ powinno być wyświetlane następujące potwierdzenie:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command blink invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned payload was
    {"description": "abc"}
    Enter any key to finish
    ```

1. Przejdź do terminalu _urządzenia,_ zobaczysz, że polecenie zostało potwierdzone:

    ```cmd/sh
    Command - blink was invoked from the service
    Data - null
    Request Id - <some ID value>.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak podłączyć urządzenie Typu Plug and Play do rozwiązania IoT. Aby dowiedzieć się więcej o tworzeniu rozwiązania współdziałanego z urządzeniami IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: łączenie się z urządzeniem i interakcję z nim](howto-develop-solution.md)
