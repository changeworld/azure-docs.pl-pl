---
title: Tożsamość modułu usługi Azure IoT Hub i bliźniacza reprezentacja modułu (Python)
description: Dowiedz się, jak utworzyć tożsamość modułu i zaktualizować bliźniaczy moduł przy użyciu pakietów SDK IoT dla języka Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756940"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Wprowadzenie do tożsamości modułu IoT Hub i bliźniaczej reprezentacji modułu (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Tożsamości modułów i bliźniaczych reprezentacji modułów](iot-hub-devguide-module-twins.md) są podobne do tożsamości urządzeń usługi Azure IoT Hub i bliźniaczych reprezentacji urządzeń, ale zapewniają większą szczegółowość. Podczas gdy tożsamości urządzeń usługi Azure IoT Hub i bliźniacze urządzenia umożliwiają aplikacji zaplecza skonfigurowanie urządzenia i zapewnienie widoczności w warunkach urządzenia, tożsamości modułów i bliźniacze moduły zapewniają te możliwości dla poszczególnych składników urządzenia. Na urządzeniach obsługujących wiele składników, takich jak urządzenia oparte na systemie operacyjnym lub urządzenia oprogramowania układowego, umożliwiają one izolowane konfiguracje i warunki dla każdego składnika.
>

Na końcu tego samouczka masz trzy aplikacje Pythona:

* **CreateModule**, który tworzy tożsamość urządzenia, tożsamość modułu i skojarzone klucze zabezpieczeń do łączenia klientów urządzenia i modułu.

* **UpdateModuleTwinDesiredProperties**, który wysyła zaktualizowane moduł bliźniaczej żądane właściwości do centrum IoT Hub.

* **ReceiveModuleTwinDesiredPropertiesPatch**, który odbiera moduł twin żądane właściwości poprawki na urządzeniu.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Tworzenie centrum IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę zaplecza, która dodaje urządzenie w rejestrze tożsamości, a następnie dodaje moduł do tego urządzenia. Ta usługa wymaga uprawnienia **do zapisu rejestru** (który obejmuje również **rejestr odczytu).** Można również utworzyć usługę, która dodaje żądane właściwości do bliźniaczej reprezentacji modułu dla nowo utworzonego modułu. Ta usługa wymaga uprawnienia **do łączenia usługi.** Chociaż istnieją domyślne zasady dostępu współdzielonego, które przyznają te uprawnienia indywidualnie, w tej sekcji utworzysz niestandardową zasadę dostępu udostępnionego, która zawiera oba te uprawnienia.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Tworzenie tożsamości urządzenia i tożsamości modułu w Centrum IoT

W tej sekcji utworzysz aplikację usługi Języka Python, która tworzy tożsamość urządzenia i tożsamość modułu w rejestrze tożsamości w centrum IoT hub. Urządzenie lub moduł nie może połączyć się z centrum IoT Hub, chyba że ma wpis w rejestrze tożsamości. Aby uzyskać więcej informacji, zobacz [Opis rejestru tożsamości w centrum IoT hub](iot-hub-devguide-identity-registry.md). Uruchomienie tej aplikacji konsolowej powoduje wygenerowanie unikatowego identyfikatora i klucza zarówno dla urządzenia, jak i modułu. Urządzenie i moduł korzystają z tych wartości w celu identyfikowania się podczas wysyłania komunikatów urządzenie-chmura do usługi IoT Hub. W identyfikatorach jest uwzględniana wielkość liter.

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **msrest.** Ten pakiet jest potrzebny do wychwytuowania wyjątków **HTTPOperationError.**

    ```cmd/sh
    pip install msrest
    ```

1. Za pomocą edytora tekstu utwórz plik o nazwie **CreateModule.py** w katalogu roboczym.

