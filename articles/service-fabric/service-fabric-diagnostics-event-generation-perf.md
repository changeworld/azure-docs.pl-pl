---
title: Usługa Azure Service Fabric wydajności monitorowania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat liczników wydajności do monitorowania i diagnostyki klastrów usługi Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: ee1608c40801f568b38ace4670b0d5ea7f73003c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663081"
---
# <a name="performance-metrics"></a>Metryki wydajności

Metryki powinny być zbierane, aby poznać wydajność klastra, a także aplikacje uruchomione w nim. W przypadku klastrów usługi Service Fabric firma Microsoft zaleca zbieranie następujących liczników wydajności.

## <a name="nodes"></a>Węzły

W przypadku komputerów w klastrze należy wziąć pod uwagę zbieranie następujących liczników wydajności, aby lepiej zrozumieć obciążenia na każdym komputerze i wprowadzić odpowiednie skalowanie decyzje dotyczące klastra.

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| Dysk logiczny | Wolne miejsce na dysku logicznym |
| Dysk fizyczny (na dysku) | Średni Długość kolejki odczytu dysku |
| Dysk fizyczny (na dysku) | Średni Długość kolejki zapisu dysku |
| Dysk fizyczny (na dysku) | Średni Czas dysku w s/Odczyt |
| Dysk fizyczny (na dysku) | Średni Dysku w s/Zapis |
| Dysk fizyczny (na dysku) | Odczyty dysku/s |
| Dysk fizyczny (na dysku) | Bajty odczytu z dysku/s |
| Dysk fizyczny (na dysku) | Zapisy dysku/s |
| Dysk fizyczny (na dysku) | Bajty zapisu na dysku/s |
| Memory (Pamięć) | Dostępna pamięć (MB) |
| Plik stronicowania | % Użycia |
| Processor(Total) | Czas procesora (%) |
| Proces (usługi) | Czas procesora (%) |
| Proces (usługi) | Identyfikator procesu |
| Proces (usługi) | Bajty prywatne |
| Proces (usługi) | Liczba wątków |
| Proces (usługi) | Bajty wirtualne |
| Proces (usługi) | Zestaw roboczy |
| Proces (usługi) | Zestaw roboczy — prywatny |
| Interface(all-instances) sieci | Recd bajtów |
| Interface(all-instances) sieci | Bajty wysłane |
| Interface(all-instances) sieci | Łączna liczba bajtów |
| Interface(all-instances) sieci | Długość kolejki wyjściowej |
| Interface(all-instances) sieci | Usunięte pakiety wychodzące |
| Interface(all-instances) sieci | Odebrane pakiety |
| Interface(all-instances) sieci | Pakiety wychodzące z błędami |
| Interface(all-instances) sieci | Odebrane pakiety z błędami |

## <a name="net-applications-and-services"></a>Aplikacje platformy .NET i usługi

Zbierz następujące liczniki, Jeżeli wdrażasz usługi .NET do klastra. 

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| Pamięć .NET CLR (na usługę) | Identyfikator procesu |
| Pamięć .NET CLR (na usługę) | # Łączna liczba przydzielonych bajtów |
| Pamięć .NET CLR (na usługę) | # Łączna liczba Zarezerwowane bajty |
| Pamięć .NET CLR (na usługę) | Liczba bajtów we wszystkich Stertach |
| Pamięć .NET CLR (na usługę) | Duży rozmiar sterty obiektów |
| Pamięć .NET CLR (na usługę) | # Obsługuje GC |
| Pamięć .NET CLR (na usługę) | # Pokolenia 0 |
| Pamięć .NET CLR (na usługę) | # Pokolenia 1 |
| Pamięć .NET CLR (na usługę) | # Pokolenia 2 |
| Pamięć .NET CLR (na usługę) | % Czasu odzyskiwania pamięci |

### <a name="service-fabrics-custom-performance-counters"></a>Usługa Service Fabric niestandardowych liczników wydajności

Usługa Service Fabric generuje rozległe niestandardowych liczników wydajności. Jeśli masz zainstalowany zestaw SDK, na komputerze Windows widać kompleksowej listy w Monitorze wydajności aplikacji (Start > Monitor wydajności). 

W aplikacji jest wdrażany z klastrem, korzystając z elementów Reliable Actors, Dodaj liczniki z `Service Fabric Actor` i `Service Fabric Actor Method` kategorii (zobacz [diagnostyki usługi sieci szkieletowej Reliable Actors](service-fabric-reliable-actors-diagnostics.md)).

Jeśli używasz usług Reliable Services lub zdalna komunikacja usług podobnie mamy `Service Fabric Service` i `Service Fabric Service Method` licznik kategorii, czy należy zbierać liczniki z adresem [monitorowanie za pomocą komunikacji zdalnej usługi](service-fabric-reliable-serviceremoting-diagnostics.md) i [niezawodne liczniki wydajności usług](service-fabric-reliable-services-diagnostics.md#performance-counters). 

Jeśli korzystasz z elementami Reliable Collections, zalecamy dodanie `Avg. Transaction ms/Commit` z `Service Fabric Transactional Replicator` zbierać opóźnienie średni zatwierdzenia na transakcję metrykę.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [generowanie zdarzeń na poziomie platformy](service-fabric-diagnostics-event-generation-infra.md) w usłudze Service Fabric
* Zbieraj metryki wydajności za pomocą [agenta usługi Log Analytics](service-fabric-diagnostics-oms-agent.md)
