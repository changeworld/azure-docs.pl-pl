---
title: Połącz ogólnego aplikacji klienckiej Node.js do usługi Azure IoT centralnej | Dokumentacja firmy Microsoft
description: Deweloper urządzenia jak połączyć ogólnego urządzenia Node.js do aplikacji Azure IoT centralnej.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 42ede975f2cfde2d9c0a61d15ba1af412a88c556
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628542"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Łączenie aplikacji klienckiej ogólnych do aplikacji Azure IoT centralnej (Node.js)

W tym artykule opisano sposób Deweloper urządzenia nawiązać ogólnego aplikacji Node.js reprezentujący urządzenie fizyczne do aplikacji Microsoft Azure IoT centralnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja Azure IoT centralnej. Aby uzyskać więcej informacji, zobacz [tworzenie aplikacji centralnej Azure IoT](howto-create-application.md).
1. Maszyna programowanie z [Node.js](https://nodejs.org/) wersji 4.0.0 lub nowszej. Można uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

W aplikacji Azure IoT centralną należy szablonu urządzenia z poniższe pomiarów i zdefiniowane właściwości urządzenia:

### <a name="telemetry-measurements"></a>Pomiary telemetrii

Dodaj następujące dane telemetryczne w **pomiary** strony:

| Nazwa wyświetlana | Nazwa pola  | Jednostki | Min. | Maks. | Po przecinku |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperature | F     | 60  | 110 | 0              |
| Wilgotność     | wilgotność    | %     | 0   | 100 | 0              |
| Ciśnienie     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
  Typ danych miary telemetrii jest dwa razy.

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne, telemetrii nie można wyświetlić w aplikacji.

### <a name="state-measurements"></a>Pomiary stanu

Dodaj następujący stan w **pomiary** strony:

| Nazwa wyświetlana | Nazwa pola  | Wartość 1 | Nazwa wyświetlana | Wartość 2 | Nazwa wyświetlana |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Tryb wentylatora     | fanmode     | 1       | Działanie      | 0       | Zatrzymano      |

> [!NOTE]
  Typ danych miary stanu jest ciągiem.

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne, stan nie można wyświetlić w aplikacji.

### <a name="event-measurements"></a>Pomiary zdarzeń

Dodaj następujące zdarzenie w **pomiary** strony:

| Nazwa wyświetlana | Nazwa pola  | Ważność |
| ------------ | ----------- | -------- |
| Przegrzaniu  | przegrzeje się    | Błąd    |

> [!NOTE]
  Typ danych miary zdarzeń jest ciągiem.

### <a name="device-properties"></a>Właściwości urządzenia

Dodaj następujące właściwości urządzenia w **stronę właściwości**:

| Nazwa wyświetlana        | Nazwa pola        | Typ danych |
| ------------------- | ----------------- | --------- |
| Numer seryjny       | serialNumber      | tekst      |
| Producent urządzenia | producent      | tekst      |

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne, aplikacja nie może wyświetlić wartość właściwości.

### <a name="settings"></a>Ustawienia

Dodaj następujące **numer** ustawienia w **strony ustawień**:

| Nazwa wyświetlana    | Nazwa pola     | Jednostki | Liczba cyfr dziesiętnych | Min. | Maks.  | Początkowa |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Wentylator szybkości       | fanSpeed       | obr. / min   | 0        | 0   | 3000 | 0       |
| Ustaw temperaturę | setTemperature | F     | 0        | 20  | 200  | 80      |

Wprowadź nazwę pola dokładnie tak jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne, urządzenie nie może otrzymywać wartości ustawień.

### <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji Azure IoT centralnej Dodaj rzeczywiste urządzenie z urządzenia szablonu tworzenia i zanotuj parametry połączenia urządzenia. Aby uzyskać więcej informacji, zobacz [dodawania rzeczywistego urządzenia do aplikacji Azure IoT centralnej](tutorial-add-device.md)

## <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js

Poniższe kroki przedstawiają sposób tworzenia aplikacji klienckiej, który implementuje rzeczywiste urządzenie dodane do aplikacji.

1. Utwórz folder o nazwie `connected-air-conditioner-adv` na maszynie. Przejdź do tego folderu w środowisku wiersza polecenia.

1. Aby zainicjować projektu Node.js, uruchom następujące polecenia:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Utwórz plik o nazwie **connectedAirConditionerAdv.js** w `connected-air-conditioner-adv` folderu.

1. Dodaj następujące `require` instrukcje na początku **connectedAirConditionerAdv.js** pliku:

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. Dodaj następujące deklaracje zmiennych do pliku:

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    Zaktualizuj symbol zastępczy `{your device connection string}` z parametrów połączenia urządzenia. Na stronie szczegółów połączenia będą kopiowane tej wartości, po dodaniu prawdziwe urządzenia. W tym przykładzie możemy zainicjować `targetTemperature` do zera, można opcjonalnie wykonać odczyt bieżącego z urządzenia lub wartości z dwie urządzenia. 

1. Aby wysłać dane telemetryczne, stanu i zdarzeń pomiarów do aplikacji Azure IoT centralnej, dodaj następującą funkcję do pliku:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var data = JSON.stringify({ 
        temperature: temperature, 
        humidity: humidity, 
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    1. Aby wysłać właściwości urządzenia Azure IoT centralnej aplikacji, należy dodać do pliku następujących funkcji:

    ```javascript
    // Send device properties.
    function sendDeviceProperties(twin) {
      var properties = {
        serialNumber: '123-ABC',
        manufacturer: 'Contoso'
      };
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Aby zdefiniować ustawienia, które odpowiada urządzenia, dodaj następującą definicję:

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Aby obsługiwać zaktualizowanych ustawień aplikacji Azure IoT centralnej, dodaj następującą wartość do pliku:

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. Dodaj następujące polecenie, aby nawiązać połączenie Azure IoT centralnej i dołączenie funkcji w kodzie klienta:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Uruchom aplikację Node.js

Uruchom następujące polecenie w wierszu polecenia środowiska:

```cmd/sh
node connectedAirConditionerAdv.js
```

Operator w aplikacji Azure IoT centralnej rzeczywistego urządzenia może:

* Wyświetl dane telemetryczne na **pomiary** strony:

    ![Wyświetlanie danych telemetrycznych](media/howto-connect-nodejs/viewtelemetry.png)

* Wyświetl wartości właściwości urządzenia wysyłane z urządzenia **właściwości** strony.

    ![Wyświetl właściwości urządzenia](media/howto-connect-nodejs/viewproperties.png)

* Ustaw temperatury szybkość i obiekt docelowy wentylator z **ustawienia** strony.

    ![Wentylator prędkości](media/howto-connect-nodejs/setfanspeed.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz sposobu łączenia ogólnego klienta Node.js do aplikacji Azure IoT centralnej, Oto Sugerowane następne kroki:
* [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
<!-- Next how-tos in the sequence -->
