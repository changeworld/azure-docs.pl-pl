---
title: Jak używać tematów usługi Service Bus (Ruby) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać tematów usługi Service Bus i subskrypcji na platformie Azure. Przykłady kodu są napisane dla języka Ruby aplikacji.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: c2862c3521c05ba81f7a567f7951d22a9ab95c44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61472339"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Jak korzystać z subskrypcji i tematów usługi Service Bus za pomocą języka Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano, jak używać tematów usługi Service Bus i subskrypcji z aplikacji języka Ruby. Omówione scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie komunikatów do tematu 
- Odbieranie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji


## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować swoje [korzyści dla subskrybentów programu Visual Studio i MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj czynności opisane w [Szybki Start: Użyj witryny Azure portal do utworzenia tematu usługi Service Bus i subskrypcji do tematu](service-bus-quickstart-topics-subscriptions-portal.md) do utworzenia usługi Service Bus **przestrzeni nazw** i Uzyskaj **parametry połączenia**. 

    > [!NOTE]
    > Utworzysz **tematu** i **subskrypcji** do tematu przy użyciu **Ruby** w tym przewodniku Szybki Start. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Tworzenie tematu
**Azure::ServiceBusService** obiekt umożliwia pracę z obsługą tematów. Poniższy kod tworzy **Azure::ServiceBusService** obiektu. Aby utworzyć temat, należy użyć `create_topic()` metody. Poniższy przykład tworzy temat lub wyświetla wszelkie błędy.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Można również przekazać **Azure::ServiceBus::Topic** obiektu dodatkowe opcje, które pozwalają na zastąpienie domyślnych ustawień temacie, np. czas komunikat do kolejki na żywo lub maksymalny rozmiar. Poniższy przykład pokazuje, ustawienie Maksymalny rozmiar kolejki 5 GB, a czas wygaśnięcia na 1 minutę:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje tematu są również tworzone przy użyciu **Azure::ServiceBusService** obiektu. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów dostarczane do wirtualnej kolejki subskrypcji.

Subskrypcje są trwałe. Nadal istniał aż do każdej z nich lub temat są skojarzone, są usuwane. Jeśli Twoja aplikacja logiki, aby utworzyć subskrypcję, jej powinny najpierw sprawdzić, czy ta subskrypcja już istnieje, przy użyciu metody metody getsubscription interfejsu.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Jeśli po utworzeniu nowej subskrypcji został określony żaden filtr **MatchAll** filtr (ustawienie domyślne) jest używany. Gdy **MatchAll** filtr jest stosowany, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie "all — liczba komunikatów" i używa domyślnego **MatchAll** filtru.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również definiować filtry, które umożliwiają określenie, które komunikaty wysyłane do tematu powinny być widoczne w określonej subskrypcji.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest **Azure::ServiceBus::SqlFilter**, która implementuje podzbiór standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, przejrzyj [SqlFilter](service-bus-messaging-sql-filter.md) składni.

Można dodać filtry do subskrypcji przy użyciu `create_rule()` metody **Azure::ServiceBusService** obiektu. Ta metoda umożliwia dodawanie nowych filtrów do istniejącej subskrypcji.

Ponieważ domyślny filtr jest automatycznie stosowana do wszystkich nowych subskrypcji, należy najpierw usunąć domyślny filtr, lub **MatchAll** zastępuje inne filtry, można określić. Możesz usunąć domyślną regułę przy użyciu `delete_rule()` metody **Azure::ServiceBusService** obiektu.

Poniższy przykład tworzy subskrypcję o nazwie "komunikaty o wysokim" z **Azure::ServiceBus::SqlFilter** wybiera tylko komunikaty o niestandardowej `message_number` właściwość wyższej niż 3:

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

Podobnie poniższy przykład tworzy subskrypcję o nazwie `low-messages` z **Azure::ServiceBus::SqlFilter** wybiera tylko komunikaty, które mają `message_number` właściwość mniejszą lub równą 3:

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

