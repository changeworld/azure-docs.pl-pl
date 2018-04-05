---
title: Komunikaty chmury do urządzenia z Centrum IoT Azure (węzeł) | Dokumentacja firmy Microsoft
description: Jak wysłać wiadomości chmury do urządzenia na urządzeniu z Centrum Azure IoT przy użyciu zestawów SDK IoT Azure dla środowiska Node.js. Możesz zmodyfikować aplikację symulowane urządzenie, aby odbierać komunikaty z chmury do urządzenia i modyfikowania aplikacji zaplecza, do wysyłania wiadomości chmury do urządzenia.
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: 526fdce85df388fec04dd5df35183200c1478570
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-node"></a>Wysyłanie komunikatów chmury do urządzenia z Centrum IoT (węzeł)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Wprowadzenie
Centrum IoT Azure jest w pełni zarządzaną usługę, która umożliwia włączanie i niezawodności komunikację dwukierunkową między milionów urządzeń i zaplecze rozwiązania. [Rozpoczynanie pracy z Centrum IoT] samouczek przedstawia sposób tworzenia Centrum IoT, udostępnić tożsamość urządzenia w nim i kodem aplikacji symulowane urządzenie, który wysyła wiadomości urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

W tym samouczku opiera się na [Rozpoczynanie pracy z Centrum IoT]. Jak przedstawiono na:

* Z sieci zaplecza rozwiązania wysyłać chmury do urządzenia do jednego urządzenia za pośrednictwem Centrum IoT.
* Komunikaty chmury do urządzenia na urządzeniu.
* Z sieci zaplecza rozwiązania, żądania potwierdzenia dostarczenia (*opinii*) dla wiadomości wysyłanych do urządzenia z Centrum IoT.

Można znaleźć więcej informacji na temat wiadomości chmury do urządzenia w [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].

Na końcu tego samouczka możesz uruchomić dwóch aplikacji konsoli Node.js:

* **SimulatedDevice**, zmodyfikowanej wersji aplikacji utworzonych w [Rozpoczynanie pracy z Centrum IoT], która łączy się z Centrum IoT i odbiera komunikaty chmury do urządzenia.
* **SendCloudToDeviceMessage**, który wysyła komunikat chmury do urządzenia do aplikacji symulowane urządzenie za pomocą Centrum IoT i następnie odbiera jego potwierdzenie dostarczenia.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków (w tym C, Java i Javascript) za pośrednictwem zestawy SDK urządzenia Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu Podłącz urządzenie do kodu w tym samouczku i zwykle z Centrum IoT Azure, zobacz [Azure IoT Developer Center].
> 
> 

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko Node.js w wersji 4.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie komunikatów w aplikacji symulowane urządzenie
W tej sekcji możesz zmodyfikować aplikację symulowane urządzenie, utworzony w [Rozpoczynanie pracy z Centrum IoT] odbierać komunikaty z chmury do urządzenia z Centrum IoT.

1. Za pomocą edytora tekstu, otwórz plik SimulatedDevice.js.
2. Modyfikowanie **connectCallback** funkcji obsługi wiadomości wysyłane z Centrum IoT. W tym przykładzie zawsze wywołuje urządzenia **pełną** funkcji, aby powiadomić Centrum IoT, że został on przetworzony wiadomości. Nową wersję pakietu **connectCallback** funkcja wygląda następujący fragment kodu:
   
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
   > Jeśli jest używany protokół HTTPS zamiast MQTT lub AMQP do transportu **DeviceClient** wystąpienia sprawdza, czy komunikaty z Centrum IoT rzadko (mniej niż co 25 minut). Aby uzyskać więcej informacji na temat różnic między MQTT, protokołu AMQP lub HTTPS obsługi i ograniczania Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera][IoT Hub developer guide - C2D].
   > 
   > 

## <a name="send-a-cloud-to-device-message"></a>Wyślij wiadomość chmury do urządzenia
W tej sekcji utworzysz aplikację konsoli Node.js, która wysyła komunikaty chmury do urządzenia do aplikacji symulowane urządzenie. Potrzebny jest identyfikator urządzenia urządzenia dodane w [Rozpoczynanie pracy z Centrum IoT] samouczka. Należy również parametry połączenia Centrum IoT, który znajduje się w Centrum [portalu Azure].

1. Utwórz pusty folder o nazwie **sendcloudtodevicemessage**. W **sendcloudtodevicemessage** folderu, Utwórz plik package.json za pomocą następującego polecenia z wiersza polecenia. Zaakceptuj wszystkie ustawienia domyślne:
   
    ```shell
    npm init
    ```
2. Z wiersza polecenia w **sendcloudtodevicemessage** folderu, uruchom następujące polecenie, aby zainstalować **Centrum iothub azure** pakietu:
   
    ```shell
    npm install azure-iothub --save
    ```
3. Za pomocą edytora tekstu, Utwórz **SendCloudToDeviceMessage.js** w pliku **sendcloudtodevicemessage** folderu.
4. Dodaj następujące `require` instrukcje na początku **SendCloudToDeviceMessage.js** pliku:
   
    ```javascript
    'use strict';
   
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```
5. Dodaj następujący kod do **SendCloudToDeviceMessage.js** pliku. Zastąp wartość symbolu zastępczego "{iot hub parametry połączenia}" z Centrum IoT parametry połączenia dla koncentratora utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczka. Zastąp symbol zastępczy "{identyfikator urządzenia}" o identyfikatorze urządzenia urządzenie dodane w [Rozpoczynanie pracy z Centrum IoT] samouczek:
   
    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';
   
    var serviceClient = Client.fromConnectionString(connectionString);
    ```
6. Dodaj następującą funkcję Drukowanie wyników operacji do konsoli:
   
    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. Dodaj następującą funkcję drukowania dostarczania komunikatów opinię do konsoli:
   
    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```
8. Dodaj następujący kod, aby wysłać komunikat do Twojego urządzenia i obsłużyć komunikat opinii, gdy urządzenie potwierdza komunikatu chmura urządzenie:
   
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

1. W wierszu polecenia w **simulateddevice** folderu, uruchom następujące polecenie, aby wysłać dane telemetryczne z Centrum IoT i nasłuchiwać komunikatów chmury do urządzenia:
   
    ```shell
    node SimulatedDevice.js 
    ```
   
    ![Uruchamianie aplikacji symulowane urządzenie][img-simulated-device]
2. W wierszu polecenia **sendcloudtodevicemessage** folderu, uruchom następujące polecenie, aby wysłać wiadomość chmury do urządzenia i poczekaj, aż opinii potwierdzenia:
   
    ```shell
    node SendCloudToDeviceMessage.js 
    ```
   
    ![Uruchamianie aplikacji, aby wysłać polecenie chmury do urządzenia][img-send-command]
   
   > [!NOTE]
   > Sake na prostotę w tym samouczku nie implementuje wszystkie zasady ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (na przykład wykładniczego wycofywania), zgodnie z sugestią podaną w artykuł w witrynie MSDN [obsługi błędów przejściowych].
   > 
   > 

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku przedstawiono sposób wysyłania i odbierania wiadomości chmury do urządzenia. 

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [pakiet IoT Azure].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Rozpoczynanie pracy z Centrum IoT]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[Azure IoT Developer Center]: http://azure.microsoft.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[obsługi błędów przejściowych]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portalu Azure]: https://portal.azure.com
[pakiet IoT Azure]: https://azure.microsoft.com/documentation/suites/iot-suite/
