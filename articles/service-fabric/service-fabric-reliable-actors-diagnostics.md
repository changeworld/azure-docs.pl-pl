---
title: Diagnostyka i monitorowanie podmiotów
description: W tym artykule opisano funkcje diagnostyki i monitorowania wydajności w czasie wykonywania usługi sieci szkieletowej niezawodne podmioty, w tym zdarzenia i liczniki wydajności emitowane przez niego.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: e6e9fb66368461e0d3ebdd2709f4ced0e796bea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282330"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostyka i monitorowanie wydajności struktury Reliable Actors
Środowisko wykonawcze Reliable Actors emituje zdarzenia [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) i [liczniki wydajności.](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) Zapewniają one wgląd w sposób działania środowiska wykonawczego i pomoc w rozwiązywaniu problemów i monitorowaniu wydajności.

## <a name="eventsource-events"></a>Zdarzenia EventSource
Nazwa dostawcy EventSource środowiska wykonawczego Reliable Actors to "Microsoft-ServiceFabric-Actors". Zdarzenia z tego źródła zdarzeń pojawiają się w oknie [Zdarzenia diagnostyczne,](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) gdy aplikacja aktora jest [debugowana w programie Visual Studio.](service-fabric-debugging-your-application.md)

Przykładami narzędzi i technologii ułatwiających zbieranie i/lub wyświetlanie zdarzeń EventSource są [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Diagnostyka platformy Azure,](../cloud-services/cloud-services-dotnet-diagnostics.md) [Rejestrowanie semantyczne](https://msdn.microsoft.com/library/dn774980.aspx)i [Biblioteka śledzenia zdarzeń firmy Microsoft.](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent)

### <a name="keywords"></a>Słowa kluczowe
Wszystkie zdarzenia, które należą do Reliable Actors EventSource są skojarzone z co najmniej jednym słowem kluczowym. Umożliwia to filtrowanie zdarzeń, które są zbierane. Zdefiniowane są następujące bity słów kluczowych.

| Bitowych | Opis |
| --- | --- |
| 0x1 |Zestaw ważnych zdarzeń, które podsumowują działanie środowiska uruchomieniowego aktorzy sieci szkieletowej. |
| 0x2 |Zestaw zdarzeń, które opisują wywołania metody aktora. Aby uzyskać więcej informacji, zobacz [temat wprowadzający na temat aktorów](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Zestaw zdarzeń związanych ze stanem aktora. Aby uzyskać więcej informacji, zobacz temat [zarządzania stanem aktora](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Zestaw zdarzeń związanych z koncelisja w aktora. Aby uzyskać więcej informacji, zobacz temat [współbieżności](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe Reliable Actors definiuje następujące kategorie liczników wydajności.

| Kategoria | Opis |
| --- | --- |
| Aktor sieci szkieletowej usług |Liczniki specyficzne dla podmiotów sieci szkieletowej usług Azure, np. |
| Metoda aktora sieci szkieletowej usług |Liczniki specyficzne dla metod implementowanych przez podmioty sieci szkieletowej usług, np. |

Każda z powyższych kategorii ma jeden lub więcej liczników.

Aplikacja [Monitor wydajności systemu Windows,](https://technet.microsoft.com/library/cc749249.aspx) która jest domyślnie dostępna w systemie operacyjnym Windows, może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka platformy Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) to kolejna opcja do zbierania danych licznika wydajności i przekazywania ich do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień licznika wydajności
Klaster, który ma dużą liczbę usług aktora lub partycji usługi aktora będzie miał dużą liczbę wystąpień licznika wydajności aktora. Nazwy wystąpień licznika wydajności może pomóc w identyfikacji określonej [partycji](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) i metody aktora (jeśli dotyczy), że wystąpienie licznika wydajności jest skojarzony z.

#### <a name="service-fabric-actor-category"></a>Kategoria Aktora sieci szkieletowej usług
W przypadku `Service Fabric Actor`kategorii nazwy wystąpień licznika są w następującym formacie:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* jest reprezentacją ciągu identyfikatora partycji sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) reprezentacja ciągu jest generowana za pomocą metody z specyfikatorem formatu "D".

*ActorRuntimeInternalID* jest reprezentacją ciągu 64-bitowej liczby całkowitej, która jest generowana przez środowisko uruchomieniowe aktorzy sieci szkieletowej do użytku wewnętrznego. Jest to zawarte w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi nazwami wystąpień licznika wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Actor` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

W powyższym `2740af29-78aa-44bc-a20b-7e60fb783264` przykładzie jest reprezentacja ciągu identyfikator partycji sieci szkieletowej usług i `635650083799324046` jest 64-bitowy identyfikator, który jest generowany do użytku wewnętrznego środowiska wykonawczego.

#### <a name="service-fabric-actor-method-category"></a>Kategoria Metoda aktora sieci szkieletowej usługi
W przypadku `Service Fabric Actor Method`kategorii nazwy wystąpień licznika są w następującym formacie:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* to nazwa metody aktora, z którą jest skojarzone wystąpienie licznika wydajności. Format nazwy metody jest określany na podstawie pewnej logiki w czasie wykonywania podmiotów sieci szkieletowej, która równoważy czytelność nazwy z ograniczeniami dotyczącymi maksymalnej długości nazw wystąpień licznika wydajności w systemie Windows.

*ActorsRuntimeMethodId* jest reprezentacją ciągu 32-bitowej liczby całkowitej, która jest generowana przez środowisko uruchomieniowe aktorzy sieci szkieletowej do użytku wewnętrznego. Jest to zawarte w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi nazwami wystąpień licznika wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

*ServiceFabricPartitionID* jest reprezentacją ciągu identyfikatora partycji sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) reprezentacja ciągu jest generowana za pomocą metody z specyfikatorem formatu "D".

*ActorRuntimeInternalID* jest reprezentacją ciągu 64-bitowej liczby całkowitej, która jest generowana przez środowisko uruchomieniowe aktorzy sieci szkieletowej do użytku wewnętrznego. Jest to zawarte w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi nazwami wystąpień licznika wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Actor Method` kategorii:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

W powyższym `ivoicemailboxactor.leavemessageasync` przykładzie jest `2` nazwą metody, jest 32-bitowy identyfikator generowany dla `89383d32-e57e-4a9b-a6ad-57c6792aa521` użytku wewnętrznego środowiska wykonawczego, jest `635650083804480486` reprezentacją ciągu identyfikator partycji sieci szkieletowej usług i jest 64-bitowy identyfikator generowany dla wewnętrznego użytku środowiska wykonawczego.

## <a name="list-of-events-and-performance-counters"></a>Lista zdarzeń i liczników wydajności
### <a name="actor-method-events-and-performance-counters"></a>Zdarzenia metody aktora i liczniki wydajności
Środowisko wykonawcze Reliable Actors emituje następujące zdarzenia związane z [metodami aktora.](service-fabric-reliable-actors-introduction.md)

| Nazwa wydarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorMethodStart (Początek metody aktorki) |7 |Pełny |0x2 |Środowisko uruchomieniowe aktorów ma zamiar wywołać metodę aktora. |
| AktorMetodStop |8 |Pełny |0x2 |Metoda aktora została zakończona wykonywanie. Oznacza to, że wywołanie asynchroniczne środowiska wykonawczego do metody aktora zostało zwrócone, a zadanie zwrócone przez metodę aktora zostało zakończone. |
| AktorMethodThrewException |9 |Ostrzeżenie |0x3 |Wyjątek został zgłoszony podczas wykonywania metody aktora, podczas wywołania asynchronicznego środowiska wykonawczego do metody aktora lub podczas wykonywania zadania zwróconego przez metodę aktora. To zdarzenie wskazuje pewnego rodzaju błąd w kodzie aktora, który wymaga badania. |

Środowisko wykonawcze Reliable Actors publikuje następujące liczniki wydajności związane z wykonywaniem metod aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Metoda aktora sieci szkieletowej usług |Wywołania/s |Liczba wywołań metody usługi aktora na sekundę |
| Metoda aktora sieci szkieletowej usług |Średnia milisekundy na wywołanie |Czas wykonania metody usługi aktora w milisekundach |
| Metoda aktora sieci szkieletowej usług |Wyjątki generowane/s |Liczba wyświetlenia wyjątku przez metodę usługi aktora na sekundę |

### <a name="concurrency-events-and-performance-counters"></a>Zdarzenia współbieżności i liczniki wydajności
Środowisko wykonawcze Reliable Actors emituje następujące zdarzenia związane z [współbieżnością](service-fabric-reliable-actors-introduction.md#concurrency).

| Nazwa wydarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| AktorMethodCallsWaitingForLock |12 |Pełny |0x8 |To zdarzenie jest napisane na początku każdej nowej tury w aktora. Zawiera liczbę oczekujących wywołań aktora, które oczekują na uzyskanie blokady na aktora, która wymusza współbieżność turową. |

Środowisko wykonawcze Reliable Actors publikuje następujące liczniki wydajności związane z współbieżnością.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor sieci szkieletowej usług |Liczba połączeń aktora czeka na blokadę aktora |Liczba oczekujących wywołań aktora oczekujących na zdobycie blokady na aktora, która wymusza współbieżność turową |
| Aktor sieci szkieletowej usług |Średnia milisekundy na blokadę oczekiwania |Czas (w milisekundach) na nabycie blokady na aktora, która wymusza współbieżność turową |
| Aktor sieci szkieletowej usług |Średnia blokada aktora milisekundy trzymana |Czas (w milisekundach), dla którego blokada na aktora jest utrzymywana |

### <a name="actor-state-management-events-and-performance-counters"></a>Zdarzenia zarządzania stanem aktora i liczniki wydajności
Środowisko wykonawcze Reliable Actors emituje następujące zdarzenia związane z [zarządzaniem stanem aktora](service-fabric-reliable-actors-state-management.md).

| Nazwa wydarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Pełny |0x4 |Środowisko uruchomieniowe aktorów ma zamiar zapisać stan aktora. |
| ActorSaveStateStop |11 |Pełny |0x4 |Środowisko wykonawcze aktorów zakończyło zapisywanie stanu aktora. |

Środowisko wykonawcze Reliable Actors publikuje następujące liczniki wydajności związane z zarządzaniem stanem aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor sieci szkieletowej usług |Średnia milisekundy na operację stanu zapisu |Czas pracy w celu zapisania stanu aktora w milisekundach |
| Aktor sieci szkieletowej usług |Średnia milisekunda na operację stanu obciążenia |Czas ładowania stanu aktora w milisekundach |

### <a name="events-related-to-actor-replicas"></a>Zdarzenia związane z replikami aktorów
Środowisko wykonawcze Reliable Actors emituje następujące zdarzenia związane z [replikami aktora.](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)

| Nazwa wydarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ReplikaRoleToPrimary |1 |Informacyjne |0x1 |Replika aktora zmieniła rolę na Podstawowa. Oznacza to, że podmioty dla tej partycji zostaną utworzone wewnątrz tej repliki. |
| ReplicaChangeRoleFromPrimary |2 |Informacyjne |0x1 |Replika aktora zmieniła rolę na nienakazową. Oznacza to, że podmioty dla tej partycji nie będą już tworzone wewnątrz tej repliki. Żadne nowe żądania nie zostaną dostarczone do podmiotów już utworzonych w ramach tej repliki. Podmioty zostaną zniszczone po zakończeniu wszelkich wniosków w toku. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Zdarzenia aktywacji i dezaktywacji aktora oraz liczniki wydajności
Środowisko wykonawcze Reliable Actors emituje następujące zdarzenia związane z [aktywacją i dezaktywacją aktora.](service-fabric-reliable-actors-lifecycle.md)

| Nazwa wydarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| Aktor aktywowany |5 |Informacyjne |0x1 |Aktor został aktywowany. |
| AktorZaaktywowany |6 |Informacyjne |0x1 |Aktor został dezaktywowany. |

Środowisko wykonawcze Reliable Actors publikuje następujące liczniki wydajności związane z aktywacją i dezaktywacją aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor sieci szkieletowej usług |Średnia onactivateAsync milisekundy |Czas wykonania metody OnActivateAsync w milisekundach |

### <a name="actor-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądań aktora
Gdy klient wywołuje metodę za pośrednictwem obiektu proxy aktora, powoduje to wysłanie wiadomości żądania za pośrednictwem sieci do usługi aktora. Usługa przetwarza komunikat żądania i wysyła odpowiedź z powrotem do klienta. Środowisko wykonawcze Reliable Actors publikuje następujące liczniki wydajności związane z przetwarzaniem żądań aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor sieci szkieletowej usług |Liczba zaległych wniosków |Liczba żądań przetwarzanych w usłudze |
| Aktor sieci szkieletowej usług |Średnia milisekundy na żądanie |Czas przejmujeny przez usługę (w milisekundach) na przetworzenie żądania |
| Aktor sieci szkieletowej usług |Średnia milisekundy dla deserializacji żądania |Czas (w milisekundach) na deserializację komunikatu żądania aktora po odebraniu go w usłudze |
| Aktor sieci szkieletowej usług |Średnia milisekundy dla serializacji odpowiedzi |Czas potrzebny (w milisekundach) do serializacji komunikatu odpowiedzi aktora w usłudze przed wysłaniem odpowiedzi do klienta |

## <a name="next-steps"></a>Następne kroki
* [Jak niezawodni aktorzy korzystają z platformy sieci szkieletowej usług](service-fabric-reliable-actors-platform.md)
* [Dokumentacja referencyjna interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Dostawcy usługi EventSource w utrw](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
