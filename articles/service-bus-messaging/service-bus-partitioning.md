---
title: Tworzenie partycjonowanych kolejek usługi Azure Service Bus i tematy | Dokumentacja firmy Microsoft
description: Opisuje sposób partycji tematów i kolejek usługi Service Bus przy użyciu kilku brokerami.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2019
ms.author: aschhab
ms.openlocfilehash: 699581c7ccd3f36da0cd0c1def623607b7c0a13b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589676"
---
# <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Usługa Azure Service Bus wykorzystuje kilku brokerami do przetwarzania komunikatów i wiele magazynów obsługi komunikatów do przechowywania komunikatów. Konwencjonalne kolejki lub tematu są obsługiwane przez brokera pojedynczy komunikat i przechowywane w jeden Magazyn obsługi komunikatów. Usługa Service Bus *partycje* Włączanie kolejek i tematów, lub *jednostki obsługi komunikatów*, aby można podzielić na partycje w wielu brokerami i magazynami komunikatów. Partycjonowanie oznacza, że ogólną przepływność jednostki podzielonej na partycje nie jest już ograniczone przez wydajności pojedynczego brokera lub magazynu komunikatów. Ponadto tymczasowe awarii magazynu komunikatów nie jest renderowana podzieleniu kolejki lub tematu niedostępny. Partycjonowane kolejki i tematy może zawierać wszystkie zaawansowane funkcje usługi Service Bus, takie jak obsługa transakcji i sesji.

> [!NOTE]
> Partycjonowanie jest dostępna podczas tworzenia jednostki dla wszystkich kolejek i tematów w Basic lub standardowej jednostki SKU. Nie jest dostępna dla wersji Premium, komunikatów jednostki SKU, ale wcześniej istniejących partycjonowane jednostki w przestrzeni nazw Premium w dalszym ciągu działać zgodnie z oczekiwaniami.
 
Nie jest możliwe zmienić opcję partycjonowania na dowolnym istniejącej kolejki lub tematu; opcję można ustawić tylko podczas tworzenia jednostki.

## <a name="how-it-works"></a>Jak to działa

Każdy podzieleniu kolejki lub tematu składa się z wielu partycji. Każdej partycji są przechowywane w różnych Magazyn obsługi komunikatów i obsługiwane przez brokera inny komunikat. Gdy wiadomość jest wysyłana na podzieleniu kolejki lub tematu, Service Bus przydziela ten komunikat do jednej z partycji. Wybór odbywa się losowo przez usługę Service Bus lub za pomocą klucza partycji, określające nadawcę.

Gdy klient chce komunikat o błędzie z podzieleniu kolejki lub subskrypcji podzielonym na partycje tematu usługi Service Bus zapytania wszystkich partycji dla komunikatów, funkcja zwraca pierwszy komunikat, który jest uzyskiwana z dowolnego magazyny obsługi komunikatów do odbiorcy. Pamięci podręczne usługi Service Bus, innych komunikatów i zwraca je po odebraniu dodatkowe odbierania żądań. Odbieranie klienta nie ma informacji o partycjonowanie; zachowanie ukierunkowane na klienta podzieleniu kolejki lub tematu (np. odczytywać, wykonaj, odroczone utraconych wiadomości, pobieranie z wyprzedzeniem) jest taka sama jak zachowanie regularne jednostki.

Nie ma żadnych dodatkowych kosztów, podczas wysyłania wiadomości do lub odebranie komunikatu z podzieleniu kolejki lub tematu.

## <a name="enable-partitioning"></a>Włącz partycjonowanie

Za pomocą partycjonowane kolejki i tematy usługi Azure Service Bus, należy użyć zestawu Azure SDK w wersji 2,2 lub nowszej, albo określić `api-version=2013-10` lub nowszym w żądaniach HTTP.

### <a name="standard"></a>Standardowa (Standard)

W warstwie standardowej obsługi komunikatów można utworzyć kolejki usługi Service Bus i tematy w 1, 2, 3, 4 lub 5 GB rozmiarów (wartość domyślna to 1 GB). Z podziałem na partycje włączone, Usługa Service Bus tworzy kopie 16 (16 partycje) jednostki, każdy taki sam rozmiar określony. Jako takie, tworząc kolejki, która ma rozmiar 5 GB, z 16 partycji maksymalny rozmiar kolejki staje się (5 \* 16) = 80 GB. Maksymalny rozmiar podzieleniu kolejki lub tematu widoczne, analizując jego wpis [witryny Azure portal][Azure portal]w **Przegląd** bloku dla tej jednostki.

### <a name="premium"></a>Premium

