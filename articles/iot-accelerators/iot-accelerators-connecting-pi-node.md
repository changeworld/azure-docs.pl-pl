---
title: Aprowizowanie urządzenia Raspberry Pi do zdalnego monitorowania w języku Node.js — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób łączenia urządzeń Raspberry Pi do akceleratora rozwiązania monitorowania zdalnego przy użyciu aplikacji napisanych w języku Node.js.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 01/24/2018
ms.author: dobett
ms.openlocfilehash: 20d50ac4ac4a1919077ebe67bb529e2dc5abf187
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61449696"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-nodejs"></a>Łączenie urządzenia Raspberry Pi do akceleratora rozwiązań zdalnego monitorowania (Node.js)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ten samouczek pokazuje, jak połączyć prawdziwe urządzenie do akceleratora rozwiązania monitorowania zdalnego. W tym samouczku użyjesz środowiska Node.js, czyli dobra opcja w środowiskach z ograniczeniami minimalny zasobów.

Jeśli wolisz symulowanie urządzenia, zobacz [tworzenie i testowanie nowych symulowanych urządzeń](iot-accelerators-remote-monitoring-create-simulated-device.md).

### <a name="required-hardware"></a>Wymagany sprzęt

Komputer stacjonarny, aby włączyć zdalne łączenie się z wiersza polecenia na urządzenia Raspberry Pi.

