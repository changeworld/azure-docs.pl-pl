---
title: 'Szybki start: używanie tematów i subskrypcji usługi Azure Service Bus w języku Python'
description: W tym artykule pokazano, jak utworzyć temat usługi Azure Service Bus, subskrypcja, wysyłać wiadomości do tematu i odbierać wiadomości z subskrypcji.
services: service-bus-messaging
documentationcenter: python
author: axisc
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 4745d675086f1b07bf7fccf17c14c76e4b18fba2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478072"
---
# <a name="quickstart-use-service-bus-topics-and-subscriptions-with-python"></a>Szybki start: używanie tematów i subskrypcji usługi Service Bus w języku Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

W tym artykule opisano sposób używania języka Python z usługą Azure Service Bus tematy i subskrypcji. Przykłady używają pakietu [zestawu SDK języka Azure Python][Azure Python package] do: 

- Tworzenie tematów i subskrypcji tematów
- Tworzenie filtrów i reguł subskrypcji
- Wysyłanie wiadomości do tematów 
- Odbieranie wiadomości z subskrypcji
- Usuwanie tematów i subskrypcji

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja platformy Azure. Możesz aktywować [korzyści dla subskrybenta programu Visual Studio lub MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) lub założyć [bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Obszar nazw usługi Service Bus utworzony przez wykonując kroki opisane w [przewodniku Szybki start: Tworzenie tematu i subskrypcji usługi Service Bus za pomocą portalu Azure.](service-bus-quickstart-topics-subscriptions-portal.md) Skopiuj nazwę obszaru nazw, nazwę klucza dostępu udostępnionego i wartość klucza podstawowego na ekranie **Zasady dostępu udostępnionego,** aby użyć go w dalszej części tego przewodnika Szybki start. 
- Python 3.4x lub wyższy, z zainstalowanym pakietem [zestawu Azure Python SDK.][Azure Python package] Aby uzyskać więcej informacji, zobacz [Podręcznik instalacji języka Python](/azure/developer/python/azure-sdk-install).

## <a name="create-a-servicebusservice-object"></a>Tworzenie obiektu usługi ServiceBusService

**Obiekt Usługi ServiceBusService** umożliwia pracę z tematami i subskrypcjami tematów. Aby programowo uzyskać dostęp do usługi Service Bus, dodaj następujący wiersz w górnej części pliku języka Python:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Dodaj następujący kod, aby utworzyć **obiekt Usługi ServiceBusService.** Zamień `<namespace>` `<sharedaccesskeyvalue>` i `<sharedaccesskeyname>`nazwę obszaru nazw usługi Service Bus, nazwę klucza sygnatury dostępu współdzielonego (SAS) i wartość klucza podstawowego. Te wartości można znaleźć w obszarze **Zasady dostępu współdzielonego** w obszarze nazw usługi Service Bus w [witrynie Azure portal][Azure portal].

```python
bus_service = ServiceBusService(
    service_namespace='<namespace>',
    shared_access_key_name='<sharedaccesskeyname>',
    shared_access_key_value='<sharedaccesskeyvalue>')
```

## <a name="create-a-topic"></a>Tworzenie tematu

Poniższy kod używa `create_topic` metody do tworzenia tematu usługi Service Bus o nazwie `mytopic`, z ustawieniami domyślnymi:

```python
bus_service.create_topic('mytopic')
```

Za pomocą opcji tematu można zastąpić domyślne ustawienia tematu, takie jak czas wygaśnięcia wiadomości (TTL) lub maksymalny rozmiar tematu. Poniższy przykład tworzy `mytopic` temat o nazwie o maksymalnym rozmiarze tematu 5 GB i domyślnym tlt wiadomości jednej minuty:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Tworzenie subskrypcji

Obiekt **ServiceBusService** służy również do tworzenia subskrypcji tematów. Subskrypcja może mieć filtr, aby ograniczyć zestaw wiadomości dostarczonych do kolejki wirtualnej. Jeśli nie określisz filtru, nowe subskrypcje użyją domyślnego **filtru MatchAll,** który umieszcza wszystkie wiadomości opublikowane w temacie w kolejce wirtualnej subskrypcji. Poniższy przykład tworzy `mytopic` subskrypcję o nazwie, `AllMessages` która używa filtru **MatchAll:**

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="use-filters-with-subscriptions"></a>Używanie filtrów z subskrypcjami

Użyj `create_rule` metody **ServiceBusService** obiektu do filtrowania wiadomości, które pojawiają się w subskrypcji. Podczas tworzenia subskrypcji można określić reguły lub dodać reguły do istniejących subskrypcji.

Najbardziej elastycznym typem filtru jest **SqlFilter**, który używa podzbioru SQL-92. Filtry SQL działają na podstawie właściwości wiadomości opublikowanych w temacie. Aby uzyskać więcej informacji na temat wyrażeń, których można używać z filtrem SQL, zobacz składnię [SqlFilter.SqlExpression.][SqlFilter.SqlExpression]

Ponieważ domyślny filtr **MatchAll** stosuje się automatycznie do wszystkich nowych subskrypcji, należy usunąć go z subskrypcji, które chcesz filtrować, lub **MatchAll** zastąpi wszystkie inne filtry, które określisz. Regułę domyślną można usunąć `delete_rule` przy użyciu metody obiektu **ServiceBusService.**

Poniższy przykład tworzy `mytopic` subskrypcję o nazwie `HighMessages`, z regułą **SqlFilter** o nazwie `HighMessageFilter`. Reguła `HighMessageFilter` wybiera tylko wiadomości `messageposition` z właściwością niestandardową większą niż 3:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Poniższy przykład tworzy `mytopic` subskrypcję o nazwie `LowMessages`, z regułą **SqlFilter** o nazwie `LowMessageFilter`. Reguła `LowMessageFilter` wybiera tylko wiadomości `messageposition` z właściwością mniejszą lub równą 3:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messageposition <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Z `AllMessages` `HighMessages`, `LowMessages` i wszystko w efekcie, wiadomości wysyłane do `mytopic` `AllMessages` są zawsze dostarczane do odbiorców subskrypcji. Wiadomości są również selektywnie `HighMessages` `LowMessages` dostarczane do lub subskrypcji, `messageposition` w zależności od wartości właściwości wiadomości. 

## <a name="send-messages-to-a-topic"></a>Wysyłanie komunikatów do tematu

Aplikacje `send_topic_message` używają metody **ServiceBusService** obiektu do wysyłania wiadomości do usługi Service Bus tematu.

Poniższy przykład wysyła pięć `mytopic` komunikatów testowych do tematu. Wartość `messageposition` właściwości niestandardowej zależy od iteracji pętli i określa, które subskrypcje odbierają wiadomości. 

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'),
                  custom_properties={'messageposition': i})
    bus_service.send_topic_message('mytopic', msg)
