---
title: 'Testowalność: Usługa Komunikacja | Dokumentacja firmy Microsoft'
description: Komunikacja usług — jest punkt krytyczny integracji aplikacji usługi Service Fabric. W tym artykule omówiono zagadnienia dotyczące projektowania i testowania technik.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 017557df-fb59-4e4a-a65d-2732f29255b8
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 529c8d74b6e0a63a7969f31d5b5e8073ecb79411
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543227"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Scenariusze testowania usługi Service Fabric: Komunikacja usług
Powierzchnia stylami architektury zorientowanej na usługi w naturalny sposób w usłudze Azure Service Fabric i Mikrousług. W tego rodzaju rozproszonej architektury aplikacji mikrousług składającej składają się zazwyczaj z wielu usług, które muszą komunikować się ze sobą. W nawet najprostsza przypadków ogólnie masz co najmniej bezstanowej usługi internetowej i usługi magazynu danych stanowej, która muszą komunikować się.

Komunikacja usług — jest punkt krytyczny integracji aplikacji, ponieważ każda usługa udostępnia zdalny interfejs API z innymi usługami. Praca z zestaw granice interfejsu API, który obejmuje operacje We/Wy zwykle wymaga niektórych opieki, dobrym ilość testowania i sprawdzania poprawności.

Jest wiele istotnych kwestii, aby upewnić się, gdy granice te usługi są połączone ze sobą w rozproszonym systemie:

* *Protokół transportowy*. Będzie używana HTTP współdziałania w ramach zwiększone lub niestandardowego protokołu binarne maksymalną przepustowość?
* *Obsługa błędów*. Jak będą obsługiwane stałych i przejściowych błędów? Co się stanie, gdy usługa przejdzie do innego węzła?
* *Limity czasu i opóźnienia*. W aplikacjach wielowarstwowych jak każda warstwa usługi obsłuży opóźnienie stosu i użytkownika?

Czy używasz jeden ze składników komunikacji wbudowanej usługi, które są dostarczane przez usługę Service Fabric lub kompilowania, testowania interakcje między usługami jest mają kluczowe znaczenie dla zapewnienia odporności w aplikacji.

## <a name="prepare-for-services-to-move"></a>Przygotowanie do usług przenieść
Wystąpienia usługi może poruszania się wraz z upływem czasu. Jest to szczególnie istotne w przypadku, gdy są skonfigurowane przy użyciu metryk dostosowane do niestandardowych wymagań optymalnego rozmieszczenia zasobów równoważenia obciążenia. Usługa Service Fabric przenosi swoich wystąpień usługi, aby zmaksymalizować ich dostępności, nawet podczas uaktualnień, pracy w trybie Failover, skalowalnego w poziomie i innych sytuacjach, które występują w okresie istnienia systemu rozproszonego.

Ponieważ usługi poruszanie się w klastrze, klientów i innych usług powinna być przygotowana do obsługi dwa scenariusze, gdy one komunikować się z usługą:

* Repliki wystąpienia lub partycji usługi zostały przeniesione od czasu ostatniego, których mówił do niego. Jest to normalna część cyklu życia usługi. Ponadto należy się spodziewać, do wykonania w okresie istnienia aplikacji.
* Repliki wystąpienia lub partycji usługi jest w trakcie przenoszenia. Chociaż usługi z jednego węzła do drugiego przejścia do trybu failover bardzo szybko w usłudze Service Fabric, może wystąpić opóźnienie dostępności Jeśli składnik komunikacji z usługą odbywa się powoli, można uruchomić.

Bez problemu zmieniała obsługi tych scenariuszy jest ważne dla systemu smooth działa. Aby to zrobić, należy pamiętać, że:

