---
title: Menedżer zasobów klastra — integracja z zarządzaniem
description: Omówienie punktów integracji między Menedżerem zasobów klastra a zarządzaniem siecią szkieletową usług.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 50751c7d23797a597dc5e2d209c1e3eecf6f7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258748"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Integracja menedżera zasobów klastra z zarządzaniem klastrem sieci szkieletowej usług
Menedżer zasobów klastra sieci szkieletowej usług nie zwiększa uaktualnień w sieci szkieletowej usług, ale jest zaangażowany. Pierwszym sposobem, w jaki Menedżer zasobów klastra pomaga w zarządzaniu, jest śledzenie żądanego stanu klastra i usług w nim przebywanych. Menedżer zasobów klastra wysyła raporty kondycji, gdy nie można umieścić klastra w żądanej konfiguracji. Na przykład jeśli nie ma wystarczającej pojemności, Menedżer zasobów klastra wysyła ostrzeżenia zdrowotne i błędy wskazujące na problem. Kolejny element integracji ma do czynienia z tym, jak działają aktualizacje. Menedżer zasobów klastra zmienia jego zachowanie nieznacznie podczas uaktualniania.  

## <a name="health-integration"></a>Integracja zdrowotna
Menedżer zasobów klastra stale śledzi reguły zdefiniowane do umieszczania usług. Śledzi również pozostałą pojemność dla każdej metryki w węzłach i w klastrze i w klastrze jako całości. Jeśli nie może spełnić tych zasad lub jeśli nie ma wystarczającej pojemności, ostrzeżenia zdrowotne i błędy są emitowane. Na przykład jeśli węzeł jest ponad pojemność i Menedżer zasobów klastra spróbuje naprawić sytuację przez przeniesienie usług. Jeśli nie można poprawić sytuacji emituje ostrzeżenie o kondycji wskazujące, który węzeł jest ponad pojemność i dla których metryk.

Innym przykładem ostrzeżeń zdrowotnych Menedżera zasobów jest naruszenie ograniczeń umieszczania. Na przykład jeśli zdefiniowano ograniczenie umieszczania `“NodeColor == Blue”`(na przykład) i Menedżer zasobów wykryje naruszenie tego ograniczenia, emituje ostrzeżenie o kondycji. Dotyczy to ograniczeń niestandardowych i ograniczeń domyślnych (takich jak ograniczenia domeny błędów i domeny uaktualnienia).

Oto przykład jednego z takich raportów o kondycji. W takim przypadku raport kondycji jest dla jednej z partycji usługi systemowej. Komunikat kondycji wskazuje, że repliki tej partycji są tymczasowo spakowane do zbyt mało domen uaktualnienia.

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

Oto, co mówi nam ta wiadomość o zdrowiu:

1. Wszystkie repliki same są zdrowe: Każdy ma AggregatedHealthState: Ok
2. Ograniczenie dystrybucji domeny uaktualnienia jest obecnie naruszane. Oznacza to, że określona domena uaktualnienia ma więcej replik z tej partycji niż powinna.
3. Który węzeł zawiera replikę powodującą naruszenie. W tym przypadku jest to węzeł o nazwie "Node.8"
4. Czy uaktualnienie jest obecnie dzieje się dla tej partycji ("Currently Upgrading -- false")
5. Zasady dystrybucji dla tej usługi: "Zasady dystrybucji — pakowanie". Jest to regulowane `RequireDomainDistribution` przez [zasady umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Pakowanie" wskazuje, że w tym przypadku DomainDistribution _nie_ było wymagane, więc wiemy, że zasady umieszczania nie został określony dla tej usługi. 
6. Kiedy doszło do raportu - 8/10/2015 19:13:02

Informacje takie jak ten uprawnień alarmuje, że ogień w produkcji, aby poinformować, że coś poszło nie tak, a także jest używany do wykrywania i zatrzymywanie złych uaktualnień. W takim przypadku chcielibyśmy sprawdzić, czy możemy dowiedzieć się, dlaczego Menedżer zasobów musiał spakować repliki do domeny uaktualnienia. Zazwyczaj pakowanie jest przejściowe, ponieważ węzły w innych domenach uaktualnienia były w dół, na przykład.

Załóżmy, że Menedżer zasobów klastra próbuje umieścić niektóre usługi, ale nie ma żadnych rozwiązań, które działają. Gdy usługi nie mogą być umieszczone, zwykle z jednego z następujących powodów:

1. Niektóre stan przejściowy uniemożliwił prawidłowe umieszczenie tego wystąpienia usługi lub repliki
2. Wymagania dotyczące umieszczania usługi są niezaspokojalne.

W takich przypadkach raporty kondycji z Menedżera zasobów klastra pomagają określić, dlaczego nie można umieścić usługi. Nazywamy ten proces sekwencją eliminacji ograniczeń. Podczas niego system przechodzi przez skonfigurowane ograniczenia wpływające na usługę i rejestruje, co eliminują. W ten sposób, gdy usługi nie są w stanie być umieszczone, można zobaczyć, które węzły zostały wyeliminowane i dlaczego.

## <a name="constraint-types"></a>Typy ograniczeń
Porozmawiajmy o każdym z różnych ograniczeń w tych raportach dotyczących kondycji. Komunikaty dotyczące kondycji związane z tymi ograniczeniami zostaną wyświetlone, gdy nie można umieścić replik.

* **ReplicaExclusionStatic** i **ReplicaExclusionDynamic**: Te ograniczenia wskazują, że rozwiązanie zostało odrzucone, ponieważ dwa obiekty usługi z tej samej partycji musiałyby zostać umieszczone w tym samym węźle. Nie jest to dozwolone, ponieważ wtedy awaria tego węzła będzie nadmiernie wpływać na tę partycję. ReplicaExclusionStatic i ReplicaExclusionDynamic są prawie tej samej reguły i różnice naprawdę nie ma znaczenia. Jeśli widzisz sekwencję eliminacji ograniczeń zawierającą ograniczenie ReplicaExclusionStatic lub ReplicaExclusionDynamic, Menedżer zasobów klastra uważa, że nie ma wystarczającej liczby węzłów. Wymaga to pozostałych rozwiązań do korzystania z tych nieprawidłowych miejsc docelowych, które są niedozwolone. Inne ograniczenia w sekwencji zwykle mówią nam, dlaczego węzły są eliminowane w pierwszej kolejności.
* **PlacementConstraint:** Jeśli widzisz ten komunikat, oznacza to, że wyeliminowaliśmy niektóre węzły, ponieważ nie były one zgodne z ograniczeniami umieszczania usługi. Możemy wynajdyć aktualnie skonfigurowane ograniczenia umieszczania jako część tego komunikatu. Jest to normalne, jeśli zdefiniowano ograniczenie umieszczania. Jednak jeśli ograniczenie umieszczania jest niepoprawnie powodując zbyt wiele węzłów do wyeliminowania jest to, jak można zauważyć.
* **NodeCapacity:** To ograniczenie oznacza, że Menedżer zasobów klastra nie może umieścić repliki na wskazanych węzłach, ponieważ spowoduje to umieszczenie ich nad pojemnością.
* **Koligacja:** To ograniczenie wskazuje, że nie możemy umieścić repliki na węzłach, których dotyczy problem, ponieważ spowodowałoby to naruszenie ograniczenia koligacji. Więcej informacji na temat powinowactwa znajduje się w [tym artykule](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** i **UpgradeDomain:** To ograniczenie eliminuje węzły, jeśli umieszczenie repliki na wskazanych węzłach spowodowałoby pakowanie w określonej domenie błędu lub uaktualnienia. Kilka przykładów omawiających to ograniczenie przedstawiono w temacie dotyczących [ograniczeń domeny błędów i uaktualnienia oraz wynikowego zachowania](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Zwykle nie powinno być widoczne to ograniczenie usuwające węzły z rozwiązania, ponieważ domyślnie działa jako optymalizacja. Ograniczenie preferowanej lokalizacji jest również obecne podczas uaktualniania. Podczas uaktualniania jest on używany do przenoszenia usług z powrotem do miejsca, w którym były podczas uaktualniania.

## <a name="blocklisting-nodes"></a>Węzły listy zablokowanych
Inny komunikat kondycji raportowany przez Menedżer zasobów klastra jest, gdy węzły są blokowane. Możesz myśleć o blocklisting jako tymczasowe ograniczenie, które jest automatycznie stosowane dla Ciebie. Węzły uzyskać blocklisted gdy występują powtarzające się błędy podczas uruchamiania wystąpień tego typu usługi. Węzły są blokowane na podstawie typu usługi. Węzeł może być zablokowany dla jednego typu usługi, ale nie dla innego. 

Zobaczysz blocklisting kick często podczas rozwoju: niektóre błędy powoduje, że host usługi do awarii podczas uruchamiania. Sieć szkieletowa usług próbuje utworzyć hosta usługi kilka razy, a błąd nadal występuje. Po kilku próbach węzeł zostanie umieszczony na liście zablokowanych, a Menedżer zasobów klastra spróbuje utworzyć usługę w innym miejscu. Jeśli ten błąd nadal dzieje się w wielu węzłach, jest możliwe, że wszystkie prawidłowe węzły w klastrze kończy się zablokowany. Lista zablokowanych można również usunąć tak wiele węzłów, że za mało można pomyślnie uruchomić usługę, aby spełnić żądaną skalę. Zazwyczaj będą widoczne dodatkowe błędy lub ostrzeżenia z Menedżera zasobów klastra wskazujące, że usługa znajduje się poniżej żądanej liczby replik lub wystąpień, a także komunikaty o kondycji wskazujące, co powoduje błąd, który prowadzi do listy zablokowanych w pierwszym Miejsce.

Lista zablokowanych nie jest stałym warunkiem. Po kilku minutach węzeł zostanie usunięty z listy zablokowanych i sieci szkieletowej usług może ponownie aktywować usługi w tym węźle. Jeśli usługi nadal nie powiodą się, węzeł zostanie ponownie zablokowany dla tego typu usługi. 

### <a name="constraint-priorities"></a>Priorytety ograniczeń

> [!WARNING]
> Zmiana priorytetów ograniczeń nie jest zalecana i może mieć znaczący niekorzystny wpływ na klaster. Poniższe informacje są dostarczane w odniesieniu do domyślnych priorytetów ograniczeń i ich zachowania. 
>

Z wszystkich tych ograniczeń, może być myślenie "Hej - Myślę, że ograniczenia domeny błędów są najważniejsze w moim systemie. Aby upewnić się, że ograniczenie domeny błędów nie jest naruszane, jestem gotów naruszyć inne ograniczenia."

Ograniczenia można skonfigurować z różnymi poziomami priorytetu. Są to:

   - "twardy" (0)
   - "miękki" (1)
   - "optymalizacja" (2)
   - "off" (-1). 
   
Większość ograniczeń są domyślnie skonfigurowane jako twarde ograniczenia.

Zmiana priorytetu ograniczeń jest rzadkością. Były czasy, w których priorytety ograniczeń potrzebne do zmiany, zwykle do obejść niektóre inne błędy lub zachowanie, które miało wpływ na środowisko. Ogólnie elastyczność infrastruktury priorytetowej ograniczenia działa bardzo dobrze, ale nie jest to często potrzebne. W większości przypadków wszystko jest w ich domyślnych priorytetach. 

Poziomy priorytetu nie oznaczają, że dane ograniczenie _zostanie_ naruszone, ani że zawsze będzie ono spełniane. Priorytety ograniczeń definiują kolejność, w której ograniczenia są wymuszane. Priorytety definiują kompromisy, gdy nie można spełnić wszystkich ograniczeń. Zazwyczaj wszystkie ograniczenia mogą być spełnione, chyba że coś innego dzieje się w środowisku. Niektóre przykłady scenariuszy, które doprowadzą do naruszenia ograniczeń są sprzeczne ograniczenia lub dużą liczbę równoczesnych błędów.

W sytuacjach zaawansowanych można zmienić priorytety ograniczeń. Na przykład załóżmy, że chcesz upewnić się, że koligacja będzie zawsze naruszana, gdy jest to konieczne do rozwiązania problemów z pojemnością węzła. Aby to osiągnąć, można ustawić priorytet ograniczenia koligacji na "miękkie" (1) i pozostawić ograniczenie pojemności ustawione na "twarde" (0).

Domyślne wartości priorytetu dla różnych ograniczeń są określone w następującej konfiguracji:

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

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Ograniczenia domeny błędów i uaktualnienia domeny
Menedżer zasobów klastra chce, aby usługi były rozłożone między domeny błędów i uaktualnienia. Modeluje to jako ograniczenie wewnątrz aparatu Menedżera zasobów klastra. Aby uzyskać więcej informacji na temat sposobu ich użycia i ich określonego zachowania, zapoznaj się z artykułem o [konfiguracji klastra](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

Menedżer zasobów klastra może być konieczne spakowanie replik kilka do domeny uaktualnienia w celu radzenia sobie z uaktualnień, błędów lub innych ograniczeń naruszenia. Pakowanie do domen błędów lub uaktualnienia zwykle odbywa się tylko wtedy, gdy istnieje kilka awarii lub innych zmian w systemie uniemożliwiających prawidłowe umieszczenie. Jeśli chcesz zapobiec pakowaniu nawet w takich sytuacjach, `RequireDomainDistribution` możesz skorzystać z [zasad umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Należy pamiętać, że może to mieć wpływ na dostępność usługi i niezawodność jako efekt uboczny, więc należy rozważyć go dokładnie.

Jeśli środowisko jest poprawnie skonfigurowane, wszystkie ograniczenia są w pełni przestrzegane, nawet podczas uaktualniania. Najważniejsze jest to, że Menedżer zasobów klastra jest pilnowanie ograniczeń. Po wykryciu naruszenia natychmiast zgłasza je i próbuje rozwiązać problem.

## <a name="the-preferred-location-constraint"></a>Preferowane ograniczenie lokalizacji
Ograniczenie PreferredLocation jest nieco inny, ponieważ ma dwa różne zastosowania. Jednym z zastosowań tego ograniczenia jest podczas uaktualniania aplikacji. Menedżer zasobów klastra automatycznie zarządza tym ograniczeniem podczas uaktualniania. Służy do zapewnienia, że po zakończeniu uaktualnień, że repliki powrócić do swoich początkowych lokalizacji. Inne zastosowanie ograniczenia PreferredLocation dotyczy [ `PreferredPrimaryDomain` zasad umieszczania](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Oba są optymalizacje i dlatego ograniczenie PreferredLocation jest jedynym ograniczeniem ustawionym domyślnie na "Optymalizacja".

## <a name="upgrades"></a>Uaktualnień
Menedżer zasobów klastra pomaga również podczas uaktualniania aplikacji i klastra, podczas których ma dwa zadania:

* upewnić się, że zasady klastra nie są zagrożone
* spróbuj pomóc uaktualnieniu przejść płynnie

### <a name="keep-enforcing-the-rules"></a>Kontynuuj egzekwowanie zasad
Najważniejsze, o których należy pamiętać, jest to, że zasady – ścisłe ograniczenia, takie jak ograniczenia i możliwości umieszczania – są nadal egzekwowane podczas uaktualniania. Ograniczenia umieszczania zapewniają, że obciążenia działają tylko tam, gdzie są dozwolone, nawet podczas uaktualniania. Gdy usługi są bardzo ograniczone, uaktualnienia mogą trwać dłużej. Gdy usługa lub węzeł, na którym jest uruchomiona, jest sprowadzony do aktualizacji, może istnieć kilka opcji, do których może się udać.

### <a name="smart-replacements"></a>Inteligentne zamienniki
Po uruchomieniu uaktualnienia Menedżer zasobów wykonuje migawkę bieżącego układu klastra. Po zakończeniu każdej domeny uaktualnienia próbuje przywrócić usługi, które znajdowały się w tej domenie uaktualnienia, do ich pierwotnego układu. W ten sposób istnieją co najwyżej dwa przejścia dla usługi podczas uaktualniania. Istnieje jeden ruch z węzła, którego dotyczy problem i jeden przenieść z powrotem. Zwracanie klastra lub usługi do sposobu, w jaki było przed uaktualnieniem również zapewnia, że uaktualnienie nie ma wpływu na układ klastra. 

### <a name="reduced-churn"></a>Zmniejszenie zmian
Inną rzeczą, która dzieje się podczas uaktualniania jest to, że Menedżer zasobów klastra wyłącza równoważenie. Zapobieganie równoważenia zapobiega niepotrzebne reakcje na uaktualnienie się, jak przenoszenie usług do węzłów, które zostały opróżnione dla uaktualnienia. Jeśli uaktualnienie, o którym mowa, jest uaktualnieniem klastra, cały klaster nie jest zrównoważony podczas uaktualniania. Kontrole ograniczeń pozostają aktywne, wyłącza się tylko ruch oparty na proaktywnym równoważeniu metryk.

### <a name="buffered-capacity--upgrade"></a>Buforowana pojemność & uaktualnieniu
Ogólnie rzecz biorąc, chcesz, aby uaktualnienie było zakończone, nawet jeśli klaster jest ograniczony lub bliski pełnej. Zarządzanie pojemnością klastra jest jeszcze ważniejsze podczas uaktualniania niż zwykle. W zależności od liczby domen uaktualnienia, od 5 do 20 procent pojemności muszą być migrowane jako uaktualnienia rolkach za pośrednictwem klastra. Ta praca musi gdzieś iść. W tym miejscu przydatne jest pojęcie [buforowanych zdolności](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) produkcyjnych. Wydajność buforowana jest przestrzegana podczas normalnej pracy. Menedżer zasobów klastra może wypełnić węzły do ich całkowitej pojemności (zużywa buforu) podczas uaktualniania, jeśli to konieczne.

## <a name="next-steps"></a>Następne kroki
* Zacznij od początku i [uzyskaj wprowadzenie do Menedżera zasobów klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-introduction.md)
