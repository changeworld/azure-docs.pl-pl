---
title: Jak używać kolejek usługi Azure Service Bus z Ruby | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure. Przykłady kodu napisane w języku Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 357a7277dd42b6973cf35a9f642b8eec36a745e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23868445"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Jak używać kolejek usługi Service Bus z Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym przewodniku opisano, jak używać kolejek usługi Service Bus. Przykłady są napisane w języku Ruby i używają gem platformy Azure. Omówione scenariusze obejmują **tworzenie kolejek, wysyłanie i odbieranie wiadomości**, i **usuwanie kolejek**. Aby uzyskać więcej informacji na temat kolejek usługi Service Bus, zobacz [następne kroki](#next-steps) sekcji.

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]
   
[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Tworzenie kolejki
**Azure::ServiceBusService** obiektu umożliwia pracę z kolejki. Aby utworzyć kolejkę, należy użyć `create_queue()` metody. Poniższy przykład tworzy kolejkę lub błędy do drukowania.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Można również przekazać **Azure::ServiceBus::Queue** obiekt dodatkowe opcje, co umożliwia zastępują ustawienia domyślne kolejki, takie jak czas wiadomości do kolejki na żywo lub maksymalny rozmiar. Poniższy przykład przedstawia sposób maksymalny rozmiar kolejki 5 GB i godzinę ustawioną na żywo na 1 minutę:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Jak wysyłać wiadomości do kolejki
Aby wysłać wiadomość do kolejki usługi Service Bus, wywołania aplikacji `send_queue_message()` metoda **Azure::ServiceBusService** obiektu. Komunikaty wysłane do (i odebrane z) usługi Service Bus są kolejki **Azure::ServiceBus::BrokeredMessage** obiekty i mają zestaw właściwości standardowych (takich jak `label` i `time_to_live`), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja możne ustawić treść komunikatu przez przekazanie wartości ciąg jako komunikat i wszelkie wymagane właściwości standardowe są wypełniane przy użyciu wartości domyślnych.

W poniższym przykładzie pokazano sposób wysyłania wiadomości testowej do kolejki o nazwie `test-queue` przy użyciu `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak odbierać komunikaty z kolejki
Odebrane komunikaty z kolejki przy użyciu `receive_queue_message()` metoda **Azure::ServiceBusService** obiektu. Domyślnie komunikaty są odczytywanie oraz zablokowany bez są usuwane z kolejki. Jednak można usunąć wiadomości z kolejki podczas wczytywania przez ustawienie `:peek_lock` opcji w celu **false**.

Domyślne zachowanie umożliwia odczytywanie i usuwanie operacją dwuetapową, co umożliwia także do obsługi aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody `delete_queue_message()` — metoda i dostarczający komunikat, który ma zostać usunięty jako parametr. `delete_queue_message()` Metoda będzie oznaczyć komunikat jako wykorzystany i usunąć go z kolejki.

Jeśli `:peek_lock` ustawiono parametr **false**, odczytywanie i usuwanie wiadomości staje się najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus został oznaczony komunikat jako wykorzystany, gdy aplikacja ponownego uruchomienia i rozpocznie korzystanie z komunikatów ponownie, pominie utracony komunikat, który został wykorzystany przed awarią.

W poniższym przykładzie pokazano sposób odbierały i przetwarzały wiadomości przy użyciu `receive_queue_message()`. W przykładzie najpierw odbiera i usuwa komunikat przy użyciu `:peek_lock` ustawioną **false**, a następnie odbiera kolejną wiadomość, a następnie usuwa komunikat przy użyciu `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlock_queue_message()` metoda **Azure::ServiceBusService** obiektu. To wywołanie powoduje to odblokowanie komunikatu w kolejce i udostępnienie go do ponownego odbioru, przez tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed przekroczenie limitu czasu blokady wygaśnięcia (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i udostępnia go do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem `delete_queue_message()` metoda jest wywoływana, a następnie wiadomość jest dostarczony do aplikacji po jej ponownym uruchomieniu. Ten proces jest często nazywany *przetwarzaniem co najmniej raz*; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu `message_id` właściwości wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Omówienie [kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).
* Odwiedź stronę [zestawu Azure SDK dla środowiska Ruby](https://github.com/Azure/azure-sdk-for-ruby) repozytorium w witrynie GitHub.

Porównanie między kolejek usługi Azure Service Bus omówione w tym artykule i kolejek Azure opisano w [jak używać magazynu kolejek w języku Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) artykułu, zobacz [kolejek Azure oraz Azure kolejek usługi Service Bus - porównywane i odróżniające](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

