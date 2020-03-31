---
title: Zarządzanie ładowaniem aplikacji usługi Azure Service Fabric przy użyciu metryk
description: Dowiedz się, jak skonfigurować metryki w sieci szkieletowej usług i używać ich do zarządzania zużyciem zasobów usługi.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ea21502cdab35b261e20af7f23b7b522f77c6667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451994"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Zarządzanie zużyciem zasobów i ładowaniem w sieci szkieletowej usług za pomocą metryk
*Metryki* są zasoby, które usługi dbają o i które są dostarczane przez węzły w klastrze. Metryka to wszystko, co chcesz zarządzać, aby poprawić lub monitorować wydajność usług. Na przykład można obejrzeć zużycie pamięci, aby wiedzieć, czy usługa jest przeciążona. Innym zastosowaniem jest, aby dowiedzieć się, czy usługa może przenieść gdzie indziej, gdzie pamięć jest mniej ograniczona w celu uzyskania lepszej wydajności.

Takie rzeczy jak użycie pamięci, dysku i procesora CPU są przykładami metryk. Te metryki są metryki fizyczne, zasoby, które odpowiadają zasobów fizycznych w węźle, które muszą być zarządzane. Metryki mogą być również (i często są) metryki logiczne. Metryki logiczne to takie rzeczy jak "MyWorkQueueDepth" lub "MessagesToProcess" lub "TotalRecords". Metryki logiczne są zdefiniowane przez aplikację i pośrednio odpowiadają niektórym zużyciem zasobów fizycznych. Metryki logiczne są wspólne, ponieważ może być trudne do pomiaru i raportowania zużycia zasobów fizycznych na podstawie usługi. Złożoność pomiaru i raportowania własnych metryk fizycznych jest również dlaczego sieci szkieletowej usług zawiera pewne domyślne metryki.

## <a name="default-metrics"></a>Domyślne metryki
Załóżmy, że chcesz rozpocząć pisanie i wdrażanie usługi. W tym momencie nie wiesz, jakie zasoby fizyczne lub logiczne zużywa. To jest w porządku! Menedżer zasobów klastra sieci szkieletowej usług używa niektórych domyślnych metryk, gdy nie określono żadnych innych metryk. Oto one:

  - PrimaryCount - liczba replik podstawowych w węźle 
  - ReplicaCount - liczba całkowitych replik stanowych w węźle
  - Count - count of all service objects (stateless and stateful) on the node Count - count of all service objects (stateless and stateful) on the node

| Metryka | Obciążenie wystąpienia bezstanowego | Stanowe obciążenie dodatkowe | Stanowe obciążenie podstawowe | Waga |
| --- | --- | --- | --- | --- |
| Liczba podstawowa |0 |0 |1 |Wysoka |
| Liczba replik |0 |1 |1 |Medium |
| Liczba |1 |1 |1 |Małe |


W przypadku podstawowych obciążeń domyślne metryki zapewniają przyzwoitą dystrybucję pracy w klastrze. W poniższym przykładzie zobaczmy, co się dzieje, gdy tworzymy dwie usługi i polegaj na domyślnych metrykach równoważenia. Pierwsza usługa jest usługą stanową z trzema partycjami i docelowym rozmiarem zestawu replik trzech. Druga usługa jest usługą bezstanową z jedną partycją i liczbą wystąpień trzech.

Oto, co otrzymujemy:

<center>

![Układ klastra z domyślnymi metrykami][Image1]
</center>

Dodatkowe kwestie, na które należy zwrócić uwagę:
  - Repliki podstawowe dla usługi stanowej są dystrybuowane w kilku węzłach
  - Repliki dla tej samej partycji znajdują się w różnych węzłach
  - Całkowita liczba prawyborów i pomocniczych jest rozdzielana w klastrze
  - Całkowita liczba obiektów usługi są równomiernie przydzielane w każdym węźle

Dobry!

