---
title: Wysyłanie lub odbieranie zdarzeń z usługi Azure Event Hubs przy użyciu języka Python (stary)
description: W tym przewodniku pokazano, jak tworzyć i uruchamiać skrypty języka Python, które wysyłają zdarzenia do lub odbierają zdarzenia z usługi Azure Event Hubs przy użyciu starego pakietu azure eventhub w wersji 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162603"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Szybki start: wysyłanie i odbieranie zdarzeń za pomocą centrum zdarzeń przy użyciu języka Python (usługa Azure eventhub w wersji 1)
Ten przewodnik Szybki start pokazuje, jak wysyłać zdarzenia i odbierać zdarzenia z centrum zdarzeń przy użyciu pakietu Języka Python **w usłudze azure eventhub w wersji 1.** 

> [!WARNING]
> Ten przewodnik Szybki start używa starego pakietu azure eventhub w wersji 1. Aby uzyskać przewodnik Szybki start, który używa **najnowszej wersji 5** pakietu, zobacz [Wysyłanie i odbieranie zdarzeń przy użyciu usługi azure-eventhub w wersji 5](get-started-python-send-v2.md). Aby przenieść aplikację z używania starego pakietu do nowego, zobacz [Przewodnik do migracji z usługi azure eventhub w wersji 1 do wersji 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli jesteś nowy w usłudze Azure Event Hubs, zobacz [Centrum zdarzeń omówienie](event-hubs-about.md) przed rozpoczęciem tego przewodnika Szybki start. 

Do wykonania kroków tego przewodnika Szybki start niezbędne jest spełnienie następujących wymagań wstępnych:

- **Subskrypcja platformy Microsoft Azure**. Aby korzystać z usług platformy Azure, w tym usługi Azure Event Hubs, potrzebujesz subskrypcji.  Jeśli nie masz istniejącego konta platformy Azure, możesz zarejestrować się w celu [uzyskania bezpłatnej wersji próbnej](https://azure.microsoft.com/free/) lub skorzystać z korzyści dla subskrybenta MSDN podczas [tworzenia konta.](https://azure.microsoft.com)
- Python 3.4 lub `pip` nowszy, z zainstalowanym i zaktualizowanym.
- Pakiet Języka Python dla centrów zdarzeń. Aby zainstalować pakiet, uruchom to polecenie w wierszu polecenia, w który ma Pythona w swojej ścieżce: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Utwórz obszar nazw Centrów zdarzeń i centrum zdarzeń**. Pierwszym krokiem jest użycie [witryny Azure Portal](https://portal.azure.com) do utworzenia obszaru nazw typu Event Hubs i uzyskania poświadczeń zarządzania, których aplikacja potrzebuje do komunikowania się z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Następnie uzyskaj wartość klucza dostępu dla Centrum zdarzeń, wykonując instrukcje z artykułu: [Pobierz parametry połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Klucz dostępu w kodzie, który piszesz w dalszej części tego przewodnika Szybki start. Domyślna nazwa klucza to: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Wysyłanie zdarzeń

Aby utworzyć aplikację języka Python, która wysyła zdarzenia do centrum zdarzeń:

> [!NOTE]
> Zamiast pracować nad przewodnikiem Szybki start, możesz pobrać i uruchomić [przykładowe aplikacje](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) z usługi GitHub. Zastąp `EventHubConnectionString` i `EventHubName` ciągi wartości centrum zdarzeń.

1. Otwórz ulubiony edytor Języka Python, na przykład [Visual Studio Code](https://code.visualstudio.com/)
2. Utwórz nowy plik o nazwie *send.py*. Ten skrypt wysyła 100 zdarzeń do centrum zdarzeń.
3. Wklej następujący kod do *send.py*, zastępując> \<obszaru nazw Centrum \<zdarzeń,> eventhub, \<> AccessKeyName i \<wartość klucza podstawowego> wartościami: 
   
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

Aby uruchomić skrypt, z katalogu, w którym został zapisany *send.py*, uruchom to polecenie:

```cmd
start python send.py
```

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="receive-events"></a>Odbieranie zdarzeń

Aby utworzyć aplikację języka Python, która odbiera zdarzenia z centrum zdarzeń:

1. W edytorze Python utwórz plik o nazwie *recv.py*.
2. Wklej następujący kod do *recv.py*, zastępując \<> obszaru nazw \<Centrum zdarzeń,> \<eventhub,> AccessKeyName \<i wartość klucza podstawowego> wartościami: 
   
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

Aby uruchomić skrypt, z katalogu, w którym został zapisany *recv.py*, uruchom to polecenie:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat Centrum zdarzeń, zobacz następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia w usłudze Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)

