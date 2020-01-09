---
title: Monitorowanie wydajności Service Fabric platformy Azure
description: Dowiedz się więcej o licznikach wydajności służących do monitorowania i diagnostyki klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464707"
---
# <a name="performance-metrics"></a>Metryki wydajności

Metryki powinny być zbierane, aby zrozumieć wydajność klastra, a także uruchomione w nim aplikacje. W przypadku klastrów Service Fabric zalecamy zbieranie następujących liczników wydajności.

## <a name="nodes"></a>Węzły

W przypadku maszyn w klastrze rozważ zebranie następujących liczników wydajności, aby lepiej zrozumieć obciążenie poszczególnych maszyn i podjąć odpowiednie decyzje dotyczące skalowania klastra.

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| Dysk logiczny | Wolne miejsce na dysku logicznym |
| Dysk fizyczny (na dysk) | Średnia długość kolejki odczytu dysku |
| Dysk fizyczny (na dysk) | Średnia długość kolejki zapisu na dysku |
| Dysk fizyczny (na dysk) | Średni czas dysku w s/odczyt |
| Dysk fizyczny (na dysk) | Średni czas dysku w s/zapis |
| Dysk fizyczny (na dysk) | Odczyty dysku/s |
| Dysk fizyczny (na dysk) | Bajty odczytu z dysku/s |
| Dysk fizyczny (na dysk) | Zapisy dysku/s |
| Dysk fizyczny (na dysk) | Bajty zapisu na dysku/s |
| Pamięć | Dostępna pamięć (MB) |
| PagingFile | % Użycia |
| Procesor (łącznie) | Czas procesora (%) |
| Proces (według usługi) | Czas procesora (%) |
| Proces (według usługi) | Identyfikator procesu |
| Proces (według usługi) | Prywatne bajty |
| Proces (według usługi) | Liczba wątków |
| Proces (według usługi) | Bajty wirtualne |
| Proces (według usługi) | Zestaw roboczy |
| Proces (według usługi) | Zestaw roboczy — prywatny |
| Interfejs sieciowy (wszystkie wystąpienia) | Bajty RECD |
| Interfejs sieciowy (wszystkie wystąpienia) | Wysłane bajty |
| Interfejs sieciowy (wszystkie wystąpienia) | Całkowita liczba bajtów |
| Interfejs sieciowy (wszystkie wystąpienia) | Długość kolejki wyjściowej |
| Interfejs sieciowy (wszystkie wystąpienia) | Odrzucone pakiety wychodzące |
| Interfejs sieciowy (wszystkie wystąpienia) | Odebrane pakiety zostały odrzucone |
| Interfejs sieciowy (wszystkie wystąpienia) | Błędy wychodzące pakietów |
| Interfejs sieciowy (wszystkie wystąpienia) | Błędy odebrane pakietów |

## <a name="net-applications-and-services"></a>Aplikacje i usługi platformy .NET

Zbierz następujące liczniki, jeśli usługi platformy .NET są wdrażane w klastrze. 

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| Pamięć środowiska CLR platformy .NET (na usługę) | Identyfikator procesu |
| Pamięć środowiska CLR platformy .NET (na usługę) | Liczba zadeklarowanych bajtów |
| Pamięć środowiska CLR platformy .NET (na usługę) | Liczba zarezerwowanych bajtów |
| Pamięć środowiska CLR platformy .NET (na usługę) | Liczba bajtów we wszystkich stertach |
| Pamięć środowiska CLR platformy .NET (na usługę) | Rozmiar sterty dla dużego obiektu |
| Pamięć środowiska CLR platformy .NET (na usługę) | Liczba dojść do # GC |
| Pamięć środowiska CLR platformy .NET (na usługę) | Liczba kolekcji generacji 0 |
| Pamięć środowiska CLR platformy .NET (na usługę) | Liczba kolekcji generacji 1 |
| Pamięć środowiska CLR platformy .NET (na usługę) | Liczba kolekcji generacji 2 |
| Pamięć środowiska CLR platformy .NET (na usługę) | Czas (%) w GC |

### <a name="service-fabrics-custom-performance-counters"></a>Niestandardowe liczniki wydajności Service Fabric

Service Fabric generuje znaczną liczbę niestandardowych liczników wydajności. Jeśli masz zainstalowany zestaw SDK, możesz zobaczyć kompleksową listę na komputerze z systemem Windows w aplikacji monitora wydajności (Uruchom > Monitor wydajności). 

W przypadku aplikacji wdrażanych w klastrze, jeśli używasz Reliable Actors, Dodaj liczniki z kategorii `Service Fabric Actor` i `Service Fabric Actor Method` (zobacz [Service Fabric Reliable Actors Diagnostics](service-fabric-reliable-actors-diagnostics.md)).

W przypadku korzystania z usług Reliable Services lub komunikacji zdalnej, firma Microsoft ma podobne `Service Fabric Service` i `Service Fabric Service Method` kategorie liczników, z których mają być zbierane liczniki, zobacz [monitorowanie z usługami zdalnymi](service-fabric-reliable-serviceremoting-diagnostics.md) i [niezawodnymi usługami](service-fabric-reliable-services-diagnostics.md#performance-counters). 

W przypadku korzystania z niezawodnych kolekcji zalecamy dodanie `Avg. Transaction ms/Commit` z `Service Fabric Transactional Replicator` w celu zebrania średniego opóźnienia zatwierdzania na metrykę transakcji.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [generowaniu zdarzeń na poziomie platformy](service-fabric-diagnostics-event-generation-infra.md) w Service Fabric
* Zbieranie metryk wydajności za poorednictwem [agenta log Analytics](service-fabric-diagnostics-oms-agent.md)
