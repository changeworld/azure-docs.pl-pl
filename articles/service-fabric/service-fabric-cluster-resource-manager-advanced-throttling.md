---
title: Ograniczanie żądań w usłudze Menedżer zasobów klastra usługi Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się skonfigurować ograniczenia podana przez usługę Service Fabric Menedżer zasobów klastra.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 4abc3e4a28b8b98070affe19b7b7ca38f904c45b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384973"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Ograniczanie Menedżer zasobów klastra usługi Service Fabric
Nawet jeśli Menedżer zasobów klastra zostały skonfigurowane poprawnie, można uzyskać zakłócone klastra. Na przykład może być jednocześnie węzła błędów domeny awarii i — co się stanie, jeśli, które wystąpiły podczas uaktualniania? Menedżer zasobów klastra zawsze próbuje naprawić wszystkie elementy, zużywają zasoby klastra próby reorganizowanie i naprawić klastra. Ograniczenia zapewniają backstop tak, aby klaster może używać zasobów w celu stabilizacji — możesz wrócić w węzłach, poprawianie partycje sieci, wdrożony poprawiony usługi bits.

Aby ułatwić te wielu sytuacjach, Menedżer zasobów klastra usługi Service Fabric zawiera kilka ograniczenia. Te ograniczenia są wszystkie młotki dość duży. Ogólnie nie można zmienić bez starannego planowania i testowania.

Jeśli zmienisz ograniczenia Menedżer zasobów klastra, możesz określić je do oczekiwanego rzeczywistego obciążenia. Mogą określić, że musisz mieć pewne ograniczenia obowiązuje, nawet w przypadku oznacza to, że klastra trwa dłużej, w celu stabilizacji w niektórych sytuacjach. Testowanie jest wymagane, aby określić poprawne wartości dla ograniczenia. Ograniczenia muszą być odpowiednio wysoka, aby umożliwić klastrowi reagowanie na zmiany w rozsądnym czasie, a tyle niskie, aby faktycznie zapobiegania zbyt dużo zużycie zasobów. 

W większości przypadków, w związku z czym dostrzegliśmy klientów za pomocą ograniczenia, których minęło, ponieważ zostały one już środowisku ograniczonych zasobów. Niektóre przykłady będzie ograniczona przepustowość sieci dla poszczególnych węzłów lub dysków, które nie są możliwe do skompilowania wiele stanowych replik równolegle ze względu na ograniczenia przepływności. Bez ograniczenia operacje można przeciąży tych zasobów, powodując operacje się niepowodzeniem lub wolne. W takich sytuacjach klienci używane ograniczenia i wiedzieliśmy, że na rozszerzeniu czas zajęłoby klaster osiągnął stabilności. Klienci także rozumieć, że może pozostać uruchomione w dolnym ogólną niezawodność, chociaż były one ograniczone.


## <a name="configuring-the-throttles"></a>Konfigurowanie ograniczenia

Usługa Service Fabric ma dwa mechanizmy ograniczenie liczby operacji repliki. Domyślnego mechanizmu, które istniały przed wykonaniem usługi Service Fabric w wersji 5.7 reprezentuje ograniczania jako wartość bezwzględną przeniesień dozwolone. Nie działa w przypadku klastrów dowolnego rozmiaru. W szczególności w przypadku dużych klastrach wartość domyślna może być za mały, znacznie spowalniać równoważenie nawet wtedy, gdy jest to konieczne, mając efektu w mniejszych klastrów. Ten mechanizm wcześniejsze została zastąpiona opartych na procentach ograniczanie przepustowości, która skaluje się lepiej za pomocą dynamiczne klastry, w których liczba usług i węzły są regularnie zmieniane.

Ograniczenia są oparte na procent liczby replik w klastrach. Ograniczenia na podstawie wartości procentowej Włącz wyrażanie reguła: "nie przenoś więcej niż 10% replik w 10 minut", na przykład.

Ustawienia konfiguracji opartych na procentach ograniczania są następujące:

  - GlobalMovementThrottleThresholdPercentage — maksymalna liczba przeniesień dozwolone w klastrze, w dowolnym momencie, wyrażone jako procent łącznej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno tego ustawienia, jak i GlobalMovementThrottleThreshold bardziej konserwatywnego limit jest używany.
  - GlobalMovementThrottleThresholdPercentageForPlacement — maksymalna liczba przeniesień typu dozwolone w fazie umieszczania, wyrażony jako procent łącznej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno tego ustawienia, jak i GlobalMovementThrottleThresholdForPlacement bardziej konserwatywnego limit jest używany.
  - GlobalMovementThrottleThresholdPercentageForBalancing — maksymalna liczba przeniesień typu dozwolone w fazie równoważenia wyrażony jako procent łącznej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno tego ustawienia, jak i GlobalMovementThrottleThresholdForBalancing bardziej konserwatywnego limit jest używany.

Podczas określania procent przepustowości, należy określić jako 0,05 5%. Interwał, w którym te ograniczenia są regulowane jest GlobalMovementThrottleCountingInterval, która jest określona w ciągu kilku sekund.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "GlobalMovementThrottleThresholdPercentage",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForPlacement",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleThresholdPercentageForBalancing",
          "value": "0.0"
      },
      {
          "name": "GlobalMovementThrottleCountingInterval",
          "value": "600"
      }
    ]
  }
]
```

### <a name="default-count-based-throttles"></a>Ograniczenia na podstawie liczby domyślne
Te informacje są dostarczane w przypadku, gdy masz starsze klastry lub nadal zachować te konfiguracje w klastrach, które od zostały uaktualnione. Ogólnie rzecz biorąc zalecane jest, że te są zastępowane powyższe ograniczenia na podstawie wartości procentowej. Ponieważ opartych na procentach ograniczania przepustowości jest domyślnie wyłączony, te ograniczenia pozostają ograniczenia domyślne dla klastra, do momentu ich są wyłączone i zastąpione przez ograniczenia na podstawie. 

  - GlobalMovementThrottleThreshold — to ustawienie kontroluje łączną liczbę przepływów w klastrze trochę czasu. Czas jest określona w ciągu kilku sekund jako GlobalMovementThrottleCountingInterval. Wartością domyślną dla GlobalMovementThrottleThreshold wynosi 1000, a wartość domyślna dla GlobalMovementThrottleCountingInterval to 600.
  - MovementPerPartitionThrottleThreshold — to ustawienie kontroluje łączną liczbę przepływów, każda partycja usługi trochę czasu. Czas jest określona w ciągu kilku sekund jako MovementPerPartitionThrottleCountingInterval. Wartością domyślną dla MovementPerPartitionThrottleThreshold to 50, a wartość domyślna dla MovementPerPartitionThrottleCountingInterval to 600.

Konfiguracja te ograniczenia następuje jako ograniczenie na podstawie tego samego wzorca.

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się o zarządza Menedżer zasobów klastra i równoważy obciążenie w klastrze, zapoznaj się z artykułem na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Menedżer zasobów klastra ma wiele opcji do opisywania klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tego artykułu na [opisujące klaster usługi Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
