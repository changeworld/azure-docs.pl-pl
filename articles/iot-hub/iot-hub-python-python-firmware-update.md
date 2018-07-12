---
title: Aktualizacja oprogramowania układowego urządzenia za pomocą usługi Azure IoT Hub (Python) | Dokumentacja firmy Microsoft
description: Jak zainicjować aktualizację oprogramowania układowego urządzenia, za pomocą zarządzania urządzeniami w usłudze Azure IoT Hub. Przy użyciu zestawów SDK usługi Azure IoT dla języka Python aplikacji symulowanego urządzenia i app service, która powoduje aktualizację oprogramowania układowego.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: kgremban
ms.openlocfilehash: d2ebdf54e595c2f02464c0c2446a6e5f5feefb9c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38482024"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Użyj urządzenia zarządzania do inicjowania oprogramowania układowego urządzenia aktualizacji (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczków pokazano, jak używać [bliźniaczej reprezentacji urządzenia] [ lnk-devtwin] i [metody bezpośrednie ] [ lnk-c2dmethod] podstawowych, aby zdalnie ponownie uruchomić urządzenie. Ten samouczek używa tych samych podstawowych usługi IoT Hub i zawiera wskazówki i dowiesz się, jak przeprowadzić aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany we wdrażaniu aktualizacji oprogramowania układowego dla przykładu urządzenia Intel Edison.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Tworzenie aplikacji konsoli języka Python, który wywołuje metody bezpośredniej firmwareUpdate w aplikacji symulowanego urządzenia za pośrednictwem usługi IoT hub.
* Tworzenie aplikacji symulowanego urządzenia, który implementuje **firmwareUpdate** bezpośrednie metody. Ta metoda inicjuje proces wieloetapowych czeka do pobierania obrazu oprogramowania układowego, który pobierze obraz oprogramowania układowego i na koniec ma zastosowanie obrazu oprogramowania układowego. Na etapie każdej aktualizacji urządzenie używa zgłaszanych właściwości do raportowania postępu.

Na końcu tego samouczka masz dwie aplikacje konsolowe środowiska Python:

**dmpatterns_fwupdate_service.PY**, który wywołuje metody bezpośredniej w symulowanej aplikacji urządzenia, wyświetla odpowiedzi i okresowo (co 500 ms) Wyświetla zaktualizowany zgłoszonych właściwości.

**dmpatterns_fwupdate_device.PY**, która łączy się z Centrum IoT hub przy użyciu utworzonej wcześniej tożsamości urządzenia otrzymuje metody bezpośredniej o firmwareUpdate, uruchamia proces wielostanowy zasymulować, w tym aktualizacji oprogramowania układowego: Oczekiwanie na obraz Pobierz pobierania nowego obrazu i na koniec stosowania obrazu.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, korzystając z metody bezpośredniej
W tej sekcji utworzysz aplikację konsoli języka Python, która inicjuje aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja używa metody bezpośredniej do inicjowania aktualizacji i używa zapytań bliźniaczych reprezentacji urządzeń, aby okresowo pobieranie stanu aktualizacji oprogramowania układowego active.

1. W wierszu polecenia, uruchom następujące polecenie, aby zainstalować **azure-iothub-service-client** pakietu:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Za pomocą edytora tekstu w katalogu roboczym, Utwórz **dmpatterns_getstarted_service.py** pliku.

1. Dodaj następujący import instrukcji i zmienne na początku **dmpatterns_getstarted_service.py** pliku. Zastąp `IoTHubConnectionString` i `deviceId` z własnymi wartościami zanotowanymi wcześniej:
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. Dodaj następującą funkcję, aby wywołać metody bezpośredniej i wyświetlić wartość firmwareUpdate zgłoszonych właściwości. Również dodać `main` procedury:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
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
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. Zapisz i Zamknij **dmpatterns_fwupdate_service.py** pliku.


## <a name="create-a-simulated-device-app"></a>Tworzenie aplikacji symulowanego urządzenia
W tej sekcji omówiono następujące zagadnienia:

* Tworzenie aplikacji konsoli języka Python, która reaguje na metodę bezpośrednią wywołaną przez chmurę
* Wyzwalanie symulowanej aktualizacji oprogramowania układowego
* Włączanie zapytań bliźniaczych reprezentacji urządzeń przy użyciu zgłoszonych właściwości w celu zidentyfikowania urządzeń i ustalenia, kiedy ostatnio przeprowadzono na nich aktualizację oprogramowania układowego

1. W wierszu polecenia, uruchom następujące polecenie, aby zainstalować **azure-iothub-device-client** pakietu:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Za pomocą edytora tekstów Utwórz **dmpatterns_fwupdate_device.py** pliku.

1. Dodaj następujący import instrukcji i zmienne na początku **dmpatterns_fwupdate_device.py** pliku. Zastąp `deviceConnectionString` przy użyciu parametrów połączenia urządzenia z usługi IoT hub:
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. Dodaj następujące funkcje, które są używane do zapewnienia zgłaszane właściwości aktualizacji i implementować metody bezpośredniej:
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Dodaj następujące funkcje, które symulują pobieranie i stosowanie obrazu oprogramowania układowego:
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. Dodaj następującą funkcję, która inicjuje bliźniaczej reprezentacji urządzenia zgłoszone właściwości i poczekaj na metody bezpośredniej do wywołania. Również dodać `main` procedury:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym należy wdrożyć zasady ponawiania (np. wycofywanie wykładnicze) zgodnie z sugestią w artykule z witryny MSDN [obsługi błędów przejściowych](https://msdn.microsoft.com/library/hh675232.aspx).
> 


## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie, aby rozpocząć nasłuchiwanie metody bezpośredniej ponowny rozruch.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. W wierszu innego polecenia Uruchom następujące polecenie, można wyzwolić ponowne uruchomienie zdalnego i wykonywania zapytań o bliźniaczej reprezentacji urządzenia można znaleźć w ciągu ostatnich ponowny rozruch czasu.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Zostanie wyświetlona odpowiedź urządzenia, do metody bezpośredniej w konsoli. Następnie należy pamiętać, zmiana zgłoszonych właściwości w całym aktualizacji oprogramowania układowego.

    ![dane wyjściowe programu][1]


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używane metody bezpośredniej do wyzwalania aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postęp aktualizacji oprogramowania układowego w zgłaszanych właściwości.

Aby dowiedzieć się, jak rozszerzyć rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach IoT, zobacz [harmonogramu i zadań emisji] [ lnk-tutorial-jobs] samouczka.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
