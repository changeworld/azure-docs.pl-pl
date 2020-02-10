---
title: Komunikaty z chmury do urządzenia za pomocą usługi Azure IoT Hub (Python) | Microsoft Docs
description: Jak wysyłać komunikaty z chmury do urządzenia z usługi Azure IoT Hub za pomocą zestawów SDK usługi Azure IoT dla języka Python. Zmodyfikuj aplikację symulowanego urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikować aplikację zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110421"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Wysyłanie komunikatów z chmury do urządzeń za pomocą IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md) pokazuje, jak utworzyć Centrum IoT, zainicjować w nim tożsamość urządzenia i kod aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek kompiluje na temat [wysyłania danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md). Pokazano, jak:

* Z zaplecza rozwiązania Wyślij komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem IoT Hub.

* Odbieraj komunikaty z chmury do urządzenia na urządzeniu.

* Z zaplecza rozwiązania Poproś o potwierdzenie dostarczenia (*Opinie*) o komunikatach wysyłanych do urządzenia z IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzeń można znaleźć w [przewodniku dewelopera IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchamiasz dwie aplikacje konsolowe języka Python:

* **SimulatedDevice.py**, zmodyfikowana wersja aplikacji utworzona w wysyłanie danych [telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md), która łączy się z Centrum IoT Hub i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage.py**, który wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pomocą IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. W przykładzie urządzenia w tym artykule jest używany protokół MQTT, który komunikuje się przez port 8883. Ten port może być blokowany w niektórych firmowych i edukacyjnych środowiskach sieciowych. Aby uzyskać więcej informacji i sposobów obejścia tego problemu, zobacz [nawiązywanie połączenia z IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieraj komunikaty w aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową języka Python w celu symulowania urządzenia i otrzymywania komunikatów z chmury do urządzeń z Centrum IoT.

1. Za pomocą edytora tekstów Utwórz plik **SimulatedDevice.py** .

2. Dodaj następujące `import` instrukcje i zmienne na początku pliku **SimulatedDevice.py** :

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Dodaj następujący kod do pliku **SimulatedDevice.py** . Zastąp wartość symbolu zastępczego "{deviceConnectionString}" parametrami połączenia urządzenia dla urządzenia utworzonego w polu [Wyślij dane telemetryczne z urządzenia do](quickstart-send-telemetry-python.md) przewodnika Szybki Start dotyczącego usługi IoT Hub:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Dodaj następującą funkcję, aby wydrukować odebrane komunikaty do konsoli:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Dodaj następujący kod, aby zainicjować klienta i poczekać na odebranie komunikatu z chmury do urządzenia:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Dodaj następującą funkcję główną:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Zapisz i zamknij plik **SimulatedDevice.py** .

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md). Aby można było wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnień do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatu z chmury do urządzenia

W tej sekcji utworzysz aplikację konsolową w języku Python, która wysyła komunikaty z chmury do urządzenia do aplikacji symulowanego urządzenia. Potrzebujesz identyfikatora urządzenia dodanego w polu [Wyślij telemetrię z urządzenia do](quickstart-send-telemetry-python.md) przewodnika Szybki Start dotyczącego usługi IoT Hub. Potrzebne są również parametry połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

1. Za pomocą edytora tekstów Utwórz plik **SendCloudToDeviceMessage.py** .

2. Dodaj następujące `import` instrukcje i zmienne na początku pliku **SendCloudToDeviceMessage.py** :

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Dodaj następujący kod do pliku **SendCloudToDeviceMessage.py** . Zastąp wartości symboli zastępczych "{ciąg połączenia usługi IoT Hub}" i "{Device ID}" parametrami połączenia usługi IoT Hub i zanotowanym wcześniej IDENTYFIKATORem urządzenia:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Dodaj następującą funkcję, aby wydrukować wiadomości z opiniami do konsoli:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Dodaj następujący kod, aby wysłać wiadomość na urządzenie i obsłużyć komunikat o opinii, gdy urządzenie potwierdzi komunikat z chmury do urządzenia:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Dodaj następującą funkcję główną:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Zapisz i zamknij plik **SendCloudToDeviceMessage.py** .

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. Otwórz wiersz polecenia i zainstaluj **zestaw SDK urządzeń IoT Hub platformy Azure dla języka Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. W wierszu polecenia Uruchom następujące polecenie, aby nasłuchiwać komunikatów z chmury do urządzenia:

    ```shell
    python SimulatedDevice.py
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Otwórz nowy wiersz polecenia i zainstaluj **zestaw SDK usługi Azure IoT Hub dla języka Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. W wierszu polecenia Uruchom następujące polecenie, aby wysłać komunikat z chmury do urządzenia i zaczekaj na odpowiedź na komunikat:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Uruchom aplikację, aby wysłać polecenie z chmury do urządzenia](./media/iot-hub-python-python-c2d/send-command.png)

5. Zwróć uwagę na komunikat otrzymany przez urządzenie.

    ![Odebrano komunikat](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zapoznać się z przykładami kompletnych kompleksowych rozwiązań, które używają IoT Hub, zobacz [Akcelerator rozwiązania do monitorowania zdalnego usługi Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą IoT Hub, zobacz [przewodnik dewelopera IoT Hub](iot-hub-devguide.md).
