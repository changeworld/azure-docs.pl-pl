---
title: Jak używać tematów usługi Service Bus (Ruby) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać tematów usługi Service Bus i subskrypcji platformy Azure. Przykłady kodu są napisane dla aplikacji dopisków fonetycznych.
services: service-bus-messaging
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 08/10/2017
ms.author: sethm
ms.openlocfilehash: 4a4c9949843b16ae6be2f516de4fd1e3f7415959
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926795"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Jak używać tematów usługi Service Bus i subskrypcje z Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano, jak używać tematów usługi Service Bus i subskrypcji z aplikacji dopisków fonetycznych. Omówione scenariusze obejmują **tworzenie tematów i subskrypcji, tworzenie filtrów subskrypcji, wysyłanie komunikatów** do tematu, **odbieranie komunikatów z subskrypcji**, i **usuwanie Tematy i subskrypcje**. Aby uzyskać więcej informacji dotyczących tematów i subskrypcji, zobacz [następne kroki](#next-steps) sekcji.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Tworzenie tematu
**Azure::ServiceBusService** obiektu umożliwia pracę z tematów. Poniższy kod tworzy **Azure::ServiceBusService** obiektu. Aby utworzyć temat, użyj `create_topic()` metody. Poniższy przykład tworzy temat lub drukuje się błędy, jeśli istnieją.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_queue("test-topic")
rescue
  puts $!
