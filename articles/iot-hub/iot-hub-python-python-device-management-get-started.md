---
title: Wprowadzenie do zarządzania urządzeniami Azure IoT Hub (Python) | Dokumentacja firmy Microsoft
description: Jak zainicjować ponownego uruchomienia urządzenia zdalnego za pomocą zarządzania urządzeniami usługi IoT Hub. Przy użyciu zestawu SDK usługi Azure IoT dla języka Python aplikacji symulowanego urządzenia, która obejmuje metody bezpośredniej i app service, która wywołuje metody bezpośredniej.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 04fc1da04d9da715acfed8ca9d26e9c325afb403
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59279928"
---
# <a name="get-started-with-device-management-python"></a>Wprowadzenie do zarządzania urządzeniami (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj witryny Azure portal do utworzenia Centrum IoT, a następnie tworzenie tożsamości urządzenia w usłudze IoT hub.

* Tworzenie aplikacji symulowanego urządzenia, która zawiera bezpośrednie metodę, która wywołuje ponowne uruchomienie tego urządzenia. Metody bezpośrednie są wywoływane z poziomu chmury.

* Tworzenie aplikacji konsoli języka Python, który wywołuje metody bezpośredniej ponowny rozruch w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.

Na końcu tego samouczka masz dwie aplikacje konsolowe środowiska Python:

* **dmpatterns_getstarted_device.PY**, łączy się z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia otrzymuje metody bezpośredniej o ponowne uruchomienie komputera, symuluje ponowne uruchomienie komputera fizycznego, a następnie raportuje czas ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service.PY**, która wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia, wyświetla odpowiedzi i wyświetla zaktualizowany zgłoszonych właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Python 2.x lub 3.x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*](https://pip.pypa.io/en/stable/installing/).

* Zainstaluj [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) pakietów, za pomocą polecenia       `pip install azure-iothub-device-client`

* Zainstaluj [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) pakietów, za pomocą polecenia       `pip install azure-iothub-service-client`

* Zainstaluj [Pakiet redystrybucyjny języka Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji wykonasz następujące czynności:

* Tworzenie aplikacji konsoli języka Python, która reaguje na metodę bezpośrednią wywołaną przez chmurę

* Zasymulować ponowne uruchomienie urządzenia

* Włączanie zapytań bliźniaczych reprezentacji urządzeń do identyfikowania urządzeń za pomocą zgłoszonych właściwości, a podczas ostatniego ponownego uruchomienia

1. Za pomocą edytora tekstów Utwórz **dmpatterns_getstarted_device.py** pliku.

2. Dodaj następujący kod `import` instrukcji na początku **dmpatterns_getstarted_device.py** pliku.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. Dodaj zmienne, w tym **parametry_połączenia** zmienną i inicjowania klienta.  Zastąp parametry połączenia parametrami połączenia urządzenia.  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

4. Dodaj następujące wywołania zwrotne funkcja implementacji metody bezpośredniej na urządzeniu.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

5. Uruchomić odbiornika metody bezpośredniej, a następnie zaczekaj.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

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

6. Zapisz i Zamknij **dmpatterns_getstarted_device.py** pliku.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Zdalne ponowne uruchamianie systemu na urządzeniu, korzystając z metody bezpośredniej wyzwalacza

W tej sekcji utworzysz aplikację konsoli języka Python, która inicjuje ponowne uruchomienie komputera zdalnego na urządzeniu przy użyciu metody bezpośredniej o. Aplikacja używa zapytań bliźniaczych reprezentacji urządzeń, aby odnaleźć ostatniego ponownego uruchomienia dla tego urządzenia.

1. Za pomocą edytora tekstów Utwórz **dmpatterns_getstarted_service.py** pliku.

2. Dodaj następujący kod `import` instrukcji na początku **dmpatterns_getstarted_service.py** pliku.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. Dodaj następujące deklaracje zmiennych. Tylko Zastąp wartości symboli zastępczych _IoTHubConnectionString_ i _deviceId_.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. Dodaj następującą funkcję, można wywołać metody urządzenia, aby ponownie uruchomić urządzenie docelowe, a następnie wysłać zapytanie o bliźniaki urządzeń i Uzyskaj czas ostatniego ponownego uruchomienia.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

5. Zapisz i Zamknij **dmpatterns_getstarted_service.py** pliku.

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponowny rozruch.

    ```
    python dmpatterns_getstarted_device.py
    ```

2. W wierszu innego polecenia Uruchom następujące polecenie, można wyzwolić ponowne uruchomienie zdalnego i wykonywania zapytań o bliźniaczej reprezentacji urządzenia można znaleźć w ciągu ostatnich ponowny rozruch czasu.

    ```
    python dmpatterns_getstarted_service.py
    ```

3. Zostanie wyświetlona odpowiedź urządzenia, do metody bezpośredniej w konsoli.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]