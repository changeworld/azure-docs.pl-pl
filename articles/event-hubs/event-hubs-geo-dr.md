---
title: Odzyskiwania po awarii na geograficznie w usłudze Azure Event Hubs | Dokumentacja firmy Microsoft
description: Jak używać regionów geograficznych do trybu failover i odzyskiwania po awarii w Azure Event Hubs
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 237b0639be75e12cff56f40ac76426aba7a8a701
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2017
ms.locfileid: "26745898"
---
# <a name="azure-event-hubs-geo-disaster-recovery"></a>Azure awarii usługi Event Hubs Geo

Gdy cały regiony platformy Azure lub centrów danych (Jeśli nie [stref dostępności](../availability-zones/az-overview.md) są używane) Przestój, jest bardzo istotne dla przetwarzania danych w dalszym ciągu działać w innym regionie lub w centrum danych. W efekcie *odzyskiwania po awarii z magazynu geograficznie* i *— replikacja geograficzna* są ważne funkcje dla każdego przedsiębiorstwa. Usługa Azure Event Hubs obsługuje odzyskiwanie po awarii geograficznie i replikacja geograficzna, na poziomie przestrzeni nazw. 

Funkcja odzyskiwania po awarii geograficzna jest ogólnie dostępna dla wersji Standard centra zdarzeń.

## <a name="outages-and-disasters"></a>Awarie i awarii

Ważne jest, aby należy zwrócić uwagę różnice między "awarie" i "awarii." *Awarii* jest tymczasowej niedostępności usługi Azure Event hubs i może mieć wpływ na niektóre składniki usługi, takie jak magazynie obsługi komunikatów, lub nawet całe centrum danych. Jednak po usunięciu problemu centra zdarzeń znowu dostępne. Zazwyczaj awarii nie powoduje utraty wiadomości lub innych danych. Przykładem takich awarii może być awarii zasilania w centrum danych. Niektóre awarie są tylko połączenia krótkich straty problemy przejściowe lub sieci. 

A *po awarii* jest zdefiniowany jako stały lub długoterminowego utraty klastra usługi Event Hubs, region platformy Azure lub centrum danych. Region lub centrum danych może nie może stać się dostępne ponownie lub może nie działać przez kilka godzin lub dni. Przykładami takich awarii są fire, zalewania lub trzęsienie ziemi. Po awarii, które staje się stałe może spowodować utratę niektórych komunikatów, zdarzenia lub innych danych. Jednak w większości przypadków nie powinny istnieć bez utraty danych i komunikaty mogą zostać odzyskane po centrum danych kopii zapasowych.

