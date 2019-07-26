---
title: Rozpoczynanie pracy z usługą Azure IoT Hub Device Management (Python) | Microsoft Docs
description: Jak za pomocą IoT Hub zarządzanie urządzeniami zainicjować zdalne ponowne uruchomienie urządzenia. Zestaw SDK usługi Azure IoT dla języka Python służy do implementowania aplikacji symulowanego urządzenia, która obejmuje metodę bezpośrednią i aplikację usługi, która wywołuje metodę bezpośrednią.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: c4c8957e8d9b355216e10503d58915977c3b9b1a
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403407"
---
# <a name="get-started-with-device-management-python"></a>Wprowadzenie do zarządzania urządzeniami (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj Azure Portal, aby utworzyć IoT Hub i utworzyć tożsamość urządzenia w centrum IoT.

* Utwórz aplikację symulowanego urządzenia, która zawiera metodę bezpośrednią, która uruchamia ponownie to urządzenie. Metody bezpośrednie są wywoływane z chmury.

* Utwórz aplikację konsolową w języku Python, która wywołuje metodę bezpośredniego ponownego uruchomienia w aplikacji symulowanego urządzenia za pośrednictwem Centrum IoT.

Na końcu tego samouczka masz dwie aplikacje konsolowe języka Python:

* **dmpatterns_getstarted_device. PR**, który łączy się z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia, odbiera metodę bezpośredniego ponownego uruchomienia, symuluje rozruch fizyczny i raportuje godzinę ostatniego ponownego uruchomienia.

* **dmpatterns_getstarted_service. PR**, który wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia, wyświetla odpowiedź i wyświetla zaktualizowane raportowane właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Python 2. x lub 3. x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*](https://pip.pypa.io/en/stable/installing/).

* Zainstaluj pakiet [Azure-iothub-Device-Client](https://pypi.org/project/azure-iothub-device-client/) przy użyciu polecenia`pip install azure-iothub-device-client`

* Zainstaluj pakiet [Azure-iothub-Service-Client](https://pypi.org/project/azure-iothub-service-client/) , używając polecenia`pip install azure-iothub-service-client`

* Zainstaluj [Pakiet redystrybucyjny języka Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji znajdziesz następujące:

* Tworzenie aplikacji konsolowej w języku Python, która reaguje na bezpośrednią metodę wywoływaną przez chmurę

* Symulowanie ponownego uruchamiania urządzenia

* Użyj raportowanych właściwości, aby włączyć do identyfikowania urządzeń i podczas ostatniego ponownego uruchomienia zapytania o sznury

1. Za pomocą edytora tekstów Utwórz plik **dmpatterns_getstarted_device. PR** .

2. Dodaj następujące `import` instrukcje na początku pliku **dmpatterns_getstarted_device. PR** .

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

3. Dodaj zmienne, w tym zmienną **CONNECTION_STRING** i inicjalizację klienta.  Zamień parametry połączenia na parametry połączenia urządzenia.  

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

4. Dodaj następujące wywołania zwrotne funkcji w celu zaimplementowania metody bezpośredniej na urządzeniu.

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

5. Uruchom odbiornik metody Direct i poczekaj.

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

6. Zapisz i zamknij plik **dmpatterns_getstarted_device. PR** .

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsolową języka Python, która inicjuje zdalne ponowne uruchomienie na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań o bliźniaczych urządzeniach do wykrywania czasu ostatniego rozruchu dla tego urządzenia.

1. Za pomocą edytora tekstów Utwórz plik **dmpatterns_getstarted_service. PR** .

2. Dodaj następujące `import` instrukcje na początku pliku **dmpatterns_getstarted_service. PR** .

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

3. Dodaj następujące deklaracje zmiennych. Zastąp tylko wartości zastępcze dla _IoTHubConnectionString_ i _deviceId_.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

4. Dodaj następującą funkcję, aby wywołać metodę urządzenia w celu ponownego uruchomienia urządzenia docelowego, a następnie wyszukać bliźniaczych reprezentacji urządzenia i uzyskać czas ostatniego ponownego uruchomienia.

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

5. Zapisz i zamknij plik **dmpatterns_getstarted_service. PR** .

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniego ponownego uruchomienia.

    ```
    python dmpatterns_getstarted_device.py
    ```

2. W innym wierszu polecenia Uruchom następujące polecenie, aby wyzwolić zdalne ponowne uruchomienie i zapytanie dla sznurka urządzenia, aby znaleźć czas ostatniego ponownego uruchomienia.

    ```
    python dmpatterns_getstarted_service.py
    ```

3. Odpowiedź urządzenia na metodę bezpośrednią w konsoli programu.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]