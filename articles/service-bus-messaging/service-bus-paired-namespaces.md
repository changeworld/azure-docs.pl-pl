---
title: Usługa Azure Service Bus sparowane przestrzenie nazw | Dokumentacja firmy Microsoft
description: Szczegóły implementacji sparowanej przestrzeni nazw i kosztów
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 2440c8d3-ed2e-47e0-93cf-ab7fbb855d2e
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: aschhab
ms.openlocfilehash: 54376f3067e6aca52af8d283b42f6363f8016bff
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2019
ms.locfileid: "55728728"
---
# <a name="paired-namespace-implementation-details-and-cost-implications"></a>Skojarzone szczegółów implementacji przestrzeni nazw i koszt skutki

[PairNamespaceAsync] [ PairNamespaceAsync] metody, przy użyciu [SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] wystąpienia, wykonuje zadania widoczne na Twoim imieniu. Ponieważ są kwestii związanych z kosztami podczas korzystania z funkcji, to grupowaniu można sprawdzić te zadania, tak aby oczekiwać zachowanie, gdy nastąpi. Interfejs API angażuje następujące zachowanie automatyczne w Twoim imieniu:

* Tworzenie zaległości kolejki.
* Tworzenie [MessageSender] [ MessageSender] obiekt, który komunikuje się w kolejkach lub tematach.
* Gdy jednostki obsługi komunikatów staje się niedostępny, wysyła polecenie ping wiadomości do jednostki w celu podjęcia próby wykrycia podczas tej jednostki znowu stanie się dostępny.
* Opcjonalnie tworzy zestaw "pompy komunikatów", przenosić wiadomości z kolejki zaległości do kolejki głównej.
* Służy do koordynowania zamknięcia/powodujący błąd podstawowych i pomocniczych [MessagingFactory] [ MessagingFactory] wystąpień.

Na wysokim poziomie, ta funkcja działa w następujący sposób: po podstawowej jednostki jest w dobrej kondycji, nie zmiany zachowania wystąpić. Gdy [FailoverInterval] [ FailoverInterval] upłynie czas trwania i pomyślne nie widzi podstawowej jednostki wysyła po innych niż przejściowe [MessagingException] [ MessagingException] lub [TimeoutException][TimeoutException], spowoduje następujące zachowanie:

1. Wyślij operacje do podstawowej jednostki są wyłączone i system polecenia ping podstawowej jednostki, dopóki nie można pomyślnie dostarczyć polecenia ping.
2. Wybrano losowy zaległości kolejki.
3. [BrokeredMessage] [ BrokeredMessage] obiekty są kierowane do kolejki wybranej listy prac.
4. W przypadku niepowodzenia operacji wysyłania kolejki zaległości wybranym tej kolejki jest określany na podstawie obrót i wybrano wraz z nową kolejką. Wszystkich nadawców na [MessagingFactory] [ MessagingFactory] wystąpienia dowiedzieć się więcej o awarii.

Następujące dane liczbowe przedstawiać sekwencję. Po pierwsze Nadawca wysyła wiadomości.

![Sparowane przestrzenie nazw][0]

W przypadku awarii do wysłania do kolejki głównej nadawcy rozpoczyna wysyłanie komunikatów do kolejki losowo wybranym zaległości. Jednocześnie uruchamia zadanie ping.

![Sparowane przestrzenie nazw][1]

W tym momencie komunikaty są nadal w kolejki dodatkowej i nie zostały dostarczone do kolejki głównej. Gdy kolejki głównej jest w dobrej kondycji ponownie, co najmniej jeden proces powinien być uruchomiony syphon. Syphon dostarcza komunikaty ze wszystkich różnych kolejek zaległości do jednostki docelowej odpowiednie (kolejki i tematy).

![Sparowane przestrzenie nazw][2]

W pozostałej części tego tematu omawia konkretnych szczegółów jak działają te elementy.

## <a name="creation-of-backlog-queues"></a>Tworzenie zaległości kolejki
[SendAvailabilityPairedNamespaceOptions] [ SendAvailabilityPairedNamespaceOptions] obiekt przekazany do [PairNamespaceAsync] [ PairNamespaceAsync] metoda wskazuje liczbę kolejki zaległości, do którego chcesz użyć. Każda kolejka zaległości zostanie utworzona z następującymi właściwościami jawnie ustawić (wszystkie inne wartości są ustawione na [QueueDescription] [ QueueDescription] wartości domyślne):

