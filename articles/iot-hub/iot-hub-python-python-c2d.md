---
title: Komunikaty między chmurami a urządzeniem za pomocą usługi Azure IoT Hub (Python) | Dokumenty firmy Microsoft
description: Jak wysyłać komunikaty z chmury do urządzenia do urządzenia z centrum Usługi Azure IoT przy użyciu zestawów SDK usługi Azure IoT dla języka Python. Zmodyfikowana aplikacja urządzenia jest modyfikowana w celu odbierania komunikatów z chmury do urządzenia i modyfikowania aplikacji zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110421"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Wysyłanie wiadomości z chmury do urządzenia za pomocą usługi IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Usługa Azure IoT Hub to w pełni zarządzana usługa, która pomaga umożliwić niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania. Wyślij [dane telemetryczne z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md) Szybki start pokazuje, jak utworzyć centrum IoT hub, aprowizować tożsamość urządzenia w nim i kod symulowanej aplikacji urządzenia, która wysyła komunikaty urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek opiera się na [wysyłaniu danych telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md)hub . To pokazuje, jak:

* Z zaplecza rozwiązania wysyłaj komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem usługi IoT Hub.

* Odbieranie komunikatów z chmury do urządzenia na urządzeniu.

* W rozwiązaniu zaplecza, żądanie potwierdzenia dostarczania *(opinie)* dla wiadomości wysyłanych do urządzenia z Usługi IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzenia można znaleźć w [przewodniku dla deweloperów usługi IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchomisz dwie aplikacje konsoli Python:

* **SimulatedDevice.py**, zmodyfikowanej wersji aplikacji utworzonej w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-python.md), który łączy się z centrum IoT hub i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage.py**, który wysyła komunikat chmury do urządzenia do aplikacji symulowanego urządzenia za pośrednictwem usługi IoT Hub, a następnie odbiera potwierdzenie dostarczenia.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Upewnij się, że port 8883 jest otwarty w zaporze. Przykład urządzenia w tym artykule używa protokołu MQTT, który komunikuje się za pomocą portu 8883. Ten port może być zablokowany w niektórych środowiskach sieci firmowych i edukacyjnych. Aby uzyskać więcej informacji i sposobów obejść ten problem, zobacz [Łączenie się z centrum IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieranie wiadomości w aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsoli Języka Python, aby symulować urządzenie i odbierać komunikaty z chmury do urządzenia z centrum IoT hub.

1. Za pomocą edytora tekstu utwórz plik **SimulatedDevice.py.**

2. Dodaj następujące `import` instrukcje i zmienne na początku pliku **SimulatedDevice.py:**

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Dodaj następujący kod do **pliku SimulatedDevice.py.** Zastąp wartość zastępczą "{deviceConnectionString}" ciągiem połączenia urządzenia dla urządzenia utworzonego w programie [Wyślij dane telemetryczne z urządzenia do szybkiego startu centrum IoT Hub:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Dodaj następującą funkcję, aby wydrukować odebrane wiadomości do konsoli:

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

7. Zapisz i zamknij **plik SimulatedDevice.py.**

## <a name="get-the-iot-hub-connection-string"></a>Pobierz ciąg połączenia koncentratora IoT

W tym artykule utworzysz usługę wewnętrznej bazy danych do wysyłania komunikatów z chmury do urządzenia za pośrednictwem centrum IoT utworzonego w [aplikacji Wyślij dane telemetryczne z urządzenia do centrum IoT hub](quickstart-send-telemetry-python.md). Aby wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnienia **do połączenia usługi.** Domyślnie każdy Centrum IoT jest tworzony przy pomocą zasady dostępu współdzielonego o nazwie **usługi,** która udziela tego uprawnienia.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie wiadomości z chmury do urządzenia

W tej sekcji utworzysz aplikację konsoli Języka Python, która wysyła komunikaty z chmury do urządzenia do symulowanej aplikacji urządzenia. Potrzebny jest identyfikator urządzenia dodanego w danych [telemetrycznych wysyłania z urządzenia do szybkiego startu centrum IoT Hub.](quickstart-send-telemetry-python.md) Potrzebny jest również ciąg połączenia centrum IoT skopiowany wcześniej w [pliku Pobierz ciąg połączenia centrum IoT](#get-the-iot-hub-connection-string).

1. Za pomocą edytora tekstu utwórz plik **SendCloudToDeviceMessage.py.**

2. Dodaj następujące `import` instrukcje i zmienne na początku pliku **SendCloudToDeviceMessage.py:**

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

3. Dodaj następujący kod do **pliku SendCloudToDeviceMessage.py.** Zastąp wartości zastępcze symboli zastępczych "{iot hub connection string}" i "{device id}" ciągiem połączenia i identyfikatorem urządzenia:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Dodaj następującą funkcję, aby wydrukować wiadomości zwrotne do konsoli:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Dodaj następujący kod, aby wysłać wiadomość do urządzenia i obsłużyć wiadomość zwrotną, gdy urządzenie potwierdzi komunikat z chmury do urządzenia:

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

7. Zapisz i zamknij **plik SendCloudToDeviceMessage.py.**

## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz można uruchomić aplikacje.

1. Otwórz wiersz polecenia i zainstaluj **zestaw SDK urządzenia usługi Azure IoT Hub dla języka Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. W wierszu polecenia uruchom następujące polecenie, aby nasłuchiwać komunikatów z chmury do urządzenia:

    ```shell
    python SimulatedDevice.py
    ```

    ![Uruchamianie aplikacji symulowanego urządzenia](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Otwórz nowy wiersz polecenia i zainstaluj **zestaw SDK usługi Azure IoT Hub dla języka Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. W wierszu polecenia uruchom następujące polecenie, aby wysłać wiadomość z chmury do urządzenia i poczekać na opinię o wiadomości:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Uruchamianie aplikacji w celu wysłania polecenia chmury do urządzenia](./media/iot-hub-python-python-c2d/send-command.png)

5. Zanotuj wiadomość odebraną przez urządzenie.

    ![Odebrano wiadomość](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak wysyłać i odbierać komunikaty z chmury do urządzenia.

Aby zobaczyć przykłady kompletnych kompleksowych rozwiązań korzystających z usługi IoT Hub, zobacz [akcelerator rozwiązań zdalnego monitorowania usługi Azure IoT.](https://azure.microsoft.com/documentation/suites/iot-suite/)

Aby dowiedzieć się więcej na temat opracowywania rozwiązań za pomocą usługi IoT Hub, zobacz [przewodnik dla deweloperów usługi IoT Hub.](iot-hub-devguide.md)
