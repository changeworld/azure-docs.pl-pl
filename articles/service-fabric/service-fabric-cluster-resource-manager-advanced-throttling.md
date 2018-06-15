---
title: Ograniczanie w Menedżerze zasobów klastra usługi sieć szkieletowa | Dokumentacja firmy Microsoft
description: Dowiedz się skonfigurować limity udostępniane przez usługi sieci szkieletowej klastra Menedżera zasobów.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4a44678b-a5aa-4d30-958f-dc4332ebfb63
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e9db1070066a2a02b72b5cc051e59d8b04dc9928
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205131"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Ograniczanie Menedżera zasobów klastra sieci szkieletowej usług
Nawet jeśli Menedżera zasobów klastra został prawidłowo skonfigurowany, można pobrać zakłócona klastra. Na przykład może być jednocześnie węzła błędów domeny awarii i — co się stanie po, który wystąpił podczas uaktualniania? Menedżer zasobów klastra zawsze próbuje naprawić wszystkie elementy, korzysta z zasobów klastra próby reorganizować i napraw klastra. Limity zapewniają backstop tak, aby klaster może używać zasobów do utrwalania — węzły wrócić, poprawianego partycje sieciowe, wdrożony poprawiony usługi bits.

Ułatwiające pracę z tego rodzaju sytuacjach Menedżera zasobów klastra sieci szkieletowej usług zawiera kilka limity. Te limity są wszystkie młotów dość duży. Zazwyczaj nie można zmienić bez zachować ostrożność podczas planowania i testowania.

Jeśli zmienisz limity Menedżera zasobów klastra powinna dostroić ich oczekiwanego rzeczywistego obciążenia. Użytkownik może określić musi mieć niektóre limity w miejscu, nawet w przypadku oznacza to, że klaster obsługuje żądanie dłużej do utrwalania w niektórych sytuacjach. Testowanie jest wymagana do określania poprawnych wartości dla limity. Limity muszą być wystarczająco duża, aby umożliwić klastrowi odpowiadają na zmiany w rozsądnym czasie, a tyle niskie, aby zapobiec faktycznie zużycie zbyt dużej ilości zasobów. 

W większości przypadków możemy w tym samouczku klientów za pomocą limity została ponieważ już zostały w środowisku ograniczonego zasobów. Przykłady będzie ograniczona przepustowość sieci dla poszczególnych węzłów i dysków, które nie są możliwe do skompilowania wiele replik stanowe równolegle ze względu na ograniczenia przepustowości. Bez limity operacje można przeciąży tych zasobów, powoduje operacji się nie powieść lub powolne. W takich sytuacjach klienci używane limity i wiedziały, że zostały one rozszerzanie ilość czasu zajmuje klaster osiągnął stabilności. Klienci również Rozumiem, że ich może służyć do wykonywania w dolnym ogólną niezawodność, podczas gdy zostały one ograniczany.


## <a name="configuring-the-throttles"></a>Konfigurowanie limity

Sieć szkieletowa usług ma dwa mechanizmy do ograniczania liczby przeniesień repliki. Domyślny mechanizm, które istniały przed usługi sieć szkieletowa 5.7 reprezentuje ograniczenie w postaci bezwzględną liczbę przenosi dozwolone. Nie działa w przypadku klastrów wszystkich rozmiarów. W szczególności w dużych klastrów wartości domyślnej może być za mały, znacznie spowolnieniem równoważenie nawet wtedy, gdy jest to konieczne, a jednocześnie ma efektu w mniejszych klastrów. Ten mechanizm uprzedniego została zastąpiona procentowych ograniczania przepustowości, która skaluje się lepiej z klastrami dynamicznej, w których liczba usług i węzły zmieniają się regularnie.

Limity są oparte na procent liczba replik w klastrach. Włącz limity Percetage na podstawie wyrażenia reguła: "nie przenoś więcej niż 10% replik w 10-minutowych interwałach", np.

Ustawienia konfiguracji dla opartych na procentach ograniczania są:

  - GlobalMovementThrottleThresholdPercentage — maksymalna liczba przeniesień dozwolone w klastrze, w dowolnym momencie, wyrażony jako procent całkowitej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno to ustawienie, aby GlobalMovementThrottleThreshold więcej zachowawcze limit jest używany.
  - GlobalMovementThrottleThresholdPercentageForPlacement — maksymalna liczba przeniesień dozwolone podczas fazy umieszczania wyrażony jako procent całkowitej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno to ustawienie, aby GlobalMovementThrottleThresholdForPlacement więcej zachowawcze limit jest używany.
  - GlobalMovementThrottleThresholdPercentageForBalancing — maksymalna liczba przeniesień dozwolone podczas fazy równoważenia wyrażony jako procent całkowitej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno to ustawienie, aby GlobalMovementThrottleThresholdForBalancing więcej zachowawcze limit jest używany.

Podczas określania procent przepustowości, należy określić jako 0,05 5%. Interwał zarządzane te limity jest GlobalMovementThrottleCountingInterval, która została określona w sekundach.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

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

### <a name="default-count-based-throttles"></a>Limity liczby na podstawie domyślnego
Te informacje są dostarczane w przypadku, gdy masz starszą klastrów lub nadal zachować te konfiguracje klastrów, ponieważ zostały uaktualnione. Ogólnie rzecz biorąc zaleca się, że te są zastępowane limity procentowych powyżej. Procentowych ograniczania przepustowości jest domyślnie wyłączona, te limity pozostają limity domyślne dla klastra, dopóki nie są wyłączone i zastąpione procentowych limity. 

  - GlobalMovementThrottleThreshold — to ustawienie określa liczba przeniesień w klastrze trochę czasu. Ilość czasu został określony w sekundach jako GlobalMovementThrottleCountingInterval. Wartością domyślną dla GlobalMovementThrottleThreshold wynosi 1000, a wartość domyślna dla GlobalMovementThrottleCountingInterval to 600.
  - MovementPerPartitionThrottleThreshold — to ustawienie określa liczba przeniesień do żadnej partycji usługi trochę czasu. Ilość czasu został określony w sekundach jako MovementPerPartitionThrottleCountingInterval. MovementPerPartitionThrottleThreshold wartością domyślną jest 50, a wartość domyślna dla MovementPerPartitionThrottleCountingInterval to 600.

Konfiguracja te limity zgodny ze wzorcem samej jako procentowych ograniczania.

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się o jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz artykuł na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Menedżer zasobów klastra ma wiele opcji opisujące klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się w tym artykule na [opisujące klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
