---
title: Planowanie zadań za pomocą usługi Azure IoT Hub (Python) | Dokumenty firmy Microsoft
description: Jak zaplanować zadanie usługi Azure IoT Hub, aby wywołać metodę bezpośrednią na wielu urządzeniach. ZestawY SDK usługi Azure IoT dla języka Python umożliwiają zaimplementowanie symulowanych aplikacji urządzeń i aplikacji usługi do uruchomienia zadania.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527405"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Planowanie i emisja zadań (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji zaplecza tworzenie i śledzenie zadań, które planują i aktualizują miliony urządzeń.  Zadania mogą być używane dla następujących akcji:

* Aktualizowanie żądanych właściwości
* Aktualizuj tagi
* Wywoływanie metod bezpośrednich

Koncepcyjnie zadanie zawija jedną z tych akcji i śledzi postęp wykonywania względem zestawu urządzeń, który jest zdefiniowany przez zapytanie bliźniaczej reprezentacji urządzenia.  Na przykład aplikacja zaplecza można użyć zadania do wywołania metody ponownego uruchamiania na 10 000 urządzeń, określone przez zapytanie bliźniaczej reprezentacji urządzenia i zaplanowane w przyszłości.  Ta aplikacja może następnie śledzić postęp, jak każdy z tych urządzeń odbierać i wykonywać metodę ponownego uruchamiania.

Dowiedz się więcej o każdej z tych funkcji w następujących artykułach:

