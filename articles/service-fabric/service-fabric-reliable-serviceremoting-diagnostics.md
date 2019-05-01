---
title: Usługa Azure ServiceFabric Diagnostyka i monitorowanie | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje monitorowania wydajności w środowisku uruchomieniowym usługi Service Fabric Reliable ServiceRemoting, takich jak liczniki wydajności emitowane przez nią.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: suchiagicha
ms.assetid: 1c229923-670a-4634-ad59-468ff781ad18
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: suchiagicha
ms.openlocfilehash: e4c698dc5aa8a07d09835adeac39db6ee35f720c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123182"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-service-remoting"></a>Diagnostyka i monitorowanie wydajności dla wiarygodnych zdalna komunikacja usług
Emituje ServiceRemoting niezawodne środowisko uruchomieniowe [liczniki wydajności](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Te zapewniają wgląd w jaki sposób działa ServiceRemoting i ułatwić rozwiązywanie problemów i monitorowania wydajności.


## <a name="performance-counters"></a>Liczniki wydajności
Środowisko uruchomieniowe niezawodne ServiceRemoting definiuje następujące kategorie liczników wydajności:

| Category | Opis |
| --- | --- |
| Usługa Service Fabric |Liczniki specyficzne dla usługi Azure Service Fabric zdalna komunikacja usług, na przykład średni czas przetwarzania żądania |
| Metoda usługi Service Fabric |Liczniki specyficzne dla metody implementowane przez usługi Service Fabric wywołaniem funkcji zdalnych, na przykład, jak często jest wywoływana metoda usługi |

Każda z powyższych kategorii ma co najmniej jeden licznik.

[Windows Performance Monitor](https://technet.microsoft.com/library/cc749249.aspx) aplikację, która jest dostępna domyślnie w systemie operacyjnym Windows może służyć do zbierania i wyświetlania danych licznika wydajności. [Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) to kolejna opcja umożliwiająca zbieranie danych licznika wydajności i przekazać go do tabel platformy Azure.

### <a name="performance-counter-instance-names"></a>Nazwy wystąpienia licznika wydajności
Klastra, który ma dużą liczbę usług ServiceRemoting lub partycje mają dużą liczbę wystąpień liczników wydajności. Nazwy wystąpienia licznika wydajności może pomóc w identyfikacji określonej partycji i metody usługi (jeśli dotyczy), wystąpienie licznika wydajności jest skojarzony.

#### <a name="service-fabric-service-category"></a>Kategoria usługi Service Fabric
Dla kategorii `Service Fabric Service`, nazwy wystąpienia licznika znajdują się w następującym formacie:

`ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*ServiceFabricPartitionID* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora partycji usługi Service Fabric. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentację ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody przy użyciu specyfikatora formatu "D".

*ServiceReplicaOrInstanceId* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora repliki i wystąpienia usługi Service Fabric.

*ServiceRuntimeInternalID* jest ciąg reprezentujący 64-bitową liczbę całkowitą, który jest generowany przez środowisko uruchomieniowe usługi Service Fabric użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi nazwy wystąpienia licznika wydajności. Użytkownicy nie należy próbować interpretacji ta część nazwę wystąpienia licznika wydajności.

Oto przykład nazwę wystąpienia licznika licznika, który należy do `Service Fabric Service` kategorii:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046_5008379932`

W powyższym przykładzie `2740af29-78aa-44bc-a20b-7e60fb783264` jest ciąg reprezentujący identyfikator partycji usługi Service Fabric `635650083799324046` jest reprezentacją ciągu repliki/InstanceId i `5008379932` jest identyfikator 64-bitowym, który jest generowany dla wewnętrznego środowiska uruchomieniowego.

#### <a name="service-fabric-service-method-category"></a>Metoda usługi Service Fabric kategorii
Dla kategorii `Service Fabric Service Method`, nazwy wystąpienia licznika znajdują się w następującym formacie:

`MethodName_ServiceRuntimeMethodId_ServiceFabricPartitionID_ServiceReplicaOrInstanceId_ServiceRuntimeInternalID`

*MethodName* jest nazwą metody usługi, która wystąpienie licznika wydajności jest skojarzony. Format nazwy metody jest określana w oparciu o logikę w środowisku uruchomieniowym usługi Service Fabric, która równoważy czytelność nazwy z ograniczeniami na maksymalną długość nazwy wystąpienia licznika wydajności w Windows.

*ServiceRuntimeMethodId* jest ciąg reprezentujący 32-bitową liczbę całkowitą, który jest generowany przez środowisko uruchomieniowe usługi Service Fabric użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi nazwy wystąpienia licznika wydajności. Użytkownicy nie należy próbować interpretacji ta część nazwę wystąpienia licznika wydajności.

*ServiceFabricPartitionID* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora partycji usługi Service Fabric. Identyfikator partycji jest identyfikatorem GUID, a jego reprezentację ciągu jest generowany przez [ `Guid.ToString` ](https://msdn.microsoft.com/library/97af8hh4.aspx) metody przy użyciu specyfikatora formatu "D".

*ServiceReplicaOrInstanceId* jest ciąg reprezentujący wystąpienie licznika wydajności jest skojarzony z identyfikatora repliki i wystąpienia usługi Service Fabric.

*ServiceRuntimeInternalID* jest ciąg reprezentujący 64-bitową liczbę całkowitą, który jest generowany przez środowisko uruchomieniowe usługi Service Fabric użytku wewnętrznego. To jest objęte nazwę wystąpienia licznika wydajności w celu zapewnienia jej unikatowości i uniknąć konfliktów z innymi nazwy wystąpienia licznika wydajności. Użytkownicy nie należy próbować interpretacji ta część nazwę wystąpienia licznika wydajności.

Oto przykład nazwę wystąpienia licznika licznika, który należy do `Service Fabric Service Method` kategorii:

`ivoicemailboxservice.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486_5008380`

W powyższym przykładzie `ivoicemailboxservice.leavemessageasync` jest nazwa metody `2` jest Identyfikatorem 32-bitowych wygenerowane do użytku wewnętrznego w środowisku uruchomieniowym, `89383d32-e57e-4a9b-a6ad-57c6792aa521` jest ciąg reprezentujący identyfikator partycji usługi Service Fabric`635650083804480486` jest reprezentacją ciągu Identyfikator repliki i wystąpienia usługi Service Fabric i `5008380` jest generowany dla czasu wykonywania wewnętrznego identyfikator 64-bitowych.

## <a name="list-of-performance-counters"></a>Lista liczników wydajności
### <a name="service-method-performance-counters"></a>Liczniki wydajności usługi Service — metoda

Środowisko uruchomieniowe usługi Reliable Service publikuje następujące liczniki wydajności, związane z uruchamianiem usługi metody.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Metoda usługi Service Fabric |Wywołania/s |Liczba prób wywołaniu metody usługi na sekundę |
| Metoda usługi Service Fabric |Średnia liczba milisekund dla wywołania |Czas wykonania metody usługi w milisekundach |
| Metoda usługi Service Fabric |Zgłoszone wyjątki/s |Liczba przypadków, metoda usługi zgłosił wyjątek na sekundę |

### <a name="service-request-processing-performance-counters"></a>Liczniki wydajności przetwarzania żądania usługi
Gdy klient wywołuje metodę za pośrednictwem obiektu serwera proxy usługi, wynikiem komunikat żądania wysyłane za pośrednictwem sieci z usługą komunikacji zdalnej. Usługa przetwarza komunikat żądania i wysyła odpowiedź z powrotem do klienta. Środowisko uruchomieniowe niezawodne ServiceRemoting publikuje następujące liczniki wydajności powiązane z przetwarzania żądania usługi.

| Nazwa kategorii | Nazwa licznika | Opis |
| --- | --- | --- |
| Usługa Service Fabric |Liczba oczekujących żądań |Liczba żądań przetwarzanych w usłudze |
| Usługa Service Fabric |Średni czas na żądanie (milisekundy) |Czas (w milisekundach) przez usługę do przetwarzania żądań |
| Usługa Service Fabric |Średni czas deserializacji żądania (milisekundy) |Czas trwania (w milisekundach) do deserializacji komunikatu żądania usługi, po odebraniu na usługę |
| Usługa Service Fabric |Średni czas serializacji odpowiedzi (milisekundy) |Czas trwania (w milisekundach), można serializować komunikatu odpowiedzi usługi na usługę przed odpowiedź jest wysyłana do klienta |

## <a name="next-steps"></a>Kolejne kroki
* [Przykładowy kod](https://azure.microsoft.com/en-us/resources/samples/?service=service-fabric&sort=0)
* [EventSource dostawców w PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