| Ścieżka | [Podstawowa przestrzeń nazw] / x-servicebus transferu / [index] [index] w przypadku wartości w [0, BacklogQueueCount) |
| --- | --- |
| MaxSizeInMegabytes |5120 |
| MaxDeliveryCount |int.MaxValue |
| DefaultMessageTimeToLive |TimeSpan.MaxValue |
| AutoDeleteOnIdle |TimeSpan.MaxValue |
| Dotyczy |1 min |
| EnableDeadLetteringOnMessageExpiration |true |
| EnableBatchedOperations |true |

Na przykład tworzenia pierwszego kolejki listy prac dla przestrzeni nazw **contoso** nosi nazwę `contoso/x-servicebus-transfer/0`.

Podczas tworzenia kolejki, ten kod najpierw sprawdza, czy istnieje takiej kolejki. Jeśli kolejka nie istnieje, kolejce zostanie utworzony. Kod nie usuwaj "dodatkowe" zaległości kolejki. W szczególności jeśli aplikację za pomocą podstawowej przestrzeni nazw **contoso** żądań pięciu kolejkach zaległości, ale zaległości kolejki ze ścieżką `contoso/x-servicebus-transfer/7` istnieje, jest nadal obecny tej kolejki dodatkowe zaległości, ale nie są używane. System pozwala jawnie kolejki dodatkowe zaległości istnieje, których nie można używać. Jako właściciel przestrzeni nazw odpowiedzialność za czyszczenie wszystkie nieużywane/niechciane zaległości kolejki. Przyczyną tej decyzji jest, Usługa Service Bus nie wiedzieć, jakich celów istnieje dla wszystkich kolejek w Twojej przestrzeni nazw. Ponadto jeśli kolejka o podanej nazwie istnieje, ale nie spełnia zakładanego [QueueDescription][QueueDescription], z przyczyn, znajdują się własne dla zmiana domyślnego zachowania. Żadnej gwarancji wprowadzono zmiany do kolejek zaległości w kodzie. Upewnij się, że należy dokładnie przetestować zmiany.

## <a name="custom-messagesender"></a>Niestandardowe MessageSender
Podczas wysyłania, wszystkie komunikaty go za pośrednictwem wewnętrznego [MessageSender] [ MessageSender] obiektu, który zachowuje się normalnie, kiedy wszystko, co działa i przekierowuje do zaległości kolejki, gdy elementy "break." Po odebraniu błędów nieprzejściowych, uruchamia czasomierz. Po [TimeSpan] [ TimeSpan] okres składający się z [FailoverInterval] [ FailoverInterval] wartości właściwości, podczas którego są wysyłane nie komunikaty zakończone powodzeniem, Przełączenie w tryb failover jest zaangażowane. W tym momencie dla każdej jednostki się zdarzyć następujących czynności:

* Wykonuje zadanie ping co [PingPrimaryInterval] [ PingPrimaryInterval] do sprawdzenia, czy jednostka jest dostępna. Gdy to zadanie zakończy się powodzeniem, cały kod klienta, który używa jednostki natychmiast rozpoczyna, wysyłanie nowych wiadomości do podstawowej przestrzeni nazw.
* Przyszłe żądania do wysyłania do tej samej jednostki od innych nadawców spowoduje [BrokeredMessage] [ BrokeredMessage] są wysyłane do modyfikacji w celu znajdują się w kolejce zaległości. Modyfikacja spowoduje usunięcie niektórych właściwości z [BrokeredMessage] [ BrokeredMessage] obiektu i przechowuje je w innym miejscu. Następujące właściwości są wyczyszczone, a następnie dodawany nowy alias, dzięki czemu usługa Service Bus i zestawu SDK w jednolity sposób przetwarzania komunikatów:

| Stara nazwa właściwości | Nowa nazwa właściwości |
| --- | --- |
| Identyfikator sesji |x-ms-sessionid |
| TimeToLive |x-ms-timetolive |
| ScheduledEnqueueTimeUtc |x-ms-path |

