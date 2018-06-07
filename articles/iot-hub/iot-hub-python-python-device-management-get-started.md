---
title: Wprowadzenie do zarządzania urządzeniami w usłudze Azure IoT Hub (Python) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami Centrum IoT można zainicjować ponownego uruchomienia urządzenia zdalnego. Przy użyciu zestawu Azure IoT SDK dla języka Python aplikacji symulowane urządzenie, która zawiera metodę bezpośredniego i aplikacji usługi, która wywołuje metodę bezpośredniego.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: kgremban
ms.openlocfilehash: fa966ee2ea26cccc7d841a0e969d8329ac5bc0de
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635291"
---
# <a name="get-started-with-device-management-python"></a>Wprowadzenie do zarządzania urządzeniami (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Użyj portalu Azure do tworzenia Centrum IoT i tworzenia tożsamości urządzenia w Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie zawierającej bezpośredniego metodę, która wykonuje ponowny rozruch tego urządzenia. Bezpośrednie metody są wywoływane z chmury.
* Utwórz aplikację konsoli języka Python, który wywołuje metodę bezpośredniego ponowny rozruch w aplikacji symulowane urządzenie za pomocą Centrum IoT.

Na końcu tego samouczka znajdują się dwie aplikacje konsoli Python:

**dmpatterns_getstarted_device.PY**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje metoda bezpośrednia ponowny rozruch, symuluje ponowny rozruch komputera fizycznego i raporty czasu ostatniego ponownego uruchomienia.

**dmpatterns_getstarted_service.PY**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie Wyświetla odpowiedzi i wyświetla zaktualizowane zgłoszone właściwości.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
    * Zainstaluj [urządzenia klienta, azure Centrum iothub w-](https://pypi.org/project/azure-iothub-device-client/) pakietu przy użyciu polecenia   `pip install azure-iothub-device-client`
    * Zainstaluj [usługi klienta, azure Centrum iothub w-](https://pypi.org/project/azure-iothub-service-client/) pakietu przy użyciu polecenia   `pip install azure-iothub-service-client`
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji obejmują:

* Utwórz aplikację konsoli języka Python, które odpowiada metoda bezpośrednia wywoływane przez chmury
* Symulowanie ponowne uruchomienie urządzenia
* Włącz zapytania dwie urządzenia w celu identyfikowania urządzeń przy użyciu właściwości zgłoszone, a podczas ostatniego ponownego uruchomienia

1. Za pomocą edytora tekstu, Utwórz **dmpatterns_getstarted_device.py** pliku.

1. Dodaj następujące `import` instrukcje na początku **dmpatterns_getstarted_device.py** pliku.
   
    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

1. Dodawanie zmiennych, łącznie z **ciągu łączącym** zmienną i inicjowania klienta.  Parametry połączenia należy zastąpić ciąg połączenia urządzenia.  
   
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

1. Dodaj następujące wywołania zwrotne funkcji do zaimplementowania metoda bezpośrednia na urządzeniu.
   
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

1. Uruchomić odbiornik metoda bezpośrednia, a następnie zaczekaj.
   
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

1. Zapisz i Zamknij **dmpatterns_getstarted_device.py** pliku.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Wyzwalacz zdalnego ponownego uruchomienia na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji utworzysz aplikację konsoli języka Python, która inicjuje zdalnego ponownego uruchomienia na urządzeniu przy użyciu metody bezpośredniego. Aplikacja używa urządzenia dwie zapytania do odnajdywania ostatniego ponownego uruchomienia dla tego urządzenia.

1. Za pomocą edytora tekstu, Utwórz **dmpatterns_getstarted_service.py** pliku.

1. Dodaj następujące `import` instrukcje na początku **dmpatterns_getstarted_service.py** pliku.
   
    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

1. Dodaj następujące deklaracje zmiennej. Tylko Zastąp symbole zastępcze dla _IoTHubConnectionString_ i _deviceId_.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Dodaj następującą funkcję można wywołać metody urządzenie do ponownego uruchamiania urządzenia docelowego, a następnie wyszukać twins urządzenie i uzyskać czasu ostatniego ponownego uruchomienia.
   
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

1. Zapisz i Zamknij **dmpatterns_getstarted_service.py** pliku.


## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.
   
    ```
    python dmpatterns_getstarted_device.py
    ```

1. Innego wiersza polecenia Uruchom następujące polecenie, aby zdalne ponowne uruchamianie systemu i kwerendę dla dwie urządzenia znaleźć ostatniego ponownego rozruchu czasu.
   
    ```
    python dmpatterns_getstarted_service.py
    ```

1. Zobaczysz odpowiedź urządzenia do metody bezpośrednio w konsoli.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
