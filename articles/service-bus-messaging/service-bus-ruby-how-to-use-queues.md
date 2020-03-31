---
title: Jak korzystać z kolejek usługi Azure Service Bus z ruby
description: W tym samouczku dowiesz się, jak tworzyć aplikacje Ruby do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Service Bus.
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
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: a699543bb442e7c57d57e72acb2cdf6ac40159c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760593"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Szybki start: jak korzystać z kolejek usługi Service Bus z Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

W tym samouczku dowiesz się, jak tworzyć aplikacje Ruby do wysyłania wiadomości do i odbierania wiadomości z kolejki usługi Service Bus. Przykłady są zapisywane w ruby i używać klejnotu azure.

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [swoje korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) lub założyć bezpłatne [konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Wykonaj kroki opisane w artykule [Użyj portalu Azure, aby utworzyć kolejkę usługi Service Bus.](service-bus-quickstart-portal.md)
    1. Przeczytaj krótki **przegląd** **kolejek**usługi Service Bus . 
    2. Tworzenie obszaru **nazw**usługi Service Bus . 
    3. Pobierz **ciąg połączenia**. 

        > [!NOTE]
        > Spowoduje to utworzenie **kolejki** w obszarze nazw usługi Service Bus przy użyciu ruby w tym samouczku. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Jak utworzyć kolejkę
**Obiekt Azure::ServiceBusService** umożliwia pracę z kolejkami. Aby utworzyć kolejkę, `create_queue()` użyj tej metody. Poniższy przykład tworzy kolejkę lub drukuje wszelkie błędy.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Można również przekazać **azure::ServiceBus::Queue** obiektu z dodatkowych opcji, które umożliwia zastąpienie domyślne ustawienia kolejki, takie jak czas wiadomości do żywo lub maksymalny rozmiar kolejki. W poniższym przykładzie pokazano, jak ustawić maksymalny rozmiar kolejki na 5 GB i czas na żywo do 1 minuty:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Jak wysyłać wiadomości do kolejki
Aby wysłać wiadomość do kolejki usługi Service `send_queue_message()` Bus, aplikacja wywołuje metodę na **azure::ServiceBusService** obiektu. Wiadomości wysyłane do (i odebrane z) kolejek usługi Service Bus to **Azure::ServiceBus::BrokeredMessage** obiektów i mają zestaw właściwości standardowych (takich jak `label` i `time_to_live`), słownik, który jest używany do przechowywania właściwości specyficzne dla aplikacji niestandardowe i treść dowolnych danych aplikacji. Aplikacja może ustawić treść wiadomości, przekazując wartość ciągu jako komunikat, a wszystkie wymagane właściwości standardowe są wypełniane wartościami domyślnymi.

W poniższym przykładzie pokazano, jak wysłać `test-queue` `send_queue_message()`wiadomość testową do kolejki o nazwie przy użyciu:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Kolejki usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w kolejce, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez kolejkę. Ten rozmiar kolejki jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Jak odbierać komunikaty z kolejki
Wiadomości są odbierane z `receive_queue_message()` kolejki przy użyciu metody na **Azure::ServiceBusService** obiektu. Domyślnie wiadomości są odczytywane i blokowane bez usuwania z kolejki. Można jednak usunąć wiadomości z kolejki, ponieważ są `:peek_lock` one odczytywane, ustawiając opcję **false**.

Domyślne zachowanie sprawia, że odczytywanie i usuwanie operacji dwuetapowej, co również umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po zakończeniu przetwarzania wiadomości (lub magazynuje ją niezawodnie do przetwarzania w przyszłości), kończy `delete_queue_message()` drugi etap procesu odbierania przez metodę wywoływania i dostarczanie wiadomości do usunięcia jako parametr. Metoda `delete_queue_message()` oznaczy wiadomość jako zużytą i usunie ją z kolejki.

Jeśli `:peek_lock` parametr jest ustawiony na **false,** odczytu i usuwania wiadomości staje się najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nie przetwarzania wiadomości w przypadku awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ usługa Service Bus oznaczyła komunikat jako zużyty, gdy aplikacja uruchomi się ponownie i zacznie ponownie likwidować wiadomości, zostanie wyświetlony komunikat, który został zużyty przed awarią.

W poniższym przykładzie pokazano, jak `receive_queue_message()`odbierać i przetwarzać wiadomości za pomocą programu . Przykład najpierw odbiera i usuwa wiadomość `:peek_lock` przy użyciu zestawu do **false,** a następnie `delete_queue_message()`odbiera kolejną wiadomość, a następnie usuwa wiadomość za pomocą:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie jest w stanie przetworzyć `unlock_queue_message()` komunikatu z jakiegoś powodu, następnie można wywołać metodę na **Azure::ServiceBusService** obiektu. To wywołanie powoduje, że usługa Service Bus, aby odblokować wiadomość w kolejce i udostępnić go do odebrania ponownie, za pomocą tej samej aplikacji zużywającej lub przez inną aplikację zużywającą.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w kolejce, a jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), usługa Service Bus automatycznie odblokowuje wiadomość i ją tworzy. można otrzymać ponownie.

W przypadku awarii aplikacji po przetworzeniu wiadomości, ale przed wywołaniem `delete_queue_message()` metody, komunikat jest ponownie dostarczony do aplikacji po ponownym uruchomieniu. Proces ten jest często nazywany *Co najmniej raz przetwarzania;* oznacza to, że każda wiadomość jest przetwarzana co najmniej raz, ale w niektórych sytuacjach ta sama wiadomość może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Jest to często osiągane przy użyciu `message_id` właściwości wiadomości, która pozostaje stała w próbach dostarczenia.

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy kolejek usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Omówienie [kolejek, tematów i subskrypcji](service-bus-queues-topics-subscriptions.md).
* Odwiedź [zestaw SDK platformy Azure dla](https://github.com/Azure/azure-sdk-for-ruby) repozytorium Ruby w usłudze GitHub.

Aby uzyskać porównanie kolejek usługi Azure Service Bus omówionych w tym artykule i kolejek platformy Azure omówionych w artykule [Jak używać magazynu kolejek z ruby,](../storage/queues/storage-ruby-how-to-use-queue-storage.md) zobacz [Kolejki platformy Azure i kolejki usługi Azure Service Bus — w porównaniu i w przeciwieństwie do kontrastu](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

