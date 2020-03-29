---
title: Asynchroniza komunikacja asynchronizawna usługi Service Bus | Dokumenty firmy Microsoft
description: Dowiedz się, jak usługa Azure Service Bus obsługuje asynchronizm za pośrednictwem mechanizmu sklepu i przesyłania dalej z kolejkami, tematami i subskrypcjami.
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
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 554260f403104d815b9b63c576c7ba0a2f3cf1e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761036"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Asynchroniczne wzorce wiadomości i wysoka dostępność

Asynchroniczne wiadomości mogą być implementowane na wiele różnych sposobów. Z kolejek, tematów i subskrypcji usługi Azure Service Bus obsługuje asynchronizm za pośrednictwem magazynu i mechanizmu przesyłania dalej. W normalnej (synchroniczowej) operacji wysyłasz wiadomości do kolejek i tematów oraz odbierasz wiadomości z kolejek i subskrypcji. Aplikacje, które piszesz, zależą od tych jednostek, które są zawsze dostępne. Gdy zmienia się kondycja jednostki, ze względu na różne okoliczności, potrzebny jest sposób, aby zapewnić jednostkę o zmniejszonych możliwościach, która może zaspokoić większość potrzeb.

Aplikacje zazwyczaj używają wzorców asynchronicznych wiadomości, aby włączyć wiele scenariuszy komunikacji. Można tworzyć aplikacje, w których klienci mogą wysyłać wiadomości do usług, nawet wtedy, gdy usługa nie jest uruchomiona. W przypadku aplikacji, które doświadczają serii komunikacji, kolejka może pomóc wyrównać obciążenie, zapewniając miejsce do buforowania komunikacji. Na koniec można uzyskać prosty, ale skuteczny moduł równoważenia obciążenia do dystrybucji wiadomości na wielu komputerach.

Aby zachować dostępność dowolnej z tych jednostek, należy wziąć pod uwagę wiele różnych sposobów, w których te jednostki mogą być niedostępne dla systemu obsługi wiadomości trwałe. Ogólnie rzecz biorąc, widzimy, że jednostka staje się niedostępna dla aplikacji, które piszemy na następujący sposoby:

* Nie można wysyłać wiadomości.
* Nie można odbierać wiadomości.
* Nie można zarządzać encjami (tworzyć, pobierać, aktualizować lub usuwać encji).
* Nie można skontaktować się z serwisem.

Dla każdego z tych błędów istnieją różne tryby awarii, które umożliwiają aplikacji kontynuować pracę na pewnym poziomie zmniejszonej możliwości. Na przykład system, który może wysyłać wiadomości, ale ich nie odbierać, nadal może odbierać zamówienia od klientów, ale nie może przetwarzać tych zamówień. W tym temacie omówiono potencjalne problemy, które mogą wystąpić i jak te problemy są złagodzone. Usługa Service Bus wprowadziła szereg środków zaradczych, które należy wybrać, a w tym temacie omówiono również zasady dotyczące korzystania z tych środków ograniczających opt-in.

## <a name="reliability-in-service-bus"></a>Niezawodność w magistrali serwisowej
Istnieje kilka sposobów obsługi problemów z wiadomościami i jednostkami i istnieją wytyczne dotyczące odpowiedniego użycia tych środków zaradczych. Aby zrozumieć wytyczne, należy najpierw zrozumieć, co może zakończyć się niepowodzeniem w usłudze Service Bus. Ze względu na projekt systemów platformy Azure wszystkie te problemy wydają się być krótkotrwałe. Na wysokim poziomie różne przyczyny niedostępności pojawiają się w następujący sposób:

* Ograniczanie z zewnętrznego systemu, od którego zależy usługa Service Bus. Ograniczanie występuje z interakcji z magazynu i zasobów obliczeniowych.
* Problem dla systemu, od którego zależy usługa Service Bus. Na przykład dana część magazynu może wystąpić problemy.
* Awaria usługi Service Bus w jednym podsystemie. W tej sytuacji węzeł obliczeniowy może uzyskać niespójny stan i musi ponownie uruchomić się, powodując wszystkie jednostki służy do równoważenia obciążenia do innych węzłów. To z kolei może spowodować krótki okres powolnego przetwarzania wiadomości.
* Awaria usługi Service Bus w centrum danych platformy Azure. Jest to "katastrofalna awaria", podczas której system jest nieosiągalny przez wiele minut lub kilka godzin.

