---
title: Jak używać kolejek usługi Azure Service Bus za pomocą języka Python | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Azure Service Bus za pomocą języka Python.
services: service-bus-messaging
documentationcenter: python
author: spelluru
manager: timlt
editor: ''
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/30/2018
ms.author: spelluru
ms.openlocfilehash: afc310ce4dd373b632f183245ab427a3a65a0af6
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696772"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Jak używać kolejek usługi Service Bus za pomocą języka Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym artykule opisano sposób używania kolejek usługi Service Bus. Przykłady są zapisywane w języku Python i użyj [pakiet języka Python usługi Azure Service Bus][Python Azure Service Bus package]. Omówione scenariusze obejmują **tworzenie kolejek, wysyłanie i odbieranie komunikatów**, i **usuwanie kolejek**.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

> [!NOTE]
> Do zainstalowania języka Python lub [pakiet języka Python usługi Azure Service Bus][Python Azure Service Bus package], zobacz [Przewodnik instalacji języka Python](../python-how-to-install.md).
> 
> 

## <a name="create-a-queue"></a>Tworzenie kolejki
**ServiceBusService** obiekt umożliwia pracę z kolejkami. Dodaj następujący kod u góry każdego pliku Python, w której chcesz uzyskać programowy dostęp do usługi Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Queue
```

Poniższy kod tworzy **ServiceBusService** obiektu. Zastąp `mynamespace`, `sharedaccesskeyname`, i `sharedaccesskey` z przestrzeni nazw, nazwę klucza (SAS) sygnatury dostępu współdzielonego i wartość.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Wartości dla nazwy klucza sygnatury dostępu Współdzielonego i wartość znajduje się w [witryny Azure portal] [ Azure portal] informacje o połączeniu, lub w programie Visual Studio **właściwości** okienko podczas wybierania usługi Przestrzeń nazw magistrali w Eksploratorze serwera (jak pokazano w poprzedniej sekcji).

```python
bus_service.create_queue('taskqueue')
```

`create_queue` Metoda obsługuje również dodatkowe opcje, które umożliwiają zastąpić domyślne ustawienia kolejki, takie jak czas wygaśnięcia (TTL) lub maksymalny rozmiar kolejki komunikatów. Poniższy przykład ustawia maksymalny rozmiar kolejki 5 GB, a wartość czasu wygaśnięcia na 1 minutę:

```python
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## <a name="send-messages-to-a-queue"></a>Wysyłanie komunikatów do kolejki
Aby wysłać komunikat do kolejki usługi Service Bus, wywołania aplikacji `send_queue_message` metody **ServiceBusService** obiektu.

Poniższy przykład pokazuje, jak wysyłać wiadomości testowej kolejkę o nazwie `taskqueue` przy użyciu `send_queue_message`:

```python
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat limitów przydziału, zobacz [przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Odbieranie komunikatów z kolejki
Komunikaty są odbierane z kolejki za pomocą `receive_queue_message` metody **ServiceBusService** obiektu:

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=False)
print(msg.body)
```

Komunikaty są usuwane z kolejki, ponieważ są odczytywane, gdy parametr `peek_lock` ustawiono **False**. Może odczytywać (peek) i blokowanie wiadomość bez usuwania go z kolejki, ustawiając parametr `peek_lock` do **True**.

Zachowanie odczytywanie i usuwanie wiadomości jako część operacji odbierania jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ usługi Service Bus będą oznaczyła komunikat jako wykorzystany, a następnie, gdy aplikacja ponownie uruchamia i ponownie rozpocznie korzystanie z komunikatów, pominie utracony komunikat, który został wykorzystany przed awarią.

Jeśli `peek_lock` parametr ma wartość **True**, odbieranie staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody **Usuń** metody **komunikat** obiekt. **Usuń** metoda będzie Oznacz komunikat jako wykorzystany i usunąć go z kolejki.

```python
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać **odblokować** metody **komunikat** obiektu. Spowoduje to odblokowanie komunikatu w kolejce i udostępnić go do ponownego odbioru, tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (np. Jeśli wystąpiła awaria aplikacji), Usługa Service Bus zostanie automatycznie odblokować wiadomości i ułatwiają dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed **Usuń** metoda jest wywoływana, a następnie komunikat zostanie dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane **przetwarzaniem co najmniej raz**, oznacza to, że każdy komunikat będzie przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu **MessageId** właściwości wiadomości, która pozostaje niezmienna między kolejnymi próbami dostarczenia.

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, zobacz następujące artykuły, aby dowiedzieć się więcej.

* [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

