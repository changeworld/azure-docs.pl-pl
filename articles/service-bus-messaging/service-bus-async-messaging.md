---
title: Asynchroniczna Obsługa komunikatów usługi Service Bus | Dokumentacja firmy Microsoft
description: Opis asynchronicznej obsługi komunikatów usługi Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 50778ae742c1ec66857a6c2fa6250dc3d67e5601
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531113"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Wzorce asynchronicznej obsługi komunikatów i wysoka dostępność

Asynchroniczna Obsługa komunikatów może być implementowany w wiele różnych sposobów. Kolejki, tematy i subskrypcje usługi Azure Service Bus obsługuje asynchronism za pośrednictwem sklepu i mechanizm do przodu. W normalnych operacji (synchroniczne) wysyłanie komunikatów do kolejki i tematy i odbieranie komunikatów z subskrypcji i kolejek. Aplikacje, które piszesz zależą od tych jednostek, które zawsze są dostępne. Kondycja jednostki zmieni się, ze względu na różnych okolicznościach, konieczne jest sposób zapewnienia jednostki ograniczoną możliwość, która może spełnić zastosowaniach.

Aplikacje zazwyczaj umożliwiają wzorce asynchronicznej obsługi komunikatów wiele scenariuszy komunikacji. Możesz tworzyć aplikacje, w których klienci mogą wysyłać komunikaty do usług, nawet wtedy, gdy usługa nie jest uruchomiona. Dla aplikacji tego procesu, które mogą pomóc wzrosty komunikacji za pośrednictwem kolejki wyrównania obciążenia, zapewniając miejsce do buforu łączności. Na koniec możesz uzyskać modułu równoważenia obciążenia prosty, lecz skuteczne rozłożenie wiadomości na wielu komputerach.

W celu zapewnienia dostępności dowolnej z tych jednostek, należy wziąć pod uwagę wiele różnych sposobów, w którym te jednostki może znajdować się niedostępna w przypadku trwałych system obsługi komunikatów. Ogólnie rzecz biorąc widzimy jednostki stają się niedostępne dla aplikacji, które napiszemy na następujące sposoby:

* Nie można wysłać wiadomości.
* Nie można odbierać komunikaty.
* Nie można zarządzać jednostki (Tworzenie, pobieranie, aktualizowanie lub usuwanie jednostek).
* Nie można skontaktować się z usługą.

Dla każdego z tych błędów awarii w różnych trybach istnieje, które umożliwiają aplikacji, aby kontynuować do wykonywania pracy na pewien stopień ograniczoną możliwość. Na przykład system, który może wysyłać wiadomości, ale nie ich odbierania, może nadal otrzymywać zamówienia od klientów, ale nie będzie mógł przetworzyć tych zamówień. W tym temacie omówiono potencjalnych problemów, które mogą wystąpić, i jak te problemy zostały skorygowane. Usługa Service Bus ma wprowadzono szereg środki zaradcze, które użytkownik musi wyrazić zgodę, a w tym temacie omówiono również zasad regulujących korzystanie z tych opcjonalnych środki zaradcze.

## <a name="reliability-in-service-bus"></a>Niezawodność w usłudze Service Bus
Istnieje kilka sposobów obsługi problemów komunikat i jednostki, a istnieją wytyczne dotyczące właściwego użycia tych środków zaradczych. Zrozumienie wytycznych, musisz najpierw zrozumieć, co może nie działać w usłudze Service Bus. Ze względu na projekt systemy usługi Azure wszystkie te problemy zwykle krótkotrwałe. Na wysokim poziomie inne przyczyny niedostępności wyglądać następująco:

* Ograniczanie przepustowości z systemu zewnętrznego, od których zależy usługa Service Bus. Ograniczanie występuje z interakcji z zasobami magazynu i mocy obliczeniowej.
* Problem w przypadku systemu, od których zależy usługa Service Bus. Na przykład określona część magazynu mogą wystąpić problemy.
* Błąd usługi Service Bus w jednym podsystemie. W takiej sytuacji węzła obliczeniowego można uzyskać w niespójnym stanie i należy ponownie uruchomić, powodując wszystkich jednostek, który można załadować saldo do innych węzłów. Z kolei może to spowodować krótkim przetwarzania komunikatów powolne.
* Błąd usługi Service Bus w obrębie centrum danych platformy Azure. To jest "poważnej awarii" w którym system jest nieosiągalna przez wiele minut lub kilka godzin.

