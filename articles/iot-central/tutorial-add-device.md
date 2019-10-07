---
title: Dodawanie rzeczywistego urządzenia do aplikacji usługi Azure IoT Central | Microsoft Docs
description: Jako operator Dodaj rzeczywiste urządzenie do aplikacji IoT Central platformy Azure.
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2673d0d2c1cb174316e99a79a10a67347e2bc031
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001352"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Samouczek: Dodawanie rzeczywistego urządzenia do aplikacji IoT Central platformy Azure

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

W tym samouczku pokazano, jak dodać i skonfigurować *rzeczywiste urządzenie* do aplikacji IoT Central Microsoft Azure. W tym samouczku pokoduje rzeczywiste urządzenie przy użyciu środowiska Node. js i uruchamiasz kod na komputerze stacjonarnym. Do ukończenia tego samouczka nie jest wymagane oddzielne urządzenie IoT, takie jak Raspberry Pi lub zestawu deweloperskiego IoT DevKit.

Ten samouczek składa się z dwóch części:

* Po pierwsze, jako operator, dowiesz się, jak dodać i skonfigurować rzeczywiste urządzenie w aplikacji IoT Central platformy Azure. Na końcu tej części pobierasz parametry połączenia do użycia w drugiej części.
* Następnie jako deweloper urządzenia znajdziesz informacje o kodzie w Twoim rzeczywistym urządzeniu. Należy dodać parametry połączenia z pierwszej części do przykładowego kodu.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Dodaj nowe rzeczywiste urządzenie
> * Skonfiguruj rzeczywiste urządzenie
> * Pobierz parametry połączenia dla rzeczywistego urządzenia z aplikacji
> * Informacje o sposobie mapowania kodu klienta do aplikacji
> * Konfigurowanie kodu klienta dla rzeczywistego urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem, Konstruktor powinien ukończyć co najmniej pierwszy samouczek konstruktora, aby utworzyć aplikację usługi Azure IoT Central: [Zdefiniuj nowy typ urządzenia](tutorial-define-device-type.md) (wymagane)

