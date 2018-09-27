---
title: Komunikaty z chmury do urządzenia z usługą Azure IoT Hub (Node) | Dokumentacja firmy Microsoft
description: Jak wysyłanie komunikatów z chmury do urządzeń do urządzenia z usługi Azure IoT hub przy użyciu zestawów SDK usługi Azure IoT dla środowiska Node.js. Możesz zmodyfikować aplikacji symulowanego urządzenia do odbierania komunikatów z chmury do urządzeń i modyfikowania aplikacji zaplecza, do wysyłania komunikatów z chmury do urządzeń.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: c2b8df708a568fa995cf3af783c112127726811b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2018
ms.locfileid: "47221812"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Wprowadzenie
Usługa Azure IoT Hub to w pełni zarządzana usługa, która ułatwia włączanie bezpieczną i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Rozpoczynanie pracy z usługą IoT Hub] samouczek pokazuje sposób tworzenia Centrum IoT hub, aprowizować w nim tożsamości urządzenia i kodu aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się [Rozpoczynanie pracy z usługą IoT Hub]. Przedstawia on sposób do:

* Z zapleczem rozwiązania wysyłanie komunikatów z chmury do urządzeń do pojedynczego urządzenia za pomocą usługi IoT Hub.
* Odbieranie komunikatów z chmury do urządzeń na urządzeniu.
* Z zapleczem rozwiązania, żądania potwierdzenia dostarczania (*opinii*) dla wiadomości wysyłanych do urządzenia z usługi IoT Hub.

Można znaleźć więcej informacji na temat komunikatów z chmury do urządzeń w [usługi IoT Hub — przewodnik dewelopera][IoT Hub developer guide - C2D].

Na końcu tego samouczka możesz uruchomić dwie aplikacje konsolowe środowiska Node.js:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonej w [Rozpoczynanie pracy z usługą IoT Hub], który nawiązuje połączenie z Centrum IoT i odbiera komunikaty z chmury do urządzenia.
* **SendCloudToDeviceMessage**, która jest wysyłana wiadomość chmury do urządzenia w aplikacji symulowanego urządzenia za pomocą usługi IoT Hub i odbiera jego potwierdzenie dostawy.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK na wielu platformach i językach (w tym C, Java i Javascript) za pomocą zestawów SDK urządzeń Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące łączenia urządzenia do kodu w tym samouczku i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT].
> 
> 

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko Node.js w wersji 4.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia
W tej sekcji zmodyfikujesz symulowanej aplikacji urządzenia utworzone w [Rozpoczynanie pracy z usługą IoT Hub] do odbierania komunikatów z chmury do urządzeń z usługi IoT hub.

1. Za pomocą edytora tekstu Otwórz plik SimulatedDevice.js.
2. Modyfikowanie **connectCallback** funkcji do obsługi komunikatów wysyłanych z usługi IoT Hub. W tym przykładzie zawsze wywołuje urządzenia **pełną** funkcję, aby powiadomić usługi IoT Hub zostało przetworzone wiadomości. Nową wersję pakietu **connectCallback** funkcja wygląda jak poniższy fragment kodu:
   
    ```javascript
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
   > [!NOTE]
   > Jeśli używasz protokołu HTTPS zamiast protokołu MQTT, AMQP jako transportu, **DeviceClient** wystąpienia sprawdza, czy komunikaty z usługi IoT Hub rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji na temat różnic między Obsługa protokołu MQTT, AMQP i protokołu HTTPS i ograniczania przepustowości usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatów chmura urządzenie
W tej sekcji utworzysz aplikację konsoli środowiska Node.js, która wysyła komunikaty z chmury do urządzenia w aplikacji symulowanego urządzenia. Potrzebny jest identyfikator urządzenia, które urządzenia zostały dodane w [Rozpoczynanie pracy z usługą IoT Hub] samouczka. Należy również parametry połączenia Centrum IoT Hub dla Centrum można znaleźć w [Azure Portal].

1. Utwórz pusty folder o nazwie **sendcloudtodevicemessage**. W **sendcloudtodevicemessage** folderze utwórz plik Package.JSON, uruchamiając następujące polecenie w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:
   
    ```shell
    npm init
    ```
2. W wierszu polecenia w **sendcloudtodevicemessage** folder, uruchom następujące polecenie, aby zainstalować **azure-iothub** pakietu:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Za pomocą edytora tekstów Utwórz **SendCloudToDeviceMessage.js** w pliku **sendcloudtodevicemessage** folderu.
4. Dodaj następujący kod `require` instrukcji na początku **SendCloudToDeviceMessage.js** pliku:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Dodaj następujący kod do **SendCloudToDeviceMessage.js** pliku. Zastąp wartość symbolu zastępczego "{Centrum parametry połączenia Centrum iot}" przy użyciu parametrów połączenia usługi IoT Hub dla Centrum utworzonego w [Rozpoczynanie pracy z usługą IoT Hub] samouczka. Zastąp symbol zastępczy "{id urządzenia}" z Identyfikatorem urządzenia, które urządzenia zostały dodane w [Rozpoczynanie pracy z usługą IoT Hub] samouczka:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Dodaj następującą funkcję, aby wydrukować wyniki operacji do konsoli:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Dodaj następującą funkcję umożliwiającą wydrukowanie dostarczanie komunikatów zwrotnych do konsoli:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Dodaj następujący kod, aby wysłać wiadomość do Twojego urządzenia i obsłużyć komunikat opinii, gdy urządzenie potwierdza komunikatu chmura urządzenie:
   
    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```
9. Zapisz i Zamknij **SendCloudToDeviceMessage.js** pliku.

## <a name="run-the-applications"></a>Uruchamianie aplikacji
Teraz można uruchomić aplikacje.

1. W wierszu polecenia w **simulateddevice** folder, uruchom następujące polecenie do wysyłania telemetrii do usługi IoT Hub oraz nasłuchiwać komunikatów z chmury do urządzeń:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Uruchamianie aplikacji symulowanego urządzenia][img-simulated-device]
2. W wierszu polecenia w **sendcloudtodevicemessage** folder, uruchom następujące polecenie, aby wysłać komunikatu chmura urządzenie i poczekaj na opinie potwierdzenia:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Uruchamianie aplikacji na wysyłanie poleceń chmura urządzenie][img-send-command]
   
   > [!NOTE]
   > Sake dla uproszczenia w tym samouczku nie implementuje żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).
   > 
   > 

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób wysyłania i odbierania komunikatów z chmury do urządzeń. 

Aby wyświetlić przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [Akcelerator rozwiązań IoT zdalnego monitorowania platformy Azure].

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [Przewodnik dla deweloperów usługi IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Rozpoczynanie pracy z usługą IoT Hub]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Przewodnik dla deweloperów usługi IoT Hub]: iot-hub-devguide.md
[Centrum deweloperów Azure IoT]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Azure Portal]: https://portal.azure.com
[Akcelerator rozwiązań IoT zdalnego monitorowania platformy Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/