1. Dodaj następujący kod do pliku języka Python. Zastąp *YourIoTHubConnectionString* ciągiem połączenia skopiowanym w [aplikacji Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. W wierszu polecenia uruchom następujące polecenie:

    ```cmd/sh
    python CreateModule.py
    ```

Ta aplikacja tworzy tożsamość urządzenia z identyfikatorem **myFirstDevice** i tożsamości modułu z **identyfikatorem myFirstModule** pod **urządzeniem myFirstDevice**. (Jeśli identyfikator urządzenia lub modułu już istnieje w rejestrze tożsamości, kod po prostu pobiera istniejące informacje o urządzeniu lub module). Aplikacja wyświetla identyfikator i klucz podstawowy dla każdej tożsamości.

> [!NOTE]
> Rejestr tożsamości usługi IoT Hub przechowuje tożsamości urządzenia i modułu tylko po to, aby umożliwić bezpieczny dostęp do centrum IoT. W rejestrze tożsamości są przechowywane identyfikatory urządzeń i klucze służące jako poświadczenia zabezpieczeń. W rejestrze tożsamości są także przechowywane flagi włączenia/wyłączenia dla każdego urządzenia, za pomocą których można wyłączyć dostęp do danego urządzenia. Jeśli aplikacja wymaga przechowywania innych metadanych dla określonego urządzenia, powinna korzystać z magazynu określonego dla aplikacji. Nie istnieje flaga włączenia/wyłączenia tożsamości modułów. Aby uzyskać więcej informacji, zobacz [Opis rejestru tożsamości w centrum IoT hub](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Aktualizowanie bliźniaczej reprezentacji modułu przy użyciu pakietu SDK usługi Python

W tej sekcji utworzysz aplikację usługi Języka Python, która aktualizuje moduł bliźniaczej reprezentacji żądane właściwości.

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **azure-iot-hub.** Ten krok można pominąć, jeśli pakiet **azure-iot-hub** został zainstalowany w poprzedniej sekcji.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Za pomocą edytora tekstu utwórz plik o nazwie **UpdateModuleTwinDesiredProperties.py** w katalogu roboczym.

1. Dodaj następujący kod do pliku języka Python. Zastąp *YourIoTHubConnectionString* ciągiem połączenia skopiowanym w [aplikacji Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Pobierz aktualizacje po stronie urządzenia

W tej sekcji utworzysz aplikację Języka Python, aby uzyskać bliźniaczącą modułową aktualizację żądanych właściwości na urządzeniu.

1. Pobierz parametry połączenia modułu. W [witrynie Azure portal](https://portal.azure.com/)przejdź do centrum IoT Hub i wybierz pozycję Urządzenia **IoT** w lewym okienku. Wybierz **myFirstDevice** z listy urządzeń i otwórz go. W obszarze **Tożsamości modułu**wybierz **myFirstModule**. Skopiuj parametry połączenia modułu. Potrzebujesz go w następnym kroku.

   ![Szczegóły modułu w witrynie Azure Portal](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. W wierszu polecenia uruchom następujące polecenie, aby zainstalować pakiet **urządzenia azure-iot:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Za pomocą edytora tekstu utwórz plik o nazwie **ReceiveModuleTwinDesiredPropertiesPatch.py** w katalogu roboczym.

1. Dodaj następujący kod do pliku języka Python. *Zastąp YourModuleConnectionString* ciągiem połączenia modułu skopiowanym w kroku 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Uruchamianie aplikacji

W tej sekcji należy uruchomić aplikację urządzenia **ReceiveModuleTwinDesiredPropertiesPatch,** a następnie uruchomić aplikację usługi **UpdateModuleTwinDesiredProperties,** aby zaktualizować żądane właściwości modułu.

1. Otwórz wiersz polecenia i uruchom aplikację urządzenia:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Wstępne wyjście aplikacji urządzenia](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Otwórz osobny wiersz polecenia i uruchom aplikację usługi:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Należy zauważyć, że **TelemetryInterval** żądana właściwość pojawia się w zaktualizowanej bliźniaczej reprezentacji modułu w danych wyjściowych aplikacji usługi:

   ![Dane wyjściowe aplikacji usługi](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Ta sama właściwość pojawia się w żądanej poprawce właściwości otrzymanej w danych wyjściowych aplikacji urządzenia:

   ![Dane wyjściowe aplikacji urządzenia pokazuje żądaną poprawkę właściwości](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Następne kroki

Aby kontynuować wprowadzenie do usługi IoT Hub i zapoznać się z innymi scenariuszami IoT, zobacz:

* [Wprowadzenie do zarządzania urządzeniami](iot-hub-node-node-device-management-get-started.md)

* [Getting started with IoT Edge](../iot-edge/tutorial-simulate-device-linux.md) (Wprowadzenie do usługi IoT Edge)