Oryginalna ścieżka docelowa także jest przechowywany w komunikacie jako właściwości o nazwie x-ms-path. Ten projekt umożliwia wiadomości dla wielu jednostek pod kątem współistnienia z pojedynczą Zaległość kolejki. Właściwości są tłumaczone przez syphon.

Niestandardowy [MessageSender] [ MessageSender] obiektów, które mogą wystąpić problemy podczas limitu 256 KB podejście do wiadomości i pracy awaryjnej jest zaangażowane. Niestandardowy [MessageSender] [ MessageSender] obiekt przechowuje komunikaty dla wszystkich kolejek i tematów, które razem w kolejkach zaległości. Ten obiekt napisana komunikaty z wielu kolory podstawowe razem w kolejkach zaległości. Aby obsłużyć obciążenia równoważenia wśród wielu klientów, którzy nie wiadomo na siebie nawzajem, zestaw SDK po losowo wybiera jednej zaległości kolejki dla każdego [QueueClient] [ QueueClient] lub [TopicClient] [ TopicClient] tworzenie w kodzie.

## <a name="pings"></a>Polecenia ping
Komunikat ping jest pustym [BrokeredMessage] [ BrokeredMessage] z jego [ContentType] [ ContentType] właściwością aplikacji/vnd.ms-servicebus-ping i [TimeToLive] [ TimeToLive] wartość 1 sekundę. Polecenie ping jest pojedynczy parametr specjalne w usłudze Service Bus: serwer nigdy nie dostarcza polecenia ping, gdy dowolny obiekt wywołujący żąda [BrokeredMessage][BrokeredMessage]. W związku z tym bez konieczności Dowiedz się, jak odbierać i Ignoruj te komunikaty. Każda jednostka (unikatowy kolejki lub tematu) na [MessagingFactory] [ MessagingFactory] wystąpienia na klienta będzie za pomocą polecenia ping, gdy są wówczas uważane za niedostępną. Domyślnie odbywa się to raz na minutę. Komunikaty ping są traktowane jako regularne komunikatów usługi Service Bus i może spowodować naliczenie opłat dla komunikatów i przepustowości. Tak szybko, jak klienci wykryje, że system będzie dostępny, Zatrzymaj komunikaty.

## <a name="the-syphon"></a>Syphon
Co najmniej jeden program wykonywalny w aplikacji powinna być aktywnie uruchomiona syphon. Syphon wykonuje długotrwałego sondowania odbierania, które obowiązuje 15 minut. Jeśli masz 10 zaległości kolejki wszystkie jednostki są dostępne, aplikacji, który jest hostem syphon wywołania operacji odbierania 40 razy na godzinę, 960 razy dziennie i 28800 razy w ciągu 30 dni. Przemieszczając się syphon jest aktywnie komunikatów z zaległości do kolejki głównej, każdy komunikat napotyka następujące opłaty (stosowane są standardowe opłaty za rozmiaru wiadomości i przepustowości na wszystkich etapach):

1. Wyślij do listy prac.
2. Otrzymywać zaległości.
3. Wyślij do podstawowej.
4. Odbieranie z serwera podstawowego.

## <a name="closefault-behavior"></a>Zamknij/błędów zachowanie
W aplikacji, która hostuje syphon, gdy podstawowy lub pomocniczy [MessagingFactory] [ MessagingFactory] błędów lub zamknięciu bez jej partnerów, również błędny lub zamknięte i syphon wykrywa ten stan syphon działa. Jeśli drugi [MessagingFactory] [ MessagingFactory] nie są zamknięte w ciągu 5 sekund syphon błędów nadal open [MessagingFactory][MessagingFactory].

## <a name="next-steps"></a>Kolejne kroki
Zobacz [asynchronicznej obsługi komunikatów, wzorce i wysoka dostępność] [ Asynchronous messaging patterns and high availability] szczegółowe omówienie asynchronicznej obsługi komunikatów usługi Service Bus. 

[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[MessageSender]: /dotnet/api/microsoft.servicebus.messaging.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[FailoverInterval]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
[PingPrimaryInterval]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[ContentType]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[TimeToLive]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md
[0]: ./media/service-bus-paired-namespaces/IC673405.png
[1]: ./media/service-bus-paired-namespaces/IC673406.png
[2]: ./media/service-bus-paired-namespaces/IC673407.png
