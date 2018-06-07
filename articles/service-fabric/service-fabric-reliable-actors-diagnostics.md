---
title: Diagnostyka złośliwych użytkowników i monitorowanie | Dokumentacja firmy Microsoft
description: W tym artykule opisano diagnostyki i wydajności monitorowania funkcji w środowisku uruchomieniowym usługi sieć szkieletowa Reliable Actors, w tym zdarzenia i liczniki wydajności emitowane przez nią.
services: service-fabric
documentationcenter: .net
author: abhishekram
manager: timlt
editor: vturecek
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: 03c8bf129bcb8bf1b8af4bb7d189f1a38ba8f503
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809094"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnostyka i monitorowanie wydajności struktury Reliable Actors
Środowisko uruchomieniowe Reliable Actors emituje [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) zdarzeń i [liczniki wydajności](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Te zapewniają wgląd w działania środowiska uruchomieniowego i ułatwić rozwiązywanie problemów i monitorowania wydajności.

## <a name="eventsource-events"></a>Element EventSource zdarzenia
Nazwa dostawcy EventSource środowiska uruchomieniowego Reliable Actors jest "Microsoft-ServiceFabric uczestników". Zdarzenia z źródłem tego zdarzenia wyświetlane w [zdarzenia diagnostyczne](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) okna, gdy aplikacja aktora jest [debugowania w programie Visual Studio](service-fabric-debugging-your-application.md).

Narzędzia i technologie, które pomagają w zbierania i/lub wyświetlanie zdarzeń EventSource przykłady [narzędzia PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [diagnostyki Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [semantycznego rejestrowania](https://msdn.microsoft.com/library/dn774980.aspx)i [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Słowa kluczowe
Wszystkie zdarzenia, które należą do niezawodnej EventSource złośliwych użytkowników są skojarzone z jedną lub kilka słów kluczowych. Dzięki temu filtrowania zdarzeń, które są zbierane. Zdefiniowano następujące bitów — słowo kluczowe.

| bitowe | Opis |
| --- | --- |
| 0x1 |Zestaw ważnych wydarzeń, które Podsumuj operacji środowiska uruchomieniowego złośliwych użytkowników sieci szkieletowej. |
| 0x2 |Zestaw zdarzenia, które opisują wywołania metody aktora. Aby uzyskać więcej informacji, zobacz [wprowadzające temat podmiotów](service-fabric-reliable-actors-introduction.md). |
| 0x4 |Zestaw zdarzeń związanych z stanu aktora. Aby uzyskać więcej informacji, zobacz temat na [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Zestaw zdarzeń związanych z systemem Włącz współbieżność w aktora. Aby uzyskać więcej informacji, zobacz temat na [współbieżności](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe Reliable Actors definiuje następujące kategorii licznika wydajności.

| Kategoria | Opis |
| --- | --- |
| Aktor usługi Service Fabric |Liczniki specyficzne dla usługi Azure Service Fabric uczestników, np. czasu podjąć w celu zapisania stanu aktora |
| Metoda aktora usługi Service Fabric |Liczniki specyficzne dla metod zaimplementowanych przez złośliwych użytkowników usługi Service Fabric, np. częstotliwość aktora jest wywoływana metoda |

Każdy z powyższych kategorii ma co najmniej jeden licznik.

[Monitor wydajności systemu Windows](https://technet.microsoft.com/library/cc749249.aspx) aplikacji, która jest dostępna domyślnie w systemie operacyjnym Windows może służyć do zbierania i przeglądać dane liczników wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) jest inną opcją w przypadku zbierania danych licznika wydajności i przekazać go do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień liczników wydajności
Klaster ma dużą liczbę usług aktora lub partycji usługi aktora ma dużą liczbę wystąpień liczników wydajności aktora. Nazwy wystąpień liczników wydajności mogą pomóc w określeniu konkretnym [partycji](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) i metoda aktora (jeśli dotyczy) skojarzonego z wystąpienia licznika wydajności.

#### <a name="service-fabric-actor-category"></a>Kategoria aktora sieci szkieletowej usług
Dla kategorii `Service Fabric Actor`, nazwy wystąpień liczników znajdują się w następującym formacie:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* jest reprezentację ciągu Identyfikatora partycji usługi sieć szkieletowa skojarzonego z wystąpienia licznika wydajności. Identyfikator partycji jest identyfikatorem GUID i reprezentacji ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody z specyfikator formatu "D".

*ActorRuntimeInternalID* jest reprezentację ciągu 64-bitową liczbę całkowitą, generowany przez środowisko uruchomieniowe sieci szkieletowej podmiotów użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jego unikatowości i uniknąć konfliktu z innym nazwy wystąpień liczników wydajności. Użytkownicy nie należy próbować zinterpretować tej części nazwę wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwę wystąpienia licznika dla licznika, który należy do `Service Fabric Actor` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

W powyższym przykładzie `2740af29-78aa-44bc-a20b-7e60fb783264` jest reprezentację ciągu Identyfikatora partycji usługi Service Fabric i `635650083799324046` za pomocą Identyfikatora 64-bitowych, generowany dla wewnętrznego obiektu środowiska wykonawczego.

#### <a name="service-fabric-actor-method-category"></a>Kategoria metoda aktora sieci szkieletowej usług
Dla kategorii `Service Fabric Actor Method`, nazwy wystąpień liczników znajdują się w następującym formacie:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* jest nazwą metody aktora skojarzonego z wystąpienia licznika wydajności. Format nazwy metody jest określany na podstawie na logikę w środowisku wykonawczym złośliwych użytkowników sieci szkieletowej, który równoważy czytelność nazwy z ograniczeń maksymalna długość nazwy wystąpienia licznika wydajności w systemie Windows.

*ActorsRuntimeMethodId* jest reprezentację ciągu 32-bitową liczbę całkowitą, generowany przez środowisko uruchomieniowe sieci szkieletowej podmiotów użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jego unikatowości i uniknąć konfliktu z innym nazwy wystąpień liczników wydajności. Użytkownicy nie należy próbować zinterpretować tej części nazwę wystąpienia licznika wydajności.

*ServiceFabricPartitionID* jest reprezentację ciągu Identyfikatora partycji usługi sieć szkieletowa skojarzonego z wystąpienia licznika wydajności. Identyfikator partycji jest identyfikatorem GUID i reprezentacji ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody z specyfikator formatu "D".

*ActorRuntimeInternalID* jest reprezentację ciągu 64-bitową liczbę całkowitą, generowany przez środowisko uruchomieniowe sieci szkieletowej podmiotów użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jego unikatowości i uniknąć konfliktu z innym nazwy wystąpień liczników wydajności. Użytkownicy nie należy próbować zinterpretować tej części nazwę wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwę wystąpienia licznika dla licznika, który należy do `Service Fabric Actor Method` kategorii:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

W powyższym przykładzie `ivoicemailboxactor.leavemessageasync` jest nazwą metody `2` jest Identyfikatorem 32-bitowych wygenerowane do użytku wewnętrznego w czasie wykonywania, `89383d32-e57e-4a9b-a6ad-57c6792aa521` jest reprezentację ciągu Identyfikatora partycji usługi sieć szkieletowa usług i `635650083804480486` za pomocą Identyfikatora 64-bitowych generowane podczas wewnętrzny środowiska uruchomieniowego.

## <a name="list-of-events-and-performance-counters"></a>Lista zdarzeń i liczników wydajności
### <a name="actor-method-events-and-performance-counters"></a>Aktora metody zdarzenia i liczniki wydajności
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [metody aktora](service-fabric-reliable-actors-introduction.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Pełne |0x2 |Środowisko uruchomieniowe złośliwych użytkowników jest wywołanie metody aktora. |
| ActorMethodStop |8 |Pełne |0x2 |Metoda aktora zakończono wykonywanie. To, że zwrócił środowiska uruchomieniowego asynchroniczne wywołanie metody aktora i ukończeniu zadania zwracany przez metodę aktora. |
| ActorMethodThrewException |9 |Ostrzeżenie |0x3 |Wystąpił wyjątek podczas wykonywania metody aktora podczas wywołania asynchronicznego środowiska uruchomieniowego do metody aktora lub podczas wykonywania zadania zwracany przez metodę aktora. To zdarzenie oznacza jakiegoś awarii w kodzie aktora, który wymaga badania. |

Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności związane z uruchamianiem metody aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Metoda aktora usługi Service Fabric |Wywołania/s |Ile razy wywołaniu metody usługi aktora na sekundę |
| Metoda aktora usługi Service Fabric |Średnia liczba milisekund dla wywołania |Czas wykonania metody usługi aktora w milisekundach |
| Metoda aktora usługi Service Fabric |Zgłoszone wyjątki/s |Ile razy metody usługi aktora zwrócił wyjątek na sekundę |

### <a name="concurrency-events-and-performance-counters"></a>Współbieżność zdarzenia i liczniki wydajności
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [współbieżności](service-fabric-reliable-actors-introduction.md#concurrency).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Pełne |0x8 |To zdarzenie jest zapisywane na początku każdego nowego Włącz w aktora. Zawiera liczbę oczekujących wywołań aktora, które oczekują na uzyskanie blokady dla aktora, wymusza współbieżności opartej na włączanie. |

Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności powiązane z współbieżności.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Liczba wywołań aktora czekających na blokadę aktora |Liczba oczekujących wywołań aktora czekających na uzyskanie blokady dla aktora, wymusza współbieżności opartej na włączanie |
| Aktor usługi Service Fabric |Średni czas oczekiwania na blokadę (milisekundy) |Czas (w milisekundach) wymusza współbieżności opartej na włączanie blokady dla aktora |
| Aktor usługi Service Fabric |Średni czas utrzymania blokady aktora (milisekundy) |Czas (w milisekundach), dla której jest przechowywany blokady dla aktora |

### <a name="actor-state-management-events-and-performance-counters"></a>Liczniki wydajności i zdarzeń zarządzania stanu aktora
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Pełne |0x4 |Środowisko uruchomieniowe złośliwych użytkowników jest zapisania stanu aktora. |
| ActorSaveStateStop |11 |Pełne |0x4 |Środowisko uruchomieniowe podmiotów zakończył zapisywanie stanu aktora. |

Środowisko uruchomieniowe Reliable Actors publikuje następujących liczników wydajności związanych z zarządzaniem stanu aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Średnia liczba milisekund dla operacji zapisu stanu |Czas zapisania stanu aktora w milisekundach |
| Aktor usługi Service Fabric |Średni czas operacji ładowania stanu (milisekundy) |Czas ładowania stanu aktora w milisekundach |

### <a name="events-related-to-actor-replicas"></a>Zdarzenia związane z replik aktora
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [replik aktora](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informacyjne |0x1 |Repliki aktora zmienić roli do podstawowych. Oznacza to, że zostaną utworzone złośliwych użytkowników dla tej partycji wewnątrz tej repliki. |
| ReplicaChangeRoleFromPrimary |2 |Informacyjne |0x1 |Repliki aktora zmienić roli do innej niż podstawowa. Oznacza to, że złośliwych użytkowników dla tej partycji nie można utworzyć wewnątrz tej repliki. Żadne żądania nie zostaną dostarczone do podmiotów już utworzone w ramach tej repliki. Podmioty zostaną usunięte po ukończeniu wszystkich żądań w toku. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Aktora Aktywacja i dezaktywacja zdarzenia i liczniki wydajności
Środowisko uruchomieniowe Reliable Actors emituje następujące zdarzenia związane z [aktora Aktywacja i dezaktywacja](service-fabric-reliable-actors-lifecycle.md).

| Nazwa zdarzenia | Identyfikator zdarzenia | Poziom | Słowo kluczowe | Opis |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informacyjne |0x1 |Uaktywnieniu aktora. |
| ActorDeactivated |6 |Informacyjne |0x1 |Dezaktywowano aktora. |

Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności powiązane z aktorem Aktywacja i dezaktywacja.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Średni czas dla metody OnActivateAsync (milisekundy) |Czas wykonania metody OnActivateAsync w milisekundach |

### <a name="actor-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądania aktora
Gdy klient wywołuje metodę za pośrednictwem obiektu proxy aktora, wynikiem komunikat żądania wysyłane przez sieć do usługi aktora. Usługa przetwarza komunikat żądania i wysyła odpowiedź do klienta. Środowisko uruchomieniowe Reliable Actors publikuje następujące liczniki wydajności powiązane z przetwarzania żądania aktora.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Aktor usługi Service Fabric |Liczba oczekujących żądań |Liczba żądań przetwarzanych przez usługę |
| Aktor usługi Service Fabric |Średni czas na żądanie (milisekundy) |Czas (w milisekundach przez usługę do przetwarzania żądań) |
| Aktor usługi Service Fabric |Średni czas deserializacji żądania (milisekundy) |Czas (w milisekundach) do deserializacji komunikatu żądania aktora po odebraniu przez usługę |
| Aktor usługi Service Fabric |Średni czas serializacji odpowiedzi (milisekundy) |Czas (w milisekundach) do serializacji komunikatu odpowiedzi aktora w usłudze przed wysłaniem odpowiedzi do klienta |

## <a name="next-steps"></a>Kolejne kroki
* [Jak używać platformy Service Fabric w Reliable Actors](service-fabric-reliable-actors-platform.md)
* [Dokumentację referencyjną aktora interfejsu API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Przykładowy kod](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Element EventSource dostawców w narzędzia PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
