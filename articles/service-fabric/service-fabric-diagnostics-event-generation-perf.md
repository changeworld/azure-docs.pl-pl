---
title: Monitorowanie wydajności sieci szkieletowej usług Azure
description: Dowiedz się więcej o licznikach wydajności do monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 30b9b8393007033a7c2e6798cd57d9cf0128820d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464707"
---
# <a name="performance-metrics"></a>Metryki wydajności

Metryki powinny być zbierane, aby zrozumieć wydajność klastra, a także aplikacji w nim uruchomionych. W przypadku klastrów sieci szkieletowej usług zaleca się zbieranie następujących liczników wydajności.

## <a name="nodes"></a>Węzły

W przypadku komputerów w klastrze należy rozważyć zbieranie następujących liczników wydajności, aby lepiej zrozumieć obciążenie każdego komputera i podjąć odpowiednie decyzje dotyczące skalowania klastra.

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| Dysk logiczny | Wolne miejsce na dysku logicznym |
| Dysk fizyczny(na dysk) | Średnia długość kolejki odczytu dysku |
| Dysk fizyczny(na dysk) | Średnia długość kolejki zapisu dysku |
| Dysk fizyczny(na dysk) | Średnia dysk s/odczyt |
| Dysk fizyczny(na dysk) | Średnia sek./zapis |
| Dysk fizyczny(na dysk) | Odczyty dysku/s |
| Dysk fizyczny(na dysk) | Bajty odczytu dysku/s |
| Dysk fizyczny(na dysk) | Zapisy na dysku/s |
| Dysk fizyczny(na dysk) | Bajty zapisu dysku na sekundę |
| Memory (Pamięć) | Dostępne MBytes |
| Plik stronicowania | % użycia |
| Procesor (razem) | Czas procesora (%) |
| Proces (na usługę) | Czas procesora (%) |
| Proces (na usługę) | Proces identyfikatora |
| Proces (na usługę) | Bajty prywatne |
| Proces (na usługę) | Liczba wątków |
| Proces (na usługę) | Bajty wirtualne |
| Proces (na usługę) | Zestaw roboczy |
| Proces (na usługę) | Zestaw roboczy - Prywatny |
| Interfejs sieciowy(wszystkie wystąpienia) | Bajty cofnięte |
| Interfejs sieciowy(wszystkie wystąpienia) | Wysłane bajty |
| Interfejs sieciowy(wszystkie wystąpienia) | Suma bajtów |
| Interfejs sieciowy(wszystkie wystąpienia) | Długość kolejki wyjściowej |
| Interfejs sieciowy(wszystkie wystąpienia) | Pakiety odrzucone wychodzące |
| Interfejs sieciowy(wszystkie wystąpienia) | Odebrane pakiety odrzucone |
| Interfejs sieciowy(wszystkie wystąpienia) | Błędy wychodzące pakietów |
| Interfejs sieciowy(wszystkie wystąpienia) | Odebrane błędy pakietów |

## <a name="net-applications-and-services"></a>Aplikacje i usługi platformy .NET

Zbieranie następujących liczników w przypadku wdrażania usług .NET w klastrze. 

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| Pamięć CLR .NET (na usługę) | Identyfikator procesu |
| Pamięć CLR .NET (na usługę) | # Całkowita liczba zatwierdzonych bajtów |
| Pamięć CLR .NET (na usługę) | # Całkowita zarezerwowana bajty |
| Pamięć CLR .NET (na usługę) | # Bajty we wszystkich heaps |
| Pamięć CLR .NET (na usługę) | Rozmiar sterty dużego obiektu |
| Pamięć CLR .NET (na usługę) | # Uchwyty GC |
| Pamięć CLR .NET (na usługę) | # Gen 0 Kolekcje |
| Pamięć CLR .NET (na usługę) | # Gen 1 Kolekcje |
| Pamięć CLR .NET (na usługę) | # Gen 2 Kolekcje |
| Pamięć CLR .NET (na usługę) | % czasu w GC |

### <a name="service-fabrics-custom-performance-counters"></a>Niestandardowe liczniki wydajności sieci szkieletowej usług

Usługa Fabric generuje znaczną ilość niestandardowych liczników wydajności. Jeśli masz zainstalowany pakiet SDK, możesz wyświetlić pełną listę na komputerze z systemem Windows w aplikacji Monitor wydajności (Monitor wydajności Uruchamianie >). 

W aplikacjach wdrażanych w klastrze, jeśli używasz reliable actors, dodaj liczniki z `Service Fabric Actor` i `Service Fabric Actor Method` kategorie (zobacz [Diagnostyka wiarygodnych aktorów sieci](service-fabric-reliable-actors-diagnostics.md)szkieletowej usług).

Jeśli korzystasz z niezawodnych usług lub usługi `Service Fabric Service` Remoting, mamy podobnie i `Service Fabric Service Method` licznik kategorii, które należy zbierać liczniki z, patrz [monitorowanie z usługi zdalnej](service-fabric-reliable-serviceremoting-diagnostics.md) i [niezawodne liczniki wydajności usług](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Jeśli używasz niezawodne kolekcje, `Avg. Transaction ms/Commit` zaleca `Service Fabric Transactional Replicator` się dodanie z do pobrania średniej opóźnienia zatwierdzania na metrykę transakcji.


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [generowaniu zdarzeń na poziomie platformy](service-fabric-diagnostics-event-generation-infra.md) w sieci szkieletowej usług
* Zbieranie danych o wydajności za pośrednictwem [agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md)
