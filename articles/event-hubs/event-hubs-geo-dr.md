---
title: Odzyskiwanie po awarii Geo - usługi Azure Event Hubs | Dokumentacja firmy Microsoft
description: Jak używać regionów geograficznych do trybu failover i odzyskiwanie danych po awarii w usłudze Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 8dca94f0200f6bd41dfdc199b41bf69981a960da
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69562702"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Usługa Azure Event Hubs — odzyskiwanie po awarii geograficznie 

Gdy cały regiony platformy Azure lub w centrach danych (Jeśli nie [strefy dostępności](../availability-zones/az-overview.md) są używane) doświadczają przestoju, koniecznie do przetwarzania danych w dalszym ciągu działać w innym regionie lub w centrum danych. W efekcie *odzyskiwania po awarii geograficznie* i *Geografickou replikaci* są ważne funkcje w każdej firmie. Usługa Azure Event Hubs obsługuje zarówno odzyskiwania po awarii geograficzne i replikacja geograficzna, na poziomie przestrzeni nazw. 

Funkcja odzyskiwania po awarii geograficznej jest globalnie dostępna dla systemów Event Hubs Standard i dedykowana jednostka SKU. Należy pamiętać, że w tej samej warstwie jednostki SKU można tylko przestrzenie nazw z parą geograficzną. Na przykład jeśli masz przestrzeń nazw w klastrze, który jest oferowany tylko w dedykowanej jednostce SKU, można sparować tylko z przestrzenią nazw w innym klastrze. 

## <a name="outages-and-disasters"></a>Wyłączeń i awarii

Ważne jest, aby należy zauważyć różnicę między "wyłączeń" i "awarii." *Awarii* jest tymczasowa niedostępność usługi Azure Event Hubs i może mieć wpływ na niektóre składniki usługi, takie jak magazyn obsługi komunikatów lub nawet całe centrum danych. Jednak po usunięciu problemu usługi Event Hubs staje się dostępny ponownie. Zazwyczaj awaria nie powoduje utraty wiadomości lub inne dane. Przykładem takich awarii może być awaria zasilania w centrum danych. Niektóre awarie są tylko połączenia krótki straty ze względu na problemy przejściowe lub sieci. 

A *po awarii* jest zdefiniowany jako stałe lub dłuższy okres utraty klastra usługi Event Hubs, region platformy Azure lub centrum danych. Region lub centrum danych może nie może stać się dostępna ponownie lub może nie działać przez kilka godzin lub dni. Przykładami takich awarii są pożaru, przepełnieniu lub trzęsienie ziemi. Po awarii, która staje się stałe może spowodować utratę niektórych komunikatów, zdarzenia lub inne dane. Jednak w większości przypadków należy bez utraty danych, a komunikaty mogą być odzyskiwane po centrum danych zacznie ponownie działać.

