---
title: Wysyłanie i odbieranie zdarzeń przy użyciu języka Python — Event Hubs platformy Azure
description: W tym instruktażu przedstawiono sposób tworzenia i uruchamiania skryptów języka Python, które wysyłają zdarzenia do i odbierają zdarzenia z usługi Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428886"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>Wysyłanie i odbieranie zdarzeń za pomocą Event Hubs przy użyciu języka Python

Azure Event Hubs to usługa do przesyłania strumieniowego danych Big Data i usługi pozyskiwania zdarzeń, które mogą odbierać i przetwarzać miliony zdarzeń na sekundę. Event Hubs może przetwarzać i przechowywać zdarzenia, dane lub Telemetria z rozproszonych oprogramowania i urządzeń. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby uzyskać więcej informacji na temat Event Hubs, zobacz [azure Event Hubs](event-hubs-about.md) i [funkcje i terminologia na platformie Azure Event Hubs](event-hubs-features.md).

Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji języka Python, które wysyłają zdarzenia do i odbierają zdarzenia z centrum zdarzeń. 

> [!NOTE]
> Zamiast korzystać z przewodnika Szybki Start można pobrać i uruchomić [przykładowe aplikacje](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) z usługi GitHub. Zastąp ciągi `EventHubConnectionString` i `EventHubName` wartościami z centrum zdarzeń. 

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Aktywna przestrzeń nazw Event Hubs i centrum zdarzeń utworzone zgodnie z instrukcjami podanymi w [przewodniku szybki start: tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md). Zanotuj nazwy przestrzeni nazw i centrów zdarzeń do użycia w dalszej części tego przewodnika. 
- Nazwa klucza dostępu współdzielonego i wartość klucza podstawowego dla przestrzeni nazw Event Hubs. Pobierz nazwę i wartość klucza dostępu, postępując zgodnie z instrukcjami podanymi w polu [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Domyślna nazwa klucza dostępu to **RootManageSharedAccessKey**. Skopiuj nazwę klucza i wartość klucza podstawowego do użycia w dalszej części tego instruktażu. 
- Środowisko Python 3,4 lub nowsze z zainstalowaną i zaktualizowaną `pip`.
- Pakiet języka Python dla Event Hubs. Aby zainstalować pakiet, Uruchom to polecenie w wierszu polecenia zawierającym Język Python w ścieżce: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Kod w tym przewodniku szybki start używa bieżącego stabilnej wersji 1.3.1 zestawu SDK Event Hubs. Przykładowy kod korzystający z wersji zapoznawczej zestawu SDK znajduje się w temacie [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Wysyłanie zdarzeń

Aby utworzyć aplikację w języku Python, która wysyła zdarzenia do centrum zdarzeń:

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz nowy plik o nazwie *send.py*. Ten skrypt wysyła zdarzenia 100 do centrum zdarzeń.
3. Wklej następujący kod do *send.py*, zastępując Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > i \<primary wartości klucza > z wartościami: 
   
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
2. Wklej następujący kod do *recv.py*, zastępując Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > i \<primary wartości klucza > z wartościami: 
   
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

