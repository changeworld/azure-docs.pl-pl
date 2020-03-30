---
title: Ograniczanie przepustowości w Menedżerze zasobów klastra sieci szkieletowej usług
description: Dowiedz się, jak skonfigurować ograniczenia dostarczane przez Menedżera zasobów klastra sieci szkieletowej usług.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b4d78b339bab02b5c44a31939e0da769dc21c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452172"
---
# <a name="throttling-the-service-fabric-cluster-resource-manager"></a>Ograniczanie Menedżera zasobów klastra sieci szkieletowej usług
Nawet jeśli Menedżer zasobów klastra został poprawnie skonfigurowany, klaster może zostać zakłócony. Na przykład mogą wystąpić jednoczesne błędy węzłów i domen błędów — co by się stało, gdyby wystąpiło podczas uaktualniania? Menedżer zasobów klastra zawsze próbuje naprawić wszystko, zużywając zasoby klastra, próbując zreorganizować i naprawić klaster. Ograniczania pomagają zapewnić mechanizm ochronny, dzięki czemu klaster może używać zasobów do stabilizacji - węzły wracają, partycje sieciowe leczą, korygują bity.

Aby pomóc w tego rodzaju sytuacjach, Menedżer zasobów klastra sieci szkieletowej usług zawiera kilka przepustek. Te przepustnice są dość duże młoty. Ogólnie rzecz biorąc, nie należy ich zmieniać bez starannego planowania i testowania.

Jeśli zmienisz ograniczenia Menedżera zasobów klastra, należy dostroić je do oczekiwanego rzeczywistego obciążenia. Można określić, że trzeba mieć pewne ograniczenia w miejscu, nawet jeśli oznacza to, że klaster trwa dłużej, aby ustabilizować się w niektórych sytuacjach. Testowanie jest wymagane do określenia prawidłowych wartości dla przepustów. Ograniczania muszą być wystarczająco wysokie, aby umożliwić klastra reagować na zmiany w rozsądnym czasie i wystarczająco niskie, aby faktycznie zapobiec zbyt dużej ilości zużycia zasobów. 

Przez większość czasu widzieliśmy klientów używać przepustnice to było, ponieważ były one już w środowisku ograniczonym zasobem. Niektóre przykłady mogą być ograniczona przepustowość sieci dla poszczególnych węzłów lub dysków, które nie są w stanie zbudować wiele replik stanowych równolegle ze względu na ograniczenia przepływności. Bez ograniczania przepustowości operacje mogą przeciążyć te zasoby, powodując niepowodzenie operacji lub spowolnienie. W takich sytuacjach klienci używali przepustów i wiedzieli, że wydłużają czas, jaki zajmie klastrowi osiągnięcie stabilnego stanu. Klienci zrozumieli również, że mogą skończyć działać przy niższej ogólnej niezawodności, gdy byli ograniczani.


## <a name="configuring-the-throttles"></a>Konfigurowanie przepustów

Sieci szkieletowej usług ma dwa mechanizmy ograniczania liczby ruchów repliki. Domyślny mechanizm, który istniał przed sieci szkieletowej usług 5.7 reprezentuje ograniczanie przepustowości jako bezwzględną liczbę ruchów dozwolone. To nie działa dla klastrów wszystkich rozmiarów. W szczególności dla dużych klastrów wartość domyślna może być zbyt mała, znacznie spowalniając równoważenie nawet wtedy, gdy jest to konieczne, nie mając wpływu w mniejszych klastrach. Ten wcześniejszy mechanizm został zastąpiony przez ograniczanie procentowe, które lepiej skaluje się w przypadku klastrów dynamicznych, w których liczba usług i węzłów zmienia się regularnie.

Ograniczenia są oparte na procentu liczby replik w klastrach. Ograniczenia procentowe umożliwiają wyrażenie reguły: "nie należy przenosić więcej niż 10% replik w odstępie 10 minut", na przykład.

Ustawienia konfiguracji ograniczania przepustowości opartej na procentach to:

  - GlobalMovementThrottleThresholdPercentage — maksymalna liczba ruchów dozwolonych w klastrze w dowolnym momencie, wyrażona jako procent całkowitej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno to ustawienie, jak i GlobalMovementThrottleThreshold, używany jest bardziej konserwatywny limit.
  - GlobalMovementThrottleThresholdPercentageForPlacement — maksymalna liczba ruchów dozwolonych podczas fazy umieszczania, wyrażona jako procent całkowitej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno to ustawienie, jak i GlobalMovementThrottleThresholdForPlacement, używany jest bardziej konserwatywny limit.
  - GlobalMovementThrottleThresholdPercentageForBalancing — maksymalna liczba ruchów dozwolonych podczas fazy równoważenia, wyrażona jako procent całkowitej liczby replik w klastrze. 0 oznacza brak limitu. Wartość domyślna to 0. Jeśli określono zarówno to ustawienie, jak i GlobalMovementThrottleThresholdForBalancing, używany jest bardziej konserwatywny limit.

Określając procent przepustnicy, należy określić 5% jako 0,05. Interwał, w którym te przepustnice są regulowane jest GlobalMovementThrottleCountingInterval, który jest określony w sekundach.


``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThresholdPercentage" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForPlacement" Value="0" />
     <Parameter Name="GlobalMovementThrottleThresholdPercentageForBalancing" Value="0" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
</Section>
```

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

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

### <a name="default-count-based-throttles"></a>Ograniczenia oparte na liczbie domyślnej
Te informacje są dostarczane w przypadku, gdy masz starsze klastry lub nadal zachować te konfiguracje w klastrach, które zostały uaktualnione. Ogólnie rzecz biorąc zaleca się, aby zostały one zastąpione przepustkami opartymi na procentach powyżej. Ponieważ ograniczanie procentowe jest domyślnie wyłączone, te ograniczenia pozostają domyślnymi ograniczeniami dla klastra, dopóki nie zostaną wyłączone i zastąpione ograniczeniami opartymi na procentach. 

  - GlobalMovementThrottleThreshold — to ustawienie kontroluje całkowitą liczbę ruchów w klastrze w pewnym czasie. Czas jest określony w sekundach jako GlobalMovementThrottleCountingInterval. Wartość domyślna globalmovementThrottleThreshold wynosi 1000, a wartość domyślna dla GlobalMovementThrottleCountingInterval wynosi 600.
  - MovementPerPartitionThrottleThreshold — to ustawienie kontroluje całkowitą liczbę ruchów dla dowolnej partycji usługi przez pewien czas. Ilość czasu jest określona w sekundach jako MovementPerPartitionThrottleCountingInterval. Wartość domyślna dla MovementPerPartitionThrottleThreshold wynosi 50, a wartość domyślna dla MovementPerPartitionThrottleCountingInterval wynosi 600.

Konfiguracja dla tych przepustów jest zgodna z tym samym wzorcem co ograniczanie oparte na procentach.

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z artykułem na temat [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Menedżer zasobów klastra ma wiele opcji opisu klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tym [artykułem opisującym klaster sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
