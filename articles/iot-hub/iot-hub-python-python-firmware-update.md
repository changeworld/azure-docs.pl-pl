---
title: Aktualizacja oprogramowania układowego urządzenia z Centrum IoT Azure (Python) | Dokumentacja firmy Microsoft
description: Jak używać zarządzania urządzeniami w usłudze Azure IoT Hub zainicjować aktualizację oprogramowania układowego urządzenia. Przy użyciu zestawów SDK IoT Azure dla języka Python aplikacji symulowane urządzenie i usługi aplikacji, które wyzwala aktualizacji oprogramowania układowego.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/16/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 4a042723e6bebebb891640950e579e031e27e61d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Zarządzanie urządzeniami używany do inicjowania oprogramowanie układowe urządzenia aktualizacji (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

W [wprowadzenie do zarządzania urządzeniami] [ lnk-dm-getstarted] samouczka przedstawiono sposób użycia [dwie urządzenia] [ lnk-devtwin] i [bezpośrednie metody ] [ lnk-c2dmethod] podstawowych zdalnie ponownego uruchomienia urządzenia. W tym samouczku korzysta z tej samej podstawowych Centrum IoT i zawiera wskazówki i pokazuje, jak wykonać aktualizację oprogramowania układowego symulowane end-to-end.  Ten wzorzec jest używany podczas wdrażania aktualizacji oprogramowania układowego przykładowej urządzenia Intel Edison.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację konsoli języka Python, który wywołuje metodę bezpośredniego firmwareUpdate w aplikacji symulowane urządzenie za pomocą Centrum IoT.
* Tworzenie aplikacji symulowane urządzenie, który implementuje **firmwareUpdate** metoda bezpośrednia. Ta metoda inicjuje procesem wieloetapowym czeka na pobranie obrazu oprogramowania układowego, pobierze obraz oprogramowania układowego i na koniec dotyczy oprogramowania układowego obrazu. Podczas każdego etapu aktualizacji urządzenie korzysta z właściwości zgłoszony do raport dotyczący postępu.

Na końcu tego samouczka znajdują się dwie aplikacje konsoli Python:

**dmpatterns_fwupdate_service.PY**, która wywołuje metodę bezpośrednio w aplikacji symulowane urządzenie Wyświetla odpowiedzi i okresowo (co 500 MS.) Wyświetla zaktualizowanego zgłoszone właściwości.

**dmpatterns_fwupdate_device.PY**, która łączy się z Centrum IoT z tożsamości urządzenia utworzony wcześniej, otrzymuje firmwareUpdate metoda bezpośrednia, uruchamia proces wielu stanu do symulowania, w tym aktualizacji oprogramowania układowego: Oczekiwanie na obraz Pobierz pobieranie nowy obraz, a ostatecznie stosowania obrazu.

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Wyzwalanie aktualizacji oprogramowania układowego zdalnego na urządzeniu, za pomocą bezpośrednich — metoda
W tej sekcji utworzysz aplikację konsoli języka Python, która inicjuje aktualizacji oprogramowania układowego zdalnego na urządzeniu. Aplikacja korzysta metoda bezpośrednia zainicjować aktualizację, a zapytania dwie urządzenia okresowo pobrać stanu aktualizacji oprogramowania układowego active.

1. Z wiersza polecenia, uruchom następujące polecenie, aby zainstalować **usługi klienta, azure Centrum iothub w-** pakietu:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Tworzenie za pomocą edytora tekstu, w katalogu roboczym **dmpatterns_getstarted_service.py** pliku.

1. Dodaj następujące "import" instrukcje i zmienne na początku **dmpatterns_getstarted_service.py** pliku. Zastąp `IoTHubConnectionString` i `deviceId` własnymi wartościami podano wcześniej:
   
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

1. Dodaj następującą funkcję można wywołać metodę bezpośredniego i wyświetlać wartość firmwareUpdate zgłosił właściwości. Dodaj również `main` procedury:
   
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

* Utwórz aplikację konsoli języka Python, które odpowiada metoda bezpośrednia wywoływane przez chmury
* Wyzwalanie symulowanej aktualizacji oprogramowania układowego
* Włączanie zapytań bliźniaczych reprezentacji urządzeń przy użyciu zgłoszonych właściwości w celu zidentyfikowania urządzeń i ustalenia, kiedy ostatnio przeprowadzono na nich aktualizację oprogramowania układowego

1. Z wiersza polecenia, uruchom następujące polecenie, aby zainstalować **urządzenia klienta, azure Centrum iothub w-** pakietu:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Za pomocą edytora tekstu, Utwórz **dmpatterns_fwupdate_device.py** pliku.

1. Dodaj następujące "import" instrukcje i zmienne na początku **dmpatterns_fwupdate_device.py** pliku. Zastąp `deviceConnectionString` parametrami połączenia urządzenia z Centrum IoT:
   
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

1. Dodaj następujące funkcje, które są używane do Podaj zgłosił aktualizacji właściwości i zaimplementuj metodę bezpośrednie:
   
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

8. Dodaj następującą funkcję, która inicjuje dwie urządzenia zgłoszone właściwości, a następnie zaczekaj bezpośredniego metody do wywołania. Dodaj również `main` procedury:
   
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
> Dla uproszczenia ten samouczek nie zawiera opisu wdrożenia żadnych zasad ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania (na przykład wykładniczego wycofywania), zgodnie z sugestią podaną w artykuł w witrynie MSDN [obsługi błędów przejściowych](https://msdn.microsoft.com/library/hh675232.aspx).
> 


## <a name="run-the-apps"></a>Uruchamianie aplikacji
Teraz można przystąpić do uruchomienia aplikacji.

1. W wierszu polecenia Uruchom następujące polecenie Rozpoczęcie nasłuchiwania metoda bezpośrednia ponowne uruchomienie komputera.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. Innego wiersza polecenia Uruchom następujące polecenie, aby zdalne ponowne uruchamianie systemu i kwerendę dla dwie urządzenia znaleźć ostatniego ponownego rozruchu czasu.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Zobaczysz odpowiedź urządzenia do metody bezpośrednio w konsoli. Następnie zanotuj zmianę właściwości zgłoszonego przez aktualizację oprogramowania układowego.

    ![Dane wyjściowe programu][1]


## <a name="next-steps"></a>Kolejne kroki
W tym samouczku używana metoda bezpośrednia do wyzwolenia aktualizacji oprogramowania układowego zdalnego na urządzeniu i można śledzić postępy aktualizacji oprogramowania układowego zgłoszone właściwości.

Aby dowiedzieć się, jak rozszerzyć IoT, Twoje rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach, zobacz [emisji zadania i harmonogramu] [ lnk-tutorial-jobs] samouczka.

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