[Pakiet startowy IoT firmy Microsoft do Raspberry Pi 3](https://azure.microsoft.com/develop/iot/starter-kits/) lub równoważne składników. W tym samouczku korzysta z następujących elementów z zestawu SDK:

- Raspberry Pi 3
- Karta MicroSD (z NOOBS)
- Kabel USB Mini
- Kabel Ethernet

### <a name="required-desktop-software"></a>Wymagane oprogramowania dla komputerów stacjonarnych

Klient SSH jest niezbędne w komputera stacjonarnego, aby umożliwić dostęp zdalny do wiersza polecenia na urządzenia Raspberry Pi.

- Windows nie zawiera klienta SSH. Firma Microsoft zaleca używanie [PuTTY](https://www.putty.org/).
- Większość dystrybucje systemu Linux i Mac OS obejmują narzędzia wiersza polecenia SSH. Aby uzyskać więcej informacji, zobacz [SSH przy użyciu systemu Linux lub Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md).

### <a name="required-raspberry-pi-software"></a>Wymagane oprogramowanie urządzenia Raspberry Pi

Jeśli użytkownik jeszcze tego nie zrobiono, zainstaluj środowisko Node.js w wersji 4.0.0 lub później na urządzenia Raspberry Pi. Poniższe kroki pokazują, jak zainstalować środowisko Node.js w wersji 6 na urządzenia Raspberry Pi:

1. Łączenie z urządzeniem Raspberry Pi z wykorzystaniem `ssh`. Aby uzyskać więcej informacji, zobacz [SSH (Secure Shell)](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md) na [witryny sieci Web urządzenia Raspberry Pi](https://www.raspberrypi.org/).

1. Aby zaktualizować urządzenia Raspberry Pi, użyj następującego polecenia:

    ```sh
    sudo apt-get update
    ```

1. Aby usunąć wszelkie istniejącej instalacji środowiska Node.js z urządzenia Raspberry Pi, użyj następujących poleceń:

    ```sh
    sudo apt-get remove nodered -y
    sudo apt-get remove nodejs nodejs-legacy -y
    sudo apt-get remove npm  -y
    ```

1. Aby pobrać i zainstalować środowisko Node.js w wersji 6 na urządzenia Raspberry Pi, użyj następującego polecenia:

    ```sh
    curl -sL https://deb.nodesource.com/setup_6.x | sudo bash -
    sudo apt-get install nodejs npm
    ```

1. Użyj następującego polecenia, aby sprawdzić, czy pomyślnie zainstalowano Node.js v6.11.4:

    ```sh
    node --version
    ```

## <a name="create-a-nodejs-solution"></a>Tworzenie rozwiązania środowiska Node.js

Wykonaj następujące czynności za pomocą `ssh` połączenie urządzenia Raspberry Pi:

1. Utwórz folder o nazwie `remotemonitoring` w folderze głównym na urządzenia Raspberry Pi. Przejdź do tego folderu, w wierszu polecenia:

    ```sh
    cd ~
    mkdir remotemonitoring
    cd remotemonitoring
    ```

1. Aby pobrać i zainstalować te pakiety, należy wykonać przykładową aplikację, uruchom następujące polecenia:

    ```sh
    npm install async azure-iot-device azure-iot-device-mqtt
    ```

1. W `remotemonitoring` folderze utwórz plik o nazwie **remote_monitoring.js**. Otwórz ten plik w edytorze tekstu. Urządzenia Raspberry Pi, mogą używać `nano` lub `vi` edytorów tekstu.

1. W **remote_monitoring.js** plików, Dodaj następujący kod `require` instrukcji:

    ```javascript
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var async = require('async');
    ```

1. Dodaj następujące deklaracje zmiennych po instrukcji `require`. Zastąp wartość symbolu zastępczego `{device connection string}` wartością zanotowaną urządzenia aprowizowanej w rozwiązaniu monitorowania zdalnego:

    ```javascript
    var connectionString = '{device connection string}';
    ```

1. Aby zdefiniować niektórych danych telemetrycznych podstawowej, Dodaj następujące zmienne:

    ```javascript
    var temperature = 50;
    var temperatureUnit = 'F';
    var humidity = 50;
    var humidityUnit = '%';
    var pressure = 55;
    var pressureUnit = 'psig';
    ```

1. Aby zdefiniować niektóre wartości właściwości, Dodaj następujące zmienne:

    ```javascript
    var schema = "real-chiller;v1";
    var deviceType = "RealChiller";
    var deviceFirmware = "1.0.0";
    var deviceFirmwareUpdateStatus = "";
    var deviceLocation = "Building 44";
    var deviceLatitude = 47.638928;
    var deviceLongitude = -122.13476;
    var deviceOnline = true;
    ```

1. Dodaj następującą zmienną do definiowania zgłaszanych właściwości do wysłania do rozwiązania. Te właściwości obejmują metadanych do wyświetlania w Interfejsie użytkownika sieci Web:

    ```javascript
    var reportedProperties = {
      "SupportedMethods": "Reboot,FirmwareUpdate,EmergencyValveRelease,IncreasePressure",
      "Telemetry": {
        [schema]: ""
      },
      "Type": deviceType,
      "Firmware": deviceFirmware,
      "FirmwareUpdateStatus": deviceFirmwareUpdateStatus,
      "Location": deviceLocation,
      "Latitude": deviceLatitude,
      "Longitude": deviceLongitude,
      "Online": deviceOnline
    }
    ```

1. Aby wydrukować wyniki operacji, dodaj następującą funkcję pomocnika:

    ```javascript
    function printErrorFor(op) {
        return function printError(err) {
            if (err) console.log(op + ' error: ' + err.toString());
        };
    }
    ```

1. Dodaj następującą funkcję pomocnika na potrzeby losowe wartości telemetryczne:

     ```javascript
     function generateRandomIncrement() {
         return ((Math.random() * 2) - 1);
     }
     ```

1. Dodaj następującą funkcję ogólnego, aby obsłużyć wywołania metody bezpośredniej z rozwiązania. Funkcja wyświetla informacje o bezpośrednie metody, która została wywołana, ale w tym przykładzie nie powoduje modyfikacji urządzenia w dowolny sposób. Rozwiązanie używa metod bezpośrednich do działania na urządzeniach:

     ```javascript
     function onDirectMethod(request, response) {
       // Implement logic asynchronously here.
       console.log('Simulated ' + request.methodName);

       // Complete the response
       response.send(200, request.methodName + ' was called on the device', function (err) {
         if (err) console.error('Error sending method response :\n' + err.toString());
         else console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');
       });
     }
     ```

1. Dodaj następującą funkcję, aby obsłużyć **FirmwareUpdate** bezpośrednie wywołania metody z rozwiązania. Funkcja sprawdza parametrów przekazanych w ładunku metody bezpośredniej, a następnie asynchronicznie uruchamia symulacji aktualizacji oprogramowania układowego:

     ```javascript
     function onFirmwareUpdate(request, response) {
       // Get the requested firmware version from the JSON request body
       var firmwareVersion = request.payload.Firmware;
       var firmwareUri = request.payload.FirmwareUri;
      
       // Ensure we got a firmware values
       if (!firmwareVersion || !firmwareUri) {
         response.send(400, 'Missing firmware value', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else console.log('400 Response to method \'' + request.methodName + '\' sent successfully.');
         });
       } else {
         // Respond the cloud app for the device method
         response.send(200, 'Firmware update started.', function(err) {
           if (err) console.error('Error sending method response :\n' + err.toString());
           else {
             console.log('200 Response to method \'' + request.methodName + '\' sent successfully.');

             // Run the simulated firmware update flow
             runFirmwareUpdateFlow(firmwareVersion, firmwareUri);
           }
         });
       }
     }
     ```

1. Dodaj następującą funkcję, aby symulować przepływ aktualizacji oprogramowania układowego długotrwałych, który zgłasza postępy pracy powrót do rozwiązania:

     ```javascript
     // Simulated firmwareUpdate flow
     function runFirmwareUpdateFlow(firmwareVersion, firmwareUri) {
       console.log('Simulating firmware update flow...');
       console.log('> Firmware version passed: ' + firmwareVersion);
       console.log('> Firmware URI passed: ' + firmwareUri);
       async.waterfall([
         function (callback) {
           console.log("Image downloading from " + firmwareUri);
           var patch = {
             FirmwareUpdateStatus: 'Downloading image..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Downloaded, applying firmware " + firmwareVersion);
           deviceOnline = false;
           var patch = {
             FirmwareUpdateStatus: 'Applying firmware..',
             Online: false
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(8000, callback);
         },
         function (callback) {
           console.log("Rebooting");
           var patch = {
             FirmwareUpdateStatus: 'Rebooting..'
           };
           reportUpdateThroughTwin(patch, callback);
           sleep(10000, callback);
         },
         function (callback) {
           console.log("Firmware updated to " + firmwareVersion);
           deviceOnline = true;
           var patch = {
             FirmwareUpdateStatus: 'Firmware updated',
             Online: true,
             Firmware: firmwareVersion
           };
           reportUpdateThroughTwin(patch, callback);
           callback(null);
         }
       ], function(err) {
         if (err) {
           console.error('Error in simulated firmware update flow: ' + err.message);
         } else {
           console.log("Completed simulated firmware update flow");
         }
       });

       // Helper function to update the twin reported properties.
       function reportUpdateThroughTwin(patch, callback) {
         console.log("Sending...");
         console.log(JSON.stringify(patch, null, 2));
         client.getTwin(function(err, twin) {
           if (!err) {
             twin.properties.reported.update(patch, function(err) {
               if (err) callback(err);
             });      
           } else {
             if (err) callback(err);
           }
         });
       }

       function sleep(milliseconds, callback) {
         console.log("Simulate a delay (milleseconds): " + milliseconds);
         setTimeout(function () {
           callback(null);
         }, milliseconds);
       }
     }
     ```

1. Dodaj następujący kod do wysyłania danych telemetrycznych do rozwiązania. Aplikacja kliencka dodaje właściwości w oknie komunikatu identyfikowanie schematów komunikatów:

     ```javascript
     function sendTelemetry(data, schema) {
       if (deviceOnline) {
         var d = new Date();
         var payload = JSON.stringify(data);
         var message = new Message(payload);
         message.properties.add('iothub-creation-time-utc', d.toISOString());
         message.properties.add('iothub-message-schema', schema);

         console.log('Sending device message data:\n' + payload);
         client.sendEvent(message, printErrorFor('send event'));
       } else {
         console.log('Offline, not sending telemetry');
       }
     }
     ```

1. Dodaj następujący kod, aby utworzyć wystąpienie klienta:

     ```javascript
     var client = Client.fromConnectionString(connectionString, Protocol);
     ```

1. Dodaj następujący kod, aby:

    * Otwórz połączenie.
    * Skonfiguruj program obsługi żądane właściwości.
    * Wysyłać zgłaszane właściwości.
    * Zarejestruj procedury obsługi dla metod bezpośrednich. W przykładzie użyto oddzielny program obsługi dla metody bezpośredniej aktualizacji oprogramowania układowego.
    * Rozpoczęcie wysyłania danych telemetrycznych.

      ```javascript
      client.open(function (err) {
      if (err) {
        printErrorFor('open')(err);
      } else {
        // Create device Twin
        client.getTwin(function (err, twin) {
          if (err) {
            console.error('Could not get device twin');
          } else {
            console.log('Device twin created');

            twin.on('properties.desired', function (delta) {
              // Handle desired properties set by solution
              console.log('Received new desired properties:');
              console.log(JSON.stringify(delta));
            });

            // Send reported properties
            twin.properties.reported.update(reportedProperties, function (err) {
              if (err) throw err;
              console.log('Twin state reported');
            });

            // Register handlers for all the method names we are interested in.
            // Consider separate handlers for each method.
            client.onDeviceMethod('Reboot', onDirectMethod);
            client.onDeviceMethod('FirmwareUpdate', onFirmwareUpdate);
            client.onDeviceMethod('EmergencyValveRelease', onDirectMethod);
            client.onDeviceMethod('IncreasePressure', onDirectMethod);
          }
        });

        // Start sending telemetry
        var sendDeviceTelemetry = setInterval(function () {
          temperature += generateRandomIncrement();
          pressure += generateRandomIncrement();
          humidity += generateRandomIncrement();
          var data = {
            'temperature': temperature,
            'temperature_unit': temperatureUnit,
            'humidity': humidity,
            'humidity_unit': humidityUnit,
            'pressure': pressure,
            'pressure_unit': pressureUnit
          };
          sendTelemetry(data, schema)
        }, 5000);

        client.on('error', function (err) {
          printErrorFor('client')(err);
          if (sendTemperatureInterval) clearInterval(sendTemperatureInterval);
          if (sendHumidityInterval) clearInterval(sendHumidityInterval);
          if (sendPressureInterval) clearInterval(sendPressureInterval);
          client.close(printErrorFor('client.close'));
        });
      }
      });
      ```

1. Czy zapisać zmiany **remote_monitoring.js** pliku.

1. Aby uruchomić przykładową aplikację, uruchom następujące polecenie w wierszu polecenia na urządzenia Raspberry Pi:

     ```sh
     node remote_monitoring.js
     ```

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
