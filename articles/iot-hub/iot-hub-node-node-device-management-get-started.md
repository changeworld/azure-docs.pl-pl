---
title: Wprowadzenie do zarządzania urządzeniami Centrum IoT Azure (węzeł) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami Centrum IoT można zainicjować ponownego uruchomienia urządzenia zdalnego. Przy użyciu zestawu SDK IoT Azure dla środowiska Node.js aplikacji symulowane urządzenie, która zawiera metodę bezpośredniego i aplikacji usługi, która wywołuje metodę bezpośredniego.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: juanpere
ms.openlocfilehash: 54658ea72ac8e32db45774e87e3ab177d68046fa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635954"
---
# <a name="get-started-with-device-management-node"></a>Wprowadzenie do zarządzania urządzeniami (węzeł)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj portalu Azure do tworzenia Centrum IoT i tworzenia tożsamości urządzenia w Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie zawierającej bezpośredniego metodę, która wykonuje ponowny rozruch tego urządzenia. Bezpośrednie metody są wywoływane z chmury.
* Tworzenie aplikacji konsoli Node.js, który wywołuje metodę bezpośredniego ponowny rozruch w aplikacji symulowane urządzenie za pomocą Centrum IoT.

Na końcu tego samouczka znajdują się dwie aplikacje konsoli Node.js:

**dmpatterns_getstarted_device.js**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje metoda bezpośrednia ponowny rozruch, symuluje ponowny rozruch komputera fizycznego i raporty czasu ostatniego ponownego uruchomienia.

**dmpatterns_getstarted_service.js**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie Wyświetla odpowiedzi i wyświetla zaktualizowane zgłoszone właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko node.js w wersji 4.0.x lub nowszym <br/>  [Przygotowywanie środowiska projektowego] [ lnk-dev-setup] opisuje sposób instalowania programu Node.js w tym samouczku w systemie Windows lub Linux.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji zostanie

* Tworzenie aplikacji konsolowej Node.js, która reaguje na metodę bezpośrednią wywołaną przez chmurę
* Wyzwalacz symulowane urządzenie ponowny rozruch
* Włącz zapytania dwie urządzenia w celu identyfikowania urządzeń przy użyciu właściwości zgłoszone, a podczas ostatniego ponownego uruchomienia

1. Utwórz pusty folder o nazwie **manageddevice**.  W folderze **manageddevice** utwórz plik package.json przy użyciu następującego polecenia z poziomu wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **manageddevice** folderu, uruchom następujące polecenie, aby zainstalować **azure iot urządzenia** pakiet SDK urządzenia i **azure-iot urządzenie mqtt** pakietu:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Za pomocą edytora tekstu, Utwórz **dmpatterns_getstarted_device.js** w pliku **manageddevice** folderu.
4. Dodaj następujące "wymagane" instrukcje na początku **dmpatterns_getstarted_device.js** pliku:
   
    ```
    'use strict';
   
    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```
5. Dodaj zmienną **connectionString** i użyj jej do utworzenia wystąpienia **Client**.  Parametry połączenia należy zastąpić ciąg połączenia urządzenia.  
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```
6. Dodanie następujących funkcji do zaimplementowania metoda bezpośrednia na urządzeniu
   
    ```
    var onReboot = function(request, response) {
   
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
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
7. Otwórz połączenie z Centrum IoT i uruchomić odbiornik metoda bezpośrednia:
   
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
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalacz zdalnego ponownego uruchomienia na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji utworzysz aplikację konsoli Node.js, która inicjuje zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniego. Aplikacja używa urządzenia dwie zapytania do odnajdywania ostatniego ponownego uruchomienia dla tego urządzenia.

1. Utwórz pusty folder o nazwie **triggerrebootondevice**.  W **triggerrebootondevice** folderu, Utwórz plik package.json za pomocą następującego polecenia z wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **triggerrebootondevice** folderu, uruchom następujące polecenie, aby zainstalować **Centrum iothub azure** pakiet SDK urządzenia i **azure-iot urządzenie mqtt** pakietu:
   
    ```
    npm install azure-iothub --save
    ```
3. Za pomocą edytora tekstu, Utwórz **dmpatterns_getstarted_service.js** w pliku **triggerrebootondevice** folderu.
4. Dodaj następujące "wymagane" instrukcje na początku **dmpatterns_getstarted_service.js** pliku:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Dodaj następujące deklaracje zmiennych i zastąp symbole zastępcze:
   
    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
6. Dodaj następującą funkcję można wywołać metody urządzenia o ponownym uruchomieniu urządzenia docelowego:
   
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
7. Dodaj następującą funkcję wyszukiwania dla urządzenia i pobrać ostatniego ponownego uruchomienia:
   
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
8. Dodaj następujący kod, aby wywoływać funkcje wywołujące metoda bezpośrednia ponownego rozruchu i zapytań dla ostatniego ponownego uruchomienia:
   
    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
9. Zapisz i Zamknij **dmpatterns_getstarted_service.js** pliku.

## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **manageddevice** folderu, uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.
   
    ```
    node dmpatterns_getstarted_device.js
    ```
2. W wierszu polecenia w **triggerrebootondevice** folderu, uruchom następujące polecenie, aby zdalne ponowne uruchamianie systemu i kwerendę dla dwie urządzenia znaleźć ostatniego ponownego rozruchu czasu.
   
    ```
    node dmpatterns_getstarted_service.js
    ```
3. Zobaczysz odpowiedź urządzenia do metody bezpośrednio w konsoli.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
