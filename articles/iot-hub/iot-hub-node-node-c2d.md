---
title: Komunikaty z chmury do urządzenia z usługą Azure IoT Hub (Node) | Dokumentacja firmy Microsoft
description: Jak wysyłanie komunikatów z chmury do urządzeń do urządzenia z usługi Azure IoT hub przy użyciu zestawów SDK usługi Azure IoT dla środowiska Node.js. Możesz zmodyfikować aplikacji symulowanego urządzenia do odbierania komunikatów z chmury do urządzeń i modyfikowania aplikacji zaplecza, do wysyłania komunikatów z chmury do urządzeń.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.openlocfilehash: e2c3c3988193242cd0afe0135b019c7e6f73b59c
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596727"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Wysyłanie komunikatów z chmury do urządzeń z usługą IoT Hub (Node)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Wprowadzenie
Usługa Azure IoT Hub to w pełni zarządzana usługa, która ułatwia włączanie bezpieczną i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md) samouczek pokazuje sposób tworzenia Centrum IoT hub, aprowizować w nim tożsamości urządzenia i kodu aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md). Przedstawia on sposób do:

* Z zapleczem rozwiązania wysyłanie komunikatów z chmury do urządzeń do pojedynczego urządzenia za pomocą usługi IoT Hub.
* Odbieranie komunikatów z chmury do urządzeń na urządzeniu.
* Z zapleczem rozwiązania, żądania potwierdzenia dostarczania (*opinii*) dla wiadomości wysyłanych do urządzenia z usługi IoT Hub.

Można znaleźć więcej informacji na temat komunikatów z chmury do urządzeń w [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide-messaging.md).

Na końcu tego samouczka możesz uruchomić dwie aplikacje konsolowe środowiska Node.js:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonej w [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md), który nawiązuje połączenie z Centrum IoT i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage**, która jest wysyłana wiadomość chmury do urządzenia w aplikacji symulowanego urządzenia za pomocą usługi IoT Hub i odbiera jego potwierdzenie dostawy.

> [!NOTE]
> Usługa IoT Hub obsługuje zestaw SDK na wielu platformach i językach (w tym C, Java i Javascript) za pomocą zestawów SDK urządzeń Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące łączenia urządzenia do kodu w tym samouczku i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://azure.microsoft.com/develop/iot).
>

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko node.js w wersji 10.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial) w zaledwie kilka minut.)

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia

W tej sekcji zmodyfikujesz symulowanej aplikacji urządzenia utworzone w [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md) do odbierania komunikatów z chmury do urządzeń z usługi IoT hub.

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
   > Jeśli używasz protokołu HTTPS zamiast protokołu MQTT, AMQP jako transportu, **DeviceClient** wystąpienia sprawdza, czy komunikaty z usługi IoT Hub rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji na temat różnic między Obsługa protokołu MQTT, AMQP i protokołu HTTPS i ograniczania przepustowości usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide-messaging.md).
   >

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatów chmura urządzenie

W tej sekcji utworzysz aplikację konsoli środowiska Node.js, która wysyła komunikaty z chmury do urządzenia w aplikacji symulowanego urządzenia. Potrzebny jest identyfikator urządzenia, które urządzenia zostały dodane w [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md) samouczka. Należy również parametry połączenia Centrum IoT Hub dla Centrum można znaleźć w [witryny Azure portal](https://portal.azure.com).

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

5. Dodaj następujący kod do **SendCloudToDeviceMessage.js** pliku. Zastąp wartość symbolu zastępczego "{Centrum parametry połączenia Centrum iot}" przy użyciu parametrów połączenia usługi IoT Hub dla Centrum utworzonego w [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md) samouczka. Zastąp symbol zastępczy "{id urządzenia}" z Identyfikatorem urządzenia, które urządzenia zostały dodane w [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md) samouczka:
   
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
   
    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-node-node-c2d/receivec2d.png)

2. W wierszu polecenia w **sendcloudtodevicemessage** folder, uruchom następujące polecenie, aby wysłać komunikatu chmura urządzenie i poczekaj na opinie potwierdzenia:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Uruchamianie aplikacji na wysyłanie poleceń chmura urządzenie](./media/iot-hub-node-node-c2d/sendc2d.png)
   
   > [!NOTE]
   > Sake dla uproszczenia w tym samouczku nie implementuje żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).
   >

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób wysyłania i odbierania komunikatów z chmury do urządzeń. 

Aby wyświetlić przykłady kompletne rozwiązania end-to-end, które używają usługi IoT Hub, zobacz [akcelerator rozwiązań Azure IoT zdalne monitorowanie](https://azure.microsoft.com/documentation/suites/iot-suite/).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań usługi IoT Hub, zobacz [usługi IoT Hub — przewodnik dewelopera](iot-hub-devguide.md).