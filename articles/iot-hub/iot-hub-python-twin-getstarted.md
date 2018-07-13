---
title: Wprowadzenie do bliźniaków urządzeń usługi Azure IoT Hub (Python) | Dokumentacja firmy Microsoft
description: Jak używać usługi Azure IoT Hub bliźniaczych reprezentacji urządzeń Dodawanie tagów, a następnie użyć zapytania usługi IoT Hub. Przy użyciu zestawów SDK usługi Azure IoT dla języka Python w aplikacji symulowanego urządzenia i aplikacji usługi, która dodaje znaczniki, która uruchamia kwerendę usługi IoT Hub.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 12/04/2017
ms.author: kgremban
ms.openlocfilehash: 08e457febaa7522cac86e63c0c187d1e8e49daff
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38619369"
---
# <a name="get-started-with-device-twins-python"></a>Rozpoczynanie pracy z bliźniaczych reprezentacji urządzeń (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe środowiska Python:

* **AddTagsAndQuery.py**, zaplecza aplikacji w języku Python, który dodaje znaczniki i zapytań bliźniaczych reprezentacji urządzeń.
* **ReportConnectivity.py**, aplikacji w języku Python, która symuluje urządzenie, który nawiązuje połączenie z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia, a następnie raportuje stanu łączności.

> [!NOTE]
> Artykuł [Azure IoT SDKs] [ lnk-hub-sdks] informacje dotyczące zestawów SDK usługi Azure IoT, w której można tworzyć aplikacje zarówno w przypadku urządzeń, jak i zaplecza.
> 
> 

Do ukończenia tego samouczka potrzebne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

> [!NOTE]
> Pakiety *pip* dla składników `azure-iothub-service-client` i `azure-iothub-device-client` są obecnie dostępne tylko w przypadku systemu operacyjnego Windows. W przypadku systemu operacyjnego Linux lub Mac OS zapoznaj się z sekcją poświęconą danemu systemowi w poście [Prepare your development environment for Python][lnk-python-devbox] (Przygotowanie środowiska projektowego dla języka Python).
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi
W tej sekcji utworzysz aplikacji konsoli języka Python, która dodaje metadanymi lokalizacji do bliźniaczej reprezentacji urządzenia skojarzone z Twojej **{identyfikator urządzenia}**. Następnie wykonuje zapytanie bliźniacze reprezentacje urządzeń, przechowywane w usłudze IoT hub, wybieranie urządzeń znajduje się w Redmond i te, które zgłaszanej połączenie komórkowe.

1. Otwórz wiersz polecenia i zainstaluj **zestaw SDK usługi Azure IoT Hub dla języka Python**. Zamknij wiersz polecenia po zainstalowaniu zestawu SDK.

    ```
    pip install azure-iothub-service-client
    ```

1. Za pomocą edytora tekstu Utwórz nowy **AddTagsAndQuery.py** pliku.

3. Dodaj następujący kod umożliwiający zaimportowanie wymaganych modułów z zestawu SDK usługi:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. Dodaj następujący kod, zastępując symbol zastępczy `[IoTHub Connection String]` i `[Device Id]` parametrami połączenia dla Centrum IoT i identyfikatora urządzenia został utworzony w poprzednich sekcjach.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

1. Dodaj następujący kod do **AddTagsAndQuery.py** pliku:
   
     ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
        
            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)
        
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)
        
            print ( "Devices in Redmond: " )        
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
         
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )
        
            print ( "" )
        
            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)
                
                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```
   
    **Rejestru** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z bliźniaczych reprezentacji urządzeń z usługi. Ten kod najpierw inicjuje **rejestru** obiektu, a następnie aktualizacji bliźniaczej reprezentacji urządzenia dla **deviceId**, a na koniec wykonuje dwa zapytania. Pierwszy wybiera tylko bliźniaków urządzeń urządzeń znajdujących się w **Redmond43** zakładu produkcyjnego, a drugi usprawniają zapytanie, aby wybrać tylko urządzenia, które są także połączone za pośrednictwem sieci komórkowej.
   
1. Dodaj następujący kod na końcu **AddTagsAndQuery.py** do zaimplementowania **iothub_service_sample_run** funkcji:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. Uruchom aplikację za pomocą:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Powinien zostać wyświetlony jedno urządzenie w wynikach dotyczące zadawania zapytań dla wszystkich urządzeń znajdujących się w **Redmond43** a dla zapytania, które ogranicza wyniki do urządzenia korzystające z sieci komórkowej.
   
    ![Pierwsze zapytanie][1]

W następnej sekcji utworzysz aplikację urządzenie, raportuje informacje o łączności, która zmienia się wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia
W tej sekcji opisano tworzenie aplikacji konsoli języka Python, który nawiązuje połączenie z Centrum jako swojej **{identyfikator urządzenia}** i następnie aktualizacje jego bliźniaczej reprezentacji urządzenia użytkownika zgłoszonych właściwości zawierają informacje, że jest ona połączona korzystania z sieci komórkowej.

1. Otwórz wiersz polecenia i zainstaluj **zestaw SDK usługi Azure IoT Hub dla języka Python**. Zamknij wiersz polecenia po zainstalowaniu zestawu SDK.

    ```
    pip install azure-iothub-device-client
    ```

1. Za pomocą edytora tekstu Utwórz nowy **ReportConnectivity.py** pliku.

3. Dodaj następujący kod umożliwiający zaimportowanie wymaganych modułów z zestawu SDK usługi:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. Dodaj następujący kod, zastępując symbol zastępczy `[IoTHub Device Connection String]` przy użyciu parametrów połączenia dla urządzenia Centrum IoT utworzonego w poprzedniej sekcji.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. Dodaj następujący kod do **ReportConnectivity.py** twins plików do wdrożenia na urządzeniu funkcji:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```   

    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z bliźniaczych reprezentacji urządzeń z urządzenia. Powyższy kod po inicjuje **klienta** obiektu, pobiera bliźniaczą reprezentację urządzenia dla danego urządzenia i jego zgłaszanych właściwości zostaje zaktualizowana o informacje o łączności.

1. Dodaj następujący kod na końcu **ReportConnectivity.py** do zaimplementowania **iothub_client_sample_run** funkcji:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. Uruchamianie aplikacji urządzenia
   
    ```cmd/sh
    python ReportConnectivity.py
    ```
   
    Powinien zostać wyświetlony potwierdzenia, które zostały zaktualizowane bliźniaczych reprezentacji urządzeń.

    ![Aktualizuj bliźniaczych reprezentacji][2]

6. Teraz, gdy urządzenie jest zgłaszane jego informacje o łączności, powinien pojawić się w obu zapytań. Przejdź wstecz i ponownie uruchom zapytania:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Tym razem z **{identyfikator urządzenia}** powinno pojawić się w obu wyników zapytania.
   
    ![drugie zapytanie][3]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodane metadane urządzenia jako tagi z aplikacji zaplecza, a aplikacja powstała z jednego urządzenia symulowanego do raportu informacje o łączności urządzenia w bliźniaku urządzenia. Przedstawiono również sposób wykonywania zapytań te informacje za pomocą rejestru.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzeń przy użyciu [Rozpoczynanie pracy z usługą IoT Hub] [ lnk-iothub-getstarted] samouczka
* Konfigurowanie urządzeń przy użyciu żądane właściwości bliźniaczej reprezentacji urządzenia za pomocą [Użyj żądane właściwości, aby skonfigurować urządzenia] [ lnk-twin-how-to-configure] samouczka
* Sterowanie urządzeniami interaktywnie (na przykład włączając wentylator z aplikacji kontrolowanej przez użytkownika), za pomocą [używanie metod bezpośrednich] [ lnk-methods-tutorial] samouczka.

<!-- images -->
[1]: media/iot-hub-python-twin-getstarted/1.png
[2]: media/iot-hub-python-twin-getstarted/2.png
[3]: media/iot-hub-python-twin-getstarted/3.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