> [!NOTE]
> Termin **magazyn** może oznaczać zarówno usługę Azure Storage, jak i sql azure.
> 
> 

Usługa Service Bus zawiera szereg czynników ograniczających zagrożenie dla tych problemów. W poniższych sekcjach omówiono każdy problem i ich odpowiednie środki zaradcze.

### <a name="throttling"></a>Ograniczanie przepływności
Dzięki usługi Service Bus ograniczanie przepustowości umożliwia zarządzanie szybkością komunikatów współpracujących. Każdy węzeł usługi Service Bus mieści wiele jednostek. Każda z tych jednostek sprawia, że wymagania w systemie pod względem procesora CPU, pamięci, magazynu i innych aspektów. Gdy którykolwiek z tych aspektów wykryje użycie, które przekracza zdefiniowane progi, usługa Service Bus może odmówić danego żądania. Wywołujący odbiera [ServerBusyException][ServerBusyException] i ponawia ponawia po 10 sekundach.

Jako zagrożenie, kod musi odczytać błąd i zatrzymać wszelkie ponownych prób wiadomości przez co najmniej 10 sekund. Ponieważ błąd może się zdarzyć w różnych częściach aplikacji klienta, oczekuje się, że każdy kawałek niezależnie wykonuje logikę ponawiania. Kod można zmniejszyć prawdopodobieństwo jest ograniczona przez włączenie partycjonowania w kolejce lub tematu.

### <a name="issue-for-an-azure-dependency"></a>Problem dotyczący zależności platformy Azure
Inne składniki na platformie Azure mogą od czasu do czasu mieć problemy z usługą. Na przykład, gdy system, który używa usługi Service Bus jest uaktualniany, że system może tymczasowo wystąpić zmniejszone możliwości. Aby obejść te typy problemów, usługa Service Bus regularnie bada i implementuje środki zaradcze. Pojawiają się skutki uboczne tych środków zaradczych. Na przykład do obsługi przejściowych problemów z magazynu, Usługa Service Bus implementuje system, który umożliwia operacje wysyłania wiadomości do pracy spójnie. Ze względu na charakter łagodzenia, wysłane wiadomości może potrwać do 15 minut, aby pojawić się w kolejce lub subskrypcji dotyczy i być gotowy do operacji odbierania. Ogólnie rzecz biorąc, większość podmiotów nie doświadczy tego problemu. Jednak biorąc pod uwagę liczbę jednostek w usłudze Service Bus na platformie Azure, to ograniczenie jest czasami potrzebne dla małego podzbioru klientów usługi Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Błąd usługi Service Bus w jednym podsystemie
W przypadku dowolnej aplikacji okoliczności mogą spowodować niespójność składnika wewnętrznego usługi Service Bus. Gdy usługa Service Bus wykryje to, zbiera dane z aplikacji, aby pomóc w diagnozowaniu, co się stało. Po zebraniu danych aplikacja jest ponownie uruchamiana w celu przywrócenia jej do stanu spójnego. Ten proces odbywa się dość szybko i powoduje, że jednostka wydaje się być niedostępna przez maksymalnie kilka minut, choć typowe czasy przestoju są znacznie krótsze.

W takich przypadkach aplikacja kliencka generuje wyjątek [System.TimeoutException][System.TimeoutException] lub [MessagingException.][MessagingException] Usługa Service Bus zawiera środki zaradcze dla tego problemu w postaci logiki ponawiania próby klienta automatycznego. Po wyczerpaniu okresu ponawiania próby i nie dostarczenie wiadomości można zbadać przy użyciu innych wymienionych w artykule na [temat obsługi awarii i awarii][handling outages and disasters].

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz podstawy komunikacji asynchroniiowej w usłudze Service Bus, przeczytaj więcej szczegółów na temat [obsługi awarii i awarii][handling outages and disasters].

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