Zainstaluj program [Node. js](https://nodejs.org/) w wersji 8.0.0 lub nowszej na komputerze deweloperskim. Aby sprawdzić swoją wersję, można uruchomić `node --version` w wierszu polecenia. Node. js jest dostępny dla wielu różnych systemów operacyjnych.

## <a name="add-a-real-device"></a>Dodaj rzeczywiste urządzenie

Aby dodać rzeczywiste urządzenie do aplikacji, należy użyć szablonu urządzenia **Conditioner połączonego środowiska AIR** utworzonego w samouczku [Definiowanie nowego typu urządzenia](tutorial-define-device-type.md) .

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **urządzenia** w menu nawigacji po lewej stronie:

   ![Strona Eksploratora urządzeń pokazująca podłączony warunek powietrza](media/tutorial-add-device/explorer.png)

   **Device Explorer** przedstawia szablon urządzenia z **warunkiem podłączonego powietrza** oraz symulowane urządzenie. Podczas tworzenia szablonu urządzenia IoT Central automatycznie tworzy symulowane urządzenie.

2. Zwróć uwagę, że szablon urządzenia z **warunkiem załączonego powietrza** to ten, który został wybrany w **Device Explorer**. Aby rozpocząć łączenie urządzenia z warunkiem środowiska AIR, które używa tego szablonu, wybierz **+** , a następnie pozycję **prawdziwe**:

   ![Zacznij dodawać nowe, rzeczywiste połączone urządzenie klimatyzacyjne](media/tutorial-add-device/newreal.png)

3. Wprowadź własny **Identyfikator urządzenia** (powinien być pisany małymi literami) lub użyj sugerowanej wartości. Możesz również wprowadzić **nazwę urządzenia** dla nowego urządzenia i wybrać pozycję **Utwórz**.

   ![Zmień nazwę urządzenia](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurowanie rzeczywistego urządzenia

Rzeczywiste urządzenie jest tworzone na podstawie szablonu urządzenia **związanego z warunkiem środowiska AIR** . **Ustawienia** umożliwiają skonfigurowanie urządzenia i ustawienie wartości właściwości w celu rejestrowania informacji o urządzeniu.

1. Na stronie **Ustawienia** należy zauważyć, że ustawienie **Ustawianie temperatury** **nie ma aktualizacji**. Pozostaje w tym stanie, dopóki rzeczywiste urządzenie nie połączy się z aplikacją i potwierdzi, że działał on na tym ustawieniu.

    ![Ustawienia pokazujące synchronizację](media/tutorial-add-device/settingssyncing.png)

2. Na stronie **Właściwości** nowego urządzenia rzeczywistego, obie lokalizacje usługi i Data ostatniej usługi są edytowalnymi właściwościami. Pola numer seryjny i wersja oprogramowania układowego są puste, dopóki urządzenie nie zostanie podłączone do aplikacji. Te wartości tylko do odczytu są wysyłane z urządzenia i nie można ich edytować.

    ![Właściwości urządzenia dla rzeczywistego urządzenia](media/tutorial-add-device/setproperties1.png)

3. Możesz wyświetlić strony **pomiarów**, **reguł**i **pulpitów nawigacyjnych** dla rzeczywistego urządzenia.

## <a name="prepare-the-client-code"></a>Przygotuj kod klienta

Przykładowy kod w tym artykule został zapisany w języku [Node. js](https://nodejs.org/) i zawiera wystarczającą ilość kodu dla urządzenia:

* Połącz się z aplikacją IoT Central platformy Azure.
* Wysyłaj dane telemetryczne dotyczące temperatury jako połączone urządzenie klimatyzacyjne.
* Wyślij właściwości urządzenia do aplikacji IoT Central platformy Azure.
* Odpowiedz do operatora, który używa ustawienia **temperatury** .
* Obsłuż polecenie echo w aplikacji IoT Central platformy Azure.

Artykuły wymienione w sekcji [następne kroki](#next-steps) zawierają więcej kompletnych przykładów i są wyświetlane w innych językach programowania. Aby uzyskać więcej informacji na temat sposobu łączenia urządzeń z usługą Azure IoT Central, zobacz artykuł dotyczący [łączności urządzeń](concepts-connectivity.md) .

Poniższe kroki pokazują, jak przygotować przykład środowiska [Node. js](https://nodejs.org/) :

### <a name="get-the-device-connection-information"></a>Pobierz informacje o połączeniu z urządzeniem

1. Parametry połączenia dla wystąpienia urządzenia w aplikacji są generowane na podstawie informacji o urządzeniu udostępnianych przez IoT Central.

   Na ekranie urządzenia dla rzeczywistego połączonego warunku Air wybierz pozycję **Połącz**.

   ![Strona urządzenia przedstawia link Wyświetl informacje o połączeniu](media/tutorial-add-device/connectionlink.png)

1. Na stronie **połączenie urządzenia** Zanotuj **Identyfikatory zakresu**, **Identyfikator urządzenia**i wartości **klucza podstawowego** . Te wartości są używane w dalszej części tego samouczka.

   ![Szczegóły połączenia](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Przygotowywanie projektu Node. js

1. Utwórz folder o nazwie `connectedairconditioner` na komputerze deweloperskim.

1. W środowisku wiersza polecenia przejdź do utworzonego folderu `connectedairconditioner`.

1. Aby zainicjować projekt node. js, uruchom następujące polecenie akceptujące wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
      ```

1. Aby zainstalować wymagane pakiety, uruchom następujące polecenie:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Za pomocą edytora tekstów Utwórz plik o nazwie **ConnectedAirConditioner. js** w folderze `connectedairconditioner`.

1. Dodaj następujące instrukcje `require` na początku pliku **ConnectedAirConditioner. js** :

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Dodaj następujące deklaracje zmiennych do pliku. Zastąp symbole zastępcze `{your Scope ID}`, `{your Device ID}` i `{your Primary Key}` z zanotowanymi wcześniej informacjami o połączeniu z urządzeniem:

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. Zapisz zmiany wprowadzone do tej pory, ale pozostaw plik otwarty.

## <a name="review-client-code"></a>Przegląd kodu klienta

W poprzedniej sekcji utworzono szkielet środowiska Node. js dla aplikacji urządzenia, która nawiązuje połączenie z aplikacją IoT Central platformy Azure. Następnym krokiem jest dodanie kodu do:

* Połącz się z aplikacją IoT Central platformy Azure.
* Wyślij dane telemetryczne do aplikacji IoT Central platformy Azure.
* Wyślij właściwości urządzenia do aplikacji IoT Central platformy Azure.
* Odbieranie ustawień z aplikacji IoT Central platformy Azure.
* Obsłuż polecenie echo w aplikacji IoT Central platformy Azure.

1. Aby wysłać dane telemetryczne dotyczące temperatury do aplikacji IoT Central platformy Azure, Dodaj następujący kod do pliku **ConnectedAirConditioner. js** :

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    Nazwa pola w wysyłanym formacie JSON musi być zgodna z nazwą pola określonego dla telemetrii temperatury w szablonie urządzenia. W tym przykładzie nazwa pola jest **temperaturą**.

1. Aby wysłać właściwości urządzenia, takie jak **wersja oprogramowania układowego** i numer **seryjny**, Dodaj następującą definicję:

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. Aby zdefiniować ustawienia obsługiwane przez urządzenie, takie jak **Settemperatura**, Dodaj następującą definicję:

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Aby obsłużyć ustawienia wysyłane z usługi Azure IoT Central, Dodaj następującą funkcję, która lokalizuje i wykonuje odpowiedni kod urządzenia:

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

    Ta funkcja:

    * Czujki na platformie Azure IoT Central wysyłania żądanej właściwości.
    * Lokalizuje odpowiednią funkcję do wywołania, aby obsłużyć zmianę ustawienia.
    * Wysyła potwierdzenie z powrotem do aplikacji IoT Central platformy Azure.

1. Aby odpowiedzieć na polecenie, takie jak **echo** z aplikacji IoT Central platformy Azure, Dodaj następującą definicję:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
    }
    ```

1. Dodaj następujący kod, aby zakończyć połączenie z usługą Azure IoT Central i podłączyć funkcje w kodzie klienta:

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
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
    ```

1. Zarejestruj urządzenie i połącz je z aplikacją IoT Central:

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. Zapisz wprowadzone zmiany.

## <a name="run-the-client-code"></a>Uruchamianie kodu klienta

Teraz możesz uruchomić kod klienta i zobaczyć, jak współdziała z aplikacją IoT Central:

1. Aby uruchomić przykład, wprowadź następujące polecenie w środowisku wiersza polecenia:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Upewnij się, że w folderze `connectedairconditioner` znajduje się okno po uruchomieniu tego polecenia.

1. Aplikacja drukuje dane wyjściowe w konsoli programu:

   ![Dane wyjściowe aplikacji klienta](media/tutorial-add-device/output.png)

1. Po około 30 sekundach zobaczysz dane telemetryczne na stronie **pomiarów** urządzeń:

   ![Realna Telemetria](media/tutorial-add-device/realtelemetry.png)

1. Na stronie **Ustawienia** można zobaczyć, że ustawienie jest teraz zsynchronizowane. Gdy urządzenie jest po raz pierwszy podłączone, odebrano wartość ustawienia i potwierdzono zmianę:

   ![Ustawienie zsynchronizowane](media/tutorial-add-device/settingsynced.png)

1. Na stronie **Ustawienia** ustaw wartość w polu temperatura urządzenia na **95** , a następnie wybierz pozycję **Aktualizuj urządzenie**. Twoja Przykładowa aplikacja odbiera i przetwarza tę zmianę:

   ![Ustawienia odbierania i przetwarzania](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Istnieją dwie komunikaty "Ustawianie aktualizacji". Jeden po wysłaniu stanu `pending` i po wysłaniu stanu `completed`.

1. Na stronie **pomiary** można sprawdzić, czy urządzenie wysyła wyższe wartości temperatury:

    ![Dane telemetryczne temperatury są teraz wyższe](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania tych instrukcji:

> [!div class="nextstepaction"]
> * Dodaj nowe rzeczywiste urządzenie
> * Skonfiguruj nowe urządzenie
> * Pobierz parametry połączenia dla rzeczywistego urządzenia z aplikacji
> * Informacje o sposobie mapowania kodu klienta do aplikacji
> * Konfigurowanie kodu klienta dla rzeczywistego urządzenia

Teraz, gdy połączono rzeczywiste urządzenie z aplikacją IoT Central platformy Azure, Oto kilka sugerowanych następnych kroków.

Jako operator można dowiedzieć się, jak:

* [Zarządzanie urządzeniami](howto-manage-devices.md)
* [Korzystanie z zestawów urządzeń](howto-use-device-sets.md)
* [Tworzenie analizy niestandardowej](howto-use-device-sets.md)

Jako programista urządzeń można dowiedzieć się, jak:

* [Przygotowywanie i łączenie urządzenia DevKit (C)](howto-connect-devkit.md)
* [Przygotowywanie i łączenie Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Przygotuj i Połącz Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Przygotowanie i podłączenie urządzenia z systemem Windows 10 IoT CoreC#()](howto-connect-windowsiotcore.md)
* [Łączenie ogólnego klienta Node. js z aplikacją IoT Central platformy Azure](howto-connect-nodejs.md)
