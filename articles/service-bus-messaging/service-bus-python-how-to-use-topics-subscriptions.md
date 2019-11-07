---
title: 'Szybki Start: jak używać tematów Azure Service Bus w języku Python'
description: 'Szybki Start: informacje dotyczące korzystania z Azure Service Bus tematów i subskrypcji w języku Python.'
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 8f7d47879a025742dbca6a5cafa634899e60ee68
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719179"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Szybki Start: jak używać tematów Service Bus i subskrypcji w języku Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano sposób używania tematów i subskrypcji usługi Service Bus. Przykłady są zapisywane w języku Python i używają [pakietu SDK języka Python platformy Azure][Azure Python package]. Omówione scenariusze obejmują:

- Tworzenie tematów i subskrypcji 
- Tworzenie filtrów subskrypcji 
- Wysyłanie komunikatów do tematu 
- Otrzymywanie komunikatów z subskrypcji
- Usuwanie tematów i subskrypcji

## <a name="prerequisites"></a>Wymagania wstępne
1. Subskrypcja platformy Azure. Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Możesz aktywować korzyści dla [subskrybentów programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Wykonaj kroki opisane w [przewodniku szybki start: użyj Azure Portal, aby utworzyć temat Service Bus i subskrypcje w temacie](service-bus-quickstart-topics-subscriptions-portal.md) w celu utworzenia **przestrzeni nazw** Service Bus i pobrania **parametrów połączenia**.

    > [!NOTE]
    > W tym przewodniku szybki start utworzysz **temat** i **subskrypcję** tematu przy użyciu języka **Python** . 
3. Zainstaluj [pakiet platformy Azure][Azure Python package]w języku Python. Zobacz [Przewodnik instalacji języka Python](/azure/python/python-sdk-azure-install).

## <a name="create-a-topic"></a>Tworzenie tematu

Obiekt **ServiceBusService** umożliwia współpracę z tematami. Dodaj następujący kod w górnej części każdego pliku języka Python, do którego chcesz programowo uzyskać dostęp Service Bus:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Poniższy kod tworzy obiekt **ServiceBusService** . Zastąp `mynamespace`, `sharedaccesskeyname`i `sharedaccesskey` rzeczywistą przestrzenią nazw, nazwą klucza sygnatury dostępu współdzielonego (SAS) i wartością klucza.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Można uzyskać wartości dla nazwy i wartości klucza sygnatury dostępu współdzielonego z [Azure Portal][Azure portal].

```python
bus_service.create_topic('mytopic')
```

Metoda `create_topic` obsługuje również dodatkowe opcje, które umożliwiają przesłonięcie domyślnych ustawień tematu, takich jak czas wygaśnięcia komunikatów lub maksymalny rozmiar tematu. W poniższym przykładzie ustawiono maksymalny rozmiar tematu do 5 GB, a wartość czasu wygaśnięcia (TTL) wynosi jedna minuta:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji

Subskrypcje dotyczące tematów również są tworzone za pomocą obiektu **ServiceBusService** . Subskrypcje mają nazwę i mogą mieć opcjonalny filtr, który ogranicza zestaw komunikatów dostarczonych do kolejki wirtualnej subskrypcji.

> [!NOTE]
> Domyślnie subskrypcje są trwałe i będą nadal istnieć do momentu, aż zostaną usunięte lub temat, do którego subskrybujesz.
> 
> Subskrypcje mogą być automatycznie usuwane przez ustawienie [Właściwości auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Tworzenie subskrypcji z filtrem domyślnym (MatchAll)

Jeśli podczas tworzenia nowej subskrypcji nie zostanie określony żaden filtr, zostanie użyty filtr **MatchAll** (domyślnie). Gdy filtr **MatchAll** jest używany, wszystkie komunikaty opublikowane w temacie są umieszczane w kolejce wirtualnej subskrypcji. Poniższy przykład tworzy subskrypcję o nazwie `AllMessages` i używa domyślnego filtru **MatchAll** .

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Tworzenie subskrypcji z filtrami

Można również zdefiniować filtry, które umożliwiają określenie, które komunikaty wysyłane do tematu powinny być wyświetlane w ramach subskrypcji określonego tematu.

Najbardziej elastycznym typem filtru obsługiwanym przez subskrypcje jest obiekt **sqlfilter**, który IMPLEMENTUJE podzestaw standardu SQL92. Filtry SQL działają na właściwościach komunikatów, które są publikowane do tematu. Aby uzyskać więcej informacji na temat wyrażeń, które mogą być używane z filtrem SQL, zobacz składnię [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Filtry do subskrypcji można dodać przy użyciu metody **tworzenia reguły\_** obiektu **ServiceBusService** . Ta metoda umożliwia dodanie nowych filtrów do istniejącej subskrypcji.

> [!NOTE]
> Ponieważ domyślny filtr jest automatycznie stosowany do wszystkich nowych subskrypcji, należy najpierw usunąć filtr domyślny lub **MatchAll** zastępuje wszystkie inne filtry, które można określić. Można usunąć regułę domyślną przy użyciu metody `delete_rule` obiektu **ServiceBusService** .
> 
> 

Poniższy przykład tworzy subskrypcję o nazwie `HighMessages` przy użyciu elementu **sqlfilter** , który wybiera tylko komunikaty o niestandardowej właściwości `messagenumber` większej niż 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Podobnie Poniższy przykład tworzy subskrypcję o nazwie `LowMessages` z obiektem **sqlfilter** , który wybiera tylko komunikaty o właściwości `messagenumber` mniejszej lub równej 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Teraz, gdy wiadomość jest wysyłana do `mytopic` jest ona zawsze dostarczana do odbiorców, którzy zasubskrybują subskrypcję tematu **AllMessages** , i wybiórczo dostarczane do odbiorników subskrybowanych w temacie **HighMessages** i **LowMessages** subskrypcje (w zależności od zawartości wiadomości).

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu

Aby wysłać komunikat do Service Bus tematu, aplikacja musi używać metody `send_topic_message` obiektu **ServiceBusService** .

W poniższym przykładzie pokazano, jak wysłać pięć wiadomości testowych do `mytopic`. Wartość właściwości `messagenumber` każdego komunikatu różni się w zależności od iteracji pętli (określa, które subskrypcje otrzymują ją):

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messagenumber': i})
    bus_service.send_topic_message('mytopic', msg)
```

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma żadnego limitu liczby komunikatów w temacie, ale jest ograniczenie całkowitego rozmiaru komunikatów przechowywanych przez temat. Rozmiar tematu jest definiowany w czasie tworzenia, z górnym limitem 5 GB. Aby uzyskać więcej informacji na temat przydziałów, zobacz [Service Bus przydziały][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie komunikatów z subskrypcji

Komunikaty są odbierane z subskrypcji przy użyciu metody `receive_subscription_message` w obiekcie **ServiceBusService** :

```python
msg = bus_service.receive_subscription_message(
    'mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Komunikaty są usuwane z subskrypcji, ponieważ są odczytywane, gdy parametr `peek_lock` ma **wartość FAŁSZ**. Można odczytać (wgląd) i zablokować komunikat bez usuwania go z kolejki, ustawiając parametr `peek_lock` na **true**.

Zachowanie odczytu i usunięcia komunikatu w ramach operacji odbierania jest najprostszym modelem i najlepiej sprawdza się w scenariuszach, w których aplikacja może tolerować nieprzetwarzanie komunikatu w przypadku wystąpienia błędu. Aby zrozumieć to zachowanie, rozważ scenariusz, w którym odbiorca wystawia żądanie odebrania, a następnie ulega awarii przed jego przetworzeniem. Ponieważ Service Bus oznaczył komunikat jako używany, wtedy, gdy aplikacja zostanie ponownie uruchomiona i rozpocznie korzystanie z komunikatów, zostanie pominięty komunikat, który był używany przed awarią.

Jeśli parametr `peek_lock` ma **wartość true**, odbieranie staje się operacją dwuetapową, co umożliwia obsługę aplikacji, które nie mogą tolerować brakujących komunikatów. Gdy usługa Service Bus odbiera żądanie, znajduje następny komunikat do wykorzystania, blokuje go w celu uniemożliwienia innym klientom odebrania go i zwraca go do aplikacji. Gdy aplikacja zakończy przetwarzanie komunikatu (lub zapisuje ją w sposób niezawodny w przyszłości), kończy drugi etap procesu odbierania przez wywołanie `delete` metody dla obiektu **Message** . Metoda `delete` oznacza komunikat jako używany i usuwa go z subskrypcji.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Sposób obsługi awarii aplikacji i komunikatów niemożliwych do odczytania

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu z jakiegoś powodu, może wywołać metodę `unlock` obiektu **Message** . Ta metoda powoduje, że Service Bus odblokowania wiadomości w ramach subskrypcji i udostępnienie jej do ponownego odebrania przez tę samą aplikację lub przez inną aplikację, która korzysta z aplikacji.

Istnieje również limit czasu skojarzony z komunikatem zablokowanym w ramach subskrypcji i jeśli aplikacja nie może przetworzyć komunikatu przed upływem limitu czasu blokady (na przykład jeśli awaria aplikacji), Service Bus odblokować komunikat automatycznie i sprawia, że jest on dostępny do ponownego odebrania.

W przypadku awarii aplikacji po przetworzeniu komunikatu, ale przed wywołaniem metody `delete`, komunikat zostanie ponownie dostarczony do aplikacji po jej ponownym uruchomieniu. Takie zachowanie jest często wywoływane. Co najmniej raz przetwarzanie\*; oznacza to, że każdy komunikat jest przetwarzany co najmniej raz, ale w pewnych sytuacjach może zostać ponownie dostarczony ten sam komunikat. Jeśli scenariusz nie toleruje dwukrotnego przetwarzania, deweloperzy aplikacji powinni dodać dodatkową logikę do swojej aplikacji w celu obsługi dwukrotnego dostarczania komunikatów. W tym celu można użyć właściwości **MessageID** komunikatu, która pozostaje stała między kolejnymi próbami dostarczenia.

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji

Tematy i subskrypcje są trwałe, chyba że [Właściwość auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) jest ustawiona. Można je usunąć za pomocą [Azure Portal][Azure portal] lub programowo. Poniższy przykład pokazuje, jak usunąć temat o nazwie `mytopic`:

```python
bus_service.delete_topic('mytopic')
```

Usunięcie tematu powoduje również usunięcie subskrypcji, które są zarejestrowane z tematem. Subskrypcje mogą być również usuwane niezależnie. Poniższy kod przedstawia sposób usuwania subskrypcji o nazwie `HighMessages` z tematu `mytopic`:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> Za pomocą [eksploratora Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/)można zarządzać zasobami Service Bus. Eksplorator Service Bus umożliwia użytkownikom łączenie się z przestrzenią nazw Service Bus i administrowanie jednostkami obsługi komunikatów w prosty sposób. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcja importowania/eksportowania lub możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już podstawy Service Bus tematów, Skorzystaj z poniższych linków, aby dowiedzieć się więcej.

* Zobacz [kolejki, tematy i subskrypcje][Queues, topics, and subscriptions].
* Odwołanie do elementu [sqlfilter. sqlexpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
