---
title: Izolacji wyłączeń i awarii aplikacji usługi Azure Service Bus | Dokumentacja firmy Microsoft
description: Techniki, aby chronić aplikacje przed potencjalnych awarii usługi Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24fba1961c8fd95f1b9489716d690dd6eaa97b62
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274846"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Najlepsze rozwiązania dotyczące izolacji aplikacji w ramach usługi Service Bus wyłączeń i awarii

Aplikacji o krytycznym znaczeniu musi działać w sposób ciągły, nawet w obecności nieplanowanych przestojów lub awarii. W tym artykule opisano techniki, które można użyć do ochrony aplikacji usługi Service Bus dla potencjalnych awarii usługi lub awarii.

Awaria jest zdefiniowany jako tymczasową niedostępność usługi Azure Service Bus. Awarii może mieć wpływ na niektóre składniki usługi Service Bus, takich jak magazyn obsługi komunikatów lub nawet całe centrum danych. Po problem został rozwiązany, Service Bus znowu dostępne. Zazwyczaj awaria nie powoduje utraty wiadomości lub inne dane. Przykład błędu składnika to niedostępność określonym magazynie obsługi komunikatów. Przykładem awarii całego centrum danych jest awarii zasilania w centrum danych lub przełącznika sieciowego błędny centrum danych. Awaria może trwać od kilku minut do kilku dni.

Po awarii jest zdefiniowany jako trwałą utratę jednostki skalowania usługi Service Bus lub centrum danych. Centrum danych mogą być lub może nie stanie się dostępny ponownie. Zazwyczaj awarii spowoduje utratę niektórych lub wszystkich wiadomości lub inne dane. Przykłady awarii to pożaru, przepełnieniu lub trzęsienie ziemi.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Ochrona przed wyłączeń i awarii — usługi Service Bus w warstwie Premium
Wysoka dostępność i odzyskiwanie awaryjne pojęcia wbudowanych warstwy usługi Azure Service Bus w warstwie Premium, zarówno w tym samym regionie (przy użyciu stref dostępności) i w różnych regionach (za pośrednictwem Geo-Disaster Recovery).

### <a name="geo-disaster-recovery"></a>Odzyskiwanie po awarii geograficznie

Service Bus w warstwie Premium obsługuje odzyskiwanie po awarii geograficznie, na poziomie przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [odzyskiwania po awarii geograficznej usługi Azure Service Bus](service-bus-geo-dr.md). Funkcja odzyskiwania po awarii, dostępne dla [jednostki SKU Premium](service-bus-premium-messaging.md) tylko implementuje odzyskiwanie po awarii metadane i opiera się na przestrzeni nazw odzyskiwania po awarii podstawowego i pomocniczego.

### <a name="availability-zones"></a>Strefy dostępności

Jednostka SKU usługi Service Bus Premium obsługuje [strefy dostępności](../availability-zones/az-overview.md), zapewniając izolowane od usterek lokalizacje w tym samym regionie platformy Azure.