```

### <a name="message-size-limits-and-quotas"></a>Limity rozmiaru wiadomości i przydziały

Tematy usługi Service Bus obsługują maksymalny rozmiar komunikatu 256 KB w [warstwie Standardowa](service-bus-premium-messaging.md) i 1 MB w [warstwie Premium](service-bus-premium-messaging.md). Nagłówek, który zawiera standardowe i niestandardowe właściwości aplikacji, może mieć maksymalny rozmiar 64 KB. Nie ma limitu liczby wiadomości, które może pomieścić temat, ale istnieje ograniczenie całkowitego rozmiaru wiadomości, które zawiera temat. Rozmiar tematu można zdefiniować w czasie tworzenia, z górnym limitem 5 GB. 

Aby uzyskać więcej informacji na temat przydziałów, zobacz [Przydziały usługi Service Bus][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Odbieranie wiadomości z subskrypcji

Aplikacje `receive_subscription_message` używają metody w **obiekcie ServiceBusService** do odbierania wiadomości z subskrypcji. Poniższy przykład odbiera `LowMessages` wiadomości z subskrypcji i usuwa je podczas ich odczytywania:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Opcjonalny `peek_lock` parametr `receive_subscription_message` określa, czy usługa Service Bus usuwa wiadomości z subskrypcji podczas ich odczytywania. Domyślnym trybem odbierania wiadomości jest `peek_lock` *PeekLock*lub ustawiona na **True**, która odczytuje (wgląd) i blokuje wiadomości bez usuwania ich z subskrypcji. Każda wiadomość musi być jawnie wypełniona, aby usunąć ją z subskrypcji.

Aby usunąć wiadomości z subskrypcji podczas ich odczytywania, można ustawić `peek_lock` parametr false , jak w poprzednim przykładzie. **False** Usuwanie wiadomości w ramach operacji odbierania jest najprostszym modelem i działa poprawnie, jeśli aplikacja może tolerować brakujące komunikaty, jeśli występuje błąd. Aby zrozumieć to zachowanie, należy wziąć pod uwagę scenariusz, w którym aplikacja wystawia żądanie odbierania, a następnie ulega awarii przed przetworzeniem. Jeśli wiadomość została usunięta po odebraniu, gdy aplikacja zostanie ponownie uruchomiona i zacznie ponownie zużywać wiadomości, nie odebrano wiadomości odebranej przed awarią.

Jeśli aplikacja nie toleruje nieodebranych wiadomości, odbieranie staje się operacją dwuetapową. PeekLock znajduje następną wiadomość do korzystania, blokuje go, aby uniemożliwić innym konsumentom odbieranie go i zwraca go do aplikacji. Po przetworzeniu lub zapisaniu wiadomości aplikacja kończy drugi etap procesu `complete` odbierania, wywołując metodę w **Message** obiektu.  Metoda `complete` oznacza komunikat jako używane i usuwa go z subskrypcji.

W poniższym przykładzie pokazano scenariusz blokady wglądu:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
    print(msg.body)
    msg.complete()
```

