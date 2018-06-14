---
title: Przestrzenie nazw łączyć usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Szczegóły implementacji par nazw i kosztów
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: f16c65286b0aa079889c9d53e98bf54e3d57c95f
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2017
ms.locfileid: "27159545"
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Skojarzone szczegóły implementacji przestrzeni nazw i kosztów efekty

[PairNamespaceAsync] [ PairNamespaceAsync] — metoda, za pomocą [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] wystąpienia, wykonuje zadania widoczne w Twoim imieniu. Ponieważ są koszt zagadnienia, podczas korzystania z funkcji, takiemu grupowaniu można sprawdzić te zadania, tak aby oczekiwać zachowanie, gdy nastąpi. Interfejs API angażujący następujące automatyczne działanie w imieniu użytkownika:

* Tworzenie zaległości kolejki.
* Tworzenie [MessageSender] [ MessageSender] obiekt, który komunikuje się z kolejki i tematy.
* Gdy jednostka obsługi komunikatów staje się niedostępny, wysyła polecenie ping wiadomości do jednostki w celu Wykryj, kiedy jednostkę znowu dostępne.
* Opcjonalnie tworzy zestawu pomp"komunikat" który przenosić wiadomości z kolejki zaległości do kolejki głównej.
* Współrzędne zamknięcia/powodujący błąd podstawowych i pomocniczych [MessagingFactory] [ MessagingFactory] wystąpień.

Na wysokim poziomie, ta funkcja działa w następujący sposób: podczas podstawowej jednostki jest w dobrej kondycji, nie zmiany sposobu działania są wykonywane. Gdy [FailoverInterval] [ FailoverInterval] upłynie czas trwania, a nie powiodło się widzi podstawowej jednostki wysyła po z systemem innym niż przejściowy [MessagingException] [ MessagingException] lub [TimeoutException][TimeoutException], spowoduje następujące zachowanie:

1. Wyślij operacji do podstawowej jednostki są wyłączone i systemu wysyła pakiet usługi ping podstawowej jednostki aż pomyślnie dostarczony polecenia ping.
2. Wybrano losowe zaległości kolejki.
3. [BrokeredMessage] [ BrokeredMessage] obiekty są kierowane do wybranego zaległości kolejki.
4. W przypadku niepowodzenia operacji wysyłania do kolejki zaległości wybrany tej kolejki są pobierane z obrót i Nowa kolejka jest zaznaczone. Wszystkich nadawców na [MessagingFactory] [ MessagingFactory] wystąpienia Dowiedz się więcej o awarii.

Poniższe rysunki przedstawiać sekwencję. Nadawca wysyła najpierw wiadomości.

![Sparowanego przestrzenie nazw][0]

W przypadku awarii do wysyłania do kolejki głównej nadawca zaczyna wysyła wiadomości do kolejki zaległości losowo wybrany. Rozpoczyna się jednocześnie, zadanie ping.

![Sparowanego przestrzenie nazw][1]

W tym momencie komunikaty są nadal w kolejce dodatkowej i nie zostały dostarczone do kolejki głównej. Gdy podstawowy kolejki jest w dobrej kondycji ponownie, co najmniej jeden proces powinna być uruchomiona syphon. Syphon dostarcza komunikaty z różnych kolejek zaległości na jednostki właściwe miejsce docelowe (kolejki i tematy).

![Sparowanego przestrzenie nazw][2]

W pozostałej części w tym temacie omówiono konkretne szczegółowe informacje, jak te elementy pracy.

## <a name="creation-of-backlog-queues"></a>Tworzenie zaległości kolejki
[SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] obiekt przekazywany do [PairNamespaceAsync] [ PairNamespaceAsync] metody wskazuje liczbę kolejek zaległości, którego chcesz użyć. Każdej kolejki zaległości zostanie utworzona z następującymi właściwościami jawnie ustaw (wszystkie inne wartości są ustawiane na [QueueDescription] [ QueueDescription] ustawień domyślnych):