> [!NOTE]
> Obsługa stref dostępności platformy Azure Service Bus w warstwie Premium jest dostępna tylko w [regionów świadczenia usługi Azure](../availability-zones/az-overview.md#services-support-by-region) gdzie strefy dostępności są obecne.

Strefy dostępności można włączyć na nowe przestrzenie nazw, przy użyciu witryny Azure portal. Usługa Service Bus nie obsługuje migracji istniejącej przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po jej włączeniu, w ramach przestrzeni nazw.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Ochrona przed wyłączeń i awarii — Standard magistrali usług
Aby osiągnąć odporności na awarie centrów danych przy użyciu standardowych komunikatów warstwy cenowej, Usługa Service Bus obsługuje dwa podejścia: *active* i *pasywnym* replikacji. Dla danej metody Jeśli danej kolejki lub tematu muszą pozostać dostępne obecności awarii centrum danych, można go utworzyć w obu tych przestrzeni nazw. Obie te jednostki można mieć taką samą nazwę. Na przykład kolejki głównej można z Tobą skontaktować w obszarze **contosoPrimary.servicebus.windows.net/myQueue**, natomiast jego odpowiednika dodatkowej można z Tobą skontaktować w obszarze **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> **Aktywna replikacja** i **pasywnym replikacji** instalacji są rozwiązań ogólnego przeznaczenia i nie określonych funkcji usługi Service Bus. Logiki replikacji (wysłanie do 2 różnych obszarach nazw) znajduje się na aplikacjach nadawca i odbiorca musi mieć niestandardowej logiki do wykrywania duplikatów.

Jeśli aplikacja wymaga trwałych komunikacji nadawcy do odbiorcy, aplikacja można zaimplementować trwałej kolejce po stronie klienta, aby zapobiec utracie wiadomości i włączyć osłonę nadawca wszelkich przejściowych błędów usługi Service Bus.

### <a name="active-replication"></a>Aktywna replikacja
Aktywna replikacja korzysta z jednostek w obu tych przestrzeni nazw dla każdej operacji. Każdy klient, który jest wysyłany komunikat wysyła dwie kopie tego samego komunikatu. Pierwsza kopia są wysyłane do podstawowej jednostki (na przykład **contosoPrimary.servicebus.windows.net/sales**), i drugą kopię wiadomości są wysyłane do dodatkowej jednostki (na przykład  **contosoSecondary.servicebus.windows.net/sales**).

Klient odbiera komunikaty z kolejek o obu. Odbiorca przetwarza pierwsza kopia wiadomości, a druga kopia jest pominięty. Aby pominąć zduplikowanych komunikatów, nadawca musisz otagować każdy komunikat o unikatowym identyfikatorze. Obu kopiach wiadomości muszą być oznaczone tym samym identyfikatorze. Możesz użyć [BrokeredMessage.MessageId][BrokeredMessage.MessageId] lub [BrokeredMessage.Label][BrokeredMessage.Label] właściwości lub właściwość niestandardową do znakowania wiadomości. Odbiornik, musisz utrzymywać listę komunikatów, które już otrzymało.

[Replikację geograficzną za pomocą usługi Service Bus w warstwie standardowa][Geo-replication with Service Bus Standard Tier] w przykładzie pokazano aktywna replikacja jednostek do obsługi komunikatów.

> [!NOTE]
> Podejście aktywna replikacja podwaja się liczba operacji, w związku z tym takie podejście może prowadzić do wyższych kosztów.
> 
> 

### <a name="passive-replication"></a>Pasywnym replikacji
W przypadku usterek pasywnym replikacji używa tylko jeden z dwóch jednostek obsługi komunikatów. Klient wysyła komunikat do aktywnego jednostki. W przypadku niepowodzenia operacji na jednostce active z kodem błędu, który wskazuje, że centrum danych, który jest hostem aktywnej jednostki może być niedostępny, klient wysyła kopię wiadomości do tworzenia kopii zapasowej jednostki. W tym momencie aktywny i jednostek kopii zapasowej przełączyć role: wysyłanie klienta uwzględnia stare jednostki aktywne do nowych jednostek kopii zapasowej i starych jednostek kopii zapasowej jest nowa jednostka active. Jeśli niepowodzenie operacji zarówno wysyłać, uległy zmianie role dwie jednostki i zostanie zwrócony błąd.

Klient odbiera komunikaty z kolejek o obu. Ponieważ istnieje ryzyko, że odbiorca otrzyma dwie kopie tej samej wiadomości, odbiorca musi pomijania zduplikowanych komunikatów. Można pominąć duplikaty w taki sam sposób, zgodnie z opisem dla aktywnej replikacji.

Ogólnie rzecz biorąc pasywnym replikacja jest bardziej ekonomiczne niż aktywnej replikacji, ponieważ w większości przypadków odbywa się tylko jedna operacja. Czas oczekiwania, przepływność i koszt są identyczne z niezreplikowanej scenariusza.

Korzystając z pasywnego replikacji, w następujących scenariuszach komunikaty są zapominane lub odebrane dwa razy:

* **Opóźnienie wiadomości lub utratą**: Załóżmy, że nadawca pomyślnie wysłane m1 komunikat do kolejki głównej, a kolejki staje się niedostępny przed odbiornika odbiera m1. Nadawca wysyła m2 wyświetlony komunikat do kolejki dodatkowej. Jeśli podstawowy kolejka jest tymczasowo niedostępny, odbiornik odbiera m1 po kolejce znowu dostępne. W razie awarii odbiornika nigdy nie może zostać wyświetlony m1.
* **Duplikuj odbioru**: Załóżmy, że nadawca wysyła komunikat m do kolejki głównej. Usługa Service Bus pomyślnie przetwarza m, ale nie może wysłać odpowiedź. Po upłynie limit czasu operacji wysyłania, nadawca wysyła identyczną kopię m do kolejki dodatkowej. Jeśli odbiorca jest możliwość odbierania pierwszego kopiowania m, zanim kolejki głównej staje się niedostępny, odbiornik odbiera obu kopiach m, w tym samym czasie. Jeśli odbiornik nie jest możliwość odbierania pierwszego kopiowania m, zanim kolejki głównej staje się niedostępny, odbiornik początkowo odbiera drugą kopię m, ale odbiera drugą kopię m po udostępnieniu kolejki głównej.

[Replikację geograficzną za pomocą usługi Service Bus w warstwie standardowa][Geo-replication with Service Bus Standard Tier] w przykładzie pokazano pasywnym replikacji jednostki obsługi komunikatów.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Punktem końcowym przekaźnika ochronę przed awariami centrum danych lub awarii
Replikacja geograficzna z [usługi Azure Relay](../service-bus-relay/relay-what-is-it.md) punktów końcowych pozwala to usługa, która uwidacznia punkt końcowy usługi relay, być dostępny w obecności awarii usługi Service Bus. Aby osiągnąć replikacji geograficznej, usługi należy utworzyć dwa punkty końcowe usługi relay w różnych obszarach nazw. Przestrzenie nazw musi znajdować się w różnych centrach danych i dwa punkty końcowe muszą mieć różne nazwy. Na przykład podstawowego punktu końcowego można z Tobą skontaktować w obszarze **contosoPrimary.servicebus.windows.net/myPrimaryService**, natomiast jego odpowiednika dodatkowej można z Tobą skontaktować w obszarze **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Usługa następnie nasłuchuje na obu punktów końcowych, a klient może wywołać usługę za pośrednictwem dowolnego punktu końcowego. Aplikacja kliencka losowo wybiera jeden z przekaźników jako podstawowego punktu końcowego i wysyła jego żądanie do aktywnego punktu końcowego. Jeśli operacja zakończy się niepowodzeniem z kodem błędu, ten błąd wskazuje, że punkt końcowy usługi relay jest dostępne. Aplikacja otworzy kanał do endpoint kopii zapasowej i wysyła żądanie. W tym momencie aktywny i tworzenia kopii zapasowych punktów końcowych przełączyć role: aplikacja kliencka uwzględnia stare aktywny punkt końcowy był nowy punkt końcowy z kopii zapasowej i starych kopii zapasowych punktu końcowego jako nowy, aktywny punkt końcowy. Jeśli niepowodzenie operacji zarówno wysyłać, uległy zmianie role dwie jednostki i zostanie zwrócony błąd.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz następujące artykuły:

* [Usługa Azure odzyskiwanie po awarii geograficznego usługi Service Bus](service-bus-geo-dr.md)
* [Ciągłość działania usługi Azure SQL Database][Azure SQL Database Business Continuity]
* [Projektowanie aplikacji odpornych na błędy dla platformy Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
