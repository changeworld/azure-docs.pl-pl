---
title: Menedżer zasobów klastra usługi sieć szkieletowa — integracji zarządzania | Dokumentacja firmy Microsoft
description: Przegląd punkty integracji między Cluster Resource Manager i usługi zarządzania siecią szkieletową.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3f93ca94d5aa3e95637a53a4c8fe3d9d264dd58c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208288"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integracji Menedżera zasobów klastra z zarządzania klastrem sieci szkieletowej usług
Menedżer zasobów klastra sieci szkieletowej usługi uaktualnienia nie na dysku w sieci szkieletowej usług, ale zostało ono uwzględnione. Pierwszy sposób, aby Menedżer zasobów klastra z zarządzania jest śledzenie żądanego stanu klastra i usług w nim. Menedżer zasobów klastra wysyła raporty kondycji gdy go nie można wstawić klastra do żądanej konfiguracji. Na przykład jeśli są niewystarczające Menedżera zasobów klastra wysyła ostrzeżeń i błędów wskazujących na ten problem. Inny element integracji związana z działania uaktualnienia. Menedżer zasobów klastra nieco zmienia jego zachowania podczas uaktualniania.  

## <a name="health-integration"></a>Integracja kondycji
Menedżer zasobów klastra śledzi stale reguły, które zostały określone dla wprowadzenie do usługi. Również śledzi pozostałych pojemności dla każdej metryki na węzłach w klastrze i w klastrze jako całość. Jeśli nie spełniają tych zasad lub są niewystarczające, są emitowane ostrzeżeń i błędów. Na przykład, jeśli węzeł znajduje się nad pojemności i Menedżera zasobów klastra próbuje naprawić tę sytuację przez przeniesienie usług. Jeśli nie można rozwiązać tę sytuację emituje ostrzeżenie kondycji wskazująca, który węzeł jest za pośrednictwem pojemności i dla których metryki.

Innym przykładem ostrzeżeń menedżera zasobów jest naruszenia ograniczeń umieszczania. Na przykład, jeśli zdefiniowano ograniczenia umieszczania (takie jak `“NodeColor == Blue”`) i Menedżer zasobów wykrywa naruszenie takiego ograniczenia, jego emituje ostrzeżenie kondycji. Dotyczy to niestandardowe ograniczenia i ograniczenia domyślnego (na przykład ograniczenia domeny błędów i domeny uaktualnienia).

Oto przykład jeden taki raport o kondycji. W takim przypadku raport o kondycji jest dla jednej partycji usług systemowych. Komunikat kondycji wskazuje, że replik tej partycji tymczasowo są pakować na zbyt mało domeny uaktualnienia.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Oto, co ten komunikat kondycji informuje NAS jest:

