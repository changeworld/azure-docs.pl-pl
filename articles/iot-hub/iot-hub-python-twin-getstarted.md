---
title: Wprowadzenie do usługi Azure IoT Hub Device bliźniaczych reprezentacji (Python) | Microsoft Docs
description: Jak dodać tagi przy użyciu usługi Azure IoT Hub Device bliźniaczych reprezentacji, a następnie użyć kwerendy IoT Hubowej. Korzystając z zestawów SDK usługi Azure IoT dla języka Python, można zaimplementować aplikację symulowanego urządzenia i aplikację usługi, która dodaje Tagi i uruchamia kwerendę IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 565330528638bb6c8e0458a9761e2cf9fa4e3d2a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001474"
---
# <a name="get-started-with-device-twins-python"></a>Rozpoczynanie pracy z usługą Device bliźniaczych reprezentacji (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na końcu tego samouczka będziesz mieć dwie aplikacje konsolowe języka Python:

* **AddTagsAndQuery.py**— aplikacja zaplecza w języku Python, która dodaje Tagi i wysyła zapytania do bliźniaczych reprezentacji urządzeń.

* **ReportConnectivity.py**, aplikacja w języku Python, która symuluje urządzenie, które nawiązuje połączenie z Centrum IoT Hub przy użyciu utworzonej wcześniej tożsamości urządzenia, i zgłasza warunek łączności.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Rejestrowanie nowego urządzenia w usłudze IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Tworzenie aplikacji usługi

W tej sekcji utworzysz aplikację konsolową w języku Python, która dodaje metadane lokalizacji do sznurka urządzenia skojarzonego z Twoim programem **{ID urządzenia}** . Następnie wysyła zapytanie do bliźniaczych reprezentacji urządzenia przechowywanego w usłudze IoT Hub, wybierając urządzenia znajdujące się w Redmond, a następnie te, które zgłaszają połączenie komórkowe.

1. W katalogu roboczym Otwórz wiersz polecenia i zainstaluj **zestaw SDK usługi Azure IoT Hub dla języka Python**.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Pakiet PIP for Azure-iothub-Service-Client jest obecnie dostępny tylko dla systemu operacyjnego Windows. W przypadku systemu Linux/Mac OS zapoznaj się z sekcjami dotyczącymi systemu Linux i Mac OS w sekcji [przygotowanie środowiska programistycznego dla języka Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Za pomocą edytora tekstów Utwórz nowy plik **AddTagsAndQuery.py** .

3. Dodaj następujący kod umożliwiający zaimportowanie wymaganych modułów z zestawu SDK usługi:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Dodaj następujący kod. Zamień `[IoTHub Connection String]` na parametry połączenia usługi IoT Hub skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string). Zastąp `[Device Id]` wartość identyfikatorem urządzenia zarejestrowanym w temacie [Rejestrowanie nowego urządzenia w usłudze IoT Hub](#register-a-new-device-in-the-iot-hub).
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Dodaj następujący kod do pliku **AddTagsAndQuery.py** :

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    Obiekt **Registry** ujawnia wszystkie metody wymagane do współużytkowania z bliźniaczych reprezentacji urządzeń z usługi. Kod najpierw inicjuje obiekt **rejestru** , a następnie aktualizuje sznurki urządzenia dla **deviceId**, a na koniec uruchamia dwa zapytania. Najpierw wybiera tylko urządzenie bliźniaczych reprezentacji urządzeń znajdujące się w zakładzie **Redmond43** , a drugi zawęża zapytanie w celu wybrania tylko tych urządzeń, które są również połączone przez sieć komórkową.

6. Dodaj następujący kod na końcu **AddTagsAndQuery.py** , aby zaimplementować funkcję **iothub_service_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Uruchom aplikację przy użyciu:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Powinno zostać wyświetlone jedno urządzenie w wynikach zapytania z pytaniem o wszystkie urządzenia znajdujące się w **Redmond43** i brak dla zapytania, które ogranicza wyniki do urządzeń korzystających z sieci komórkowej.

    ![pierwsze zapytanie pokazujące wszystkie urządzenia w Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

W następnej sekcji utworzysz aplikację urządzenia, która zgłosi informacje o łączności i zmieni wynik zapytania w poprzedniej sekcji.

## <a name="create-the-device-app"></a>Tworzenie aplikacji urządzenia

W tej sekcji utworzysz aplikację konsolową w języku Python, która łączy się z centrum jako **identyfikatorem {Device ID}** , a następnie aktualizuje raportowane właściwości z użyciem sznurka urządzenia, aby zawierała informacje, które są połączone z siecią komórkową.

1. W wierszu polecenia w katalogu roboczym zainstaluj **zestaw SDK usługi Azure IoT Hub Device dla języka Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Za pomocą edytora tekstów Utwórz nowy plik **ReportConnectivity.py** .

3. Dodaj następujący kod w celu zaimportowania wymaganych modułów z zestawu SDK urządzeń:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Dodaj następujący kod. Zastąp wartość [](#register-a-new-device-in-the-iot-hub) symboluzastępczegoparametramipołączeniaurządzenia,którezostałyskopiowanepodczasrejestrowanianowegourządzeniawusłudzeIoT`[IoTHub Device Connection String]` Hub.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Dodaj następujący kod do pliku **ReportConnectivity.py** , aby zaimplementować funkcję bliźniaczych reprezentacji urządzenia:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    Obiekt **Client** uwidacznia wszystkie metody wymagane do współpracy z urządzeniem bliźniaczych reprezentacji z urządzenia. Poprzedni kod, po zainicjowaniu obiektu **klienta** , pobiera sznurki urządzenia dla urządzenia i aktualizuje swoją raportowaną Właściwość informacjami o łączności.

6. Dodaj następujący kod na końcu **ReportConnectivity.py** , aby zaimplementować funkcję **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Uruchom aplikację urządzenia:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Powinno zostać wyświetlone potwierdzenie, że bliźniaczych reprezentacji urządzenia zostały zaktualizowane.

    ![Aktualizacja bliźniaczych reprezentacji](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Teraz, gdy urządzenie zgłosiło swoje informacje o łączności, powinno być wyświetlane w obu zapytaniach. Wróć i ponownie uruchom zapytania:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Tym razem, gdy **{ID urządzenia}** powinien pojawić się w obu wynikach zapytania.

    ![drugie zapytanie](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku opisano konfigurowanie nowego centrum IoT Hub w witrynie Azure Portal, a następnie tworzenie tożsamości urządzenia w rejestrze tożsamości centrum. Metadane urządzenia zostały dodane jako Tagi z aplikacji zaplecza i zapisały symulowaną aplikację urządzenia do raportowania informacji o łączności urządzenia w ramach sznurka urządzenia. Dowiesz się również, jak badać te informacje przy użyciu rejestru.

Skorzystaj z następujących zasobów, aby dowiedzieć się, jak:

* Wyślij dane telemetryczne z urządzeń za pomocą samouczka Wprowadzenie [do IoT Hub](quickstart-send-telemetry-python.md) .

* Konfigurowanie urządzeń za pomocą odpowiednich właściwości sznurka urządzenia z użyciem [wymaganych właściwości do konfigurowania urządzeń](tutorial-device-twins.md) .

* Interakcyjne sterowanie urządzeniami (na przykład Włączanie wentylatorów z poziomu aplikacji sterowanej przez użytkownika) przy użyciu samouczka [Korzystanie z metod bezpośrednich](quickstart-control-device-python.md) .