Domyślne metryki działają świetnie jako początek. Jednak domyślne metryki będą prowadzić tylko do tej pory. Na przykład: Jakie jest prawdopodobieństwo, że schemat partycjonowania, który wybrałeś, powoduje idealne równomierne wykorzystanie przez wszystkie partycje? Jaka jest szansa, że obciążenie danej usługi jest stałe w czasie, a nawet takie same na wielu partycjach teraz?

Można uruchomić tylko z domyślnymi metrykami. Jednak zwykle oznacza to, że wykorzystanie klastra jest niższe i bardziej nierówne niż chcesz. Dzieje się tak, ponieważ domyślne metryki nie są adaptacyjne i zakładają, że wszystko jest równoważne. Na przykład podstawowy, który jest zajęty i jeden, który nie jest zarówno przyczynić "1" do PrimaryCount metryki. W najgorszym przypadku przy użyciu tylko metryki domyślne może również spowodować przeliczane węzły, co powoduje problemy z wydajnością. Jeśli chcesz w pełni wykorzystać zakres klastra i uniknąć problemów z wydajnością, musisz użyć niestandardowych metryk i dynamicznego raportowania obciążenia.

## <a name="custom-metrics"></a>Metryki niestandardowe
Metryki są konfigurowane na podstawie wystąpienia usługi dla nazwy podczas tworzenia usługi.

Każda metryka ma pewne właściwości, które ją opisują: nazwę, wagę i obciążenie domyślne.

* Nazwa metryki: nazwa metryki. Nazwa metryki jest unikatowym identyfikatorem metryki w klastrze z perspektywy Menedżera zasobów.
* Waga: Waga metryki określa, jak ważne jest ta metryka względem innych metryk dla tej usługi.
* Obciążenie domyślne: Domyślne obciążenie jest reprezentowane inaczej w zależności od tego, czy usługa jest bezstanowa czy stanowa.
  * W przypadku usług bezstanowych każda metryka ma jedną właściwość o nazwie DefaultLoad
  * W przypadku usług stanowych zdefiniowanych:
    * PrimaryDefaultLoad: Domyślna ilość tej metryki zużywa, gdy jest podstawowa
    * SecondaryDefaultLoad: Domyślna ilość tej metryki zużywa, gdy jest pomocnicza

> [!NOTE]
> Jeśli zdefiniujesz metryki niestandardowe i chcesz _również_ użyć metryk domyślnych, musisz _jawnie_ dodać domyślne metryki z powrotem i zdefiniować wagi i wartości dla nich. Jest tak, ponieważ należy zdefiniować relację między domyślnymi metrykami a metrykami niestandardowymi. Na przykład, może zależy Ci na ConnectionCount lub WorkQueueDepth więcej niż podstawowej dystrybucji. Domyślnie waga metryki PrimaryCount jest wysoka, więc chcesz zmniejszyć go do średniego po dodaniu innych metryk, aby upewnić się, że mają pierwszeństwo.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiowanie danych dla usługi — przykład
Załóżmy, że chcesz następującą konfigurację:

  - Usługa zgłasza metrykę o nazwie "ConnectionCount"
  - Chcesz również użyć domyślnych danych 
  - Zrobiłeś kilka pomiarów i wiesz, że zwykle podstawowa replika tej usługi zajmuje 20 jednostek "ConnectionCount"
  - Secondaries używać 5 jednostek "ConnectionCount"
  - Wiesz, że "ConnectionCount" jest najważniejszą metryką pod względem zarządzania wydajnością tej konkretnej usługi
  - Nadal chcesz repliki podstawowe zrównoważone. Równoważenie replik podstawowych jest ogólnie dobrym pomysłem bez względu na to, co. Pomaga to zapobiec utracie niektórych węzłów lub domeny błędów wpływ na większość replik podstawowych wraz z nim. 
  - W przeciwnym razie domyślne metryki są