Kiedy wiadomość jest teraz wysyłane do `test-topic`, zawsze jest on dostarczany do odbiorców mających `all-messages` subskrypcji tematu i selektywnie dostarczany do odbiorców mających subskrypcję `high-messages` i `low-messages` subskrypcje tematu (w zależności od zawartości komunikatu).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać komunikat do tematu usługi Service Bus, aplikacja musi używać `send_topic_message()` metody **Azure::ServiceBusService** obiektu. Komunikaty wysyłane do tematów usługi Service Bus są wystąpieniami **Azure::ServiceBus::BrokeredMessage** obiektów. **Azure::ServiceBus::BrokeredMessage** obiekty mają zestaw właściwości standardowych (takich jak `label` i `time_to_live`), słownik, który jest używany do przechowywania niestandardowych właściwości specyficzne dla aplikacji oraz treść danych ciągu. Aplikacja możne ustawić treść komunikatu przez przekazanie wartości ciągu na `send_topic_message()` metody oraz wszelkie wymagane właściwości standardowe są wypełniane przez wartości domyślne.

Poniższy przykład pokazuje sposób wysyłania pięciu testowych komunikatów do `test-topic`. `message_number` Wartość właściwości niestandardowej każdego komunikatu różni się w iteracji pętli (określa subskrypcję, która odbiera on):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji
Komunikaty są odbierane z subskrypcji przy użyciu `receive_subscription_message()` metody **Azure::ServiceBusService** obiektu. Domyślnie komunikaty są read(peak) i zablokowane bez usuwania go z subskrypcji. Może odczytywać i Usuń komunikat z subskrypcji, ustawiając `peek_lock` opcję **false**.

Domyślne zachowanie umożliwia odczyt i usuwanie operacją dwuetapową, co umożliwia także do obsługi aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po skopiowaniu aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody `delete_subscription_message()` metody i dostarczający komunikat do usunięcia jako parametr. `delete_subscription_message()` Metoda oznacza komunikat jako wykorzystany i usunąć go z subskrypcji.

Jeśli `:peek_lock` parametr ma wartość **false**, Odczyt i usuwanie wiadomości jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w razie niepowodzenia. Rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Usługa Service Bus oznaczyła komunikat jako wykorzystany, następnie gdy aplikacja zostanie ponownie uruchomiona i ponownie rozpocznie korzystanie z komunikatów, jego ma utracony komunikat, który został wykorzystany przed awarią.

W poniższym przykładzie pokazano, jak mogą być odbierane wiadomości i przetworzone przy użyciu `receive_subscription_message()`. Przykładzie najpierw odbiera i usuwa komunikat z `low-messages` subskrypcji przy użyciu `:peek_lock` równa **false**, a następnie otrzymuje kolejną wiadomość z `high-messages` i usuwa komunikat przy użyciu `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlock_subscription_message()` metody **Azure::ServiceBusService** obiektu. Powoduje to odblokowanie komunikatu w subskrypcji i udostępnić go do ponownego odbioru, tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w subskrypcji i jeśli aplikacja nie może przetworzyć komunikatu przed blokady upłynie limit czasu (na przykład jeśli wystąpiła awaria aplikacji), a następnie usługi Service Bus automatycznie odblokowuje komunikat i udostępnienie go do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, lecz przed `delete_subscription_message()` metoda jest wywoływana, a następnie wiadomości są przed przeniesieniem do aplikacji po jej ponownym uruchomieniu. Jest często nazywany *co najmniej raz przetwarzania*; oznacza, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Tę logikę to często osiągane przy użyciu `message_id` właściwości komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Tematy i subskrypcje są trwałe i musi zostać jawnie usunięte za pomocą [witryny Azure portal] [ Azure portal] lub programowo. W poniższym przykładzie pokazano sposób usuwania tematu o nazwie `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy kod ilustruje sposób usuwania subskrypcji o nazwie `high-messages` z `test-topic` tematu:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy tematów usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz [kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).
* Dokumentacja interfejsów API dla elementu [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Odwiedź stronę [zestawu Azure SDK dla języka Ruby](https://github.com/Azure/azure-sdk-for-ruby) repozytorium w witrynie GitHub.

[Azure portal]: https://portal.azure.com
