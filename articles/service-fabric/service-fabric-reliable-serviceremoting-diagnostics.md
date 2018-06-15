---
title: ServiceFabric diagnostyki Azure i monitorowanie | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje monitorowania wydajności w środowisku wykonawczym ServiceRemoting niezawodnej sieci szkieletowej usług takich jak liczniki wydajności emitowane przez nią.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: timlt
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: d462ba0955a362c27b786ee6a5670eec20c52a22
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206423"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostyka i monitorowanie wydajności dla niezawodnego Remoting Service
Emituje środowiska uruchomieniowego niezawodnej ServiceRemoting [liczniki wydajności](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Te zapewniają wgląd w działania ServiceRemoting i ułatwić rozwiązywanie problemów i monitorowania wydajności.


## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe ServiceRemoting niezawodnej definiuje następujące kategorii licznika wydajności:

| Kategoria | Opis |
| --- | --- |
| Usługa Service Fabric |Liczniki specyficzne dla usługi Azure Service Fabric Service Remoting, na przykład, Średni czas przetwarzania żądania |
| Metoda usługi Service Fabric |Liczniki specyficzne dla metody implementowane przez usługi sieć szkieletowa usług zdalnych, na przykład, jak często jest wywoływana metoda usługi |

Każdy z powyższych kategoriach ma co najmniej jeden licznik.

[Monitor wydajności systemu Windows](https://technet.microsoft.com/library/cc749249.aspx) aplikacji, która jest dostępna domyślnie w systemie operacyjnym Windows może służyć do zbierania i przeglądać dane liczników wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) jest inną opcją w przypadku zbierania danych licznika wydajności i przekazać go do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpień liczników wydajności
Klaster ma dużą liczbę usług ServiceRemoting lub partycje ma dużą liczbę wystąpień liczników wydajności. Nazwy wystąpień liczników wydajności mogą pomóc w zidentyfikowaniu określonej partycji i metody usługi (jeśli dotyczy) czy wystąpienie licznika wydajności jest skojarzony z.

#### <a name="service-fabric-service-category"></a>Kategoria Usługa sieci szkieletowej usług
Dla kategorii `Service Fabric Service`, nazwy wystąpień liczników znajdują się w następującym formacie:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* jest reprezentację ciągu Identyfikatora partycji usługi sieć szkieletowa skojarzonego z wystąpienia licznika wydajności. Identyfikator partycji jest identyfikatorem GUID i reprezentacji ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody z specyfikator formatu "D".

*ServiceReplicaOrInstanceId* jest reprezentację ciągu identyfikator repliki i wystąpienia usługi sieci szkieletowej skojarzonego z wystąpienia licznika wydajności.

*ServiceRuntimeInternalID* jest reprezentację ciągu 64-bitową liczbę całkowitą, generowany przez środowisko uruchomieniowe usługi sieć szkieletowa użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jego unikatowości i uniknąć konfliktu z innym nazwy wystąpień liczników wydajności. Użytkownicy nie należy próbować zinterpretować tej części nazwę wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwę wystąpienia licznika dla licznika, który należy do `Service Fabric Service` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

W poprzednim przykładzie `2740af29-78aa-44bc-a20b-7e60fb783264` reprezentację ciągu Identyfikatora partycji usługi Service Fabric, jest `635650083799324046` jest reprezentację ciągu repliki/InstanceId i `5008379932` za pomocą Identyfikatora 64-bitowych, generowany dla wewnętrznego obiektu środowiska wykonawczego.

#### <a name="service-fabric-service-method-category"></a>Kategoria metody usługi sieci szkieletowej usług
Dla kategorii `Service Fabric Service Method`, nazwy wystąpień liczników znajdują się w następującym formacie:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* jest nazwą metody usługi skojarzonego z wystąpienia licznika wydajności. Format nazwy metody jest określany na podstawie na logikę w środowisku uruchomieniowym usługi sieć szkieletowa, który równoważy czytelność nazwy z ograniczeń maksymalna długość nazwy wystąpienia licznika wydajności w systemie Windows.

*ServiceRuntimeMethodId* jest reprezentację ciągu 32-bitową liczbę całkowitą, generowany przez środowisko uruchomieniowe usługi sieć szkieletowa użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jego unikatowości i uniknąć konfliktu z innym nazwy wystąpień liczników wydajności. Użytkownicy nie należy próbować zinterpretować tej części nazwę wystąpienia licznika wydajności.

*ServiceFabricPartitionID* jest reprezentację ciągu Identyfikatora partycji usługi sieć szkieletowa skojarzonego z wystąpienia licznika wydajności. Identyfikator partycji jest identyfikatorem GUID i reprezentacji ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody z specyfikator formatu "D".

*ServiceReplicaOrInstanceId* jest reprezentację ciągu identyfikator repliki i wystąpienia usługi sieci szkieletowej skojarzonego z wystąpienia licznika wydajności.

*ServiceRuntimeInternalID* jest reprezentację ciągu 64-bitową liczbę całkowitą, generowany przez środowisko uruchomieniowe usługi sieć szkieletowa użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jego unikatowości i uniknąć konfliktu z innym nazwy wystąpień liczników wydajności. Użytkownicy nie należy próbować zinterpretować tej części nazwę wystąpienia licznika wydajności.

Poniżej przedstawiono przykład nazwę wystąpienia licznika dla licznika, który należy do `Service Fabric Service Method` kategorii:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

W poprzednim przykładzie `ivoicemailboxservice.leavemessageasync` jest nazwą metody `2` jest Identyfikatorem 32-bitowych wygenerowane do użytku wewnętrznego w czasie wykonywania, `89383d32-e57e-4a9b-a6ad-57c6792aa521` reprezentację ciągu Identyfikatora partycji usługi Service Fabric, jest`635650083804480486` jest reprezentację ciągu identyfikator repliki i wystąpienia usługi sieci szkieletowej i `5008380` za pomocą Identyfikatora 64-bitowych generowane podczas wewnętrzny środowiska uruchomieniowego.

## <a name="list-of-performance-counters"></a>Lista liczników wydajności
### <a name="service-method-performance-counters"></a>Liczniki wydajności usług — metoda

Środowisko uruchomieniowe niezawodnej usługi publikuje następujące liczniki wydajności związane z uruchamianiem usługi metod.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Metoda usługi Service Fabric |Wywołania/s |Ile razy na sekundę wywoływana jest metoda usługi |
| Metoda usługi Service Fabric |Średnia liczba milisekund dla wywołania |Czas wykonania metody usługi (w milisekundach) |
| Metoda usługi Service Fabric |Zgłoszone wyjątki/s |Ile razy czy metody usługi zwrócił wyjątek na sekundę |

### <a name="service-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądania usługi
Gdy klient wywołuje metodę za pośrednictwem obiektu serwera proxy usługi, wynikiem są wysyłane za pośrednictwem sieci, aby usługa zdalnego komunikat żądania. Usługa przetwarza komunikat żądania i wysyła odpowiedź do klienta. Środowisko uruchomieniowe ServiceRemoting niezawodnej publikuje następujące liczniki wydajności powiązane z przetwarzania żądania usługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Usługa Service Fabric |Liczba oczekujących żądań |Liczba żądań przetwarzanych przez usługę |
| Usługa Service Fabric |Średni czas na żądanie (milisekundy) |Czas (w milisekundach przez usługę do przetwarzania żądań) |
| Usługa Service Fabric |Średni czas deserializacji żądania (milisekundy) |Czas (w milisekundach) do deserializacji komunikatu żądania usługi po odebraniu przez usługę |
| Usługa Service Fabric |Średni czas serializacji odpowiedzi (milisekundy) |Czas (w milisekundach) do serializacji komunikatu odpowiedzi usług w usłudze przed wysłaniem odpowiedzi do klienta |

## <a name="next-steps"></a>Kolejne kroki
* [Przykładowy kod](https://github.com/Azure/servicefabric-samples)
* [Element EventSource dostawców w narzędzia PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
