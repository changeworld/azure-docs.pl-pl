---
title: Diagnostyka i monitorowanie usługi Azure ServiceFabric
description: W tym artykule opisano funkcje monitorowania wydajności w czasie wykonywania niezawodnej usługi usługi UsługiZamykanie, takie jak liczniki wydajności emitowane przez niego.
author: suchiagicha
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: pepogors
ms.openlocfilehash: 31095a619fc4d756fa4ef9c29691d1d511d59ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282278"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostyka i monitorowanie wydajności w celu niezawodnej komunikacji serwisowej
Środowisko wykonawcze Reliable ServiceRemoting emituje [liczniki wydajności.](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx) Zapewniają one wgląd w sposób serviceremoting działa i pomoc w rozwiązywaniu problemów i monitorowania wydajności.


## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe Reliable ServiceRemoting definiuje następujące kategorie liczników wydajności:

| Kategoria | Opis |
| --- | --- |
| Usługa sieci szkieletowej usług |Liczniki specyficzne dla usługi Azure Service Fabric Service Service Remoting , na przykład średni czas przetwarzania żądania |
| Metoda usługi sieci szkieletowej usług |Liczniki specyficzne dla metod zaimplementowanych przez usługę komunikacji zdalnej sieci szkieletowej usług, na przykład, jak często wywoływana jest metoda usługi |

Każda z powyższych kategorii ma jeden lub więcej liczników.

Aplikacja [Monitor wydajności systemu Windows,](https://technet.microsoft.com/library/cc749249.aspx) która jest domyślnie dostępna w systemie operacyjnym Windows, może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka platformy Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) to kolejna opcja do zbierania danych licznika wydajności i przekazywania ich do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień licznika wydajności
Klaster, który ma dużą liczbę usługRekontowanie usług lub partycji mają dużą liczbę wystąpień licznika wydajności. Nazwy wystąpień licznika wydajności może pomóc w identyfikacji określonej partycji i service metody (jeśli dotyczy), że wystąpienie licznika wydajności jest skojarzony z.

#### <a name="service-fabric-service-category"></a>Kategoria usługi sieci szkieletowej usług
W przypadku `Service Fabric Service`kategorii nazwy wystąpień licznika są w następującym formacie:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* jest reprezentacją ciągu identyfikatora partycji sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) reprezentacja ciągu jest generowana za pomocą metody z specyfikatorem formatu "D".

*Identyfikator InstanceId usługi Jest* reprezentacją ciągu identyfikatora repliki/wystąpienia sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności.

*ServiceRuntimeInternalID* jest reprezentacją ciągu 64-bitowej liczby całkowitej, która jest generowana przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Jest to zawarte w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi nazwami wystąpień licznika wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Service` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

W poprzednim przykładzie `2740af29-78aa-44bc-a20b-7e60fb783264` jest reprezentacja ciągu identyfikatora partycji sieci szkieletowej `635650083799324046` usług, `5008379932` jest reprezentacją ciągu Replika/InstanceId i jest 64-bitowym identyfikatorem, który jest generowany do użytku wewnętrznego środowiska wykonawczego.

#### <a name="service-fabric-service-method-category"></a>Kategoria metody usługi sieci szkieletowej usług
W przypadku `Service Fabric Service Method`kategorii nazwy wystąpień licznika są w następującym formacie:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* to nazwa metody usługi, z którą jest skojarzone wystąpienie licznika wydajności. Format nazwy metody jest określany na podstawie pewnej logiki w czasie wykonywania usługi sieci szkieletowej, która równoważy czytelność nazwy z ograniczeniami dotyczącymi maksymalnej długości nazw wystąpień licznika wydajności w systemie Windows.

*ServiceRuntimeMethodId* jest reprezentacją ciągu 32-bitowej liczby całkowitej, która jest generowana przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Jest to zawarte w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi nazwami wystąpień licznika wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

*ServiceFabricPartitionID* jest reprezentacją ciągu identyfikatora partycji sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności. Identyfikator partycji jest identyfikatorem GUID, a jego [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) reprezentacja ciągu jest generowana za pomocą metody z specyfikatorem formatu "D".

*Identyfikator InstanceId usługi Jest* reprezentacją ciągu identyfikatora repliki/wystąpienia sieci szkieletowej usług, z którą jest skojarzone wystąpienie licznika wydajności.

*ServiceRuntimeInternalID* jest reprezentacją ciągu 64-bitowej liczby całkowitej, która jest generowana przez środowisko uruchomieniowe usługi sieci szkieletowej do użytku wewnętrznego. Jest to zawarte w nazwie wystąpienia licznika wydajności, aby zapewnić jego unikatowość i uniknąć konfliktu z innymi nazwami wystąpień licznika wydajności. Użytkownicy nie powinni próbować interpretować tej części nazwy wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwy wystąpienia licznika dla licznika, który należy do `Service Fabric Service Method` kategorii:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

W poprzednim przykładzie `ivoicemailboxservice.leavemessageasync` jest nazwą `2` metody, jest 32-bitowy identyfikator generowany dla użytku `89383d32-e57e-4a9b-a6ad-57c6792aa521` wewnętrznego środowiska wykonawczego, jest reprezentacją ciągu identyfikator partycji sieci szkieletowej`635650083804480486` `5008380` usług, jest reprezentacją ciągu repliki/wystąpienia sieci szkieletowej usług i jest identyfikatorem 64-bitowym generowanym do użytku wewnętrznego środowiska wykonawczego.

## <a name="list-of-performance-counters"></a>Lista liczników wydajności
### <a name="service-method-performance-counters"></a>Liczniki wydajności metody serwisowej

Środowisko uruchomieniowe niezawodnej usługi publikuje następujące liczniki wydajności związane z wykonywaniem metod usługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Metoda usługi sieci szkieletowej usług |Wywołania/s |Liczba wywołań metody usługi na sekundę |
| Metoda usługi sieci szkieletowej usług |Średnia milisekundy na wywołanie |Czas wykonania metody usługi w milisekundach |
| Metoda usługi sieci szkieletowej usług |Wyjątki generowane/s |Liczba wyświetlek, w których metoda usługi zgłosiła wyjątek na sekundę |

### <a name="service-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądań serwisowych
Gdy klient wywołuje metodę za pośrednictwem obiektu serwera proxy usługi, powoduje to wysłanie komunikatu żądania za pośrednictwem sieci do usługi komunikacji zdalnej. Usługa przetwarza komunikat żądania i wysyła odpowiedź z powrotem do klienta. Środowisko wykonawcze Niezawodne usługiRemoting publikuje następujące liczniki wydajności związane z przetwarzaniem żądań usługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Usługa sieci szkieletowej usług |Liczba zaległych wniosków |Liczba żądań przetwarzanych w usłudze |
| Usługa sieci szkieletowej usług |Średnia milisekundy na żądanie |Czas przejmujeny przez usługę (w milisekundach) na przetworzenie żądania |
| Usługa sieci szkieletowej usług |Średnia milisekundy dla deserializacji żądania |Czas (w milisekundach) na deserializację komunikatu żądania usługi po odebraniu go w usłudze |
| Usługa sieci szkieletowej usług |Średnia milisekundy dla serializacji odpowiedzi |Czas potrzebny (w milisekundach) na serializację komunikatu odpowiedzi usługi w usłudze przed wysłaniem odpowiedzi do klienta |

## <a name="next-steps"></a>Następne kroki
* [Przykładowy kod](https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0)
* [Dostawcy usługi EventSource w utrw](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
