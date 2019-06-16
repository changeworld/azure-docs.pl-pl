---
title: Wysyłanie lub odbieranie zdarzeń za pomocą języka Python — Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera wskazówki dotyczące tworzenia aplikacji w języku Python, która wysyła zdarzenia do usługi Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 6a8f0ddcfe6de904219059c6e761ead4c004732d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60461454"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Wysyłanie zdarzeń do lub odbieranie zdarzeń z usługi Event Hubs przy użyciu języka Python

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób tworzenia aplikacji Python do wysyłania zdarzeń do lub odbieranie zdarzeń z Centrum zdarzeń. 

> [!NOTE]
> Ten przewodnik Szybki start możesz pobrać jako przykład z witryny [GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), zastąpić ciągi `EventHubConnectionString` i `EventHubName` wartościami swojego centrum zdarzeń, a następnie uruchomić go. Alternatywnie możesz utworzyć własne rozwiązanie, wykonując kroki opisane w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- Python 3.4 lub nowsze.
- Użyj [witryny Azure portal](https://portal.azure.com) do utworzenia przestrzeni nazw typu Event Hubs i uzyskania poświadczeń zarządzania wymaganych przez aplikację do komunikacji z Centrum zdarzeń. Aby utworzyć obszar nazw i Centrum zdarzeń, wykonaj procedurę opisaną w [w tym artykule](event-hubs-create.md). Następnie Pobierz wartość klucza dostępu Centrum zdarzeń, postępując zgodnie z instrukcjami opisanymi w artykule: [Pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Klucz dostępu został użyty w kodzie, napisany w dalszej części tego samouczka. Jest domyślna nazwa klucza: **RootManageSharedAccessKey**.

## <a name="install-python-package"></a>Zainstaluj pakiet języka Python

Aby zainstalować pakiet języka Python dla usługi Event Hubs, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie: 

```bash
pip install azure-eventhub
```

## <a name="send-events"></a>Wysyłanie zdarzeń

### <a name="create-a-python-script-to-send-events"></a>Utwórz skrypt w języku Python do wysyłania zdarzeń

Następnie utwórz aplikację w języku Python, która wysyła zdarzenia do Centrum zdarzeń:

1. Otwórz ulubionym edytorze języka Python, takich jak [programu Visual Studio Code](https://code.visualstudio.com/)
2. Tworzenie skryptu o nazwie **send.py**. Ten skrypt wysyła 100 zdarzeń do Centrum zdarzeń.
3. Wklej następujący kod do send.py, zastępując wartości klucza, użytkownika i adres wartościami uzyskanymi z witryny Azure portal w poprzedniej sekcji: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

### <a name="run-application-to-send-events"></a>Uruchamianie aplikacji w celu wysyłania zdarzeń

Aby uruchomić skrypt, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie:

```bash
start python send.py
```

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="receive-events"></a>Odbieranie zdarzeń

### <a name="create-a-python-script-to-receive-events"></a>Utwórz skrypt w języku Python do odbierania zdarzeń

Następnie utwórz aplikację w języku Python, który odbiera zdarzenia z Centrum zdarzeń:

1. Otwórz ulubionym edytorze języka Python, takich jak [programu Visual Studio Code](https://code.visualstudio.com/)
2. Tworzenie skryptu o nazwie **recv.py**.
3. Wklej następujący kod do recv.py, zastępując wartości klucza, użytkownika i adres wartościami uzyskanymi z witryny Azure portal w poprzedniej sekcji: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
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

### <a name="receive-events"></a>Odbieranie zdarzeń

Aby uruchomić skrypt, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Kolejne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologią dotyczącą usługi Azure Event Hubs](event-hubs-features.md)
- [Event Hubs — często zadawane pytania](event-hubs-faq.md)