* Do każdej usługi, które mogą być połączone z ma *adres* który nasłuchuje (na przykład HTTP lub gniazda Websocket). Kiedy przesuwa się wystąpienie usługi lub partycję, zmienia jego adres punktu końcowego. (Przesuwa się do innego węzła z innym adresem IP.) Jeśli używasz składniki komunikacji wbudowanej obsługiwanemu ponownego rozpoznawania adresów usługi dla Ciebie.
* Może wystąpić tymczasowy wzrost liczby opóźnienie usługi podczas uruchamiania wystąpienia usługi się jego odbiornika ponownie. Zależy to od jak szybko usługi otwiera odbiornik po przeniesieniu wystąpienia usługi.
* Istniejące połączenia muszą być zamknięte i otwarte ponownie po usługi zostanie otwarty w nowym węźle. Węzeł łagodne zamykanie lub ponowne uruchomienie umożliwia czas prawidłowe zamknięcie istniejącego połączenia.

### <a name="test-it-move-service-instances"></a>Przetestuj ją: Przenoszenie wystąpienia usługi
Za pomocą narzędzia do testowania usługi Service Fabric, możesz tworzyć scenariusza testu, aby przetestować tych sytuacji na różne sposoby:

1. Przenieś replika podstawowa usługi stanowej.
   
    Dla dowolnej liczby przyczyny można przenieść podstawową replikę partycji usługi stanowej. Użyj tego pod kątem repliką podstawową określonej partycji, aby zobaczyć, jak usługi reagować na przeniesienie w bardzo kontrolowany sposób.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zatrzymanie węzła.
   
    Po zatrzymaniu węzła usługi Service Fabric przenosi wszystkie wystąpienia usługi lub partycje, które były w tym węźle do jednej z dostępnych węzłów w klastrze. Służy do testowania w sytuacji, gdy węzeł zostanie utracony z klastra i wszystkich wystąpień usługi i replik w tym węźle się okazać konieczne przeniesienie.
   
    Węzeł można zatrzymać za pomocą programu PowerShell **Stop-ServiceFabricNode** polecenia cmdlet:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Obsługa dostępności usługi
To platforma usługi Service Fabric jest zaprojektowany w celu zapewnienia wysokiej dostępności usługi. Ale w skrajnych przypadkach podstawowych problemów związanych z infrastrukturą nadal może spowodować niedostępność. Należy przetestować dla tych scenariuszy za.

Usługi stanowe za pomocą systemu kworum replikować stanu w celu zapewnienia wysokiej dostępności. Oznacza to, że kworum replik musi być dostępny, aby wykonać operacje zapisu. W rzadkich przypadkach, takich jak awarii sprzętu powszechne kworum replik może nie być dostępne. W takich przypadkach nie można wykonać operacji zapisu, ale nadal będzie można przeprowadzić operacji odczytu.

### <a name="test-it-write-operation-unavailability"></a>Przetestuj ją: Niedostępność operacji zapisu
Za pomocą narzędzia do testowania w usłudze Service Fabric, należy wstrzyknąć błędów, które wywołuje utraciła kworum, która jest testem. Mimo że takiej sytuacji jest rzadkie, ważne jest, czy klientów i usług, które są zależne od usługi stanowej są przygotowywane do obsługi sytuacje, w których nie może ona podjąć, żądania zapisu do niego. Jest również zdaje sobie sprawę z tej możliwości samej usługi stanowe i bez problemu zmieniała można przekazać do obiektów wywołujących.

Utraciła kworum może wywołać przy użyciu programu PowerShell **Invoke ServiceFabricPartitionQuorumLoss** polecenia cmdlet:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

W tym przykładzie ustawimy `QuorumLossMode` do `QuorumReplicas` do wskazania, że chcemy wywołać utraciła kworum bez konieczności wyłączania wszystkich replik. W ten sposób operacje odczytu są nadal możliwe. Aby przetestować scenariusz, w którym całą partycję jest niedostępny, można ustawić ten przełącznik `AllReplicas`.

## <a name="next-steps"></a>Kolejne kroki
[Dowiedz się więcej na temat akcji testowalności](service-fabric-testability-actions.md)

[Dowiedz się więcej na temat scenariuszy testowalności](service-fabric-testability-scenarios.md)

