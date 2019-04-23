---
title: Rozpoczynanie pracy z usługą Azure IoT Hub bliźniaczych reprezentacji urządzeń (Node) | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure IoT Hub bliźniaczych reprezentacji urządzeń Dodawanie tagów, a następnie użyć zapytania usługi IoT Hub. Przy użyciu zestawów SDK usługi Azure IoT dla środowiska Node.js w aplikacji symulowanego urządzenia i aplikacji usługi, która dodaje znaczniki, która uruchamia kwerendę usługi IoT Hub.
author: fsautomata
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: elioda
ms.openlocfilehash: f93abac563d47f6505f42d29e882698ef31174bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795889"
---
# <a name="get-started-with-device-twins-node"></a>Rozpoczynanie pracy z bliźniaczych reprezentacji urządzeń (Node)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe środowiska Node.js:

* **AddTagsAndQuery.js**, aplikacji zaplecza Node.js, która dodaje znaczniki i zapytań bliźniaczych reprezentacji urządzeń.

* **TwinSimulatedDevice.js**, aplikacja Node.js, która symuluje urządzenie, który nawiązuje połączenie z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia, a następnie raportuje stanu łączności.

> [!NOTE]
> Artykuł [Azure IoT SDKs](iot-hub-devguide-sdks.md) informacje dotyczące zestawów SDK usługi Azure IoT, w której można tworzyć aplikacje zarówno w przypadku urządzeń, jak i zaplecza.
>

Do ukończenia tego samouczka potrzebne są następujące elementy:

* Środowisko Node.js w wersji 4.0.x lub nowszej.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikacji konsolowej Node.js, która dodaje metadanymi lokalizacji do bliźniaczej reprezentacji urządzenia skojarzone z **myDeviceId**. Następnie wykonuje zapytanie bliźniacze reprezentacje urządzeń, przechowywane w usłudze IoT hub, wybieranie urządzeń znajduje się w Stanach Zjednoczonych i te, które zgłaszanej połączenie komórkowe.

1. Utwórz nowy pusty folder o nazwie **addtagsandqueryapp**. W **addtagsandqueryapp** folderu, Utwórz nowy plik package.json przy użyciu następującego polecenia w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:

    ```
    npm init
    ```

2. W wierszu polecenia w **addtagsandqueryapp** folder, uruchom następujące polecenie, aby zainstalować **azure-iothub** pakietu:
   
    ```
    npm install azure-iothub --save
    ```

3. Za pomocą edytora tekstu Utwórz nowy **AddTagsAndQuery.js** w pliku **addtagsandqueryapp** folderu.

4. Dodaj następujący kod do **AddTagsAndQuery.js** plik i zastąpić **{parametry połączenia Centrum iot hub}** symbolu zastępczego parametrami połączenia Centrum IoT Hub, kopiowane podczas tworzenia Centrum:

   ``` javascript
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
   ```

    **Rejestru** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z bliźniaczych reprezentacji urządzeń z usługi. Poprzedni kod najpierw inicjuje **rejestru** obiektu, a następnie pobiera bliźniaczą reprezentację urządzenia dla **myDeviceId**i aktualizuje jej tagi z informacjami o wybraną lokalizację.

    Po zaktualizowaniu tagi wywoływanych przez nią **queryTwins** funkcji.

5. Dodaj następujący kod na końcu **AddTagsAndQuery.js** do zaimplementowania **queryTwins** funkcji:

   ```javascript
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
   ```

    Powyższy kod wykonuje dwa zapytania: pierwszy wybiera tylko bliźniaków urządzeń urządzeń znajdujących się w **Redmond43** zakładu produkcyjnego, a drugi usprawniają zapytanie, aby wybrać tylko urządzenia, które są także połączone za pośrednictwem sieci komórkowej.

    Powyższy kod podczas tworzenia **zapytania** obiektów, określa maksymalną liczbę zwróconych dokumentów. **Zapytania** obiekt zawiera **hasMoreResults** właściwość typu boolean, którego można użyć do wywołania **nextAsTwin** metody kilka razy, aby pobrać wszystkie wyniki. Metoda wywoływana **dalej** jest dostępna dla wyników, które są nie bliźniacze reprezentacje urządzeń, na przykład wyniki zapytań agregacji.