W danej przestrzeni nazw warstwy Premium partycjonowania jednostki nie są obsługiwane. Jednakże można nadal utworzyć tematów i kolejek usługi Service Bus w 1, 2, 3, 4, 5, 10, 20, 40 lub rozmiary 80 GB (wartość domyślna to 1 GB). Zobaczysz rozmiar kolejki lub tematu, analizując jego wpis [witryny Azure portal][Azure portal]w **Przegląd** bloku dla tej jednostki.

### <a name="create-a-partitioned-entity"></a>Tworzenie jednostki podzielonej na partycje

Istnieje kilka sposobów tworzenia podzieleniu kolejki lub tematu. Podczas tworzenia kolejki lub tematu z aplikacji, aby umożliwić partycjonowanie kolejki lub tematu odpowiednio ustawiając [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] lub [ TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] właściwości **true**. Te właściwości należy ustawić podczas tworzenia kolejki lub tematu i są dostępne tylko w starszej wersji [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) biblioteki. Jak wspomniano wcześniej, nie jest możliwa zmiana tych właściwości na podstawie istniejącej kolejki lub tematu. Na przykład:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternatywnie można utworzyć podzieleniu kolejki lub tematu w [witryny Azure portal][Azure portal]. Podczas tworzenia kolejki lub tematu w portalu **Włącz partycjonowanie** opcji kolejki lub tematu **Utwórz** okno dialogowe jest zaznaczone domyślnie. Możesz tylko tę opcję można wyłączyć w jednostce w warstwie standardowa; w warstwie Premium partycjonowanie nie jest obsługiwany, a pole wyboru nie ma wpływu. 

## <a name="use-of-partition-keys"></a>Korzystanie z kluczy partycji

Komunikat po umieszczonych w kolejce na podzieleniu kolejki lub tematu usługi Service Bus sprawdza obecność klucza partycji. Jeśli zostanie znaleziony, wybiera partycji na podstawie tego klucza. Jeśli nie znajdzie klucza partycji, wybiera partycji, w oparciu o wewnętrznego algorytmu.

### <a name="using-a-partition-key"></a>Użycie klucza partycji

Niektóre scenariusze, takie jak sesji lub transakcji, wymagają wiadomości, które mają być przechowywane w określonej partycji. Te scenariusze wymagają użycia klucza partycji. Wszystkie komunikaty, które używają tego samego klucza partycji są przypisane do tej samej partycji. Jeśli partycja jest tymczasowo niedostępny, Usługa Service Bus zwraca błąd.

W zależności od scenariusza inny komunikat właściwości są używane jako klucza partycji:

**Identyfikator sesji**: Jeśli wiadomość ma [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) właściwością, a następnie używa usługi Service Bus **SessionID** jako klucza partycji. Dzięki temu wszystkie komunikaty, które należą do tej samej sesji są obsługiwane przez ten sam brokera komunikatów. Sesje umożliwiają usługi Service Bus zagwarantować, komunikat zamawiania oraz spójność stanów sesji.

**PartitionKey**: Jeśli wiadomość ma [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) właściwości, ale nie [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) właściwością, a następnie używa usługi Service Bus [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) wartości właściwości jako klucz partycji. Jeśli wiadomość zawiera zarówno [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) i [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) zestaw właściwości, obie te właściwości muszą być identyczne. Jeśli [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) właściwość jest ustawiona na wartość inną niż [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) wyjątku dotyczącego nieprawidłowej operacji zwraca wartość właściwości, usługi Service Bus. [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) właściwość powinna być używana, jeśli nadawca wysyła pamiętać wiadomości transakcyjnych poza sesją. Klucz partycji gwarantuje, że wszystkie komunikaty, które są wysyłane w ramach transakcji są obsługiwane przez ten sam brokera obsługi komunikatów.

**Identyfikator komunikatu**: Jeśli kolejka lub temat [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) właściwością **true** i [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) lub [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) Nie ustawiono właściwości, a następnie [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) wartość właściwości służy jako klucz partycji. (Biblioteki Microsoft .NET i protokołu AMQP automatycznie przypisywać identyfikator wiadomości, jeśli nie ma aplikacji wysyłającej.) W takim przypadku wszystkie kopie tego samego komunikatu są obsługiwane przez ten sam brokera komunikatów. Ten identyfikator umożliwia usługi Service Bus, wykrywanie i eliminowanie zduplikowanych komunikatów. Jeśli [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) nie ustawiono właściwości **true**, Service Bus nie należy wziąć pod uwagę [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) właściwość jako klucz partycji.

### <a name="not-using-a-partition-key"></a>Nie używa klucza partycji

