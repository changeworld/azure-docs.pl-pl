---
title: Diagnostyka stanowych niezawodnych usług usługi azure service
description: Funkcje diagnostyczne dla niezawodnych usług stanowych w sieci szkieletowej usług Azure
author: dkkapur
ms.topic: conceptual
ms.date: 8/24/2018
ms.author: dekapur
ms.openlocfilehash: 37162287e130b05dc41453c579b3a628ac878fca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282265"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Funkcja diagnostyki dla stanowych usług Reliable Services
Klasa Usługi Azure Service Stateful Reliable Services StatefulServiceBase emituje zdarzenia [EventSource,](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) które mogą służyć do debugowania usługi, zapewniają wgląd w sposób działania środowiska wykonawczego i pomoc w rozwiązywaniu problemów.

## <a name="eventsource-events"></a>Zdarzenia EventSource
Nazwa eventsource dla stateful reliable services StatefulServiceBase klasy jest "Microsoft-ServiceFabric-Services." Zdarzenia z tego źródła zdarzeń są wyświetlane w oknie [Zdarzenia diagnostyczne,](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) gdy usługa jest [debugowana w programie Visual Studio.](service-fabric-debugging-your-application.md)

Przykładami narzędzi i technologii ułatwiających zbieranie i/lub wyświetlanie zdarzeń EventSource są [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Diagnostyka platformy Azure](../cloud-services/cloud-services-dotnet-diagnostics.md)i [Biblioteka zdarzeń śledzenia firmy Microsoft.](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)

## <a name="events"></a>Zdarzenia
| Nazwa wydarzenia | Identyfikator zdarzenia | Poziom | Opis zdarzenia |
| --- | --- | --- | --- |
| StatefulRunAsyncInwovacja |1 |Informacyjne |Emitowane po uruchomieniu zadania RunAsync usługi |
| StatefulRunAsyncCancellation (StatefulRunAsyncCancellation) |2 |Informacyjne |Emitowane, gdy usługa RunAsync zadanie jest anulowane |
| StatefulRunAsyncUkońca |3 |Informacyjne |Emitowane po zakończeniu zadania RunAsync usługi |
| StatefulRunAsyncSlowCancellation |4 |Ostrzeżenie |Emitowane, gdy zadanie RunAsync usługi trwa zbyt długo, aby zakończyć anulowanie |
| StatefulRunAsyncFailure |5 |Błąd |Emitowane, gdy usługa RunAsync zadanie zgłasza wyjątek |

## <a name="interpret-events"></a>Interpretowanie zdarzeń
StatefulRunAsyncInvocation, StatefulRunAsyncCompletion i StatefulRunAsyncCancellation zdarzenia są przydatne do modułu zapisującego usługi, aby zrozumieć cykl życia usługi, a także czas, kiedy usługa jest uruchamiana, anuluje lub kończy. Te informacje mogą być przydatne podczas debugowania problemów z usługą lub zrozumienia cyklu życia usługi.

Autorzy usługi należy zwrócić szczególną uwagę na StatefulRunAsyncSlowation i StatefulRunAsyncFailure zdarzeń, ponieważ wskazują one problemy z usługą.

StatefulRunAsyncFailure jest emitowany za każdym razem, gdy usługa RunAsync() zadanie zgłasza wyjątek. Zazwyczaj wyjątek wskazuje błąd lub błąd w usłudze. Ponadto wyjątek powoduje, że usługa nie powiedzie się, więc jest przenoszony do innego węzła. Ta operacja może być kosztowne i może opóźniać przychodzące żądania, gdy usługa jest przenoszona. Moduły odwołują usługi należy określić przyczynę wyjątku i, jeśli to możliwe, ograniczyć go.

StatefulRunAsyncSlowation jest emitowany, gdy żądanie anulowania dla zadania RunAsync trwa dłużej niż cztery sekundy. Gdy usługa trwa zbyt długo, aby zakończyć anulowanie, wpływa na możliwość usługi do szybkiego ponownego uruchomienia w innym węźle. Ten scenariusz może mieć wpływ na ogólną dostępność usługi.

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe niezawodne usługi definiuje następujące kategorie liczników wydajności:

| Kategoria | Opis |
| --- | --- |
| Replikator transakcyjny sieci szkieletowej usług |Liczniki specyficzne dla replikatora transakcyjnego sieci szkieletowej usługi Azure |
| Usługa Fabric TStore |Liczniki specyficzne dla usługi Azure Service Fabric TStore |

Replikator transakcyjny sieci szkieletowej usług jest używany przez [Menedżera stanu niezawodne](service-fabric-reliable-services-reliable-collections-internals.md) do replikowania transakcji w ramach danego zestawu [replik](service-fabric-concepts-replica-lifecycle.md).

TStore sieci szkieletowej usług jest składnikiem używanym w [niezawodnych kolekcjach](service-fabric-reliable-services-reliable-collections-internals.md) do przechowywania i pobierania par klucz-wartość.

Aplikacja [Monitor wydajności systemu Windows,](https://technet.microsoft.com/library/cc749249.aspx) która jest domyślnie dostępna w systemie operacyjnym Windows, może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka platformy Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) to kolejna opcja do zbierania danych licznika wydajności i przekazywania ich do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień licznika wydajności
Klaster, który ma dużą liczbę niezawodnych usług lub partycji niezawodnej usługi będzie miał dużą liczbę wystąpień licznika wydajności replikatora transakcyjnego. Dotyczy to również liczników wydajności TStore, ale jest również mnożona przez liczbę używanych słowników niezawodnych i niezawodnych kolejek. Nazwy wystąpienia licznika wydajności może pomóc w identyfikacji określonej [partycji](service-fabric-concepts-partitioning.md), repliki usługi i dostawcy stanu w przypadku TStore, że wystąpienie licznika wydajności jest skojarzony z.

#### <a name="service-fabric-transactional-replicator-category"></a>Kategoria replikatora transakcyjnego sieci szkieletowej usług
W przypadku `Service Fabric Transactional Replicator`kategorii nazwy wystąpień licznika są w następującym formacie:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* jest reprezentacją ciągu identyfikatora partycji sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) jego reprezentacja ciągu jest generowana za pomocą specyfikatora formatu "D".

