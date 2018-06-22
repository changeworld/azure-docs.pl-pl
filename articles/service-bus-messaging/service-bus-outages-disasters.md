---
title: Izolacji aplikacji usługi Azure Service Bus przed awariami i awarii | Dokumentacja firmy Microsoft
description: Technik w celu ochrony aplikacji przed potencjalnych awarii usługi Service Bus.
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/14/2018
ms.author: sethm
ms.openlocfilehash: 1d960349b50e2618365fd085cba7b3e55fa53874
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301720"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Najlepsze rozwiązania dotyczące izolacji aplikacji przed awariami usługi Service Bus i awarii

Kluczowych aplikacji musi działać w sposób ciągły, nawet obecności nieplanowanych przestojów lub awarii. W tym artykule opisano metod, które służy do ochrony aplikacji usługi Service Bus względem potencjalnych awarii usługi lub po awarii.

Awaria jest zdefiniowany jako tymczasowej niedostępności usługi Azure Service Bus. Awaria może mieć wpływ na niektórych składników usługi Service Bus, takich jak magazynie obsługi komunikatów, lub nawet całe centrum danych. Po problem został rozwiązany, usługi Service Bus znowu dostępne. Zazwyczaj awarii nie powoduje utraty wiadomości lub innych danych. Przykładem awarii składnika jest niedostępności określonym magazynie obsługi komunikatów. Przykład awaria sieci centrum danych jest awarii zasilania w centrum danych lub przełącznik sieciowy błędny centrum danych. Awaria może trwać od kilku minut do kilku dni.

Awarii jest zdefiniowany jako trwałą utratę jednostki skalowania usługi Service Bus lub datacenter. Centrum danych może lub nie może stać się dostępne ponownie. Zazwyczaj awarii spowoduje, że niektóre lub wszystkie komunikaty lub innych danych. Przykłady awarii fire, zalewania lub trzęsienie ziemi.

## <a name="current-architecture"></a>Architektura bieżącego
Usługa Service Bus używa wiele magazynów obsługi komunikatów do przechowywania komunikatów, które są wysyłane do kolejki i tematy. Niepartycjonowany kolejka lub temat jest przypisany do jednego magazynu obsługi komunikatów. Ten magazyn obsługi komunikatów jest niedostępny, wszystkie operacje dla tej kolejki lub temat zakończy się niepowodzeniem.

Wszystkie jednostki obsługi komunikatów usługi Service Bus (kolejek, tematów, przekaźników) znajdują się w przestrzeni nazw usługi, które jest powiązane z centrum danych. Usługa Service Bus obsługuje teraz [ *odzyskiwania po awarii geograficznie* i *— replikacja geograficzna* ](service-bus-geo-dr.md) na poziomie przestrzeni nazw.

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Ochrona kolejek i tematów z błędami magazynu do obsługi komunikatów
Niepartycjonowany kolejka lub temat jest przypisany do jednego magazynu obsługi komunikatów. Ten magazyn obsługi komunikatów jest niedostępny, wszystkie operacje dla tej kolejki lub temat zakończy się niepowodzeniem. Kolejki podzielonym na partycje, z drugiej strony, składa się z wielu fragmentów. Każdy fragment są przechowywane w różnych magazynie obsługi komunikatów. Po wysłaniu wiadomości do kolejki podzielonym na partycje lub tematu usługi Service Bus przypisuje wiadomości na jeden z fragmentów. Jeśli odpowiedni magazyn obsługi komunikatów jest niedostępny, usługi Service Bus zapisuje komunikat różnych fragment, jeśli to możliwe. Partycjonowane jednostki są już obsługiwane w [warstwy Premium](service-bus-premium-messaging.md). 