Funkcja odzyskiwania po awarii geograficznej usługi Azure Event Hubs jest rozwiązanie odzyskiwania po awarii. Pojęcia i przepływ pracy opisany w tym artykule mają zastosowanie do scenariuszy awarii, a nie przejściowe i tymczasowe awarii. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii na platformie Microsoft Azure, zobacz [w tym artykule](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Podstawowych pojęć i terminów

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadane i opiera się na przestrzeni nazw odzyskiwania po awarii podstawowego i pomocniczego. Należy pamiętać, że funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [standardowych i dedykowanych jednostek SKU](https://azure.microsoft.com/pricing/details/event-hubs/) . Nie trzeba wprowadzać żadnych zmian ciągu połączenia, ponieważ połączenie zostało nawiązane za pomocą aliasu.

W tym artykule są używane następujące terminy:

-  *Alias*: Nazwa skonfigurowanej konfiguracji odzyskiwania po awarii. Alias zapewnia pojedynczy ciąg stabilne połączenie w pełni kwalifikowanej domeny nazwę (FQDN). Aplikacje za pomocą te parametry połączenia aliasu połączyć z przestrzeni nazw. 

-  *Podstawowa/pomocnicza przestrzeń nazw*: Przestrzenie nazw, które odnoszą się do aliasu. Podstawowa przestrzeń nazw jest "aktywny" i odbiera komunikaty (może to być istniejącej lub nowej przestrzeni nazw). Pomocnicza przestrzeń nazw jest "pasywny" i nie otrzymywać wiadomości. Metadanych między obiema jest zsynchronizowany, więc zarówno bezproblemowo może akceptować komunikaty bez wprowadzania żadnych zmian parametry aplikacji, jak połączenie lub kod. Aby upewnić się, że tylko aktywnej przestrzeni nazw odbiera komunikaty, musisz użyć tego aliasu. 

-  *Metadane*: Jednostki, takie jak centra zdarzeń i grupy konsumentów; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy pamiętać, że tylko jednostek i ich ustawienia są replikowane automatycznie. Komunikaty i zdarzenia nie są replikowane. 

-  *Tryb failover*: Proces aktywowania pomocniczej przestrzeni nazw.

## <a name="setup-and-failover-flow"></a>Przepływ instalacji i trybu failover

Poniższa sekcja zawiera omówienie procesu pracy awaryjnej i wyjaśniono, jak skonfigurować początkowe trybu failover. 

![1][]

### <a name="setup"></a>Konfigurowanie

Możesz najpierw utworzyć lub użyj istniejącej głównej przestrzeni nazw i nowej pomocniczej przestrzeni nazw, a następnie łączyć dwóch. Połączenie umożliwia alias, który służy do łączenia. Ponieważ używasz aliasu, trzeba zmienić parametry połączenia. Można dodać tylko nowe przestrzenie nazw na parowanie zegarków trybu failover. Na koniec należy dodać pewne informacje monitorowania do wykrywania, jeśli konieczne jest przejścia w tryb failover. W większości przypadków usługa jest jedną z części dużego ekosystemu, tak więc automatycznego przejścia w tryb failover są rzadko jest to możliwe, jak bardzo często przejścia w tryb failover należy wykonać synchronizację z pozostałych podsystemu lub infrastruktury.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza należy wziąć pod uwagę rozwiązanie punktu sprzedaży (POS), który emituje komunikaty lub zdarzenia. Usługa Event Hubs przekazuje te zdarzenia, niektóre rozwiązania mapowanie lub formatowania, które następnie przekazuje zmapowanych danych do innego systemu w celu dalszego przetwarzania. W tym momencie wszystkich tych systemach mogą być hostowane w tym samym regionie platformy Azure. Decyzja w sprawie kiedy i jaka część do trybu failover jest zależna od przepływu danych w infrastrukturze. 

Można zautomatyzować trybu failover z systemów monitorowania lub za pomocą niestandardowej rozwiązania do monitorowania. Jednak takie automatyzacji zajmują dodatkowego planowania i współpracować, która wykracza poza zakres tego artykułu.

### <a name="failover-flow"></a>Przepływ pracy awaryjnej

Możesz zainicjować trybu failover, wymagane są dwa kroki:

1. Jeśli inna awaria wystąpi, chcesz mieć możliwość przełączenia ponownie. W związku z tym Ustaw innej pasywnym przestrzeni nazw i zaktualizuj parowanie. 

2. Ściągają komunikaty z poprzedniej wersji portalu podstawowej przestrzeni nazw, gdy będzie znowu dostępna. Po tym regularne komunikatów poza konfigurację odzyskiwania replikacji geograficznej na użytek tej przestrzeni nazw lub Usuń stare podstawowej przestrzeni nazw.

> [!NOTE]
> Obsługiwane są wyłącznie zakończyć się niepowodzeniem do przodu semantyki. W tym scenariuszu w trybie Failover, a następnie ponownie łączyć się z nową przestrzeń nazw. Powrotem nie jest obsługiwana; na przykład w klastrze programu SQL. 

![2][]

## <a name="management"></a>Zarządzanie

Jeśli w przypadku popełnienia; na przykład sparowane regiony problem podczas początkowej konfiguracji, może przerwać parowanie dwie przestrzeni nazw w dowolnym momencie. Jeśli chcesz użyć sparowane przestrzenie nazw jako regularne przestrzenie nazw, Usuń ten alias.

## <a name="samples"></a>Przykłady

[Próbki w serwisie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) pokazuje, jak skonfigurować i zainicjuj tryb failover. W tym przykładzie przedstawiono następujące pojęcia:

- Ustawienia wymagane w usłudze Azure Active Directory do usługi Azure Resource Manager za pomocą usługi Event Hubs. 
- Kroki wymagane do wykonania w przykładowym kodzie. 
- Wysyłanie i odbieranie od podstawowej przestrzeni nazw. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę następujące kwestie, które należy uwzględnić w tej wersji:

1. W procesie planowania trybu failover, należy również rozważyć współczynnik czasu. Na przykład jeśli w przypadku utraty łączności przez czas dłuższy niż 15-20 minut, można zdecydować do zainicjowania trybu failover. 
 
2. Fakt, że żadne dane nie są replikowane oznacza, że obecnie aktywnych sesji nie są replikowane. Ponadto wykrywania duplikatów i zaplanowane komunikaty mogą nie działać. Nowej sesji, zaplanowane wiadomości i nowe duplikaty będą działać. 

3. Przechodzenie w tryb failover złożonych rozproszonej infrastruktury powinny być [rehearsed](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz. 

4. Synchronizowanie jednostek może zająć trochę czasu około 50 – 100 jednostek na minutę.

## <a name="availability-zones"></a>Strefy dostępności 

Standardowa jednostka SKU Event Hubs obsługuje [strefy dostępności](../availability-zones/az-overview.md), zapewniając lokalizację izolowaną od błędów w regionie świadczenia usługi Azure. 

> [!NOTE]
> Strefy dostępności pomoc techniczna dla usługi Azure Event Hubs Standard jest dostępna tylko w [regionach świadczenia usługi Azure](../availability-zones/az-overview.md#services-support-by-region) , w których znajdują się strefy dostępności.

Strefy dostępności można włączyć na nowe przestrzenie nazw, przy użyciu witryny Azure portal. Usługa Event Hubs nie obsługuje migracji istniejącej przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po jej włączeniu, w ramach przestrzeni nazw.

![3][]

## <a name="next-steps"></a>Kolejne kroki

* [Próbki w serwisie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) przeprowadzi prosty przepływ pracy, który tworzy parowania geograficznego i przełączenia w tryb failover do scenariusza odzyskiwania po awarii.
* [Dokumentacja interfejsu API REST](/rest/api/eventhub/disasterrecoveryconfigs) opisuje interfejsy API umożliwiające wykonywanie konfiguracji odzyskiwania po awarii na geograficznie.

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
