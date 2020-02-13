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
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158030"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Usługa Azure Event Hubs — odzyskiwanie po awarii geograficznie 

W przypadku awarii całego regionu platformy Azure lub centrów danych (jeśli nie są używane [strefy dostępności](../availability-zones/az-overview.md) ) nie ma możliwości zapewnienia przestoju w celu przeprowadzenia działania w innym regionie lub w centrum. W związku z tym *odzyskiwanie geograficzne* i *replikacja geograficzna* są ważnymi funkcjami dla każdego przedsiębiorstwa. Usługa Azure Event Hubs obsługuje zarówno odzyskiwania po awarii geograficzne i replikacja geograficzna, na poziomie przestrzeni nazw. 

> [!NOTE]
> Funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [standardowej i dedykowanej jednostki SKU](https://azure.microsoft.com/pricing/details/event-hubs/).  

## <a name="outages-and-disasters"></a>Wyłączeń i awarii

Ważne jest, aby należy zauważyć różnicę między "wyłączeń" i "awarii." *Awaria* to tymczasowa niedostępność usługi Azure Event Hubs i może mieć wpływ na niektóre składniki usługi, takie jak magazyn komunikatów, a nawet całe centrum danych. Jednak po usunięciu problemu usługi Event Hubs staje się dostępny ponownie. Zazwyczaj awaria nie powoduje utraty wiadomości lub inne dane. Przykładem takich awarii może być awaria zasilania w centrum danych. Niektóre awarie są tylko połączenia krótki straty ze względu na problemy przejściowe lub sieci. 

*Awaria* jest definiowana jako trwała lub w długim czasie utrata Event Hubs klastra, regionu platformy Azure lub centrum danych. Region lub centrum danych może nie może stać się dostępna ponownie lub może nie działać przez kilka godzin lub dni. Przykładami takich awarii są pożaru, przepełnieniu lub trzęsienie ziemi. Po awarii, która staje się stałe może spowodować utratę niektórych komunikatów, zdarzenia lub inne dane. Jednak w większości przypadków należy bez utraty danych, a komunikaty mogą być odzyskiwane po centrum danych zacznie ponownie działać.

Funkcja odzyskiwania po awarii geograficznej usługi Azure Event Hubs jest rozwiązanie odzyskiwania po awarii. Pojęcia i przepływ pracy opisany w tym artykule mają zastosowanie do scenariuszy awarii, a nie przejściowe i tymczasowe awarii. Aby uzyskać szczegółowe omówienie odzyskiwania po awarii w Microsoft Azure, zobacz [ten artykuł](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Podstawowych pojęć i terminów

Funkcja odzyskiwania po awarii implementuje odzyskiwanie po awarii metadane i opiera się na przestrzeni nazw odzyskiwania po awarii podstawowego i pomocniczego. 

Funkcja odzyskiwania geograficznego po awarii jest dostępna tylko dla [standardowych i dedykowanych jednostek SKU](https://azure.microsoft.com/pricing/details/event-hubs/) . Nie trzeba wprowadzać żadnych zmian ciągu połączenia, ponieważ połączenie zostało nawiązane za pomocą aliasu.

W tym artykule są używane następujące terminy:

-  *Alias*: Nazwa skonfigurowanej konfiguracji odzyskiwania po awarii. Alias zapewnia pojedynczy ciąg stabilne połączenie w pełni kwalifikowanej domeny nazwę (FQDN). Aplikacje za pomocą te parametry połączenia aliasu połączyć z przestrzeni nazw. 

-  *Podstawowa/pomocnicza przestrzeń nazw*: przestrzenie nazw, które odpowiadają aliasu. Podstawowa przestrzeń nazw jest "aktywny" i odbiera komunikaty (może to być istniejącej lub nowej przestrzeni nazw). Pomocnicza przestrzeń nazw jest "pasywny" i nie otrzymywać wiadomości. Metadanych między obiema jest zsynchronizowany, więc zarówno bezproblemowo może akceptować komunikaty bez wprowadzania żadnych zmian parametry aplikacji, jak połączenie lub kod. Aby upewnić się, że tylko aktywnej przestrzeni nazw odbiera komunikaty, musisz użyć tego aliasu. 

-  *Metadane*: jednostki, takie jak centra zdarzeń i grupy konsumentów; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy pamiętać, że tylko jednostek i ich ustawienia są replikowane automatycznie. Komunikaty i zdarzenia nie są replikowane. 

-  *Tryb failover*: proces aktywowania pomocniczej przestrzeni nazw.

## <a name="supported-namespace-pairs"></a>Obsługiwane pary przestrzeni nazw
Obsługiwane są następujące kombinacje podstawowych i pomocniczych przestrzeni nazw:  

| Podstawowa przestrzeń nazw | Pomocnicza przestrzeń nazw | Obsługiwane | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Yes | 
| Standard | Dedykowane | Yes | 
| Dedykowane | Dedykowane | Yes | 
| Dedykowane | Standard | Nie | 

> [!NOTE]
> Nie można sparować przestrzeni nazw, które znajdują się w tym samym dedykowanym klastrze. Można sparować przestrzenie nazw, które znajdują się w osobnych klastrach. 

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

[Przykład w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) pokazuje, jak skonfigurować i zainicjować tryb failover. W tym przykładzie przedstawiono następujące pojęcia:

- Ustawienia wymagane w usłudze Azure Active Directory do usługi Azure Resource Manager za pomocą usługi Event Hubs. 
- Kroki wymagane do wykonania w przykładowym kodzie. 
- Wysyłanie i odbieranie od podstawowej przestrzeni nazw. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy zwrócić uwagę następujące kwestie, które należy uwzględnić w tej wersji:

1. Zgodnie z projektem, Event Hubs odzyskiwanie geograficznego systemu nie replikuje danych i w związku z tym nie można ponownie użyć starej wartości przesunięcia głównego centrum zdarzeń w pomocniczym centrum zdarzeń. Zalecamy ponowne uruchomienie odbiornika zdarzeń z jedną z następujących czynności:

- *EventPosition. FromStart ()* — Jeśli chcesz odczytywać wszystkie dane z pomocniczego centrum zdarzeń.
- *EventPosition. FromEnd ()* — Jeśli chcesz odczytywać wszystkie nowe dane z czasu połączenia z pomocniczym centrum zdarzeń.
- *EventPosition. FromEnqueuedTime (DateTime)* — Jeśli chcesz odczytywać wszystkie dane odebrane w pomocniczym centrum zdarzeń, rozpoczynając od danego dnia i godziny.

2. W procesie planowania trybu failover, należy również rozważyć współczynnik czasu. Na przykład jeśli w przypadku utraty łączności przez czas dłuższy niż 15-20 minut, można zdecydować do zainicjowania trybu failover. 
 
3. Fakt, że żadne dane nie są replikowane oznacza, że obecnie aktywnych sesji nie są replikowane. Ponadto wykrywania duplikatów i zaplanowane komunikaty mogą nie działać. Nowej sesji, zaplanowane wiadomości i nowe duplikaty będą działać. 

4. Przełączenie w tryb failover złożonej infrastruktury rozproszonej powinno być [rehearsed](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) co najmniej raz. 

5. Synchronizowanie jednostek może zająć trochę czasu około 50 – 100 jednostek na minutę.

## <a name="availability-zones"></a>Strefy dostępności 

Standardowa jednostka SKU Event Hubs obsługuje [strefy dostępności](../availability-zones/az-overview.md), zapewniając lokalizację izolowaną od błędów w regionie świadczenia usługi Azure. 

> [!NOTE]
> Strefy dostępności pomoc techniczna dla usługi Azure Event Hubs Standard jest dostępna tylko w [regionach świadczenia usługi Azure](../availability-zones/az-overview.md#services-support-by-region) , w których znajdują się strefy dostępności.

Strefy dostępności można włączyć na nowe przestrzenie nazw, przy użyciu witryny Azure portal. Usługa Event Hubs nie obsługuje migracji istniejącej przestrzeni nazw. Nie można wyłączyć nadmiarowości strefy po jej włączeniu, w ramach przestrzeni nazw.

![3][]

## <a name="next-steps"></a>Następne kroki

* [Przykład w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) zawiera prosty przepływ pracy, który tworzy parowanie geograficzne i inicjuje tryb failover w przypadku scenariusza odzyskiwania po awarii.
* [Dokumentacja interfejsu API REST](/rest/api/eventhub/disasterrecoveryconfigs) zawiera opis interfejsów API służących do wykonywania konfiguracji odzyskiwania po awarii geograficznej.

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Rozpoczynanie pracy z usługą Event Hubs
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [JavaScript](get-started-java-send-v2.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