end
```

Można również przekazać **Azure::ServiceBus::Topic** obiektu dodatkowe opcje, które umożliwiają zastąpienie domyślnych ustawień tematu, takie jak czas wiadomości do kolejki na żywo lub maksymalny rozmiar. W poniższym przykładzie przedstawiono ustawienie Maksymalny rozmiar kolejki 5 GB i czas wygaśnięcia na 1 minutę:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje tematu są również tworzone za pomocą **Azure::ServiceBusService** obiektu. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów dostarczonych do wirtualnej kolejki subskrypcji.

Subskrypcje są trwałe i będzie nadal istnieć do czasu albo lub temat są skojarzone z, zostaną usunięte. Jeśli Twoja aplikacja logiki, aby utworzyć subskrypcję, jej należy najpierw sprawdź, czy ta subskrypcja już istnieje przy użyciu metody getSubscription.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Filtr **MatchAll** jest filtrem domyślnym, który jest używany, gdy podczas tworzenia nowej subskrypcji nie został określony żaden filtr. Gdy **MatchAll** filtr jest używany, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie "all — liczba komunikatów" i używa domyślnej **MatchAll** filtru.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również zdefiniować filtry, które pozwalają określić, które powinny być widoczne wiadomości wysłanych do tematu w ramach określonej subskrypcji.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest **Azure::ServiceBus::SqlFilter**, która implementuje podzbiór standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, przejrzyj [SqlFilter](service-bus-messaging-sql-filter.md) składni.

Filtry można dodać do subskrypcji przy użyciu `create_rule()` metody **Azure::ServiceBusService** obiektu. Ta metoda umożliwia dodawanie nowych filtrów do istniejącej subskrypcji.

Ponieważ domyślnego filtru jest automatycznie stosowana do wszystkich nowych subskrypcji, należy najpierw usunąć domyślny filtr lub **MatchAll** zastępują inne filtry, można określić. Należy usunąć domyślną regułę przy użyciu `delete_rule()` metoda **Azure::ServiceBusService** obiektu.

Poniższy przykład tworzy subskrypcję o nazwie "Wysoki — liczba komunikatów" z **Azure::ServiceBus::SqlFilter** który wybiera tylko komunikaty, które mają niestandardowy `message_number` właściwości wyższej niż 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Podobnie poniższy przykład tworzy subskrypcję o nazwie `low-messages` z **Azure::ServiceBus::SqlFilter** który wybiera tylko komunikaty, które mają `message_number` właściwości mniej niż 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Teraz wysłania komunikatu do `test-topic`, on zawsze jest dostarczany do odbiorców `all-messages` subskrypcję tematu i selektywnie dostarczany do odbiorców mających subskrypcję `high-messages` i `low-messages` subskrypcje tematu (w zależności od zawartości komunikatu).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać komunikat do tematu usługi Service Bus, aplikacja musi używać `send_topic_message()` metoda **Azure::ServiceBusService** obiektu. Komunikaty wysyłane do tematów usługi Service Bus są wystąpieniami klasy **Azure::ServiceBus::BrokeredMessage** obiektów. **Azure::ServiceBus::BrokeredMessage** obiekty mają zestaw właściwości standardowych (takich jak `label` i `time_to_live`), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść danych ciągu. Aplikacja możne ustawić treść komunikatu przez przekazanie wartości ciągu na `send_topic_message()` — metoda i wszystkie wymagane właściwości standardowych zostaną wypełnione przez wartości domyślne.

W poniższym przykładzie pokazano sposób wysyłania pięciu testowych komunikatów do `test-topic`. Należy pamiętać, że `message_number` wartość właściwości niestandardowej każdego komunikatu różni się w iteracji pętli (określa, które subskrypcji odbiera on):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Komunikaty są odbierane z subskrypcją za pomocą `receive_subscription_message()` metoda **Azure::ServiceBusService** obiektu. Domyślnie komunikaty są read(peak) i zablokowany bez usuwania go z subskrypcji. Może odczytywać i usunąć wiadomości z subskrypcji przez ustawienie `peek_lock` opcji w celu **false**.

Domyślne zachowanie umożliwia odczytywanie i usuwanie operacją dwuetapową, co umożliwia także do obsługi aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody `delete_subscription_message()` — metoda i dostarczający komunikat, który ma zostać usunięty jako parametr. `delete_subscription_message()` — Metoda będzie oznaczyć komunikat jako wykorzystany i usunąć go z subskrypcji.

Jeśli `:peek_lock` ustawiono parametr **false**, odczytywanie i usuwanie wiadomości staje się najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie awarii. Aby to zrozumieć, rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus zostanie oznaczona komunikat jako wykorzystany, a następnie po uruchomieniu i rozpocznie korzystanie z komunikatów ponownie aplikacji, pominie utracony komunikat, który został wykorzystany przed awarią.

W poniższym przykładzie pokazano, jak mogą być odbierane wiadomości i przetworzone przy użyciu `receive_subscription_message()`. Przykładzie najpierw odbiera i usuwa komunikat z `low-messages` subskrypcji przy użyciu `:peek_lock` ustawioną **false**, a następnie odbierze kolejną wiadomość z `high-messages` , a następnie usunięcie wiadomości za pomocą `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlock_subscription_message()` metoda **Azure::ServiceBusService** obiektu. Powoduje to odblokowanie komunikatu w subskrypcji przez usługę Service Bus i ponowne udostępnienie go do odebrania przez tę samą lub inną odbierającą aplikację.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w subskrypcji i jeśli aplikacja nie może przetworzyć komunikatu przed przekroczenie limitu czasu blokady upływem (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus zostanie automatycznie odblokowanie komunikatu i udostępnienie go do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem `delete_subscription_message()` metoda jest wywoływana, a następnie wiadomość jest dostarczony do aplikacji po jej ponownym uruchomieniu. Jest to często nazywane *przetwarzaniem co najmniej raz*; oznacza to, że każdy komunikat będzie przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Istotą takiej logiki jest często osiągane przy użyciu `message_id` właściwości wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Tematy i subskrypcje są trwałe i musi zostać jawnie usunięte przez [portalu Azure] [ Azure portal] lub programowo. W poniższym przykładzie pokazano sposób usuwania tematu o nazwie `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy kod ilustruje sposób usuwania subskrypcji o nazwie `high-messages` z `test-topic` tematu:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Następne kroki
Teraz, kiedy znasz już podstawy tematów usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz [kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).
* Dokumentacja interfejsów API dla elementu [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter#microsoft_servicebus_messaging_sqlfilter).
* Odwiedź stronę [zestawu Azure SDK dla środowiska Ruby](https://github.com/Azure/azure-sdk-for-ruby) repozytorium w witrynie GitHub.

[Azure portal]: https://portal.azure.com
