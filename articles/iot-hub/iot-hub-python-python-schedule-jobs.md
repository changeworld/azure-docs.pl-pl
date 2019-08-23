---
title: Planowanie zadań przy użyciu usługi Azure IoT Hub (Python) | Microsoft Docs
description: Jak zaplanować zadanie IoT Hub platformy Azure w celu wywołania metody bezpośredniej na wielu urządzeniach. Korzystając z zestawów SDK usługi Azure IoT dla języka Python, można zaimplementować aplikacje symulowanych urządzeń i aplikację usługi w celu uruchomienia zadania.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: bc93dcea74f0660a4f56aec237eaae5a4e3eeb9a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904523"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Planowanie i emitowanie zadań (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub to w pełni zarządzana usługa, która umożliwia aplikacji zaplecza tworzenie i śledzenie zadań, które umożliwiają planowanie i aktualizowanie milionów urządzeń.  Zadania mogą służyć do wykonywania następujących czynności:

* Aktualizowanie żądanych właściwości
* Aktualizowanie tagów
* Wywoływanie metod bezpośrednich

Koncepcyjnie, zadanie otacza jedną z tych akcji i śledzi postęp wykonywania w odniesieniu do zestawu urządzeń, który jest zdefiniowany przez zapytanie o pojedyncze urządzenie.  Na przykład aplikacja zaplecza może użyć zadania do wywołania metody ponownego rozruchu na urządzeniach 10 000, określonych przez zapytanie o sznurki urządzenia i zaplanowane w przyszłości.  Następnie aplikacja może śledzić postęp, ponieważ każdy z tych urządzeń otrzyma i wykona metodę ponownego uruchomienia.

Dowiedz się więcej o każdej z tych funkcji w następujących artykułach:

* Sznurki i właściwości urządzenia: [Wprowadzenie do bliźniaczych reprezentacji urządzeń](iot-hub-python-twin-getstarted.md) i [samouczek: Jak korzystać z właściwości sznurka urządzenia](tutorial-device-twins.md)

