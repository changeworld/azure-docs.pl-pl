---
title: Centrum IoT Azure bezpośrednie metod (Python) | Dokumentacja firmy Microsoft
description: Jak używać bezpośrednich metod Centrum IoT Azure. Przy użyciu zestawów SDK IoT Azure dla języka Python aplikacji symulowane urządzenie, która zawiera metodę bezpośredniego i aplikacji usługi, która wywołuje metodę bezpośredniego.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: ea9c73ca-7778-4e38-a8f1-0bee9d142f04
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 973dba8682e50af0434c557aa088d15e7a004b45
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-direct-methods-on-your-iot-device-with-python"></a>Użyj metody bezpośrednio na urządzeniu IoT z języka Python
[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Na końcu tego samouczka znajdują się dwie aplikacje konsoli Python:

* **CallMethodOnDevice.py**, która wywołuje metodę w aplikacji symulowane urządzenie i wyświetla odpowiedzi.
* **SimulatedDevice.py**, który łączy Centrum IoT z tożsamości urządzenia utworzony wcześniej, a odpowiada metodzie wywołanej przez chmurę.

> [!NOTE]
> Artykuł [Azure IoT SDKs][lnk-hub-sdks] (Zestawy SDK usług Azure IoT) zawiera informacje dotyczące zestawów SDK usług Azure IoT, przy użyciu których można tworzyć aplikacje zarówno do uruchamiania na urządzaniach, jak i w zapleczu rozwiązania.
> 

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji utworzysz aplikację konsoli języka Python, która odpowiada metoda wywoływana przez chmurę.

1. Za pomocą edytora tekstu, Utwórz nową **SimulatedDevice.py** pliku.

1. Dodaj następujące `import` instrukcje i zmienne na początku **SimulatedDevice.py** pliku:
   
    ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    
    WAIT_COUNT = 5
    METHOD_CONTEXT = 0
    METHOD_CALLBACKS = 0

    # chose MQTT or MQTT_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "<deviceConnectionString>"
    ```

1. Dodaj następujące funkcje w celu wdrożenia wywołania zwrotnego metody urządzenia oraz metoda bezpośrednia na urządzeniu:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS
    
        if method_name == "DeviceMethod":
            onDeviceMethod()
        
        print ( "\nMethod callback called with:\nmethodName = %s\npayload = %s\ncontext = %s" % (method_name, payload, user_context) )
        METHOD_CALLBACKS += 1
    
        print ( "Total calls confirmed: %d\n" % METHOD_CALLBACKS )
        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200
    
        return device_method_return_value

    def onDeviceMethod():
        print ( "Direct method called." )
    ```

1. Dodaj następującej funkcji można otworzyć połączenia z Centrum IoT i zainicjować odbiornika metody:
   
    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

        return client
    ```

1. Dodaj główne funkcje:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for direct method call, press Ctrl-C to exit" )

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
        print ( "Starting the IoT Hub Python direct methods sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Zapisz i zamknij plik **SimulatedDevice.py**.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (np. Ponów próbę połączenia), zgodnie z sugestią podaną w artykuł w witrynie MSDN [obsługi błędów przejściowych][lnk-transient-faults].
> 


## <a name="call-a-method-on-a-device"></a>Wywołanie metody na urządzeniu
W tej sekcji utworzysz aplikację konsoli języka Python, która wywołuje metodę w aplikacji symulowane urządzenie, a następnie wyświetla odpowiedzi.


1. Za pomocą edytora tekstu, Utwórz nową **CallMethodOnDevice.py** pliku.

1. Dodaj następujące `import` instrukcje i zmienne na początku **CallMethodOnDevice.py** pliku:
   
    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "<IoTHubConnectionString>"
    DEVICE_ID = "<deviceId>"

    METHOD_NAME = "DeviceMethod"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    ```

1. Dodaj następujące funkcje w celu wdrożenia wywołania zwrotnego metody urządzenia oraz metoda bezpośrednia na urządzeniu:
   
    ```python
    def iothub_devicemethod_sample_run():
        try:
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Device Method called" )
            print ( "Device Method name       : {0}".format(METHOD_NAME) )
            print ( "Device Method payload    : {0}".format(METHOD_PAYLOAD) )
            print ( "" )
            print ( "Response status          : {0}".format(response.status) )
            print ( "Response payload         : {0}".format(response.payload) )

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )
    ```

1. Dodaj funkcję głównego:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceMethod Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

1. Zapisz i Zamknij **CallMethodOnDevice.py** pliku.

## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie, aby rozpocząć nasłuchiwania dla wywołań metod z Centrum IoT:
   
    ```cmd/sh
    python SimulatedDevice.py
    ```
   
    ![][7]

1. W wierszu polecenia Uruchom następujące polecenie, aby rozpocząć monitorowanie Centrum IoT:
   
    ```cmd/sh
    python CallMethodOnDevice.py
    ```
   
    ![][8]

1. Zostanie wyświetlony urządzenia reagowania na metodę przez drukowanie wiadomość i aplikacji, w której o nazwie wyświetlanej metody odpowiedzi z urządzenia:
   
    ![][9]

## <a name="next-steps"></a>Kolejne kroki
W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Tej tożsamości urządzenia są używane do włączenia aplikacji symulowane urządzenie zareagować na metody wywoływane przez chmurę. Utworzono aplikację, która wywołuje metody na urządzeniu i wyświetla odpowiedzi z urządzenia. 

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Rozpoczynanie pracy z Centrum IoT]
* [Planowanie zadań na wielu urządzeniach][lnk-devguide-jobs]

Aby dowiedzieć się, jak rozszerzyć IoT, Twoje rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach, zobacz [emisji zadania i harmonogramu] [ lnk-tutorial-jobs] samouczka.

<!-- Images. -->
[7]: ./media/iot-hub-python-python-direct-methods/run-simulated-device.png
[8]: ./media/iot-hub-python-python-direct-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-python-python-direct-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Rozpoczynanie pracy z Centrum IoT]: iot-hub-node-node-getstarted.md