*ServiceFabricReplicaId* jest identyfikatorem skojarzonym z daną repliką niezawodnej usługi. Identyfikator repliki znajduje się w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi wystąpieniami licznika wydajności generowanymi przez tę samą partycję. Więcej informacji na temat replik i ich roli w niezawodnych usługach można znaleźć [tutaj.](service-fabric-concepts-replica-lifecycle.md)

Następująca nazwa wystąpienia licznika jest `Service Fabric Transactional Replicator` typowa dla licznika w kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

W poprzednim przykładzie `00d0126d-3e36-4d68-98da-cc4f7195d85e` jest reprezentacja ciągu identyfikatora partycji `131652217797162571` sieci szkieletowej usług i jest identyfikatorem repliki.

#### <a name="service-fabric-tstore-category"></a>Kategoria TStore usługi Fabric
W przypadku `Service Fabric TStore`kategorii nazwy wystąpień licznika są w następującym formacie:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* jest reprezentacją ciągu identyfikatora partycji sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) jego reprezentacja ciągu jest generowana za pomocą specyfikatora formatu "D".

*ServiceFabricReplicaId* jest identyfikatorem skojarzonym z daną repliką niezawodnej usługi. Identyfikator repliki znajduje się w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi wystąpieniami licznika wydajności generowanymi przez tę samą partycję. Więcej informacji na temat replik i ich roli w niezawodnych usługach można znaleźć [tutaj.](service-fabric-concepts-replica-lifecycle.md)

*StateProviderId* jest identyfikatorem skojarzonym z dostawcą stanu w ramach niezawodnej usługi. Identyfikator dostawcy stanu znajduje się w nazwie wystąpienia licznika wydajności, aby odróżnić sklep TStore od innego.

*PerformanceCounterInstanceDifferentiator* jest identyfikatorem różnicowania skojarzonym z wystąpieniem licznika wydajności w dostawcy stanu. Ten wyróżnik znajduje się w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi wystąpieniami licznika wydajności generowanymi przez tego samego dostawcę stanu.

*StateProviderName* to nazwa skojarzona z dostawcą stanu w ramach niezawodnej usługi. Nazwa dostawcy stanu znajduje się w nazwie wystąpienia licznika wydajności dla użytkowników, aby łatwo zidentyfikować, jaki stan zapewnia.

Następująca nazwa wystąpienia licznika jest `Service Fabric TStore` typowa dla licznika w kategorii:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

W poprzednim przykładzie `00d0126d-3e36-4d68-98da-cc4f7195d85e` jest reprezentacja ciągu identyfikatora partycji sieci szkieletowej usług, `131652217797162571` jest identyfikatorem repliki, `142652217797162571` jest identyfikatorem dostawcy stanu i `1337` jest wyróżnikiem wystąpienia licznika wydajności. `urn:MyReliableDictionary/dataStore`jest nazwą dostawcy stanu, który przechowuje `urn:MyReliableDictionary`dane dla kolekcji o nazwie .

### <a name="transactional-replicator-performance-counters"></a>Liczniki wydajności replikatora transakcyjnego

Środowisko wykonawcze Reliable Services emituje `Service Fabric Transactional Replicator` następujące zdarzenia w kategorii

 Nazwa licznika | Opis |
| --- | --- |
| Rozpoczynanie operacji Txn/s | Liczba nowych transakcji zapisu utworzonych na sekundę.|
| Operacje Txn/s | Liczba operacji dodawania/aktualizacji/usuwania wykonywanych w niezawodnych kolekcjach na sekundę.|
| Bajty opróżnianie dziennika/s | Liczba bajtów opróżnionych na dysk przez replikatora transakcyjnego na sekundę |
| Operacje ograniczone/s | Liczba operacji odrzuconych co sekundę przez replikatora transakcyjnego z powodu ograniczania przepustowości. |
| Średnia transakcja ms/commit | Średnie opóźnienie zatwierdzania na transakcję w milisekundach |
| Średni opóźnienie (ms) | Średni czas trwania operacji opróżniania dysku inicjowanych przez replikatora transakcyjnego w milisekundach |

### <a name="tstore-performance-counters"></a>Liczniki wydajności TStore

Środowisko wykonawcze Reliable Services emituje `Service Fabric TStore` następujące zdarzenia w kategorii

 Nazwa licznika | Opis |
| --- | --- |
| Liczba przedmiotów | Liczba towarów w sklepie.|
| Rozmiar dysku | Całkowity rozmiar dysku w bajtach plików punktów kontrolnych dla magazynu.|
| Bajty zapisu pliku punktu kontrolnego/s | Liczba bajtów zapisanych na sekundę dla najnowszego pliku punktu kontrolnego.|
| Kopiowanie bajtów transferu dysku na sekundę | Liczba bajtów dysku odczytanych (w replice podstawowej) lub zapisywanych (w replice pomocniczej) na sekundę podczas kopiowania magazynu.|

## <a name="next-steps"></a>Następne kroki
[Dostawcy usługi EventSource w utrw](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
