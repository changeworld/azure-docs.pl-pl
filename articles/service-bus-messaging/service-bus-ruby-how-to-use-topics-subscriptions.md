---
title: 'Szybki start: jak korzystać z tematów usługi Service Bus (Ruby)'
description: 'Szybki start: dowiedz się, jak korzystać z tematów i subskrypcji usługi Service Bus na platformie Azure. Przykłady kodu są zapisywane dla aplikacji Ruby.'
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
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: b5401eae844ed2113a9fbc07c8b3ad8601709d43
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73718935"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Szybki start: jak korzystać z tematów i subskrypcji usługi Service Bus w ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano sposób korzystania z tematów usługi Service Bus i subskrypcji z aplikacji Ruby. Scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie wiadomości do tematu 
- Odbieranie wiadomości z subskrypcji
- Usuwanie tematów i subskrypcji


## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Możesz aktywować [korzyści dla subskrybenta programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub założyć [bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
2. Wykonaj kroki opisane w [przewodniku Szybki start: Użyj portalu Azure, aby utworzyć temat usługi Service Bus i subskrypcje tematu, aby](service-bus-quickstart-topics-subscriptions-portal.md) utworzyć **obszar nazw** usługi Service Bus i uzyskać **ciąg połączenia**. 

    > [!NOTE]
    > W tym **przewodniku** Szybki start utworzysz temat i **subskrypcję** tematu. **Ruby** 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Tworzenie tematu
**Obiekt Azure::ServiceBusService** umożliwia pracę z tematami. Poniższy kod tworzy **obiekt Azure::ServiceBusService.** Aby utworzyć temat, `create_topic()` użyj tej metody. Poniższy przykład tworzy temat lub drukuje wszelkie błędy.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Można również przekazać **azure::ServiceBus::Topic** obiektu z dodatkowych opcji, które umożliwiają zastąpienie domyślnych ustawień tematu, takich jak czas wiadomości do żywo lub maksymalny rozmiar kolejki. Poniższy przykład pokazuje ustawienie maksymalnego rozmiaru kolejki na 5 GB i czas na żywo do 1 minuty:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji
Subskrypcje tematów są również tworzone za pomocą **obiektu Azure::ServiceBusService.** Subskrypcje są nazwane i mogą mieć opcjonalny filtr, który ogranicza zestaw wiadomości dostarczanych do kolejki wirtualnej subskrypcji.

Domyślnie subskrypcje są trwałe. Nadal istnieją, dopóki nie zostaną usunięte, albo temat, z który są skojarzone. Jeśli aplikacja zawiera logikę do tworzenia subskrypcji, należy najpierw sprawdzić, czy subskrypcja już istnieje przy użyciu getSubscription metody.

Subskrypcje można usunąć automatycznie, ustawiając [właściwość AutoDeleteOnIdle](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)
Jeśli podczas tworzenia nowej subskrypcji nie określono żadnego filtru, używany jest filtr **MatchAll** (domyślny). Gdy używany jest filtr **MatchAll,** wszystkie komunikaty opublikowane w temacie są umieszczane w kolejce wirtualnej subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie "wszystkie wiadomości" i używa domyślnego **filtru MatchAll.**

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami
Można również zdefiniować filtry, które umożliwiają określenie, które wiadomości wysyłane do tematu powinny być wyświetlane w ramach określonej subskrypcji.

Najbardziej elastycznym typem filtru obsługiwanego przez subskrypcje jest **azure::ServiceBus::SqlFilter**, który implementuje podzbiór sql92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, przejrzyj składnię [SqlFilter.](service-bus-messaging-sql-filter.md)

Filtry można dodać do subskrypcji `create_rule()` przy użyciu metody **azure::ServiceBusService** obiektu. Ta metoda umożliwia dodawanie nowych filtrów do istniejącej subskrypcji.

Ponieważ filtr domyślny jest stosowany automatycznie do wszystkich nowych subskrypcji, należy najpierw usunąć filtr domyślny lub **MatchAll** zastępuje wszystkie inne filtry, które można określić. Regułę domyślną można usunąć `delete_rule()` przy użyciu metody na **obiekcie Azure::ServiceBusService.**

Poniższy przykład tworzy subskrypcję o nazwie "high-messages" z **azure::ServiceBus::SqlFilter,** który wybiera tylko wiadomości, które mają właściwość niestandardową `message_number` większą niż 3:

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

Podobnie w poniższym przykładzie `low-messages` tworzy subskrypcję o nazwie **azure::ServiceBus::SqlFilter,** który wybiera tylko wiadomości, które mają `message_number` właściwość mniej niż lub równe 3:

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