Funkcja odzyskiwania po awarii Geo usługi Azure Event hubs jest rozwiązanie odzyskiwania po awarii. Pojęcia i przepływ pracy opisany w tym artykule należy zastosować scenariuszy awarii, a nie przejściowego lub tymczasowych przestojów. Szczegółowe omówienie odzyskiwania po awarii na platformie Microsoft Azure, zobacz [w tym artykule](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Podstawowych pojęć i terminów

Funkcja odzyskiwania po awarii implementuje odzyskiwania po awarii metadanych i zależy od usługi przestrzenie nazw odzyskiwania po awarii podstawowego i pomocniczego. Należy pamiętać, że funkcja odzyskiwania po awarii geograficznie dostępne dla [standardowy SKU](https://azure.microsoft.com/pricing/details/event-hubs/) tylko. Nie trzeba wprowadzić zmiany w ciągu połączenia, ponieważ połączenie jest realizowane za pomocą aliasu.

Poniższe terminy są używane w tym artykule:

-  *Alias*: Nazwa konfiguracji odzyskiwania po awarii, który można skonfigurować. Alias zawiera jeden ciąg połączenia stabilna pełni kwalifikowanej domeny nazwę (FQDN). Aplikacje Użyj tych parametrów połączenia alias nawiązać połączenia z przestrzenią nazw. 

-  *Przestrzeń nazw podstawowe i pomocnicze*: przestrzenie nazw, które odpowiadają alias. Głównej przestrzeni nazw jest "aktywny" i odbiera komunikaty (może to być istniejącej lub nowej przestrzeni nazw). Dodatkowej przestrzeni nazw jest "pasywny" i nie odbiera wiadomości. Metadanych między nimi jest w synchronizacji, więc zarówno bezproblemowo zaakceptować wiadomości bez żadnych zmian ciąg aplikacji, jak kod lub połączenia. Aby upewnić się, że tylko aktywnej przestrzeni nazw odbiera komunikaty, musisz użyć tego aliasu. 

-  *Metadane*: jednostek, takich jak centra zdarzeń i grupy konsumentów; i ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy zwrócić uwagę na to, czy tylko jednostek i ich ustawienia są automatycznie replikowane. Wiadomości i zdarzeń nie są replikowane. 

-  *Tryb failover*: proces aktywacji dodatkowej przestrzeni nazw.

## <a name="setup-and-failover-flow"></a>Przepływ instalacji i trybu failover

Poniższa sekcja zawiera omówienie procesu pracy awaryjnej i wyjaśniono, jak skonfigurować początkowej trybu failover. 

![1][]

### <a name="setup"></a>Konfiguracja

Należy najpierw utworzyć lub użyj istniejącej głównej przestrzeni nazw i nowej dodatkowej przestrzeni nazw, a następnie skojarzyć dwa. Połączenie umożliwia alias, który służy do połączenia. Ponieważ używasz aliasu, nie trzeba zmienić parametry połączenia. Tylko nowych przestrzeni nazw można dodać do Twojej pracy awaryjnej parowania. Na koniec należy dodać pewne aspekty monitorowania do wykrywania, czy tryb failover jest konieczne. W większości przypadków usługa jest częścią dużych ekosystem, w związku z tym automatycznego przechodzenia w tryb failover są rzadko możliwe, jak bardzo często trybu failover musi zostać wykonana w pozostałych podsystemu lub infrastruktury.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza należy wziąć pod uwagę rozwiązanie punktu sprzedaży (POS), które emituje wiadomości lub zdarzeń. Centra zdarzeń przekazuje te zdarzenia do niektórych mapowanie ani formatowania rozwiązania, które następnie przekazuje zmapowanych danych do innego systemu dla dalszego przetwarzania. W tym momencie wszystkich tych systemach mogą znajdować się w tym samym regionie Azure. Decyzja na kiedy i jaka część do pracy awaryjnej jest zależna od przepływu danych w ramach infrastruktury. 

Można zautomatyzować tryb failover z systemów monitorowania lub z niestandardowej rozwiązań w zakresie monitorowania. Jednak takie automatyzacji ma dodatkowe planowanie i pracy, która wykracza poza zakres tego artykułu.

### <a name="failover-flow"></a>Przepływ pracy awaryjnej

Zainicjuj tryb failover, wymagane są dwa kroki:

1. Inny awarii, ma możliwość pracy awaryjnej ponownie. W związku z tym ustawienie innej przestrzeni nazw pasywnego i zaktualizuj parowanie. 

2. Ściągać komunikaty z poprzedniego głównej przestrzeni nazw, gdy jest ona dostępna ponownie. Po wykonaniu tej tej przestrzeni nazw używany do regularnych wiadomości poza ustawień odzyskiwania z magazynu geograficznie lub Usuń stare głównej przestrzeni nazw.

> [!NOTE]
> Obsługiwane są tylko semantyki przekazywania błędów. W tym scenariuszu w tryb failover i ponownie skojarzyć z nowej przestrzeni nazw. Niepowodzenie Wstecz nie jest obsługiwany; na przykład w klastrze programu SQL. 

![2][]

## <a name="management"></a>Zarządzanie

Jeśli wprowadzono błędu; na przykład łączyć niewłaściwy regionów podczas początkowej konfiguracji, mogą być dzielone parowanie dwie przestrzenie nazw w dowolnym momencie. Jeśli chcesz użyć pary nazw jako regularne przestrzeni nazw, Usuń ten alias.

## <a name="samples"></a>Przykłady

[w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) pokazano, jak skonfigurować i zainicjuj tryb failover. W tym przykładzie przedstawiono następujące kwestie:

- Ustawienia wymagane w usłudze Azure Active Directory do użycia usługi Azure Resource Manager z usługą Event Hubs. 
- Kroki wymagane do wykonania w przykładowym kodzie. 
- Wysyłanie i odbieranie od bieżącej podstawowej przestrzeni nazw. 

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy uwzględnić następujące kwestie należy wziąć pod uwagę w tej wersji:

1. W procesie planowania trybu failover, należy również rozważyć współczynnik czasu. Na przykład w przypadku utraty połączenia przez czas dłuższy niż 15-20 minut, możesz zainicjować trybu failover. 
 
2. Fakt, że żadne dane nie są replikowane oznacza, że obecnie aktywnych sesji nie są replikowane. Ponadto wykrywania duplikatów i zaplanowane komunikaty mogą nie działać. Nowej sesji, zaplanowane wiadomości i nowych duplikaty będzie działać. 

3. Awarii złożonych infrastruktury rozproszonej powinna być [rehearsed](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) co najmniej raz. 

4. Synchronizowanie jednostek może zająć trochę czasu, około 50-100 jednostek na minutę.

## <a name="next-steps"></a>Następne kroki

* [w witrynie GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) przeprowadzi Cię przez prosty przepływ pracy, który tworzy parowanie geograficznie i inicjuje trybu failover do scenariusza odzyskiwania po awarii.
* [Dokumentacji interfejsu API REST](/rest/api/eventhub/disasterrecoveryconfigs) opisuje interfejsy API do wykonywania konfiguracji odzyskiwania po awarii Geo.

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

* Rozpocznij pracę od [samouczka dotyczącego usługi Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png