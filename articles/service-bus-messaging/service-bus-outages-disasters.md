---
title: Izoluj aplikacje usługi Azure Service Bus przed awariami i awariami
description: W tym artykułach przedstawiono techniki ochrony aplikacji przed potencjalną awarią usługi Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 07b071b0e8efc5d664dada133a214d778c6531d0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984950"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Najlepsze rozwiązania dotyczące izolowania aplikacji od wyłączeń i awarii usługi Service Bus

Aplikacje o znaczeniu krytycznym muszą działać w sposób ciągły, nawet w przypadku nieplanowanych awarii lub awarii. W tym artykule opisano techniki, których można użyć do ochrony aplikacji usługi Service Bus przed potencjalną awarią lub awarią usługi.

Awaria jest definiowana jako tymczasowa niedostępność usługi Azure Service Bus. Awaria może mieć wpływ na niektóre składniki usługi Service Bus, takie jak magazyn wiadomości lub nawet całe centrum danych. Po naprawieniu problemu usługa Service Bus staje się ponownie dostępna. Zazwyczaj awaria nie powoduje utraty wiadomości lub innych danych. Przykładem awarii składnika jest niedostępność określonego magazynu obsługi wiadomości. Przykładem awarii w całym centrum danych jest awaria zasilania centrum danych lub wadliwy przełącznik sieciowy centrum danych. Awaria może trwać od kilku minut do kilku dni.

Awaria jest definiowana jako trwała utrata jednostki skalowania usługi Service Bus lub centrum danych. Centrum danych może lub nie może stać się ponownie dostępne. Zazwyczaj awaria powoduje utratę niektórych lub wszystkich wiadomości lub innych danych. Przykładami katastrof są pożar, powodzie lub trzęsienie ziemi.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Ochrona przed awariami i awariami — Service Bus Premium
Koncepcje wysokiej dostępności i odzyskiwania po awarii są wbudowane bezpośrednio w warstwę usługi Azure Service Bus Premium, zarówno w tym samym regionie (za pośrednictwem stref dostępności), jak i w różnych regionach (za pośrednictwem odzyskiwania po awarii geograficznej).

### <a name="geo-disaster-recovery"></a>Odzyskiwanie po awarii geograficznej

Usługa Service Bus Premium obsługuje odzyskiwanie po awarii geograficznej na poziomie obszaru nazw. Aby uzyskać więcej informacji, zobacz [Odzyskiwanie po awarii geograficznej usługi Azure Service Bus](service-bus-geo-dr.md). Funkcja odzyskiwania po awarii, dostępna tylko dla [jednostki SKU w warstwie Premium,](service-bus-premium-messaging.md) implementuje odzyskiwanie po awarii metadanych i opiera się na podstawowych i pomocniczych obszarach nazw odzyskiwania po awarii.

### <a name="availability-zones"></a>Strefy dostępności

Usługa Service Bus Premium SKU obsługuje [strefy dostępności,](../availability-zones/az-overview.md)zapewniając lokalizacje izolowane od błędów w tym samym regionie platformy Azure. Usługa Service Bus zarządza trzema kopiami magazynu wiadomości (1 podstawowy i 2 pomocniczy). Usługa Service Bus synchronizuje wszystkie trzy kopie w celu obsługi operacji zarządzania danymi i zarządzaniem. Jeśli kopia podstawowa nie powiedzie się, jedna z kopii pomocniczych jest podyzywana do podstawowego bez postrzeganego przestoju. Jeśli aplikacje zobacz przejściowe rozłącza z usługą Service Bus, logiki ponawiania w SDK automatycznie ponownie połączy się z usługą Service Bus. 