Oto kod, który można napisać, aby utworzyć usługę z tej konfiguracji metryki:

Kod:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Powyższe przykłady i reszta tego dokumentu opisano zarządzanie metrykami na podstawie usługi dla osób o nazwie. Istnieje również możliwość definiowania metryki dla usług na poziomie _typu_ usługi. Jest to realizowane przez określenie ich w manifestach usługi. Definiowanie metryk poziomu typu nie jest zalecane z kilku powodów. Pierwszym powodem jest to, że nazwy metryki są często specyficzne dla środowiska. Jeśli nie ma umowy firmy w miejscu, nie można mieć pewności, że metryka "Rdzenie" w jednym środowisku nie jest "MiliCores" lub "CoReS" w innych. Jeśli metryki są zdefiniowane w manifeście, należy utworzyć nowe manifesty dla środowiska. Zwykle prowadzi to do rozprzestrzeniania się różnych manifestów z niewielkimi różnicami, co może prowadzić do trudności w zarządzaniu.  
>
> Obciążenia metryki są często przypisywane na podstawie wystąpienia dla usługi dla o nazwie. Załóżmy na przykład, że tworzysz jedno wystąpienie usługi dla CustomerA, który planuje używać go tylko lekko. Załóżmy również, że tworzysz inny dla CustomerB, który ma większe obciążenie pracą. W takim przypadku prawdopodobnie chcesz dostosować domyślne obciążenia dla tych usług. Jeśli masz metryki i obciążenia zdefiniowane za pomocą manifestów i chcesz obsługiwać ten scenariusz, wymaga różnych typów aplikacji i usług dla każdego klienta. Wartości zdefiniowane w czasie tworzenia usługi zastępują te zdefiniowane w manifeście, dzięki czemu można go użyć do ustawienia określonych wartości domyślnych. Jednak powoduje to wartości zadeklarowane w manifestach, aby nie odpowiadać tym, z którymi usługa faktycznie działa. Może to prowadzić do zamieszania. 
>

Przypominamy: jeśli chcesz po prostu użyć domyślnych danych, nie musisz w ogóle dotykać kolekcji metryk ani robić nic specjalnego podczas tworzenia usługi. Domyślne metryki są używane automatycznie, gdy nie zdefiniowano żadnych innych. 

Teraz przejdźmy przez każde z tych ustawień bardziej szczegółowo i porozmawiajmy o zachowaniu, na które ma wpływ.

## <a name="load"></a>Ładowanie
Cały punkt definiowania metryki jest reprezentować pewne obciążenie. *Obciążenie* jest, jaka część danej metryki jest zużywana przez niektóre wystąpienia usługi lub repliki w danym węźle. Obciążenie można skonfigurować w niemal każdym punkcie. Przykład:

  - Ładowanie można zdefiniować podczas tworzenia usługi. Nazywa się to _obciążeniem domyślnym_.
  - Informacje metryki, w tym obciążenia domyślne, dla usługi można zaktualizować po utworzeniu usługi. Jest to tak zwane _aktualizowanie usługi_. 
  - Obciążenia dla danej partycji można zresetować do wartości domyślnych dla tej usługi. Nazywa się to _resetowaniem obciążenia partycji_.
  - Obciążenie można zgłaszać dynamicznie na obiekt usługi w czasie wykonywania. Jest to tak zwane _obciążenie raportowania_. 
  
Wszystkie te strategie mogą być używane w ramach tej samej usługi w ciągu swojego okresu istnienia. 

