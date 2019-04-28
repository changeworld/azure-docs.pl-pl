---
title: Połącz ogólnego klienta aplikacji Node.js usługi Azure IoT Central | Dokumentacja firmy Microsoft
description: Jako deweloper urządzenia jak połączyć ogólny urządzenia środowiska Node.js do aplikacji usługi Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 5497e4956fbdc74eced302867c33a66d07d6a184
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888945"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Łączenie aplikacji klienckiej ogólnego aplikację usługi Azure IoT Central (Node.js)

W tym artykule opisano jak Deweloper urządzenia do łączenia z ogólnych aplikacji Node.js reprezentujący rzeczywistego urządzenia do aplikacji Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja usługi Azure IoT Central. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Jest maszyna deweloperska z [Node.js](https://nodejs.org/) wersji 4.0.0 lub nowszej. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

W aplikacji usługi Azure IoT Central potrzebne są szablon urządzenia z pomiarów następujące właściwości urządzenia, ustawienia i polecenia:

### <a name="telemetry-measurements"></a>Pomiary dotyczące prawdziwych danych telemetrycznych

Dodaj następujące dane telemetryczne na **pomiarów** strony:

| Nazwa wyświetlana | Nazwa pola  | Jednostki | Min. | Maks. | Miejsca dziesiętne |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperature | F     | 60  | 110 | 0              |
| Wilgotność     | humidity    | %     | 0   | 100 | 0              |
| Ciśnienie     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Typ danych miary telemetrii jest zmiennoprzecinkowy numer punktu.

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenia, dane telemetryczne nie można wyświetlić w aplikacji.

### <a name="state-measurements"></a>Pomiary stanu

Dodaj następujący stan na **pomiarów** strony:

| Nazwa wyświetlana | Nazwa pola  | Wartość 1 | Nazwa wyświetlana | Wartość 2 | Nazwa wyświetlana |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Tryb wentylatora     | fanmode     | 1       | Działanie      | 0       | Zatrzymano      |

> [!NOTE]
> Typ danych miary stanu jest ciągiem.

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenia, stan, nie można wyświetlić w aplikacji.

### <a name="event-measurements"></a>Zdarzenie pomiarów

Dodaj następujące zdarzenie na **pomiarów** strony:

| Nazwa wyświetlana | Nazwa pola  | Ważność |
| ------------ | ----------- | -------- |
| Przegrzaniu  | przegrzeje się    | Błąd    |

> [!NOTE]
> Typ danych miary zdarzeń jest ciągiem.

### <a name="device-properties"></a>Właściwości urządzenia

Dodaj następujące właściwości urządzenia na **właściwości** strony:

| Nazwa wyświetlana        | Nazwa pola        | Typ danych |
| ------------------- | ----------------- | --------- |
| Numer seryjny       | serialNumber      | tekst      |
| Producent urządzenia | producent      | tekst      |

Wprowadź nazwy pól dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenia, właściwości nie można wyświetlić w aplikacji.

### <a name="settings"></a>Ustawienia

Dodaj następujący kod **numer** ustawień na **ustawienia** strony:

| Nazwa wyświetlana    | Nazwa pola     | Jednostki | Miejsca dziesiętne | Min. | Maks.  | Początkowa |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Wentylator szybkości       | fanSpeed       | obr. / min   | 0        | 0   | 3000 | 0       |
| Ustaw temperaturę | setTemperature | F     | 0        | 20  | 200  | 80      |

Wprowadź nazwę pola dokładnie tak jak pokazano w tabeli do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenie, urządzenie nie może odbierać wartości ustawienia.

### <a name="commands"></a>Polecenia

Dodaj następujące polecenie na **polecenia** strony:

| Nazwa wyświetlana    | Nazwa pola     | Domyślny limit czasu | Typ danych |
| --------------- | -------------- | --------------- | --------- |
| Odliczanie       | Odliczanie      | 30              | numer    |

Dodaj następujące pola wejściowego, do polecenia odliczania:

| Nazwa wyświetlana    | Nazwa pola     | Typ danych | Wartość |
| --------------- | -------------- | --------- | ----- |
| Są liczone od      | countFrom      | numer    | 10    |

Wprowadź nazwy pól dokładnie tak jak pokazano w tabelach do szablonu urządzenia. Jeśli nazwy pól są niezgodne nazwy właściwości w odpowiednim kodzie urządzenie, urządzenie nie może przetworzyć polecenia.

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji usługi Azure IoT Central należy dodać do szablonu urządzenia, który został utworzony w poprzedniej sekcji rzeczywistego urządzenia.

Następnie postępuj zgodnie z instrukcjami w samouczku "Dodaj urządzenia", aby [Generowanie ciąg połączenia dla rzeczywistego urządzenia](tutorial-add-device.md#generate-connection-string). Można użyć tych parametrów połączenia w poniższej sekcji:

### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js

Poniższe kroki pokazują jak utworzyć aplikację kliencką, która implementuje rzeczywistego urządzenia, które dodanych do aplikacji. W tym miejscu aplikacji Node.js reprezentuje rzeczywistego urządzenia. 

1. Utwórz folder o nazwie `connected-air-conditioner-adv` na maszynie. Przejdź do tego folderu w środowisku wiersza polecenia.

1. Aby zainicjować projektu środowiska Node.js, uruchom następujące polecenia:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Utwórz plik o nazwie **connectedAirConditionerAdv.js** w `connected-air-conditioner-adv` folderu.

1. Dodaj następujący kod `require` instrukcji na początku **connectedAirConditionerAdv.js** pliku:

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

    Aktualizuj symbol zastępczy `{your device connection string}` z [parametry połączenia urządzenia](tutorial-add-device.md#generate-connection-string). W tym przykładzie należy zainicjować `targetTemperature` na zero, można użyć bieżącej odczytu z urządzenia lub wartość z bliźniaczej reprezentacji urządzenia.

1. Aby wysłać dane telemetryczne, stanu i zdarzenie pomiarów do aplikacji usługi Azure IoT Central, dodaj następującą funkcję do pliku:

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

1. Aby wysłać właściwości urządzenia z aplikacją usługi Azure IoT Central, dodaj następującą funkcję do pliku:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Aby zdefiniować ustawienia, które odpowiada urządzenie, dodaj następującą definicję:

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

1. Aby obsłużyć zaktualizowanych ustawień z poziomu aplikacji usługi Azure IoT Central, Dodaj następujący element do pliku:

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

1. Dodaj następujący kod do obsługi polecenia odliczania wysyłane z aplikacji IoT Central:

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');

      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;

      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          client.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }

            doCountdown();
          });
        }
      });
    }
    ```

1. Dodaj następujący kod, aby umożliwić nawiązanie połączenia z usługą Azure IoT Central i dołączyć funkcje do kodu klienta:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        client.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (connect it to Azure IoT Central).
    client.open(connectCallback);
    ```