> [!NOTE]
> Obsługa stref dostępności usługi Azure Service Bus Premium jest dostępna tylko w [regionach platformy Azure,](../availability-zones/az-overview.md#services-support-by-region) w których znajdują się strefy dostępności.

Strefy dostępności można włączyć tylko w nowych obszarach nazw, korzystając z witryny Azure portal. Usługa Service Bus nie obsługuje migracji istniejących obszarów nazw. Nadmiarowość strefy nie może być wyłączna po włączeniu jej w obszarze nazw.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Ochrona przed awariami i awariami — standard magistrali usług
Aby osiągnąć odporność na awarie centrum danych podczas korzystania ze standardowej warstwy cenowej obsługi wiadomości, usługa Service Bus obsługuje dwa podejścia: *replikację aktywną* i *pasywną.* Dla każdego podejścia, jeśli dana kolejka lub temat musi pozostać dostępne w obecności awarii centrum danych, można utworzyć go w obu obszarach nazw. Obie jednostki mogą mieć taką samą nazwę. Na przykład kolejkę podstawową można osiągnąć w obszarze **contosoPrimary.servicebus.windows.net/myQueue**, podczas gdy jej drugorzędny odpowiednik można osiągnąć w **obszarze contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> Konfiguracja **replikacji aktywnej** i **replikacji pasywnej** to rozwiązania ogólnego przeznaczenia, a nie określone funkcje usługi Service Bus. Logika replikacji (wysyłanie do 2 różnych obszarów nazw) dotyczy aplikacji nadawcy, a odbiorca musi mieć niestandardową logikę wykrywania duplikatów.

Jeśli aplikacja nie wymaga stałej komunikacji nadawca-odbiorca, aplikacja może zaimplementować trwałą kolejkę po stronie klienta, aby zapobiec utracie wiadomości i chronić nadawcę przed wszelkimi błędami przejściowej usługi Service Bus.

### <a name="active-replication"></a>Aktywna replikacja
Aktywna replikacja używa jednostek w obu obszarach nazw dla każdej operacji. Każdy klient, który wysyła wiadomość wysyła dwie kopie tej samej wiadomości. Pierwsza kopia jest wysyłana do jednostki podstawowej (na przykład **contosoPrimary.servicebus.windows.net/sales),** a druga kopia wiadomości jest wysyłana do jednostki pomocniczej (na przykład **contosoSecondary.servicebus.windows.net/sales**).

Klient odbiera wiadomości z obu kolejek. Odbiornik przetwarza pierwszą kopię wiadomości, a druga kopia jest pomijana. Aby pominąć zduplikowane wiadomości, nadawca musi oznaczyć każdą wiadomość unikatowym identyfikatorem. Obie kopie wiadomości muszą być oznaczone tym samym identyfikatorem. Można użyć [BrokeredMessage.MessageId][BrokeredMessage.MessageId] lub [BrokeredMessage.Label][BrokeredMessage.Label] właściwości lub właściwości niestandardowej do tagowania wiadomości. Odbiorca musi obsługiwać listę wiadomości, które już otrzymał.

[Przykład replikacji geograficznej z warstwą standardowa usługi Service Bus][Geo-replication with Service Bus Standard Tier] pokazuje aktywną replikację jednostek obsługi wiadomości.

> [!NOTE]
> Podejście aktywnej replikacji podwaja liczbę operacji, w związku z tym takie podejście może prowadzić do wyższych kosztów.
> 
> 

### <a name="passive-replication"></a>Replikacja pasywna
W przypadku bezbędy replikacji pasywnej używa tylko jednej z dwóch jednostek obsługi wiadomości. Klient wysyła wiadomość do aktywnej jednostki. Jeśli operacja na aktywnej jednostce zakończy się niepowodzeniem przy obliczu kodu błędu wskazującego, że centrum danych, w którym znajduje się aktywna jednostka, może być niedostępne, klient wysyła kopię wiadomości do jednostki kopii zapasowej. W tym momencie aktywne i jednostki kopii zapasowej przełączają role: klient wysyłający uważa starą aktywną jednostkę za nową jednostkę kopii zapasowej, a stara jednostka kopii zapasowej jest nową aktywną jednostką. Jeśli obie operacje wysyłania nie powiodą się, role obu jednostek pozostają niezmienione i zwracany jest błąd.

Klient odbiera wiadomości z obu kolejek. Ponieważ istnieje prawdopodobieństwo, że odbiorca otrzyma dwie kopie tej samej wiadomości, odbiorca musi pominąć zduplikowane komunikaty. Duplikaty można pominąć w taki sam sposób, jak opisano w przypadku aktywnej replikacji.

Ogólnie rzecz biorąc replikacja pasywna jest bardziej ekonomiczna niż replikacja aktywna, ponieważ w większości przypadków wykonywana jest tylko jedna operacja. Opóźnienie, przepływność i koszt pieniężny są identyczne ze scenariuszem niezreplikowanym.

Podczas korzystania z replikacji pasywnej, w następujących scenariuszach wiadomości mogą zostać utracone lub odebrane dwukrotnie:

* **Opóźnienie lub utrata wiadomości:** Załóżmy, że nadawca pomyślnie wysłał wiadomość m1 do kolejki podstawowej, a następnie kolejka staje się niedostępna, zanim odbiorca odbierze m1. Nadawca wysyła kolejną wiadomość m2 do kolejki pomocniczej. Jeśli kolejka podstawowa jest tymczasowo niedostępna, odbiorca odbiera m1 po ponownym udostępnieniu kolejki. W przypadku awarii odbiornik może nigdy nie otrzymać m1.
* **Zduplikowany odbiór:** Załóżmy, że nadawca wysyła wiadomość m do kolejki podstawowej. Usługa Service Bus pomyślnie przetwarza m, ale nie można wysłać odpowiedzi. Po upływie czasu operacji wysyłania nadawca wysyła identyczną kopię m do kolejki pomocniczej. Jeśli odbiorca jest w stanie odebrać pierwszą kopię m, zanim kolejka podstawowa stanie się niedostępna, odbiorca otrzyma obie kopie m w przybliżeniu w tym samym czasie. Jeśli odbiorca nie jest w stanie odebrać pierwszą kopię m przed kolejki podstawowej staje się niedostępny, odbiorca początkowo odbiera tylko drugą kopię m, ale następnie odbiera drugą kopię m, gdy kolejka podstawowa staje się dostępna.

[Przykład replikacji geograficznej z warstwą standardowa usługi Service Bus][Geo-replication with Service Bus Standard Tier] demonstruje pasywną replikację jednostek obsługi wiadomości.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Ochrona punktów końcowych przekazywania przed awariami lub awariami centrum danych
Replikacja geograficzna punktów końcowych [usługi Azure Relay](../service-bus-relay/relay-what-is-it.md) umożliwia usłudze, która udostępnia punkt końcowy przekazywania, aby był osiągalny w obecności awarii usługi Service Bus. Aby osiągnąć replikację geograficzną, usługa musi utworzyć dwa punkty końcowe przekazywania w różnych obszarach nazw. Obszary nazw muszą znajdować się w różnych centrach danych, a dwa punkty końcowe muszą mieć różne nazwy. Na przykład podstawowy punkt końcowy można osiągnąć w obszarze **contosoPrimary.servicebus.windows.net/myPrimaryService,** podczas gdy jego drugorzędny odpowiednik można osiągnąć w **obszarze contosoSecondary.servicebus.windows.net/mySecondaryService**.

Usługa następnie nasłuchuje w obu punktach końcowych, a klient może wywołać usługę za pośrednictwem jednego punktu końcowego. Aplikacja kliencka losowo wybiera jeden z przekaźników jako podstawowy punkt końcowy i wysyła jego żądanie do aktywnego punktu końcowego. Jeśli operacja nie powiedzie się z kodem błędu, ten błąd wskazuje, że punkt końcowy przekaźnika nie jest dostępny. Aplikacja otwiera kanał do punktu końcowego kopii zapasowej i ponownie wysuwa żądanie. W tym momencie aktywne i zapasowe punkty końcowe przełączają role: aplikacja kliencka uważa stary aktywny punkt końcowy za nowy punkt końcowy kopii zapasowej, a stary punkt końcowy kopii zapasowej za nowy aktywny punkt końcowy. Jeśli obie operacje wysyłania nie powiodą się, role obu jednostek pozostają niezmienione i zwracany jest błąd.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o odzyskiwaniu po awarii, zobacz następujące artykuły:

* [Odzyskiwanie geograficzne usługi Azure Service Bus](service-bus-geo-dr.md)
* [Ciągłość działania bazy danych SQL platformy Azure][Azure SQL Database Business Continuity]
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