6. Uruchom aplikację za pomocą:

    ```
        node AddTagsAndQuery.js
    ```

   Powinien zostać wyświetlony jedno urządzenie w wynikach dotyczące zadawania zapytań dla wszystkich urządzeń znajdujących się w **Redmond43** a dla zapytania, które ogranicza wyniki do urządzenia korzystające z sieci komórkowej.
   
    ![Zobacz jedno urządzenie w wynikach zapytania](media/iot-hub-node-node-twin-getstarted/service1.png)

W następnej sekcji utworzysz aplikację urządzenie, raportuje informacje o łączności, która zmienia się wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz Aplikacja konsoli Node.js, który nawiązuje połączenie z Centrum jako **myDeviceId**i następnie aktualizacje jego bliźniaczej reprezentacji urządzenia użytkownika zgłoszonych właściwości zawierają informacje, że jest ona połączona korzystania z sieci komórkowej.

1. Utwórz nowy pusty folder o nazwie **reportconnectivity**. W **reportconnectivity** folderu, Utwórz nowy plik package.json przy użyciu następującego polecenia w wierszu polecenia. Zaakceptuj wszystkie ustawienia domyślne:
   
    ```
    npm init
    ```

2. W wierszu polecenia w **reportconnectivity** folder, uruchom następujące polecenie, aby zainstalować **azure-iot-device**, i **azure-iot-device-mqtt** pakietu:
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

3. Za pomocą edytora tekstu Utwórz nowy **ReportConnectivity.js** w pliku **reportconnectivity** folderu.

4. Dodaj następujący kod do **ReportConnectivity.js** plik i zastąpić **{parametry połączenia urządzenia}** symbol zastępczy parametrów połączenia urządzenia, które są kopiowane po utworzeniu **myDeviceId** tożsamości urządzenia:

    ```
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
    ```

    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z bliźniaczych reprezentacji urządzeń z urządzenia. Powyższy kod po inicjuje **klienta** obiektów, pobiera bliźniaczą reprezentację urządzenia dla **myDeviceId** i aktualizuje informacje o łączności jego zgłaszanej właściwości.

5. Uruchamianie aplikacji urządzenia

    ```   
        node ReportConnectivity.js
    ```

    Powinien zostać wyświetlony komunikat `twin state reported`.

6. Teraz, gdy urządzenie jest zgłaszane jego informacje o łączności, powinien pojawić się w obu zapytań. Przejdź wstecz w **addtagsandqueryapp** folderu i ponownie uruchom zapytania:

    ```   
        node AddTagsAndQuery.js
    ```

    Tym razem **myDeviceId** powinno pojawić się w obu wyników zapytania.

    ![Pokaż myDeviceId w obu wyników zapytania](media/iot-hub-node-node-twin-getstarted/service2.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodane metadane urządzenia jako tagi z aplikacji zaplecza, a aplikacja powstała z jednego urządzenia symulowanego do raportu informacje o łączności urządzenia w bliźniaku urządzenia. Przedstawiono również sposób wykonywania zapytań te informacje przy użyciu języka zapytań usługi IoT Hub podobnego do SQL.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* Wysyłanie danych telemetrycznych z urządzeń przy użyciu [Rozpoczynanie pracy z usługą IoT Hub](quickstart-send-telemetry-node.md) samouczka

* Konfigurowanie urządzeń przy użyciu żądane właściwości bliźniaczej reprezentacji urządzenia za pomocą [Użyj żądane właściwości, aby skonfigurować urządzenia](tutorial-device-twins.md) samouczka

* Sterowanie urządzeniami interaktywnie (na przykład włączając wentylator z aplikacji kontrolowanej przez użytkownika), za pomocą [używanie metod bezpośrednich](quickstart-control-device-node.md) samouczka.