## <a name="run-your-nodejs-application"></a>Uruchamianie aplikacji Node.js

W środowisku wiersza polecenia, uruchom następujące polecenie:

```cmd/sh
node connectedAirConditionerAdv.js
```

Operator w aplikacji usługi Azure IoT Central rzeczywistego urządzenia możesz wykonywać następujące czynności:

* Wyświetlanie danych telemetrycznych na **pomiarów** strony:

    ![Wyświetlanie danych telemetrycznych](media/howto-connect-nodejs/viewtelemetry.png)

* Wyświetlanie wartości właściwości urządzenia, które są wysyłane z urządzenia **właściwości** strony. Właściwości urządzenia Kafelki aktualizacji, gdy urządzenia przenośnego:

    ![Wyświetl właściwości urządzenia](media/howto-connect-nodejs/viewproperties.png)

* Ustaw wentylator temperatury szybkość i docelowej z **ustawienia** strony:

    ![Wentylator prędkości](media/howto-connect-nodejs/setfanspeed.png)

* Wywołaj polecenie odliczania **polecenia** strony:

    ![Odliczanie wywołań — polecenie](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, wyjaśniono, jak połączyć ogólnego klienta Node.js do aplikacji usługi Azure IoT Central, sugerowane następnym krokiem jest Dowiedz się, jak [Konfigurowanie szablonu niestandardowego urządzenia](howto-set-up-template.md) dla urządzenia IoT.
