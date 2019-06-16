---
title: Rozpoczynanie pracy z usługą Azure IoT Hub tożsamości i moduł bliźniaczą reprezentację modułu (Node.js) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć moduł tożsamości i zaktualizować bliźniaczą reprezentację modułu przy użyciu zestawów SDK usługi IoT dla środowiska Node.js.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: node
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 312d3abad2ee2c9e668f8b354aaba96f8a652698
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626220"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-nodejs-back-end-and-nodejs-device"></a>Rozpoczynanie pracy z usługą IoT Hub tożsamości i moduł bliźniaczą reprezentację modułu przy użyciu zaplecza Node.js i urządzenia środowiska Node.js

> [!NOTE]
> [Tożsamości modułów i bliźniacze reprezentacje modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzenia i bliźniaczej reprezentacji urządzenia usługi Azure IoT Hub, ale zapewniają większy stopień szczegółowości. Tożsamość urządzenia i bliźniacza reprezentacja urządzenia usługi Azure IoT Hub umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewniają widoczność warunków urządzenia, natomiast tożsamość modułu i bliźniacza reprezentacja modułu zapewniają te możliwości dla poszczególnych składników urządzenia. Na odpowiednich urządzeniach z wieloma składnikami, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia z oprogramowaniem układowym, pozwala to na zastosowanie odrębnej konfiguracji i odrębnych warunków dla każdego składnika.

Na końcu tego samouczka będziesz mieć dwie aplikacje Node.js:

* **CreateIdentities**, która tworzy tożsamość urządzenia, tożsamość modułu oraz skojarzony klucz zabezpieczeń na potrzeby łączenia klientów modułu i urządzenia.

* **UpdateModuleTwinReportedProperties**, która wysyła zaktualizowane zgłoszone właściwości bliźniaczej reprezentacji modułu do Twojego centrum IoT Hub.

> [!NOTE]
> Aby uzyskać informacji na temat Azure IoT SDKs można użyć, aby tworzyć aplikacje zarówno do uruchamiania na urządzeniach i w zapleczu rozwiązania, zobacz [Azure IoT SDKs](iot-hub-devguide-sdks.md).

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)
* An IoT Hub.
* Zainstaluj najnowszą wersję [zestawu SDK środowiska Node.js](https://github.com/Azure/azure-iot-sdk-node).

Centrum IoT zostało już utworzone i masz nazwę hosta oraz parametry połączenia usługi IoT Hub potrzebne do ukończenia pozostałej części tego samouczka.

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Tworzenie tożsamości urządzenia i tożsamości modułu w usłudze IoT Hub

W tej sekcji utworzysz aplikację Node.js, która tworzy tożsamość urządzenia i tożsamości modułu w rejestrze tożsamości w usłudze IoT hub. Urządzenie lub moduł nie mogą łączyć się z centrum IoT Hub, jeśli nie mają odpowiedniego wpisu w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz sekcję "Identity registry" [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide-identity-registry.md). Uruchomienie tej aplikacji konsolowej powoduje wygenerowanie unikatowego identyfikatora i klucza zarówno dla urządzenia, jak i modułu. Urządzenie i moduł korzystają z tych wartości w celu identyfikowania się podczas wysyłania komunikatów urządzenie-chmura do usługi IoT Hub. W identyfikatorach jest uwzględniana wielkość liter.

1. Utwórz katalog do przechowywania kodu.

2. W tym katalogu, należy najpierw uruchomić **npm init -y** utworzyć pusty plik package.json przy użyciu ustawień domyślnych. Jest to plik projektu kodu.

3. Uruchom **npm zainstalować azure-iothub - S\@modułów w wersji zapoznawczej** instalacji zestawu SDK usługi wewnątrz **node_modules** podkatalogu.

    > [!NOTE]
    > Node_modules nazwę podkatalogu używa modułu program word oznacza "bibliotekę węzła". Okres, w tym miejscu nie ma nic wspólnego z modułów usługi IoT Hub.

4. Utwórz następujący plik js w Twoim katalogu. Wywołaj go **add.js**. Kopiuj i wklejaj parametry połączenia Centrum i nazwy Centrum.

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

Ta aplikacja tworzy tożsamość urządzenia o identyfikatorze **myFirstDevice** i tożsamości modułu o identyfikatorze **myFirstModule** obszarze urządzenie **myFirstDevice**. (Jeśli ten identyfikator modułu już istnieje w rejestrze tożsamości, kod po prostu pobiera informacje o istniejącym module). Aplikacja następnie wyświetla klucz podstawowy dla tej tożsamości. Tego klucza używa się w symulowanej aplikacji modułu, aby nawiązać połączenie z centrum IoT.

Uruchom to za pomocą add.js węzła. Podaje ciąg połączenia dla Twojej tożsamości urządzenia i inny dla Twojej tożsamości modułu.

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia i modułu tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. W rejestrze tożsamości są przechowywane identyfikatory urządzeń i klucze służące jako poświadczenia zabezpieczeń. W rejestrze tożsamości są także przechowywane flagi włączenia/wyłączenia dla każdego urządzenia, za pomocą których można wyłączyć dostęp do danego urządzenia. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Nie istnieje flaga włączenia/wyłączenia tożsamości modułów. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide-identity-registry.md).

## <a name="update-the-module-twin-using-nodejs-device-sdk"></a>Zaktualizować bliźniaczą reprezentację modułu przy użyciu zestawu SDK urządzenia środowiska Node.js

W tej sekcji opisano tworzenie środowiska Node.js aplikacji na urządzenie symulowane, która aktualizuje bliźniaczą reprezentację modułu zgłoszonych właściwości.

1. **Pobierz parametry połączenia modułu** — Zaloguj się do [witryny Azure portal](https://portal.azure.com/). Przejdź do centrum IoT Hub i kliknij pozycję Urządzenia IoT. Znajdź myFirstDevice, otwórz go i zobaczyć myFirstModule został pomyślnie utworzony. Skopiuj parametry połączenia modułu. Będą potrzebne w następnym kroku.

   ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-node-node-module-twin-getstarted/module-detail.png)

2. Jest podobna do Ciebie została w poprzednim kroku, Utwórz katalog dla kodu urządzenia i zainicjować go i zainstaluj zestaw SDK urządzeń za pomocą usługi NPM (**npm zainstalować -S: azure-iot-device-amqp\@modułów w wersji zapoznawczej**).

   > [!NOTE]
   > Polecenia npm install mogą czuć się wolno. O cierpliwość, go to przeciągnij w dół mnóstwo kodu z repozytorium pakietów.

   > [!NOTE]
   > Jeśli zostanie wyświetlony błąd informujący, że npm błąd! Rejestr błąd analizy json jest bezpiecznie zignorować. Jeśli zostanie wyświetlony błąd informujący, że npm błąd! Rejestr błąd analizy json jest bezpiecznie zignorować.

3. Utwórz plik o nazwie twin.js. Skopiuj i Wklej ciąg tożsamości użytkownika modułu.

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

4. Teraz uruchom za pomocą polecenia **twin.js węzła**.

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

* [Wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md)

* [Wprowadzenie do usługi IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)