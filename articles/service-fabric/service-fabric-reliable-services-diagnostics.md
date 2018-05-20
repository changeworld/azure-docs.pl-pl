---
title: Diagnostyka Azure Service Fabric stanowych usług Reliable Services | Dokumentacja firmy Microsoft
description: Funkcja diagnostyki dla stanowych usług Reliable Services w sieci szkieletowej usług Azure
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ae0e8f99-69ab-4d45-896d-1fa80ed45659
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 268ec61515f438fb7f98b6cef7a8ec60ba22e23f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funkcja diagnostyki dla stanowych usług Reliable Services
Klasa Azure Usługa sieci szkieletowej Stateful niezawodnej usługi StatefulServiceBase emituje [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) zdarzenia, które mogą służyć do debugowania usługi, zapewniają wgląd w sposób działania środowiska uruchomieniowego oraz pomóc w rozwiązywaniu problemów.

## <a name="eventsource-events"></a>Element EventSource zdarzenia
Nazwa źródła zdarzeń dla klasy Stateful niezawodnej usługi StatefulServiceBase jest "Microsoft-ServiceFabric-Services". Zdarzenia z źródłem tego zdarzenia wyświetlane w [zdarzenia diagnostyczne](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okna, jeśli usługa jest w trakcie [debugowania w programie Visual Studio](service-fabric-debugging-your-application.md).

Narzędzia i technologie, które pomagają w zbierania i/lub wyświetlanie zdarzeń EventSource przykłady [narzędzia PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [diagnostyki Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)i [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Zdarzenia
| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Opis zdarzenia |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informacyjne |Wysyłanego po uruchomieniu zadania RunAsync usługi |
| StatefulRunAsyncCancellation |2 |Informacyjne |Podczas anulowania zadania RunAsync usługi |
| StatefulRunAsyncCompletion |3 |Informacyjne |Podczas zadania RunAsync usługi zostało zakończone. |
| StatefulRunAsyncSlowCancellation |4 |Ostrzeżenie |Podczas zadania RunAsync usługi trwa zbyt długo, aby ukończyć anulowania |
| StatefulRunAsyncFailure |5 |Błąd |Podczas zadania RunAsync usługi zgłasza wyjątek |

## <a name="interpret-events"></a>Interpretowanie zdarzenia
Zdarzenia StatefulRunAsyncInvocation, StatefulRunAsyncCompletion i StatefulRunAsyncCancellation są przydatne do usługi składnika zapisywania usługi do informacje o cyklu życia usługi, a także chronometraż podczas usługi uruchamiania, anuluje lub zakończeniu. Te informacje mogą być przydatne, gdy debugowanie problemów dotyczących usługi lub zrozumienie cyklu życia usług.

Moduły zapisujące usługi powinna zwrócić szczególną uwagę na zdarzenia StatefulRunAsyncSlowCancellation i StatefulRunAsyncFailure, ponieważ ich wskazują na problemy z usługą.

StatefulRunAsyncFailure jest emitowany zawsze, gdy zadanie RunAsync() usługi zgłasza wyjątek. Zazwyczaj wyjątek wskazuje błąd lub usterki w usłudze. Ponadto wyjątek powoduje awarię, usługi, więc zostanie przeniesiona do innego węzła. Ta operacja może być kosztowne i może opóźnić żądań przychodzących podczas przenoszenia usługi. Moduły zapisujące usługi należy ustalić przyczynę wyjątku i, jeśli to możliwe, ograniczyć jej.

StatefulRunAsyncSlowCancellation jest emitowany zawsze, gdy żądanie anulowania zadania RunAsync trwa ponad 4 sekundy. Usługi trwa zbyt długo, aby ukończyć anulowania, wpływa na możliwość szybkiego należy ponownie uruchomić w innym węźle usługi. Ten scenariusz może mieć wpływ na ogólnej dostępności usługi.

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe niezawodne usługi definiuje następujące kategorii licznika wydajności:

| Kategoria | Opis |
| --- | --- |
| Replikator transakcyjny usługi Service Fabric |Liczniki specyficzne dla replikatora transakcyjnego sieci szkieletowej usług Azure |

Replikator transakcyjny sieci szkieletowej usług jest używany przez [niezawodnej Menedżer stanu](service-fabric-reliable-services-reliable-collections-internals.md) replikacji transakcji w ramach danego zestawu [replik](service-fabric-concepts-replica-lifecycle.md). 

[Monitor wydajności systemu Windows](https://technet.microsoft.com/library/cc749249.aspx) aplikacji, która jest dostępna domyślnie w systemie operacyjnym Windows może służyć do zbierania i przeglądać dane liczników wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) jest inną opcją w przypadku zbierania danych licznika wydajności i przekazać go do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień liczników wydajności
Klaster ma dużą liczbę niezawodne usługi lub partycje niezawodnej usługi ma dużą liczbę wystąpień liczników wydajności replikatora transakcyjnego. Nazwy wystąpień liczników wydajności mogą pomóc w określeniu konkretnym [partycji](service-fabric-concepts-partitioning.md) i repliki usługi, którą skojarzona jest wystąpienie licznika wydajności.

#### <a name="service-fabric-transactional-replicator-category"></a>Kategoria Replikator transakcyjny sieci szkieletowej usług
Dla kategorii `Service Fabric Transactional Replicator`, nazwy wystąpień liczników znajdują się w następującym formacie:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* jest reprezentację ciągu Identyfikatora partycji usługi sieć szkieletowa skojarzonego z wystąpienia licznika wydajności. Identyfikator partycji jest identyfikatorem GUID i reprezentacji ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) z specyfikator formatu "D".

*ServiceFabricReplicaId* to identyfikator skojarzony z danym repliki niezawodnej usługi. Identyfikator repliki znajduje się nazwę wystąpienia licznika wydajności w celu zapewnienia unikatowości jego i uniknąć konfliktów z innymi wystąpieniami licznika wydajności wygenerowane przez tę samą partycję. Szczegółowe informacje na temat repliki i ich role w niezawodnej usługi można znaleźć [tutaj](service-fabric-concepts-replica-lifecycle.md).

Następująca nazwa wystąpienia liczników jest typowa dla licznika w obszarze `Service Fabric Transactional Replicator` kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

W powyższym przykładzie `00d0126d-3e36-4d68-98da-cc4f7195d85e` jest reprezentację ciągu Identyfikatora partycji usługi Service Fabric i `131652217797162571` to identyfikator repliki.

### <a name="transactional-replicator-performance-counters"></a>Liczniki wydajności Replikator transakcyjny

Środowisko uruchomieniowe niezawodne usługi emituje następujących zdarzeń w obszarze `Service Fabric Transactional Replicator` kategorii

 Nazwa licznika | Opis |
| --- | --- |
| Rozpoczęcia operacji transakcji/s | Liczba nowych transakcji zapisu tworzonych w ciągu sekundy.|
| Operacje transakcji/s | Liczba operacji dodawania/aktualizacji/usuwania wykonywanych na kolekcji niezawodnej na sekundę.|
| Średni Flush czas oczekiwania (ms) | Liczba bajtów opróżnianych na dysk przez replikatora transakcyjnego na sekundę |
| Ograniczone operacje/s | Liczba operacji odrzuconych w każdej sekundzie przez replikatora transakcyjnego z powodu ograniczania. |
| Średni Ms/zatwierdzania transakcji | Opóźnienie zatwierdzania średnia dla transakcji w milisekundach |
| Średni Flush czas oczekiwania (ms) | Średni czas trwania operacji opróżniania dysku inicjowane przez replikatora transakcyjnego w milisekundach |

## <a name="next-steps"></a>Kolejne kroki
[Element EventSource dostawców w narzędzia PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