* Bliźniaczy bliźniaczej reprezentacji urządzenia i właściwości: [Wprowadzenie do bliźniaczych reprezentacji urządzeń](iot-hub-python-twin-getstarted.md) i [samouczek: Jak używać właściwości bliźniaczej reprezentacji urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [Przewodnik dla programistów IoT Hub - metody bezpośrednie](iot-hub-devguide-direct-methods.md) i [samouczek: metody bezpośrednie](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację urządzenia symulowanego języka Python, która ma metodę bezpośrednią, która umożliwia **lockDoor**, który może być wywoływany przez zaplecze rozwiązania.

* Utwórz aplikację konsoli Języka Python, która wywołuje metodę **lockDoor** direct w symulowanej aplikacji urządzenia przy użyciu zadania i aktualizuje żądane właściwości przy użyciu zadania urządzenia.

Na końcu tego samouczka masz dwie aplikacje Pythona:

**simDevice.py**, który łączy się z centrum IoT hub z tożsamości urządzenia i odbiera **lockDoor** bezpośredniej metody.

**scheduleJobService.py**, która wywołuje metodę bezpośrednią w symulowanej aplikacji urządzenia i aktualizuje żądane właściwości bliźniaczej reprezentacji urządzenia za pomocą zadania.

> [!NOTE]
> Zestaw **SDK usługi Azure IoT dla języka Python** nie obsługuje bezpośrednio funkcji **zadań.** Zamiast tego ten samouczek oferuje alternatywne rozwiązanie wykorzystujące wątki asynchroniczne i czasomierze. Aby uzyskać dalsze aktualizacje, zobacz listę funkcji **SDK klienta usługi** na stronie [Zestaw SDK usługi Azure IoT dla języka Python.](https://github.com/Azure/azure-iot-sdk-python)
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zarejestruj nowe urządzenie w centrum IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli Języka Python, która odpowiada na metodę bezpośrednią wywoływaną przez chmurę, która wyzwala symulowaną metodę **lockDoor.**

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **urządzenia azure-iot:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Za pomocą edytora tekstu utwórz nowy plik **simDevice.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje i zmienne na początku pliku **simDevice.py.** Zastąp `deviceConnectionString` ciągiem połączenia utworzonego powyżej urządzenia:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Dodaj następujące wywołanie zwrotne funkcji, aby obsłużyć metodę **lockDoor:**

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Dodaj inną funkcję wywołania zwrotnego do obsługi bliźniąt urządzeń aktualizacje:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Dodaj następujący kod, aby zarejestrować program obsługi dla **lockDoor** metody. Obejmują również `main` rutynowe:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Zapisz i zamknij plik **simDevice.py.**

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy zaimplementować zasady ponawiania prób (takie jak wykładnicze wycofywanie), zgodnie z sugestią w artykule Obsługa [błędów przejściowych.](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych, która wywołuje metodę bezpośrednią na urządzeniu i aktualizuje bliźniaczej reprezentacji urządzenia. Usługa wymaga uprawnienia **do łączenia usługi,** aby wywołać metodę bezpośrednią na urządzeniu. Usługa potrzebuje również uprawnień do **odczytu** i **zapisu rejestru** rejestru do odczytu i zapisu rejestru tożsamości. Nie ma domyślnej zasady dostępu współdzielonego, która zawiera tylko te uprawnienia, więc należy je utworzyć.

Aby utworzyć zasadę dostępu współdzielonego, która udziela **uprawnień do zapisu usługi,** **odczytu rejestru**i **zapisu rejestru** oraz uzyskać parametry połączenia dla tej zasady, wykonaj następujące kroki:

1. Otwórz centrum IoT w [witrynie Azure portal](https://portal.azure.com). Najprostszym sposobem dostania się do centrum IoT hub jest **wybranie grup zasobów,** wybierz grupę zasobów, w której znajduje się centrum IoT, a następnie wybierz centrum IoT z listy zasobów.

2. W lewym okienku centrum IoT wybierz pozycję **Zasady dostępu współużytkowego**.

3. W górnym menu nad listą zasad wybierz pozycję **Dodaj**.

4. W okienku **Dodawanie zasad dostępu udostępnionego** wprowadź opisową nazwę zasad; na przykład: *serviceAndRegistryReadWrite*. W obszarze **Uprawnienia**wybierz pozycję **Połączenie usługi** i zapis **rejestru** **(odczyt rejestru** jest automatycznie wybierany po **wybraniu wpisu rejestru**). Następnie wybierz pozycję **Utwórz**.

    ![Pokazywale dodawania nowych zasad dostępu współdzielonego](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. W okienku **Zasady dostępu współdzielonego** wybierz nową zasadę z listy zasad.

6. W obszarze **Udostępnione klawisze dostępu**wybierz ikonę kopiowania **ciągu połączenia — klucz podstawowy** i zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Aby uzyskać więcej informacji na temat zasad i uprawnień dostępu współużytkowane centrum IoT Hub, zobacz [Kontrola dostępu i uprawnienia](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań wywoływania metody bezpośredniej i aktualizowania właściwości bliźniaczej reprezentacji urządzenia

W tej sekcji utworzysz aplikację konsoli Języka Python, która inicjuje zdalną **lockDoor** na urządzeniu przy użyciu metody bezpośredniej, a także aktualizuje żądane właściwości bliźniaczej reprezentacji urządzenia.

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Za pomocą edytora tekstu utwórz nowy plik **scheduleJobService.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje i zmienne na początku pliku **scheduleJobService.py.** Zastąp symbol zastępczy `{IoTHubConnectionString}` parametryem połączenia centrum IoT skopiowanym wcześniej w aplikacji Pobierz ciąg połączenia centrum [IoT](#get-the-iot-hub-connection-string). Zastąp `{deviceId}` symbol zastępczy identyfikatorem urządzenia zarejestrowanym w [Rejestrze nowe urządzenie w centrum IoT Hub:](#register-a-new-device-in-the-iot-hub)

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Dodaj następującą funkcję, która jest używana do wykonywania zapytań dotyczących urządzeń:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Dodaj następujące metody, aby uruchomić zadania, które wywołują metodę bezpośrednią i bliźniaczątkę urządzenia:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Dodaj następujący kod, aby zaplanować zadania i zaktualizować stan zadania. Obejmują również `main` rutynowe:

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

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
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

7. Zapisz i zamknij plik **scheduleJobService.py.**

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w katalogu roboczym uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponownego uruchomienia:

    ```cmd/sh
    python simDevice.py
    ```

2. W innym wierszu polecenia w katalogu roboczym uruchom następujące polecenie, aby wyzwolić zadania, aby zablokować drzwi i zaktualizować bliźniaczej reprezentacji:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. W konsoli są widoczne odpowiedzi urządzenia na metodę bezpośrednią i aktualizację bliźniaczek urządzeń.

    ![Przykład zadania centrum IoT 1 - wyjście urządzenia](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Przykładowe zadanie centrum IoT — 2- wyjście urządzenia](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania, aby zaplanować metodę bezpośrednią do urządzenia i aktualizację właściwości bliźniaczej reprezentacji urządzenia.

Aby kontynuować pracę z usługą IoT Hub i wzorcami zarządzania urządzeniami, takimi jak zdalna aktualizacja oprogramowania układowego, zobacz [Jak wykonać aktualizację oprogramowania układowego](tutorial-firmware-update.md).