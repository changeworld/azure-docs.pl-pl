---
title: 'Testowalność: Komunikacja serwisowa'
description: Komunikacja między usługami jest krytycznym punktem integracji aplikacji sieci szkieletowej usług. W tym artykule omówiono zagadnienia dotyczące projektu i techniki testowania.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 87b922cb9655588a22c739d26c9ce9e49d35781a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465550"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Scenariusze testowania sieci szkieletowej usług: komunikacja serwisowa
Mikrousługi i stylów architektonicznych zorientowanych na usługi powierzchni naturalnie w sieci szkieletowej usług Azure. W tych typach architektur rozproszonych componentized aplikacji mikrousług są zazwyczaj składa się z wielu usług, które muszą rozmawiać ze sobą. Nawet w najprostszych przypadkach zazwyczaj masz co najmniej bezstanową usługę sieci web i stanową usługę przechowywania danych, które muszą się komunikować.

Komunikacja między usługami jest krytycznym punktem integracji aplikacji, ponieważ każda usługa udostępnia zdalny interfejs API innym usługom. Praca z zestawem granic interfejsu API, który obejmuje we/wy zazwyczaj wymaga pewnej opieki, z dużą ilością testowania i sprawdzania poprawności.

Istnieje wiele zagadnień, które należy podjąć, gdy te granice usługi są połączone ze sobą w systemie rozproszonym:

* *Protokół transportowy*. Czy użyjesz protokołu HTTP dla zwiększenia współdziałania lub niestandardowego protokołu binarnego dla maksymalnej przepływności?
* *Obsługa błędów*. W jaki sposób będą traktowane błędy trwałe i przejściowe? Co się stanie, gdy usługa zostanie przeniesiony do innego węzła?
* *Limity czasu i opóźnienia*. W aplikacjach wielopoziomowych, w jaki sposób każda warstwa usługi obsługuje opóźnienia za pośrednictwem stosu i użytkownika?

Niezależnie od tego, czy używasz jednego z wbudowanych składników komunikacji usługi dostarczonych przez sieć szkieletową usług, czy tworzysz własne, testowanie interakcji między usługami ma kluczowe znaczenie dla zapewnienia odporności w aplikacji.

## <a name="prepare-for-services-to-move"></a>Przygotuj się na usługi do przeniesienia
Wystąpienia usługi mogą się poruszać w czasie. Jest to szczególnie ważne, gdy są one skonfigurowane z metryki obciążenia dla niestandardowego dostosowane optymalne równoważenie zasobów. Sieć szkieletowa usług przenosi wystąpienia usługi, aby zmaksymalizować ich dostępność nawet podczas uaktualniania, pracy awaryjnej, skalowania w poziomie i innych sytuacji, które występują w okresie istnienia systemu rozproszonego.

W miarę jak usługi poruszają się w klastrze, klienci i inne usługi powinny być przygotowane do obsługi dwóch scenariuszy podczas rozmowy z usługą:

* Replika wystąpienia lub partycji usługi została przeniesiona od czasu ostatniej rozmowy z nią. Jest to normalna część cyklu życia usługi i należy się spodziewać, że stanie się to w okresie istnienia aplikacji.
* Wystąpienie usługi lub replika partycji jest w trakcie przenoszenia. Chociaż przewijanie awaryjne usługi z jednego węzła do drugiego występuje bardzo szybko w sieci szkieletowej usług, może wystąpić opóźnienie w dostępności, jeśli składnik komunikacji usługi jest powolny, aby uruchomić.

Obsługa tych scenariuszy bezpiecznie jest ważne dla systemu płynnie działającego. Aby to zrobić, należy pamiętać, że:

