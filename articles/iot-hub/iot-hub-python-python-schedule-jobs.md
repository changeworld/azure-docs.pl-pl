---
title: Planowanie zadań z Centrum IoT Azure (Python) | Dokumentacja firmy Microsoft
description: Sposób tworzenia harmonogramu zadań Centrum IoT Azure do wywołania metody bezpośrednio na wielu urządzeniach. Zestawy SDK IoT Azure dla języka Python umożliwia wdrożenie symulowane urządzenie aplikacje i usługi aplikacji w celu uruchomienia zadania.
services: iot-hub
documentationcenter: .net
author: msebolt
manager: timlt
editor: ''
ms.assetid: 2233356e-b005-4765-ae41-3a4872bda943
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo
ms.openlocfilehash: 632e1788f614b0a57d631759d1638027e1d0118a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="schedule-and-broadcast-jobs-python"></a>Zadania harmonogramu i emisji (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Centrum IoT Azure jest w pełni zarządzaną usługę, która umożliwia aplikacji zaplecza utworzyć i śledzenia zadań, które zaplanować i zaktualizuj milionów urządzeń.  Zadania mogą służyć do następujących czynności:

* Aktualizowanie żądanych właściwości
* Tagi aktualizacji
* Wywołanie metody bezpośredniego

Koncepcyjnie zadanie opakowuje jedną z następujących czynności i śledzi postęp wykonywania pod kątem zestawu urządzeń, jest zdefiniowany przez zapytanie dwie urządzenia.  Na przykład aplikacji zaplecza zadanie służy do wywoływania metody ponownego uruchomienia na 10 000 urządzeń, określonych przez zapytanie dwie urządzenia i zaplanowanych w przyszłości.  Aplikację można następnie śledzić każde z tych urządzeń odbierają i wykonaj metodę ponownego uruchomienia.

Dowiedz się więcej na temat każdego z tych funkcji w tych artykułach:

* Dwie urządzeń i właściwości: [Rozpoczynanie pracy z urządzenia twins] [ lnk-get-started-twin] i [samouczek: sposób użycia właściwości dwie urządzenia][lnk-twin-props]
* Bezpośrednie metody: [przewodnik dewelopera Centrum IoT — metody bezpośredniego] [ lnk-dev-methods] i [samouczek: bezpośrednie metody][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji symulowane urządzenie Python, który ma bezpośredni metodę, która umożliwia **lockDoor**, który może być wywoływany przez zaplecza rozwiązania.
* Utwórz aplikację konsoli języka Python, który wywołuje **lockDoor** metoda bezpośrednia w aplikacji symulowane urządzenie przy użyciu zadania i aktualizacje żądanej właściwości, za pomocą zadania urządzenia.

Na końcu tego samouczka znajdują się dwie aplikacje Python:

**simDevice.py**, która łączy się z Centrum IoT z tożsamości urządzenia i odbiera **lockDoor** metoda bezpośrednia.

**scheduleJobService.py**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie i aktualizuje dwie urządzenia żądanego właściwości przy użyciu zadania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

> [!NOTE]
> **Azure IoT SDK dla języka Python** nie obsługuje bezpośrednio **zadania** funkcji. Zamiast tego w tym samouczku oferuje alternatywne rozwiązanie przy użyciu asynchronicznych wątków i czasomierze. Dodatkowe aktualizacje, zobacz **zestawu SDK klienta usługi** lista funkcji na [Azure IoT SDK dla języka Python](https://github.com/Azure/azure-iot-sdk-python) strony. 
> 
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji Tworzenie aplikacji konsoli języka Python, które odpowiada metoda bezpośrednia wywoływane przez chmury, co jest wyzwalane symulowane **lockDoor** metody.

1. Z wiersza polecenia, uruchom następujące polecenie, aby zainstalować **urządzenia klienta, azure iot w-** pakietu:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Za pomocą edytora tekstu, Utwórz nową **simDevice.py** pliku w katalogu roboczym.

1. Dodaj następujące `import` instrukcje i zmienne na początku **simDevice.py** pliku. Zastąp `deviceConnectionString` z parametrami połączenia urządzenia utworzone powyżej:
   
    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Dodaj następujące wywołanie zwrotne funkcji, aby obsłużyć **lockDoor** metody:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Dodaj inny funkcja wywołania zwrotnego do obsługi aktualizacji twins urządzenia:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Dodaj następujący kod, aby zarejestrować obsługi dla **lockDoor** metody. Obejmuje to też `main` procedury:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

1. Zapisz i Zamknij **simDevice.py** pliku.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [Transient Fault Handling][lnk-transient-faults] (Obsługa przejściowych błędów).
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań dla wywołania metody bezpośredniego i aktualizowanie właściwości dwie urządzenia
W tej sekcji, Utwórz aplikację konsoli języka Python, która inicjuje zdalnej **lockDoor** na urządzeniu za pomocą innej metody bezpośredniego i aktualizowania właściwości dwie urządzenia.

1. Z wiersza polecenia, uruchom następujące polecenie, aby zainstalować **usługi klienta, azure iot w-** pakietu:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Za pomocą edytora tekstu, Utwórz nową **scheduleJobService.py** pliku w katalogu roboczym.

1. Dodaj następujące `import` instrukcje i zmienne na początku **scheduleJobService.py** pliku:
   
    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

1. Dodaj następujące funkcji, która jest używana do zapytań dla urządzeń:
   
    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
    
        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)
    
        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

1. Dodaj następujące metody do uruchomienia zadania, które wywołanie bezpośrednie dwie metody i urządzeń:
   
    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
    
            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
        
            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )
        
    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
    
            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
        
            print ( "" )
            print ( "Device twin updated." )
    ```

1. Dodaj następujący kod, aby zaplanować zadania i stan zadania aktualizacji. Obejmuje to też `main` procedury:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()
        
            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )
        
            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()
        
            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )
        
            while True:
                print ( "" )
            
                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )
            
                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )
                
                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

1. Zapisz i Zamknij **scheduleJobService.py** pliku.


## <a name="run-the-applications"></a>Uruchamianie aplikacji
Teraz można uruchomić aplikacje.

1. W wierszu polecenia w katalogu roboczym uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponownego rozruchu:
   
    ```cmd/sh
    python simDevice.py
    ```

1. W wierszu innego polecenia w katalogu roboczym, uruchom następujące polecenie, aby wyzwolić zadań, aby zablokować drzwi i zaktualizować dwie:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Zobaczysz odpowiedź urządzenia do bezpośredniego metody i urządzenia twins aktualizacji w konsoli.

    ![urządzenia wyjściowego][1]

    ![usługi danych wyjściowych][2]


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku użyto zadanie można zaplanować metoda bezpośrednia urządzenia i aktualizacji właściwości dwie urządzenia.

Aby kontynuować, wprowadzenie do korzystania z Centrum IoT i urządzenia zarządzania wzorców, takich jak zdalnego za pośrednictwem aktualizacji oprogramowania układowego udziału użytkownika, zobacz:

[Samouczek: Sposób wykonywania aktualizacji oprogramowania układowego][lnk-fwupdate]

Aby kontynuować, wprowadzenie do korzystania z Centrum IoT, zobacz [wprowadzenie do korzystania z usługi Azure IoT krawędzi][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-python-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-python-python-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults

[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
