---
title: Wprowadzenie do zarządzania urządzeniami Azure IoT Hub (Node) | Dokumentacja firmy Microsoft
description: Jak zainicjować ponownego uruchomienia urządzenia zdalnego za pomocą zarządzania urządzeniami usługi IoT Hub. Przy użyciu zestawu SDK usługi Azure IoT dla środowiska Node.js aplikacji symulowanego urządzenia, która obejmuje metody bezpośredniej i app service, która wywołuje metody bezpośredniej.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.openlocfilehash: 15166d3943bc72a2eeff3580cefdd264ecaba61d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258089"
---
# <a name="get-started-with-device-management-node"></a>Wprowadzenie do zarządzania urządzeniami (Node)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj [witryny Azure portal](https://portal.azure.com) Tworzenie Centrum IoT i tworzenie tożsamości urządzenia w usłudze IoT hub.

* Tworzenie aplikacji symulowanego urządzenia, która zawiera bezpośrednie metodę, która wywołuje ponowne uruchomienie tego urządzenia. Metody bezpośrednie są wywoływane z poziomu chmury.

* Tworzenie aplikacji konsoli środowiska Node.js, która wywołuje metody bezpośredniej ponowny rozruch w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe środowiska Node.js:

* **dmpatterns_getstarted_device.js**, łączy się z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia otrzymuje metody bezpośredniej o ponowne uruchomienie komputera, symuluje ponowne uruchomienie komputera fizycznego, a następnie raportuje czas ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service.js**, która wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia, wyświetla odpowiedzi i wyświetla zaktualizowany zgłoszonych właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko Node.js w wersji 4.0.x lub nowszej. [Przygotowywanie środowiska projektowego](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md) opisano, jak zainstalować środowisko Node.js na potrzeby tego samouczka w systemie Windows lub Linux.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji należy wykonać następujące czynności:

* Tworzenie aplikacji konsolowej Node.js, która reaguje na metodę bezpośrednią wywołaną przez chmurę

* Wyzwolić ponowne uruchomienie symulowanego urządzenia

* Włączanie zapytań bliźniaczych reprezentacji urządzeń do identyfikowania urządzeń za pomocą zgłoszonych właściwości, a podczas ostatniego ponownego uruchomienia

1. Utwórz pusty folder o nazwie **manageddevice**.  W folderze **manageddevice** utwórz plik package.json przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
      
    ```
    npm init
    ```

2. W wierszu polecenia w **manageddevice** folder, uruchom następujące polecenie, aby zainstalować **azure-iot-device** pakiet zestawu SDK urządzenia i **azure-iot-device-mqtt** pakiet:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstów Utwórz **dmpatterns_getstarted_device.js** w pliku **manageddevice** folderu.

4. Dodaj następujące "Wymagaj" instrukcji na początku **dmpatterns_getstarted_device.js** pliku:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  Zastąp parametry połączenia parametrami połączenia urządzenia.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Dodaj następującą funkcję, aby wdrożyć metodę bezpośrednich na urządzeniu
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
   
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };
   
        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
   
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Otwórz połączenie z Centrum IoT hub i uruchomić odbiornika metody bezpośredniej:

   
    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```

8. Zapisz i Zamknij **dmpatterns_getstarted_device.js** pliku.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Zdalne ponowne uruchamianie systemu na urządzeniu, korzystając z metody bezpośredniej wyzwalacza

W tej sekcji służy do tworzenia aplikacji konsolowej Node.js, która inicjuje zdalnego ponowny rozruch na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań bliźniaczych reprezentacji urządzeń, aby odnaleźć ostatniego ponownego uruchomienia dla tego urządzenia.

1. Utwórz pusty folder o nazwie **triggerrebootondevice**. W **triggerrebootondevice** folderze utwórz plik Package.JSON, uruchamiając następujące polecenie w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```

2. W wierszu polecenia w **triggerrebootondevice** folder, uruchom następujące polecenie, aby zainstalować **azure-iothub** pakiet zestawu SDK urządzenia i **azure-iot-device-mqtt** pakiet:
   
    ```
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstów Utwórz **dmpatterns_getstarted_service.js** w pliku **triggerrebootondevice** folderu.

4. Dodaj następujące "Wymagaj" instrukcji na początku **dmpatterns_getstarted_service.js** pliku:

  
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Dodaj następujące deklaracje zmiennych i Zastąp wartości symboli zastępczych:

   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```

6. Dodaj następującą funkcję, można wywołać metody urządzenia, aby ponownie uruchomić urządzenie docelowe:
   
    ```
    var startRebootDevice = function(twin) {
   
        var methodName = "reboot";
   
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
   
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) {
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Dodaj następującą funkcję, aby wyszukiwać urządzenia i Uzyskaj czas ostatniego ponownego uruchomienia:
   
    ```
    var queryTwinLastReboot = function() {
   
        registry.getTwin(deviceToReboot, function(err, twin){
   
            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```

8. Dodaj następujący kod, aby wywoływać funkcje, które mogą powodować metody bezpośredniej ponowny rozruch i zapytanie o czas ostatniej ponowny rozruch:

   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```

9. Zapisz i Zamknij **dmpatterns_getstarted_service.js** pliku.

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **manageddevice** folder, uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponowny rozruch.

   
    ```
    node dmpatterns_getstarted_device.js
    ```

2. W wierszu polecenia w **triggerrebootondevice** folder, uruchom następujące polecenie, można wyzwolić ponowne uruchomienie zdalnego i wykonywania zapytań o bliźniaczej reprezentacji urządzenia można znaleźć w ciągu ostatnich ponowny rozruch czasu.

   
    ```
    node dmpatterns_getstarted_service.js
    ```

3. Zostanie wyświetlona odpowiedź urządzenia, do metody bezpośredniej w konsoli.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]