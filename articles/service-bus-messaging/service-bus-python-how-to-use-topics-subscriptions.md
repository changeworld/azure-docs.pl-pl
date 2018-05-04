---
title: Jak używać tematów usługi Azure Service Bus z Python | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Service Bus tematów i subskrypcji w języku Python.
services: service-bus-messaging
documentationcenter: python
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/20/2018
ms.author: sethm
ms.openlocfilehash: 6f262a63de9409d6b355d9783ca958e4715b1ea5
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Jak używać tematów usługi Service Bus i subskrypcje z języka Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano sposób używania tematów i subskrypcji usługi Service Bus. Próbki są napisane w języku Python i użyj [pakiet Azure Python SDK][Azure Python package]. Omówione scenariusze obejmują **tworzenie tematów i subskrypcji**, **tworzenie filtrów subskrypcji**, **wysyłanie komunikatów do tematu**, **odbieranie komunikatów z subskrypcji**, i **usuwanie tematów i subskrypcji**. Aby uzyskać więcej informacji o tematach i subskrypcjach, zobacz [następne kroki](#next-steps) sekcji.

[!INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

> [!NOTE] 
> Jeśli musisz zainstalować Python lub [pakiet języka Python Azure][Azure Python package], zobacz [Przewodnik instalacji języka Python](../python-how-to-install.md).

## <a name="create-a-topic"></a>Tworzenie tematu

**ServiceBusService** obiektu umożliwia pracę z tematów. Dodaj następujący kod u góry każdego pliku Python, w którym chcesz uzyskania programowego dostępu do usługi Service Bus:

```python
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Poniższy kod tworzy **ServiceBusService** obiektu. Zastąp `mynamespace`, `sharedaccesskeyname`, i `sharedaccesskey` z obszaru nazw rzeczywiste, nazwy i klucza wartość klucza dostępu sygnatury dostępu Współdzielonego.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Można uzyskać wartości dla nazwy klucza sygnatury dostępu Współdzielonego i wartość z [portalu Azure][Azure portal].

```python
bus_service.create_topic('mytopic')
```

`create_topic` Metoda obsługuje również dodatkowe opcje, które umożliwiają zastąpienie domyślnych ustawień tematu, takie jak czas wygaśnięcia wiadomości lub temat maksymalny rozmiar. Poniższy przykład przedstawia rozmiar maksymalny tematu 5 GB i czas wygaśnięcia (TTL) wartość jednej minuty:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji

Subskrypcje do tematów, również są tworzone za pomocą **ServiceBusService** obiektu. Subskrypcje są nazywane i mogą zawierać opcjonalny filtr, który ogranicza zestaw komunikatów dostarczonych do wirtualnej kolejki subskrypcji.

> [!NOTE]
> Subskrypcje są trwałe i będzie nadal istnieć do czasu ich albo tematu, w której subskrybowanych, zostaną usunięte.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)

Filtr **MatchAll** jest filtrem domyślnym, który jest używany, gdy podczas tworzenia nowej subskrypcji nie został określony żaden filtr. Gdy **MatchAll** filtr jest używany, wszystkie komunikaty opublikowane do tematu są umieszczane w wirtualnej kolejce subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie `AllMessages` i używa domyślnego **MatchAll** filtru.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami

Można również zdefiniować filtry, które pozwalają określić, które powinny być widoczne wiadomości wysłanych do tematu w subskrypcji określonego tematu.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest **SqlFilter**, która implementuje podzbiór standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, zobacz składnię [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Filtry można dodać do subskrypcji przy użyciu **utworzyć\_reguły** metody **ServiceBusService** obiektu. Ta metoda umożliwia dodawanie nowych filtrów do istniejącej subskrypcji.

> [!NOTE]
> Ponieważ domyślnego filtru jest automatycznie stosowane do wszystkich nowych subskrypcji, należy najpierw usunąć domyślny filtr lub **MatchAll** zastępują inne filtry, można określić. Należy usunąć domyślną regułę przy użyciu `delete_rule` metody **ServiceBusService** obiektu.
> 
> 

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` z **SqlFilter** który wybiera tylko komunikaty, które mają niestandardowy `messagenumber` właściwości wyższej niż 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Podobnie poniższy przykład tworzy subskrypcję o nazwie `LowMessages` z **SqlFilter** który wybiera tylko komunikaty, które mają `messagenumber` właściwości mniej niż 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Teraz, gdy wiadomość jest wysyłana do `mytopic` zawsze jest dostarczany do odbiorców mających subskrypcję **AllMessages** subskrypcję tematu i selektywnie dostarczany do odbiorców mających subskrypcję **HighMessages** i **LowMessages** subskrypcje tematu (w zależności od zawartości komunikatu).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu

Aby wysłać komunikat do tematu usługi Service Bus, aplikacja musi używać `send_topic_message` metody **ServiceBusService** obiektu.

W poniższym przykładzie pokazano sposób wysyłania pięciu testowych komunikatów do `mytopic`. `messagenumber` Wartość właściwości każdego komunikatu różni się w iteracji pętli (określa subskrypcje, które go otrzymają):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji

Komunikaty są odbierane z subskrypcją za pomocą `receive_subscription_message` metoda **ServiceBusService** obiektu:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Wiadomości są usuwane z subskrypcji, ponieważ są one podczas odczytu parametru `peek_lock` ustawiono **False**. Można odczytać (peek) i Zablokuj wiadomość bez jego usuwania z kolejki przez ustawienie dla parametru `peek_lock` do **True**.

Zachowanie odczytywanie i usuwanie wiadomości w ramach operacji receive jest najprostszym modelem i działa najlepiej w scenariuszach, w których aplikacja może tolerować nieprzetworzenie komunikatu w przypadku awarii. Aby zrozumieć to zachowanie, Rozważmy scenariusz, w którym konsument wystawia żądanie odbioru, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Usługa Service Bus zostanie oznaczona komunikat jako wykorzystany, a następnie po uruchomieniu i rozpocznie korzystanie z komunikatów ponownie aplikacji, pominie utracony komunikat, który został wykorzystany przed awarią.

Jeśli `peek_lock` ustawiono parametr **True**, odbieranie staje się operacją dwuetapowy, co umożliwia obsługę aplikacji, które nie tolerują brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Kiedy aplikacja zakończy przetwarzanie komunikatu (lub niezawodnie zapisze go w celu przyszłego przetwarzania), wykonuje drugi etap procesu odbierania przez wywołanie metody `delete` metoda **komunikat** obiektu. `delete` Metoda oznacza komunikat jako wykorzystany i usuwa go z subskrypcji.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiorcy nie może przetworzyć komunikatu z jakiegoś powodu, wówczas może wywołać `unlock` metoda **komunikat** obiektu. Ta metoda powoduje to odblokowanie komunikatu w subskrypcji i udostępnienie go do ponownego odbioru, przez tę samą lub inną odbierającą aplikację usługę Service Bus.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w subskrypcji i jeśli aplikacja nie może przetworzyć komunikatu przed przekroczenie limitu czasu blokady wygaśnięcia (na przykład jeśli wystąpiła awaria aplikacji), Usługa Service Bus automatycznie odblokowuje komunikat i udostępnia go do ponownego odbioru.

W przypadku, gdy aplikacja przestaje działać po przetworzeniu komunikatu, ale przed wysłaniem `delete` metoda jest wywoływana, a następnie komunikat zostanie dostarczony do aplikacji po jej ponownym uruchomieniu. To zachowanie jest często nazywana *przetwarzaniem co najmniej raz*; oznacza to, że każdy komunikat będzie przetwarzany co najmniej raz, ale w pewnych sytuacjach ten sam komunikat może być dostarczony ponownie. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. Aby to zrobić, można użyć **MessageId** właściwości wiadomości, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji

Tematy i subskrypcje są trwałe i musi zostać jawnie usunięte przez [portalu Azure] [ Azure portal] lub programowo. Poniższy przykład przedstawia sposób usuwania tematu o nazwie `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy kod przedstawia sposób usuwania subskrypcji o nazwie `HighMessages` z `mytopic` tematu:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, kiedy znasz już podstawy tematów usługi Service Bus, skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].
* Odwołanie do [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
