---
title: Rozpoczynanie pracy z Centrum IoT Azure modułu tożsamości i moduł dwie (Node.js) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć moduł tożsamości i zaktualizuj dwie modułu przy użyciu IoT zestawy SDK dla środowiska Node.js.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: e1fcca7f37f928c488b1111d6d3299cb03c73cc1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37036660"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Rozpoczynanie pracy z Centrum IoT modułu tożsamości i moduł dwie przy użyciu zaplecza Node.js i Node.js urządzenia

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Tożsamość urządzenia i bliźniacza reprezentacja urządzenia usługi Azure IoT Hub umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewniają widoczność warunków urządzenia, natomiast tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.

Na końcu tego samouczka znajdują się dwie aplikacje Node.js:

* **CreateIdentities**, która tworzy tożsamość urządzenia, tożsamość modułu oraz skojarzony klucz zabezpieczeń na potrzeby łączenia klientów modułu i urządzenia.
* **UpdateModuleTwinReportedProperties**, która wysyła zaktualizowane zgłoszone właściwości bliźniaczej reprezentacji modułu do Twojego centrum IoT Hub.

> [!NOTE]
> Artykuł [Azure IoT SDKs][lnk-hub-sdks] (Zestawy SDK usługi Azure IoT) zawiera informacje dotyczące zestawów SDK usługi Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).
* Centrum IoT.
* Zainstaluj najnowszą [Node.js SDK](https://github.com/Azure/azure-iot-sdk-node).


Centrum IoT zostało już utworzone i masz nazwę hosta oraz parametry połączenia usługi IoT Hub potrzebne do ukończenia pozostałej części tego samouczka.

<a id="DeviceIdentity_csharp"></a>
## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Tworzenie tożsamości urządzenia i tożsamości modułu w Centrum IoT

W tej sekcji utworzysz aplikację Node.js, która tworzy tożsamości urządzenia i tożsamości modułu w rejestrze tożsamości w Centrum IoT. Urządzenie lub moduł nie mogą łączyć się z centrum IoT Hub, jeśli nie mają odpowiedniego wpisu w rejestrze tożsamości. Więcej informacji znajduje się w sekcji „Identity registry” (Rejestr tożsamości) artykułu [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera). Uruchomienie tej aplikacji konsolowej powoduje wygenerowanie unikatowego identyfikatora i klucza zarówno dla urządzenia, jak i modułu. Urządzenie i moduł korzystają z tych wartości w celu identyfikowania się podczas wysyłania komunikatów urządzenie-chmura do usługi IoT Hub. W identyfikatorach jest uwzględniana wielkość liter.

1.  Utwórz katalog do przechowywania kodu.
2. Wewnątrz tego katalogu, najpierw uruchom **npm init -y** utworzyć pusty plik package.json przy użyciu ustawień domyślnych. To jest plik projektu dla kodu.
3. Uruchom **-S instalacji narzędzia npm azure-iothub@modules-preview**  ma zostać zainstalowana usługa SDK wewnątrz **node_modules** podkatalogu. 

    > [!NOTE] 
    > Node_modules nazwę podkatalogu używa modułu word oznacza "biblioteki węzła". Termin w tym miejscu nie ma nic wspólnego z modułów Centrum IoT.

4. Utworzyć następującego pliku .js w katalogu. Wywołać ją **add.js**. Skopiuj i Wklej parametry połączenia koncentratora, a nazwa koncentratora.

    ```javascript
    var Registry = require('azure-iothub').Registry;
    var uuid = require('uuid');
    // Copy/paste your connection string and hub name here
    var serviceConnectionString = '<hub connection string from portal>';
    var hubName = '<hub name>.azure-devices.net';
    // Create an instance of the IoTHub registry
    var registry = Registry.fromConnectionString(serviceConnectionString);
    // Insert your device ID and moduleId here.
    var deviceId = 'myFirstDevice';
    var moduleId = 'myFirstModule';
    // Create your device as a SAS authentication device
    var primaryKey = new Buffer(uuid.v4()).toString('base64');
    var secondaryKey = new Buffer(uuid.v4()).toString('base64');
    var deviceDescription = {
      deviceId: deviceId,
      status: 'enabled',
      authentication: {
        type: 'sas',
        symmetricKey: {
          primaryKey: primaryKey,
          secondaryKey: secondaryKey
        }
      }
    };

    // First, create a device identity
    registry.create(deviceDescription, function(err) {
      if (err) {
        console.log('Error creating device identity: ' + err);
        process.exit(1);
      }
      console.log('device connection string = "HostName=' + hubName + ';DeviceId=' + deviceId + ';SharedAccessKey=' + primaryKey + '"');

    // Then add a module to that device
      registry.addModule({ deviceId: deviceId, moduleId: moduleId }, function(err) {
        if (err) {
          console.log('Error creating module identity: ' + err);
          process.exit(1);
        }

    // Finally, retrieve the module details from the hub so we can construct the connection string
        registry.getModule(deviceId, moduleId, function(err, foundModule) {
          if (err) {
            console.log('Error getting module back from hub: ' + err);
            process.exit(1);
          }
          console.log('module connection string = "HostName=' + hubName + ';DeviceId=' + foundModule.deviceId + ';ModuleId='+foundModule.moduleId+';SharedAccessKey=' + foundModule.authentication.symmetricKey.primaryKey + '"');
          process.exit(0);
        });
      });
    });

    ```

Ta aplikacja tworzy tożsamość urządzenia o identyfikatorze **myFirstDevice** i tożsamością modułu o identyfikatorze **myFirstModule** obszarze urządzenie **myFirstDevice**. (Jeśli ten identyfikator modułu już istnieje w rejestrze tożsamości, kod po prostu pobiera informacje o istniejącym module). Aplikacja następnie wyświetla klucz podstawowy dla tej tożsamości. Tego klucza używa się w symulowanej aplikacji modułu, aby nawiązać połączenie z centrum IoT.

5. Uruchom to za pomocą add.js węzła. Podaje ciąg połączenia dla Twojej tożsamości urządzenia i kolejnego dla tożsamości użytkownika modułu.

    > [!NOTE]
    > Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia i modułu tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. W rejestrze tożsamości są przechowywane identyfikatory urządzeń i klucze służące jako poświadczenia zabezpieczeń. W rejestrze tożsamości są także przechowywane flagi włączenia/wyłączenia dla każdego urządzenia, za pomocą których można wyłączyć dostęp do danego urządzenia. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Nie istnieje flaga włączenia/wyłączenia tożsamości modułów. Więcej informacji znajduje się w temacie [IoT Hub Developer Guide][lnk-devguide-identity] (Usługa IoT Hub — przewodnik dewelopera).


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Zaktualizuj dwie modułu przy użyciu urządzenia Node.js SDK

W tej sekcji utworzysz Node.js aplikacji na urządzeniu symulowane, która aktualizuje dwie moduł zgłosił właściwości.

1. **Pobierz parametry połączenia modułu** — teraz zaloguj się do witryny [Azure Portal][lnk-portal]. Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź tożsamość myFirstDevice i otwórz ją. Zobaczysz, że tożsamość myFirstModule została pomyślnie utworzona. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

    ![Szczegóły modułu w witrynie Azure Portal][15]

2. Czy podobny do Ciebie w poprzednim kroku, Utwórz katalog dla kodu urządzenia i umożliwia NPM go zainicjować i zainstaluj zestaw SDK urządzenia (**-S instalacji narzędzia npm azure-iot-device-amqp@modules-preview** ).

    > [!NOTE]
    > Polecenie install npm mogą uznać powolne. Cierpliwość, jego jest ściąganie dół partii kodu z repozytorium pakietów.

    > [!NOTE] 
    > Jeśli zostanie wyświetlony komunikat o błędzie informujący npm błąd! rejestru błąd podczas analizowania json jest bezpiecznie zignorować. Jeśli zostanie wyświetlony komunikat o błędzie informujący npm błąd! rejestru błąd podczas analizowania json jest bezpiecznie zignorować.

3. Utwórz plik o nazwie twin.js. Skopiuj i Wklej ciąg tożsamości z modułu.

    ```javascript
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-amqp').Amqp;
    // Copy/paste your module connection string here.
    var connectionString = '<insert module connection string here>';
    // Create a client using the Amqp protocol.
    var client = Client.fromConnectionString(connectionString, Protocol);
    client.on('error', function (err) {
      console.error(err.message);
    });
    // connect to the hub
    client.open(function(err) {
      if (err) {
        console.error('error connecting to hub: ' + err);
        process.exit(1);
      }
      console.log('client opened');
    // Create device Twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('error getting twin: ' + err);
          process.exit(1);
        }
    // Output the current properties
        console.log('twin contents:');
        console.log(twin.properties);
    // Add a handler for desired property changes
        twin.on('properties.desired', function(delta) {
            console.log('new desired properties received:');
            console.log(JSON.stringify(delta));
        });
    // create a patch to send to the hub
        var patch = {
          updateTime: new Date().toString(),
          firmwareVersion:'1.2.1',
          weather:{
            temperature: 72,
            humidity: 17
          }
        };
    // send the patch
        twin.properties.reported.update(patch, function(err) {
          if (err) throw err;
          console.log('twin state reported');
        });
      });
    });
    ```

2. Teraz uruchom polecenie **twin.js węzła**.

    ```
    F:\temp\module_twin>node twin.js
    client opened
    twin contents:
    { reported: { update: [Function: update], '$version': 1 },
      desired: { '$version': 1 } }
    new desired properties received:
    {"$version":1}
    twin state reported
    ```

## <a name="next-steps"></a>Kolejne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Wprowadzenie do zarządzania urządzeniami][lnk-device-management]
* [Getting started with IoT Edge][lnk-iot-edge] (Wprowadzenie do usługi IoT Edge)


<!-- Images. -->
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/