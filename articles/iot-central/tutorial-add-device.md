---
title: Dodawanie rzeczywistego urządzenia do aplikacji usługi Azure IoT Central | Microsoft Docs
description: Jako operator możesz dodać rzeczywiste urządzenie do aplikacji usługi Azure IoT Central.
services: iot-central
author: sandeeppujar
ms.author: sandeepu
ms.date: 04/16/2018
ms.topic: tutorial
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: f95d9ec8cf22c287169a8de077ff9eb5907a8e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34201935"
---
# <a name="add-a-real-device-to-your-azure-iot-central-application"></a>Dodawanie rzeczywistego urządzenia do aplikacji usługi Azure IoT Central

Ten samouczek przedstawia dodawanie rzeczywistego urządzenia do aplikacji usługi Microsoft Azure IoT Central i konfigurowanie go.

Ten samouczek składa się z dwóch części:

1. Najpierw, jako operator, poznasz działania dodawania i konfigurowania rzeczywistego urządzenia w aplikacji usługi Azure IoT Central. Na końcu tej części pobierzesz parametry połączenia do użycia w drugiej części.
1. Następnie, jako deweloper urządzenia, poznasz kod przeznaczony dla rzeczywistego urządzenia. Parametry połączenia z pierwszej części zostaną dodane do przykładowego kodu.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie nowego rzeczywistego urządzenia
> * Konfigurowanie nowego urządzenia
> * Pobieranie parametrów połączenia dla rzeczywistego urządzenia z aplikacji
> * Poznawanie sposobu mapowania kodu klienta na aplikację
> * Konfigurowanie kodu klienta dla rzeczywistego urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konstruktor powinien ukończyć co najmniej pierwszy samouczek przeznaczony dla konstruktorów w celu utworzenia aplikacji usługi Azure IoT Central:

* [Definiowanie nowego typu urządzenia](tutorial-define-device-type.md) (wymagany)
* [Konfigurowanie reguł i akcji dla urządzenia](tutorial-configure-rules.md) (opcjonalny)
* [Dostosowywanie widoków operatora](tutorial-customize-operator.md) (opcjonalny)

## <a name="add-a-real-device"></a>Dodawanie rzeczywistego urządzenia

Aby dodać rzeczywiste urządzenie do aplikacji, należy użyć szablonu urządzenia **Połączony klimatyzator** utworzonego w ramach samouczka [Definiowanie nowego typu urządzenia](tutorial-define-device-type.md).

1. Aby dodać nowe urządzenie jako operator, wybierz pozycję **Device Explorer** w menu nawigacji po lewej stronie:

    ![Strona Device Explorer pokazująca połączony klimatyzator](media/tutorial-add-device/explorer.png)

    Strona **Device Explorer** pokazuje szablon urządzenia **Połączony klimatyzator** i symulowane urządzenie, które zostało utworzone automatycznie podczas tworzenia szablonu urządzenia przez konstruktora.

1. Aby rozpocząć łączenie rzeczywistego połączonego klimatyzatora, wybierz pozycję **Nowe**, a następnie **Rzeczywiste**:

    ![Rozpoczynanie dodawania nowego rzeczywistego połączonego klimatyzatora](media/tutorial-add-device/newreal.png)

