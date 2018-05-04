---
title: Routing komunikatów z Centrum IoT Azure (Python) | Dokumentacja firmy Microsoft
description: Jak komunikaty do przetwarzania komunikatów urządzenia do chmury Azure IoT Hub przy użyciu reguł routingu oraz niestandardowe punkty końcowe wysłania wiadomości do innych usług zaplecza.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 5803d237a946a91536eb662a28416926f6f690fb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>Routing komunikatów z Centrum IoT (Python)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

W tym samouczku opiera się na [Rozpoczynanie pracy z Centrum IoT] samouczka.  Samouczek:

* Pokazuje, jak używać reguł routingu do wysyłania wiadomości urządzenia do chmury w sposób łatwy, na podstawie konfiguracji.
* Przedstawiono sposób izolować interakcyjne komunikaty, które wymagają natychmiastowego działania z zaplecza rozwiązania dla dalszego przetwarzania.  Na przykład urządzenie może wysłać komunikat alarmu, które wyzwala Wstawianie biletu do systemu CRM.  Z kolei wiadomości punktu danych, takich jak telemetrii temperatury źródła danych do aparatu analizy.

Na końcu tego samouczka możesz uruchomić trzech Python aplikacji konsoli:

* **SimulatedDevice.py**, zmodyfikowanej wersji aplikacji utworzonych w [Rozpoczynanie pracy z Centrum IoT] samouczek, wysyła komunikaty urządzenia do chmury punktu danych, co drugi i interaktywne wiadomości urządzenia do chmury dla losowych Interwał. Ta aplikacja korzysta z protokołu MQTT do komunikowania się z Centrum IoT.
* **ReadCriticalQueue.py** usuwania kolejki krytycznych wiadomości z kolejki usługi Service Bus dołączony do Centrum IoT.

> [!NOTE]
> Centrum IoT obsługuje zestawu SDK dla wielu platform urządzeń i języków, w tym C, Java i JavaScript. Aby uzyskać instrukcje dotyczące sposobu Zamień w tym samouczku urządzenie fizyczne urządzenia oraz sposób podłączania urządzeń do Centrum IoT, zobacz [Azure IoT Developer Center].

Do wykonania kroków tego samouczka niezbędne są następujące elementy:

