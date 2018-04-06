---
title: Użyj właściwości dwie urządzenia Azure IoT Hub (Python) | Dokumentacja firmy Microsoft
description: Jak używać do konfigurowania urządzeń twins urządzenia Azure IoT Hub. Przy użyciu zestawów SDK IoT Azure dla języka Python aplikacji symulowane urządzenie i aplikacji usługi, który modyfikuje konfigurację urządzenia przy użyciu podwójnego urządzenia.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: d0bcec50-26e6-40f0-8096-733b2f3071ec
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: e6f4aa61e37769dc4851f8284d07a7991ee38c91
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-desired-properties-to-configure-devices-python"></a>Żądane właściwości umożliwiają konfigurowanie urządzeń (Python)
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Na końcu tego samouczka masz dwie aplikacje konsoli Python:

* **SimulateDeviceConfiguration.py**, aplikację symulowane urządzenie, która oczekuje na aktualizację wymaganą konfiguracją i raportowanie stanu procesu aktualizacji konfiguracji symulowane.
* **SetDesiredConfigurationAndQuery.py**, aplikacji wewnętrznych Python, która konfiguruje wymaganą konfiguracją na urządzeniu i zapytanie proces aktualizacji konfiguracji.

> [!NOTE]
> Artykuł [Azure IoT SDK] [ lnk-hub-sdks] informacje na temat zestawów SDK IoT Azure można tworzyć aplikacje zarówno urządzenia, jak i zaplecza.
> 
> 

Do ukończenia tego samouczka należy spełnić następujące warunki:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Po wykonaniu [Rozpoczynanie pracy z urządzenia twins] [ lnk-twin-tutorial] samouczek, masz już Centrum IoT i tożsamość urządzenia o nazwie **myDeviceId**; i można przejść do [tworzenie aplikacji symulowane urządzenie] [ lnk-how-to-configure-createapp] sekcji.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-simulated-device-app"></a>Tworzenie aplikacji symulowane urządzenie
W tej sekcji utworzysz aplikację konsoli języka Python łączący się do Centrum jako **myDeviceId**, czeka na aktualizację wymaganą konfiguracją, a następnie przedstawia aktualizacje na proces aktualizacji konfiguracji symulowane.

1. Zainstaluj **zestawu SDK usługi Azure IoT Python urządzenia** z wiersza polecenia przy użyciu następującego polecenia:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Za pomocą edytora tekstu, Utwórz nową **SimulateDeviceConfiguration.py** pliku.

1. Dodaj następujący kod do **SimulateDeviceConfiguration.py** plików i Zastąp **{ciąg połączenia urządzenia}** symbol zastępczy w ciągu połączenia urządzenia został skopiowany podczas tworzenia **myDeviceId** tożsamości urządzenia:

    ```python   
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0

    TWIN_CALLBACKS = 0
    SEND_REPORTED_STATE_CALLBACKS = 0

    CONFIG_ID = "1"
    TWIN_PAYLOAD = "{\"configId\":" + CONFIG_ID + ",\"sendFrequency\":\"24h\"}"
    ```

    **Klienta** obiekt udostępnia wszystkie metody, które są wymagane do interakcji z twins urządzenia z urządzenia. W dalszych kodu następnie dołączą program obsługi aktualizacji na odpowiednich właściwościach Dodaj dodatkowe obsługi do Sprawdź, czy porównując configIds, który następnie wywołuje metodę, która rozpoczyna się zmian w konfiguracji jest rzeczywista konfiguracja żądania zmiany . Dla uproszczenia poprzedni kod używa domyślnego ustalony ukończenie początkowej konfiguracji. Rzeczywiste aplikacji będzie prawdopodobnie załadować tej konfiguracji z magazynu lokalnego.
   
   > [!IMPORTANT]
   > Zdarzenia zmiany żądanej właściwości zawsze są emitowane raz na połączenie z urządzeniem, upewnij się sprawdzić, czy jest rzeczywistą zmianę w odpowiednich właściwościach przed wykonaniem jakiegokolwiek działania.
   > 

