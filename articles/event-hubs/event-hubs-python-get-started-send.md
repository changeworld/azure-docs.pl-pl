---
title: 'Szybki Start: wysyłanie i odbieranie zdarzeń przy użyciu języka Python — Event Hubs platformy Azure'
description: 'Szybki Start: w tym przewodniku pokazano, jak tworzyć i uruchamiać skrypty języka Python, które wysyłają zdarzenia do i odbierają zdarzenia z usługi Azure Event Hubs.'
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/08/2020
ms.author: shvija
ms.openlocfilehash: c4fa9e6038f4007246552610f537825f9def92a8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75939954"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>Szybki Start: wysyłanie i odbieranie zdarzeń za pomocą Event Hubs przy użyciu języka Python

Azure Event Hubs to usługa do przesyłania strumieniowego danych Big Data i usługi pozyskiwania zdarzeń, które mogą odbierać i przetwarzać miliony zdarzeń na sekundę. Event Hubs może przetwarzać i przechowywać zdarzenia, dane lub Telemetria z rozproszonych oprogramowania i urządzeń. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby uzyskać więcej informacji na temat Event Hubs, zobacz [azure Event Hubs](event-hubs-about.md) i [funkcje i terminologia na platformie Azure Event Hubs](event-hubs-features.md).

Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji języka Python, które wysyłają zdarzenia do i odbierają zdarzenia z centrum zdarzeń. 

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji 1 zestawu SDK języka Python platformy Azure Event Hubs. Jeśli dopiero zaczynasz korzystać z usługi Azure Event Hubs, użyj wersji 5 zestawu SDK języka Python. Aby zapoznać się z przewodnikiem Szybki Start, który używa wersji 5 zestawu SDK języka Python, zobacz [ten artykuł](get-started-python-send-v2.md). Aby przeprowadzić migrację istniejącego kodu z wersji 1 do wersji 5, zapoznaj się z [przewodnikiem migracji](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


> [!NOTE]
> Zamiast korzystać z przewodnika Szybki Start można pobrać i uruchomić [przykładowe aplikacje](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) z usługi GitHub. Zastąp ciągi `EventHubConnectionString` i `EventHubName` wartościami z centrum zdarzeń. 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Aktywna przestrzeń nazw Event Hubs i centrum zdarzeń utworzone zgodnie z instrukcjami podanymi w [przewodniku szybki start: tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md). Zanotuj nazwy przestrzeni nazw i centrów zdarzeń do użycia w dalszej części tego przewodnika. 
- Nazwa klucza dostępu współdzielonego i wartość klucza podstawowego dla przestrzeni nazw Event Hubs. Pobierz nazwę i wartość klucza dostępu, postępując zgodnie z instrukcjami podanymi w polu [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Domyślna nazwa klucza dostępu to **RootManageSharedAccessKey**. Skopiuj nazwę klucza i wartość klucza podstawowego do użycia w dalszej części tego instruktażu. 
- Program Python 3,4 lub nowszy z zainstalowanym i zaktualizowanym `pip`.
- Pakiet języka Python dla Event Hubs. Aby zainstalować pakiet, Uruchom to polecenie w wierszu polecenia zawierającym Język Python w ścieżce: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Kod w tym przewodniku szybki start używa bieżącego stabilnej wersji 1.3.1 zestawu SDK Event Hubs. Przykładowy kod, który używa wersji zapoznawczej zestawu SDK, znajduje się w temacie [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Wysyłanie zdarzeń

Aby utworzyć aplikację w języku Python, która wysyła zdarzenia do centrum zdarzeń:

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz nowy plik o nazwie *send.py*. Ten skrypt wysyła 100 zdarzeń do Centrum zdarzeń.
3. Wklej następujący kod do *send.py*, zastępując Event Hubs \<przestrzeni nazw > \<eventhub >, \<AccessKeyName > i \<wartość klucza podstawowego > z wartościami: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Zapisz plik. 

Aby uruchomić skrypt, w katalogu, w którym zapisano *send.py*, uruchom następujące polecenie:

```cmd
start python send.py
```

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="receive-events"></a>Odbieranie zdarzeń

Aby utworzyć aplikację w języku Python, która odbiera zdarzenia z centrum zdarzeń:

1. W edytorze języka Python Utwórz plik o nazwie *recv.py*.
2. Wklej następujący kod do *recv.py*, zastępując Event Hubs \<przestrzeni nazw > \<eventhub >, \<AccessKeyName > i \<wartość klucza podstawowego > z wartościami: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Zapisz plik.

Aby uruchomić skrypt, w katalogu, w którym zapisano *recv.py*, uruchom następujące polecenie:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat Event Hubs, zobacz następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)