Aby uzyskać więcej informacji na temat partycjonowane jednostki, zobacz [partycjonowane jednostki do obsługi komunikatów][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Ochrona przed przestojami centrum danych lub awarii
Aby zezwolić na przejściu w tryb failover między dwoma centrami danych, można utworzyć przestrzeni nazw usługi Service Bus w każde centrum danych. Na przykład nazw usługi Service Bus **contosoPrimary.servicebus.windows.net** może znajdować się w regionie Stanów Zjednoczonych północ/centralnego i **contosoSecondary.servicebus.windows.net**może znajdować się w regionie nam Południowa/centralnego. Jeśli jednostki do obsługi komunikatów usługi Service Bus musi pozostać dostępna obecności awarii centrum danych, możesz utworzyć jednostkę w obu tych przestrzeni nazw.

Aby uzyskać więcej informacji, zobacz sekcję "Niepowodzenie usługi Service Bus w obrębie centrum danych Azure" w [asynchroniczne wzorce i wysokiej dostępności do obsługi komunikatów][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Ochrona punktów końcowych przekazywania względem przestojami centrum danych lub awarii
Replikacja geograficzna punktów końcowych przekazywania umożliwia to usługa, która udostępnia punkt końcowy przekazywania, aby był dostępny obecności awarie usługi Service Bus. Uzyskanie — replikacja geograficzna, usługi należy utworzyć dwa punkty końcowe przekazywania w różnych przestrzeniach nazw. Przestrzenie nazw musi znajdować się w różnych centrach danych i dwa punkty końcowe muszą mieć różne nazwy. Na przykład podstawowy punkt końcowy jest osiągalny w obszarze **contosoPrimary.servicebus.windows.net/myPrimaryService**, a jego odpowiednikiem dodatkowej można połączyć się w obszarze **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Usługa następnie nasłuchuje na obu punktów końcowych, a klient może wywołać usługę za pośrednictwem albo punktu końcowego. Aplikacja kliencka losowo wybiera jeden z przekaźników jako podstawowy punkt końcowy i wysyła żądania do aktywnego punktu końcowego. Jeśli operacja zakończy się niepowodzeniem z kodem błędu, to niepowodzenie oznacza przekazywania punktu końcowego nie jest dostępna. Aplikacja otworzy kanał do zapasowego punktu końcowego i wysyła żądanie. W tym momencie kopii zapasowej punktów końcowych i aktywne przełączyć role: aplikacja kliencka uwzględnia starego aktywny punkt końcowy jako nowy punkt końcowy z kopii zapasowej, a stare zapasowego punktu końcowego jako nowy aktywny punkt końcowy. Jeśli niepowodzenie operacji zarówno wysyłania, role dwie jednostki pozostają bez zmian i zostanie zwrócony błąd.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Ochrona kolejek i tematów względem przestojami centrum danych lub awarii
Aby osiągnąć odporność na awarie centrum danych przy użyciu komunikatów obsługiwanych przez brokera, Usługa Service Bus obsługuje dwa podejścia: *active* i *pasywnym* replikacji. Dla każdego z podejść Jeśli dany kolejka lub temat musi pozostać dostępna obecności awarii centrum danych, można go utworzyć w obu tych przestrzeni nazw. Obie te jednostki mogą mieć takiej samej nazwy. Na przykład w obszarze osiągalna kolejki głównej **contosoPrimary.servicebus.windows.net/myQueue**, a jego odpowiednikiem dodatkowej można połączyć się w obszarze **contosoSecondary.servicebus.windows.net/myQueue**.

Jeśli aplikacja nie wymaga stałej łączności nadawcy do odbiornika, aplikację można zaimplementować trwałe kolejki po stronie klienta, aby zapobiec utracie wiadomości i aby nadawca z przejściowych błędów usługi Service Bus.

## <a name="active-replication"></a>Aktywna replikacja
Aktywna replikacja używa jednostek w obu tych przestrzeni nazw dla każdej operacji. Każdy klient, który wysyła komunikat wysyła dwie kopie tej samej wiadomości. Pierwsza kopia są wysyłane do podstawowej jednostki (na przykład **contosoPrimary.servicebus.windows.net/sales**), i druga kopia wiadomości są wysyłane do dodatkowej jednostki (na przykład  **contosoSecondary.servicebus.windows.net/sales**).

Klient odbiera komunikaty z kolejek obu. Odbiornika przetwarza pierwsza kopia wiadomości, a druga kopia jest pomijane. Do pomijania zduplikowanych komunikatów, nadawca musi oznaczać każdy komunikat z unikatowym identyfikatorem. Obydwie kopie wiadomości muszą być oznakowane o tym samym identyfikatorze. Można użyć [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] lub [BrokeredMessage.Label] [ BrokeredMessage.Label] właściwości lub właściwości niestandardowych do znakowania wiadomości. Odbiornik muszą zachować listę komunikatów, które już została odebrana.

[— Replikacja geograficzna z komunikatów obsługiwanych przez brokera magistrali usługi] [ Geo-replication with Service Bus Brokered Messages] przykładzie pokazano aktywna replikacja jednostek do obsługi komunikatów.

> [!NOTE]
> Podejście aktywna replikacja podwaja liczby operacji, dlatego ta metoda może prowadzić do wyższe koszty.
> 
> 

## <a name="passive-replication"></a>Pasywne replikacji
W przypadku awarii bez replikacji pasywnym używa tylko jeden z dwóch jednostek obsługi komunikatów. Klient wysyła wiadomość do aktywnych jednostek. W przypadku niepowodzenia operacji na obiekcie active kod błędu, który wskazuje, że centrum danych, który jest hostem aktywnej jednostki może być niedostępny, klient wysyła kopię wiadomości do tworzenia kopii zapasowej jednostki. W tym momencie aktywne i jednostek kopii zapasowej przełączyć role: wysyłania klienta uwzględnia starego active jednostki jako nowego obiektu kopii zapasowej, a stare jednostka kopii zapasowej jest nowy obiekt active. Jeśli niepowodzenie operacji zarówno wysyłania, role dwie jednostki pozostają bez zmian i zostanie zwrócony błąd.

Klient odbiera komunikaty z kolejek obu. Ponieważ istnieje ryzyko, że odbiorca otrzyma dwie kopie tej samej wiadomości, odbiorca musi pomijania zduplikowanych wiadomości. Można pominąć duplikaty w taki sam sposób, zgodnie z opisem dla aktywnej replikacji.

Ogólnie rzecz biorąc pasywnym replikacji jest bardziej ekonomiczne niż replikacja usługi active, ponieważ w większości przypadków jest wykonywane tylko jedna operacja. Czas oczekiwania, przepływności i koszt są identyczne do scenariusza, które nie są replikowane.

Korzystając z pasywnym replikacji, w następujących scenariuszach komunikaty mogą być utracone lub odbierane dwa razy:

* **Opóźnienie wiadomości lub utraty**: założono, że nadawca pomyślnie wysłał m1 wiadomości do kolejki głównej i kolejki staje się niedostępna przed odbiornika odbiera m1. Nadawca wysyła m2 kolejnych komunikatów Szukaj komunikatu do kolejki dodatkowej. Jeśli podstawowy kolejka jest tymczasowo niedostępny, odbiorca odbiera m1 po kolejki znowu dostępne. W przypadku awarii odbiorca nigdy nie może zostać wyświetlony m1.
* **Zduplikowana odbioru**: założono, że nadawca wysyła komunikat m do kolejki głównej. Usługa Service Bus pomyślnie przetwarza m, ale nie może wysłać odpowiedzi. Po upływie limitu czasu operacji wysyłania, nadawca wysyła identyczne Kopiuj mln do kolejki dodatkowej. Jeśli odbiornik może odbierać pierwszego kopiowania m, aby kolejki głównej stał się niedostępny, odbiorca odbiera obydwie kopie m w tym samym czasie. Odbiornik nie będzie mógł odbierać pierwszego kopiowania m przed kolejki podstawowy stanie się niedostępny, odbiornika początkowo odbiera drugą kopię m, ale otrzyma kopię m po udostępnieniu kolejki głównej.

[— Replikacja geograficzna z usługą Service Bus obsługiwanych przez brokera komunikatów] [ Geo-replication with Service Bus Brokered Messages] przykładzie pokazano pasywnym replikacji jednostki do obsługi komunikatów.

## <a name="geo-replication"></a>Replikacja geograficzna

Usługa Service Bus obsługuje odzyskiwania po awarii geograficznie i replikacja geograficzna, na poziomie przestrzeni nazw. Aby uzyskać więcej informacji, zobacz [Azure Service Bus geograficznie-odzyskiwaniem](service-bus-geo-dr.md). Funkcja odzyskiwania po awarii, dostępna dla [warstwy Premium](service-bus-premium-messaging.md) tylko implementuje odzyskiwania po awarii metadanych i zależy od usługi przestrzenie nazw odzyskiwania po awarii podstawowego i pomocniczego.

## <a name="availability-zones-preview"></a>Dostępność strefy (wersja zapoznawcza)

Warstwy Premium magistrali usługi obsługuje [stref dostępności](../availability-zones/az-overview.md), zapewniając izolowane błędów lokalizacji w obrębie regionu platformy Azure. 

> [!NOTE]
> Podgląd stref dostępności jest obsługiwana tylko w programie **środkowe stany USA**, **wschodnie stany USA 2**, i **Francja centralnej** regionów.

Dostępność strefy można włączyć w nowej przestrzeni nazw, przy użyciu portalu Azure. Usługa Service Bus nie obsługuje migracji istniejącej przestrzeni nazw. Nie można wyłączyć nadmiarowość strefy po jej włączeniu na przestrzeni nazw.

![1][]

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat odzyskiwania po awarii, zobacz następujące artykuły:

* [Azure awarii usługi Service Bus Geo](service-bus-geo-dr.md)
* [Ciągłość prowadzenia działalności biznesowej bazy danych Azure SQL][Azure SQL Database Business Continuity]
* [Projektowanie aplikacji odpornych na błędy dla platformy Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png