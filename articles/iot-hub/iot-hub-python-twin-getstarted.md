---
title: Rozpoczynanie pracy z Centrum IoT Azure urządzenia twins (Python) | Dokumentacja firmy Microsoft
description: Jak używać twins urządzenia Azure IoT Hub Dodawanie tagów, a następnie użyć kwerendy Centrum IoT. Zestawy SDK IoT Azure dla języka Python umożliwia wdrożenie symulowane urządzenie aplikacji i usługi aplikacji, która dodaje znaczniki i uruchamia kwerendy Centrum IoT.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 314c88e4-cce1-441c-b75a-d2e08e39ae7d
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2017
ms.author: v-masebo;kgremban
ms.openlocfilehash: d5b606532ac445b03527c5027dc3b434d88b6b1f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-device-twins-python"></a>Rozpoczynanie pracy z urządzenia twins (Python)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka masz dwie aplikacje konsoli Python:

* **AddTagsAndQuery.py**, aplikacji wewnętrznych Python, która dodaje znaczniki i zapytanie twins urządzenia.
* **ReportConnectivity.py**, aplikacji Python, która symuluje urządzenie, które łączy do Centrum IoT z tożsamości urządzenia utworzony wcześniej, a następnie raportuje stanu łączności.

> [!NOTE]
> Artykuł [Azure IoT SDK] [ lnk-hub-sdks] informacje na temat zestawów SDK IoT Azure można tworzyć aplikacje zarówno urządzenia, jak i zaplecza.
> 
> 

Do ukończenia tego samouczka należy spełnić następujące warunki:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

> [!NOTE]
> Pakiety *pip* dla składników `azure-iothub-service-client` i `azure-iothub-device-client` są obecnie dostępne tylko w przypadku systemu operacyjnego Windows. W przypadku systemu operacyjnego Linux lub Mac OS zapoznaj się z sekcją poświęconą danemu systemowi w poście [Prepare your development environment for Python][lnk-python-devbox] (Przygotowanie środowiska projektowego dla języka Python).
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi
W tej sekcji, Utwórz aplikację konsoli języka Python, która dodaje dwie urządzeń skojarzonych z lokalizacji metadanych z **{identyfikator urządzenia}**. Tworzy następnie kwerendę twins urządzenia przechowywane w Centrum IoT Wybieranie urządzeń znajdujących się w Redmond, a następnie te, które są raportowania komórkowej połączenia.

1. Otwórz wiersz polecenia i zainstaluj **zestaw SDK usługi Azure IoT Hub dla języka Python**. Zamknij wiersz polecenia po zainstalowaniu zestawu SDK.

    ```
    pip install azure-iothub-service-client
    ```

1. Za pomocą edytora tekstu, Utwórz nową **AddTagsAndQuery.py** pliku.

3. Dodaj następujący kod umożliwiający zaimportowanie wymaganych modułów z zestawu SDK usługi:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubError
    ```
2. Dodaj następujący kod, zastępując symbolu zastępczego dla `[IoTHub Connection String]` i `[Device Id]` ciągu połączenia dla Centrum IoT i identyfikator urządzenia utworzone w poprzednich sekcjach.
   
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
   
    **Rejestru** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z usługi. Kod najpierw inicjuje **rejestru** obiektu, a następnie aktualizacje dwie urządzenia dla **deviceId**, a na koniec uruchamia dwa zapytania. Pierwszy wybiera tylko twins urządzenia urządzeń znajdujących się w **Redmond43** zakładu, a drugi udoskonalanie zapytanie, aby wybrać tylko te urządzenia, które także są połączone za pośrednictwem sieci komórkowej.
   
1. Dodaj następujący kod na końcu **AddTagsAndQuery.py** do zaimplementowania **iothub_service_sample_run** funkcji:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

1. Uruchom aplikację klawiszem:
   
    ```cmd/sh
    python AddTagsAndQuery.py
    ```
   
    Jedno urządzenie w wynikach zadać kwerendy powinna być widoczna dla wszystkich urządzeń znajdujących się w **Redmond43** i Brak dla zapytania, który ogranicza wyniki do urządzenia, które korzystają z sieci komórkowej.
   
    ![pierwszego zapytania][1]

W następnej sekcji utworzysz aplikację urządzenia, która raportuje informacje dotyczące łączności i zmienia się wynik kwerendy w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzeń
W tej sekcji utworzysz aplikację konsoli języka Python łączący się do Centrum jako sieci **{identyfikator urządzenia}**, a następnie aktualizacje jego dwie urządzenia użytkownika zgłosiła właściwości zawierają informacje, że jest połączony za pomocą sieci komórkowej.

1. Otwórz wiersz polecenia i zainstaluj **zestaw SDK usługi Azure IoT Hub dla języka Python**. Zamknij wiersz polecenia po zainstalowaniu zestawu SDK.

    ```
    pip install azure-iothub-device-client
    ```

1. Za pomocą edytora tekstu, Utwórz nową **ReportConnectivity.py** pliku.

3. Dodaj następujący kod umożliwiający zaimportowanie wymaganych modułów z zestawu SDK usługi:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

2. Dodaj następujący kod, zastępując symbolu zastępczego dla `[IoTHub Device Connection String]` ciągu połączenia dla urządzenia Centrum IoT utworzone w poprzednich sekcjach.
   
    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

1. Dodaj następujący kod do **ReportConnectivity.py** pliku implementacji urządzenia twins funkcji:

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

    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z urządzenia. Poprzedni kod po jego inicjuje **klienta** obiektu, pobiera dwie urządzenia dla danego urządzenia i aktualizuje jego właściwość zgłoszone informacje o łączności.

1. Dodaj następujący kod na końcu **ReportConnectivity.py** do zaimplementowania **iothub_client_sample_run** funkcji:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

1. Uruchamianie aplikacji urządzeń
   
    ```cmd/sh
    python ReportConnectivity.js
    ```
   
    Powinny pojawić się potwierdzenie twins urządzenia zostały zaktualizowane.

    ![twins aktualizacji][2]

6. Teraz, gdy urządzenie zgłosiło jego informacje dotyczące łączności, powinna pojawić się w obu zapytania. Przejdź wstecz i ponownie uruchom zapytania:
   
    ```cmd/sh
    python AddTagsAndQuery.js
    ```
   
    Tym razem z **{identyfikator urządzenia}** powinny być wyświetlane w obu wyników zapytania.
   
    ![drugiego zapytania][3]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Dodaje metadane urządzenia jako tagi z aplikacji zaplecza i zapisano aplikacji symulowane urządzenie informacji w raporcie urządzenia łączności w dwie urządzenia. Przedstawiono również sposób kwerendy te informacje za pomocą rejestru.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzenia z [Rozpoczynanie pracy z Centrum IoT] [ lnk-iothub-getstarted] samouczka
* Konfigurowanie urządzeń przy użyciu właściwości żądaną dwie urządzenia z [Użyj żądanego właściwości, aby skonfigurować urządzenia] [ lnk-twin-how-to-configure] samouczka
* urządzenia interakcyjne (takich jak włączanie wentylator z aplikacji kontrolowane przez użytkownika) i sterować za pomocą [metody bezpośredniego] [ lnk-methods-tutorial] samouczka.

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
