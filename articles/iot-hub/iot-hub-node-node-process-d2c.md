---
title: Routing komunikatów z Centrum IoT Azure (węzeł) | Dokumentacja firmy Microsoft
description: Jak komunikaty do przetwarzania komunikatów urządzenia do chmury Azure IoT Hub przy użyciu reguł routingu oraz niestandardowe punkty końcowe wysłania wiadomości do innych usług zaplecza.
services: iot-hub
documentationcenter: node
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: baed8a4c5086a9e3c757a3ecb8a6811c7be6b09e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>Routing komunikatów z Centrum IoT (węzeł)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

W tym samouczku opiera się na [Rozpoczynanie pracy z Centrum IoT] samouczka.  Samouczek:

* Pokazuje, jak używać reguł routingu do wysyłania wiadomości urządzenia do chmury w sposób łatwy, na podstawie konfiguracji.
* Przedstawiono sposób izolować interakcyjne komunikaty, które wymagają natychmiastowego działania z zaplecza rozwiązania dla dalszego przetwarzania.  Na przykład urządzenie może wysłać komunikat alarmu, które wyzwala Wstawianie biletu do systemu CRM.  Z kolei wiadomości punktu danych, takich jak telemetrii temperatury źródła danych do aparatu analizy.

Na końcu tego samouczka możesz uruchomić trzech aplikacji konsoli Node.js:

* **SimulatedDevice.js**, zmodyfikowanej wersji aplikacji utworzonych w [Rozpoczynanie pracy z Centrum IoT] samouczek, wysyła komunikaty urządzenia do chmury punktu danych, co drugi i interaktywne wiadomości urządzenia do chmury dla losowych Interwał. Ta aplikacja korzysta z protokołu MQTT do komunikowania się z Centrum IoT.
* **ReadDeviceToCloudMessages.js** Wyświetla telemetrii wysyłane przez aplikację na urządzeniu.
* **ReadCriticalQueue.js** usuwania kolejki krytycznych wiadomości z kolejki usługi Service Bus dołączony do Centrum IoT.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków, w tym C, Java i JavaScript. Aby uzyskać instrukcje dotyczące sposobu Zamień w tym samouczku urządzenie fizyczne urządzenia oraz sposób podłączania urządzeń do Centrum IoT, zobacz [Azure IoT Developer Center].

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Pełną wersję pracy [Rozpoczynanie pracy z Centrum IoT] samouczka.
* Środowisko Node.js w wersji 4.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Zalecamy również informacje o [usługi Azure Storage] i [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Wysyłanie wiadomości interaktywne z aplikacji przez urządzenia
W tej sekcji możesz zmodyfikować aplikację urządzenia, utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczek czasami wysłać wiadomości, które wymagają natychmiastowego przetwarzania.

1. Użyj edytora tekstów, aby otworzyć **simulateddevice\SimulatedDevice.js** pliku. Ten plik zawiera kod **SimulatedDevice** aplikacji utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczka.

2. Zastąp **connectCallback** funkcja następującym kodem:

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    Ta metoda losowo dodaje właściwość `"level": "critical"` i `"level": "storage"` dla komunikatów wysyłanych przez urządzenia, która symuluje komunikat, który wymaga natychmiastowego działania przez zaplecza aplikacji lub jeden, który musi być trwale przechowywane. Aplikacja obsługuje routing wiadomości opartych na treść komunikatu.
   
   > [!NOTE]
   > Można użyć właściwości wiadomości do przesyłania wiadomości dla różnych scenariuszy, w tym chłodni ścieżką podczas przetwarzania, oprócz tu przykładzie aktywnej ścieżki.

2. Zapisz i Zamknij **simulateddevice\SimulatedDevice.js** pliku.

    > [!NOTE]
    > Firma Microsoft zaleca, aby zaimplementować zasady ponawiania wykładniczego wycofywania, zgodnie z sugestią podaną w artykuł w witrynie MSDN np. [obsługi błędów przejściowych].

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>Dodawanie kolejki usługi Service Bus do IoT hub i tras wiadomości do niego

W tej sekcji Tworzenie kolejki usługi Service Bus, połącz go z Centrum IoT i skonfigurować do wysyłania wiadomości do kolejki na podstawie obecności właściwość w komunikacie Centrum IoT. Aby uzyskać więcej informacji o sposobie przetwarzania komunikatów z kolejek usługi Service Bus, zobacz [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-node].

1. Tworzenie kolejki usługi Service Bus, zgodnie z opisem w [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-node]. Zanotuj nazwę przestrzeni nazw i kolejki.

    > [!NOTE]
    > Tematy dotyczące używany jako punkty końcowe Centrum IoT nie może mieć i kolejek usługi Service Bus **sesji** lub **wykrywania duplikatów** włączone. Jeśli dowolna z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w portalu Azure.

2. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.

    ![Punkty końcowe Centrum IoT][30]

3. W **punkty końcowe** bloku, kliknij przycisk **Dodaj** u góry, aby dodać kolejki do Centrum IoT. Nazwa punktu końcowego **CriticalQueue** i umożliwia wybranie listach rozwijanych **kolejki usługi Service Bus**, przestrzeń nazw magistrali usług, w której znajduje się kolejki i nazwy kolejki. Gdy wszystko będzie gotowe, kliknij przycisk **OK** u dołu.  

    ![Dodawanie punktu końcowego][31]

4. Teraz kliknij **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. Wybierz **komunikaty** jako źródło danych. Wprowadź `level="critical"` jako warunek i wybierz polecenie **CriticalQueue** jako punktu końcowego niestandardowych jako routingu końcowy reguły. Kliknij przycisk **zapisać** u dołu.  

    ![Dodawanie trasy][32]

    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. To ustawienie jest domyślną konfigurację Centrum IoT.

    ![Trasy rezerwowej][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcjonalnie) Odczyt z kolejki punktu końcowego

W tej sekcji utworzysz aplikację konsoli Node.js, która odczytuje krytycznych wiadomości z usługi Service Bus IoT. Dalsze informacje wyświetlane w [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-node]. 

1. Utwórz pusty folder o nazwie `readcriticalqueue`. W folderze `readcriticalqueue` utwórz plik package.json, uruchamiając następujące polecenie w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```cmd/sh
    npm init
    ```

2. Z wiersza polecenia w `readcriticalqueue` folderu, uruchom następujące polecenie, aby zainstalować **azure** pakietu:

    ```cmd/sh
    npm install azure --save
    ```

3. Za pomocą edytora tekstu, Utwórz **ReadCriticalQueue.js** w pliku `readcriticalqueue` folderu.

4. Dodaj następujące `require` instrukcje na początku **ReadCriticalQueue.js** pliku:

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. Dodaj następujące deklaracja zmiennej i zastąp symbole zastępcze nazwą ciągu i kolejki połączenia magistrali usługi IoT:

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. Dodaj następujące dwie funkcje, które drukują dane wyjściowe do konsoli:

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. Zapisz i Zamknij **ReadCriticalQueue.js** pliku.

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia trzech aplikacji.

1. Aby uruchomić **ReadDeviceToCloudMessages.js** aplikacji, w wierszu polecenia lub powłoki przejdź do folderu readdevicetocloudmessages i uruchom następujące polecenie:

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![Uruchom odczytu — d2c — liczba komunikatów][readd2c]

2. Aby uruchomić **ReadCriticalQueue.js** aplikacji, w wierszu polecenia lub powłoki przejdź do folderu readcriticalqueue i uruchom następujące polecenie:

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![Uruchom odczytu — krytyczne — liczba komunikatów][readqueue]

3. Aby uruchomić **SimulatedDevice.js** aplikacji, w wierszu polecenia lub powłoki przejdź do folderu simulateddevice i uruchom następujące polecenie:

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![Uruchom symulowane urządzenie][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcjonalnie) Dodaj kontener magazynu do IoT hub i tras wiadomości do niego

W tej sekcji Utwórz konto magazynu, połącz go z Centrum IoT i skonfigurować do wysyłania komunikatów do konta, na podstawie obecności właściwość w komunikacie Centrum IoT. Aby uzyskać więcej informacji o sposobie zarządzania magazynu, zobacz [Rozpoczynanie pracy z magazynem Azure][usługi Azure Storage].

 > [!NOTE]
   > Jeśli nie masz tylko jeden **punktu końcowego**, może skonfigurować **StorageContainer** oprócz **CriticalQueue** i uruchom oba simulatneously.

1. Utwórz konto magazynu, zgodnie z opisem w [dokumentacji usługi Magazyn Azure][lnk-storage]. Zanotuj nazwę konta.

2. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.

3. W **punkty końcowe** bloku, wybierz opcję **CriticalQueue** punktu końcowego, a następnie kliknij przycisk **usunąć**. Kliknij przycisk **tak**, a następnie kliknij przycisk **Dodaj**. Nazwa punktu końcowego **StorageContainer** i umożliwia wybranie listach rozwijanych **kontenera magazynu Azure**i Utwórz **konta magazynu** i **magazynu kontener**.  Zanotuj nazwy.  Gdy wszystko będzie gotowe, kliknij przycisk **OK** u dołu. 

 > [!NOTE]
   > Jeśli nie masz tylko jeden **punktu końcowego**, nie trzeba usunąć **CriticalQueue**.

4. Kliknij przycisk **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. Wybierz **komunikaty** jako źródło danych. Wprowadź `level="storage"` jako warunek i wybierz polecenie **StorageContainer** jako punktu końcowego niestandardowych jako routingu końcowy reguły. Kliknij przycisk **zapisać** u dołu.  

    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. To ustawienie jest domyślną konfigurację Centrum IoT.

1. Upewnij się, że aplikacja poprzedniej **SimulatedDevice.js** nadal działa. 

1. W portalu Azure, przejdź do konta magazynu, w obszarze **usługa Blob**, kliknij przycisk **Przeglądaj obiekty BLOB...** .  Wybierz kontener użytkownika, przejdź do kliknij plik JSON i kliknij przycisk **Pobierz** do wyświetlania danych.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób niezawodny sposób wysyłania wiadomości urządzenia do chmury przy użyciu funkcji routing komunikatów z Centrum IoT.

[Sposób wysyłania wiadomości chmury do urządzenia z Centrum IoT] [ lnk-c2d] przedstawiono sposób wysyłania komunikatów do urządzeń z Twojej zaplecza rozwiązania.

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [zdalnego monitorowania Azure IoT akcelerator rozwiązań][lnk-suite].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

Aby dowiedzieć się więcej na temat w Centrum IoT rozsyłania wiadomości, zobacz [wysyłania i odbierania wiadomości z Centrum IoT][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[usługi Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Rozpoczynanie pracy z Centrum IoT]: iot-hub-node-node-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot
[obsługi błędów przejściowych]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Obsługa błędu przejściowego]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/