## <a name="default-load"></a>Obciążenie domyślne
*Domyślne obciążenie* jest, ile metryki każdy obiekt usługi (wystąpienie bezstanowe lub repliki stanowej) tej usługi zużywa. Menedżer zasobów klastra używa tego numeru do ładowania obiektu usługi, dopóki nie otrzyma innych informacji, takich jak raport obciążenia dynamicznego. W przypadku prostszych usług obciążenie domyślne jest definicją statyczną. Domyślne obciążenie nigdy nie jest aktualizowane i jest używane przez cały okres istnienia usługi. Obciążenia domyślne doskonale sprawdzają się w scenariuszach planowania pojemności prostych, w których określone ilości zasobów są dedykowane różnym obciążeniom i nie zmieniają się.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zarządzania pojemnościami i definiowania pojemności węzłów w klastrze, zobacz [ten artykuł](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Menedżer zasobów klastra umożliwia usługom stanowym określenie innego obciążenia domyślnego dla ich ustawień ceł i pomocniczych. Usługi bezstanowe można określić tylko jedną wartość, która ma zastosowanie do wszystkich wystąpień. W przypadku usług stanowych domyślne obciążenie repliki podstawowej i pomocniczej są zazwyczaj różne, ponieważ repliki wykonują różne rodzaje pracy w każdej roli. Na przykład prawybory zwykle służą zarówno odczyty i zapisy i obsługiwać większość obciążenia obliczeniowego, podczas gdy pomocnicze nie. Zwykle domyślne obciążenie repliki podstawowej jest wyższe niż obciążenie domyślne dla replik pomocniczych. Liczby rzeczywiste powinny zależeć od własnych pomiarów.

## <a name="dynamic-load"></a>Obciążenie dynamiczne
Załóżmy, że usługa działa od jakiegoś czasu. Z pewnym monitoringiem zauważyłeś, że:

1. Niektóre partycje lub wystąpienia danej usługi zużywają więcej zasobów niż inne
2. Niektóre usługi mają obciążenie, które zmienia się w czasie.

Istnieje wiele rzeczy, które mogą powodować tego typu wahania obciążenia. Na przykład różne usługi lub partycje są skojarzone z różnymi klientami o różnych wymaganiach. Obciążenie może również ulec zmianie, ponieważ ilość pracy, jaką usługa wykonuje, zmienia się w ciągu dnia. Niezależnie od przyczyny, zwykle nie ma jednego numeru, który można użyć domyślnie. Jest to szczególnie ważne, jeśli chcesz uzyskać jak najwięcej wykorzystania z klastra. Każda wartość, którą wybierzesz dla obciążenia domyślnego, jest nieprawidłowa przez pewien czas. Nieprawidłowe obciążenia domyślne powodują, że Menedżer zasobów klastra jest objęty przydziałem zasobów lub w obszarze alokacji. W rezultacie masz węzły, które są w pełni lub w ramach wykorzystywane, mimo że Menedżer zasobów klastra uważa, że klaster jest zrównoważony. Obciążenia domyślne są nadal dobre, ponieważ dostarczają pewnych informacji o początkowym umieszczeniu, ale nie są kompletną historią dla rzeczywistych obciążeń. Aby dokładnie przechwycić zmieniające się wymagania dotyczące zasobów, Menedżer zasobów klastra umożliwia każdemu obiektowi usługi aktualizowanie własnego obciążenia w czasie wykonywania. Jest to tak zwane dynamiczne raportowanie obciążenia.

Raporty obciążenia dynamicznego umożliwiają replikom lub wystąpieniom dostosowanie ich alokacji/zgłoszonego obciążenia metryk w okresie ich istnienia. Replika usługi lub wystąpienie, które było zimne i nie wykonuje żadnej pracy zwykle raport, że był przy użyciu niskich ilości danej metryki. Zajęta replika lub wystąpienie zgłosi, że używają więcej.

Raportowanie obciążenia na replikę lub wystąpienie umożliwia Menedżerowi zasobów klastra reorganizację poszczególnych obiektów usługi w klastrze. Reorganizacja usług pomaga zapewnić, że otrzymują zasoby, których potrzebują. Zajęty usługi skutecznie dostać się do "odzyskiwania" zasobów z innych replik lub wystąpień, które są obecnie zimne lub robi mniej pracy.

W ramach usług niezawodnych kod do dynamicznego raportowania obciążenia wygląda następująco:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Usługa może raportować dowolne metryki zdefiniowane dla niego w czasie tworzenia. Jeśli usługa raportuje obciążenie dla metryki, która nie jest skonfigurowana do użycia, sieci szkieletowej usług ignoruje ten raport. Jeśli istnieją inne metryki zgłaszane w tym samym czasie, które są prawidłowe, te raporty są akceptowane. Kod usługi można mierzyć i raportować wszystkie metryki, które wie, jak, a operatorzy można określić konfigurację metryki do użycia bez konieczności zmiany kodu usługi. 

### <a name="updating-a-services-metric-configuration"></a>Aktualizowanie konfiguracji metryki usługi
Lista metryk skojarzonych z usługą i właściwości tych metryk można aktualizować dynamicznie, gdy usługa jest na żywo. Pozwala to na eksperymentowanie i elastyczność. Oto kilka przykładów, kiedy jest to przydatne:

  - wyłączanie danych z raportem błędnego błędu dla określonej usługi
  - ponowne konfigurowanie wag metryk na podstawie pożądanego zachowania
  - włączenie nowej metryki dopiero po wdrożeniu i zatwierdzeniu kodu za pomocą innych mechanizmów
  - zmiana domyślnego obciążenia usługi na podstawie obserwowanego zachowania i zużycia

Główne interfejsy API do zmiany `FabricClient.ServiceManagementClient.UpdateServiceAsync` konfiguracji metryki są w języku C# i `Update-ServiceFabricService` w programie PowerShell. Niezależnie od informacji określonych za pomocą tych interfejsów API zastępuje istniejące informacje metryki dla usługi natychmiast. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Mieszanie domyślnych wartości obciążenia i raportów obciążenia dynamicznego
Obciążenia domyślne i obciążenia dynamiczne mogą być używane dla tej samej usługi. Gdy usługa wykorzystuje zarówno domyślne raporty obciążenia, jak i obciążenia dynamicznego, domyślne obciążenie służy jako szacowanie, dopóki nie pojawią się raporty dynamiczne. Domyślne obciążenie jest dobre, ponieważ daje Menedżerowi zasobów klastra coś do pracy. Domyślne obciążenie umożliwia Menedżerowi zasobów klastra umieszczanie obiektów usługi w dobrych lokalizacjach podczas ich tworzenia. Jeśli nie podano żadnych domyślnych informacji o obciążeniu, umieszczenie usług jest skutecznie losowe. Gdy raporty ładowania przychodzą później początkowe losowe umieszczenie jest często błędne i Menedżer zasobów klastra musi przenieść usługi.

Weźmy nasz poprzedni przykład i zobaczmy, co się stanie, gdy dodamy pewne niestandardowe metryki i dynamiczne raportowanie obciążenia. W tym przykładzie używamy "MemoryInMb" jako przykład metryki.

> [!NOTE]
> Pamięć jest jednym z metryk systemu, które sieci szkieletowej usług może [regulować zasobów,](service-fabric-resource-governance.md)a raportowanie go samodzielnie jest zazwyczaj trudne. W rzeczywistości nie oczekujemy, że raport na zużycie pamięci; Pamięć jest w tym miejscu używana jako pomoc w poznawaniu możliwości Menedżera zasobów klastra.
>

Załóżmy, że początkowo utworzono usługę stanową za pomocą następującego polecenia:

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Przypominamy, że ta składnia jest ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Zobaczmy, jak może wyglądać jeden z możliwych układów klastra:

<center>

![Saldo klastra z metrykami domyślnymi i niestandardowymi][Image2]
</center>

Kilka rzeczy, które warto zauważyć:

* Repliki pomocnicze w obrębie partycji mogą mieć własne obciążenie
* Ogólnie rzecz biorąc, dane wyglądają na zrównoważone. W przypadku pamięci stosunek obciążenia maksymalnego do minimalnego wynosi 1,75 (węzeł o największym obciążeniu to N3, najmniej to N2, a 28/16 = 1,75).

Jest kilka rzeczy, które musimy jeszcze wyjaśnić:

* Co decydowało, czy stosunek 1,75 był rozsądny, czy nie? Skąd Menedżer zasobów klastra wie, czy jest wystarczająco dobry, czy jest więcej pracy do wykonania?
* Kiedy odbywa się równoważenie?
* Co to znaczy, że pamięć była ważona "High"?

## <a name="metric-weights"></a>Wagi metryczne
Śledzenie tych samych metryk w różnych usługach jest ważne. Ten widok globalny umożliwia Menedżerowi zasobów klastra śledzenie zużycia w klastrze, równoważenie zużycia między węzłami i zapewnianie, że węzły nie przestają pojemności. Jednak usługi mogą mieć różne widoki co do ważności tej samej metryki. Ponadto w klastrze z wieloma metrykami i wieloma usługami doskonale wyważone rozwiązania mogą nie istnieć dla wszystkich metryk. Jak Menedżer zasobów klastra powinien obsługiwać te sytuacje?

Wagi metryki umożliwiają Menedżerowi zasobów klastra zdecydować, jak zrównoważyć klaster, gdy nie ma idealnej odpowiedzi. Wagi metryki umożliwiają również Menedżerowi zasobów klastra inne równoważenie określonych usług. Metryki mogą mieć cztery różne poziomy wagi: Zero, Niski, Średni i Wysoki. Metryka o wadze Zero nic nie przyczynia się, gdy rozważa, czy rzeczy są zrównoważone, czy nie. Jednak jego obciążenie nadal przyczynia się do zarządzania wydajnością. Metryki z zerową wagą są nadal przydatne i są często używane jako część zachowania usługi i monitorowania wydajności. [Ten artykuł](service-fabric-diagnostics-event-generation-infra.md) zawiera więcej informacji na temat korzystania z metryk do monitorowania i diagnostyki usług. 

Rzeczywisty wpływ różnych wag metryk w klastrze jest, że Menedżer zasobów klastra generuje różne rozwiązania. Wagi metryki informują Menedżera zasobów klastra, że niektóre metryki są ważniejsze niż inne. Gdy nie ma idealnego rozwiązania, Menedżer zasobów klastra może preferować rozwiązania, które lepiej równoważą wyższe metryki ważone. Jeśli usługa uważa, że określona metryka jest nieistotna, może znaleźć ich użycie tej metryki nierównowagą. Dzięki temu inna usługa, aby uzyskać równomierny rozkład niektórych metryki, która jest dla niego ważna.

Przyjrzyjmy się przykładowi niektórych raportów obciążenia i jak różne wagi metryki powoduje różne alokacje w klastrze. W tym przykładzie widzimy, że przełączanie względnej wagi metryk powoduje, że Menedżer zasobów klastra do tworzenia różnych uzgodnień usług.

<center>

![Przykład wagi metrycznej i jego wpływ na rozwiązania równoważące][Image3]
</center>

W tym przykładzie istnieją cztery różne usługi, wszystkie raportowanie różnych wartości dla dwóch różnych metryk, MetricA i MetricB. W jednym przypadku wszystkie usługi definiują MetricA jest najważniejszy (Waga = Wysoki) i MetricB jako nieistotne (Waga = Niski). W rezultacie widzimy, że Menedżer zasobów klastra umieszcza usługi, dzięki czemu MetricA jest lepiej zrównoważony niż MetricB. "Lepiej wyważone" oznacza, że MetricA ma niższe ma niższe odchylenie standardowe niż MetricB. W drugim przypadku odwracamy wagi metryczne. W rezultacie Menedżer zasobów klastra zamienia usługi A i B, aby wymyślić alokację, w której MetricB jest lepiej zrównoważony niż MetricA.

> [!NOTE]
> Wagi metryki określają, jak Menedżer zasobów klastra powinien równoważyć, ale nie podczas równoważenia powinno się zdarzyć. Aby uzyskać więcej informacji na temat równoważenia, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globalne wagi metryczne
Załóżmy, że ServiceA definiuje MetricA jako waga wysoka, a ServiceB ustawia wagę dla MetricA do niskiego lub zerowego. Jaka jest rzeczywista waga, która kończy się przyzwyczaić?

Istnieje wiele wag, które są śledzone dla każdej metryki. Pierwsza waga jest zdefiniowana dla metryki podczas tworzenia usługi. Druga waga to waga globalna, która jest obliczana automatycznie. Menedżer zasobów klastra używa obu tych wag podczas oceniania rozwiązań. Biorąc pod uwagę obie wagi jest ważne. Dzięki temu Menedżer zasobów klastra do równoważenia każdej usługi zgodnie z własnymi priorytetami, a także upewnij się, że klaster jako całość jest przydzielany poprawnie.

Co by się stało, gdyby Menedżer zasobów klastra nie dbał zarówno o równowagę globalną, jak i lokalną? Cóż, łatwo jest konstruować rozwiązania, które są globalnie zrównoważone, ale które skutkują słabą równowagą zasobów dla poszczególnych usług. W poniższym przykładzie przyjrzyjmy się usłudze skonfigurowaną z tylko domyślnymi metrykami i zobaczmy, co się stanie, gdy bierze się pod uwagę tylko saldo globalne:

<center>

![Wpływ globalnego rozwiązania][Image4]
</center>

W najlepszym przykładzie opartym tylko na równowadze globalnej klaster jako całość jest rzeczywiście zrównoważony. Wszystkie węzły mają taką samą liczbę całkowitej liczby i tę samą liczbę replik. Jednak jeśli spojrzeć na rzeczywisty wpływ tej alokacji nie jest tak dobry: utrata dowolnego węzła wpływa na określone obciążenie nieproporcjonalnie, ponieważ wyjmuje wszystkie jego prawybory. Na przykład jeśli pierwszy węzeł nie powiedzie się trzy prawybory dla trzech różnych partycji usługi Circle zostaną utracone. Z drugiej strony Triangle i Hexagon usługi mają ich partycje tracą replikę. Powoduje to żadnych zakłóceń, inne niż konieczności odzyskania repliki w dół.

W dolnym przykładzie Menedżer zasobów klastra rozesłał repliki na podstawie salda globalnego i na usługę. Podczas obliczania wyniku rozwiązania daje większość wagi do globalnego rozwiązania i (konfigurowalne) część do poszczególnych usług. Saldo globalne dla metryki jest obliczane na podstawie średniej wag metryki z każdej usługi. Każda usługa jest zrównoważona zgodnie z własnymi określonymi wagami metrycznymi. Gwarantuje to, że usługi są zrównoważone w sobie zgodnie z własnymi potrzebami. W rezultacie, jeśli ten sam pierwszy węzeł ulegnie awarii, błąd jest dystrybuowany między wszystkimi partycjami wszystkich usług. Wpływ na każdy z nich jest taki sam.

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definiowanie metryk defragmentacji jest jednym ze sposobów konsolidacji obciążenia w węzłach zamiast rozprzestrzeniania go. Aby dowiedzieć się, jak skonfigurować defragmentację, zapoznaj się [z tym artykułem](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z artykułem na temat [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Zacznij od początku i [uzyskaj wprowadzenie do Menedżera zasobów klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-introduction.md)
- Koszt przemieszczenia jest jednym ze sposobów sygnalizowania Menedżerowi zasobów klastra, że niektóre usługi są droższe niż inne. Aby dowiedzieć się więcej o kosztach ruchu, zapoznaj się [z tym artykułem](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
