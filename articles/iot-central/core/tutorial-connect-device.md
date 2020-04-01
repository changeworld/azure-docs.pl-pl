---
title: Samouczek — łączenie ogólnej aplikacji klienckiej Node.js z usługą Azure IoT Central | Dokumenty firmy Microsoft
description: W tym samouczku pokazano, jak jako deweloper urządzeń połączyć urządzenie z uruchomieniem aplikacji klienckiej Node.js z aplikacją Azure IoT Central. Szablon urządzenia można utworzyć, importując model możliwości urządzenia i dodaj widoki, które umożliwiają interakcję z podłączonym urządzeniem
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624543"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>Samouczek: Tworzenie i łączenie aplikacji klienckiej Node.js z aplikacją Central usługi Azure IoT (Node.js)

W tym samouczku pokazano, jak jako deweloper urządzeń połączyć aplikację kliencką Node.js z aplikacją Azure IoT Central. Aplikacja Node.js symuluje zachowanie rzeczywistego urządzenia. Przykładowego _modelu funkcji urządzenia_ dla urządzenia z czujnikiem środowiska służy do tworzenia _szablonu urządzenia_ w centrum IoT. Widoki są dodawanye do szablonu urządzenia, aby wizualizować dane telemetryczne urządzenia, zarządzać właściwościami urządzenia i używać poleceń do sterowania urządzeniami.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Zaimportowanie modelu możliwości urządzenia w celu utworzenia szablonu urządzenia.
> * Dodawanie widoków domyślnych i niestandardowych do szablonu urządzenia.
> * Opublikuj szablon urządzenia i dodaj prawdziwe urządzenie do aplikacji IoT Central.
> * Utwórz i uruchom kod urządzenia Node.js i zobacz, jak łączy się z aplikacją IoT Central.
> * Wyświetl symulowane dane telemetryczne, które wysyła urządzenie.
> * Użyj widoku, aby zarządzać właściwościami urządzenia.
> * Powołuje się na polecenia, aby sterować urządzeniem.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków opisanych w tym artykule potrzebne są:

