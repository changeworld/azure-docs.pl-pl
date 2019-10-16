---
title: 'Samouczek: korzystanie z kolejek Azure Service Bus w języku Python'
description: Dowiedz się, jak używać kolejek Azure Service Bus w języku Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: 3561d7e3569b31698fbbc5f8cf2518d9fe1fc398
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331123"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Jak używać kolejek Service Bus przy użyciu języka Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W ramach tego samouczka nauczysz się tworzyć aplikacje języka Python do wysyłania komunikatów do i odbierania komunikatów z kolejki Service Bus. 

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj czynności opisane w [Azure Portal Użyj, aby utworzyć artykuł kolejki Service Bus](service-bus-quickstart-portal.md) .
    1. Zapoznaj się z krótkim omówieniem **kolejek**Service Bus. 
    2. Utwórz **przestrzeń nazw**Service Bus. 
    3. Pobierz **Parametry połączenia**. 

        > [!NOTE]
        > W tym samouczku utworzysz **kolejkę** w przestrzeni nazw Service Bus przy użyciu języka Python. 
1. Zainstaluj Język Python lub [pakiet Azure Service Bus Python][Python Azure Service Bus package], zapoznaj się z [Podręcznikiem instalacji języka Python](/azure/python/python-sdk-azure-install). Zapoznaj się z pełną dokumentacją Service Bus Python SDK [tutaj](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Tworzenie kolejki
Obiekt **ServiceBusClient** umożliwia korzystanie z kolejek. Dodaj następujący kod w górnej części każdego pliku języka Python, do którego chcesz programowo uzyskać dostęp Service Bus:

```python
from azure.servicebus import ServiceBusClient
```

Poniższy kod tworzy obiekt **ServiceBusClient** . Zastąp `<CONNECTION STRING>` elementem ConnectionString ServiceBus.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Wartości nazwy i wartości klucza sygnatury dostępu współdzielonego można znaleźć w [Azure Portal][Azure portal] informacji o połączeniu lub w okienku **Właściwości** programu Visual Studio podczas wybierania przestrzeni nazw Service Bus w Eksplorator serwera (jak pokazano w poprzedniej sekcji).

```python
sb_client.create_queue("taskqueue")
```

Metoda `create_queue` obsługuje również dodatkowe opcje, które umożliwiają przesłonięcie domyślnych ustawień kolejki, takich jak czas wygaśnięcia komunikatu (TTL) lub maksymalny rozmiar kolejki. Poniższy przykład ustawia maksymalny rozmiar kolejki na 5 GB, a wartość czasu wygaśnięcia na 1 minutę:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Aby uzyskać więcej informacji, zobacz [Azure Service Bus Dokumentacja języka Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać komunikat do kolejki Service Bus, aplikacja wywołuje metodę `send` w obiekcie `ServiceBusClient`.

W poniższym przykładzie pokazano, jak wysłać wiadomość testową do kolejki o nazwie `taskqueue` przy użyciu `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały][Service Bus quotas].

Aby uzyskać więcej informacji, zobacz [Azure Service Bus Dokumentacja języka Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Komunikaty są odbierane z kolejki przy użyciu metody `get_receiver` w obiekcie `ServiceBusService`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Aby uzyskać więcej informacji, zobacz [Azure Service Bus Dokumentacja języka Python](/python/api/overview/azure/servicebus?view=azure-python).


Komunikaty są usuwane z kolejki, ponieważ są odczytywane, gdy parametr `peek_lock` ma wartość **Fałsz**. Można odczytać (wgląd) i zablokować komunikat bez usuwania go z kolejki, ustawiając parametr `peek_lock` na **true**.

Zachowanie odczytu i usunięcia komunikatu w ramach operacji odbierania jest najprostszym modelem i najlepiej sprawdza się w scenariuszach, w których aplikacja może tolerować nieprzetwarzanie komunikatu w przypadku awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, a następnie aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięta wiadomość, która była używana przed awarią.

Jeśli parametr `peek_lock` ma **wartość true**, odbieranie staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niegodny w przyszłości), kończy drugi etap procesu odbierania przez wywołanie metody **delete** dla obiektu **Message** . Metoda **delete** oznaczy komunikat jako używany i usuń go z kolejki.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać metodę **Unlock** dla obiektu **Message** . Spowoduje to Service Bus odblokowanie komunikatu w kolejce i udostępnienie go do odebrania przez tę samą aplikację wykorzystującą lub przez inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli aplikacja ulega awarii), Service Bus automatycznie zamknie komunikat i ustawi go dostępne do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed wywołaniem metody **delete** komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane **co najmniej raz, co**oznacza, że każdy komunikat zostanie przetworzony co najmniej jeden raz, ale w pewnych sytuacjach ten sam komunikat może zostać ponownie dostarczony. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu właściwości **MessageId** komunikatu, która pozostaje niezmienna między kolejnymi próbami dostarczenia.

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy Service Busych kolejek, zobacz te artykuły, aby dowiedzieć się więcej.

* [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