> [!NOTE]
> Termin **magazynu** może oznaczać zarówno usługi Azure Storage i SQL Azure.
> 
> 

Usługa Service Bus zawiera szereg środki zaradcze w przypadku tych problemów. W poniższych sekcjach omówiono poszczególne problemy i ich odpowiednich środków zaradczych.

### <a name="throttling"></a>Ograniczanie przepływności
Usługa Service Bus ograniczania Azure umożliwia zarządzanie współczynnik współpracy wiadomości. Poszczególnych węzłów usługi Service Bus przechowuje wiele jednostek. Każda z tych jednostek temu zapotrzebowanie na system pod kątem procesora CPU, pamięci, magazynu i innymi aspektami. Po wykryciu dowolnego z tych zestawów reguł użycia przekraczający zdefiniowanych progów, Service Bus można odmówić danego żądania. Obiekt wywołujący uzyskuje [ServerBusyException] [ ServerBusyException] i ponownych prób po 10 sekundach.

Jako ograniczenie kod musi odczytać błędu i zatrzymanie dowolnego powtórzeń komunikatu przez co najmniej 10 sekund. Ponieważ ten błąd może się zdarzyć w różnych rodzajów aplikacji klienta, oczekuje się, że każdy element niezależnie wykonuje Logika ponawiania. Kod może zmniejszyć prawdopodobieństwo, że jest ograniczane przez włączenie partycjonowania na kolejki lub tematu.

### <a name="issue-for-an-azure-dependency"></a>Problem w przypadku zależności platformy Azure
Inne składniki w ramach platformy Azure od czasu do czasu może mieć problemy z usługą. Na przykład po uaktualnieniu systemu, który używa usługi Service Bus tego systemu mogą tymczasowo występować zmniejszenie możliwości. Aby uniknąć tego rodzaju problemy, Service Bus regularnie bada i implementuje środki zaradcze. Efekty uboczne te środki zaradcze są wyświetlane. Na przykład aby obsłużyć przejściowe problemy z usługą storage, Usługa Service Bus implementuje system, który umożliwia wykonywanie operacji wysyłania komunikatu do działać spójnie. Ze względu na charakter środki zaradcze wysyłany komunikat może potrwać do 15 minut, są wyświetlane w usterce kolejki lub subskrypcji i gotowa do operacji odbierania. Ogólnie rzecz biorąc, większość jednostek będzie ten problem nie występuje. Jednak w usłudze Service Bus na platformie Azure, biorąc pod uwagę liczbę jednostek, takie rozwiązanie jest czasami wymagany przez mały podzbiór klientów usługi Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Błąd usługi Service Bus w jednym podsystemie
Z każdą aplikacją okolicznościach może powodować wewnętrznego składnikiem usługi Service Bus, aby stać się niespójna. Gdy Usługa Service Bus wykryje to, zbiera dane z aplikacji, aby pomóc w zdiagnozowaniu, co się stało. Po zebraniu danych ponownym uruchomieniu aplikacji w celu podjęcia próby przywrócić go do stanu spójności. Ten proces odbywa się dość szybko, a wyniki w jednostce, które pojawiają się jako niedostępny dla maksymalnie kilka minut, chociaż jest to typowy szczegółów czasu są znacznie krótszy.

W takich przypadkach aplikacja kliencka generuje [System.TimeoutException] [ System.TimeoutException] lub [MessagingException] [ MessagingException] wyjątku. Usługa Service Bus zawiera ograniczenia tego problemu w formie klienta automatycznego Logika ponawiania. Po wyczerpaniu okres ponowień i komunikat nie zostanie dostarczony, możesz zapoznać się z innych wymienionych w artykule [Obsługa wyłączeń i awarii][handling outages and disasters].

## <a name="next-steps"></a>Kolejne kroki
Teraz, kiedy znasz już podstawy asynchronicznej obsługi komunikatów w usłudze Service Bus, przeczytaj więcej szczegółowych informacji na temat [Obsługa wyłączeń i awarii][handling outages and disasters].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN
[handling outages and disasters]: service-bus-outages-disasters.md
