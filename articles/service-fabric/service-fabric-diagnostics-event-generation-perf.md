---
title: Wydajność sieci szkieletowej Azure usługi monitorowania | Dokumentacja firmy Microsoft
description: Więcej informacji na temat liczników wydajności dla monitorowania i diagnostyki klastrów sieci szkieletowej usług Azure.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 54837b2a43f93f43d089a2fdf35d8b74ecacfc3e
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="performance-metrics"></a>Metryki wydajności

Aby zrozumieć działanie klastra, a także aplikacji uruchomionych w niej powinny być gromadzone metryki. Dla klastrów sieci szkieletowej usług firma Microsoft zaleca zbieranie następujących liczników wydajności.

## <a name="nodes"></a>Węzły

Dla maszyn w klastrze należy wziąć pod uwagę następujące liczniki wydajności do lepszego zrozumienia obciążenia na każdej maszynie i wprowadzić odpowiednie klastra skalowanie decyzje dotyczące zbierania.

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| Dysk fizyczny (na dysku) | Średni Długość kolejki odczytu dysku |
| Dysk fizyczny (na dysku) | Średni Długość kolejki dysku zapisu |
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
| Proces (usługi) | Licznik Bajty wirtualne |
| Proces (usługi) | Zestaw roboczy |
| Proces (usługi) | Zestaw roboczy — prywatny |
| Interface(all-instances) sieci | Długość kolejki wyjściowej |
| Interface(all-instances) sieci | Usunięte pakiety wychodzące |
| Interface(all-instances) sieci | Odebrane pakiety |
| Interface(all-instances) sieci | Wychodzące pakiety z błędami |
| Interface(all-instances) sieci | Odebrane pakiety z błędami |

## <a name="net-applications-and-services"></a>Usługi i aplikacje środowiska .NET.

Zbierz następujące liczniki, Jeżeli wdrażasz usług .NET do klastra. 

| Kategoria licznika | Nazwa licznika |
| --- | --- |
| .NET CLR pamięci (dla usługi) | Identyfikator procesu |
| .NET CLR pamięci (dla usługi) | # Całkowita liczba Zadeklarowane bajty |
| .NET CLR pamięci (dla usługi) | # Całkowita liczba zastrzeżone bajtów |
| .NET CLR pamięci (dla usługi) | Liczba bajtów we wszystkich Stertach |
| .NET CLR pamięci (dla usługi) | # Kolekcje pokolenia 0 |
| .NET CLR pamięci (dla usługi) | # Kolekcje gen 1 |
| .NET CLR pamięci (dla usługi) | # Kolekcje gen 2 |
| .NET CLR pamięci (dla usługi) | % Czasu odzyskiwania pamięci |

### <a name="service-fabrics-custom-performance-counters"></a>Usługa sieć szkieletowa niestandardowe liczniki wydajności

Sieć szkieletowa usług generuje rozległe niestandardowe liczniki wydajności. Jeśli masz zainstalowany zestaw SDK, zostanie wyświetlona lista kompleksowe na komputerze z systemem Windows, w Monitorze wydajności aplikacji (Start > monitora wydajności). 

W aplikacjach wdrażasz do klastra, jeśli używasz Reliable Actors, Dodaj countes z `Service Fabric Actor` i `Service Fabric Actor Method` kategorii (zobacz [usługi sieć szkieletowa niezawodnej podmiotów diagnostyki](service-fabric-reliable-actors-diagnostics.md)).

Jeśli używasz usługi niezawodnego podobnie mamy `Service Fabric Service` i `Service Fabric Service Method` kategorii licznika, które należy zebrać liczników z. 

Użycie niezawodnej kolekcje, zaleca się dodawania `Avg. Transaction ms/Commit` z `Service Fabric Transactional Replicator` zbierać opóźnienie zatwierdzania średni na Metryka transakcji.


## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [generowania zdarzeń na poziomie platformy](service-fabric-diagnostics-event-generation-infra.md) w sieci szkieletowej usług
* Zbieranie metryk wydajności za pośrednictwem [Agent pakietu OMS](service-fabric-diagnostics-oms-agent.md)
