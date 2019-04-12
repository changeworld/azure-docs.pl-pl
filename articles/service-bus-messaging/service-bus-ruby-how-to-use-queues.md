---
title: Jak używać kolejek usługi Azure Service Bus za pomocą języka Ruby | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać kolejek usługi Service Bus na platformie Azure. Przykłady kodu napisane w języku Ruby.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 6c42fbffd0b4569a9b04dede94061e716c48ecf1
ms.sourcegitcommit: 41015688dc94593fd9662a7f0ba0e72f044915d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59501114"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Jak używać kolejek usługi Service Bus za pomocą języka Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym samouczku dowiesz się, jak tworzyć aplikacje języka Ruby do wysyłania komunikatów i odbiera komunikaty z kolejki usługi Service Bus. Przykłady są napisane w języku Ruby i używają platformy Azure gem.

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować swoje [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub zarejestrować się w celu [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj czynności opisane w [użycia usługi Azure portal można utworzyć kolejki usługi Service Bus](service-bus-quickstart-portal.md) artykułu.
    1. Przeczytaj szybkiego **Przegląd** usługi Service Bus **kolejek**. 
    2. Tworzenie usługi Service Bus **przestrzeni nazw**. 
    3. Pobierz **parametry połączenia**. 

        > [!NOTE]
        > Utworzysz **kolejki** w przestrzeni nazw usługi Service Bus przy użyciu języka Ruby w ramach tego samouczka. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Jak utworzyć kolejkę
**Azure::ServiceBusService** obiekt umożliwia pracę z kolejkami. Aby utworzyć kolejkę, należy użyć `create_queue()` metody. Poniższy przykład tworzy kolejkę lub wyświetla wszelkie błędy.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Można również przekazać **Azure::ServiceBus::Queue** obiektu dodatkowe opcje, co pozwala zastąpić domyślne ustawienia kolejki, takie jak czas komunikatu do kolejki na żywo lub maksymalnego rozmiaru. Poniższy przykład pokazuje, jak można ustawić maksymalny rozmiar kolejki 5 GB, a czas wygaśnięcia na 1 minutę:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Jak wysyłać komunikaty do kolejki
Aby wysłać komunikat do kolejki usługi Service Bus, wywołania aplikacji `send_queue_message()` metody **Azure::ServiceBusService** obiektu. Komunikaty wysłane do (i otrzymane z) usługi Service Bus są kolejki **Azure::ServiceBus::BrokeredMessage** obiektów i mają zestaw właściwości standardowych (takich jak `label` i `time_to_live`), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść dowolnych danych aplikacji. Aplikacja możne ustawić treść komunikatu przez przekazanie wartości ciągu jako wiadomość i wszelkie wymagane właściwości standardowe są wypełniane przy użyciu wartości domyślnych.

Poniższy przykład pokazuje, jak wysyłać wiadomości testowej kolejkę o nazwie `test-queue` przy użyciu `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak odbierać komunikaty z kolejki
Komunikaty są odbierane z kolejki za pomocą `receive_queue_message()` metody **Azure::ServiceBusService** obiektu. Domyślnie komunikaty są odczytu i zablokowane, nie są usuwane z kolejki. Jednakże, można usunąć wiadomości z kolejki jako są odczytywane przez ustawienie `:peek_lock` opcję **false**.

Domyślne zachowanie umożliwia odczyt i usuwanie operacją dwuetapową, co umożliwia także do obsługi aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody `delete_queue_message()` metody i dostarczający komunikat do usunięcia jako parametr. `delete_queue_message()` Metoda będzie Oznacz komunikat jako wykorzystany i usunąć go z kolejki.

Jeśli `:peek_lock` parametr ma wartość **false**, Odczyt i usuwanie wiadomości jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus oznaczyła komunikat jako wykorzystany, gdy aplikacja zostanie ponownie uruchomiona i ponownie rozpocznie korzystanie z komunikatów, pominie utracony komunikat, który został wykorzystany przed awarią.

Poniższy przykład pokazuje, jak odbierać i przetwarzać komunikaty przy użyciu `receive_queue_message()`. W przykładzie najpierw odbiera i usuwa komunikat przy użyciu `:peek_lock` równa **false**, otrzyma inny komunikat o błędzie, a następnie usuwa komunikat przy użyciu `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlock_queue_message()` metody **Azure::ServiceBusService** obiektu. To wywołanie powoduje to odblokowanie komunikatu w kolejce i udostępnić go do ponownego odbioru, tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i sprawia, że dostępne do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed `delete_queue_message()` metoda jest wywoływana, a następnie wiadomości są przed przeniesieniem do aplikacji po jej ponownym uruchomieniu. Ten proces jest często nazywany *przetwarzaniem co najmniej raz*; oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu `message_id` właściwości komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Omówienie [kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).
* Odwiedź stronę [zestawu Azure SDK dla języka Ruby](https://github.com/Azure/azure-sdk-for-ruby) repozytorium w witrynie GitHub.

W celu porównania kolejek usługi Azure Service Bus omówionych w tym artykule i kolejek usługi Azure omówione w [jak używać magazynu kolejek w języku Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) artykuł, zobacz [kolejek platformy Azure i Azure kolejek usługi Service Bus — porównanie i Porównanie](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

