---
title: Menedżer zasobów klastra usługi Service Fabric — Integracja zarządzania | Dokumentacja firmy Microsoft
description: Omówienie punktów integracji między Menedżerem zasobów klastra i usługi zarządzania siecią szkieletową.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c201945e94474d54b8a19918f3b55a0b40995a97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743517"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Klaster zasobów Menedżera Integracja z usługą zarządzania klastrem usługi Service Fabric
Menedżer zasobów klastra usługi Service Fabric nie dysku uaktualnień w usłudze Service Fabric, ale zostało ono uwzględnione. Pierwszy sposób Menedżer zasobów klastra za pomocą funkcji zarządzania jest, śledząc żądany stan klastra i usług wewnątrz niego. Menedżer zasobów klastra wysyła raporty dotyczące kondycji, gdy go nie można wstawić klastra do pożądanej konfiguracji. Na przykład w przypadku niewystarczającej pojemności Menedżer zasobów klastra wysyła ostrzeżeń i błędów wskazujących na problem. Inny element integracji związana z działania uaktualnienia. Menedżer zasobów klastra nieco zmienia jego zachowanie podczas uaktualniania.  

## <a name="health-integration"></a>Integracja kondycji
Menedżer zasobów klastra śledzi stale reguły, które zostały zdefiniowane dla wprowadzenie do usługi. Również śledzi pozostałe pojemności dla każdego metryki na węzłach w klastrze i, w klastrze jako całości. Jeśli nie spełnia tych zasad lub w przypadku niewystarczającej pojemności, ostrzeżenia i błędy są emitowane. Na przykład, jeśli węzeł znajduje się nad pojemności, a następnie próbuje naprawić tę sytuację, przenosząc usługi Menedżer zasobów klastra. Jeśli nie można go naprawić tę sytuację emituje ostrzeżenie kondycji wskazująca, który węzeł jest przeciążone i które metryki.

Innym przykładem ostrzeżeń menedżera zasobów jest naruszenia ograniczeń umieszczania. Na przykład, jeśli zdefiniowano ograniczeń umieszczania (takie jak `“NodeColor == Blue”`) i usługi Resource Manager wykrywa naruszenia tego ograniczenia, emituje ostrzeżenie kondycji. Dotyczy to niestandardowe ograniczenia i ograniczenia domyślne (np. domena błędów i domeny uaktualnienia ograniczenia).

Oto przykład jeden taki raport o kondycji. W tym przypadku raport o kondycji jest dla jednej z partycji usługi systemowej. Komunikat o kondycji wskazuje, że repliki partycji tymczasowo są pakowane w zbyt małej liczby domen uaktualnienia.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


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

Oto, co ten komunikat health informuje NAS, jest:

1. Wszystkie repliki samodzielnie są w dobrej kondycji: Każdy ma AggregatedHealthState: OK
2. Obecnie trwa naruszenia ograniczenia dystrybucji do domeny uaktualnienia. Oznacza to, że w określonej domenie uaktualnienia jest większa liczba replik danej partycji niż powinien.
3. Węzeł, który zawiera repliki, co powoduje naruszenie. W tym przypadku jest węzeł z nazwą "Node.8"
4. Czy uaktualnienie aktualnie wykonywane dla partycji to ("obecnie uaktualnianie--false")
5. Zasady dystrybucji dla tej usługi: "Zasad dystrybucji--pakowania". To jest regulowane przez `RequireDomainDistribution` [zasady rozmieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Pakowanie" oznacza, że w tym przypadku DomainDistribution była _nie_ wymagane, aby było wiadomo, że zasady rozmieszczania nie został określony dla tej usługi. 
6. Kiedy wystąpił raportu — 8/10/2015 7:13:02: 00

Informacje takie jak alerty tego uprawnień, które są aktywowane w środowisku produkcyjnym z informacją, wystąpił problem, a także jest używane do wykrywania i zatrzymanie zły uaktualnienia. W tym przypadku będzie chcemy zobaczyć, jeśli firma Microsoft może ustalić dlaczego usługi Resource Manager miał umieszczenie tych replik w domenie uaktualnienia. Zazwyczaj pakowania jest przejściowy, ponieważ węzły w innych domenach uaktualnienia w dół, na przykład.

Załóżmy, że Menedżer zasobów klastra próbuje umieścić niektóre usługi, ale nie ma żadnych rozwiązań, które działają. Nie można umieścić usług, zazwyczaj jest to jeden z następujących powodów:

1. Niektóre stan przejściowy ma uniemożliwił można umieścić tego wystąpienia usługi lub repliki poprawnie
2. Wymagania dotyczące umieszczania usługi są unsatisfiable.

W takich przypadkach raportów o kondycji z Menedżer zasobów klastra pomóc określić, dlaczego usługa nie może zostać umieszczona. Nazywamy tego procesu sekwencji eliminacji ograniczeń. Podczas, system przeprowadzi skonfigurowane ograniczenia wpływu na usługi i rekordy one wyeliminować. Dzięki temu po usług nie mogą być umieszczone widoczne węzły, które zostały wyeliminowane i dlaczego.

## <a name="constraint-types"></a>Typy ograniczeń
Poniżej omówiono każdy z nią związane inne ograniczenia w raportach o kondycji. Widoczne są komunikaty kondycji związane z tych ograniczeń podczas replik nie może zostać umieszczona.

* **ReplicaExclusionStatic** i **ReplicaExclusionDynamic**: Te ograniczenia wskazuje, że rozwiązanie zostało odrzucone, ponieważ dwa obiekty usługi z tej samej partycji, musi zostać umieszczone na tym samym węźle. To nie jest dozwolona, ponieważ, a następnie awarii tego węzła nadmiernie mogło mieć wpływ na tej partycji. ReplicaExclusionStatic i ReplicaExclusionDynamic są prawie tę samą regułę i różnice nie ma znaczenia. Jeśli widzisz sekwencji eliminacji ograniczeń zawierającą ReplicaExclusionStatic albo ReplicaExclusionDynamic ograniczenie uzna za Menedżer zasobów klastra, że nie ma wystarczającej liczby węzłów. Wymaga to pozostałe rozwiązania, aby użyć tych angażowania nieprawidłowy, co jest niedozwolone. Warunki ograniczające w sekwencji będzie zazwyczaj Powiedz nam, dlaczego węzły są zlikwidowania w pierwszej kolejności.
* **PlacementConstraint**: Jeśli ten komunikat jest wyświetlany, oznacza to, ponieważ nie odpowiadają one ograniczeniami dotyczącymi umieszczania usługi możemy wyeliminować niektóre węzły. Firma Microsoft śledzenia się ograniczenia dotyczące umieszczania aktualnie skonfigurowanych jako część tego komunikatu. Jest to normalne, jeśli ma zdefiniowane ograniczenia umieszczania. Jednak jeśli ograniczeń umieszczania niepoprawnie przyczyną jest zbyt wiele węzłów wyeliminować to, jak można będzie zauważyć.
* **NodeCapacity**: To ograniczenie oznacza to, czy Menedżer zasobów klastra nie można umieścić replik węzłach wskazany, ponieważ która je umieścić nad pojemności.
* **Koligacja**: To ograniczenie wskazuje, że firma Microsoft nie można umieścić repliki na węzłach, których to dotyczy ponieważ spowodowałoby to naruszenie ograniczenia koligacji. Aby uzyskać więcej informacji dotyczących koligacji [w tym artykule](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** i **UpgradeDomain**: To ograniczenie eliminuje węzłów, jeśli umieszczenie replik w węzłach wskazany spowodowałoby pakowania określonego błędu lub domena uaktualnienia. Kilka przykładów, omawiając to ograniczenie, zostały przedstawione w temacie na [domenach błędów i uaktualnień ograniczenia domeny i wynikowe zachowania](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Zwykle nie powinni dostrzec to ograniczenie usuwania węzła z rozwiązania, ponieważ jest uruchamiana jako optymalizacji domyślnie. Ograniczenie lokalizacji preferowane jest również obecny podczas uaktualniania. Podczas uaktualniania służy przenieść usługi, do której znajdowały się po uruchomieniu uaktualniania.

## <a name="blocklisting-nodes"></a>Węzły Blocklisting
Kolejną wiadomość kondycji raporty Menedżer zasobów klastra jest, gdy węzły zostaną blocklisted. Blocklisting można traktować jako tymczasowe ograniczenie, które jest automatycznie stosowany dla Ciebie. Węzły Uzyskaj blocklisted, gdy występuje dopuszczalnych podczas uruchamiania wystąpienia danego typu usługi. Węzły są blocklisted na podstawie poszczególnych — typ usługi. Węzeł może być blocklisted dla typu w jednej usłudze, ale nie do innego. 

Zobaczysz blocklisting zaczną działać często podczas projektowania: niektórych błędów powoduje awarię podczas uruchamiania hosta usługi. Usługa Service Fabric, próbuje je utworzyć hosta usługi kilka razy, a błąd będzie się powtarzał. Po kilku próbach węzeł pobiera blocklisted, a Menedżer zasobów klastra będzie starał się tworzyć usługi, w innym miejscu. Jeśli ten błąd utrzymuje pojawia się w wielu węzłach, istnieje możliwość, zablokowane przez wszystkie węzły prawidłowy końcowy klastra w górę. Blocklisting można również usunąć wiele węzłów, brak wystarczającej ilości pomyślnie uruchomić usługę, aby spełnić odpowiednią skalę. Zazwyczaj zobaczysz dodatkowe błędy lub ostrzeżenia z wskazujący Menedżer zasobów klastra usługi znajduje się poniżej żądanego repliki lub liczbę wystąpień, jak również kondycji komunikaty wskazujące błąd nowości, który prowadzi to do powstawania blocklisting w pierwszym Umieść.

Blocklisting nie jest trwały warunek. Po kilku minutach węzeł zostanie usunięty z listy blokowania i usługi Service Fabric może ponownie aktywować usługi w tym węźle. Usługi kończą się niepowodzeniem, ten węzeł jest blocklisted dla tego typu Usługa ponownie. 

### <a name="constraint-priorities"></a>Priorytetów ograniczenia

> [!WARNING]
> Zmieniających się priorytetów ograniczenia nie jest zalecane i może mieć znaczący negatywny wpływ na klastrze. Podano następujące informacje do priorytetów ograniczenia domyślne i ich zachowania. 
>

W przypadku wszystkich tych warunków ograniczających możesz może mieć zostały Myślimy "hej — wydaje mi się, że ograniczenia domen błędów są najważniejsze w systemie. Aby upewnić się, że nie naruszyła ograniczenia domeny błędów, chcę naruszają inne ograniczenia."

Ograniczenia można skonfigurować przy użyciu różnych poziomów priorytetu. Są to:

   - "twarde" (0)
   - "soft" (1)
   - "optymalizacji" [2]
   - "off" (-1). 
   
Większość ograniczeń są konfigurowane jako warunki ograniczające twardych domyślnie.

Zmiana priorytetu ograniczenia jest mało prawdopodobna. Było razy tam, gdzie priorytetów ograniczenia konieczne zmiany zwykle w celu obejścia inne usterki lub zachowanie, która została wpływ na środowisko. Zazwyczaj bardzo dobrze działa elastyczności infrastruktury priorytetu ograniczenia, ale nie jest potrzebny często. W większości przypadków wszystko, co znajduje się w ich priorytetów domyślnych. 

Poziomy priorytetów nie oznaczają, że dany ograniczenie _będzie_ naruszona, ani że zawsze będą spełnione. Ograniczenie priorytetów zdefiniować kolejność, w której obowiązują ograniczenia. Priorytety zdefiniować skutków ubocznych, gdy nie jest możliwe spełniać wszystkie ograniczenia. Zwykle wszystkie ograniczenia mogą zostać zrealizowane, chyba że jest coś, co inne dzieje się w środowisku. Przykładowe scenariusze, które będzie prowadzić do naruszenia ograniczeń są ograniczeniami powodujące konflikt, lub dużą liczbą jednoczesnych awarii.

W sytuacjach, zaawansowane można zmienić priorytety ograniczeń. Na przykład załóżmy, że chcesz upewnić się, że koligacji zostałyby zawsze naruszone gdy jest to niezbędne do rozwiązywania problemów z pojemnością węzła. Można to osiągnąć, można ustawić priorytetu ograniczenia koligacji "soft" (1) i pozostaw ograniczenie pojemności, ustawiona na "twarde" (0).

Domyślne wartości priorytetu dla różnych ograniczeń są określone w następujących konfiguracji:

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

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Ograniczenia domeny błędów domeny i uaktualnienia
Menedżer zasobów klastra chce zachować rozłożone między domenach błędów i uaktualnień usług. Modeluje ona to jako ograniczenie w aparacie Menedżer zasobów klastra. Aby uzyskać więcej informacji na temat sposobu ich używania oraz ich określone zachowanie, zapoznaj się z artykułem na [konfiguracji klastra](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Menedżer zasobów klastra może być konieczne umieszczenie replik kilka do domeny uaktualnienia, aby poradzić sobie z uaktualnienia, błędów lub innego naruszenia ograniczeń. Pakowanie do domen błędów lub uaktualnienie, zwykle odbywa się tylko wtedy, gdy istnieje kilka błędów lub innych zmian w systemie, uniemożliwia poprawne umieszczania. Jeśli chcesz uniknąć pakowania, nawet w takich sytuacjach można korzystać ze `RequireDomainDistribution` [zasady rozmieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Należy pamiętać, może to mieć wpływ na dostępność usług i niezawodności jak efekt uboczny, dlatego należy dokładnie należy rozważyć.

Jeśli środowisko jest skonfigurowane prawidłowo, wszystkie ograniczenia są w pełni przestrzegane, nawet podczas uaktualniania. Kluczową kwestią jest, że Menedżer zasobów klastra obserwuje na swoje ograniczenia. W przypadku wykrycia naruszenia natychmiast go zgłasza i próbuje rozwiązać ten problem.

## <a name="the-preferred-location-constraint"></a>Ograniczenie lokalizacji preferowane
Ograniczenie PreferredLocation jest nieco inny, ponieważ ta kolumna ma dwa różne sposoby zastosowania. Jednym z zastosowań to ograniczenie jest podczas uaktualniania aplikacji. Menedżer zasobów klastra automatycznie zarządza tym ograniczeniem podczas uaktualniania. Służy do zapewnienia, że po ukończeniu, że repliki powrót do ich początkowego lokalizacje uaktualnienia. Dotyczy użycia ograniczenie PreferredLocation [ `PreferredPrimaryDomain` zasady rozmieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Oba te są optymalizacje, a więc ograniczenie PreferredLocation jest jedynym ograniczeniem, które są domyślnie ustawione na "Optymalizacji".

## <a name="upgrades"></a>Uaktualnienia
Menedżer zasobów klastra pomaga również w aplikacji i Uaktualnianie klastra, w których ma dwa zadania:

* Upewnij się, że reguły klastra nie są naruszone
* Aby pomóc uaktualnienia bezproblemowe działanie

### <a name="keep-enforcing-the-rules"></a>Zachowaj wymuszania zasad
Główny jest, aby mieć świadomość jest, czy reguły — ścisłe ograniczeń takich jak ograniczeniami dotyczącymi umieszczania i pojemności — nadal są wymuszane podczas uaktualniania. Ograniczeniami dotyczącymi umieszczania upewnij się, że działania Twoich obciążeń tylko którym mogą one, nawet podczas uaktualniania. Gdy usługi są bardzo ograniczony, uaktualnienia może trwać dłużej. Gdy węzeł, na którym jest uruchomiona na lub usługi jest obniżona aktualizacji może być kilka opcji, gdzie można go.

### <a name="smart-replacements"></a>Inteligentne zamiany
Po rozpoczęciu uaktualnienia, Menedżer zasobów tworzy migawkę bieżącego rozmieszczenia klastra. Zgodnie z każdej domeny uaktualnienia zakończeniu próbuje zwrócić usług, które znajdowały się w tej domenie uaktualnienia, aby ich oryginalny układ. W ten sposób jest co najwyżej dwa przejścia usługi podczas uaktualniania. Istnieje jeden Przenieś dotyczy węzła i jednej przenieść z powrotem. Zwracanie klastra lub usługi do jak sprzed uaktualnienia gwarantuje też, że uaktualnienie nie ma wpływu na układ klastra. 

### <a name="reduced-churn"></a>Postęp dokonany w ograniczonym
Innym czynnikiem, który będzie się działo podczas uaktualniania jest to, że Menedżer zasobów klastra wyłącza równoważenie. Zapobieganie równoważenia zapobiega niepotrzebne reakcje do uaktualnienia, takie jak przeniesienie usług do węzłów, które zostały opróżnione do uaktualnienia. Uaktualnienie w przypadku uaktualniania klastra cały klaster nie jest równoważone podczas uaktualniania. Sprawdzanie ograniczenia pozostają aktywne, tylko ruch, oparte na aktywne równoważenia metryki jest wyłączona.

### <a name="buffered-capacity--upgrade"></a>Pojemność buforowanego & uaktualnianie
Zazwyczaj ma zostać uaktualniony do ukończenia, nawet wtedy, gdy klaster jest ograniczona lub w pobliżu pełnej. Zarządzanie pojemność klastra jest szczególnie ważne podczas uaktualniania niż zwykle. W zależności od liczby domen uaktualnienia od 5 do 20 procent pojemność, muszą być migrowane jako uaktualnienie jest wprowadzane za pośrednictwem klastra. Ta praca ma go w innym miejscu. Jest to miejsce pojęcie [buforowane pojemności](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) przydaje się. Pojemność buforowanego są uwzględniane podczas normalnego działania. Menedżer zasobów klastra mogą wypełnić węzłów do ich całkowita pojemność (Korzystanie z buforu) podczas uaktualniania, jeśli to konieczne.

## <a name="next-steps"></a>Kolejne kroki
* Zacznij od początku i [zapoznaj się z wprowadzeniem do usługi Service Fabric Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md)