W przypadku braku klucza partycji usługi Service Bus dystrybuuje wiadomości w okrężne wszystkich partycji podzieleniu kolejki lub tematu. Jeśli wybranej partycji nie jest dostępna, usługi Service Bus przydziela ten komunikat do różnych partycji. W ten sposób wysyłania powiedzie się niezależnie od tymczasową niedostępność Magazyn obsługi komunikatów. Jednak nie będzie osiągnąć gwarantowaną kolejność, który zawiera klucz partycji.

Aby uzyskać bardziej szczegółowe omówienie zależnościami między dostępnością (nie klucz partycji) i spójności (przy użyciu klucza partycji), zobacz [w tym artykule](../event-hubs/event-hubs-availability-and-consistency.md). Te informacje dotyczą równie partycjonowane jednostki usługi Service Bus.

Aby zapewnić usługi Service Bus wystarczająco dużo czasu można umieścić w kolejce wiadomości do innej partycji, [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) wartość określoną przez klienta, który wysyła wiadomości musi być większa niż 15 sekund. Zalecane jest, aby ustawić [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) właściwość domyślną wartość 60 sekund.

Klucz partycji "Przypina" komunikat do określonej partycji. Jeśli Magazyn obsługi komunikatów, który przechowuje tę partycję jest niedostępny, Usługa Service Bus zwraca błąd. W przypadku braku klucza partycji usługi Service Bus można wybrać innej partycji, a operacja powiedzie się. Dlatego zalecane jest, że nie zostanie podany klucz partycji, chyba że jest to wymagane.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tematy zaawansowane: transakcji za pomocą partycjonowane jednostki

Komunikaty wysyłane w ramach transakcji muszą określać klucz partycji. Klucz może być jednym z następujących właściwości: [Identyfikator sesji](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), lub [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Wszystkie komunikaty, które są wysyłane w ramach tej samej transakcji, należy określić ten sam klucz partycji. Jeśli spróbujesz wysłać wiadomość bez klucza partycji w ramach transakcji usługi Service Bus zwraca wyjątku dotyczącego nieprawidłowej operacji. Usługa Service Bus, jeśli użytkownik spróbuje wysłać wiele wiadomości w ramach tej samej transakcji, o różnych kluczach partycji, zwraca wyjątku dotyczącego nieprawidłowej operacji. Na przykład:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Jeśli dowolne z właściwości, które służą jako klucza partycji są skonfigurowane, Usługa Service Bus Przypina komunikatu do określonej partycji. Dzieje się tak, czy transakcja jest używany. Zaleca się, że nie określisz klucza partycji, jeśli nie jest konieczne.

## <a name="using-sessions-with-partitioned-entities"></a>Korzystanie z sesji przy użyciu partycjonowane jednostki

Wiadomość do wysłania wiadomości transakcyjnych do sesji aware tematu lub kolejki, musi mieć [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) zestawu właściwości. Jeśli [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) również określić właściwość, musi być taka sama jak [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) właściwości. Usługa Service Bus, jeśli będą się różnić, zwraca wyjątku dotyczącego nieprawidłowej operacji.

W przeciwieństwie do regularnego (niepartycjonowana) kolejek lub tematów nie jest możliwe, używaj jednej transakcji wysyłać wielu różnych sesji. Usługa Service Bus, jeśli próba, zwraca wyjątku dotyczącego nieprawidłowej operacji. Na przykład:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Przekazywanie komunikatów automatyczne przy użyciu partycjonowane jednostki

Usługa Service Bus obsługuje wiadomości automatycznego przekazywania z, aby nie lub między partycjonowane jednostki. Aby włączyć przekazywanie komunikatów automatycznego, należy ustawić [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] właściwości kolejki źródłowej lub subskrypcji. Jeśli komunikat określa klucz partycji ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), lub [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), ten klucz partycji służy do jednostki docelowej.

