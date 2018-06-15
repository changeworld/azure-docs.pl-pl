---
title: 'Kontroli: Komunikacja usługi | Dokumentacja firmy Microsoft'
description: Usługi do komunikacji jest punktem krytyczne integracji aplikacji sieci szkieletowej usług. W tym artykule omówiono zagadnienia dotyczące projektowania i testowania technik.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: e3ea537d310d49c934cf6789184f090791cf16a4
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34211229"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Scenariusze testowania usługi Service Fabric: komunikacji usługi
Powierzchnia zorientowane na usługę architektury style naturalnie w sieci szkieletowej usług Azure i Mikrousług. W tych typach rozproszonej architektury mikrousługi składnikowa aplikacji zwykle składają się z wielu usług, które muszą komunikować się ze sobą. W nawet sytuacjach najprostszym zazwyczaj masz co najmniej bezstanowych usługą sieci web i usługi magazynu danych stanowe potrzebne do komunikowania się.

Usługi do komunikacji jest punktem krytyczne integracji aplikacji, ponieważ każda usługa przedstawia zdalnego interfejsu API z innymi usługami. Praca z zestawu granice interfejsu API, które zwykle obejmuje we/wy wymaga niektórych opieki z dobrym ilością testowania i weryfikacji.

Istnieje wiele uwagi, aby podczas granice te usługi są połączone ze sobą w rozproszonym systemie:

* *Protokół transportu*. Maksymalnej przepływności zostanie użyty HTTP współdziałania zwiększone lub binarne protokołu niestandardowego?
* *Obsługa błędów*. Sposób trwałe i przejściowych błędów obsługi? Co się stanie, jeśli usługa jest przenoszony do innego węzła?
* *Limity czasu i opóźnienia*. W aplikacjach rozwiązania jak każdej warstwy usług obsłuży opóźnienia przez stos i dla użytkownika?

Czy użyć jednego ze składników komunikacji wbudowanej usługi udostępniane przez usługi sieć szkieletowa lub tworzenia, testowania interakcje między usług jest istotny dla zapewnienia odporności w aplikacji.

## <a name="prepare-for-services-to-move"></a>Przygotuj się do usługi, aby przenieść
Wystąpienie usługi może poruszanie się w czasie. Jest to szczególnie istotne, skonfigurowanymi metryki dostosowane niestandardowych zasobów optymalne równoważenia obciążenia. Sieć szkieletowa usług przenosi swoich wystąpień usługi, aby zmaksymalizować ich dostępności nawet podczas uaktualnienia, praca awaryjna skalowalnego w poziomie i innych sytuacjach, które występują w okresie istnienia systemu rozproszonego.

Jak usługi poruszanie się w klastrze, klientów i innych usług powinna być przygotowana do obsługi dwa scenariusze, gdy są one komunikować się z usługą:

* Repliki wystąpienia lub partycji usługi zostały przeniesione od czasu ostatniego zawsze mówię można do niego. Jest to normalne część cyklu życia usług i można się spodziewać się stać się przez cały okres istnienia aplikacji.
* Repliki wystąpienia lub partycji usługi jest w trakcie przenoszenia. Mimo że w sieci szkieletowej usług występuje bardzo szybko pracy awaryjnej usługi z jednego węzła do innego, mogą występować opóźnienia w dostępności Jeśli składnik komunikacji z usługą przebiega powoli rozpocząć.

Bezpiecznie obsługi tych scenariuszy jest ważna w przypadku systemu smooth uruchomiona. Aby to zrobić, należy pamiętać, że:

* Każda usługa, która jest połączona z ma *adres* nasłuchującej na (na przykład HTTP lub Websocket). Przenosi wystąpienie usługi lub partycję, zmienia jego adres punktu końcowego. (Powoduje przeniesienie do innego węzła, używając innego adresu IP.) Jeśli używasz składniki komunikacji wbudowanej one obsłuży ponowne rozpoznawania adresów usługi dla Ciebie.
* Prawdopodobnie wystąpił tymczasowy wzrost opóźnienia usługi podczas uruchamiania wystąpienia usługi się jego odbiornika ponownie. Zależy to od jak szybko usługi otwiera odbiornika po przeniesieniu wystąpienia usługi.
* Istniejących połączeń, należy zamknąć i otworzyć ponownie po usługi zostanie otwarty w nowym węźle. Bezpieczne węzła zamknięcia lub ponownego uruchomienia umożliwia czas dla istniejących połączeń, które można bezpiecznie zamknąć.

### <a name="test-it-move-service-instances"></a>Należy przeprowadzić test: Przenieś wystąpienia usługi
Za pomocą narzędzia do testowania usługi Service Fabric, można tworzyć scenariusza testu do przetestowania tych sytuacji na różne sposoby:

1. Przenieś usługi stanowej repliki podstawowej.
   
    Repliki podstawowej partycji usługi stanowej można przenieść dla dowolnej liczby powodów. Służy do docelowego repliką podstawową określonej partycji, aby zobaczyć, jak usługi reagują na przeniesienie w bardzo kontrolowany sposób.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zatrzymanie węzła.
   
    Po zatrzymaniu węzeł sieci szkieletowej usług przenosi wszystkie wystąpienia usługi lub partycje, które były w tym węźle do jednego z innych dostępnych węzłów w klastrze. Umożliwia testowanie sytuacji, gdy węzeł ma utracone z klastra i wszystkich wystąpień usługi i replik w tym węźle ma przenoszenia.
   
    Węzeł można wyłączyć przy użyciu programu PowerShell **Stop ServiceFabricNode** polecenia cmdlet:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Obsługa dostępność usługi
Jako platforma usługi sieć szkieletowa zaprojektowano w celu zapewnienia wysokiej dostępności usług. Ale w ekstremalnych przypadkach problemy związane z infrastrukturą podstawowej nadal powodują niedostępności. Należy przetestować w tych sytuacjach zbyt.

Usługi stanowej używać systemu kworum w celu replikowania stanu wysokiej dostępności. Oznacza to, że kworum replik musi być dostępna do wykonania operacji zapisu. W rzadkich przypadkach, takich jak awaria sprzętu powszechnie kworum replik nie mogą być dostępne. W takich przypadkach nie można wykonać operacji zapisu, ale nadal będzie mógł wykonywać operacji odczytu.

### <a name="test-it-write-operation-unavailability"></a>Należy przeprowadzić test: niedostępności operację zapisu
Przy użyciu narzędzia do testowania w sieci szkieletowej usług, można wstrzyknąć błędów, która powoduje utratę kworum jako test. Mimo że takiej sytuacji jest rzadko, ważne jest, klientów i usług, które są zależne od usługi stanowej są przygotowywane do obsługi sytuacji, gdy nie może podjąć żądań zapisu do niego. Ważne jest również czy samej usługi stanowej zna tej możliwości i bezpiecznie można przekazać do wywoływania.

Utrata kworum może wywołać przy użyciu programu PowerShell **Invoke ServiceFabricPartitionQuorumLoss** polecenia cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

W tym przykładzie ustawiliśmy `QuorumLossMode` do `QuorumReplicas` wskazująca chęć wywołać wyniku utraty kworum bez konieczności przełączania w dół wszystkich replik. W ten sposób operacje odczytu są nadal możliwe. Aby przetestować scenariusz, w którym cały partycji jest niedostępna, można ustawić ten przełącznik `AllReplicas`.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat testowania czynności](service-fabric-testability-actions.md)

[Dowiedz się więcej o scenariuszach kontroli](service-fabric-testability-scenarios.md)

