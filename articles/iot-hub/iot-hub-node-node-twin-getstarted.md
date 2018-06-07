---
title: Rozpoczynanie pracy z Centrum IoT Azure urządzenia twins (węzeł) | Dokumentacja firmy Microsoft
description: Jak używać twins urządzenia Azure IoT Hub Dodawanie tagów, a następnie użyć kwerendy Centrum IoT. Przy użyciu zestawów SDK IoT Azure dla środowiska Node.js i usługi aplikacji, która dodaje znaczniki i uruchamia kwerendy Centrum IoT aplikacji symulowane urządzenie.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: dbb28d7df104887a4056489e9f693ce28ec406df
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635104"
---
# <a name="get-started-with-device-twins-node"></a>Rozpoczynanie pracy z urządzenia twins (węzeł)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka masz dwie aplikacje konsoli Node.js:

* **AddTagsAndQuery.js**, aplikacji zaplecza Node.js, która dodaje znaczniki i zapytanie twins urządzenia.
* **TwinSimulatedDevice.js**, aplikacja Node.js, która symuluje urządzenie, które łączy do Centrum IoT z tożsamości urządzenia utworzony wcześniej, a następnie raportuje stanu łączności.

> [!NOTE]
> Artykuł [Azure IoT SDK] [ lnk-hub-sdks] informacje na temat zestawów SDK IoT Azure można tworzyć aplikacje zarówno urządzenia, jak i zaplecza.
> 
> 

Do ukończenia tego samouczka należy spełnić następujące warunki:

* Środowisko Node.js w wersji 4.0.x lub nowszej.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi
W tej sekcji, Utwórz aplikację konsoli Node.js, która dodaje dwie urządzeń skojarzonych z lokalizacji metadanych **myDeviceId**. Tworzy następnie kwerendę twins urządzenia przechowywane w Centrum IoT Wybieranie urządzeń znajdujących się w Stanach Zjednoczonych i te, które są raportowania komórkowej połączenia.

1. Utwórz nowy, pusty folder o nazwie **addtagsandqueryapp**. W **addtagsandqueryapp** folderu, Utwórz nowy plik package.json za pomocą następującego polecenia z wiersza polecenia. Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **addtagsandqueryapp** folderu, uruchom następujące polecenie, aby zainstalować **Centrum iothub azure** pakietu:
   
    ```
    npm install azure-iothub --save
    ```
3. Za pomocą edytora tekstu, Utwórz nową **AddTagsAndQuery.js** w pliku **addtagsandqueryapp** folderu.
4. Dodaj następujący kod do **AddTagsAndQuery.js** plików i Zastąp **{parametry połączenia Centrum iot}** symbol zastępczy parametrów połączenia Centrum IoT skopiowane podczas tworzenia Centrum:
   
        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{iot hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
   
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
   
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });
   
    **Rejestru** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z usługi. Poprzedni kod najpierw inicjuje **rejestru** obiekt, a następnie pobiera dwie urządzenia dla **myDeviceId**i na koniec aktualizuje jego tagi informacji żądaną lokalizację.
   
    Po zaktualizowaniu tagi wywołuje **queryTwins** funkcji.
5. Dodaj następujący kod na końcu **AddTagsAndQuery.js** do zaimplementowania **queryTwins** funkcji:
   
        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
   
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };
   
    Poprzedni kod wykonuje dwa zapytania: pierwszy wybiera tylko twins urządzenia urządzeń znajdujących się w **Redmond43** zakładu, a drugi udoskonalanie zapytanie, aby wybrać tylko te urządzenia, które także są połączone za pośrednictwem sieci komórkowej.
   
    Poprzedni kod, w którym tworzy **zapytania** obiektów, określa maksymalną liczbę zwracanych dokumentów. **Zapytania** zawiera obiekt **hasMoreResults** właściwości typu boolean, którego można użyć do wywołania **nextAsTwin** metody kilka razy, aby pobrać wszystkie wyniki. Wywołana metoda **dalej** jest dostępna dla wyników, które są nie twins urządzenia, na przykład wyniki zapytań agregacji.
6. Uruchom aplikację klawiszem:
   
        node AddTagsAndQuery.js
   
    Jedno urządzenie w wynikach zadać kwerendy powinna być widoczna dla wszystkich urządzeń znajdujących się w **Redmond43** i Brak dla zapytania, który ogranicza wyniki do urządzenia, które korzystają z sieci komórkowej.
   
    ![][1]

W następnej sekcji utworzysz aplikację urządzenia, która raportuje informacje dotyczące łączności i zmienia się wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzeń
W tej sekcji zostanie utworzona aplikacja konsoli Node.js łączący się do Centrum jako **myDeviceId**, a następnie aktualizacje jego dwie urządzenia użytkownika zgłosiła właściwości zawierają informacje, że jest połączony za pomocą sieci komórkowej.


1. Utwórz nowy, pusty folder o nazwie **reportconnectivity**. W **reportconnectivity** folderu, Utwórz nowy plik package.json za pomocą następującego polecenia z wiersza polecenia. Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```
2. Z wiersza polecenia w **reportconnectivity** folderu, uruchom następujące polecenie, aby zainstalować **azure iot urządzenia**, i **azure-iot urządzenie mqtt** pakietu:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. Za pomocą edytora tekstu, Utwórz nową **ReportConnectivity.js** w pliku **reportconnectivity** folderu.
4. Dodaj następujący kod do **ReportConnectivity.js** plików i Zastąp **{ciąg połączenia urządzenia}** symbol zastępczy w ciągu połączenia urządzenia został skopiowany podczas tworzenia **myDeviceId** tożsamości urządzenia:
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
   
            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };
   
                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });
   
    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z urządzenia. Poprzedni kod po jego inicjuje **klienta** obiektów, pobiera dwie urządzenia dla **myDeviceId** i aktualizuje jego właściwość zgłoszone informacje o łączności.
5. Uruchamianie aplikacji urządzeń
   
        node ReportConnectivity.js
   
    Powinien zostać wyświetlony komunikat `twin state reported`.
6. Teraz, gdy urządzenie zgłosiło jego informacje dotyczące łączności, powinna pojawić się w obu zapytania. Przejdź wstecz w **addtagsandqueryapp** folderu i ponownie uruchom zapytania:
   
        node AddTagsAndQuery.js
   
    Teraz **myDeviceId** powinny być wyświetlane w obu wyników zapytania.
   
    ![][3]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodaje metadane urządzenia jako tagi z aplikacji zaplecza i zapisano aplikacji symulowane urządzenie informacji w raporcie urządzenia łączności w dwie urządzenia. Przedstawiono również sposób kwerendy te informacje przy użyciu języka przypominającego SQL Centrum IoT zapytania.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzenia z [Rozpoczynanie pracy z Centrum IoT] [ lnk-iothub-getstarted] samouczka
* Konfigurowanie urządzeń przy użyciu właściwości żądaną dwie urządzenia z [Użyj żądanego właściwości, aby skonfigurować urządzenia] [ lnk-twin-how-to-configure] samouczka
* urządzenia interakcyjne (takich jak włączanie wentylator z aplikacji kontrolowane przez użytkownika) i sterować za pomocą [metody bezpośredniego] [ lnk-methods-tutorial] samouczka.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