1. Wszystkie repliki sami są w dobrej kondycji: każdy ma AggregatedHealthState: Ok
2. Obecnie trwa naruszenia ograniczenia dystrybucji domeny uaktualnienia. Oznacza to, że konkretnej domeny uaktualnienia zawiera więcej repliki z tej partycji niż powinien.
3. Węzeł, który zawiera replikę powoduje naruszenie. W takim przypadku jest węzeł z nazwą "Node.8"
4. Określa, czy uaktualnienie aktualnie wykonywane dla partycji to ("obecnie uaktualnianie — false")
5. Zasady dystrybucji dla tej usługi: "Pakowania — zasady dystrybucji". To jest objęte `RequireDomainDistribution` [zasady rozmieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Pakowanie" oznacza, że w takim przypadku DomainDistribution była _nie_ wymagane, aby było wiadomo, że zasady rozmieszczania nie został określony dla tej usługi. 
6. Jeśli wystąpiły raport - 2015-8-10 19:13:02: 00

Informacje takie jak alerty tego uprawnienia, uruchamianych w środowisku produkcyjnym z powiadomieniem coś niepowodzenia, a także jest używane do wykrywania i zatrzymanie zły uaktualnienia. W takim przypadku czy chcemy Zobacz, jeśli firma Microsoft może dowiedzieć się, dlaczego Menedżer zasobów musiał pakietu replik w domenie uaktualnienia. Zazwyczaj pakowania jest przejściowe, ponieważ węzły w innych domenach, uaktualniania, na przykład.

Załóżmy, że próbuje umieścić niektóre usługi Menedżer zasobów klastra, ale nie ma żadnych rozwiązań, które działają. Gdy nie można umieścić usługi jest zwykle dla jednego z następujących powodów:

1. Niektóre stan przejściowy wprowadził można umieścić tego wystąpienia usługi lub repliki poprawnie
2. Wymagania dotyczące umieszczania usługi są unsatisfiable.

W takich przypadkach raportów o kondycji z Menedżera zasobów klastra pomóc ustalić, dlaczego nie można umieścić usługi. Ten proces nazywa sekwencji eliminacji ograniczenia. Podczas, system przeprowadzi Cię przez skonfigurowane ograniczenia wpływu na usługi i rekordy one usunąć. Dzięki temu po usług nie mogą być umieszczone widać węzły, które zostały usunięte i dlaczego.

## <a name="constraint-types"></a>Ograniczenia typów
Załóżmy porozmawiać na temat każdego z różnych ograniczeń w tych raportach o kondycji. Zobaczysz wiadomości kondycji związane z tych warunków ograniczających przy replik nie może zostać umieszczona.

* **ReplicaExclusionStatic** i **ReplicaExclusionDynamic**: tych warunków ograniczających wskazuje, że rozwiązanie zostało odrzucone, ponieważ dwa obiekty usługi z tej samej partycji musi znajdować się na tym samym węźle. To nie jest dozwolona, ponieważ, a następnie awarii tego węzła zbyt będzie mieć wpływ na tej partycji. ReplicaExclusionStatic i ReplicaExclusionDynamic są prawie tę samą zasadę i różnice, rzeczywiście nie ma znaczenia. Jeśli widzisz sekwencję eliminacji ograniczenia zawierającą ReplicaExclusionStatic albo ReplicaExclusionDynamic ograniczenie Menedżera zasobów klastra sądzi, że nie ma wystarczającej liczby węzłów. Wymaga to pozostałe rozwiązania do używania tych nieprawidłowy rozmieszczanie, które są niedozwolone. Inne ograniczenia w sekwencji zostanie zwykle Powiedz nam Dlaczego węzły są wyeliminowano w pierwszej kolejności.
* **PlacementConstraint**: Jeśli ten komunikat zostanie wyświetlony, oznacza to, ponieważ nie odpowiadają one ograniczenia umieszczania usług możemy wyeliminować niektóre węzły. Firma Microsoft śledzenia limit ograniczenia dotyczące umieszczania aktualnie skonfigurowanych jako część tego komunikatu. Jest to normalne, jeśli ma zdefiniowane ograniczenia umieszczania. Jednak jeśli ograniczenia umieszczania niepoprawnie powoduje zbyt wiele węzłów wyeliminować to, jak można będzie zauważyć.
* **NodeCapacity**: to ograniczenie oznacza, że Menedżera zasobów klastra nie umieścić replik na wskazanych węzłów, ponieważ który spowodowałaby je za pośrednictwem pojemności.
* **Koligacja**: to ograniczenie wskazuje, że firma Microsoft nie można umieścić repliki na odpowiednich węzłów ponieważ spowodowałoby to naruszenie ograniczenia koligacji. Więcej informacji o koligacji jest [w tym artykule](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** i **UpgradeDomain**: to ograniczenie eliminuje węzłów, jeśli umieszczenie replik w węzłach wskazanych spowodowałoby pakowania w domenie uaktualnienia lub określonego błędu. Kilka przykładów dyskutować tego ograniczenia są podane w temacie na [ograniczenia domeny awarii i uaktualniania i efekty](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: zwykle niepowołanymi to ograniczenie usuwania węzłów z rozwiązania, ponieważ jest on optymalizacji domyślnie. Ograniczenie lokalizacji preferowany jest również obecny podczas uaktualniania. Podczas uaktualniania służy można przenieść usług, do których znajdowały się po rozpoczęciu uaktualnienia.

## <a name="blocklisting-nodes"></a>Węzły Blocklisting
Kolejną wiadomość kondycji raporty Menedżera zasobów klastra jest w przypadku węzły są blocklisted. Blocklisting można traktować jako tymczasowe ograniczenie, które zostanie automatycznie zastosowana dla Ciebie. Węzły Pobierz blocklisted po wystąpieniu błędów wielokrotnego podczas uruchamiania wystąpienia danego typu usługi. Węzły są blocklisted na podstawie na — typ usługi. Węzeł może być blocklisted dla typu w jednej usłudze, ale nie inna. 

Zobaczysz blocklisting zaczną działać często podczas tworzenia: niektóre usterki powoduje, że host usług awarii podczas uruchamiania. Usługa sieci szkieletowej, próbuje je utworzyć hosta usługi kilka razy, a błąd będzie się powtarzał. Mimo kilku prób blocklisted pobiera węzeł, a Menedżer zasobów klastra podejmie próbę utworzenia usługi w innym miejscu. Jeśli błąd ten problem będzie nadal występować na wielu węzłach, prawdopodobnie zablokowany przez wszystkie węzły prawidłowy w końcu klastra w górę. Blocklisting można również usunąć tak wiele węzłów, czy nie ma wystarczającej ilości pomyślnie uruchomić usługę, aby spełnić żądaną skali. Zwykle widoczne dodatkowe błędy lub ostrzeżenia z klastra Menedżera zasobów wskazujący, że usługa poniżej żądanej repliki lub liczba wystąpień, jak również wiadomości kondycji wskazująca, co to jest błąd, który prowadzi do blocklisting w pierwszej kolejności.

Blocklisting nie jest trwały warunek. Po kilku minutach węzeł zostanie usunięty z blocklist i sieci szkieletowej usług mogą ponownie aktywować usług w tym węźle. Usługi w dalszym ciągu się nie powieść, ten węzeł jest blocklisted dla danego typu usługi ponownie. 

### <a name="constraint-priorities"></a>Ograniczenie priorytetów

> [!WARNING]
> Zmiana ograniczenia priorytetów nie jest zalecane i może mieć negatywnego wpływu na klastrze. Podano poniżej informacje do priorytetów ograniczenie domyślne i ich zachowanie. 
>

Biorąc pod uwagę następujące ograniczenia możesz może mieć zostały myśląc "Witaj — myślę, że ograniczenia domeny błędów są najważniejsze w systemie. Aby zapewnić, że nie jest naruszyła ograniczenia domeny błędów, chcę narusza inne ograniczenia."

Ograniczenia można skonfigurować za pomocą różnych poziomów priorytetu. Są to:

   - "twardych" (0)
   - "soft" (1)
   - "optymalizacji" [2]
   - "off" (-1). 
   
Większość ograniczenia są domyślnie skonfigurowane jako twardych ograniczeń.

Zmiana priorytetu ograniczenia jest rzadko. Było razy zmiany zwykle w celu obejścia inne usterki lub zachowanie wpływał środowiska, gdy potrzebne priorytetów ograniczenia. Zazwyczaj bardzo dobrze działa elastyczności infrastruktury priorytet ograniczenia, ale nie jest często potrzebne. W większości przypadków wszystkie znajduje się w ich priorytety domyślnych. 

Poziomy priorytetu nie oznaczają, że dany ograniczenia _będzie_ naruszone, ani zawsze będą spełnione. Ograniczenie priorytetów określić kolejność, w którym wymuszone są ograniczenia. Priorytety skutków ubocznych pozwalają zdefiniować, kiedy nie jest możliwe spełniać wszystkie ograniczenia. Zwykle wszystkie ograniczenia można spełnić, chyba że coś else dzieje się w środowisku. Przykładowe scenariusze, które będzie prowadzić do naruszenia ograniczeń to powodujące konflikt ograniczenia lub dużą liczbą jednoczesnych awarii.

W sytuacjach, zaawansowane można zmienić priorytety ograniczenia. Na przykład załóżmy, że w chcieli upewnij się, że koligacji zostałyby zawsze naruszone w razie konieczności do rozwiązywania problemów pojemność węzła. Można to osiągnąć, możesz ustawić priorytet ograniczenia koligacji "soft" (1) i pozostaw ograniczenie pojemności ustawiony na "twardych" (0).

Wartości domyślne priorytet dla różnych ograniczeń są określone w następujących konfiguracji:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Błąd ograniczenia domeny domeny i uaktualnienia
Menedżer zasobów klastra chce zachować rozłożenia między domenach awarii i uaktualniania usług. To jej modele jako ograniczenia wewnątrz aparatu Menedżera zasobów klastra. Aby uzyskać więcej informacji na temat sposobu ich używania i ich określone zachowanie wyewidencjonować artykułu na [konfiguracji klastra](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Menedżer zasobów klastra może być konieczne pakietu kilka replik w domenie uaktualnienia, aby poradzić sobie z uaktualnień, niepowodzenia lub inne naruszenia ograniczenia. Pakowanie w domenach awarii lub uaktualnienia zwykle odbywa się tylko wtedy, gdy istnieje kilka błędów lub innych zmian w systemie uniemożliwia poprawne umieszczania. Jeśli chcesz zapobiec pakowania nawet w takich przypadkach, mogą wykorzystywać `RequireDomainDistribution` [zasady rozmieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Należy pamiętać, może to mieć wpływ na dostępność usługi i niezawodność jako efekt uboczny, dlatego należy dokładnie należy rozważyć.

Jeśli środowisko jest skonfigurowane poprawnie, wszystkie ograniczenia są w pełni uwzględniona, nawet podczas uaktualniania. Element klucza jest czy Menedżera zasobów klastra obserwuje dla użytkownika ograniczenia. Po wykryciu naruszenia natychmiast zgłasza i próbuje rozwiązać problem.

## <a name="the-preferred-location-constraint"></a>Ograniczenie lokalizacji preferowaną
Ograniczenie PreferredLocation różni się nieco, jak składa się z dwóch różnych zastosowań. Jedno użycie to ograniczenie jest podczas uaktualniania aplikacji. Menedżer zasobów klastra automatycznie zarządza to ograniczenie podczas uaktualniania. Służy do zapewnienia, że po ukończeniu, że repliki powrócić do ich lokalizacji początkowej uaktualnienia. Użycie ograniczenia PreferredLocation dotyczy [ `PreferredPrimaryDomain` zasady rozmieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Oba te optymalizacje, i dlatego ograniczenia PreferredLocation tylko ograniczenia domyślnie ustawione na "Optymalizacji".

## <a name="upgrades"></a>Uaktualnienia
Menedżer zasobów klastra pomaga również w aplikacji i uaktualnienia klastra, w których ma dwa zadania:

* Upewnij się, że reguły klastra nie są uszkodzone
* próby uaktualnienia bezproblemowe pomocy

### <a name="keep-enforcing-the-rules"></a>Zachowaj wymuszania zasad
Główny jest, aby mieć świadomość jest, że reguły — ścisłe ograniczenia jak ograniczenia dotyczące umieszczania i pojemności - nadal są wymuszane podczas uaktualniania. Ograniczenia dotyczące umieszczania upewnij się, że obciążeń tylko uruchomiony, gdy mogą one, nawet podczas uaktualniania. Gdy usługi są bardzo ograniczone, uaktualnienia może trwać dłużej. Gdy usługa lub węzeł, na którym jest uruchomiona na jest obniżył aktualizacji może być kilka opcji, których można go.

### <a name="smart-replacements"></a>Zamienianie inteligentne
Po uruchomieniu uaktualniania Resource Manager tworzy migawkę bieżącego rozmieszczenia klastra. Zgodnie z każdej domeny uaktualnienia zakończeniu próbuje zwrócić usług, które znajdowały się w tej domenie uaktualnienia na ich oryginalny układ. W ten sposób jest co najwyżej dwa przejścia usługi podczas uaktualniania. Istnieje jeden Przenieś dotyczy węzła i jedną cofnąć się. Powrót do klastra lub usługi do jak sprzed uaktualnienia również gwarantuje, że uaktualnienie bez wpływu na układ klastra. 

### <a name="reduced-churn"></a>Postęp dokonany w obniżonej
Innym czynnikiem, która jest wywoływana podczas uaktualniania jest wyłącza Menedżera zasobów klastra równoważenia. Zapobieganie równoważenia zapobiega niepotrzebnych reakcji do uaktualnienia, takie jak przeniesienie usług w węzłach, które zostały opróżnione do uaktualnienia. Uaktualnienie w przypadku uaktualniania klastra całego klastra nie jest rozmieszczana podczas uaktualniania. Ograniczenia kontroli pozostają aktywne, tylko ruchu w oparciu aktywnego równoważenie metryki jest wyłączona.

### <a name="buffered-capacity--upgrade"></a>Pojemność buforowanego & uaktualnienia
Zwykle ma zostać uaktualniony do ukończenia nawet wtedy, gdy klaster jest ograniczona lub w pobliżu pełnej. Zarządzanie pojemności klastra jest szczególnie ważne podczas uaktualniania niż zwykle. W zależności od liczby domen uaktualnienia od 5 do 20% pojemności, muszą być migrowane jako uaktualnienia przedstawia za pośrednictwem klastra. Pracy ma go w innym miejscu. Jest to, gdy pojęcie [buforowane możliwości](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) jest użyteczne. Pojemność buforowany jest wzięty pod uwagę podczas normalnego działania. Menedżer zasobów klastra mogą wypełnić węzłów do ich całkowitej pojemności (Korzystanie z buforu) podczas uaktualniania, w razie potrzeby.

## <a name="next-steps"></a>Kolejne kroki
* Rozpocznij od początku i [wprowadzenie do usługi sieci szkieletowej klastra Menedżera zasobów](service-fabric-cluster-resource-manager-introduction.md)
