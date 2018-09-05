---
title: Azure odzyskiwanie po awarii geograficznego usługi Service Bus | Dokumentacja firmy Microsoft
description: Jak używać regionów geograficznych do trybu failover i odzyskiwanie danych po awarii w usłudze Azure Service Bus
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/14/2018
ms.author: spelluru
ms.openlocfilehash: 42960222efb1ade1b48a1d0db56ae3fb0349d174
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/05/2018
ms.locfileid: "43695395"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Usługa Azure odzyskiwanie po awarii geograficznego usługi Service Bus

Gdy cały regiony platformy Azure lub w centrach danych (Jeśli nie [strefy dostępności](../availability-zones/az-overview.md) są używane) doświadczają przestoju, koniecznie do przetwarzania danych w dalszym ciągu działać w innym regionie lub w centrum danych. W efekcie *odzyskiwania po awarii geograficznie* i *Geografickou replikaci* są ważne funkcje w każdej firmie. Usługa Azure Service Bus obsługuje zarówno odzyskiwania po awarii geograficzne i replikacja geograficzna, na poziomie przestrzeni nazw. 

Funkcji odzyskiwania po awarii geograficznej jest ogólnie dostępna dla jednostki SKU usługi Service Bus Premium. 

## <a name="outages-and-disasters"></a>Wyłączeń i awarii

Ważne jest, aby należy zauważyć różnicę między "wyłączeń" i "awarii." *Awarii* jest tymczasowa niedostępność usługi Azure Service Bus i może mieć wpływ na niektóre składniki usługi, takie jak magazyn obsługi komunikatów lub nawet całe centrum danych. Jednak po usunięciu problemu usługi Service Bus znowu dostępne. Zazwyczaj awaria nie powoduje utraty wiadomości lub inne dane. Przykładem takich awarii może być awaria zasilania w centrum danych. Niektóre awarie są tylko połączenia krótki straty ze względu na problemy przejściowe lub sieci. 

A *po awarii* jest zdefiniowany jako stałe lub dłuższy okres utraty klastra usługi Service Bus, region platformy Azure lub centrum danych. Region lub centrum danych może nie może stać się dostępna ponownie lub może nie działać przez kilka godzin lub dni. Przykładami takich awarii są pożaru, przepełnieniu lub trzęsienie ziemi. Po awarii, która staje się stałe może spowodować utratę niektórych komunikatów, zdarzenia lub inne dane. Jednak w większości przypadków należy bez utraty danych, a komunikaty mogą być odzyskiwane po centrum danych zacznie ponownie działać.

