---
title: 'Samouczek: Dodawanie rzeczywistego urządzenia do aplikacji IoT Central platformy Azure'
description: 'Samouczek: informacje na temat dodawania i konfigurowania rzeczywistego urządzenia w aplikacji IoT Central platformy Azure. Możesz również poznać kod na rzeczywistym urządzeniu.'
author: sandeeppujar
ms.author: sandeepu
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5234c371cc4a7238148915f4dcca1faac564f608
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108976"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Samouczek: dodawanie rzeczywistego urządzenia do aplikacji usługi Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

W tym samouczku pokazano, jak dodać i skonfigurować *rzeczywiste urządzenie* do aplikacji IoT Central Microsoft Azure. W tym samouczku pokoduje rzeczywiste urządzenie przy użyciu środowiska Node. js i uruchamiasz kod na komputerze stacjonarnym. Do ukończenia tego samouczka nie jest wymagane oddzielne urządzenie IoT, takie jak Raspberry Pi lub zestawu deweloperskiego IoT DevKit.

Ten samouczek składa się z dwóch części:

* Najpierw, jako operator, poznasz działania dodawania i konfigurowania rzeczywistego urządzenia w aplikacji usługi Azure IoT Central. Na końcu tej części pobierzesz parametry połączenia do użycia w drugiej części.
* Następnie, jako deweloper urządzenia, poznasz kod przeznaczony dla rzeczywistego urządzenia. Parametry połączenia z pierwszej części zostaną dodane do przykładowego kodu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowego rzeczywistego urządzenia
> * Konfigurowanie rzeczywistego urządzenia
> * Pobieranie parametrów połączenia dla rzeczywistego urządzenia z aplikacji
> * Poznawanie sposobu mapowania kodu klienta na aplikację
> * Konfigurowanie kodu klienta dla rzeczywistego urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem, Konstruktor powinien ukończyć co najmniej pierwszy samouczek konstruktora, aby utworzyć aplikację usługi Azure IoT Central: [Zdefiniuj nowy typ urządzenia](tutorial-define-device-type.md) (wymagane)

