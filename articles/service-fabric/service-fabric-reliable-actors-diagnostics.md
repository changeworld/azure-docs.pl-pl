---
title: Diagnostyka i monitorowanie aktorów | Dokumentacja firmy Microsoft
description: W tym artykule opisano dane diagnostyczne i funkcje w środowisku uruchomieniowym elementów Reliable Actors usługi Service Fabric, w tym zdarzenia i liczniki wydajności emitowane przez niego do monitorowania wydajności.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: chackdan
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 5f573db887b3acc2c4a668a8c19c7f8e3cb25019
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670748"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostyka i monitorowanie wydajności struktury Reliable Actors
W czasie wykonywania w elementach Reliable Actors emituje [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) zdarzeń i [liczniki wydajności](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Te zapewniają wgląd w jaki sposób działa środowisko uruchomieniowe i ułatwić rozwiązywanie problemów i monitorowania wydajności.

## <a name="eventsource-events"></a>Zdarzeń EventSource
Nazwa dostawcy źródła zdarzeń dla środowiska uruchomieniowego elementów Reliable Actors to "Microsoft-ServiceFabric Aktorzy". Zdarzenia z tego źródła zdarzeń są wyświetlane w [zdarzenia diagnostyczne](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okna, gdy jest aplikacja aktora [debugowania w programie Visual Studio](service-fabric-debugging-your-application.md).

Narzędzia i technologie, które pomagają w zbieraniu i/lub wyświetlanie zdarzeń EventSource przykłady [narzędzia PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [diagnostyki Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [semantycznego rejestrowania](https://msdn.microsoft.com/library/dn774980.aspx)i [ Biblioteki TraceEvent Microsoft](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Słowa kluczowe
Wszystkie zdarzenia, które należą do wiarygodne źródło zdarzeń uczestników są skojarzone z jedną lub więcej słów kluczowych. Dzięki temu filtrowanie zdarzeń, które są zbierane. Zdefiniowano następujące bitów słowa kluczowego.

| Bit | Opis |
| --- | --- |
| 0x1 |Ustaw o ważnych zdarzeniach, które podsumowują działania środowiska uruchomieniowego aktorów sieci szkieletowej. |
| 0x2 |Zestaw zdarzeń, które opisują wywołania metody aktora. Aby uzyskać więcej informacji, zobacz [wprowadzające tematu aktorów](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Zestaw zdarzeń związanych z stanu aktora. Aby uzyskać więcej informacji, zobacz temat w [zarządzania stanem aktora](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Zestaw zdarzeń związanych z tury współbieżności w aktora. Aby uzyskać więcej informacji, zobacz temat w [współbieżności](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe elementów Reliable Actors definiuje następujące kategorie liczników wydajności.

| Kategoria | Opis |
| --- | --- |
| Aktor usługi Service Fabric |Liczniki specyficzne dla usługi Azure Service Fabric aktorów, np. czas zapisania stanu aktora |
| Metoda aktora usługi Service Fabric |Liczniki specyficzne dla metod zaimplementowanych przez aktorów usługi Service Fabric, np. częstotliwość aktora wywoływana jest metoda |

Każda z powyższych kategorii ma co najmniej jeden licznik.

[Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) aplikację, która jest dostępna domyślnie w systemie operacyjnym Windows może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) to kolejna opcja umożliwiająca zbieranie danych licznika wydajności i przekazać go do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpienia licznika wydajności
Klaster, który ma dużą liczbę usług aktora lub partycje usługi aktora ma dużą liczbę wystąpień liczników wydajności aktora. Nazwy wystąpienia liczników wydajności mogą pomóc w identyfikacji konkretne [partycji](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) i metoda aktora (jeśli dotyczy) skojarzonego z wystąpienie licznika wydajności.

#### <a name="service-fabric-actor-category"></a>Kategoria Aktor usługi Service Fabric
Dla kategorii `Service Fabric Actor`, nazwy wystąpienia licznika znajdują się w następującym formacie:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora partycji usługi Service Fabric. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentację ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody przy użyciu specyfikatora formatu "D".

*ActorRuntimeInternalID* jest ciąg reprezentujący 64-bitową liczbę całkowitą, który jest generowany przez środowisko uruchomieniowe aktorów sieci szkieletowej użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi nazwy wystąpienia licznika wydajności. Użytkownicy nie należy próbować interpretacji ta część nazwę wystąpienia licznika wydajności.

Oto przykład nazwę wystąpienia licznika licznika, który należy do `Service Fabric Actor` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

W powyższym przykładzie `2740af29-78aa-44bc-a20b-7e60fb783264` jest ciąg reprezentujący identyfikator partycji usługi Service Fabric i `635650083799324046` jest identyfikator 64-bitowym, który jest generowany dla wewnętrznego środowiska uruchomieniowego.

#### <a name="service-fabric-actor-method-category"></a>Usługa Service Fabric aktora metoda kategorii
Dla kategorii `Service Fabric Actor Method`, nazwy wystąpienia licznika znajdują się w następującym formacie:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* jest nazwą metody aktora, która wystąpienie licznika wydajności jest skojarzony. Format nazwy metody jest określana w oparciu o logikę w środowisku uruchomieniowym aktorów sieci szkieletowej, która równoważy czytelność nazwy z ograniczeniami na maksymalną długość nazwy wystąpienia licznika wydajności w Windows.

*ActorsRuntimeMethodId* jest ciąg reprezentujący 32-bitową liczbę całkowitą, który jest generowany przez środowisko uruchomieniowe aktorów sieci szkieletowej użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi nazwy wystąpienia licznika wydajności. Użytkownicy nie należy próbować interpretacji ta część nazwę wystąpienia licznika wydajności.

*ServiceFabricPartitionID* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora partycji usługi Service Fabric. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentację ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody przy użyciu specyfikatora formatu "D".

*ActorRuntimeInternalID* jest ciąg reprezentujący 64-bitową liczbę całkowitą, który jest generowany przez środowisko uruchomieniowe aktorów sieci szkieletowej użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi nazwy wystąpienia licznika wydajności. Użytkownicy nie należy próbować interpretacji ta część nazwę wystąpienia licznika wydajności.

Oto przykład nazwę wystąpienia licznika licznika, który należy do `Service Fabric Actor Method` kategorii:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

W powyższym przykładzie `ivoicemailboxactor.leavemessageasync` jest nazwa metody `2` jest Identyfikatorem 32-bitowych wygenerowane do użytku wewnętrznego w środowisku uruchomieniowym, `89383d32-e57e-4a9b-a6ad-57c6792aa521` jest ciąg reprezentujący identyfikator partycji usługi Service Fabric i `635650083804480486` jest 64-bitowy identyfikator wygenerowany dla użytku wewnętrznego w środowisku uruchomieniowym.

## <a name="list-of-events-and-performance-counters"></a>Lista zdarzeń i liczników wydajności
### <a name="actor-method-events-and-performance-counters"></a>Zdarzenia metod aktora i liczniki wydajności
Środowisko uruchomieniowe elementów Reliable Actors emituje następujące zdarzenia związane z [metody aktora](service-fabric-reliable-actors-introduction.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Pełne |0x2 |Środowisko uruchomieniowe aktorów jest wywołać metodę aktora. |
| ActorMethodStop |8 |Pełne |0x2 |Metoda aktora zakończenia. Oznacza to zwrócił w środowisku uruchomieniowym asynchroniczne wywołanie metody aktora i ukończeniu zadania zwracany przez metodę aktora. |
| ActorMethodThrewException |9 |Ostrzeżenie |0x3 |Wystąpił wyjątek podczas wykonywania metody aktora podczas wywołania asynchronicznego w środowisku uruchomieniowym do metody aktora lub podczas wykonywania zadania tego typu zwracanego przez metodę aktora. To zdarzenie oznacza pewnego rodzaju błędów w kodzie aktora, który wymaga zbadania. |

Środowisko uruchomieniowe elementów Reliable Actors publikuje następujące liczniki wydajności, związane z uruchamianiem metody aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Metoda aktora usługi Service Fabric |Wywołania/s |Liczba prób wywołaniu metody usługi aktora na sekundę |
| Metoda aktora usługi Service Fabric |Średnia liczba milisekund dla wywołania |Czas wykonania metody usługi aktora w milisekundach |
| Metoda aktora usługi Service Fabric |Zgłoszone wyjątki/s |Ile razy metody usługi aktora zgłosił wyjątek na sekundę |

### <a name="concurrency-events-and-performance-counters"></a>Zdarzenia współbieżności i liczniki wydajności
Środowisko uruchomieniowe elementów Reliable Actors emituje następujące zdarzenia związane z [współbieżności](service-fabric-reliable-actors-introduction.md#concurrency).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Pełne |0x8 |To zdarzenie jest zapisywany na początku każdego nowego Włącz w aktora. Liczba oczekujących wywołań aktora, które oczekują na uzyskanie blokady dla aktora, który wymusza współbieżności opartej na włączanie go zawiera. |

Środowisko uruchomieniowe elementów Reliable Actors publikuje następujące liczniki wydajności powiązane z współbieżności.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Liczba wywołań aktora czekających na blokadę aktora |Liczba oczekujących wywołań aktora czekających można uzyskać blokady dla aktora, który wymusza współbieżności opartej na Włącz |
| Aktor usługi Service Fabric |Średni czas oczekiwania na blokadę (milisekundy) |Czas trwania (w milisekundach), można uzyskać blokady dla aktora, który wymusza współbieżności opartej na Włącz |
| Aktor usługi Service Fabric |Średni czas utrzymania blokady aktora (milisekundy) |Czas (w milisekundach), dla którego jest czas utrzymania blokady dla aktora |

### <a name="actor-state-management-events-and-performance-counters"></a>Liczniki wydajności i zdarzeń zarządzania stanu aktora
Środowisko uruchomieniowe elementów Reliable Actors emituje następujące zdarzenia związane z [zarządzania stanem aktora](service-fabric-reliable-actors-state-management.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Pełne |0x4 |Środowisko uruchomieniowe aktorów jest zapisania stanu aktora. |
| ActorSaveStateStop |11 |Pełne |0x4 |Środowisko uruchomieniowe aktorów zostało zakończone, Zapisywanie stanu aktora. |

Środowisko uruchomieniowe elementów Reliable Actors publikuje następujące liczniki wydajności, związane z zarządzaniem stanu aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Średnia liczba milisekund dla operacji zapisu stanu |Czas zapisania stanu aktora w milisekundach |
| Aktor usługi Service Fabric |Średni czas operacji ładowania stanu (milisekundy) |Czas ładowania stanu aktora w milisekundach |

### <a name="events-related-to-actor-replicas"></a>Zdarzenia związane z replik aktorów
Środowisko uruchomieniowe elementów Reliable Actors emituje następujące zdarzenia związane z [replik aktorów](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informacyjne |0x1 |Repliki aktora zmienić roli podstawowej. Oznacza to, że Aktorzy dla tej partycji zostanie utworzony wewnątrz tej repliki. |
| ReplicaChangeRoleFromPrimary |2 |Informacyjne |0x1 |Repliki aktora zmienić rolę do innych niż podstawowe. Oznacza to, że Reliable actors dla tej partycji nie jest już zostanie utworzony wewnątrz tej repliki. Żadne żądania nie zostaną dostarczone do podmiotów już utworzone w ramach tej repliki. Aktorzy zostaną usunięte po ukończeniu wszystkich żądań w toku. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktor Aktywacja i dezaktywacja zdarzenia i liczniki wydajności
Środowisko uruchomieniowe elementów Reliable Actors emituje następujące zdarzenia związane z [aktora Aktywacja i dezaktywacja](service-fabric-reliable-actors-lifecycle.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informacyjne |0x1 |Aktor został aktywowany. |
| ActorDeactivated |6 |Informacyjne |0x1 |Dezaktywowano aktora. |

Środowisko uruchomieniowe elementów Reliable Actors publikuje następujące liczniki wydajności powiązane z aktora Aktywacja i dezaktywacja.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Średni czas dla metody OnActivateAsync (milisekundy) |Czas wykonania metody OnActivateAsync w milisekundach |

### <a name="actor-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądania aktora
Gdy klient wywołuje metodę za pośrednictwem obiektu serwera proxy aktora, wynikiem komunikat żądania wysyłane za pośrednictwem sieci z usługą aktora. Usługa przetwarza komunikat żądania i wysyła odpowiedź z powrotem do klienta. Środowisko uruchomieniowe elementów Reliable Actors publikuje następujące liczniki wydajności powiązane z przetwarzania żądania aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Liczba oczekujących żądań |Liczba żądań przetwarzanych w usłudze |
| Aktor usługi Service Fabric |Średni czas na żądanie (milisekundy) |Czas (w milisekundach) przez usługę do przetwarzania żądań |
| Aktor usługi Service Fabric |Średni czas deserializacji żądania (milisekundy) |Czas trwania (w milisekundach) do deserializacji komunikatu żądania aktora, po odebraniu na usługę |
| Aktor usługi Service Fabric |Średni czas serializacji odpowiedzi (milisekundy) |Czas trwania (w milisekundach), można serializować komunikatu odpowiedzi aktora na usługę przed odpowiedź jest wysyłana do klienta |

## <a name="next-steps"></a>Kolejne kroki
* [Jak używać platformy usługi Service Fabric w elementach Reliable Actors](service-fabric-reliable-actors-platform.md)
* [Dokumentacja interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource dostawców w PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
