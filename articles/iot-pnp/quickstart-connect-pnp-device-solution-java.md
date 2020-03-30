---
title: Interakcja z urządzeniem IoT Plug and Play Preview połączonym z twoim rozwiązaniem Azure IoT | Dokumenty firmy Microsoft
description: Użyj języka Java, aby połączyć się z urządzeniem IoT Plug and Play Preview połączonym z twoim rozwiązaniem Azure IoT i wchodzić w interakcje z nim.
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 53812e68fe397b81f29869565e0e4a0640a9ef23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76964638"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-preview-device-thats-connected-to-your-solution-java"></a>Szybki start: interakcja z urządzeniem IoT Plug and Play Preview podłączonym do rozwiązania (Java)

[!INCLUDE [iot-pnp-quickstarts-3-selector.md](../../includes/iot-pnp-quickstarts-3-selector.md)]

Podgląd ioT Plug and Play upraszcza IoT, umożliwiając interakcję z możliwościami urządzenia bez znajomości implementacji urządzenia źródłowego. Ten przewodnik Szybki start pokazuje, jak używać oprogramowania Java do łączenia się z urządzeniem Typu Plug and Play IoT podłączonym do rozwiązania i sterowania nim.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten szybki start, potrzebujesz java SE 8 na komputerze deweloperskim. Musisz również zainstalować Maven 3.

