---
title: Azure awarii usługi Service Bus geograficznie | Dokumentacja firmy Microsoft
description: Jak używać regionów geograficznych do trybu failover i odzyskiwania po awarii w Azure Service Bus
services: service-bus-messaging
documentationcenter: ''
author: christianwolf42
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: sethm
ms.openlocfilehash: 652adcf78add8ae699a7f827a915e90ce1694c61
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
ms.locfileid: "30237349"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure awarii usługi Service Bus Geo

Gdy cały regiony platformy Azure lub centrów danych (Jeśli nie [stref dostępności](../availability-zones/az-overview.md) są używane) Przestój, jest bardzo istotne dla przetwarzania danych w dalszym ciągu działać w innym regionie lub w centrum danych. W efekcie *odzyskiwania po awarii z magazynu geograficznie* i *— replikacja geograficzna* są ważne funkcje dla każdego przedsiębiorstwa. Usługa Azure Service Bus obsługuje odzyskiwania po awarii geograficznie i replikacja geograficzna, na poziomie przestrzeni nazw. 

Funkcja odzyskiwania po awarii geograficzna jest ogólnie dostępna dla warstwy Premium magistrali usługi. 

## <a name="outages-and-disasters"></a>Awarie i awarii

Ważne jest, aby należy zwrócić uwagę różnice między "awarie" i "awarii." *Awarii* jest tymczasowej niedostępności usługi Azure Service Bus i może mieć wpływ na niektóre składniki usługi, takie jak magazynie obsługi komunikatów, lub nawet całe centrum danych. Jednak po usunięciu problemu usługi Service Bus znowu dostępne. Zazwyczaj awarii nie powoduje utraty wiadomości lub innych danych. Przykładem takich awarii może być awarii zasilania w centrum danych. Niektóre awarie są tylko połączenia krótkich straty problemy przejściowe lub sieci. 

A *po awarii* jest zdefiniowany jako stały lub długoterminowego utraty klastra usługi Service Bus, region platformy Azure lub centrum danych. Region lub centrum danych może nie może stać się dostępne ponownie lub może nie działać przez kilka godzin lub dni. Przykładami takich awarii są fire, zalewania lub trzęsienie ziemi. Po awarii, które staje się stałe może spowodować utratę niektórych komunikatów, zdarzenia lub innych danych. Jednak w większości przypadków nie powinny istnieć bez utraty danych i komunikaty mogą zostać odzyskane po centrum danych kopii zapasowych.