Funkcja odzyskiwania po awarii geograficznej usługi Azure Service bus to rozwiązanie odzyskiwania po awarii. Pojęcia i przepływ pracy opisany w tym artykule mają zastosowanie do scenariuszy awarii, a nie przejściowe i tymczasowe awarii. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii na platformie Microsoft Azure, zobacz [w tym artykule](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Podstawowych pojęć i terminów

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadane i opiera się na przestrzeni nazw odzyskiwania po awarii podstawowego i pomocniczego. Należy pamiętać, że funkcji odzyskiwania po awarii geograficzna jest dostępna dla [jednostki SKU Premium](service-bus-premium-messaging.md) tylko. Nie trzeba wprowadzać żadnych zmian ciągu połączenia, ponieważ połączenie zostało nawiązane za pomocą aliasu.

W tym artykule są używane następujące terminy:

-  *Alias*: Nazwa konfiguracji odzyskiwania po awarii, który został ustawiony. Alias zapewnia pojedynczy ciąg stabilne połączenie w pełni kwalifikowanej domeny nazwę (FQDN). Aplikacje za pomocą te parametry połączenia aliasu połączyć z przestrzeni nazw. 

-  *Przestrzeń nazw podstawowy/pomocniczy*: przestrzenie nazw, które odnoszą się do aliasu. Podstawowa przestrzeń nazw jest "aktywny" i odbiera komunikaty (może to być istniejącej lub nowej przestrzeni nazw). Pomocnicza przestrzeń nazw jest "pasywny" i nie otrzymywać wiadomości. Metadanych między obiema jest zsynchronizowany, więc zarówno bezproblemowo może akceptować komunikaty bez wprowadzania żadnych zmian parametry aplikacji, jak połączenie lub kod. Aby upewnić się, że tylko aktywnej przestrzeni nazw odbiera komunikaty, musisz użyć tego aliasu. 

-  *Metadane*: jednostki, takie jak kolejek, tematów i subskrypcji; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy pamiętać, że tylko jednostek i ich ustawienia są replikowane automatycznie. Komunikaty nie są replikowane. 

-  *Tryb failover*: proces aktywacji pomocniczej przestrzeni nazw.

## <a name="setup-and-failover-flow"></a>Przepływ instalacji i trybu failover

Poniższa sekcja zawiera omówienie procesu pracy awaryjnej i wyjaśniono, jak skonfigurować początkowe trybu failover. 

![1][]

### <a name="setup"></a>Konfigurowanie

Możesz najpierw utworzyć lub użyj istniejącej głównej przestrzeni nazw i nowej pomocniczej przestrzeni nazw, a następnie łączyć dwóch. Połączenie umożliwia alias, który służy do łączenia. Ponieważ używasz aliasu, trzeba zmienić parametry połączenia. Można dodać tylko nowe przestrzenie nazw na parowanie zegarków trybu failover. Na koniec należy dodać pewne informacje monitorowania do wykrywania, jeśli konieczne jest przejścia w tryb failover. W większości przypadków usługa jest jedną z części dużego ekosystemu, tak więc automatycznego przejścia w tryb failover są rzadko jest to możliwe, jak bardzo często przejścia w tryb failover należy wykonać synchronizację z pozostałych podsystemu lub infrastruktury.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza należy wziąć pod uwagę rozwiązanie punktu sprzedaży (POS), który emituje komunikaty lub zdarzenia. Usługa Service Bus przekazuje te zdarzenia do niektórych mapowania lub ponowne sformatowanie rozwiązania, które następnie przekazuje zmapowanych danych do innego systemu w celu dalszego przetwarzania. W tym momencie wszystkich tych systemach mogą być hostowane w tym samym regionie platformy Azure. Decyzja w sprawie kiedy i jaka część do trybu failover jest zależna od przepływu danych w infrastrukturze. 

Można zautomatyzować trybu failover z systemów monitorowania lub za pomocą niestandardowej rozwiązania do monitorowania. Jednak takie automatyzacji zajmują dodatkowego planowania i współpracować, która wykracza poza zakres tego artykułu.

### <a name="failover-flow"></a>Przepływ pracy awaryjnej

Możesz zainicjować trybu failover, wymagane są dwa kroki:

1. Jeśli inna awaria wystąpi, chcesz mieć możliwość pracy awaryjnej ponownie. W związku z tym Ustaw innej pasywnym przestrzeni nazw i zaktualizuj parowanie. 

2. Ściągają komunikaty z poprzedniej wersji portalu podstawowej przestrzeni nazw, gdy będzie znowu dostępna. Po tym regularne komunikatów poza konfigurację odzyskiwania replikacji geograficznej na użytek tej przestrzeni nazw lub Usuń stare podstawowej przestrzeni nazw.

> [!NOTE]
> Obsługiwane są wyłącznie zakończyć się niepowodzeniem do przodu semantyki. W tym scenariuszu w trybie Failover, a następnie ponownie łączyć się z nową przestrzeń nazw. Powrotem nie jest obsługiwana; na przykład w klastrze programu SQL. 

![2][]

## <a name="management"></a>Zarządzanie

Jeśli w przypadku popełnienia; na przykład sparowane regiony problem podczas początkowej konfiguracji, może przerwać parowanie dwie przestrzeni nazw w dowolnym momencie. Jeśli chcesz użyć sparowane przestrzenie nazw jako regularne przestrzenie nazw, Usuń ten alias.

## <a name="use-existing-namespace-as-alias"></a>Użyj istniejącej przestrzeni nazw jako alias

W przypadku scenariusza, w której nie można zmienić połączenia producentów i konsumentów nazwa przestrzeni nazw można ponownie użyć jako aliasu. Zobacz [przykładowego kodu w usłudze GitHub tutaj](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Przykłady

[Przykłady w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) pokazują, jak skonfigurować i zainicjuj tryb failover. Te przykłady pokazują następujące pojęcia:

- Przykład .NET i ustawienia, które są wymagane w usłudze Azure Active Directory na potrzeby usługi Azure Resource Manager z usługą Service Bus, skonfigurować i włączyć odzyskiwanie po awarii geograficznie.
- Kroki wymagane do wykonania w przykładowym kodzie.
- Jak używać istniejącej przestrzeni nazw jako alias.
- Kroki można również włączyć odzyskiwanie po awarii geograficzną za pomocą programu PowerShell lub interfejsu wiersza polecenia.
- [Wysyłanie i odbieranie](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z bieżącym podstawowej lub dodatkowej przestrzeni nazw za pomocą aliasu.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę następujące kwestie, które należy uwzględnić w tej wersji:

1. W procesie planowania trybu failover, należy również rozważyć współczynnik czasu. Na przykład jeśli w przypadku utraty łączności przez czas dłuższy niż 15-20 minut, można zdecydować do zainicjowania trybu failover. 
 
2. Fakt, że żadne dane nie są replikowane oznacza, że obecnie aktywnych sesji nie są replikowane. Ponadto wykrywania duplikatów i zaplanowane komunikaty mogą nie działać. Nowej sesji, nowe zaplanowane wiadomości i nowe duplikaty będą działać. 

3. Przechodzenie w tryb failover złożonych rozproszonej infrastruktury powinny być [rehearsed](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) co najmniej raz. 

4. Synchronizowanie jednostek może zająć trochę czasu około 50 – 100 jednostek na minutę. Subskrypcje i reguł również liczone jako jednostki. 

## <a name="availability-zones-preview"></a>Strefy dostępności (wersja zapoznawcza)

Jednostka SKU usługi Service Bus Premium obsługuje również [strefy dostępności](../availability-zones/az-overview.md), zapewniając izolowane od usterek lokalizacje w regionie platformy Azure. 

> [!NOTE]
> Strefy dostępności w wersji zapoznawczej jest obsługiwana tylko w programie **środkowe stany USA**, **wschodnie stany USA 2**, i **Francja środkowa** regionów.

Strefy dostępności można włączyć na nowe przestrzenie nazw, przy użyciu witryny Azure portal. Usługa Service Bus nie obsługuje migracji istniejącej przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po jej włączeniu, w ramach przestrzeni nazw.

![3][]

## <a name="next-steps"></a>Kolejne kroki

- Zobacz odzyskiwania po awarii geograficznie [tutaj dokumentacja interfejsu API REST](/rest/api/servicebus/disasterrecoveryconfigs).
- Uruchom odzyskiwanie po awarii geograficznie [próbki w serwisie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Zobacz odzyskiwania po awarii geograficznie [próbki, która wysyła komunikaty do aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Aby dowiedzieć się więcej na temat obsługi komunikatów usługi Service Bus, zobacz następujące artykuły:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Interfejs API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png