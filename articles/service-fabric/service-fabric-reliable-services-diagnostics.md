---
title: Diagnostyka Azure Service Fabric stanowych usług Reliable Services | Dokumentacja firmy Microsoft
description: Funkcja diagnostyki dla stanowych usług Reliable Services w usłudze Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 96fc58ca8456e9a2e539e432f0ed85f6edc1a54f
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58664119"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funkcja diagnostyki dla stanowych usług Reliable Services
Klasa Azure usługi Service Fabric stanowa niezawodnych usług StatefulServiceBase emituje [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) zdarzenia, które mogą służyć do debugowania usługi dają wgląd w sposób operacyjnego środowiska uruchomieniowego i pomóc w rozwiązywaniu problemów.

## <a name="eventsource-events"></a>Zdarzeń EventSource
Nazwa źródła zdarzeń dla klasy niezawodne StatefulServiceBase usług stanowych to "Microsoft-ServiceFabric — usługi". Zdarzenia z tego źródła zdarzeń są wyświetlane w [zdarzenia diagnostyczne](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okna, gdy usługa jest [debugowania w programie Visual Studio](service-fabric-debugging-your-application.md).

Narzędzia i technologie, które pomagają w zbieraniu i/lub wyświetlanie zdarzeń EventSource przykłady [narzędzia PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [diagnostyki Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)i [Microsoft TraceEvent Library](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Zdarzenia
| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Opis zdarzenia |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informacyjne |Emitowane po uruchomieniu zadania RunAsync usługi |
| StatefulRunAsyncCancellation |2 |Informacyjne |Podczas anulowania zadania RunAsync usługi |
| StatefulRunAsyncCompletion |3 |Informacyjne |Emitowane po zakończeniu zadania RunAsync usługi |
| StatefulRunAsyncSlowCancellation |4 |Ostrzeżenie |Podczas zadania RunAsync usługi trwa zbyt długo, aby ukończyć anulowanie |
| StatefulRunAsyncFailure |5 |Błąd |Podczas zgłasza wyjątek, zadanie RunAsync usługi |

## <a name="interpret-events"></a>Interpretowanie zdarzenia
Zdarzenia StatefulRunAsyncInvocation, StatefulRunAsyncCompletion i StatefulRunAsyncCancellation przydają się do usługi składnika zapisywania usługi, aby informacje o cyklu życia usługi, a także czas, kiedy usługa uruchamia, anuluje lub zakończy się. Te informacje mogą być przydatne, gdy debugowanie problemów z usługą lub informacje o cyklu życia usług.

Moduły zapisujące usługi należy zwracać szczególną uwagę na StatefulRunAsyncSlowCancellation i StatefulRunAsyncFailure zdarzenia, ponieważ są one wskazują na problemy z usługą.

StatefulRunAsyncFailure jest emitowane zawsze wtedy, gdy zadanie RunAsync() usługi zgłasza wyjątek. Zazwyczaj wyjątku wskazuje błąd lub usterkę w usłudze. Ponadto wyjątek powoduje, że usługa ulegnie awarii, dzięki czemu jest przenoszony do innego węzła. Ta operacja może być kosztowne i może opóźnić żądań przychodzących, gdy usługi są przenoszone. Moduły zapisujące usługi należy ustalić przyczynę wyjątku i go, jeśli jest to możliwe, unikać.

StatefulRunAsyncSlowCancellation jest emitowane zawsze wtedy, gdy żądanie anulowania zadania RunAsync trwa dłużej niż czterech sekund. Usługa trwa zbyt długo, aby ukończyć anulowanie, wpływa na zdolność usługi można szybko uruchomić ponownie w innym węźle. Ten scenariusz może mieć wpływ na ogólną dostępność usługi.

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko wykonawcze usług Reliable Services definiuje następujące kategorie liczników wydajności:

| Kategoria | Opis |
| --- | --- |
| Replikator transakcyjny usługi Service Fabric |Liczniki specyficzne dla Replikator transakcyjny usługi Azure Service Fabric |
| Service Fabric TStore |Liczniki specyficzne dla platformy Azure usługi Service Fabric TStore |

Replikator transakcyjnego usługi Service Fabric jest używana przez [Reliable State Manager](service-fabric-reliable-services-reliable-collections-internals.md) replikacji transakcji w ramach danego zestawu [replik](service-fabric-concepts-replica-lifecycle.md).

Usługa Service Fabric TStore to składnik używany w [elementów Reliable Collections](service-fabric-reliable-services-reliable-collections-internals.md) do przechowywania i pobierania pary klucz wartość.

[Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) aplikację, która jest dostępna domyślnie w systemie operacyjnym Windows może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) to kolejna opcja umożliwiająca zbieranie danych licznika wydajności i przekazać go do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpienia licznika wydajności
Klaster, który ma dużą liczbę usług reliable services lub partycje usługi reliable service ma dużą liczbę wystąpień liczników wydajności Replikator transakcyjny. Jest również w przypadku TStore liczników wydajności, ale również jest mnożony przez liczbę słowników niezawodne i niezawodne kolejek używanych. Nazwy wystąpienia liczników wydajności mogą pomóc w identyfikacji konkretne [partycji](service-fabric-concepts-partitioning.md), repliki usługi i dostawca stanu w przypadku TStore, wystąpienie licznika wydajności z którym jest skojarzony.

#### <a name="service-fabric-transactional-replicator-category"></a>Kategoria Replikator transakcyjny usługi Service Fabric
Dla kategorii `Service Fabric Transactional Replicator`, nazwy wystąpienia licznika znajdują się w następującym formacie:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora partycji usługi Service Fabric. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentację ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) przy użyciu specyfikatora formatu "D".

*ServiceFabricReplicaId* to identyfikator skojarzony z danym repliki niezawodnej usługi. Identyfikator repliki są objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi wystąpieniami licznika wydajności wygenerowane przez tę samą partycję. Znajdują się dalsze szczegółowe informacje o replikach oraz ich rolę usług reliable Services [tutaj](service-fabric-concepts-replica-lifecycle.md).

Następująca nazwa wystąpienia licznika jest typowy dla licznika w obszarze `Service Fabric Transactional Replicator` kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

W powyższym przykładzie `00d0126d-3e36-4d68-98da-cc4f7195d85e` jest ciąg reprezentujący identyfikator partycji usługi Service Fabric i `131652217797162571` to identyfikator repliki.

#### <a name="service-fabric-tstore-category"></a>Usługa Service Fabric TStore kategorii
Dla kategorii `Service Fabric TStore`, nazwy wystąpienia licznika znajdują się w następującym formacie:

`ServiceFabricPartitionId:ServiceFabricReplicaId:ServiceFabricStateProviderId_PerformanceCounterInstanceDifferentiator`

*ServiceFabricPartitionId* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora partycji usługi Service Fabric. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentację ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) przy użyciu specyfikatora formatu "D".