## <a name="handle-application-crashes-and-unreadable-messages"></a>Obsługa awarii aplikacji i nieczytelnych komunikatów

Usługa Service Bus zapewnia funkcję ułatwiającą bezpieczne odzyskiwanie w razie błędów w aplikacji lub trudności z przetwarzaniem komunikatu. Jeśli aplikacja odbiornika nie może przetworzyć komunikatu `unlock` z jakiegoś powodu, można wywołać metodę na **Message** obiektu. Usługa Service Bus odblokowuje komunikat w ramach subskrypcji i udostępnia ją do odebrania ponownie za pomocą tej samej lub innej aplikacji zużywającej.

Istnieje również limit czasu dla wiadomości zablokowanych w ramach subskrypcji. Jeśli aplikacja nie może przetworzyć wiadomości przed upływem limitu czasu blokady, na przykład w przypadku awarii aplikacji usługa Service Bus automatycznie odblokowuje wiadomość i udostępnia ją do odebraniu.

Jeśli aplikacja ulegnie awarii po `complete` przetworzeniu wiadomości, ale przed wywołaniem metody, wiadomość zostanie ponownie dostarczona do aplikacji po ponownym uruchomieniu. To zachowanie jest często nazywane *przetwarzaniem co najmniej raz.* Każda wiadomość jest przetwarzana co najmniej raz, ale w niektórych sytuacjach ta sama wiadomość może zostać ponownie dostarczona. Jeśli scenariusz nie toleruje zduplikowane przetwarzania, można użyć **MessageId** właściwości wiadomości, która pozostaje stała w całej prób dostarczania, do obsługi dostarczania zduplikowanych wiadomości. 

## <a name="delete-topics-and-subscriptions"></a>Usuwanie tematów i subskrypcji

Aby usunąć tematy i subskrypcje, użyj `delete_topic` [witryny Azure portal][Azure portal] lub metody. Poniższy kod usuwa temat `mytopic`o nazwie :

```python
bus_service.delete_topic('mytopic')
```

Usunięcie tematu powoduje usunięcie wszystkich subskrypcji tematu. Subskrypcje można również usuwać niezależnie. Poniższy kod usuwa subskrypcję o nazwie `HighMessages` z tematu: `mytopic`

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

Domyślnie tematy i subskrypcje są trwałe i istnieją do momentu ich usunięcia. Aby automatycznie usuwać subskrypcje po upływie określonego czasu, można ustawić [parametr auto_delete_on_idle](https://docs.microsoft.com/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus.models.sbsubscription?view=azure-python) w subskrypcji. 

> [!TIP]
> Zasoby usługi Service Bus można zarządzać za pomocą [Eksploratora magistrali usług](https://github.com/paolosalvatori/ServiceBusExplorer/). Eksplorator usługi Service Bus umożliwia łączenie się z obszarem nazw usługi Service Bus i łatwe administrowanie jednostkami obsługi wiadomości. Narzędzie zapewnia zaawansowane funkcje, takie jak funkcje importu/eksportu oraz możliwość testowania tematów, kolejek, subskrypcji, usług przekazywania, centrów powiadomień i centrów zdarzeń. 

## <a name="next-steps"></a>Następne kroki

Teraz, gdy poznasz podstawy tematów usługi Service Bus, skorzystaj z tych łączy, aby dowiedzieć się więcej:

* [Kolejki, tematy i subskrypcje][Queues, topics, and subscriptions]
* [Odwołanie do sqlfilter.SqlExpression][SqlFilter.SqlExpression]

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
