---
title: Planowanie zadań z Centrum IoT Azure (węzeł) | Dokumentacja firmy Microsoft
description: Sposób tworzenia harmonogramu zadań Centrum IoT Azure do wywołania metody bezpośrednio na wielu urządzeniach. Przy użyciu zestawów SDK IoT Azure dla środowiska Node.js symulowane urządzenie aplikacje i usługi aplikacji w celu uruchomienia zadania.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: juanpere
ms.openlocfilehash: 42deb210c55cd4a6c2aa2c7757ed87f8f706c58f
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34634111"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Zadania harmonogramu i emisji (węzeł)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Centrum IoT Azure jest w pełni zarządzaną usługę, która umożliwia aplikacji zaplecza utworzyć i śledzenia zadań, które zaplanować i zaktualizuj milionów urządzeń.  Zadania mogą służyć do następujących czynności:

* Aktualizowanie żądanych właściwości
* Tagi aktualizacji
* Wywołanie metody bezpośredniego

Koncepcyjnie zadanie opakowuje jedną z następujących czynności i śledzi postęp wykonywania pod kątem zestawu urządzeń, jest zdefiniowany przez zapytanie dwie urządzenia.  Na przykład aplikacji zaplecza zadanie służy do wywoływania metody ponownego uruchomienia na 10 000 urządzeń, określonych przez zapytanie dwie urządzenia i zaplanowanych w przyszłości.  Aplikację można następnie śledzić każde z tych urządzeń odbierają i wykonaj metodę ponownego uruchomienia.

Dowiedz się więcej na temat każdego z tych funkcji w tych artykułach:

* Dwie urządzeń i właściwości: [Rozpoczynanie pracy z urządzenia twins] [ lnk-get-started-twin] i [samouczek: sposób użycia właściwości dwie urządzenia][lnk-twin-props]
* Bezpośrednie metody: [przewodnik dewelopera Centrum IoT — metody bezpośredniego] [ lnk-dev-methods] i [samouczek: bezpośrednie metody][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji symulowane urządzenie Node.js, który ma bezpośredni metodę, która umożliwia **lockDoor**, który może być wywoływany przez zaplecza rozwiązania.
* Tworzenie aplikacji konsoli Node.js, który wywołuje **lockDoor** metoda bezpośrednia w aplikacji symulowane urządzenie przy użyciu zadania i aktualizacje żądanej właściwości, za pomocą zadania urządzenia.

Na końcu tego samouczka znajdują się dwie aplikacje Node.js:

**simDevice.js**, która łączy się z Centrum IoT z tożsamości urządzenia i odbiera **lockDoor** metoda bezpośrednia.

**scheduleJobService.js**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie i aktualizuje dwie urządzenia żądanego właściwości przy użyciu zadania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko node.js w wersji 4.0.x lub nowszym <br/>  [Przygotowywanie środowiska projektowego] [ lnk-dev-setup] opisuje sposób instalowania programu Node.js w tym samouczku w systemie Windows lub Linux.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji Tworzenie aplikacji konsoli Node.js, które odpowiada metoda bezpośrednia wywoływane przez chmury, co jest wyzwalane symulowane **lockDoor** metody.

1. Utwórz nowy, pusty folder o nazwie **simDevice**.  W **simDevice** folderu, Utwórz plik package.json za pomocą następującego polecenia z wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **simDevice** folderu, uruchom następujące polecenie, aby zainstalować **azure iot urządzenia** pakiet SDK urządzenia i **azure-iot urządzenie mqtt** pakietu:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Za pomocą edytora tekstu, Utwórz nową **simDevice.js** w pliku **simDevice** folderu.
4. Dodaj następujące "wymagane" instrukcje na początku **simDevice.js** pliku:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Dodaj następującą funkcję obsługi **lockDoor** metody.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```
7. Dodaj następujący kod, aby zarejestrować obsługi dla **lockDoor** metody.
   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub. Register handler for lockDoor direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
8. Zapisz i Zamknij **simDevice.js** pliku.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).
> 
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań dla wywołania metody bezpośredniego i aktualizowanie właściwości dwie urządzenia
W tej sekcji Tworzenie aplikacji konsoli Node.js, który inicjuje zdalnej **lockDoor** na urządzeniu za pomocą innej metody bezpośredniego i aktualizowania właściwości dwie urządzenia.

1. Utwórz nowy, pusty folder o nazwie **scheduleJobService**.  W **scheduleJobService** folderu, Utwórz plik package.json za pomocą następującego polecenia z wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **scheduleJobService** folderu, uruchom następujące polecenie, aby zainstalować **Centrum iothub azure** pakiet SDK urządzenia i **azure-iot urządzenie mqtt** pakiet:
   
    ```
    npm install azure-iothub uuid --save
    ```
3. Za pomocą edytora tekstu, Utwórz nową **scheduleJobService.js** w pliku **scheduleJobService** folderu.
4. Dodaj następujące "wymagane" instrukcje na początku **dmpatterns_gscheduleJobServiceetstarted_service.js** pliku:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```
5. Dodaj następujące deklaracje zmiennych i zastąp symbole zastępcze:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
6. Dodaj następujące funkcji, która jest używana do monitorowania wykonywanie zadania:
   
    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```
7. Dodaj następujący kod, aby zaplanować zadanie, które wywołuje metodę urządzenia:
   
    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        responseTimeoutInSeconds: 15 // Timeout after 15 seconds if device is unable to process method
    };
   
    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                queryCondition,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
8. Dodaj następujący kod, aby zaplanować zadanie do aktualizacji dwie urządzenia:
   
    ```
    var twinPatch = {
       etag: '*', 
       properties: {
           desired: {
               building: '43', 
               floor: 3
           }
       }
    };
   
    var twinJobId = uuid.v4();
   
    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                queryCondition,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
9. Zapisz i Zamknij **scheduleJobService.js** pliku.

## <a name="run-the-applications"></a>Uruchamianie aplikacji
Teraz można uruchomić aplikacje.

1. W wierszu polecenia w **simDevice** folderu, uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.
   
    ```
    node simDevice.js
    ```
2. W wierszu polecenia w **scheduleJobService** folderu, uruchom następujące polecenie, aby wyzwolić zadań, aby zablokować drzwi i zaktualizować dwie
   
    ```
    node scheduleJobService.js
    ```
3. Zobaczysz odpowiedź urządzenia do metody bezpośrednio w konsoli.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku użyto zadanie można zaplanować metoda bezpośrednia urządzenia i aktualizacji właściwości dwie urządzenia.

Aby kontynuować, wprowadzenie do korzystania z Centrum IoT i urządzenia zarządzania wzorców, takich jak zdalnego za pośrednictwem aktualizacji oprogramowania układowego udziału użytkownika, zobacz:

[Samouczek: Sposób wykonywania aktualizacji oprogramowania układowego][lnk-fwupdate]

Aby kontynuować, wprowadzenie do korzystania z Centrum IoT, zobacz [wprowadzenie do korzystania z usługi Azure IoT krawędzi][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
