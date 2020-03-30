---
title: Interakcja z urządzeniem IoT Plug and Play Preview połączonym z twoim rozwiązaniem Azure IoT | Dokumenty firmy Microsoft
description: Funkcja Node.js umożliwia łączenie się z urządzeniem IoT Plug and Play Preview i interakcję z nim, które jest połączone z rozwiązaniem IoT usługi Azure.
author: miagdp
ms.author: miag
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 9962763e647faddc5a2179f304aeb3fa8ca256e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75550744"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-nodejs"></a>Szybki start: interakcja z urządzeniem IoT Plug and Play Preview podłączonym do rozwiązania (Node.js)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Podgląd ioT Plug and Play upraszcza IoT, umożliwiając interakcję z możliwościami urządzenia bez znajomości implementacji urządzenia źródłowego. Ten przewodnik Szybki start pokazuje, jak używać node.js do łączenia się z urządzeniem Typu Plug and Play IoT podłączonym do rozwiązania i sterowania nim.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, potrzebujesz node.js na komputerze deweloperskim. Możesz pobrać najnowszą zalecaną wersję dla wielu platform z [nodejs.org](https://nodejs.org).

Możesz sprawdzić bieżącą wersję środowiska Node.js na komputerze deweloperskim przy użyciu następującego polecenia:

```cmd/sh
node --version
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _parametry połączenia centrum IoT_ dla koncentratora (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku Szybki start można użyć przykładowego czujnika środowiska, który jest napisany w pliku Node.js jako urządzenie Typu Plug and Play IoT. Poniższe instrukcje pokazują, jak zainstalować i uruchomić urządzenie:

1. Otwórz okno terminala w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [przykłady usługi Azure IoT dla](https://github.com/azure-samples/azure-iot-samples-node) repozytorium Usługi Node.js GitHub w tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. To okno terminalu będzie teraz używane jako terminal _urządzenia._ Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-samples-node/digital-twins/Quickstarts/Device.** Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Konfigurowanie _ciągu połączenia urządzenia:_

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Uruchom próbkę za pomocą następującego polecenia:

    ```cmd/sh
    node sample_device.js
    ```

1. Zobaczysz komunikaty informujące, że urządzenie wysłało pewne informacje i zgłosiło się w trybie online. Oznacza to, że urządzenie rozpoczęło wysyłanie danych telemetrycznych do koncentratora i jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego terminalu, będziesz go potrzebować później, aby potwierdzić, że próbki usług również zadziałały.

## <a name="run-the-sample-solution"></a>Uruchamianie roztworu próbki

W tym przewodniku Szybki start można użyć przykładowego rozwiązania IoT w node.js do interakcji z przykładowym urządzeniem.

1. Otwórz kolejne okno terminala (będzie to twój terminal _serwisowy)._ Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-samples-node/digital-twins/Quickstarts/Service** folder. Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj _parametry połączenia centrum IoT,_ aby umożliwić usłudze łączenie się z nim:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    ```

### <a name="read-a-property"></a>Odczytanie właściwości

1. Po podłączeniu _urządzenia_ do jego terminala został wyświetlony następujący komunikat wskazujący jego stan online:

    ```cmd/sh
    reported state property as online
    ```

1. W folderze **/azure-iot-samples-node/digital-twins/Quickstarts/Service** otwórz plik **get_digital_twin.js**. Zastąp symbol zastępczy `<DEVICE_ID_GOES_HERE>` identyfikatorem urządzenia i zapisz plik.

1. Przejdź do terminalu _serwisowego_ i użyj następującego polecenia, aby uruchomić próbkę do odczytu informacji o urządzeniu:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. W wyjściu terminala _serwisowego_ przewiń do składnika. `environmentalSensor` Widzisz, że `state` nieruchomość została zgłoszona jako _online:_

    ```JSON
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="update-a-writable-property"></a>Aktualizowanie właściwości zapisywalnej

1. Otwórz plik **update_digital_twin_property.js**.

1. Na początku pliku jest zestaw stałych zdefiniowanych za pomocą symboli zastępczych wielkich liter. Zastąp `<DEVICE_ID_GOES_HERE>` symbol zastępczy rzeczywistym identyfikatorem urządzenia, zaktualizuj pozostałe stałe następującymi wartościami i zapisz plik:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 42;
    ```

1. Przejdź do terminalu _serwisowego_ i użyj następującego polecenia, aby uruchomić próbkę do aktualizacji właściwości:

    ```cmd/sh
    node update_digital_twin_property.js
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
            "value": "online"
          }
        }
      }
    }
    ```

1. Przejdź do _terminalu urządzenia,_ zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    Received an update for brightness: 42
    updated the property
    ```
2. Wróć do terminalu _serwisowego_ i uruchom poniższe polecenie, aby ponownie uzyskać informacje o urządzeniu, aby potwierdzić, że właściwość została zaktualizowana.
    
    ```cmd/sh
    node get_digital_twin.js
    ```

3. W wyjściu terminala `environmentalSensor` _serwisowego_ pod komponentem zostanie wyświetlona zaktualizowana wartość jasności. Uwaga: może upłynąć trochę czasu, aby urządzenie ukończyło aktualizację. Można powtórzyć ten krok, dopóki urządzenie faktycznie przetworzy aktualizację właściwości.
    
    ```json
    "environmentalSensor": {
      "name": "environmentalSensor",
      "properties": {
        "brightness": {
          "reported": {
            "value": "42",
            "desiredState": {
              "code": 200,
              "version": 2,
              "description": "helpful descriptive text"
            }
          },
          "desired": {
            "value": "42"
          }
        },
        "state": {
          "reported": {
            "value": "online"
          }
        }
      }
    }
    ```

### <a name="invoke-a-command"></a>Wywoływanie polecenia

1. Otwórz plik **invoke_command.js**.

1. Na początku pliku zastąp symbol zastępczy `<DEVICE_ID_GOES_HERE>` rzeczywistym identyfikatorem urządzenia. Zaktualizuj pozostałe stałe za pomocą następujących wartości, a następnie zapisz plik:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    const commandArgument = '<For the environmental sensor, this value does not matter. Any string will do.>'; 
    ```

1. Przejdź do terminalu _serwisowego._ Użyj następującego polecenia, aby uruchomić próbkę do wywoływania polecenia:

    ```cmd/sh
    node invoke_command.js
    ```

1. Wyjście w terminalu _serwisowym_ powinno być wyświetlane następujące potwierdzenie:

    ```cmd/sh
    invoking command blink on interface instanceenvironmentalSensor for device <device ID>...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "<some ID value>",
      "_response": "helpful response text"
    }
    ```

1. Przejdź do terminalu _urządzenia,_ zobaczysz, że polecenie zostało potwierdzone:

    ```cmd/sh
    received command: blink for interfaceInstance: environmentalSensor
    acknowledgement succeeded.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak podłączyć urządzenie Typu Plug and Play do rozwiązania IoT. Aby dowiedzieć się więcej o tworzeniu rozwiązania współdziałanego z urządzeniami IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: łączenie się z urządzeniem i interakcję z nim](howto-develop-solution.md)
