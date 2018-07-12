---
title: Aktualizacja oprogramowania układowego urządzenia za pomocą usługi Azure IoT Hub (Node) | Dokumentacja firmy Microsoft
description: Jak zainicjować aktualizację oprogramowania układowego urządzenia, za pomocą zarządzania urządzeniami w usłudze Azure IoT Hub. Przy użyciu zestawów SDK usługi Azure IoT dla środowiska Node.js aplikacji symulowanego urządzenia i app service, która powoduje aktualizację oprogramowania układowego.
author: juanjperez
manager: cberlin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: 0cd8c019cf9a65e0e72227ba99c1995a45ed4067
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452434"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Umożliwia zarządzanie urządzeniami zainicjować aktualizację oprogramowania układowego urządzenia (węzeł/Node)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczków pokazano, jak używać [bliźniaczej reprezentacji urządzenia] [ lnk-devtwin] i [metody bezpośrednie ] [ lnk-c2dmethod] podstawowych, aby zdalnie ponownie uruchomić urządzenie. Ten samouczek używa tych samych podstawowych usługi IoT Hub i zawiera wskazówki i dowiesz się, jak przeprowadzić aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany we wdrażaniu aktualizacji oprogramowania układowego dla przykładu urządzenia Intel Edison.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji konsolowej Node.js, która wywołuje metody bezpośredniej firmwareUpdate w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.
* Tworzenie aplikacji symulowanego urządzenia, który implementuje **firmwareUpdate** bezpośrednie metody. Ta metoda inicjuje proces wieloetapowych czeka do pobierania obrazu oprogramowania układowego, który pobierze obraz oprogramowania układowego i na koniec ma zastosowanie obrazu oprogramowania układowego. Na etapie każdej aktualizacji urządzenie używa zgłaszanych właściwości do raportowania postępu.

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe środowiska Node.js:

**dmpatterns_fwupdate_service.js**, który wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia, wyświetla odpowiedzi i okresowo (co 500 ms) Wyświetla zaktualizowany zgłoszonych właściwości.

**dmpatterns_fwupdate_device.js**, która łączy się z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia otrzymuje metody bezpośredniej o firmwareUpdate, uruchamia proces wielostanowy zasymulować, w tym aktualizacji oprogramowania układowego: Oczekiwanie na obraz Pobierz pobierania nowego obrazu i na koniec stosowania obrazu.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko node.js w wersji 4.0.x lub nowszej <br/>  [Przygotowywanie środowiska projektowego] [ lnk-dev-setup] opisano, jak zainstalować środowisko Node.js na potrzeby tego samouczka w systemie Windows lub Linux.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Postępuj zgodnie z [wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md) artykuł, aby utworzyć Centrum IoT i pobieranie parametrów połączenia usługi IoT Hub.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, korzystając z metody bezpośredniej
W tej sekcji utworzysz aplikację konsoli Node.js, która inicjuje aktualizacji oprogramowania układowego zdalnego na urządzeniu z systemem. Aplikacja używa metody bezpośredniej do inicjowania aktualizacji i używa zapytań bliźniaczych reprezentacji urządzeń, aby okresowo pobieranie stanu aktualizacji oprogramowania układowego active.

1. Utwórz pusty folder o nazwie **triggerfwupdateondevice**.  W **triggerfwupdateondevice** folderze utwórz plik Package.JSON, uruchamiając następujące polecenie w wierszu polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. W wierszu polecenia w **triggerfwupdateondevice** folder, uruchom następujące polecenie, aby zainstalować **azure-iot-hub** pakietu:
   
    ```
    npm install azure-iothub --save
    ```
3. Za pomocą edytora tekstów Utwórz **dmpatterns_getstarted_service.js** w pliku **triggerfwupdateondevice** folderu.
4. Dodaj następujące "Wymagaj" instrukcji na początku **dmpatterns_getstarted_service.js** pliku:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Dodaj następujące deklaracje zmiennych i Zastąp wartości symboli zastępczych:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Dodaj następującą funkcję, aby znaleźć i Wyświetl tę wartość firmwareUpdate zgłaszanych właściwości.
   
    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```
7. Dodaj następującą funkcję, można wywołać metody firmwareUpdate, aby ponownie uruchomić urządzenie docelowe:
   
    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
   
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
   
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
   
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```
8. Na koniec Dodaj następującą funkcję do kodu w celu uruchomienia sekwencji aktualizacji oprogramowania układowego i rozpoczęcia okresowo wyświetlania zgłoszonych właściwości:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Zapisz i Zamknij **dmpatterns_fwupdate_service.js** pliku.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **manageddevice** folder, uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponowny rozruch.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. W wierszu polecenia w **triggerfwupdateondevice** folder, uruchom następujące polecenie, można wyzwolić ponowne uruchomienie zdalnego i wykonywania zapytań o bliźniaczej reprezentacji urządzenia można znaleźć w ciągu ostatnich ponowny rozruch czasu.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Zostanie wyświetlona odpowiedź urządzenia, do metody bezpośredniej w konsoli.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używane metody bezpośredniej do wyzwalania aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postęp aktualizacji oprogramowania układowego w zgłaszanych właściwości.

Aby dowiedzieć się, jak rozszerzyć rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach IoT, zobacz [harmonogramu i zadań emisji] [ lnk-tutorial-jobs] samouczka.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