* Pełną wersję pracy [Rozpoczynanie pracy z Centrum IoT] samouczka.
* [Środowisko Python 2.x lub 3.x][lnk-python-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Po wyświetleniu monitu podczas instalacji upewnij się, że język Python został dodany do zmiennej środowiskowej specyficznej dla platformy. Jeśli używasz środowiska Python 2.x, może być konieczne [zainstalowanie lub uaktualnienie systemu zarządzania pakietami języka Python — *pip*][lnk-install-pip].
* [Pakiet redystrybucyjny języka Visual C++][lnk-visual-c-redist] (jeśli używasz systemu operacyjnego Windows) umożliwiający korzystanie z natywnych bibliotek DLL języka Python.
* [Środowisko Node.js 4.0 lub nowsze][lnk-node-download]. Upewnij się, że używasz 32-bitowej lub 64-bitowej instalacji zgodnie z wymaganiami konfiguracji. Jest to niezbędne do zainstalowania [narzędzia IoT Hub Explorer][lnk-iot-hub-explorer].
* Aktywne konto platformy Azure. (Jeśli go nie masz, możesz utworzyć [bezpłatne konto próbne][lnk-free-trial] w zaledwie kilka minut).

Zalecamy również informacje o [usługi Azure Storage] i [Azure Service Bus].


## <a name="send-interactive-messages-from-a-device-app"></a>Wysyłanie wiadomości interaktywne z aplikacji przez urządzenia
W tej sekcji możesz zmodyfikować aplikację urządzenia, utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczek czasami wysłać wiadomości, które wymagają natychmiastowego przetwarzania.

1. Użyj edytora tekstów, aby otworzyć **SimulatedDevice.py** pliku. Ten plik zawiera kod **SimulatedDevice** aplikacji utworzony w [Rozpoczynanie pracy z Centrum IoT] samouczka.

2. Zastąp **iothub_client_telemetry_sample_run** funkcja następującym kodem:

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    Ta metoda losowo dodaje właściwość `"level": "critical"` i `"level": "storage"` dla komunikatów wysyłanych przez urządzenia, która symuluje komunikat, który wymaga natychmiastowego działania przez zaplecza aplikacji lub jeden, który musi być trwale przechowywane. Aplikacja obsługuje routing wiadomości opartych na treść komunikatu.
   
   > [!NOTE]
   > Można użyć właściwości wiadomości do przesyłania wiadomości dla różnych scenariuszy, w tym chłodni ścieżką podczas przetwarzania, oprócz tu przykładzie aktywnej ścieżki.

1. Zapisz i zamknij plik **SimulatedDevice.py**.

    > [!NOTE]
    > Dla uproszczenia w tym samouczku nie implementuje wszystkie zasady ponawiania. W kodzie produkcyjnym, należy zaimplementować zasady ponawiania wykładniczego wycofywania, zgodnie z sugestią podaną w artykuł w witrynie MSDN np. [obsługi błędów przejściowych].


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>Dodaj do IoT hub i tras wiadomości do nich kolejek

W tej sekcji tworzyć kolejki usługi Service Bus oraz konto magazynu, podłącz je do Centrum IoT i konfigurowanie Centrum IoT do wysyłania wiadomości do kolejki na podstawie obecności właściwości wiadomości i wszystkie komunikaty do konta magazynu. Aby uzyskać więcej informacji o sposobie przetwarzania komunikatów z kolejek usługi Service Bus, zobacz [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-node] i zarządzania magazynem, zobacz [Rozpoczynanie pracy z magazynem Azure][usługi Azure Storage].

1. Tworzenie kolejki usługi Service Bus, zgodnie z opisem w [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-node]. Zanotuj nazwę przestrzeni nazw i kolejki.

    > [!NOTE]
    > Tematy dotyczące używany jako punkty końcowe Centrum IoT nie może mieć i kolejek usługi Service Bus **sesji** lub **wykrywania duplikatów** włączone. Jeśli dowolna z tych opcji są włączone, punkt końcowy jest wyświetlany jako **informujący** w portalu Azure.

1. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.

    ![Punkty końcowe Centrum IoT][30]

1. W **punkty końcowe** bloku, kliknij przycisk **Dodaj** u góry, aby dodać kolejki do Centrum IoT. Nazwa punktu końcowego **CriticalQueue** i umożliwia wybranie listach rozwijanych **kolejki usługi Service Bus**, przestrzeń nazw magistrali usług, w której znajduje się kolejki i nazwy kolejki. Gdy wszystko będzie gotowe, kliknij przycisk **OK** u dołu.  

    ![Dodawanie punktu końcowego][31]

1. Teraz kliknij **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. 

    ![Dodawanie trasy][34]

    Wprowadź nazwę i wybierz **komunikaty** jako źródło danych. Wybierz **CriticalQueue** jako punktu końcowego niestandardowych jako routingu reguły punktu końcowego, a następnie wprowadź `level="critical"` jako ciąg zapytania. Kliknij przycisk **zapisać** u dołu.

    ![Szczegóły trasy][32]

    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. To ustawienie jest domyślną konfigurację Centrum IoT.

    ![Trasy rezerwowej][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Opcjonalnie) Odczyt z kolejki punktu końcowego

W tej sekcji utworzysz aplikację konsoli języka Python, która odczytuje krytycznych wiadomości z usługi Service Bus IoT. Dalsze informacje wyświetlane w [Rozpoczynanie pracy z kolejkami][lnk-sb-queues-node]. 

1. Otwórz nowy wiersz polecenia i zainstaluj zestaw SDK urządzenia usługi Azure IoT Hub dla języka Python w następujący sposób. Po ukończeniu instalacji zamknij wiersz polecenia.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > Dla problemy z instalacją **magistrali usług azure** pakietu lub uzyskać dodatkowe opcje instalacji [pakiet języka Python magistrali usług azure][lnk-python-service-bus].

1. Utwórz plik o nazwie **ReadCriticalQueue.py**. Otwórz ten plik w edytorze języka Python lub w wybranym środowisku IDE (na przykład w środowisku IDLE).

1. Dodaj następujący kod, aby zaimportować moduł wymagany z urządzenia zestawu SDK:

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. Dodaj następujący kod i zastąp symbole zastępcze dane połączenia dla Twojej usługi service bus:

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. Dodaj następujący kod, aby połączyć się z i do odczytu magistrali usług: 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. Na koniec dodaj funkcję main. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. Zapisz i Zamknij **ReadCriticalQueue.py** pliku. Teraz można uruchomić aplikacje.


## <a name="run-the-applications"></a>Uruchamianie aplikacji

Teraz wszystko jest gotowe do uruchomienia aplikacji.

1. Otwórz wiersz polecenia i zainstaluj narzędzie IoT Hub Explorer. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. Uruchom następujące polecenie w wierszu polecenia, aby rozpocząć monitorowanie z urządzenia komunikatów wysyłanych z urządzenia do chmury. Użyj parametrów połączenia z Twojego centrum IoT w miejsce symbolu zastępczego po `--login`.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. Otwórz nowy wiersz polecenia i przejdź do katalogu zawierającego plik **SimulatedDevice.py**.

1. Uruchom plik **SimulatedDevice.py**, który okresowo wysyła dane telemetryczne do Twojego centrum IoT. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. Aby uruchomić **ReadCriticalQueue** aplikacji, w wierszu polecenia lub powłoki, przejdź do **ReadCriticalQueue.py** pliku i uruchom następujące polecenie:

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. W wierszu polecenia z uruchomionym w poprzedniej sekcji narzędziem IoT Hub Explorer obserwuj komunikaty z urządzenia. Obserwować `critical` w wiadomości **ReadCriticalQueue** aplikacji.

    ![Komunikaty środowiska Python przesyłane z urządzenia do chmury][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Opcjonalnie) Dodaj kontener magazynu do IoT hub i tras wiadomości do niego

W tej sekcji Utwórz konto magazynu, połącz go z Centrum IoT i skonfigurować do wysyłania komunikatów do konta, na podstawie obecności właściwość w komunikacie Centrum IoT. Aby uzyskać więcej informacji o sposobie zarządzania magazynu, zobacz [Rozpoczynanie pracy z magazynem Azure][usługi Azure Storage].

 > [!NOTE]
   > Centrum IoT kont utworzonych w _F1 bezpłatna_ warstwy są ograniczone do jednego **punktu końcowego**. Jeśli nie masz tylko jeden **punktu końcowego**, może skonfigurować **StorageContainer** oprócz **CriticalQueue** i uruchom oba simulatneously.

1. Utwórz konto magazynu, zgodnie z opisem w [dokumentacji usługi Magazyn Azure][lnk-storage]. Zanotuj nazwę konta.

2. W portalu Azure Otwórz Centrum IoT i kliknij przycisk **punkty końcowe**.

3. W **punkty końcowe** bloku, wybierz opcję **CriticalQueue** punktu końcowego, a następnie kliknij przycisk **usunąć**. Kliknij przycisk **tak**, a następnie kliknij przycisk **Dodaj**. Nazwa punktu końcowego **StorageContainer** i umożliwia wybranie listach rozwijanych **kontenera magazynu Azure**i Utwórz **konta magazynu** i **magazynu kontener**.  Zanotuj nazwy.  Gdy wszystko będzie gotowe, kliknij przycisk **OK** u dołu. 

 > [!NOTE]
   > Centrum IoT kont utworzonych w _F1 bezpłatna_ warstwy są ograniczone do jednego **punktu końcowego**. Jeśli nie masz tylko jeden **punktu końcowego**, nie trzeba usunąć **CriticalQueue**.

4. Kliknij przycisk **tras** w Centrum IoT. Kliknij przycisk **Dodaj** w górnej części bloku, aby utworzyć regułę routingu kieruje komunikaty do kolejki właśnie został dodany. Wybierz **komunikaty** jako źródło danych. Wprowadź `level="storage"` jako warunek i wybierz polecenie **StorageContainer** jako punktu końcowego niestandardowych jako routingu końcowy reguły. Kliknij przycisk **zapisać** u dołu.  

    Upewnij się, że rezerwowy trasy ma ustawioną wartość **ON**. To ustawienie jest domyślną konfigurację Centrum IoT.

1. Upewnij się, że aplikacja poprzedniej **SimulatedDevice.py** nadal działa. 

1. W portalu Azure, przejdź do konta magazynu, w obszarze **usługa Blob**, kliknij przycisk **Przeglądaj obiekty BLOB...** .  Wybierz kontener użytkownika, przejdź do kliknij plik JSON i kliknij przycisk **Pobierz** do wyświetlania danych.


## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób niezawodny sposób wysyłania wiadomości urządzenia do chmury przy użyciu funkcji routing komunikatów z Centrum IoT.

Aby zapoznać się przykładem kompletnych rozwiązań end-to-end korzystających z Centrum IoT, zobacz [pakiet IoT Azure][lnk-suite].

Aby dowiedzieć się więcej na temat tworzenia rozwiązań z Centrum IoT, zobacz [Centrum IoT — przewodnik dewelopera].

Aby dowiedzieć się więcej na temat w Centrum IoT rozsyłania wiadomości, zobacz [wysyłania i odbierania wiadomości z Centrum IoT][lnk-devguide-messaging].

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[usługi Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Centrum IoT — przewodnik dewelopera]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Rozpoczynanie pracy z Centrum IoT]: iot-hub-python-getstarted.md
[Azure IoT Developer Center]: https://azure.microsoft.com/develop/iot

[obsługi błędów przejściowych]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus
