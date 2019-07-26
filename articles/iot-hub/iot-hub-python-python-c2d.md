---
title: Komunikaty z chmury do urządzenia za pomocą usługi Azure IoT Hub (Python) | Microsoft Docs
description: Jak wysyłać komunikaty z chmury do urządzenia z usługi Azure IoT Hub za pomocą zestawów SDK usługi Azure IoT dla języka Python. Zmodyfikuj aplikację symulowanego urządzenia, aby odbierać komunikaty z chmury do urządzenia i modyfikować aplikację zaplecza w celu wysyłania komunikatów z chmury do urządzenia.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: da5481af1086c14ce0961d0ac6b8ef55cfc73707
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403883"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Wysyłanie komunikatów z chmury do urządzeń za pomocą IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Wprowadzenie

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania. Wysyłanie danych telemetrycznych [z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md) pokazuje, jak utworzyć Centrum IoT, zainicjować w nim tożsamość urządzenia i kod aplikacji symulowanego urządzenia, która wysyła komunikaty z urządzenia do chmury.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ten samouczek kompiluje na temat wysyłania danych telemetrycznych [z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md). Pokazano, jak:

* Z zaplecza rozwiązania Wyślij komunikaty z chmury do urządzenia do jednego urządzenia za pośrednictwem IoT Hub.

* Odbieraj komunikaty z chmury do urządzenia na urządzeniu.

* Z zaplecza rozwiązania Poproś o potwierdzenie dostarczenia (*Opinie*) o komunikatach wysyłanych do urządzenia z IoT Hub.

Więcej informacji na temat komunikatów z chmury do urządzeń można znaleźć w przewodniku [dewelopera IoT Hub](iot-hub-devguide-messaging.md).

Na końcu tego samouczka uruchamiasz dwie aplikacje konsolowe języka Python:

* **SimulatedDevice.py**, zmodyfikowana wersja aplikacji utworzona w wysyłanie danych [telemetrycznych z urządzenia do centrum IoT](quickstart-send-telemetry-python.md), która łączy się z Centrum IoT Hub i odbiera komunikaty z chmury do urządzenia.

* **SendCloudToDeviceMessage.py**, który wysyła komunikat z chmury do urządzenia do aplikacji symulowanego urządzenia za pomocą IoT Hub, a następnie otrzymuje potwierdzenie dostarczenia.

> [!NOTE]
> IoT Hub obsługuje zestaw SDK dla wielu platform i języków urządzeń (w tym C, Java i JavaScript) za pomocą zestawów SDK urządzeń usługi Azure IoT. Aby uzyskać instrukcje krok po kroku dotyczące sposobu łączenia urządzenia z tym kodem samouczka i ogólnie do usługi Azure IoT Hub, zobacz [Centrum deweloperów Azure IoT](https://www.azure.com/develop/iot).
>

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* [Python 2. x lub 3. x](https://www.python.org/downloads/). Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*](https://pip.pypa.io/en/stable/installing/).

* Zainstaluj [Pakiet redystrybucyjny języka Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.

* Aktywne konto platformy Azure. (Jeśli nie masz konta, możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/) w zaledwie kilka minut).

> [!NOTE]
> Pakiety *pip* dla składników `azure-iothub-service-client` i `azure-iothub-device-client` są obecnie dostępne tylko w przypadku systemu operacyjnego Windows. W przypadku systemu Linux/Mac OS zapoznaj się z sekcjami dotyczącymi systemu Linux i Mac OS w sekcji [przygotowanie środowiska programistycznego dla języka Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
>

## <a name="receive-messages-in-the-simulated-device-app"></a>Odbieraj komunikaty w aplikacji symulowanego urządzenia

W tej sekcji utworzysz aplikację konsolową języka Python w celu symulowania urządzenia i otrzymywania komunikatów z chmury do urządzeń z Centrum IoT.

1. Za pomocą edytora tekstów Utwórz plik **SimulatedDevice.py** .

2. Dodaj następujące `import` instrukcje i zmienne na początku pliku **SimulatedDevice.py** :

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. Dodaj następujący kod do pliku **SimulatedDevice.py** . Zastąp wartość symbolu zastępczego "{deviceConnectionString}" parametrami połączenia urządzenia dla urządzenia utworzonego w polu Wyślij dane telemetryczne [z urządzenia do](quickstart-send-telemetry-python.md) przewodnika Szybki Start dotyczącego usługi IoT Hub:

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Dodaj następującą funkcję, aby wydrukować odebrane komunikaty do konsoli:

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. Dodaj następujący kod, aby zainicjować klienta i poczekać na odebranie komunikatu z chmury do urządzenia:

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. Dodaj następującą funkcję główną:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

7. Zapisz i zamknij plik **SimulatedDevice.py** .

## <a name="get-the-iot-hub-connection-string"></a>Pobierz parametry połączenia usługi IoT Hub

W tym artykule opisano tworzenie usługi zaplecza do wysyłania komunikatów z chmury do urządzenia za pośrednictwem Centrum IoT utworzonego w artykule [wysyłanie danych telemetrycznych z urządzenia do centrum IoT Hub](quickstart-send-telemetry-python.md). Aby można było wysyłać komunikaty z chmury do urządzenia, usługa wymaga uprawnień do **połączenia z usługą** . Domyślnie każdy IoT Hub jest tworzony przy użyciu zasad dostępu współdzielonego o nazwie **Usługa** , która przyznaje to uprawnienie.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Wysyłanie komunikatu z chmury do urządzenia

W tej sekcji utworzysz aplikację konsolową w języku Python, która wysyła komunikaty z chmury do urządzenia do aplikacji symulowanego urządzenia. Potrzebujesz identyfikatora urządzenia dodanego w polu Wyślij telemetrię [z urządzenia do](quickstart-send-telemetry-python.md) przewodnika Szybki Start dotyczącego usługi IoT Hub. Potrzebne są również parametry połączenia usługi IoT Hub, które zostały wcześniej skopiowane w polu [Pobierz parametry połączenia usługi IoT Hub](#get-the-iot-hub-connection-string).

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