1. Opcjonalnie możesz zmienić nazwę nowego urządzenia, wybierając nazwę urządzenia i modyfikując wartość:

    ![Zmienianie nazwy urządzenia](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>Konfigurowanie rzeczywistego urządzenia

Rzeczywiste urządzenie jest tworzone na podstawie szablonu urządzenia **Połączony klimatyzator**. Jako konstruktor możesz użyć pozycji **Ustawienia**, aby skonfigurować urządzenie i ustawić wartości właściwości pod kątem rejestrowania informacji o urządzeniu.

1. Na stronie **Ustawienia** zwróć uwagę na to, że stan ustawienia **Ustaw temperaturę** to **brak aktualizacji**. Pozostanie ono w tym stanie do momentu, gdy rzeczywiste urządzenie połączy się i potwierdzi wykonanie działania dla ustawienia:

    ![Synchronizowanie ustawień](media/tutorial-add-device/settingssyncing.png)

1. Na stronie **Właściwości** nowego rzeczywistego połączonego klimatyzatora ustaw dla pola **Numer seryjny** wartość **rcac0010** i dla pola **Wersja oprogramowania układowego** wartość 9.75. Następnie wybierz polecenie **Zapisz**:

    ![Ustawianie właściwości rzeczywistego urządzenia](media/tutorial-add-device/setproperties.png)

1. Jako konstruktor możesz wyświetlić strony **Miary**, **Reguły** i **Pulpit nawigacyjny** dla rzeczywistego urządzenia.

## <a name="get-connection-string-for-real-device-from-application"></a>Pobieranie parametrów połączenia dla rzeczywistego urządzenia z aplikacji

Deweloper urządzenia musi osadzić _parametry połączenia_ rzeczywistego urządzenia w kodzie uruchamianym na urządzeniu. Parametry połączenia umożliwiają urządzeniu nawiązanie zabezpieczonego połączenia z aplikacją usługi Azure IoT Central. Każde wystąpienie urządzenia ma unikatowe parametry połączenia. Poniższe kroki pokazują sposób określania parametrów połączenia dla wystąpienia urządzenia w aplikacji:

1. Na ekranie **Urządzenie** rzeczywistego połączonego klimatyzatora wybierz pozycję **Połącz to urządzenie**:

    ![Strona Urządzenie przedstawiająca link umożliwiający wyświetlenie informacji o połączeniu](media/tutorial-add-device/connectionlink.png)

1. Na stronie **Łączenie** skopiuj wartość **Podstawowe parametry połączenia** i zapisz ją. Ta wartość zostanie użyta w drugiej połowie tego samouczka. Deweloper urządzenia używa tej wartości w aplikacji klienckiej uruchamianej na urządzeniu:

    ![Wartości parametrów połączenia](media/tutorial-add-device/connectionstring.png)

## <a name="prepare-the-client-code"></a>Przygotowywanie kodu klienta

Przykładowy kod w tym artykule został napisany przy użyciu oprogramowania [Node.js](https://nodejs.org/) i zawiera minimalny kod potrzebny do:

* nawiązania połączenia jako urządzenie z aplikacją usługi Azure IoT Central;
* wysłania danych telemetrycznych temperatury jako połączony klimatyzator;
* wysłania odpowiedzi do operatora, który użył ustawienia **Ustaw temperaturę**.

Artykuły zawierające instrukcje przywoływane w sekcji [Następne kroki](#next-steps) zawierają pełniejsze przykłady i przedstawiają użycie innych języków programowania. Aby uzyskać więcej informacji na temat sposobu, w jaki urządzenia łączą się z usługą Azure IoT Central, patrz artykuł [Łączność urządzeń](concepts-connectivity.md).

W poniższej procedurze pokazano, jak przygotować przykład dla oprogramowania [Node.js](https://nodejs.org/):

1. Zainstaluj oprogramowanie [Node.js](https://nodejs.org/) w wersji 4.0.x lub nowszej na maszynie. Oprogramowanie Node.js jest dostępne dla różnych systemów operacyjnych.

1. Utwórz folder o nazwie `connectedairconditioner` na maszynie.

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

## <a name="understand-how-client-code-maps-to-the-application"></a>Poznawanie sposobu mapowania kodu klienta na aplikację

W poprzedniej sekcji utworzono szkielet projektu oprogramowania Node.js dla aplikacji, która nawiązuje połączenie z aplikacją usługi Azure IoT Central. W tej sekcji jest dodawany kod, który:

* nawiązuje połączenie z aplikacją usługi Azure IoT Central,
* wysyła dane telemetryczne do aplikacji usługi Azure IoT Central,
* odbiera ustawienia z aplikacji usługi Azure IoT Central.

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

        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. Zapisz zmiany wprowadzone do tej pory, ale pozostaw plik otwarty.

## <a name="configure-client-code-for-real-device"></a>Konfigurowanie kodu klienta dla rzeczywistego urządzenia

<!-- Add the connection string to the sample code, build, and run -->
Aby skonfigurować kod klienta pod kątem nawiązywania połączenia z aplikacją usługi Azure IoT Central, dodaj parametry połączenia dla rzeczywistego urządzenia zanotowane wcześniej w tym samouczku.

1. W pliku **ConnectedAirConditioner.js** znajdź następujący wiersz kodu:

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. Zastąp element `{your device connection string}` parametrami połączenia rzeczywistego urządzenia. Parametry połączenia zanotowano pod koniec sekcji „Pobieranie parametrów połączenia dla rzeczywistego urządzenia z aplikacji”.

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

Teraz, gdy połączono rzeczywiste urządzenie z aplikacją usługi Azure IoT Central, możesz wykonać sugerowane kolejne kroki:

Jako operator możesz dowiedzieć się, jak wykonywać następujące działania:

* [Zarządzanie urządzeniami](howto-manage-devices.md)
* [Korzystanie ze zbiorów urządzeń](howto-use-device-sets.md)
* [Tworzenie niestandardowych analiz](howto-create-analytics.md)

Jako deweloper urządzenia możesz dowiedzieć się, jak wykonywać następujące działania:

* [Przygotowywanie i łączenie zestawu deweloperskiego](howto-connect-devkit.md)
* [Przygotowywanie i łączenie urządzenia Raspberry Pi](howto-connect-raspberry-pi-python.md)
* [Łączenie ogólnego klienta Node.js z aplikacją usługi Azure IoT Central](howto-connect-nodejs.md)