*ServiceFabricReplicaId* to identyfikator skojarzony z danym repliki niezawodnej usługi. Identyfikator repliki są objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi wystąpieniami licznika wydajności wygenerowane przez tę samą partycję. Znajdują się dalsze szczegółowe informacje o replikach oraz ich rolę usług reliable Services [tutaj](service-fabric-concepts-replica-lifecycle.md).

*ServiceFabricStateProviderId* to identyfikator skojarzony z dostawcą stanu, w ramach niezawodnej usługi. Identyfikator dostawcy stanu znajduje się w nazwę wystąpienia licznika wydajności, aby odróżnić TStore od innego.

*PerformanceCounterInstanceDifferentiator* różnicujący identyfikator skojarzony z wystąpieniem liczników wydajności, w ramach dostawcy stanu. Ta różnicą znajduje się w nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi wystąpieniami licznika wydajności wygenerowane przez ten sam dostawca stanu.

Następująca nazwa wystąpienia licznika jest typowy dla licznika w obszarze `Service Fabric TStore` kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337`

W powyższym przykładzie `00d0126d-3e36-4d68-98da-cc4f7195d85e` jest ciąg reprezentujący identyfikator partycji usługi Service Fabric `131652217797162571` to identyfikator repliki `142652217797162571` to identyfikator dostawcy stanu i `1337` jest różnicą wystąpienia licznika wydajności.

### <a name="transactional-replicator-performance-counters"></a>Liczniki wydajności usługi transakcyjnych replikatora

Środowisko wykonawcze usług Reliable Services emituje następujących zdarzeń w obszarze `Service Fabric Transactional Replicator` kategorii

 Nazwa licznika | Opis |
| --- | --- |
| Rozpoczęcia operacji transakcji/s | Liczba nowych zapisu utworzone transakcje na sekundę.|
| Operacje transakcji/s | Liczba operacji Dodawanie/aktualizowanie/usuwanie na elementów reliable collections na sekundę.|
| Średni Opóźnienie opróżniania (ms) | Liczba bajtów opróżnianych na dysk przez replikatora transakcyjnego na sekundę |
| Ograniczone operacje/s | Liczba operacji odrzuconych w każdej sekundzie przez replikatora transakcyjnego ze względu na ograniczenie. |
| Średni Zatwierdzanie ms transakcji | Opóźnienie średni zatwierdzenia na transakcję w milisekundach |
| Średni Opóźnienie opróżniania (ms) | Średni czas trwania operacji opróżniania dysku, zainicjowanych przez Replikator transakcyjny (w milisekundach) |

### <a name="tstore-performance-counters"></a>Liczniki wydajności TStore

Środowisko wykonawcze usług Reliable Services emituje następujących zdarzeń w obszarze `Service Fabric TStore` kategorii

 Nazwa licznika | Opis |
| --- | --- |
| Liczba elementów | Liczba elementów w magazynie.|
| Rozmiar dysku | Rozmiar całkowitego miejsca na dysku wyrażony w bajtach, plików punktu kontrolnego dla magazynu.|
| Zapis pliku punktu kontrolnego B/s | Liczba bajtów zapisanych na sekundę dla najnowszych pliku punktu kontrolnego.|
| Transfer kopiowania dysku w B/s | Liczba bajtów dysku odczytu (w replice podstawowej) lub zapisany (w replice pomocniczej) na sekundę podczas kopiowania magazynu.|

## <a name="next-steps"></a>Kolejne kroki
[EventSource dostawców w PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