Zainstaluj program [Node. js](https://nodejs.org/) w wersji 8.0.0 lub nowszej na komputerze deweloperskim. Aby sprawdzić swoją wersję, możesz uruchomić `node --version` w wierszu polecenia. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

Aby dodać rzeczywiste urządzenie do aplikacji, należy użyć szablonu urządzenia **Połączony klimatyzator** utworzonego w ramach samouczka [Definiowanie nowego typu urządzenia](tutorial-define-device-type.md).

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **urządzenia** w lewym okienku:

   ![Strona Device Explorer pokazująca połączony klimatyzator](media/tutorial-add-device/explorer.png)

   **Device Explorer** przedstawia szablon urządzenia z **warunkiem podłączonego powietrza** oraz symulowane urządzenie. Podczas tworzenia szablonu urządzenia IoT Central automatycznie tworzy symulowane urządzenie.

2. Zwróć uwagę, że szablon urządzenia z **warunkiem załączonego powietrza** to ten, który został wybrany w **Device Explorer**. Aby rozpocząć łączenie urządzenia z warunkiem środowiska AIR, które używa tego szablonu, wybierz pozycję **+** , a następnie kliknij pozycję **prawdziwe**:

   ![Rozpoczynanie dodawania nowego rzeczywistego połączonego klimatyzatora](media/tutorial-add-device/newreal.png)

3. Wprowadź własny **Identyfikator urządzenia** (powinien być pisany małymi literami) lub użyj sugerowanej wartości. Możesz również wprowadzić **nazwę urządzenia** dla nowego urządzenia i wybrać pozycję **Utwórz**.

   ![Zmienianie nazwy urządzenia](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurowanie rzeczywistego urządzenia

Rzeczywiste urządzenie jest tworzone na podstawie szablonu urządzenia **Połączony klimatyzator**. Możesz użyć pozycji **Ustawienia**, aby skonfigurować urządzenie i ustawić wartości właściwości pod kątem rejestrowania informacji o urządzeniu.

1. Na stronie **Ustawienia** zwróć uwagę na to, że stan ustawienia **Ustaw temperaturę** to **brak aktualizacji**. Pozostanie ono w tym stanie do momentu, gdy rzeczywiste urządzenie połączy się z aplikacją i potwierdzi wykonanie działania dla ustawienia.

    ![Synchronizowanie ustawień](media/tutorial-add-device/settingssyncing.png)

2. Na stronie **Właściwości** nowego urządzenia rzeczywistego, obie lokalizacje usługi i Data ostatniej usługi są edytowalnymi właściwościami. Pola numeru seryjnego i wersji oprogramowania układowego są puste do momentu połączenia urządzenia z aplikacją. Te wartości tylko do odczytu są wysyłane z urządzenia i nie można ich edytować.

    ![Właściwości rzeczywistego urządzenia](media/tutorial-add-device/setproperties1.png)

3. Dla rzeczywistego urządzenia możesz wyświetlić strony **Miary**, **Reguły** i **Pulpit nawigacyjny**.

## <a name="prepare-the-client-code"></a>Przygotowywanie kodu klienta

Przykładowy kod w tym artykule został zapisany w języku [Node. js](https://nodejs.org/) i zawiera wystarczającą ilość kodu dla urządzenia:

* nawiązuje połączenie z aplikacją usługi Azure IoT Central,
* wysłania danych telemetrycznych temperatury jako połączony klimatyzator;
* wysłania właściwości urządzenia do aplikacji usługi Azure IoT Central;
* wysłania odpowiedzi do operatora, który użył ustawienia **Ustaw temperaturę**.
* obsługi polecenia Echo z poziomu aplikacji usługi Azure IoT Central.

Artykuły wymienione w sekcji [następne kroki](#next-steps) zawierają więcej kompletnych przykładów i są wyświetlane w innych językach programowania. Aby uzyskać więcej informacji na temat sposobu, w jaki urządzenia łączą się z usługą Azure IoT Central, patrz artykuł [Łączność urządzeń](concepts-connectivity.md).

W poniższej procedurze pokazano, jak przygotować przykład dla oprogramowania [Node.js](https://nodejs.org/):

### <a name="get-the-device-connection-information"></a>Pobierz informacje o połączeniu z urządzeniem

1. Parametry połączenia dla wystąpienia urządzenia w aplikacji są generowane na podstawie informacji o urządzeniu udostępnionych przez usługę IoT Central.

   Na ekranie dla rzeczywistego połączonego klimatyzatora wybierz pozycję **Połącz**.

   ![Strona Urządzenie przedstawiająca link umożliwiający wyświetlenie informacji o połączeniu](media/tutorial-add-device/connectionlink.png)

1. Na stronie **połączenie urządzenia** Zanotuj **Identyfikatory zakresu**, **Identyfikator urządzenia**i wartości **klucza podstawowego** . Te wartości są używane w dalszej części tego samouczka.

   ![Szczegóły połączenia](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Przygotowywanie projektu Node. js

1. Utwórz folder o nazwie `connectedairconditioner` na komputerze deweloperskim.

1. W środowisku wiersza polecenia przejdź do utworzonego folderu `connectedairconditioner`.

1. Aby zainicjować projekt oprogramowania Node.js, uruchom następujące polecenie, akceptując wszystkie wartości domyślne:

    ```cmd/sh
    npm init
      ```

1. Aby zainstalować wymagane pakiety, uruchom następujące polecenie:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. Za pomocą edytora tekstów utwórz plik o nazwie **ConnectedAirConditioner.js** w folderze `connectedairconditioner`.

1. Dodaj następujące instrukcje `require` na początku pliku **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. Dodaj następujące deklaracje zmiennych do pliku. Zastąp symbole zastępcze `{your Scope ID}`, `{your Device ID}`i `{your Primary Key}` z zanotowanymi wcześniej informacjami o połączeniu z urządzeniem:

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

* nawiązuje połączenie z aplikacją usługi Azure IoT Central,
* wysyła dane telemetryczne do aplikacji usługi Azure IoT Central,
* wysłania właściwości urządzenia do aplikacji usługi Azure IoT Central;
* odbiera ustawienia z aplikacji usługi Azure IoT Central.
* obsługi polecenia Echo z poziomu aplikacji usługi Azure IoT Central.

1. Aby wysyłać dane telemetryczne temperatury do aplikacji usługi Azure IoT Central, dodaj następujący kod do pliku **ConnectedAirConditioner.js**:

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

    Nazwa pola w wysyłanym kodzie JSON musi być zgodna z nazwą pola określoną dla danych telemetrycznych temperatury w szablonie urządzenia. W tym przykładzie nazwa pola to **temperature**.

1. Aby wysłać właściwości urządzenia, takie jak **firmwareVersion** i **serialNumber**, dodaj następującą definicję:

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

1. Aby zdefiniować ustawienia obsługiwane przez urządzenie, takie jak **setTemperature**, dodaj następującą definicję:

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

1. Aby obsługiwać ustawienia wysyłane z usługi Azure IoT Central, dodaj następującą funkcję, która lokalizuje i wykonuje odpowiedni kod urządzenia:

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

    * oczekuje na wysłanie żądanej właściwości przez usługę Azure IoT Central,
    * lokalizuje odpowiednią funkcję do wywołania w celu obsłużenia zmiany ustawienia,
    * Wysyła potwierdzenie z powrotem do aplikacji IoT Central platformy Azure.

1. Aby odpowiedzieć na polecenie, takie jak **echo**, z aplikacji usługi Azure IoT Central, dodaj następującą definicję:

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
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

1. Aby uruchomić przykład, podaj następujące polecenie w środowisku wiersza polecenia:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Upewnij się, że jesteś w folderze `connectedairconditioner` w momencie uruchamiania tego polecenia.

1. Aplikacja wyświetla dane wyjściowe na konsoli:

   ![Dane wyjściowe aplikacji klienckiej](media/tutorial-add-device/output.png)

1. Po około 30 sekundach zobaczysz dane telemetryczne na stronie **Miary** urządzenia:

   ![Rzeczywiste dane telemetryczne](media/tutorial-add-device/realtelemetry.png)

1. Na stronie **Ustawienia** widać, że ustawienie jest teraz zsynchronizowane. Po pierwszym połączeniu urządzenia odebrało ono wartość ustawienia i potwierdziło zmianę:

   ![Zsynchronizowane ustawienie](media/tutorial-add-device/settingsynced.png)

1. Na stronie **Ustawienia** ustaw temperaturę urządzenia na **95** i wybierz polecenie **Aktualizuj urządzenie**. Przykładowa aplikacja odbierze i przetworzy tę zmianę:

   ![Ustawienia odbierania i przetwarzania](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Istnieją dwa komunikaty dotyczące aktualizacji ustawienia. Jeden jest stosowany po wysłaniu stanu `pending`, a drugi po wysłaniu stanu `completed`.

1. Na stronie **Miary** możesz zobaczyć, że urządzenie wysyła większe wartości temperatury:

    ![Dane telemetryczne temperatury mają teraz większe wartości](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="nextstepaction"]
> * Dodawanie nowego rzeczywistego urządzenia
> * Konfigurowanie nowego urządzenia
> * Pobieranie parametrów połączenia dla rzeczywistego urządzenia z aplikacji
> * Poznawanie sposobu mapowania kodu klienta na aplikację
> * Konfigurowanie kodu klienta dla rzeczywistego urządzenia

Teraz, gdy połączono rzeczywiste urządzenie z aplikacją IoT Central platformy Azure, Oto kilka sugerowanych następnych kroków.

Jako operator możesz dowiedzieć się, jak wykonywać następujące działania:

* [Zarządzanie urządzeniami](howto-manage-devices.md)
* [Korzystanie ze zbiorów urządzeń](howto-use-device-sets.md)
* [Tworzenie niestandardowych analiz](howto-use-device-sets.md)

Jako deweloper urządzenia możesz dowiedzieć się, jak wykonywać następujące działania:

* [Przygotowywanie i łączenie urządzenia DevKit (C)](howto-connect-devkit.md)
* [Przygotowywanie i łączenie Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Przygotuj i Połącz Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Przygotowanie i podłączenie urządzenia z systemem Windows 10 IoT CoreC#()](howto-connect-windowsiotcore.md)
* [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
