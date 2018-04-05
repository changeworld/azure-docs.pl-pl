---
title: Aktualizacja oprogramowania układowego urządzenia z Centrum IoT Azure (węzeł) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami w usłudze Azure IoT Hub zainicjować aktualizację oprogramowania układowego urządzenia. Przy użyciu zestawów SDK IoT Azure dla środowiska Node.js aplikacji symulowane urządzenie i usługi aplikacji, które wyzwala aktualizacji oprogramowania układowego.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: juanpere
ms.openlocfilehash: e8ef556793534c9eb8300ee262a4b134960f862a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-nodenode"></a>Umożliwia zarządzanie urządzeniami zainicjować aktualizację oprogramowania układowego urządzenia (węzeł/węzeł)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczka przedstawiono sposób użycia [dwie urządzenia] [ lnk-devtwin] i [bezpośrednie metody ] [ lnk-c2dmethod] podstawowych zdalnie ponownego uruchomienia urządzenia. W tym samouczku korzysta z tej samej podstawowych Centrum IoT i zawiera wskazówki i pokazuje, jak wykonać aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany podczas wdrażania aktualizacji oprogramowania układowego przykładowej urządzenia Intel Edison.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji konsoli Node.js, która wywołuje metodę firmwareUpdate bezpośrednio w aplikacji symulowane urządzenie za pomocą Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie, który implementuje **firmwareUpdate** metoda bezpośrednia. Ta metoda inicjuje procesem wieloetapowym czeka na pobranie obrazu oprogramowania układowego, pobierze obraz oprogramowania układowego i na koniec dotyczy oprogramowania układowego obrazu. Podczas każdego etapu aktualizacji urządzenie korzysta z właściwości zgłoszony do raport dotyczący postępu.

Na końcu tego samouczka znajdują się dwie aplikacje konsoli Node.js:

**dmpatterns_fwupdate_service.js**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie Wyświetla odpowiedzi i okresowo (co 500 MS.) Wyświetla zaktualizowanego zgłoszone właściwości.

**dmpatterns_fwupdate_device.js**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje firmwareUpdate metoda bezpośrednia, uruchamia proces wielu stanu do symulowania, w tym aktualizacji oprogramowania układowego: Oczekiwanie na obraz Pobierz pobieranie nowy obraz, a ostatecznie stosowania obrazu.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Środowisko node.js w wersji 4.0.x lub nowszym <br/>  [Przygotowywanie środowiska projektowego] [ lnk-dev-setup] opisuje sposób instalowania programu Node.js w tym samouczku w systemie Windows lub Linux.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Postępuj zgodnie z [wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md) artykuł, aby utworzyć Centrum IoT i pobrać parametry połączenia Centrum IoT.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji utworzysz aplikację konsoli Node.js, która inicjuje aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja korzysta metoda bezpośrednia zainicjować aktualizację, a zapytania dwie urządzenia okresowo pobrać stanu aktualizacji oprogramowania układowego active.

1. Utwórz pusty folder o nazwie **triggerfwupdateondevice**.  W **triggerfwupdateondevice** folderu, Utwórz plik package.json za pomocą następującego polecenia z wiersza polecenia.  Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **triggerfwupdateondevice** folderu, uruchom następujące polecenie, aby zainstalować **azure iot koncentrator** pakietu:
   
    ```
    npm install azure-iothub --save
    ```
3. Za pomocą edytora tekstu, Utwórz **dmpatterns_getstarted_service.js** w pliku **triggerfwupdateondevice** folderu.
4. Dodaj następujące "wymagane" instrukcje na początku **dmpatterns_getstarted_service.js** pliku:
   
    ```
    'use strict';
   
    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```
5. Dodaj następujące deklaracje zmiennych i zastąp symbole zastępcze:
   
    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
6. Dodaj następującą funkcję można znaleźć i wyświetlana wartość firmwareUpdate zgłosił właściwości.
   
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
7. Dodaj następującą funkcję można wywołać metody firmwareUpdate przeprowadzić ponowny rozruch docelowego urządzenia:
   
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
8. Na koniec należy dodać następująca funkcja kodu w celu uruchomienia sekwencji aktualizacji oprogramowania układowego i rozpoczęcia okresowo wyświetlania właściwości zgłoszone:
   
    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
9. Zapisz i Zamknij **dmpatterns_fwupdate_service.js** pliku.

[!INCLUDE [iot-hub-device-firmware-update](../../includes/iot-hub-device-firmware-update.md)]

## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia w **manageddevice** folderu, uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.
   
    ```
    node dmpatterns_fwupdate_device.js
    ```
2. W wierszu polecenia w **triggerfwupdateondevice** folderu, uruchom następujące polecenie, aby zdalne ponowne uruchamianie systemu i kwerendę dla dwie urządzenia znaleźć ostatniego ponownego rozruchu czasu.
   
    ```
    node dmpatterns_fwupdate_service.js
    ```
3. Zobaczysz odpowiedź urządzenia do metody bezpośrednio w konsoli.

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używana metoda bezpośrednia do wyzwolenia aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postępy aktualizacji oprogramowania układowego zgłoszone właściwości.

Aby dowiedzieć się, jak rozszerzyć IoT, Twoje rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach, zobacz [emisji zadania i harmonogramu] [ lnk-tutorial-jobs] samouczka.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
