---
title: Łączenie ogólnej aplikacji klienckiej Node. js z platformą Azure IoT Central | Microsoft Docs
description: Jako deweloper urządzenia dołączają ogólne urządzenie Node. js do aplikacji IoT Central platformy Azure.
author: dominicbetts
ms.author: dobett
ms.date: 06/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3b73344a233182fe8366795cfa111b706c6d06ac
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876256"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Łączenie ogólnej aplikacji klienckiej z aplikacją usługi Azure IoT Central (Node. js)

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym artykule opisano sposób, w jaki deweloper urządzenia nawiązuje połączenie ogólnej aplikacji node. js reprezentującej rzeczywiste urządzenie z aplikacją IoT Central Microsoft Azure.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Do wykonania kroków opisanych w tym artykule potrzebne są:

1. Aplikacja IoT Central platformy Azure. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
1. Komputer deweloperski z zainstalowanym środowiskiem [Node. js](https://nodejs.org/) w wersji 4.0.0 lub nowszej. Aby sprawdzić swoją `node --version` wersję, można uruchomić polecenie w wierszu polecenia. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

W aplikacji IoT Central platformy Azure potrzebny jest szablon urządzenia z następującymi pomiarami:, właściwościami, ustawieniami i poleceniami.

### <a name="telemetry-measurements"></a>Pomiary telemetrii

Na stronie **pomiary** Dodaj następujące dane telemetryczne:

| Nazwa wyświetlana | Nazwa pola  | Jednostki | Min. | Maks. | Miejsca dziesiętne |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| Temperatura  | temperature | P     | 60  | 110 | 0              |
| Wilgotność     | humidity    | %     | 0   | 100 | 0              |
| Ciśnienie     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> Typ danych pomiaru telemetrii jest liczbą zmiennoprzecinkową.

Wprowadź nazwy pól dokładnie tak, jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne z nazwami właściwości w odpowiednim kodzie urządzenia, dane telemetryczne nie mogą być wyświetlane w aplikacji.

### <a name="state-measurements"></a>Pomiary stanu

Na stronie **pomiary** należy dodać następujący stan:

| Nazwa wyświetlana | Nazwa pola  | Wartość 1 | Nazwa wyświetlana | Wartość 2 | Nazwa wyświetlana |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Tryb wentylatora     | fanmode     | 1       | Działanie      | 0       | Zatrzymano      |

> [!NOTE]
> Typ danych pomiaru stanu to ciąg.

Wprowadź nazwy pól dokładnie tak, jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne z nazwami właściwości w odpowiednim kodzie urządzenia, stan nie może być wyświetlany w aplikacji.

### <a name="event-measurements"></a>Pomiary zdarzeń

Na stronie **pomiary** Dodaj następujące zdarzenie:

| Nazwa wyświetlana | Nazwa pola  | severity |
| ------------ | ----------- | -------- |
| Przegrzaniu  | overheat    | Błąd    |

> [!NOTE]
> Typ danych pomiaru zdarzenia to ciąg.

### <a name="location-measurements"></a>Pomiary lokalizacji

Na stronie **pomiary** Dodaj następujące pomiary lokalizacji:

| Nazwa wyświetlana | Nazwa pola  |
| ------------ | ----------- |
| Location     | location    |

Typ danych pomiar lokalizacji składa się z dwóch liczb zmiennoprzecinkowych dla długości geograficznej i szerokości geograficznej oraz opcjonalnej liczby zmiennoprzecinkowej dla wysokości.

Wprowadź nazwy pól dokładnie tak, jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne z nazwami właściwości w odpowiednim kodzie urządzenia, lokalizacja nie może zostać wyświetlona w aplikacji.

### <a name="device-properties"></a>Właściwości urządzenia

Na stronie **Właściwości** Dodaj następujące właściwości urządzenia:

| Nazwa wyświetlana        | Nazwa pola        | Typ danych |
| ------------------- | ----------------- | --------- |
| Numer seryjny       | serialNumber      | text      |
| Producent urządzenia | producent      | text      |

Wprowadź nazwy pól dokładnie tak, jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne z nazwami właściwości w odpowiednim kodzie urządzenia, właściwości nie mogą być wyświetlane w aplikacji.

### <a name="settings"></a>Ustawienia

Dodaj następujące ustawienia **liczbowe** na stronie **Ustawienia** :

| Nazwa wyświetlana    | Nazwa pola     | Jednostki | Miejsca dziesiętne | Min. | Maks.  | Początkowa |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| Szybkość wentylatorów       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Ustaw temperaturę | setTemperature | P     | 0        | 20  | 200  | 80      |

Wprowadź nazwę pola dokładnie tak, jak pokazano w tabeli w szablonie urządzenia. Jeśli nazwy pól nie są zgodne z nazwami właściwości w odpowiednim kodzie urządzenia, urządzenie nie może odebrać wartości ustawienia.

### <a name="commands"></a>Polecenia

Na stronie **polecenia** Dodaj następujące polecenie:

| Nazwa wyświetlana    | Nazwa pola     | Domyślny limit czasu | Typ danych |
| --------------- | -------------- | --------------- | --------- |
| Licz       | licz      | 30              | numer    |

Dodaj następujące pole wejściowe do polecenia odliczania:

| Nazwa wyświetlana    | Nazwa pola     | Typ danych | Value |
| --------------- | -------------- | --------- | ----- |
| Liczba od      | countFrom      | numer    | 10    |

Wprowadź nazwy pól dokładnie tak, jak pokazano w tabelach w szablonie urządzenia. Jeśli nazwy pól nie są zgodne z nazwami właściwości w odpowiednim kodzie urządzenia, urządzenie nie może przetworzyć polecenia.

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji IoT Central platformy Azure Dodaj rzeczywiste urządzenie do szablonu urządzenia utworzonego w poprzedniej sekcji.

Następnie postępuj zgodnie z instrukcjami podanymi w samouczku "Dodawanie urządzenia", aby [wygenerować parametry połączenia dla rzeczywistego urządzenia](tutorial-add-device.md#generate-connection-string). Te parametry połączenia są używane w następującej sekcji:

### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js

Poniższe kroki pokazują, jak utworzyć aplikację kliencką, która implementuje rzeczywiste urządzenie dodane do aplikacji. W tym miejscu aplikacja Node. js reprezentuje rzeczywiste urządzenie. 

1. Utwórz folder o nazwie `connected-air-conditioner-adv` na maszynie. Przejdź do tego folderu w środowisku wiersza polecenia.

1. Aby zainicjować projekt node. js, uruchom następujące polecenia:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Utwórz plik o nazwie **connectedAirConditionerAdv. js** w `connected-air-conditioner-adv` folderze.

1. Dodaj następujące `require` instrukcje na początku pliku **connectedAirConditionerAdv. js** :

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
    var locLong = -122.1215;
    var locLat = 47.6740;
    var client = clientFromConnectionString(connectionString);
    ```

    Zaktualizuj symbol zastępczy `{your device connection string}` za pomocą [parametrów połączenia urządzenia](tutorial-add-device.md#generate-connection-string). W tym przykładzie zainicjowano `targetTemperature` do zera, można użyć bieżącego odczytu z urządzenia lub wartości z sznurka urządzenia.

1. Aby wysyłać pomiary danych telemetrycznych, stanowych, zdarzeń i lokalizacji do aplikacji IoT Central platformy Azure, Dodaj następującą funkcję do pliku:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. Aby wysłać właściwości urządzenia do aplikacji IoT Central platformy Azure, Dodaj następującą funkcję do pliku:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Aby zdefiniować ustawienia, na które odpowiada urządzenie, Dodaj następującą definicję:

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

1. Aby obsłużyć zaktualizowane ustawienia w aplikacji IoT Central platformy Azure, Dodaj następujący plik do pliku:

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

1. Dodaj następujący kod, aby obsłużyć polecenie odliczania wysyłane z aplikacji IoT Central:

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

## <a name="run-your-nodejs-application"></a>Uruchamianie aplikacji node. js

Uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
node connectedAirConditionerAdv.js
```

Jako operator w aplikacji IoT Central platformy Azure, możesz:

* Wyświetlanie telemetrii na stronie **miary** :

    ![Wyświetlanie danych telemetrycznych](media/howto-connect-nodejs/viewtelemetry.png)

* Wyświetl lokalizację na stronie **pomiary** :

    ![Wyświetl pomiary lokalizacji](media/howto-connect-nodejs/viewlocation.png)

* Wyświetl wartości właściwości urządzenia wysyłane z urządzenia na stronie **Właściwości** . Kafelki właściwości urządzenia są aktualizowane po nawiązaniu połączenia z urządzeniem:

    ![Wyświetl właściwości urządzenia](media/howto-connect-nodejs/viewproperties.png)

* Ustaw prędkość dla wentylatora i docelową temperaturę ze strony **ustawień** :

    ![Ustaw szybkość wentylatora](media/howto-connect-nodejs/setfanspeed.png)

* Wywołaj polecenie odliczania ze strony **poleceń** :

    ![Wywołaj polecenie odliczania](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak połączyć ogólny klient Node. js z aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Konfigurowanie niestandardowego szablonu urządzenia](howto-set-up-template.md) dla własnego urządzenia IoT.