| Ścieżka | [głównej przestrzeni nazw] / x magistrali usług transferu / [Indeks] [Indeks] w przypadku wartości [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| maxDeliveryCount |int. MaxValue |
| DefaultMessageTimeToLive |Wartość TimeSpan.MaxValue |
| AutoDeleteOnIdle |Wartość TimeSpan.MaxValue |
| LockDuration |1 minuta |
| EnableDeadLetteringOnMessageExpiration |prawda |
| EnableBatchedOperations |prawda |

Na przykład utworzyć pierwszy kolejki zaległości dla przestrzeni nazw **contoso** nosi nazwę `contoso/x-servicebus-transfer/0`.

Podczas tworzenia kolejki, kod najpierw sprawdza, czy istnieje takiej kolejki. Jeśli kolejka nie istnieje, trwa tworzenie kolejki. Ten kod nie oczyszczania "dodatkowe" zaległości kolejki. W szczególności jeśli aplikacji głównej przestrzeni nazw **contoso** żądań w pięciu kolejkach zaległości, ale zaległości kolejki ze ścieżką `contoso/x-servicebus-transfer/7` istnieje, jest nadal znajdują się dodatkowe zaległości kolejki, ale nie są używane. System zezwala jawnie kolejek zaległości dodatkowe mogą znajdować się, których nie można użyć. Jako właściciel przestrzeni nazw jest odpowiedzialny za czyszczenie wszystkie nieużywane/niechciane zaległości kolejki. Przyczyną tej decyzji jest magistrali usług nie wiadomo, jakich celów istnieje dla wszystkich kolejek w przestrzeni nazw. Ponadto jeśli kolejka o podanej nazwie istnieje, ale nie spełnia zakładanego [QueueDescription][QueueDescription], a następnie z powodów są własne dla zmianę zachowania domyślnego. Gwarancje nie są wykonywane dla modyfikacje kolejki zaległości w kodzie. Upewnij się dokładnie przetestować zmiany.

## <a name="custom-messagesender"></a>Niestandardowe MessageSender
Podczas wysyłania, wszystkie komunikaty go za pośrednictwem wewnętrznego [MessageSender] [ MessageSender] obiekt, który będzie pracował normalnie, kiedy wszystko działa i przekierowuje do zaległości kolejki podczas czynności "break." Po odebraniu Błąd przejściowy z systemem innym niż, uruchamia czasomierz. Po [TimeSpan] [ TimeSpan] okres składające się z [FailoverInterval] [ FailoverInterval] uczestniczy w którym nie powiodło się komunikaty są wysyłane, wartość właściwości pracy awaryjnej. W tym momencie dla każdej jednostki się zdarzyć następujących czynności:

* Wykonuje zadanie ping co [PingPrimaryInterval] [ PingPrimaryInterval] do sprawdzenia, czy jednostka jest dostępna. Po pomyślnym to zadanie, żadnego kodu klienta, który używa jednostki natychmiast rozpoczyna wysyłanie wiadomości do głównej przestrzeni nazw.
* Wynikiem będzie przyszłych żądań do wysyłania do tej samej jednostki z innych nadawców [BrokeredMessage] [ BrokeredMessage] wysyłane modyfikacji w celu znajdują się w kolejce zaległości. Modyfikacja usuwa niektóre właściwości z [BrokeredMessage] [ BrokeredMessage] obiektu i przechowuje je w innym miejscu. Następujące właściwości są wyczyszczone i dodany w obszarze Nowy alias, dzięki czemu jednolicie przetwarzania komunikatów usługi Service Bus i zestawu SDK:

| Stara nazwa właściwości | Nowa nazwa właściwości |
| --- | --- |
| Identyfikator sesji |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path. |

Oryginalna ścieżka docelowa jest również przechowywane w wiadomości jako właściwość o nazwie x-ms-path. Ten projekt umożliwia wiadomości dla wielu obiektów współistnienie w jednym zaległości kolejki. Właściwości są tłumaczone przez syphon.

Niestandardowa [MessageSender] [ MessageSender] obiektu może wystąpić problemy podczas wiadomości podejścia limit 256 KB i pracy awaryjnej jest włączone. Niestandardowa [MessageSender] [ MessageSender] obiekt przechowuje komunikaty wszystkich kolejek i tematów w zaległości kolejki. Ten obiekt łączy komunikaty z wielu kolory podstawowe w ramach zaległości kolejki. Do obsługi obciążenia zrównoważyć wielu klientów, których nie wiadomo, wzajemnie, zestaw SDK losowo wybiera jeden kolejki zaległości na dla każdego [QueueClient] [ QueueClient] lub [TopicClient] [ TopicClient] tworzenie w kodzie.

## <a name="pings"></a>Polecenia ping
Wiadomość ping jest pusta [BrokeredMessage] [ BrokeredMessage] z jego [ContentType] [ ContentType] właściwość aplikacji/vnd.ms-magistrali usług ping i [TimeToLive] [ TimeToLive] wartość 1 sekundę. To polecenie ping ma jeden właściwości specjalne w usłudze Service Bus: serwer nigdy nie dostarcza polecenia ping, gdy zażąda każdego obiektu wywołującego [BrokeredMessage][BrokeredMessage]. W związku z tym nie trzeba Dowiedz się, jak otrzymywać i ignorować te komunikaty. Każdy obiekt (unikatowe kolejka lub temat) na [MessagingFactory] [ MessagingFactory] wystąpienia na klienta będzie można wykonywać polecenia ping, gdy są one uznawane za jako niedostępny. Domyślnie to odbywa się raz na minutę. Komunikaty ping są traktowane jako prawidłowe komunikatów usługi Service Bus i może spowodować opłaty za przepustowości i wiadomości. Jak klienci wykrywają, że system będzie dostępny, Zatrzymaj wiadomości.

## <a name="the-syphon"></a>Syphon
Co najmniej jeden program wykonywalny w aplikacji aktywnie powinna działać syphon. Syphon wykonuje długi sondowania odbierania, które to 15 minut. Gdy wszystkie obiekty są dostępne i masz 10 zaległości kolejki, aplikacji, która obsługuje syphon wywołania operacji odbierania 40 razy na godzinę, 960 razy dziennie i 28800 razy w ciągu 30 dni. Przemieszczając się syphon jest aktywnie komunikatów z zaległości do kolejki głównej, każdy komunikat napotyka następujące opłaty (standardowe opłaty za rozmiaru wiadomości i przepustowości dotyczy wszystkich etapach):

1. Wyślij do listy prac.
2. Otrzymywać zaległości.
3. Wyślij do serwera podstawowego.
4. Odbieranie z serwera podstawowego.

## <a name="closefault-behavior"></a>Zamknij/błędów zachowanie
W aplikacji, która obsługuje syphon, raz podstawowej lub pomocniczej [MessagingFactory] [ MessagingFactory] błędów lub zamknięciu bez partnera również błędny lub zamknięte, a syphon wykrywa tego stanu syphon działa. Jeśli drugiej [MessagingFactory] [ MessagingFactory] nie są zamknięte w ciągu 5 sekund syphon błędów nadal Otwórz [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>Kolejne kroki
Zobacz [asynchroniczne wzorce i wysokiej dostępności do obsługi komunikatów] [ Asynchronous messaging patterns and high availability] szczegółowe omówienie asynchroniczne komunikatów usługi Service Bus. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions#Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_FailoverInterval
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_PingPrimaryInterval
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_ContentType
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_TimeToLive
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