Gdy wiadomość jest teraz `test-topic`wysyłana do , jest zawsze dostarczana `all-messages` do odbiorców subskrybowanych subskrypcji tematu `high-messages` i `low-messages` selektywnie dostarczana do odbiorców subskrybowanych subskrypcji i tematów (w zależności od zawartości wiadomości).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu
Aby wysłać wiadomość do tematu usługi Service `send_topic_message()` Bus, aplikacja musi użyć metody na **azure::ServiceBusService** obiektu. Wiadomości wysyłane do tematów usługi Service Bus są wystąpieniami **obiektów Azure::ServiceBus::BrokeredMessage.** **Azure::ServiceBus::BrokeredMessage** obiekty mają zestaw właściwości standardowych `label` (takich jak i `time_to_live`), słownik, który jest używany do przechowywania właściwości specyficzne dla aplikacji niestandardowych i treść danych ciągu. Aplikacja może ustawić treść wiadomości, przekazując wartość `send_topic_message()` ciągu do metody, a wszystkie wymagane właściwości standardowe są wypełniane przez wartości domyślne.

W poniższym przykładzie pokazano, jak `test-topic`wysłać pięć wiadomości testowych do . Wartość `message_number` właściwości niestandardowej każdej wiadomości różni się w zależności od iteracji pętli (określa, która subskrypcja ją odbiera):

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Odbieranie wiadomości z subskrypcji
Wiadomości są odbierane z `receive_subscription_message()` subskrypcji przy użyciu metody na **azure::ServiceBusService** obiektu. Domyślnie wiadomości są odczytywane (szczytowe) i blokowane bez usuwania ich z subskrypcji. Możesz odczytać i usunąć wiadomość z subskrypcji, ustawiając `peek_lock` opcję **false**.

Domyślne zachowanie sprawia, że odczytywanie i usuwanie operacji dwuetapowej, co również umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Po zakończeniu przetwarzania wiadomości (lub magazynuje ją niezawodnie do przetwarzania w przyszłości), kończy `delete_subscription_message()` drugi etap procesu odbierania przez metodę wywoływania i dostarczanie wiadomości do usunięcia jako parametr. Metoda `delete_subscription_message()` oznacza komunikat jako używane i usunąć go z subskrypcji.

Jeśli `:peek_lock` parametr jest ustawiony na **false,** odczytu i usuwania wiadomości staje się najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nie przetwarzania wiadomości, gdy wystąpi błąd. Należy wziąć pod uwagę scenariusz, w którym konsument wystawia żądanie odbierania, a następnie ulega awarii przed przetworzeniem go. Ponieważ usługa Service Bus oznaczyła komunikat jako zużyty, a następnie po ponownym uruchomieniu aplikacji i ponownym rozpoczęciu korzystania z wiadomości, nie odebrano komunikatu, który został zużyty przed awarią.

W poniższym przykładzie pokazano, jak wiadomości `receive_subscription_message()`mogą być odbierane i przetwarzane za pomocą programu . Przykład najpierw odbiera i usuwa wiadomość `low-messages` z `:peek_lock` subskrypcji przy użyciu set to **false**, a następnie odbiera kolejną `high-messages` wiadomość z, a następnie usuwa wiadomość za pomocą: `delete_subscription_message()`

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania
Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie jest w stanie przetworzyć `unlock_subscription_message()` komunikatu z jakiegoś powodu, następnie można wywołać metodę na **Azure::ServiceBusService** obiektu. Powoduje to, że usługa Service Bus, aby odblokować komunikat w ramach subskrypcji i udostępnić go do odebraniu ponownie, za pomocą tej samej aplikacji zużywającej lub przez inną aplikację zużywającą.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w ramach subskrypcji, a jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady (na przykład, jeśli aplikacja ulegnie awarii), usługa Service Bus automatycznie odblokuje wiadomość i udostępnić go do ponownego otrzymania.

W przypadku awarii aplikacji po przetworzeniu wiadomości, ale przed wywołaniem `delete_subscription_message()` metody, komunikat jest ponownie dostarczony do aplikacji po ponownym uruchomieniu. Jest często nazywany *co najmniej raz przetwarzania;* oznacza to, że każda wiadomość jest przetwarzana co najmniej raz, ale w niektórych sytuacjach ta sama wiadomość może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Ta logika jest często `message_id` osiągana przy użyciu właściwości wiadomości, która pozostaje stała w próbach dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji
Tematy i subskrypcje są trwałe, chyba że ustawiona jest [właściwość AutoDeleteOnIdle.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle) Można je usunąć za pośrednictwem [witryny Azure portal][Azure portal] lub programowo. W poniższym przykładzie pokazano, `test-topic`jak usunąć temat o nazwie .

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy kod pokazuje, jak usunąć `high-messages` subskrypcję o nazwie z tematu: `test-topic`

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia użytkownikom łączenie się z obszarem nazw usługi Service Bus i administrowanie jednostkami obsługi wiadomości w łatwy sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu lub możliwość testowania tematu, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki
Teraz, gdy poznasz podstawy tematów usługi Service Bus, skorzystaj z tych łączy, aby dowiedzieć się więcej.

* Zobacz [Kolejki, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md).
* Dokumentacja interfejsów API dla elementu [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Odwiedź [zestaw SDK platformy Azure dla](https://github.com/Azure/azure-sdk-for-ruby) repozytorium Ruby w usłudze GitHub.

[Azure portal]: https://portal.azure.com
