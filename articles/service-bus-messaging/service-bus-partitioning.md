---
title: Tworzenie podzielonych na partycje kolejek i tematów usługi Azure Service Bus | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób partycjonowania kolejek i tematów usługi Service Bus przy użyciu wielu brokerów komunikatów.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2020
ms.author: aschhab
ms.openlocfilehash: 671368993acb43c0d55eca73119effa934e3cff8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260945"
---
# <a name="partitioned-queues-and-topics"></a>Partycjonowane kolejki i tematy

Usługa Azure Service Bus zatrudnia wielu brokerów wiadomości do przetwarzania wiadomości i wielu magazynów wiadomości do przechowywania wiadomości. Konwencjonalna kolejka lub temat jest obsługiwana przez brokera pojedynczych wiadomości i przechowywana w jednym magazynie wiadomości. *Partycje* usługi Service Bus umożliwiają kolejki i tematy lub *jednostki obsługi wiadomości,* które mają być podzielone na partycje między wieloma brokerami wiadomości i magazynami obsługi wiadomości. Partycjonowanie oznacza, że ogólna przepływność jednostki podzielonej na partycje nie jest już ograniczona przez wydajność brokera pojedynczej wiadomości lub magazynu obsługi wiadomości. Ponadto tymczasowa awaria magazynu obsługi wiadomości nie powoduje, że kolejka na partycje lub temat są niedostępne. Podzielone na partycje kolejki i tematy mogą zawierać wszystkie zaawansowane funkcje usługi Service Bus, takie jak obsługa transakcji i sesji.

> [!NOTE]
> Partycjonowanie jest dostępne w tworzeniu encji dla wszystkich kolejek i tematów w jednostkach SKU basic lub Standard. Nie jest dostępna dla jednostki SKU obsługi wiadomości w udziale w udziale w udziale w udziale w udziale w udziale w udziale społeczności Premium, ale wszystkie wcześniej istniejące jednostki podzielone na partycje w przestrzeniach nazw usługi Premium nadal działają zgodnie z oczekiwaniami.
 
Nie jest możliwe, aby zmienić opcję partycjonowania w żadnej istniejącej kolejki lub tematu; można ustawić tylko podczas tworzenia encji.

## <a name="how-it-works"></a>Jak to działa

Każda kolejka lub temat podzielony na partycje składa się z wielu partycji. Każda partycja jest przechowywana w innym magazynie wiadomości i obsługiwana przez innego brokera komunikatów. Gdy wiadomość jest wysyłana do kolejki lub tematu podzielonej na partycje, usługa Service Bus przypisuje wiadomość do jednej z partycji. Wybór odbywa się losowo za pomocą usługi Service Bus lub przy użyciu klucza partycji, który nadawca może określić.

Gdy klient chce odbierać wiadomość z kolejki podzielonej na partycje lub z subskrypcji do tematu podzielonego na partycje, usługa Service Bus wysyła kwerendy do wszystkich partycji dla wiadomości, a następnie zwraca pierwszą wiadomość, która jest uzyskiwana z dowolnego magazynu obsługi wiadomości do odbiornika. Usługa Service Bus buforuje inne wiadomości i zwraca je po otrzymaniu dodatkowych żądań odbierania. Klient odbierający nie jest świadomy partycjonowania; zachowanie skierowane do klienta kolejki lub tematu podzielonego na partycje (na przykład odczyt, zakończenie, odroczenie, deadletter, prefetching) jest identyczne z zachowaniem zwykłej jednostki.

Nie ma żadnych dodatkowych kosztów podczas wysyłania wiadomości do kolejki lub tematu na partycje lub odbierania wiadomości z niej.

## <a name="enable-partitioning"></a>Włącz partycjonowanie

Aby użyć podzielonych na partycje kolejek i tematów za pomocą usługi Azure Service `api-version=2013-10` Bus, użyj narzędzia Azure SDK w wersji 2.2 lub nowszej lub określ lub później w żądaniach HTTP.

