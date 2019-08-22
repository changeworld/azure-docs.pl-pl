---
title: Współpracuj z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej podłączonym do rozwiązania Azure IoT | Microsoft Docs
description: Użyj środowiska Node. js, aby nawiązać połączenie z urządzeniem usługi IoT Plug and Play w wersji zapoznawczej i korzystać z niego, które jest połączone z rozwiązaniem Azure IoT.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 246d3eac8f9d8aff6d603ea8686e430ce0f772ea
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878250"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution"></a>Szybki start: Współdziałanie z urządzeniem z systemem IoT Plug and Play w wersji zapoznawczej, które jest połączone z rozwiązaniem

Wersja zapoznawcza Plug and Play IoT upraszcza IoT, umożliwiając współpracę z możliwościami urządzenia bez znajomości podstawowej implementacji urządzenia. W tym przewodniku szybki start pokazano, jak używać środowiska Node. js do nawiązywania połączenia z urządzeniem IoT Plug and Play i kontrolowania go, który jest połączony z rozwiązaniem.

## <a name="prerequisites"></a>Wymagania wstępne

Pobierz i zainstaluj program Node. js z [NodeJS.org](https://nodejs.org).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Przygotowywanie Centrum IoT Hub

Aby ukończyć ten przewodnik Szybki Start, potrzebujesz również usługi Azure IoT Hub w ramach subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Dodaj Microsoft Azure rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Uruchom następujące polecenie, aby utworzyć tożsamość urządzenia w centrum IoT Hub. Zastąp wartości **YourIoTHubName** i **YourDevice** własnymi nazwami. Jeśli nie masz IoT Hub, postępuj zgodnie z poniższymi [instrukcjami](../iot-hub/iot-hub-create-using-cli.md) , aby go utworzyć:

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Uruchom następujące polecenia, aby uzyskać _Parametry połączenia urządzenia_ dla zarejestrowanego urządzenia:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Uruchom następujące polecenia, aby uzyskać _Parametry połączenia usługi IoT Hub_ dla centrum:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="connect-your-device"></a>Połącz urządzenie

W tym przewodniku szybki start użyto przykładowego czujnika środowiska, który został zapisany w środowisku Node. js jako urządzenie Plug and Play IoT. Poniższe instrukcje przedstawiają sposób instalowania i uruchamiania urządzenia:

1. Klonowanie repozytorium GitHub:

    ```cmd/sh
    git clone https://github.com/azure-samples/azure-iot-samples-node
    ```

1. W terminalu przejdź do folderu głównego sklonowanego repozytorium, przejdź do folderu **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Device** , a następnie zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj _Parametry połączenia urządzenia_:

    ```cmd/sh
    set DEVICE_CONNECTION_STRING=<your device connection string>
    ```

1. Uruchom próbkę za pomocą następującego polecenia:

    ```cmd/sh
    node sample_device.js
    ```

1. Zobaczysz komunikat informujący o tym, że urządzenie wysłało dane telemetryczne i jego właściwości. Urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości. Nie zamykaj tego terminalu, potrzebujesz go później, aby potwierdzić, że także zadziałały również przykłady usługi.

## <a name="build-the-solution"></a>Kompilowanie rozwiązania

W tym przewodniku szybki start użyjesz przykładowego rozwiązania IoT w języku Node. js do współpracy z przykładowym urządzeniem.

1. Otwórz inny Terminal. Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/Azure-IoT-Samples-Node/Digital-Twins/QuickStarts/Service** . Zainstaluj wszystkie zależności, uruchamiając następujące polecenie:

    ```cmd/sh
    npm install
    ```

1. Skonfiguruj _Parametry połączenia centrum_:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<your hub connection string>
    ```

### <a name="read-a-property"></a>Odczytaj Właściwość

1. Po podłączeniu urządzenia do terminalu zostanie wyświetlony następujący komunikat:

    ```cmd/sh
    reported state property as online
    ```

1. Otwórz plik **get_digital_twin. js**. `deviceID` Zastąp ciąg identyfikatorem urządzenia i Zapisz plik.

1. Przejdź do terminalu, który został otwarty do uruchomienia przykładu usługi, i uruchom następujące polecenie:

    ```cmd/sh
    node get_digital_twin.js
    ```

1. W danych wyjściowych pod składnikiem _environmentalSensor_ zobaczysz ten sam stan, który został zgłoszony:

    ```JSON
    reported state property as online
    ```

### <a name="update-a-writable-property"></a>Aktualizowanie właściwości z możliwością zapisu

1. Otwórz plik **update_digital_twin_property. js**.

1. Na początku pliku istnieje zestaw stałych definiowany z dużymi symbolami zastępczymi. Zastąp ciąg **deviceID** rzeczywistym identyfikatorem urządzenia, zaktualizuj stałe przy użyciu następujących wartości i Zapisz plik:

    ```javascript
    const componentName = 'environmentalSensor';
    const propertyName = 'brightness';
    const propertyValue = 60;
    ```

1. Przejdź do terminalu, który został otwarty do uruchomienia przykładu usługi, i użyj następującego polecenia, aby uruchomić przykład:

    ```cmd/sh
    node update_digital_twin_property.js
    ```

1. W terminalu zobaczysz informacje o cyfrowej przędzce skojarzonej z urządzeniem. Znajdź składnik _environmentalSensor_, zobaczysz nową wartość jasności 60.

    ```json
        "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
          "brightness": {
            "reported": {
              "value": 60,
              "desiredState": {
                "code": 200,
                "version": 14,
                "description": "helpful descriptive text"
              }
            },
            "desired": {
              "value": 60
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

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    Received an update for brightness: 60
    updated the property
    ```
2. Wróć do terminalu _usługi_ i ponownie uruchom poniższe polecenie, aby potwierdzić, że właściwość została zaktualizowana.
    
    ```cmd/sh
    node get_digital_twin.js
    ```
3. W danych wyjściowych pod składnikiem environmentalSensor zostanie wyświetlona zaktualizowana wartość jasności. Uwaga: ukończenie aktualizacji może zająć trochę czasu. Ten krok można powtórzyć do momentu rzeczywistego przetworzenia przez urządzenie aktualizacji właściwości.
    
    ```json
      "brightness": {
        "reported": {
          "value": 60,
          }
       }
    ```

### <a name="invoke-a-command"></a>Wywołaj polecenie

1. Otwórz plik **invoke_command. js**.

1. Na początku pliku Zastąp wartość `deviceID` rzeczywistym identyfikatorem urządzenia. Zaktualizuj stałe przy użyciu następujących wartości, a następnie Zapisz plik:

    ```javascript
    const interfaceInstanceName = 'environmentalSensor';
    const commandName = 'blink';
    ```

1. Przejdź do terminalu, który został otwarty do uruchomienia przykładu usługi. Użyj następującego polecenia, aby uruchomić przykład:

    ```cmd/sh
    node invoke_command.js
    ```

1. W terminalu powodzenie wygląda jak następujące dane wyjściowe:

    ```cmd/sh
    invoking command blink on component environmentalSensor for device test...
    {
      "result": "helpful response text",
      "statusCode": 200,
      "requestId": "33e536d3-14f7-4105-88f3-629b9933851c",
      "_response": "helpful response text"
    }
    ```

1. Przejdź do terminalu _urządzenia_ , zobaczysz, że polecenie zostało potwierdzone:

    ```cmd/sh
    received command: blink for component: environmentalSensor
    acknowledgement succeeded.
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli planujesz kontynuować pracę z nowszymi artykułami, możesz zachować zasoby używane w tym przewodniku Szybki Start. W przeciwnym razie możesz usunąć zasoby utworzone w ramach tego przewodnika Szybki Start, aby uniknąć dodatkowych opłat.

Aby usunąć centrum i zarejestrowane urządzenie, wykonaj następujące kroki przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az group delete --name <Your group name>
```

Aby usunąć tylko urządzenie zarejestrowane w IoT Hub, wykonaj następujące kroki, korzystając z interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name [YourIoTHubName] --device-id [YourDevice]
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start dowiesz się, jak podłączyć urządzenie IoT Plug and Play do rozwiązania IoT. Aby dowiedzieć się więcej na temat tworzenia rozwiązania, które współdziała z urządzeniami Plug and Play IoT, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: Nawiązywanie połączenia z urządzeniem i korzystanie z niego](howto-develop-solution.md)