Funkcja odzyskiwania po awarii geograficznie Azure Service Bus jest rozwiązanie odzyskiwania po awarii. Pojęcia i przepływ pracy opisany w tym artykule należy zastosować scenariuszy awarii, a nie przejściowego lub tymczasowych przestojów. Szczegółowe omówienie odzyskiwania po awarii na platformie Microsoft Azure, zobacz [w tym artykule](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Podstawowych pojęć i terminów

Funkcja odzyskiwania po awarii implementuje odzyskiwania po awarii metadanych i zależy od usługi przestrzenie nazw odzyskiwania po awarii podstawowego i pomocniczego. Należy pamiętać, że funkcja odzyskiwania po awarii geograficznie dostępne dla [warstwy Premium](service-bus-premium-messaging.md) tylko. Nie trzeba wprowadzić zmiany w ciągu połączenia, ponieważ połączenie jest realizowane za pomocą aliasu.

Poniższe terminy są używane w tym artykule:

-  *Alias*: Nazwa konfiguracji odzyskiwania po awarii, który można skonfigurować. Alias zawiera jeden ciąg połączenia stabilna pełni kwalifikowanej domeny nazwę (FQDN). Aplikacje Użyj tych parametrów połączenia alias nawiązać połączenia z przestrzenią nazw. 

-  *Przestrzeń nazw podstawowe i pomocnicze*: przestrzenie nazw, które odpowiadają alias. Głównej przestrzeni nazw jest "aktywny" i odbiera komunikaty (może to być istniejącej lub nowej przestrzeni nazw). Dodatkowej przestrzeni nazw jest "pasywny" i nie odbiera wiadomości. Metadanych między nimi jest w synchronizacji, więc zarówno bezproblemowo zaakceptować wiadomości bez żadnych zmian ciąg aplikacji, jak kod lub połączenia. Aby upewnić się, że tylko aktywnej przestrzeni nazw odbiera komunikaty, musisz użyć tego aliasu. 

-  *Metadane*: jednostek, takich jak kolejek, tematów i subskrypcji; oraz ich właściwości usługi, które są skojarzone z przestrzenią nazw. Należy zwrócić uwagę na to, czy tylko jednostek i ich ustawienia są automatycznie replikowane. Komunikaty nie są replikowane. 

-  *Tryb failover*: proces aktywacji dodatkowej przestrzeni nazw.

## <a name="setup-and-failover-flow"></a>Przepływ instalacji i trybu failover

Poniższa sekcja zawiera omówienie procesu pracy awaryjnej i wyjaśniono, jak skonfigurować początkowej trybu failover. 

![1][]

### <a name="setup"></a>Konfiguracja

Należy najpierw utworzyć lub użyj istniejącej głównej przestrzeni nazw i nowej dodatkowej przestrzeni nazw, a następnie skojarzyć dwa. Połączenie umożliwia alias, który służy do połączenia. Ponieważ używasz aliasu, nie trzeba zmienić parametry połączenia. Tylko nowych przestrzeni nazw można dodać do Twojej pracy awaryjnej parowania. Na koniec należy dodać pewne aspekty monitorowania do wykrywania, czy tryb failover jest konieczne. W większości przypadków usługa jest częścią dużych ekosystem, w związku z tym automatycznego przechodzenia w tryb failover są rzadko możliwe, jak bardzo często trybu failover musi zostać wykonana w pozostałych podsystemu lub infrastruktury.

### <a name="example"></a>Przykład

W jednym z przykładów tego scenariusza należy wziąć pod uwagę rozwiązanie punktu sprzedaży (POS), które emituje wiadomości lub zdarzeń. Usługa Service Bus przekazuje te zdarzenia, niektóre mapowania lub ponowne formatowanie rozwiązania, które następnie przekazuje zmapowanych danych do innego systemu dla dalszego przetwarzania. W tym momencie wszystkich tych systemach mogą znajdować się w tym samym regionie Azure. Decyzja na kiedy i jaka część do pracy awaryjnej jest zależna od przepływu danych w ramach infrastruktury. 

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

## <a name="use-existing-namespace-as-alias"></a>Użyj istniejącej przestrzeni nazw jako alias

Jeśli masz scenariusz, w którym nie można zmienić połączeń producenci i konsumenci przestrzeni nazw można ponownie użyć jako aliasu. Zobacz [przykładowy kod w serwisie GitHub tutaj](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Przykłady

[Przykłady z witryny GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) pokazują, jak skonfigurować i zainicjuj tryb failover. Te przykłady pokazują następujące kwestie:

- Przykładowe .net i ustawienia wymagane w usłudze Azure Active Directory do użycia usługi Azure Resource Manager z usługą Service Bus można skonfigurować i włączyć odzyskiwanie po awarii z magazynu geograficznie.
- Kroki wymagane do wykonania w przykładowym kodzie.
- Jak używać istniejącej przestrzeni nazw jako alias.
- Kroki, aby można również włączyć odzyskiwania po awarii geograficznie za pomocą programu PowerShell lub interfejsu wiersza polecenia.
- [Wysyłanie i odbieranie](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z bieżącej podstawowej lub dodatkowej przestrzeni nazw za pomocą aliasu.

## <a name="considerations"></a>Zagadnienia do rozważenia

Należy uwzględnić następujące kwestie należy wziąć pod uwagę w tej wersji:

1. W procesie planowania trybu failover, należy również rozważyć współczynnik czasu. Na przykład w przypadku utraty połączenia przez czas dłuższy niż 15-20 minut, możesz zainicjować trybu failover. 
 
2. Fakt, że żadne dane nie są replikowane oznacza, że obecnie aktywnych sesji nie są replikowane. Ponadto wykrywania duplikatów i zaplanowane komunikaty mogą nie działać. Nowej sesji, nowe komunikaty zaplanowane i nowych duplikaty będzie działać. 

3. Awarii złożonych infrastruktury rozproszonej powinna być [rehearsed](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) co najmniej raz. 

4. Synchronizowanie jednostek może zająć trochę czasu, około 50-100 jednostek na minutę. Subskrypcje i reguły również liczba jednostki. 

## <a name="next-steps"></a>Kolejne kroki

- Zobacz odzyskiwania po awarii geograficznie [tutaj dokumentacji interfejsu API REST](/rest/api/servicebus/disasterrecoveryconfigs).
- Uruchom odzyskiwanie po awarii geograficznie [w witrynie GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Zobacz odzyskiwania po awarii geograficznie [przykład, który wysyła wiadomości do aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Aby dowiedzieć się więcej na temat komunikatów usługi Service Bus, zobacz następujące artykuły:

* [Podstawy usługi Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Interfejs API REST](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
