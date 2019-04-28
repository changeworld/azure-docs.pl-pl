---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (Python) | Dokumentacja firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub do wywołania metody bezpośredniej na wielu urządzeniach. Przy użyciu zestawów SDK usługi Azure IoT dla języka Python aplikacji urządzenia symulowanego i app service, aby uruchomić zadanie.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: c15db0766da3b4c18c306106ffdd5fc75a9143aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442804"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Planowanie i emitowanie zadań (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji zaplecza utworzyć i śledzić zadania, które planować i aktualizowania milionów urządzeń.  Zadania mogą służyć do następujących czynności:

* Aktualizowanie żądanych właściwości
* Aktualizacji tagów
* Wywoływanie metod bezpośrednich

Model zadanie opakowuje jedną z następujących czynności i śledzi postęp wykonywania względem zbiór urządzeń, która jest zdefiniowana przez zapytanie w bliźniaczej reprezentacji urządzenia.  Na przykład aby wywołać metodę ponownego uruchomienia na 10 000 urządzeń, określonych przez zapytanie bliźniacza reprezentacja urządzenia i zaplanować w czasie przyszłych zadań można użyć aplikacji zaplecza.  Tej aplikacji można śledzić postęp, zgodnie z każdego z tych urządzeń odbierania i wykonać metodę ponownego uruchomienia.

Dowiedz się więcej na temat każdego z tych możliwości w następujących artykułach:

* Bliźniacza reprezentacja urządzenia i właściwości: [Wprowadzenie do bliźniaków urządzeń](iot-hub-python-twin-getstarted.md) i [samouczka: Jak korzystać z właściwości bliźniaczych reprezentacji urządzeń](tutorial-device-twins.md)

* Metody bezpośrednie: [Usługi IoT Hub developer guide - metod bezpośrednich](iot-hub-devguide-direct-methods.md) i [samouczek: metody bezpośrednie](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji symulowanego urządzenia w języku Python został bezpośrednie metody, która umożliwia **lockDoor**, który można wywoływać za pomocą zaplecza rozwiązania.

* Tworzenie aplikacji konsoli języka Python, który wywołuje **lockDoor** bezpośrednie metody w aplikacji symulowanego urządzenia za pomocą zadania i aktualizacje żądane właściwości przy użyciu zadania urządzeń.

Na końcu tego samouczka będziesz mieć dwie aplikacje Python:

**simDevice.py**, który nawiązuje połączenie z Centrum IoT przy użyciu tożsamości urządzenia i odbiera **lockDoor** bezpośrednie metody.

**scheduleJobService.py**, która wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia i aktualizacji bliźniaczej reprezentacji urządzenia żądane właściwości przy użyciu zadania.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Python 2.x lub 3.x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*](https://pip.pypa.io/en/stable/installing/).

* Zainstaluj [Pakiet redystrybucyjny języka Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut.)

> [!NOTE]
> **Azure IoT SDK for Python** nie obsługuje bezpośrednio **zadań** funkcji. Zamiast tego w tym samouczku oferuje rozwiązanie alternatywne, wykorzystując asynchronicznego wątków i czasomierzy. Więcej aktualizacji, zobacz **zestawu SDK klienta usługi** lista funkcji na [Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) strony. 
>

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Pobieranie parametrów połączenia dla centrum IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli języka Python, która reaguje na metodę bezpośrednią wywołaną przez chmurę, co powoduje wyzwolenie symulowanego **lockDoor** metody.

1. W wierszu polecenia, uruchom następujące polecenie, aby zainstalować **azure-iot-device-client** pakietu:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Za pomocą edytora tekstu Utwórz nowy **simDevice.py** pliku w katalogu roboczym.

3. Dodaj następujący kod `import` instrukcji i zmienne na początku **simDevice.py** pliku. Zastąp `deviceConnectionString` przy użyciu parametrów połączenia urządzenia zostały utworzone powyżej:

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

4. Dodaj następujące wywołanie zwrotne funkcji, aby obsłużyć **lockDoor** metody:

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Dodaj inny funkcji wywołania zwrotnego do obsługi aktualizacji bliźniaczych reprezentacji urządzeń:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Dodaj następujący kod, aby zarejestrować program obsługi dla **lockDoor** metody. Również obejmować `main` procedury:

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

7. Zapisz i Zamknij **simDevice.py** pliku.

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią podaną w artykule [obsługi błędów przejściowych](/azure/architecture/best-practices/transient-faults).
>

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań do wywoływania metody bezpośredniej i aktualizowanie właściwości bliźniaczej reprezentacji urządzenia

W tej sekcji utworzysz aplikację konsoli języka Python, która inicjuje zdalnej **lockDoor** na urządzeniu przy użyciu metody bezpośredniej i aktualizowania właściwości bliźniaczej reprezentacji urządzenia.

1. W wierszu polecenia, uruchom następujące polecenie, aby zainstalować **azure-iot-service-client** pakietu:

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

2. Za pomocą edytora tekstu Utwórz nowy **scheduleJobService.py** pliku w katalogu roboczym.

3. Dodaj następujący kod `import` instrukcji i zmienne na początku **scheduleJobService.py** pliku:

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

4. Dodaj następującą funkcję, która jest używana do wykonywania zapytań w przypadku urządzeń:

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

5. Dodaj następujące metody do uruchamiania zadań, które wywołują bezpośrednie bliźniaczej reprezentacji metody i urządzeń:

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

6. Dodaj następujący kod do planowania zadań i zaktualizuj stan zadania. Również obejmować `main` procedury:

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

7. Zapisz i Zamknij **scheduleJobService.py** pliku.

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w katalogu roboczym uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponowne uruchomienie komputera:

    ```cmd/sh
    python simDevice.py
    ```

2. W wierszu innego polecenia w katalogu roboczym, uruchom następujące polecenie, aby wyzwolić zadania, aby zablokować drzwi biblioteki i zaktualizować bliźniaczej reprezentacji:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Zostanie wyświetlona odpowiedź urządzenia, do metody bezpośredniej i bliźniacze reprezentacje urządzeń aktualizacji w konsoli.

    ![Przykład IoT Hub Job 1 — dane wyjściowe urządzenia](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![IoT Hub Job przykładowe 2 — dane wyjściowe z urządzenia](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto zadania można zaplanować metody bezpośredniej do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Aby kontynuować wprowadzenie do usługi IoT Hub i wzorców zarządzania urządzeniami, takich jak zdalne za pośrednictwem aktualizacji oprogramowania układowego air, zobacz [jak zaktualizować oprogramowanie układowe](tutorial-firmware-update.md).