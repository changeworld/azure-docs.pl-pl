---
title: Dodawanie rzeczywistego urządzenia do aplikacji usługi Azure IoT Central | Microsoft Docs
description: Jako operator możesz dodać rzeczywiste urządzenie do aplikacji usługi Azure IoT Central.
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e9378f8d2b31bfed4c464951c427b1e9d00b7893
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64699372"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>Samouczek: Dodawanie rzeczywistego urządzenia do aplikacji usługi Azure IoT Central

Ten samouczek przedstawia dodawanie rzeczywistego urządzenia do aplikacji usługi Microsoft Azure IoT Central i konfigurowanie go.

Ten samouczek składa się z dwóch części:

1. Najpierw, jako operator, poznasz działania dodawania i konfigurowania rzeczywistego urządzenia w aplikacji usługi Azure IoT Central. Na końcu tej części pobierzesz parametry połączenia do użycia w drugiej części.
2. Następnie, jako deweloper urządzenia, poznasz kod przeznaczony dla rzeczywistego urządzenia. Parametry połączenia z pierwszej części zostaną dodane do przykładowego kodu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowego rzeczywistego urządzenia
> * Konfigurowanie rzeczywistego urządzenia
> * Pobieranie parametrów połączenia dla rzeczywistego urządzenia z aplikacji
> * Poznawanie sposobu mapowania kodu klienta na aplikację
> * Konfigurowanie kodu klienta dla rzeczywistego urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konstruktor powinien ukończyć co najmniej pierwszy samouczek przeznaczony dla konstruktorów w celu utworzenia aplikacji usługi Azure IoT Central:

* [Definiowanie nowego typu urządzenia](tutorial-define-device-type.md) (wymagany)
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md) (opcjonalny)
* [Dostosowywanie widoków operatora](tutorial-customize-operator.md) (opcjonalny)