## <a name="considerations-and-guidelines"></a>Zagadnienia i wytyczne
* **Wysoka spójność funkcji**: Jeśli jednostka korzysta z funkcji, takich jak sesje, wykrywania duplikatów lub jawną kontrolę partycjonowania klucza, operacji obsługi komunikatów są zawsze kierowane do określonej partycji. Jeśli środowisko żadnej partycji dużego ruchu lub źródłowy magazyn jest w złej kondycji, te operacje kończą się niepowodzeniem, i zmniejsza dostępności. Generalnie spójności jest nadal znacznie wyższa niż niepartycjonowana jednostki; tylko część ruchu ma problemy, a nie cały ruch. Aby uzyskać więcej informacji, zobacz ten [dyskusję na temat dostępności i spójności](../event-hubs/event-hubs-availability-and-consistency.md).
* **Zarządzanie**: Operacje, takie jak tworzenie, Update i Delete muszą być wykonywane na wszystkich partycjach jednostki. Jeśli każda partycja jest w złej kondycji, może to spowodować błędy dla tych operacji. Dla operacji pobierania informacji takich jak liczby wiadomości musi być agregowana ze wszystkich partycji. Jeśli każda partycja jest w złej kondycji, stan dostępności jednostki jest zgłaszany jako ograniczone.
* **Mało scenariuszy komunikat zbiorczej**: W przypadku takich scenariuszy, zwłaszcza w przypadku korzystania z protokołu HTTP, może być konieczne wykonanie wielu operacji pobrania, aby można było uzyskać wszystkie komunikaty. W przypadku odbierania żądań frontonu wykonuje odbioru na wszystkie partycje i przechowuje wszystkie odpowiedzi. Żądanie kolejnych odbioru w ramach tego samego połączenia będą korzystać z tej pamięci podręcznej i odbierać opóźnienia będzie niższa. Jednak jeśli masz wiele połączeń lub korzystać z protokołu HTTP, która ustanawia nowego połączenia dla każdego żądania. W efekcie nie ma żadnej gwarancji, że jej spowoduje przejście na tym samym węźle. Jeśli wszystkie istniejące komunikaty są zablokowane i buforowane w innym frontonu, operacja odbioru zwraca **null**. Po pewnym czasie wygaśnięcia komunikatów i można odbierać je ponownie. Zaleca się utrzymanie aktywności HTTP.
* **Przeglądaj/Odbierz komunikaty**: Dostępne tylko w starszej wersji [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) biblioteki. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nie zawsze zwraca liczby komunikatów określonej w [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) właściwości. Istnieją dwie typowe przyczyny to zachowanie. Jedną z przyczyn jest to, że rozmiar zagregowanych kolekcję komunikatów przekracza maksymalny rozmiar wynoszący 256 KB. Inną przyczyną jest to, że jeśli kolejka lub temat [właściwość EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) równa **true**, partycji może nie mieć wystarczającej liczby wiadomości do wykonania żądanej liczby komunikatów. Ogólnie rzecz biorąc, jeśli aplikacja chce otrzymywać określoną liczbę wiadomości, powinien wywoływać [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) wielokrotnie, dopóki nie otrzymuje tę liczbę wiadomości lub Brak dalszych komunikatów do wglądu. Aby uzyskać więcej informacji, w tym przykłady kodu, zobacz [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) lub [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) dokumentacji interfejsu API.

## <a name="latest-added-features"></a>Najnowsze funkcje dodane

* Dodawanie lub usuwanie reguły jest teraz obsługiwana za pomocą partycjonowane jednostki. Inny niż-partycjonowane jednostki, te operacje nie są obsługiwane w ramach transakcji. 
* Protokół AMQP jest teraz obsługiwana dla wysyłania i odbierania wiadomości do i z jednostki podzielonej na partycje.
* Protokół AMQP jest teraz obsługiwana w przypadku następujących operacji: [Batch Send](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [otrzymywać partii](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [wyświetlony numer porządkowy](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [wgląd](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Odnów blokadę](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [harmonogramu Komunikat](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [anulowanie zaplanowanych wiadomości](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Dodaj regułę](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Usuń regułę](/dotnet/api/microsoft.azure.servicebus.ruledescription), [sesji Odnów blokadę](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [ Ustaw stan sesji](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [stanu sesji Get](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), i [Wyliczanie sesji](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Ograniczenia partycjonowane jednostki

Obecnie Usługa Service Bus nakłada następujące ograniczenia dotyczące partycjonowane kolejki i tematy:

* Partycjonowane kolejki i tematy nie są obsługiwane w warstwie Premium obsługi komunikatów. Sesje są obsługiwane w warstwie Premium przy użyciu identyfikatora sesji. 
* Partycjonowane kolejki i tematy nie obsługują wysyłanie wiadomości, które należą do różnych sesji w ramach jednej transakcji.
* Obecnie w usłudze Service Bus można utworzyć maksymalnie 100 partycjonowanych kolejek lub tematów w każdej przestrzeni nazw. Każdy podzieleniu kolejki lub tematu, liczy się do limitu przydziału 10 000 jednostek na obszar nazw (nie ma zastosowania do warstwy Premium).

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj o podstawowych pojęciach protokołu AMQP 1.0, obsługi komunikatów w specyfikację [przewodnik dotyczący protokołu AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