* Aplikacja Azure IoT Central utworzona przy użyciu szablonu **Aplikacji niestandardowej **. Aby uzyskać więcej informacji, zapoznaj się z [przewodnikiem Szybki start dotyczącym tworzenia aplikacji](quick-deploy-iot-central.md).
* Komputer dewelopera z [zainstalowanym node.js](https://nodejs.org/) w wersji 10.0.0 lub nowszej. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych. Instrukcje w tym samouczku zakładają, że używasz polecenia **węzła** w wierszu polecenia systemu Windows. Funkcji Node.js można używać w różnych systemach operacyjnych.

## <a name="create-a-device-template"></a>Tworzenie szablonu urządzenia

Utwórz folder `environmental-sensor` wywoływany na komputerze lokalnym.

Pobierz plik JSON [dotyczący modelu czujnika środowiskowego](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) i zapisz go w folderze. `environmental-sensor`

Użyj edytora tekstu, aby zastąpić `{YOUR_COMPANY_NAME_HERE}` dwa wystąpienia nazwą `EnvironmentalSensorInline.capabilitymodel.json` firmy w pobranym pliku.

W aplikacji Azure IoT Central utwórz szablon urządzenia o `EnvironmentalSensorInline.capabilitymodel.json` nazwie *Czujnik środowiska,* importując plik modelu możliwości urządzenia:

![Szablon urządzenia z zaimportowanym modelem możliwości urządzenia](./media/tutorial-connect-device/device-template.png)

Model możliwości urządzenia zawiera dwa interfejsy: standardowy interfejs **informacji o urządzeniu** i niestandardowy interfejs **czujnika środowiska.** Interfejs **czujnika środowiska** definiuje następujące możliwości:

| Typ | Nazwa wyświetlana | Opis |
| ---- | ------------ | ----------- |
| Właściwość | Stan urządzenia     | Stan urządzenia. Dostępne są dwa stany w trybie online/offline. |
| Właściwość | Nazwa klienta    | Nazwa klienta aktualnie obsługującego urządzenie. |
| Właściwość | Poziom jasności | Poziom jasności światła na urządzeniu. Może być określony jako 1 (wysoki), 2 (średni), 3 (niski). |
| Telemetria | Temperatura | Aktualna temperatura w urządzeniu. |
| Telemetria | Wilgotność    | Aktualna wilgotność urządzenia. |
| Polecenie | Blink          | Zacznij migać diodą LED dla danego przedziału czasowego. |
| Polecenie | turnon         | Włącz diodę LED na urządzeniu. |
| Polecenie | Zjazd        | Wyłącz diodę LED na urządzeniu. |
| Polecenie | diagnostyka rundiagnostyka | To polecenie uruchamia uruchomienie diagnostyki. |

Aby dostosować sposób wyświetlania właściwości **Stan urządzenia** w aplikacji IoT Central, wybierz pozycję **Dostosuj** w szablonie urządzenia. Rozwiń wpis **Stan urządzenia,** wprowadź _online_ jako **nazwę True** i _offline_ jako **fałszywą nazwę**. Następnie zapisz zmiany:

![Dostosowywanie szablonu urządzenia](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>Tworzenie widoków

Widoki umożliwiają interakcję z urządzeniami podłączonymi do aplikacji IoT Central. Na przykład można mieć widoki, które wyświetlają dane telemetryczne, widoki, które wyświetlają właściwości i widoki, które umożliwiają edycję właściwości zapisywalnych i chmury. Widoki są częścią szablonu urządzenia.

Aby dodać widoki domyślne do szablonu urządzenia **czujnika środowiska,** przejdź do szablonu urządzenia, wybierz pozycję **Widoki**i wybierz kafelek **Generuj widoki domyślne.** Upewnij się, że **przegląd** i **informacje** są **włączone**, a następnie wybierz pozycję **Generuj domyślne widoki pulpitu nawigacyjnego**. W szablonie zdefiniowano teraz dwa widoki domyślne.

Interfejs **czujnika środowiskowego** zawiera dwie zapisywalne właściwości - **nazwę klienta** i poziom **jasności.** Aby utworzyć widok, można użyć do edycji tych właściwości:

1. Wybierz **pozycję Widoki,** a następnie wybierz polecenie Edytowanie urządzenia i kafelek **danych w chmurze.**

1. Wprowadź _właściwości_ jako nazwę formularza.

1. Wybierz właściwości **Poziom jasności** i **Nazwa klienta.** Następnie wybierz pozycję **Dodaj sekcję**.

1. Zapisz zmiany.

![Dodawanie widoku w celu umożliwienia edycji właściwości](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>Publikowanie szablonu

Przed dodaniem urządzenia do aplikacji IoT Central, która używa szablonu urządzenia **czujnika środowiska,** należy opublikować szablon.

W szablonie urządzenia wybierz pozycję **Publikuj**. Na panelu, na który widać zmiany, które mają zostać opublikowane, wybierz pozycję **Publikuj**.

Aby sprawdzić, czy szablon jest gotowy do użycia, przejdź do strony **Urządzenia** w aplikacji IoT Central. Sekcja **Urządzenia** zawiera listę opublikowanych urządzeń w aplikacji:

![Opublikowane szablony na stronie urządzenia](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

W aplikacji Azure IoT Central dodaj rzeczywiste urządzenie do szablonu urządzenia utworzonego w poprzedniej sekcji:

1. Na stronie **Urządzenia** wybierz szablon **czujnika środowiska.**

1. Wybierz **+ Nowy**.

1. Upewnij się, że **symulacja** jest **wyłączona**. Następnie wybierz pozycję **Utwórz**.

Kliknij nazwę urządzenia, a następnie wybierz pozycję **Połącz**. Zanotuj informacje o połączeniu urządzenia na stronie **Połączenie urządzenia** — **zakres identyfikatora,** **identyfikator urządzenia**i **klucz podstawowy**. Podczas tworzenia kodu urządzenia potrzebne są następujące wartości:

![Informacje o połączeniu urządzenia](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji w języku Node.js

Poniższe kroki pokazują, jak utworzyć aplikację kliencką Node.js, która implementuje rzeczywiste urządzenie dodane do aplikacji. Ta aplikacja Node.js symuluje zachowanie rzeczywistego urządzenia.

1. W środowisku wiersza polecenia `environmental-sensor` przejdź do folderu utworzonego wcześniej.

1. Aby zainicjować projekt Node.js i zainstalować wymagane zależności, uruchom następujące polecenia - zaakceptuj `npm init`wszystkie opcje domyślne po uruchomieniu:

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Utwórz plik o nazwie **environmentalSensor.js** w folderze. `environmental-sensor`

1. Dodaj następujące `require` instrukcje na początku pliku **environmentalSensor.js:**

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

    Zaktualizuj `{your Scope ID}` `{your Device ID}`symbole `{your Primary Key}` zastępcze , a także wartościami, które zostały wcześniej zanotowane. W tym przykładzie `targetTemperature` zainicjować do zera, można użyć bieżącego odczytu z urządzenia lub wartość z bliźniaczej reprezentacji urządzenia.

1. Aby wysłać dane telemetryczne do aplikacji Usługi Azure IoT Central, dodaj do pliku następującą funkcję:

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

1. Aby wysłać właściwości urządzenia do aplikacji Azure IoT Central, dodaj do pliku następującą funkcję:

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. Aby zdefiniować i obsłużyć zapisywalne właściwości, na które odpowiada urządzenie, dodaj następujący kod:

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

1. Dodaj następujący kod do obsługi poleceń wysyłanych z aplikacji IoT Central:

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

## <a name="run-your-nodejs-application"></a>Uruchamianie aplikacji Node.js

Aby uruchomić aplikację kliencką urządzenia, uruchom następujące polecenie w środowisku wiersza polecenia:

```cmd/sh
node environmentalSensor.js
```

Możesz zobaczyć, że urządzenie łączy się z aplikacją Azure IoT Central i rozpoczyna wysyłanie danych telemetrycznych:

![Uruchamianie aplikacji klienckiej](media/tutorial-connect-device/run-application.png)

Jako operator w aplikacji Azure IoT Central możesz:

* Wyświetlanie danych telemetrycznych wysyłanych przez urządzenie na stronie **Przegląd:**

    ![Wyświetlanie danych telemetrycznych](media/tutorial-connect-device/view-telemetry.png)

* Aktualizowanie wartości właściwości zapisywalnych na stronie **Właściwości:**

    ![Aktualizuj właściwości](media/tutorial-connect-device/update-properties.png)

    ![Aktualizowanie właściwości urządzenia](media/tutorial-connect-device/update-properties-device.png)

* Wywołaj polecenia ze strony **Polecenia:**

    ![Wywołanie polecenia blink](media/tutorial-connect-device/call-command.png)

    ![Zawołanie migać urządzenie poleceń](media/tutorial-connect-device/call-command-device.png)

* Wyświetlanie właściwości urządzenia na stronie **Informacje:**

    ![Wyświetl właściwości](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o modelach możliwości urządzeń i o tym, jak tworzyć własne szablony urządzeń, przejdź do przewodnika jak:

> [!div class="nextstepaction"]
> [Definiowanie nowego typu urządzenia IoT](./howto-set-up-template.md)