Aby uzyskać szczegółowe informacje na temat konfigurowania tych funkcji, zobacz [Przygotowywanie środowiska programistycznego](https://github.com/Azure/azure-iot-sdk-java/blob/preview/doc/java-devbox-setup.md) w zestawie SDK urządzenia IoT platformy Microsoft Azure dla środowiska Java.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Uruchom następujące polecenie, aby uzyskać _parametry połączenia centrum IoT_ dla koncentratora (uwaga do późniejszego użycia):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="run-the-sample-device"></a>Uruchamianie przykładowego urządzenia

W tym przewodniku Szybki start można użyć przykładowego czujnika środowiskowego napisanego w języku Java jako urządzenia Typu Plug and Play IoT. Poniższe instrukcje pokazują, jak zainstalować i uruchomić urządzenie:

1. Otwórz okno terminala w wybranym katalogu. Wykonaj następujące polecenie, aby sklonować [przykłady usługi Azure IoT dla](https://github.com/Azure-Samples/azure-iot-samples-java) repozytorium Java GitHub w tej lokalizacji:

    ```cmd/sh
    git clone https://github.com/Azure-Samples/azure-iot-samples-java
    ```

1. To okno terminalu będzie teraz używane jako terminal _urządzenia._ Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-samples-java/digital-twin/Samples/device/JdkSample folderu.Go to the folder of your cloned repozytorium and navigate to the /azure-iot-samples-java/digital-twin/Samples/device/JdkSample** folder. Zainstaluj wymagane biblioteki i skompiluj symulowaną aplikację urządzenia, uruchamiając następujące polecenie:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Konfigurowanie _ciągu połączenia urządzenia:_

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. Uruchom następujące polecenie, aby uruchomić próbkę z folderu urządzenia.

    ```cmd/sh
    java -jar environmental-sensor-sample\target\environmental-sensor-sample-with-deps.jar
    ```

1. Są widoczne komunikaty informujące, że urządzenie jest połączone, wykonywanie różnych kroków konfiguracji i oczekiwanie na aktualizacje usługi, a następnie dzienniki telemetrii. Oznacza to, że urządzenie jest teraz gotowe do odbierania poleceń i aktualizacji właściwości i rozpoczęło wysyłanie danych telemetrycznych do koncentratora. Zachowaj uruchomienie próbki podczas wykonywania kolejnych kroków. Nie zamykaj tego terminalu, będziesz go potrzebować później, aby potwierdzić, że próbki usług również zadziałały.

## <a name="run-the-sample-solution"></a>Uruchamianie roztworu próbki

W tym przewodniku Szybki start można użyć przykładowego rozwiązania IoT w języku Java do interakcji z przykładowym urządzeniem.

1. Otwórz kolejne okno terminala (będzie to twój terminal _serwisowy)._ Przejdź do folderu sklonowanego repozytorium i przejdź do folderu **/azure-iot-samples-java\digital-twin\Samples\service\JdkSample** folderu.

1. Zainstaluj wymagane biblioteki i skompiluj przykład usługi, uruchamiając następujące polecenie:

    ```cmd/sh
    mvn clean install -DskipTests
    ```

1. Skonfiguruj parametry połączenia i _identyfikator urządzenia_ _koncentratora IoT,_ aby umożliwić usłudze łączenie się z obydwoma z nich:

    ```cmd/sh
    set IOTHUB_CONNECTION_STRING=<YourIoTHubConnectionString>
    set DEVICE_ID=<YourDeviceID>
    ```

### <a name="read-a-property"></a>Odczytanie właściwości

1. Po podłączeniu _urządzenia_ do terminalu jednym z komunikatów wyjściowych był następujący komunikat wskazujący jego stan online. Właściwość, `state` która służy do wskazania, czy urządzenie jest w trybie online, jest _prawdziwa:_

    ```cmd/sh
    State of environmental sensor was set to true
    ```

1. Przejdź do terminalu _serwisowego_ i użyj następującego polecenia, aby uruchomić przykład usługi do odczytu informacji o urządzeniu:

    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```

1. W wyjściu terminala _serwisowego_ przewiń do składnika. `environmentalSensor` Widzisz, że `state` właściwość została zgłoszona jako _true:_
    ```JSON
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
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

1. Użyj następującego polecenia, aby uruchomić przykład usługi do aktualizowania właściwości:

    ```cmd/sh
    java -jar update-digital-twin/target/update-digital-twin-with-deps.jar
    ```

1. Dane wyjściowe terminala _serwisowego_ pokazują zaktualizowane informacje o urządzeniu. Przewiń `environmentalSensor` do komponentu, aby zobaczyć nową wartość jasności 42.

    ```json
    "environmentalSensor": {
        "name": "environmentalSensor",
        "properties": {
            "brightness": {
                "reported": null,
                "desired": {
                    "value": "42"
                }
            },
    ```

1. Przejdź do _terminalu urządzenia,_ zobaczysz, że urządzenie otrzymało aktualizację:

    ```cmd/sh
    OnPropertyUpdate called: propertyName=brightness, reportedValue=null, desiredVersion=2, desiredValue={"value":"42"}
    Report property: propertyName=brightness, reportedValue={"value":"42"}, desiredVersion=2 was DIGITALTWIN_CLIENT_OK
    ```
2. Wróć do terminalu _serwisowego_ i uruchom poniższe polecenie, aby ponownie uzyskać informacje o urządzeniu, aby potwierdzić, że właściwość została zaktualizowana.
    
    ```cmd/sh
    java -jar get-digital-twin/target/get-digital-twin-with-deps.jar
    ```
3. W wyjściu terminala `environmentalSensor` _serwisowego_ pod komponentem zostanie wyświetlona zaktualizowana wartość jasności. Uwaga: może upłynąć trochę czasu, aby urządzenie ukończyło aktualizację. Można powtórzyć ten krok, dopóki urządzenie faktycznie przetworzy aktualizację właściwości.
    
    ```json
    "environmentalSensor" : {
      "name" : "environmentalSensor",
      "properties" : {
        "brightness" : {
          "reported" : {
            "value" : {
              "value" : "42"
            },
            "desiredState" : {
              "code" : 200,
              "version" : 2,
              "description" : "OK"
            }
          },
          "desired" : {
            "value" : "42"
          }
        },
        "state" : {
          "reported" : {
            "value" : true,
            "desiredState" : null
          },
          "desired" : null
        }
      }
    },       
    ```

### <a name="invoke-a-command"></a>Wywoływanie polecenia

1. Przejdź do terminalu _serwisowego_ i ustaw następujące zmienne, aby zdefiniować, które polecenie ma wywołać:
    ```cmd/sh
    set INTERFACE_INSTANCE_NAME=environmentalSensor
    set COMMAND_NAME=blink
    set PAYLOAD=10
    ```

1. Użyj następującego polecenia, aby uruchomić przykład usługi do wywoływania polecenia:

    ```cmd/sh
    java -jar invoke-digital-twin-command/target/invoke-digital-twin-command-with-deps.jar
    ```

1. Wyjście w terminalu _serwisowym_ powinno być wyświetlane następujące potwierdzenie:

    ```cmd/sh
    Invoking blink on device <YourDeviceID> with interface instance name environmentalSensor
    Command invoked on the device successfully, the returned status was 200 and the request id was <some ID value>
    The returned PAYLOAD was
    ```

1. Przejdź do terminalu _urządzenia,_ zobaczysz, że polecenie zostało potwierdzone:

    ```cmd/sh
    OnCommandReceived called: commandName=blink, requestId=<some ID value>, commandPayload="10"
    EnvironmentalSensor is blinking every 10 seconds.
    ```

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start dowiesz się, jak podłączyć urządzenie Typu Plug and Play do rozwiązania IoT. Aby dowiedzieć się więcej o tworzeniu rozwiązania współdziałanego z urządzeniami IoT Plug and Play, zobacz:

> [!div class="nextstepaction"]
> [Instrukcje: łączenie się z urządzeniem i interakcję z nim](howto-develop-solution.md)