* Metody bezpośrednie: [Przewodnik dla deweloperów IoT Hub — bezpośrednie metody](iot-hub-devguide-direct-methods.md) i [Samouczek: metody bezpośrednie](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację symulowanego urządzenia w języku Python, która ma metodę bezpośrednią, która umożliwia **lockDoor**, który może być wywoływany przez zaplecze rozwiązania.

* Utwórz aplikację konsolową języka Python, która wywołuje metodę **lockDoor** Direct w aplikacji symulowanego urządzenia przy użyciu zadania i aktualizuje odpowiednie właściwości przy użyciu zadania urządzenia.

Na końcu tego samouczka będziesz mieć dwie aplikacje w języku Python:

**simDevice.py**, który łączy się z Centrum IoT Hub przy użyciu tożsamości urządzenia i otrzymuje metodę **lockDoor** Direct.

**scheduleJobService.py**, która wywołuje metodę bezpośrednią w aplikacji symulowanego urządzenia i aktualizuje odpowiednie właściwości sznurka urządzenia przy użyciu zadania.

> [!NOTE]
> **Zestaw SDK usługi Azure IoT dla języka Python** nie obsługuje bezpośrednio funkcji **zadań** . W zamian ten samouczek oferuje alternatywne rozwiązanie wykorzystujące asynchroniczne wątki i czasomierze. Aby uzyskać więcej aktualizacji, zobacz listę funkcji **zestawu SDK klienta usługi** na stronie [zestawu Azure IoT SDK dla języka Python](https://github.com/Azure/azure-iot-sdk-python) .
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Poniżej przedstawiono instrukcje instalacji dotyczące wymagań wstępnych.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową w języku Python, która reaguje na bezpośrednią metodę wywoływaną przez chmurę, która wyzwala symulowaną metodę **lockDoor** .

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > Pakiety PIP dla usług Azure-iothub-Service-Client i Azure-iothub-Device-Client są obecnie dostępne tylko dla systemu operacyjnego Windows. W przypadku systemu Linux/Mac OS zapoznaj się z sekcjami dotyczącymi systemu Linux i Mac OS w sekcji [przygotowanie środowiska programistycznego dla języka Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Za pomocą edytora tekstów Utwórz nowy plik **simDevice.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje i zmienne na początku pliku **simDevice.py** . Zamień `deviceConnectionString` na parametry połączenia utworzonego powyżej urządzenia:

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

4. Dodaj następujące wywołanie zwrotne funkcji, aby obsłużyć metodę **lockDoor** :

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Dodaj kolejne wywołanie zwrotne funkcji w celu obsługi aktualizacji bliźniaczych reprezentacji urządzeń:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Dodaj następujący kod, aby zarejestrować procedurę obsługi dla metody **lockDoor** . Uwzględnij `main` również procedurę:

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

7. Zapisz i zamknij plik **simDevice.py** .

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wykładniczy wycofywania), zgodnie z opisem w artykule, [obsłudze błędów przejściowych](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza, która wywołuje metodę bezpośrednią na urządzeniu i aktualizuje sznurek urządzeń. Usługa musi mieć uprawnienia do **połączenia z usługą** , aby wywołać metodę bezpośrednią na urządzeniu. Usługa wymaga również uprawnienia do **odczytu** rejestru i **zapisu** rejestru w celu odczytu i zapisu rejestru tożsamości. Nie ma domyślnych zasad dostępu współdzielonego, które zawierają tylko te uprawnienia, więc należy ją utworzyć.

Aby utworzyć zasady dostępu współdzielonego, które przyznają uprawnienia do **nawiązywania połączenia z usługą**, **odczytu rejestru**i **zapisu rejestru** oraz do uzyskiwania parametrów połączenia dla tych zasad, wykonaj następujące kroki:

1. Otwórz Centrum IoT Hub w [Azure Portal](https://portal.azure.com). Najprostszym sposobem, aby uzyskać dostęp do usługi IoT Hub, jest wybranie opcji **grupy zasobów**, wybranie grupy zasobów, w której znajduje się centrum IoT Hub, a następnie wybranie Centrum IoT Hub z listy zasobów.

2. W okienku po lewej stronie Centrum IoT wybierz pozycję **zasady dostępu**współdzielonego.

3. W górnym menu powyżej listy zasad wybierz pozycję **Dodaj**.

4. W okienku **Dodawanie zasad dostępu** współdzielonego Wprowadź opisową nazwę zasad. na przykład: *serviceAndRegistryReadWrite*. W obszarze **uprawnienia**wybierz **pozycję Usługa Connect** i **Zapisz rejestr** (**odczyt rejestru** jest wybierany automatycznie po wybraniu pozycji **zapis w rejestrze**). Następnie wybierz przycisk **Utwórz**.

    ![Pokaż, jak dodać nowe zasady dostępu współdzielonego](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. W okienku **zasady dostępu** współdzielonego wybierz nowe zasady z listy zasad.

6. W obszarze **klucze dostępu**współdzielonego wybierz ikonę kopiowania **parametrów połączenia — klucz podstawowy** i Zapisz wartość.

    ![Sposób pobierania parametrów połączenia](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Aby uzyskać więcej informacji na temat IoT Hub zasad i uprawnień dostępu współdzielonego, zobacz [Kontrola dostępu i uprawnienia](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Planowanie zadań związanych z wywoływaniem metody bezpośredniej i aktualizowaniem właściwości sznurka urządzenia

W tej sekcji utworzysz aplikację konsolową języka Python, która inicjuje zdalne **lockDoor** na urządzeniu przy użyciu metody bezpośredniej i aktualizuje właściwości sznurka urządzenia.

1. W wierszu polecenia Uruchom następujące polecenie, aby zainstalować pakiet **Azure-IoT-Service-Client** :

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Pakiety PIP dla usług Azure-iothub-Service-Client i Azure-iothub-Device-Client są obecnie dostępne tylko dla systemu operacyjnego Windows. W przypadku systemu Linux/Mac OS zapoznaj się z sekcjami dotyczącymi systemu Linux i Mac OS w sekcji [przygotowanie środowiska programistycznego dla języka Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Za pomocą edytora tekstów Utwórz nowy plik **scheduleJobService.py** w katalogu roboczym.

3. Dodaj następujące `import` instrukcje i zmienne na początku pliku **scheduleJobService.py** . Zastąp symbol zastępczy parametrami połączenia usługi IoT Hub skopiowanymi wcześniej w temacie [pobieranie parametrów połączenia usługi IoT Hub.](#get-the-iot-hub-connection-string) `{IoTHubConnectionString}` Zastąp symbol zastępczy identyfikatorem urządzenia zarejestrowanym w zarejestrowaniu [nowego urządzenia w usłudze IoT Hub:](#register-a-new-device-in-the-iot-hub) `{deviceId}`

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

4. Dodaj następującą funkcję, która jest używana do wykonywania zapytań dotyczących urządzeń:

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

5. Dodaj następujące metody, aby uruchomić zadania, które wywołują metodę bezpośrednią i sznurek urządzenia:

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

6. Dodaj następujący kod, aby zaplanować zadania i zaktualizować stan zadania. Uwzględnij `main` również procedurę:

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

7. Zapisz i zamknij plik **scheduleJobService.py** .

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. W wierszu polecenia w katalogu roboczym Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniego ponownego uruchomienia:

    ```cmd/sh
    python simDevice.py
    ```

2. W innym wierszu polecenia w katalogu roboczym Uruchom następujące polecenie, aby wyzwolić zadania w celu zablokowania drzwi i zaktualizowania sznurka:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. W konsoli programu są widoczne odpowiedzi urządzenia do metody bezpośredniej i aktualizacji urządzenia bliźniaczych reprezentacji.

    ![Przykład zadania IoT Hub 1 — dane wyjściowe urządzenia](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Przykład zadania IoT Hub 2 — dane wyjściowe urządzenia](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto zadania do zaplanowania metody bezpośredniej dla urządzenia i aktualizacji właściwości sznurka urządzenia.

Aby nadal zacząć korzystać z wzorców IoT Hub i zarządzania urządzeniami, takimi jak zdalne przez aktualizację oprogramowania układowego, zobacz artykuł [Jak przeprowadzić aktualizację oprogramowania układowego](tutorial-firmware-update.md).