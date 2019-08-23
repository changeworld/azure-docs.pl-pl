---
title: Rozpoczynanie pracy z usługą Azure IoT Hub Device Management (Python) | Microsoft Docs
description: Jak za pomocą IoT Hub zarządzanie urządzeniami zainicjować zdalne ponowne uruchomienie urządzenia. Zestaw SDK usługi Azure IoT dla języka Python służy do implementowania aplikacji symulowanego urządzenia, która obejmuje metodę bezpośrednią i aplikację usługi, która wywołuje metodę bezpośrednią.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 287dbd3d6da4aa2bf5bd1da652cdeaeda3136321
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907727"
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

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Poniżej przedstawiono instrukcje instalacji dotyczące wymagań wstępnych.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsolowej w języku Python, która reaguje na bezpośrednią metodę wywoływaną przez chmurę

* Symulowanie ponownego uruchamiania urządzenia

* Użyj raportowanych właściwości, aby włączyć do identyfikowania urządzeń i podczas ostatniego ponownego uruchomienia zapytania o sznury

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > Pakiety PIP dla usług Azure-iothub-Service-Client i Azure-iothub-Device-Client są obecnie dostępne tylko dla systemu operacyjnego Windows. W przypadku systemu Linux/Mac OS zapoznaj się z sekcjami dotyczącymi systemu Linux i Mac OS w sekcji [przygotowanie środowiska programistycznego dla języka Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Za pomocą edytora tekstów Utwórz plik o nazwie **dmpatterns_getstarted_device. PR** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje na początku pliku **dmpatterns_getstarted_device. PR** .

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

4. Dodaj zmienne, w tym zmienną **CONNECTION_STRING** i inicjalizację klienta.  Zastąp `{deviceConnectionString}` wartość symbolu zastępczego parametrami połączenia urządzenia. Te parametry połączenia zostały wcześniej skopiowane przez [zarejestrowanie nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).  

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

5. Dodaj następujące wywołania zwrotne funkcji w celu zaimplementowania metody bezpośredniej na urządzeniu.

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

6. Uruchom odbiornik metody Direct i poczekaj.

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

7. Zapisz i zamknij plik **dmpatterns_getstarted_device. PR** .

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalanie zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniej

W tej sekcji utworzysz aplikację konsolową języka Python, która inicjuje zdalne ponowne uruchomienie na urządzeniu przy użyciu metody bezpośredniej. Aplikacja używa zapytań o bliźniaczych urządzeniach do wykrywania czasu ostatniego rozruchu dla tego urządzenia.

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Service-Client** :

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Pakiety PIP dla usług Azure-iothub-Service-Client i Azure-iothub-Device-Client są obecnie dostępne tylko dla systemu operacyjnego Windows. W przypadku systemu Linux/Mac OS zapoznaj się z sekcjami dotyczącymi systemu Linux i Mac OS w sekcji [przygotowanie środowiska programistycznego dla języka Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Za pomocą edytora tekstów Utwórz plik o nazwie **dmpatterns_getstarted_service. PR** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje na początku pliku **dmpatterns_getstarted_service. PR** .

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. Dodaj następujące deklaracje zmiennych. Zastąp wartość [](#get-the-iot-hub-connection-string) symboluzastępczegoparametramipołączeniausługiIoTHubskopiowanymiwcześniejwpoluPobierzparametrypołączeniausługiIoT`{IoTHubConnectionString}` Hub. Zastąp wartość [](#register-a-new-device-in-the-iot-hub) symboluzastępczegoidentyfikatoremurządzeniazarejestrowanymwZarejestrujnoweurządzeniewusłudzeIoT`{deviceId}` Hub.

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Dodaj następującą funkcję, aby wywołać metodę urządzenia w celu ponownego uruchomienia urządzenia docelowego, a następnie wyszukać bliźniaczych reprezentacji urządzenia i uzyskać czas ostatniego ponownego uruchomienia.

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

6. Zapisz i zamknij plik **dmpatterns_getstarted_service. PR** .

## <a name="run-the-apps"></a>Uruchamianie aplikacji

Teraz można przystąpić do uruchamiania aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniego ponownego uruchomienia.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. W innym wierszu polecenia Uruchom następujące polecenie, aby wyzwolić zdalne ponowne uruchomienie i zapytanie dla sznurka urządzenia, aby znaleźć czas ostatniego ponownego uruchomienia.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Odpowiedź urządzenia na metodę bezpośrednią w konsoli programu.

   Poniżej przedstawiono odpowiedzi urządzenia na metodę bezpośredniego ponownego uruchomienia:

   ![Dane wyjściowe aplikacji symulowanego urządzenia](./media/iot-hub-python-python-device-management-get-started/device.png)

   Poniżej przedstawiono usługę wywołującą metodę bezpośredniego ponownego uruchomienia i sondowanie sznurka urządzenia pod kątem stanu:

   ![Wyzwalanie danych wyjściowych usługi ponownego uruchomienia](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]