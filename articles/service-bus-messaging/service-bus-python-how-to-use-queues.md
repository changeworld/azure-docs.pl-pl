---
title: 'Szybki start: używanie kolejek usługi Azure Service Bus w pythonie'
description: W tym artykule pokazano, jak używać języka Python do tworzenia, wysyłania wiadomości do i odbierania wiadomości z kolejek usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.custom: seo-python-october2019
ms.openlocfilehash: acb0b0e84804ecf6025e05590133dee9b0d54c48
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478642"
---
# <a name="quickstart-use-azure-service-bus-queues-with-python"></a>Szybki start: używanie kolejek usługi Azure Service Bus w pythonie

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym artykule pokazano, jak używać języka Python do tworzenia, wysyłania wiadomości do i odbierania wiadomości z kolejek usługi Azure Service Bus. 

Aby uzyskać więcej informacji na temat bibliotek usługi Azure Service Bus języka Python, zobacz [Biblioteki usługi Service Bus dla języka Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Możesz aktywować [korzyści dla subskrybenta programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub założyć [bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Obszar nazw usługi Service Bus utworzony przez wykonując kroki opisane w [przewodniku Szybki start: Tworzenie tematu i subskrypcji usługi Service Bus za pomocą portalu Azure.](service-bus-quickstart-topics-subscriptions-portal.md) Skopiuj podstawowy ciąg połączenia z ekranu **Zasady dostępu udostępnionego,** aby użyć go w dalszej części tego artykułu. 
- Python 3.4x lub wyższy, z zainstalowanym pakietem [usługi Azure Service Bus języka Python.][Python Azure Service Bus package] Aby uzyskać więcej informacji, zobacz [Podręcznik instalacji języka Python](/azure/developer/python/azure-sdk-install). 

## <a name="create-a-queue"></a>Tworzenie kolejki

**Obiekt ServiceBusClient** umożliwia pracę z kolejkami. Aby programowo uzyskać dostęp do usługi Service Bus, dodaj następujący wiersz w górnej części pliku języka Python:

```python
from azure.servicebus import ServiceBusClient
```

Dodaj następujący kod, aby utworzyć **obiekt ServiceBusClient.** Zamień `<connectionstring>` na wartość ciągu połączenia podstawowego usługi Service Bus. Tę wartość można znaleźć w obszarze **Zasady dostępu współdzielonego** w obszarze nazw usługi Service Bus w [witrynie Azure portal][Azure portal].

```python
sb_client = ServiceBusClient.from_connection_string('<connectionstring>')
```

Poniższy kod używa `create_queue` metody **ServiceBusClient** do utworzenia `taskqueue` kolejki o nazwie z ustawieniami domyślnymi:

```python
sb_client.create_queue("taskqueue")
```

Można użyć opcji, aby zastąpić domyślne ustawienia kolejki, takie jak czas wygaśnięcia wiadomości (TTL) lub maksymalny rozmiar tematu. Poniższy kod tworzy `taskqueue` kolejkę wywoływaną z maksymalnym rozmiarem kolejki 5 GB i wartością TTL 1 minuty:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki

Aby wysłać wiadomość do kolejki usługi Service `send` Bus, aplikacja wywołuje metodę na **ServiceBusClient** obiektu. Poniższy przykład kodu tworzy klienta kolejki i `taskqueue` wysyła komunikat testowy do kolejki. Zamień `<connectionstring>` na wartość ciągu połączenia podstawowego usługi Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

### <a name="message-size-limits-and-quotas"></a>Limity rozmiaru wiadomości i przydziały

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma limitu liczby wiadomości, które może pomieścić kolejka, ale istnieje ograniczenie całkowitego rozmiaru wiadomości, które przechowuje kolejka. Rozmiar kolejki można zdefiniować w czasie tworzenia, z górnym limitem 5 GB. 

Aby uzyskać więcej informacji na temat przydziałów, zobacz [Przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Odbieranie wiadomości z kolejki

Klient kolejki odbiera wiadomości z kolejki `get_receiver` przy użyciu metody na **ServiceBusClient** obiektu. Poniższy przykład kodu tworzy klienta kolejki i `taskqueue` odbiera komunikat z kolejki. Zamień `<connectionstring>` na wartość ciągu połączenia podstawowego usługi Service Bus. 

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string("<connectionstring>", "taskqueue")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

### <a name="use-the-peek_lock-parameter"></a>Użyj parametru peek_lock

Opcjonalny `peek_lock` parametr `get_receiver` określa, czy usługa Service Bus usuwa wiadomości z kolejki podczas ich odczytywania. Domyślnym trybem odbierania wiadomości jest `peek_lock` *PeekLock*lub ustawiona na **True**, która odczytuje (zagląda) i blokuje wiadomości bez usuwania ich z kolejki. Każda wiadomość musi być jawnie wypełniona, aby usunąć ją z kolejki.

Aby usunąć wiadomości z kolejki podczas ich odczytywania, można ustawić `peek_lock` parametr `get_receiver` **false**. Usuwanie wiadomości w ramach operacji odbierania jest najprostszym modelem, ale działa tylko wtedy, gdy aplikacja może tolerować brakujące komunikaty, jeśli wystąpi błąd. Aby zrozumieć to zachowanie, należy wziąć pod uwagę scenariusz, w którym konsument wystawia żądanie odbierania, a następnie ulega awarii przed przetworzeniem. Jeśli wiadomość została usunięta po odebraniu, gdy aplikacja zostanie ponownie uruchomiona i zacznie ponownie zużywać wiadomości, nie odebrano wiadomości odebranej przed awarią.

Jeśli aplikacja nie toleruje nieodebranych wiadomości, receive jest operacją dwuetapową. PeekLock znajduje następną wiadomość do korzystania, blokuje go, aby uniemożliwić innym konsumentom odbieranie go i zwraca go do aplikacji. Po przetworzeniu lub zapisaniu wiadomości aplikacja kończy drugi etap procesu `complete` odbierania, wywołując metodę w **Message** obiektu.  Metoda `complete` oznacza komunikat jako zużyty i usuwa go z kolejki.

## <a name="handle-application-crashes-and-unreadable-messages"></a>Obsługa awarii aplikacji i nieczytelnych komunikatów

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu `unlock` z jakiegoś powodu, można wywołać metodę na **Message** obiektu. Usługa Service Bus odblokowuje wiadomość w kolejce i udostępnia ją do odebrania ponownie za pomocą tej samej lub innej aplikacji zużywającej.

Istnieje również limit czasu dla wiadomości zablokowanych w kolejce. Jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady, na przykład w przypadku awarii aplikacji usługa Service Bus automatycznie odblokowuje wiadomość i udostępnia ją do odebraniu.

Jeśli aplikacja ulegnie awarii po `complete` przetworzeniu wiadomości, ale przed wywołaniem metody, wiadomość jest ponownie dostarczona do aplikacji po ponownym uruchomieniu. To zachowanie jest często nazywane *przetwarzaniem co najmniej raz.* Każda wiadomość jest przetwarzana co najmniej raz, ale w niektórych sytuacjach ta sama wiadomość może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje zduplikowane przetwarzania, można użyć **MessageId** właściwości wiadomości, która pozostaje stała w całej prób dostarczania, do obsługi dostarczania zduplikowanych wiadomości. 

> [!TIP]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia łączenie się z obszarem nazw usługi Service Bus i łatwe administrowanie jednostkami obsługi wiadomości. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu oraz możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy poznasz podstawy kolejek usługi Service Bus, zobacz [Kolejki, tematy i subskrypcje,][Queues, topics, and subscriptions] aby dowiedzieć się więcej.

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md