1. Dodaj następujący kod na końcu **SimulateDeviceConfiguration.py** pliku:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        global TWIN_CALLBACKS
        global CONFIG_ID

        desired_configId = payload[payload.find("configId")+11:payload.find("configId")+12]
        if CONFIG_ID != desired_configId:
            print ( "" )
            print ( "Reported pending config change: %s" % payload)
        
            desired_sendFrequency = payload[payload.find("sendFrequency")+17:payload.find("sendFrequency")+19]
            print ( "...desired configId: " + desired_configId)
            print ( "...desired sendFrequency: " + desired_sendFrequency)
            new_payload = "{\"configId\":" + desired_configId + ",\"sendFrequency\":\"" + desired_sendFrequency + "\"}"
            CLIENT.send_reported_state(new_payload, len(new_payload), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        
        CONFIG_ID = desired_configId
    
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS
    
        print ( "" )
        print ( "Device twins updated." )
    ```
   
    **Device_twin_callback** metody aktualizacji zgłosił żądania i ustawia właściwości w obiekcie dwie urządzenia lokalnego przy użyciu konfiguracji aktualizacji _configId_. Po pomyślnym zaktualizowaniu urządzenia dwie, wyświetla komunikat o aktualizacji. Aby oszczędzić przepustowość, zgłoszone właściwości są aktualizowane, określając właściwości tylko do zmodyfikowania (o nazwie **TWIN_PAYLOAD** w powyższym kodzie), a nie zastępuje całego dokumentu.
   
   > [!NOTE]
   > W tym samouczku nie zasymulować wszystkie zachowania aktualizacji konfiguracji współbieżnych. Niektóre procesy aktualizacji konfiguracji może mieć możliwość uwzględnienia zmian konfiguracji docelowej aktualizacji jest uruchomiony, gdy inne osoby mogły je z kolejki i innych użytkowników można odrzucić warunek błędu. Upewnij się, że należy wziąć pod uwagę zachowanie procesu określonej konfiguracji, a następnie dodaj odpowiednie logikę przed zainicjowaniem zmian w konfiguracji.
   > 

1. Dodaj następujący kod na końcu **SimulateDeviceConfiguration.py** pliku: 

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()
        
            CLIENT.send_reported_state(TWIN_PAYLOAD, len(TWIN_PAYLOAD), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Uruchamianie aplikacji urządzenia:
   
    ```cmd/sh
    node SimulateDeviceConfiguration.js
    ```
   
    Powinien zostać wyświetlony komunikat `Device twins updated.`. Nie zatrzymuj aplikacji.


## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi
W tej sekcji utworzysz aplikację konsoli języka Python, która aktualizuje *żądanego właściwości* na dwie urządzeń skojarzonych z **myDeviceId** z obiektem konfiguracji telemetrii. Następnie odpytuje twins urządzenia przechowywane w Centrum IoT i pokazano różnicę między konfiguracji żądanego i zgłoszonego urządzenia.

1. Zainstaluj **Azure IoT Python usługi SDK** z wiersza polecenia przy użyciu następującego polecenia:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Za pomocą edytora tekstu, Utwórz nową **SetDesiredAndQuery.py** pliku.

1. Dodaj następujący kod do **SetDesiredAndQuery.py** plików i Zastąp **{IoTHubConnectionString}** symbol zastępczy parametrów połączenia Centrum IoT skopiowane podczas tworzenia Centrum i **{deviceId}** symbolu zastępczego o nazwie urządzenia:

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubError, IoTHubDeviceTwin

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"configId\":3, \"sendFrequency\":\"" + sys.argv[1:][0] + "\"}}}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Dodaj następujący kod na końcu **SetDesiredAndQuery.py** pliku:

    ```python
    def iothub_devicetwin_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
        
            twin_info = iothub_twin_method.get_twin(DEVICE_ID)
            print ( "" )
            print ( "Device Twin before update    :" )
            print ( "{0}".format(twin_info) )

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
            print ( "" )
            print ( "Device Twin after update     :" )
            print ( "{0}".format(twin_info) )
        
            while True:
                time.sleep(10)

                twin_info = iothub_twin_method.get_twin(DEVICE_ID)
                print ( "" )
                print ( "Device Twin after client change initiated    :" )
                print ( "{0}".format(twin_info) )
        
                print ( "" )
                print ( "IoT Hub service sample complete, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(30)
                
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceTwins Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicetwin_sample_run()
    ```

1. Z **SimulateDeviceConfiguration.py** uruchomiona, uruchom aplikację w wiersz polecenia z:

    ```cmd/sh
    python SetDesiredAndQuery.py 5m
    ```
   
    Powinien zostać wyświetlony identyfikator konfiguracji zgłoszone zmiany z **1** do **2** za pomocą nowego aktywny wysyłać częstotliwość pięć minut zamiast 24 godziny.


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku, ustaw żądaną konfiguracją jako *żądanego właściwości* z poziomu aplikacji zaplecza i zapisano aplikacji symulowane urządzenie, aby wykryć zmiana i symulowanie procesu aktualizacji raportowania stanu jako *zgłosił właściwości* na dwie urządzenia.

Użyj następujących zasobów, aby dowiedzieć się, jak:

* wysyłanie danych telemetrycznych z urządzenia z [Rozpoczynanie pracy z Centrum IoT] [ lnk-iothub-getstarted] samouczka
* Zaplanuj lub wykonywania operacji na dużych zestawów urządzeń, zobacz [emisji zadania i harmonogramu] [ lnk-schedule-jobs] samouczka.
* urządzenia interakcyjne (takich jak włączanie wentylator z aplikacji kontrolowane przez użytkownika) i sterować za pomocą [metody bezpośredniego] [ lnk-methods-tutorial] samouczka.

<!-- links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-python-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-iothub-getstarted]: iot-hub-python-getstarted.md
[lnk-methods-tutorial]: iot-hub-python-python-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
