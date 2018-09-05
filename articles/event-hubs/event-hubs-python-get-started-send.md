---
title: Wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka Python | Dokumentacja firmy Microsoft
description: Rozpocznij wysyłanie zdarzeń do usługi Event Hubs przy użyciu języka Python
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703798"
---
# <a name="send-events-to-event-hubs-using-python"></a>Wysyłanie zdarzeń do usługi Event Hubs przy użyciu języka Python

Azure Event Hubs to system zarządzania zdarzeń o wysokim stopniu skalowalności, który może obsługiwać miliony zdarzeń na sekundę, dzięki czemu aplikacje mogą przetwarzać oraz analizować duże ilości danych wytworzonych przez podłączone urządzenia i innymi systemami. Po zebraniu danych do Centrum zdarzeń, może odbierać i obsługa zdarzeń z użyciem w trakcie obsługi lub funkcji przekazywania z innymi systemami analizy.

Aby dowiedzieć się więcej na temat usługi Event Hubs, zobacz [Przegląd usługi Event Hubs][Event Hubs overview].

W tym samouczku opisano sposób wysyłania zdarzeń do Centrum zdarzeń z aplikacji napisanych w języku Python. Aby odbierać zdarzenia, zobacz [do odpowiedniego artykułu Receive](event-hubs-python-get-started-receive.md).

Kod w tym samouczku pochodzi z [tych przykładów usługi GitHub](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), który można sprawdzić, aby wyświetlić pełną działającą aplikację, w tym instrukcje importowania i deklaracje zmiennych. Inne przykłady są dostępne w tym samym folderze w usłudze GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

- Python 3.4 lub nowsze.
- Istniejące usługi Event Hubs przestrzeni nazw i Centrum zdarzeń. Te jednostki można utworzyć, postępując zgodnie z instrukcjami wyświetlanymi w [w tym artykule](event-hubs-create.md). 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Zainstaluj pakiet języka Python

Aby zainstalować pakiet języka Python dla usługi Event Hubs, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Utwórz skrypt w języku Python do wysyłania zdarzeń

Następnie utwórz aplikację w języku Python, która wysyła zdarzenia do Centrum zdarzeń:

1. Otwórz ulubionym edytorze języka Python, takich jak [programu Visual Studio Code][Visual Studio Code].
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

## <a name="send-events"></a>Wysyłanie zdarzeń

Aby uruchomić skrypt, Otwórz okno wiersza polecenia, którego języka Python w ścieżce, a następnie uruchom następujące polecenie:

```bash
start python send.py
```
 
## <a name="next-steps"></a>Kolejne kroki

Skoro zdarzenia zostały wysłane do Centrum zdarzeń za pomocą języka Python, aby otrzymać zdarzenia zobacz [do odpowiedniego artykułu Receive](event-hubs-python-get-started-receive.md).

Odwiedź następujące strony, aby dowiedzieć się więcej o usłudze Event Hubs:

* [Przegląd usługi Event Hubs][Event Hubs overview]
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
