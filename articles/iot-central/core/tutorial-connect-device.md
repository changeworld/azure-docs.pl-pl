---
title: Samouczek — łączenie ogólnej aplikacji klienckiej Node. js z usługą Azure IoT Central | Microsoft Docs
description: W tym samouczku pokazano, jak deweloper urządzenia łączy urządzenie z uruchomioną aplikacją kliencką Node. js w aplikacji IoT Central platformy Azure. Szablon urządzenia można utworzyć przez zaimportowanie modelu możliwości urządzenia i dodanie widoków, które umożliwiają współpracę z podłączonym urządzeniem
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624543"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej Node. js z aplikacją IoT Central platformy Azure (Node. js)

W tym samouczku pokazano, jak programista urządzeń ma połączyć aplikację kliencką Node. js z aplikacją IoT Central platformy Azure. Aplikacja Node. js symuluje zachowanie rzeczywistego urządzenia. Aby utworzyć _szablon urządzenia_ w IoT Central, należy użyć przykładowego _modelu możliwości urządzenia_ czujnika środowiska. Do szablonu urządzenia dodawane są widoki, które umożliwiają wizualizację danych telemetrycznych urządzeń, zarządzanie właściwościami urządzeń i używanie poleceń do sterowania urządzeniami.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaimportuj model możliwości urządzenia, aby utworzyć szablon urządzenia.
> * Dodawanie domyślnych i niestandardowych widoków do szablonu urządzenia.
> * Opublikuj szablon urządzenia i Dodaj rzeczywiste urządzenie do aplikacji IoT Central.
> * Utwórz i uruchom kod urządzenia Node. js i sprawdź, czy jest on połączony z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne wysyłane przez urządzenie.
> * Użyj widoku, aby zarządzać właściwościami urządzeń.
> * Wywoływanie poleceń w celu sterowania urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja platformy Azure IoT Central utworzona przy użyciu szablonu * * aplikacja niestandardowa * *. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Komputer deweloperski z zainstalowanym środowiskiem [Node. js](https://nodejs.org/) w wersji 10.0.0 lub nowszej. Aby sprawdzić swoją wersję, możesz uruchomić `node --version` w wierszu polecenia. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych. W instrukcjach przedstawionych w tym samouczku założono, że uruchomiono polecenie **Node** w wierszu polecenia systemu Windows. Środowiska Node. js można używać w różnych systemach operacyjnych.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

Utwórz folder o nazwie `environmental-sensor` na komputerze lokalnym.

Pobierz plik JSON [modelu możliwości czujnika środowiska](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) i Zapisz go w folderze `environmental-sensor`.

Użyj edytora tekstów, aby zastąpić dwa wystąpienia `{YOUR_COMPANY_NAME_HERE}` nazwą swojej firmy w pobranym pliku `EnvironmentalSensorInline.capabilitymodel.json`.

W aplikacji IoT Central platformy Azure Utwórz szablon urządzenia o nazwie *czujnik środowiska* , importując plik modelu możliwości urządzenia `EnvironmentalSensorInline.capabilitymodel.json`:

![Szablon urządzenia z zaimportowanym modelem możliwości urządzenia](./media/tutorial-connect-device/device-template.png)

Model możliwości urządzenia obejmuje dwa interfejsy: standardowy interfejs **informacji o urządzeniu** i niestandardowy interfejs **czujnika środowiska** . Interfejs **czujnika środowiska** definiuje następujące możliwości:

| Typ | Nazwa wyświetlana | Opis |
| ---- | ------------ | ----------- |
| Właściwość | Stan urządzenia     | Stan urządzenia. Dostępne są dwa stany online/offline. |
| Właściwość | Nazwa klienta    | Nazwa klienta, na którym aktualnie działa urządzenie. |
| Właściwość | Poziom jasności | Poziom jasności światła na urządzeniu. Może być określona jako 1 (wysoka), 2 (średnia), 3 (niska). |
| Telemetria | Temperatura | Bieżąca temperatura na urządzeniu. |
| Telemetria | Wilgotność    | Bieżąca wilgotność na urządzeniu. |
| Polecenie | wskaźnika          | Rozpocznij miganie diody LED dla danego interwału czasu. |
| Polecenie | wlacza         | Włącz sygnalizator diody LED na urządzeniu. |
| Polecenie | Wyłączanie        | Wyłącz oświetlenie LED na urządzeniu. |
| Polecenie | rundiagnostics | To polecenie uruchamia przebieg diagnostyczny. |

Aby dostosować sposób wyświetlania właściwości **stanu urządzenia** w aplikacji IoT Central, wybierz pozycję **Dostosuj** w szablonie urządzenia. Rozwiń wpis **stanu urządzenia** , wprowadź _online_ jako **prawdziwą nazwę** i w _trybie offline_ jako **wartość false**. Następnie Zapisz zmiany:

![Dostosowywanie szablonu urządzenia](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Tworzenie widoków

Widoki umożliwiają współpracę z urządzeniami połączonymi z aplikacją IoT Central. Na przykład można mieć widoki, które wyświetlają dane telemetryczne, widoki, które wyświetlają właściwości i widoki umożliwiające edytowanie właściwości do zapisu i w chmurze. Widoki są częścią szablonu urządzenia.

Aby dodać widoki domyślne do szablonu urządzenia **czujnika środowiska** , przejdź do szablonu urządzenia, wybierz pozycję **widoki**i wybierz kafelek **Generuj widoki domyślne** . Upewnij się, że **Przegląd** i **informacje** są **włączone**, a następnie wybierz pozycję **Generuj domyślne widoki pulpitu nawigacyjnego**. Masz teraz dwa domyślne widoki zdefiniowane w szablonie.

Interfejs **czujnika środowiska** obejmuje dwie właściwości do zapisu — **nazwę klienta** i **poziom jasności**. Aby utworzyć widok, można użyć programu do edytowania następujących właściwości:

1. Wybierz pozycję **widoki** , a następnie wybierz kafelek **Edytowanie urządzenia i danych w chmurze** .

1. Wprowadź _Właściwości_ jako nazwę formularza.

1. Wybierz **poziom jasności** i właściwości **nazwy klienta** . Następnie wybierz pozycję **Dodaj sekcję**.

1. Zapisz zmiany.

![Dodaj widok, aby włączyć edycję właściwości](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publikowanie szablonu

Przed dodaniem urządzenia do aplikacji IoT Central, która używa szablonu urządzenia **czujnika środowiska** , należy opublikować szablon.

W szablonie urządzenia wybierz pozycję **Publikuj**. Na panelu, który pokazuje zmiany do opublikowania, wybierz pozycję **Publikuj**.

Aby sprawdzić, czy szablon jest gotowy do użycia, przejdź do strony **urządzenia** w aplikacji IoT Central. Sekcja **urządzenia** zawiera listę opublikowanych urządzeń w aplikacji:

![Opublikowane szablony na stronie urządzeń](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji IoT Central platformy Azure Dodaj rzeczywiste urządzenie do szablonu urządzenia utworzonego w poprzedniej sekcji:

1. Na stronie **urządzenia** wybierz szablon urządzenia **czujnika środowiska** .

1. Wybierz pozycję **+ Nowy**.

1. Upewnij się, że **symulowane** jest **wyłączone**. Następnie wybierz przycisk **Utwórz**.

Kliknij nazwę urządzenia, a następnie wybierz pozycję **Połącz**. Zanotuj informacje o połączeniu urządzenia w **zakresie identyfikatora**strony **połączenia urządzenia** , **identyfikatora urządzenia**i **klucza podstawowego**. Te wartości są potrzebne podczas tworzenia kodu urządzenia:

![Informacje o połączeniu z urządzeniem](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js

Poniższe kroki pokazują, jak utworzyć aplikację kliencką Node. js, która implementuje rzeczywiste urządzenie dodane do aplikacji. Ta aplikacja Node. js symuluje zachowanie rzeczywistego urządzenia.

1. W środowisku wiersza polecenia przejdź do utworzonego wcześniej folderu `environmental-sensor`.

1. Aby zainicjować projekt node. js i zainstalować wymagane zależności, uruchom następujące polecenia — Zaakceptuj wszystkie opcje domyślne podczas uruchamiania `npm init`:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Utwórz plik o nazwie **environmentalSensor. js** w folderze `environmental-sensor`.

1. Dodaj następujące instrukcje `require` na początku pliku **environmentalSensor. js** :

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Dodaj następujące deklaracje zmiennych do pliku:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    var ledOn = true;
    ```

    Zaktualizuj symbole zastępcze `{your Scope ID}`, `{your Device ID}`i `{your Primary Key}` z wartościami, które zostały wcześniej wykonane. W tym przykładzie zainicjowano `targetTemperature` do zera, można użyć bieżącego odczytu z urządzenia lub wartości z sznurka urządzenia.

1. Aby wysłać dane telemetryczne do aplikacji IoT Central platformy Azure, Dodaj następującą funkcję do pliku:

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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

1. Aby zdefiniować i obsłużyć zapisywalne właściwości, na które odpowiada urządzenie, Dodaj następujący kod:

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
      }, 5000);
      }
    };

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
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
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. Dodaj następujący kod, aby obsłużyć polecenia wysyłane z aplikacji IoT Central:

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
    function turnOn(request, response) {
      console.log('Received synchronous call to turn on LED');
      if(!ledOn){
        console.log('Turning on the LED');
        ledOn = true;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle LED turn off command
    function turnOff(request, response) {
      console.log('Received synchronous call to turn off LED');
      if(ledOn){
        console.log('Turning off the LED');
        ledOn = false;
      }
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        }
      });
    }

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
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
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);
        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Uruchamianie aplikacji node. js

Aby uruchomić aplikację kliencką urządzenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
node environmentalSensor.js
```

Możesz zobaczyć, że urządzenie nawiązuje połączenie z aplikacją IoT Central platformy Azure i zacznie wysyłać dane telemetryczne:

![Uruchom aplikację kliencką](media/tutorial-connect-device/run-application.png)

Jako operator w aplikacji IoT Central platformy Azure możesz:

* Wyświetlanie telemetrii wysyłanej przez urządzenie na stronie **Przegląd** :

    ![Wyświetlanie danych telemetrycznych](media/tutorial-connect-device/view-telemetry.png)

* Zaktualizuj wartości zapisywalnych właściwości na stronie **Właściwości** :

    ![Aktualizuj właściwości](media/tutorial-connect-device/update-properties.png)

    ![Aktualizowanie właściwości urządzenia](media/tutorial-connect-device/update-properties-device.png)

* Wywołaj polecenia ze strony **poleceń** :

    ![Wywołanie migotania polecenia](media/tutorial-connect-device/call-command.png)

    ![Wywołanie migania urządzenia polecenia](media/tutorial-connect-device/call-command-device.png)

* Wyświetl właściwości urządzenia na stronie **informacje** :

    ![Wyświetl właściwości](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o modelach możliwości urządzeń i sposobach tworzenia własnych szablonów urządzeń, przejdź do przewodnika krok po kroku:

> [!div class="nextstepaction"]
> [Zdefiniuj nowy typ urządzenia IoT](./howto-set-up-template.md)