* Każda usługa, z którą można się połączyć, ma *adres,* na który nasłuchuje (na przykład HTTP lub WebSockets). Gdy przeniesienie wystąpienia usługi lub partycji, zmienia się jego punkt końcowy adresu. (Przenosi się do innego węzła z innym adresem IP.) Jeśli używasz wbudowanych składników komunikacji, będą one obsługiwać ponowne rozpoznawanie adresów usług dla Ciebie.
* Może wystąpić tymczasowy wzrost opóźnienia usługi, jak wystąpienie usługi uruchamia się jego odbiornika ponownie. Zależy to od tego, jak szybko usługa otwiera odbiornik po przeniesieniu wystąpienia usługi.
* Wszystkie istniejące połączenia muszą zostać zamknięte i ponownie otwarte po otwarciu usługi w nowym węźle. Powitalny zamknięcie lub ponowne uruchomienie węzła umożliwia czas na wznowienie istniejących połączeń.

### <a name="test-it-move-service-instances"></a>Testowanie: Przenoszenie wystąpień usługi
Za pomocą narzędzi testowalności sieci szkieletowej usług, można autoryzować scenariusz testowy, aby przetestować te sytuacje na różne sposoby:

1. Przenoszenie repliki podstawowej usługi stanowej.
   
    Podstawowa replika partycji usługi stanowej można przenieść z dowolnej liczby powodów. Użyj tego, aby kierować replikę podstawową określonej partycji, aby zobaczyć, jak usługi reagują na ruch w bardzo kontrolowany sposób.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zatrzymaj węzeł.
   
    Po zatrzymaniu węzła sieci szkieletowej usług przenosi wszystkie wystąpienia usługi lub partycje, które znajdowały się w tym węźle, do jednego z innych dostępnych węzłów w klastrze. Służy do testowania sytuacji, w której węzeł zostanie utracony z klastra i wszystkie wystąpienia usługi i repliki w tym węźle muszą być przesuń.
   
    Węzeł można zatrzymać za pomocą polecenia cmdlet **Stop-ServiceFabricNode** programu PowerShell:
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Obsługa dostępności usług
Jako platforma sieci szkieletowej usług został zaprojektowany w celu zapewnienia wysokiej dostępności usług. Jednak w skrajnych przypadkach problemy z infrastrukturą mogą nadal powodować niedostępność. Ważne jest, aby przetestować dla tych scenariuszy, zbyt.

Usługi stanowe używają systemu opartego na kworum do replikowania stanu w celu uzyskania wysokiej dostępności. Oznacza to, że kworum replik musi być dostępne do wykonywania operacji zapisu. W rzadkich przypadkach, takich jak powszechna awaria sprzętu, kworum replik może być niedostępne. W takich przypadkach nie będzie można wykonywać operacji zapisu, ale nadal będzie można wykonywać operacje odczytu.

### <a name="test-it-write-operation-unavailability"></a>Przetestuj to: Niedostępność operacji zapisu
Za pomocą narzędzi testability w sieci szkieletowej usług, można wstrzyknąć błąd, który wywołuje utratę kworum jako test. Chociaż taki scenariusz jest rzadkością, ważne jest, aby klienci i usługi, które zależą od usługi stanowej są przygotowane do obsługi sytuacji, w których nie mogą dokonywać żądań zapisu do niego. Ważne jest również, że sama służba stanowa jest świadoma tej możliwości i może z gracją komunikować ją dzwoniącym.

Można wywołać utratę kworum przy użyciu polecenia cmdlet PowerShell **Invoke-ServiceFabricPartitionQuorumLoss:**

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

W tym przykładzie `QuorumLossMode` `QuorumReplicas` ustawiliśmy, aby wskazać, że chcemy wywołać utratę kworum bez zdejmowania wszystkich replik. W ten sposób operacje odczytu są nadal możliwe. Aby przetestować scenariusz, w którym cała partycja `AllReplicas`jest niedostępna, można ustawić ten przełącznik na .

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o działaniach testowania](service-fabric-testability-actions.md)

[Dowiedz się więcej o scenariuszach testowania](service-fabric-testability-scenarios.md)