Zainstaluj [Node.js](https://nodejs.org/) wersji 8.0.0 lub później na komputerze deweloperskim. Możesz uruchomić `node --version` w wierszu polecenia, aby sprawdzić swoją wersję. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

Aby dodać rzeczywiste urządzenie do aplikacji, należy użyć szablonu urządzenia **Połączony klimatyzator** utworzonego w ramach samouczka [Definiowanie nowego typu urządzenia](tutorial-define-device-type.md).

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **Device Explorer** w menu nawigacji po lewej stronie:

   ![Strona Device Explorer pokazująca połączony klimatyzator](media/tutorial-add-device/explorer.png)

   **Device Explorer** pokazuje **połączone klimatyzacyjne** szablon urządzeń i symulowanych urządzeń. Podczas tworzenia szablonu urządzenia IoT Central automatycznie tworzy symulowane urządzenie.

2. Aby uruchomić Podłączanie urządzenia rzeczywistym połączonych klimatyzacyjne, zaznacz **+**, następnie **rzeczywistych**:

   ![Rozpoczynanie dodawania nowego rzeczywistego połączonego klimatyzatora](media/tutorial-add-device/newreal.png)

3. Wprowadź identyfikator urządzenia (powinno wskazywać na małe litery) lub użyj sugerowanego identyfikator urządzenia. Możesz także podać nazwę nowego urządzenia i wybrać polecenie **Utwórz**.

   ![Zmienianie nazwy urządzenia](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurowanie rzeczywistego urządzenia

Rzeczywiste urządzenie jest tworzone na podstawie szablonu urządzenia **Połączony klimatyzator**. Możesz użyć pozycji **Ustawienia**, aby skonfigurować urządzenie i ustawić wartości właściwości pod kątem rejestrowania informacji o urządzeniu.

1. Na stronie **Ustawienia** zwróć uwagę na to, że stan ustawienia **Ustaw temperaturę** to **brak aktualizacji**. Pozostanie ono w tym stanie do momentu, gdy rzeczywiste urządzenie połączy się z aplikacją i potwierdzi wykonanie działania dla ustawienia.

    ![Synchronizowanie ustawień](media/tutorial-add-device/settingssyncing.png)

2. Na **właściwości** strona nowej, rzeczywistego urządzenia, zarówno lokalizacji usługi i Data ostatniego usługi są edytowalnych właściwości. Pola numeru seryjnego i wersji oprogramowania układowego są puste do momentu połączenia urządzenia z aplikacją. Te wartości tylko do odczytu są wysyłane z urządzenia i nie może być edytowany.

    ![Właściwości rzeczywistego urządzenia](media/tutorial-add-device/setproperties1.png)

3. Dla rzeczywistego urządzenia możesz wyświetlić strony **Miary**, **Reguły** i **Pulpit nawigacyjny**.

## <a name="generate-connection-string"></a>Generowanie parametrów połączenia

Deweloper urządzenia musi osadzić *parametry połączenia* rzeczywistego urządzenia w kodzie uruchamianym na urządzeniu. Parametry połączenia umożliwia urządzeniu nawiązać bezpiecznego połączenia z aplikacją. Poniższe kroki pokazują, możesz wygenerować parametry połączenia i przygotuj klienta kodu w języku Node.js.

## <a name="prepare-the-client-code"></a>Przygotowywanie kodu klienta

Przykładowy kod w tym artykule został napisany w [Node.js](https://nodejs.org/) i zawiera wystarczającą ilość kodu:

* nawiązania połączenia jako urządzenie z aplikacją usługi Azure IoT Central;
* wysłania danych telemetrycznych temperatury jako połączony klimatyzator;
* wysyła właściwości urządzenia do aplikacji usługi Azure IoT Central;
* wysłania odpowiedzi do operatora, który użył ustawienia **Ustaw temperaturę**.
* obsługuje polecenie Echo z poziomu aplikacji usługi Azure IoT Central.

Artykuły wymienione w [następne kroki](#next-steps) sekcji zawierają przykłady bardziej szczegółowy i Pokaż innych językach programowania. Aby uzyskać więcej informacji na temat sposobu, w jaki urządzenia łączą się z usługą Azure IoT Central, patrz artykuł [Łączność urządzeń](concepts-connectivity.md).

W poniższej procedurze pokazano, jak przygotować przykład dla oprogramowania [Node.js](https://nodejs.org/):

### <a name="get-the-device-connection-information"></a>Pobieranie informacji o połączeniu urządzenia

1. Parametry połączenia dla wystąpienia urządzenia w aplikacji są generowane na podstawie informacji o urządzeniu udostępnionych przez usługę IoT Central.

   Na ekranie dla rzeczywistego połączonego klimatyzatora wybierz pozycję **Połącz**.

   ![Strona Urządzenie przedstawiająca link umożliwiający wyświetlenie informacji o połączeniu](media/tutorial-add-device/connectionlink.png)

1. Na stronie połączenie z urządzeniem Zanotuj **identyfikator zakresu**, **identyfikator urządzenia** i **klucz podstawowy** wartości. Użyjesz tych wartości w następnym kroku.

   ![Szczegóły połączenia](media/tutorial-add-device/device-connect.png)

### <a name="generate-the-connection-string"></a>Generowanie parametrów połączenia

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

### <a name="prepare-the-nodejs-project"></a>Przygotowanie projektu środowiska Node.js

1. Utwórz folder o nazwie `connectedairconditioner` na komputerze deweloperskim.

1. W środowisku wiersza polecenia przejdź do utworzonego folderu `connectedairconditioner`.

1. Aby zainicjować projekt oprogramowania Node.js, uruchom następujące polecenie, akceptując wszystkie wartości domyślne:

    ```cmd/sh
    npm init
      ```

1. Aby zainstalować wymagane pakiety, uruchom następujące polecenie:

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. Za pomocą edytora tekstów utwórz plik o nazwie **ConnectedAirConditioner.js** w folderze `connectedairconditioner`.

1. Dodaj następujące instrukcje `require` na początku pliku **ConnectedAirConditioner.js**:

    ```javascript
    'use strict';

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

    > [!NOTE]
    > Symbol zastępczy `{your device connection string}` zostanie zaktualizowany w późniejszym kroku.

1. Zapisz zmiany wprowadzone do tej pory, ale pozostaw plik otwarty.

## <a name="review-client-code"></a>Przejrzyj kod klienta

W poprzedniej sekcji utworzono szkielet projektu oprogramowania Node.js dla aplikacji, która nawiązuje połączenie z aplikacją usługi Azure IoT Central. Następnym krokiem jest, aby dodać kod, aby:

* nawiązuje połączenie z aplikacją usługi Azure IoT Central,
* wysyła dane telemetryczne do aplikacji usługi Azure IoT Central,
* wysyła właściwości urządzenia do aplikacji usługi Azure IoT Central;
* odbiera ustawienia z aplikacji usługi Azure IoT Central.
* obsługuje polecenie Echo z poziomu aplikacji usługi Azure IoT Central.

1. Aby wysyłać dane telemetryczne temperatury do aplikacji usługi Azure IoT Central, dodaj następujący kod do pliku **ConnectedAirConditioner.js**:

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
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
    * wysyła potwierdzenie z powrotem do aplikacji usługi Azure IoT Central.

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
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Setup device command callbacks
        client.onDeviceMethod('echo', onCommandEcho);
        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. Zapisz zmiany wprowadzone do tej pory, ale pozostaw plik otwarty.

## <a name="configure-client-code"></a>Konfigurowanie kodu klienta

<!-- Add the connection string to the sample code, build, and run -->
Aby skonfigurować kod klienta pod kątem nawiązywania połączenia z aplikacją usługi Azure IoT Central, dodaj parametry połączenia dla rzeczywistego urządzenia zanotowane wcześniej w tym samouczku.

1. W pliku **ConnectedAirConditioner.js** znajdź następujący wiersz kodu:

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. Zastąp element `{your device connection string}` parametrami połączenia rzeczywistego urządzenia. Możesz skopiować parametry połączenia, który został wygenerowany w poprzednim kroku.

1. Zapisz zmiany w pliku **ConnectedAirConditioner.js**.

1. Aby uruchomić przykład, podaj następujące polecenie w środowisku wiersza polecenia:

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > Upewnij się, że jesteś w folderze `connectedairconditioner` w momencie uruchamiania tego polecenia.

1. Aplikacja wyświetla dane wyjściowe na konsoli:

   ![Dane wyjściowe aplikacji klienckiej](media/tutorial-add-device/output.png)

1. Po około 30 sekundach zobaczysz dane telemetryczne na stronie **Miary** urządzenia:

   ![Rzeczywiste ~ ~ telemetrii](media/tutorial-add-device/realtelemetry.png)

1. Na stronie **Ustawienia** widać, że ustawienie jest teraz zsynchronizowane. Po pierwszym połączeniu urządzenia odebrało ono wartość ustawienia i potwierdziło zmianę:

   ![Zsynchronizowane ustawienie](media/tutorial-add-device/settingsynced.png)

1. Na stronie **Ustawienia** ustaw temperaturę urządzenia na **95** i wybierz polecenie **Aktualizuj urządzenie**. Przykładowa aplikacja odbierze i przetworzy tę zmianę:

   ![Ustawienia odbierania i przetwarzania](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > Istnieją dwa komunikaty dotyczące aktualizacji ustawienia. Jeden jest stosowany po wysłaniu stanu `pending`, a drugi po wysłaniu stanu `completed`.

1. Na stronie **Miary** możesz zobaczyć, że urządzenie wysyła większe wartości temperatury:

    ![Dane telemetryczne temperatury mają teraz większe wartości](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="nextstepaction"]
> * Dodawanie nowego rzeczywistego urządzenia
> * Konfigurowanie nowego urządzenia
> * Pobieranie parametrów połączenia dla rzeczywistego urządzenia z aplikacji
> * Poznawanie sposobu mapowania kodu klienta na aplikację
> * Konfigurowanie kodu klienta dla rzeczywistego urządzenia

Teraz, gdy rzeczywistego urządzenia po połączeniu z aplikacją usługi Azure IoT Central, Oto zalecane kolejne kroki:

Jako operator możesz dowiedzieć się, jak wykonywać następujące działania:

* [Zarządzanie urządzeniami](howto-manage-devices.md)
* [Korzystanie ze zbiorów urządzeń](howto-use-device-sets.md)
* [Tworzenie niestandardowych analiz](howto-use-device-sets.md)

Jako deweloper urządzenia możesz dowiedzieć się, jak wykonywać następujące działania:

* [Przygotuj i podłącz urządzenie Mxchip (C)](howto-connect-devkit.md)
* [Przygotowanie i Połącz z urządzeniem Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [Przygotowanie i Połącz z urządzeniem Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [Przygotuj i podłącz urządzenie z systemu Windows 10 IoT core (C#)](howto-connect-windowsiotcore.md)
* [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
