---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (Node) | Dokumentacja firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub do wywołania metody bezpośredniej na wielu urządzeniach. Przy użyciu zestawów SDK usługi Azure IoT dla środowiska Node.js aplikacji urządzenia symulowanego i app service, aby uruchomić zadanie.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 10/06/2017
ms.openlocfilehash: 1a42b590fc83b89cd9f90998f835fc58f84ea960
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597399"
---
# <a name="schedule-and-broadcast-jobs-node"></a>Planowanie i emitowanie zadań (Node)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji zaplecza utworzyć i śledzić zadania, które planować i aktualizowania milionów urządzeń.  Zadania mogą służyć do następujących czynności:

* Aktualizowanie żądanych właściwości
* Aktualizacji tagów
* Wywoływanie metod bezpośrednich

Model zadanie opakowuje jedną z następujących czynności i śledzi postęp wykonywania względem zbiór urządzeń, która jest zdefiniowana przez zapytanie w bliźniaczej reprezentacji urządzenia.  Na przykład aby wywołać metodę ponownego uruchomienia na 10 000 urządzeń, określonych przez zapytanie bliźniacza reprezentacja urządzenia i zaplanować w czasie przyszłych zadań można użyć aplikacji zaplecza. Tej aplikacji można śledzić postęp, zgodnie z każdego z tych urządzeń odbierania i wykonać metodę ponownego uruchomienia.

Dowiedz się więcej na temat każdego z tych możliwości w następujących artykułach:

* Bliźniacza reprezentacja urządzenia i właściwości: [Wprowadzenie do bliźniaków urządzeń](iot-hub-node-node-twin-getstarted.md) i [samouczka: Jak korzystać z właściwości bliźniaczych reprezentacji urządzeń](tutorial-device-twins.md)

* Metody bezpośrednie: [Usługi IoT Hub developer guide - metod bezpośrednich](iot-hub-devguide-direct-methods.md) i [samouczek: metody bezpośrednie](quickstart-control-device-node.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji symulowanego urządzenia środowiska Node.js, która ma bezpośrednie metody, która umożliwia **lockDoor**, który można wywoływać za pomocą zaplecza rozwiązania.

* Tworzenie aplikacji konsolowej Node.js, która wywołuje **lockDoor** bezpośrednie metody w aplikacji symulowanego urządzenia za pomocą zadania i aktualizacje żądane właściwości przy użyciu zadania urządzeń.

Na końcu tego samouczka będziesz mieć dwie aplikacje Node.js:

* **simDevice.js**, który nawiązuje połączenie z Centrum IoT przy użyciu tożsamości urządzenia i odbiera **lockDoor** bezpośrednie metody.

* **scheduleJobService.js**, która wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia i aktualizacji bliźniaczej reprezentacji urządzenia żądane właściwości przy użyciu zadania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko node.js w wersji 10.0.x lub nowszej [przygotowywanie środowiska projektowego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisano, jak zainstalować środowisko Node.js na potrzeby tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli środowiska Node.js, która reaguje na metodę bezpośrednią wywołaną przez chmurę, co powoduje wyzwolenie symulowanego **lockDoor** metody.

1. Utwórz nowy pusty folder o nazwie **simDevice**.  W **simDevice** folderze utwórz plik Package.JSON, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

   ```
   npm init
   ```

2. W wierszu polecenia w **simDevice** folder, uruchom następujące polecenie, aby zainstalować **azure-iot-device** pakiet zestawu SDK urządzenia i **azure-iot-device-mqtt** pakietu:
   
   ```
   npm install azure-iot-device azure-iot-device-mqtt --save
   ```

3. Za pomocą edytora tekstu Utwórz nowy **simDevice.js** w pliku **simDevice** folderu.

4. Dodaj następujące "Wymagaj" instrukcji na początku **simDevice.js** pliku:
   
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

6. Dodaj następującą funkcję, aby obsłużyć **lockDoor** metody.
   
    ```
    var onLockDoor = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        console.log('Locking Door!');
    };
    ```

7. Dodaj następujący kod, aby zarejestrować program obsługi dla **lockDoor** metody.

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
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań do wywoływania metody bezpośredniej i aktualizowanie właściwości bliźniaczej reprezentacji urządzenia

W tej sekcji utworzysz aplikację konsoli środowiska Node.js, która inicjuje zdalnej **lockDoor** na urządzeniu przy użyciu metody bezpośredniej i aktualizowania właściwości bliźniaczej reprezentacji urządzenia.

1. Utwórz nowy pusty folder o nazwie **scheduleJobService**.  W **scheduleJobService** folderze utwórz plik Package.JSON, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:

    ```
    npm init
    ```

2. W wierszu polecenia w **scheduleJobService** folder, uruchom następujące polecenie, aby zainstalować **azure-iothub** pakiet zestawu SDK urządzenia i **azure-iot-device-mqtt** pakiet:
   
    ```
    npm install azure-iothub uuid --save
    ```

3. Za pomocą edytora tekstu Utwórz nowy **scheduleJobService.js** w pliku **scheduleJobService** folderu.

4. Dodaj następujące "Wymagaj" instrukcji na początku **dmpatterns_gscheduleJobServiceetstarted_service.js** pliku:
   
    ```
    'use strict';
   
    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Dodaj następujące deklaracje zmiennych i Zastąp wartości symboli zastępczych:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var queryCondition = "deviceId IN ['myDeviceId']";
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  300;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```

6. Dodaj następującą funkcję, która służy do monitorowania wykonywania zadania:
   
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

8. Dodaj następujący kod, aby zaplanować zadanie, które można zaktualizować bliźniaczej reprezentacji urządzenia:
   
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

1. W wierszu polecenia w **simDevice** folder, uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponowny rozruch.
   
    ```
    node simDevice.js
    ```

2. W wierszu polecenia w **scheduleJobService** folder, uruchom następujące polecenie, aby wyzwolić zadania, aby zablokować drzwi biblioteki i zaktualizować bliźniaczej reprezentacji
   
    ```
    node scheduleJobService.js
    ```

3. Zostanie wyświetlona odpowiedź urządzenia, do metody bezpośredniej w konsoli.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto zadania można zaplanować metody bezpośredniej do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Aby kontynuować wprowadzenie do usługi IoT Hub i wzorców zarządzania urządzeniami, takich jak zdalne za pośrednictwem aktualizacji oprogramowania układowego air, zobacz [samouczka: Jak zaktualizować oprogramowanie układowe](tutorial-firmware-update.md).

Aby kontynuować wprowadzenie do usługi IoT Hub, zobacz [wprowadzenie do usługi Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