### <a name="standard"></a>Standardowa

W warstwie Wiadomości standardowej można tworzyć kolejki i tematy usługi Service Bus w rozmiarach 1, 2, 3, 4 lub 5 GB (wartość domyślna to 1 GB). Po włączeniu partycjonowania usługa Service Bus tworzy 16 kopii (16 partycji) jednostki, z których każda ma ten sam rozmiar określony. W związku z tym, jeśli utworzysz kolejkę o rozmiarze 5 GB, z \* 16 partycjami maksymalny rozmiar kolejki staje się (5 16) = 80 GB. Maksymalny rozmiar kolejki lub tematu podzielonej na partycje można zobaczyć, patrząc na jego wpis w [witrynie Azure portal][Azure portal], w **bloku Przegląd** dla tej jednostki.

### <a name="premium"></a>Premium

W obszarze nazw warstwy Premium jednostki partycjonowania nie są obsługiwane. Można jednak nadal tworzyć kolejki i tematy usługi Service Bus w rozmiarach 1, 2, 3, 4, 5, 10, 20, 40 lub 80 GB (wartość domyślna to 1 GB). Rozmiar kolejki lub tematu można zobaczyć, patrząc na jego wpis w [witrynie Azure portal][Azure portal], w **bloku Przegląd** dla tej jednostki.

### <a name="create-a-partitioned-entity"></a>Tworzenie jednostki podzielonej na partycje

Istnieje kilka sposobów tworzenia kolejki lub tematu podzielonego na partycje. Podczas tworzenia kolejki lub tematu z aplikacji można włączyć partycjonowanie kolejki lub tematu, odpowiednio ustawiając właściwość [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] lub [TopicDescription.EnablePartitioning.EnablePartitioning.][TopicDescription.EnablePartitioning] **true** Te właściwości muszą być ustawione w momencie utworzenia kolejki lub tematu i są dostępne tylko w starszej bibliotece [WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Jak wspomniano wcześniej, nie jest możliwe, aby zmienić te właściwości w istniejącej kolejce lub tematu. Przykład:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternatywnie można utworzyć kolejkę lub temat podzielony na partycje w [witrynie Azure portal][Azure portal]. Podczas tworzenia kolejki lub tematu w portalu opcja **Włącz partycjonowanie** w oknie dialogowym kolejka lub **tematu Tworzenie** jest zaznaczona domyślnie. Tę opcję można wyłączyć tylko w jednostce warstwy standardowej; w warstwie Premium partycjonowanie nie jest obsługiwane, a pole wyboru nie ma wpływu. 

## <a name="use-of-partition-keys"></a>Korzystanie z kluczy partycji

Gdy wiadomość jest umieszczana w kolejce lub temacie podzielonym na partycje, usługa Service Bus sprawdza obecność klucza partycji. Jeśli znajdzie jeden, wybiera partycję na podstawie tego klucza. Jeśli nie znajdzie klucz partycji, wybiera partycję na podstawie algorytmu wewnętrznego.

### <a name="using-a-partition-key"></a>Korzystanie z klucza partycji

Niektóre scenariusze, takie jak sesje lub transakcje, wymagają, aby wiadomości były przechowywane w określonej partycji. Wszystkie te scenariusze wymagają użycia klucza partycji. Wszystkie komunikaty, które używają tego samego klucza partycji są przypisane do tej samej partycji. Jeśli partycja jest tymczasowo niedostępna, usługa Service Bus zwraca błąd.

W zależności od scenariusza różne właściwości wiadomości są używane jako klucz partycji:

**SessionId**: Jeśli komunikat ma ustawioną właściwość [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) usługa Service Bus używa **identyfikatora sesji** jako klucza partycji. W ten sposób wszystkie wiadomości, które należą do tej samej sesji są obsługiwane przez tego samego brokera wiadomości. Sesje umożliwiają usługę Service Bus, aby zagwarantować kolejność wiadomości, a także spójność stanów sesji.

**PartitionKey**: Jeśli komunikat ma właściwość [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ale nie ustawioną właściwość [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) usługa Service Bus używa wartości właściwości [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) jako klucza partycji. Jeśli komunikat ma zestaw właściwości [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) i [PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) obie właściwości muszą być identyczne. Jeśli [właściwość PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) jest ustawiona na inną wartość niż [sessionid](/dotnet/api/microsoft.azure.servicebus.message.sessionid) właściwości, usługa Service Bus zwraca wyjątek nieprawidłowej operacji. [Właściwość PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) powinna być używana, jeśli nadawca wysyła wiadomości transakcyjne z uwzględnieniem sesji. Klucz partycji zapewnia, że wszystkie wiadomości, które są wysyłane w ramach transakcji są obsługiwane przez tego samego brokera obsługi wiadomości.

**MessageId:** Jeśli w kolejce lub w temacie ustawiono właściwość [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) na **true** i właściwości [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) lub [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nie są ustawione, wartość właściwości [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) służy jako klucz partycji. (Biblioteki Microsoft .NET i AMQP automatycznie przypisują identyfikator wiadomości, jeśli aplikacja wysyłająca tego nie zrobi). W takim przypadku wszystkie kopie tej samej wiadomości są obsługiwane przez tego samego brokera wiadomości. Ten identyfikator umożliwia usługi Service Bus wykrywanie i eliminowanie zduplikowanych komunikatów. Jeśli [właściwość RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) nie jest ustawiona na **true,** usługa Service Bus nie uznaje [messageid](/dotnet/api/microsoft.azure.servicebus.message.messageid) właściwość jako klucz partycji.

### <a name="not-using-a-partition-key"></a>Nie używanie klucza partycji

W przypadku braku klucza partycji usługa Service Bus dystrybuuje wiadomości w sposób okrężny do wszystkich partycji kolejki lub tematu podzielonej na partycje. Jeśli wybrana partycja nie jest dostępna, usługa Service Bus przypisuje komunikat do innej partycji. W ten sposób operacja wysyłania powiedzie się pomimo tymczasowej niedostępności magazynu wiadomości. Jednak nie można osiągnąć gwarantowanej kolejności, że klucz partycji zapewnia.

Aby uzyskać bardziej dogłębną dyskusję na temat kompromisu między dostępnością (bez klucza partycji) a spójnością (przy użyciu klucza partycji), zobacz [ten artykuł](../event-hubs/event-hubs-availability-and-consistency.md). Te informacje dotyczą jednakowo podzielonych na partycje jednostek usługi Service Bus.

Aby dać service bus wystarczająco dużo czasu, aby wysłać wiadomość w kolejce do innej partycji, [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) wartość określona przez klienta, który wysyła wiadomość musi być większa niż 15 sekund. Zaleca się ustawienie [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) właściwość do wartości domyślnej 60 sekund.

Klucz partycji "przypina" wiadomość do określonej partycji. Jeśli magazyn obsługi wiadomości, który przechowuje tę partycję jest niedostępny, usługa Service Bus zwraca błąd. W przypadku braku klucza partycji usługa Service Bus może wybrać inną partycję i operacja zakończy się pomyślnie. W związku z tym zaleca się, aby nie podawać klucz partycji, chyba że jest to wymagane.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Tematy zaawansowane: używanie transakcji z encjami podzielonymi na partycje

Komunikaty wysyłane w ramach transakcji muszą określać klucz partycji. Klucz może być jedną z następujących właściwości: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)lub [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Wszystkie wiadomości, które są wysyłane jako część tej samej transakcji należy określić ten sam klucz partycji. Jeśli spróbujesz wysłać wiadomość bez klucza partycji w ramach transakcji, usługa Service Bus zwraca wyjątek nieprawidłowej operacji. Jeśli spróbujesz wysłać wiele wiadomości w ramach tej samej transakcji, które mają różne klucze partycji, usługa Service Bus zwraca wyjątek nieprawidłowej operacji. Przykład:

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

Jeśli którykolwiek z właściwości, które służą jako klucz partycji są ustawione, Service Bus przypina komunikat do określonej partycji. To zachowanie występuje, czy transakcja jest używana. Zaleca się, aby nie określać klucz partycji, jeśli nie jest to konieczne.

## <a name="using-sessions-with-partitioned-entities"></a>Korzystanie z sesji z encjami podzielonymi na partycje

Aby wysłać wiadomość transakcyjną do tematu lub kolejki obsługującej sesję, wiadomość musi mieć ustawioną właściwość [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Jeśli [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) właściwość jest również określona, musi być identyczna [z SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) właściwości. Jeśli różnią się one, usługa Service Bus zwraca wyjątek nieprawidłowej operacji.

W przeciwieństwie do regularnych (nie podzielonych na partycje) kolejek lub tematów nie jest możliwe użycie pojedynczej transakcji do wysyłania wielu wiadomości do różnych sesji. W przypadku próby usługa Service Bus zwraca nieprawidłowy wyjątek operacji. Przykład:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatyczne przekazywanie wiadomości z jednostkami podzielonymi na partycje

Usługa Service Bus obsługuje automatyczne przekazywanie wiadomości z, do lub między jednostkami podzielonymi na partycje. Aby włączyć automatyczne przekazywanie wiadomości, ustaw [właściwość QueueDescription.ForwardTo][QueueDescription.ForwardTo] w kolejce źródłowej lub subskrypcji. Jeśli komunikat określa klucz partycji ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)lub [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), ten klucz partycji jest używany dla jednostki docelowej.

## <a name="considerations-and-guidelines"></a>Uwagi i wytyczne
* **Funkcje o wysokiej spójności**: Jeśli jednostka używa funkcji, takich jak sesje, wykrywanie duplikatów lub jawna kontrola klucza partycjonowania, operacje obsługi wiadomości są zawsze kierowane do określonej partycji. Jeśli którykolwiek z partycji wystąpić duży ruch lub magazynu podstawowej jest w złej kondycji, te operacje nie i dostępność jest zmniejszona. Ogólnie rzecz biorąc spójność jest nadal znacznie wyższa niż jednostki nieobjęte partycją; tylko podzbiór ruchu występuje problemy, w przeciwieństwie do całego ruchu. Aby uzyskać więcej informacji, zobacz tę [dyskusję na temat dostępności i spójności](../event-hubs/event-hubs-availability-and-consistency.md).
* **Zarządzanie**: Operacje, takie jak Tworzenie, Aktualizacja i Usuwanie muszą być wykonywane na wszystkich partycjach jednostki. Jeśli dowolna partycja jest w złej kondycji, może to spowodować błędy dla tych operacji. W przypadku operacji Pobierz informacje, takie jak liczba wiadomości, muszą być agregowane ze wszystkich partycji. Jeśli dowolna partycja jest w złej kondycji, stan dostępności jednostki jest zgłaszany jako ograniczony.
* **Scenariusze komunikatów o niskim wolumenie:** W przypadku takich scenariuszy, szczególnie w przypadku korzystania z protokołu HTTP, może być trzeba wykonać wiele operacji odbierania w celu uzyskania wszystkich wiadomości. W przypadku odbierania żądań frontonu wykonuje odbiór na wszystkich partycjach i buforuje wszystkie otrzymane odpowiedzi. Kolejne żądanie odbioru na tym samym połączeniu będzie korzystać z tego buforowania i otrzymywać opóźnienia będą niższe. Jeśli jednak masz wiele połączeń lub używasz protokołu HTTP, ustanawia to nowe połączenie dla każdego żądania. W związku z tym nie ma gwarancji, że wyląduje on na tym samym węźle. Jeśli wszystkie istniejące wiadomości są zablokowane i buforowane w innym frontonie, operacja odbierania zwraca **wartość null**. Wiadomości ostatecznie wygasają i możesz je otrzymać ponownie. Zalecane jest utrzymanie przy życiu protokołu HTTP. W przypadku korzystania z partycjonowania w scenariuszach o niskim woluminie operacje odbierania mogą trwać dłużej niż oczekiwano. W związku z tym zaleca się, aby nie używać partycjonowania w tych scenariuszach. Usuń wszystkie istniejące jednostki podzielone na partycje i ponownie utworzyć je z partycjonowania wyłączone w celu zwiększenia wydajności.
* **Przeglądaj/wgląd wiadomości**: Dostępne tylko w starszej bibliotece [WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nie zawsze zwraca liczbę komunikatów określonych w [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) właściwości. Istnieją dwa typowe powody tego zachowania. Jednym z powodów jest, że zagregowany rozmiar kolekcji wiadomości przekracza maksymalny rozmiar 256 KB. Innym powodem jest to, że jeśli kolejka lub temat ma [EnablePartitioning właściwość](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) ustawiona na **true**, partycja może nie mieć wystarczającej ilości komunikatów, aby zakończyć żądaną liczbę wiadomości. Ogólnie rzecz biorąc, jeśli aplikacja chce odbierać określoną liczbę wiadomości, należy wywołać [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) wielokrotnie, dopóki nie pobiera tej liczby wiadomości lub nie ma więcej wiadomości, aby zajrzeć. Aby uzyskać więcej informacji, w tym przykłady kodu, zobacz [queueclient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) lub [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) dokumentacji interfejsu API.

## <a name="latest-added-features"></a>Najnowsze funkcje

* Dodaj lub usuń regułę jest teraz obsługiwana z jednostkami podzielonymi na partycje. Różni się od jednostek nie podzielonych na partycje, operacje te nie są obsługiwane w ramach transakcji. 
* Usługa AMQP jest teraz obsługiwana do wysyłania i odbierania wiadomości do i z jednostki podzielonej na partycje.
* Usługa AMQP jest teraz obsługiwana w następujących operacjach: [Wysyłanie wsadowe,](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch) [Odbieranie partii,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch) [Odbieranie przez numer sekwencyjny,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive) [Wgląd,](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek) [Odnawianie,](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock) [Komunikat harmonogramu,](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync) [Anuluj zaplanowaną wiadomość,](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync) [Dodaj regułę,](/dotnet/api/microsoft.azure.servicebus.ruledescription) [Usuń regułę,](/dotnet/api/microsoft.azure.servicebus.ruledescription) [Blokada odnawiania sesji,](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock) [Ustaw stan sesji,](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate) [Pobierz stan sesji](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)i [Wyliczaj sesje](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Ograniczenia jednostek podzielonych na partycje

Obecnie usługa Service Bus nakłada następujące ograniczenia na podzielone na partycje kolejki i tematy:

* Kolejki i tematy podzielone na partycje nie są obsługiwane w warstwie obsługi wiadomości w warstwie Premium. Sesje są obsługiwane w warstwie premier przy użyciu SessionId. 
* Kolejki i tematy podzielone na partycje nie obsługują wysyłania wiadomości należących do różnych sesji w jednej transakcji.
* Obecnie w usłudze Service Bus można utworzyć maksymalnie 100 partycjonowanych kolejek lub tematów w każdej przestrzeni nazw. Każda kolejka lub temat podzielony na partycje liczy się do przydziału 10 000 jednostek na obszar nazw (nie dotyczy warstwy Premium).

## <a name="next-steps"></a>Następne kroki

Przeczytaj o podstawowych pojęciach specyfikacji wiadomości AMQP 1.0 w [przewodniku protokołu AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
