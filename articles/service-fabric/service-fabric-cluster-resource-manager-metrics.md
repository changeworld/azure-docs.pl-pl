---
title: Zarządzanie za pomocą metryki obciążenia Azure mikrousługi | Dokumentacja firmy Microsoft
description: Więcej informacji o konfigurowaniu i metryki w sieci szkieletowej usług do zarządzania zużycie zasobów usługi.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7457a820d9179248eab976ceec64f6b7a4a38563
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643342"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Zarządzanie zużycia zasobów i obciążenia w sieci szkieletowej usług o metryki
*Metryki* zasobów, które z usług najważniejsze informacje, które są udostępniane przez węzły w klastrze. Metryka to wszystko, co chcesz zarządzać w celu poprawy lub monitorować wydajność usług. Na przykład może oglądać zużycie pamięci, aby dowiedzieć się, jeśli usługa jest przeciążona. Użyj innego jest aby dowiedzieć się, czy usługa może przenieść innym miejscu, gdzie pamięci jest mniejsza ograniczone, aby uzyskać lepszą wydajność.

Np. użycia pamięci, dysku i procesora CPU przedstawiono metryki. Te metryki są metryki fizycznych, zasobów, które odpowiadają zasoby fizyczne na węźle, który musi być zarządzane. Metryki mogą być również (i często są) logicznej metryki. Metryki logiczne są elementy, takie jak "MyWorkQueueDepth" lub "MessagesToProcess" lub "TotalRecords". Metryki logiczne są zdefiniowane przez aplikację i pośrednio odpowiadają niektórych zużycie zasobów fizycznych. Metryki logiczne są często używane, ponieważ może być trudne do miary i raportu zużycia zasobów fizycznych na podstawie-service. Złożoność pomiarów i raportowania własne fizycznych metryki jest również, dlaczego Usługa Service Fabric realizuje niektóre domyślne metryki.

## <a name="default-metrics"></a>Domyślne metryki
Załóżmy, że chcesz rozpocząć pisanie i wdrażania usługi. W tym momencie nie wiadomo, jakie zasoby fizyczne lub logiczne zużywa. To świetnie! Menedżer zasobów klastra sieci szkieletowej usług używa niektóre domyślne metryki, jeśli określono nie innych metryk. Oto one:

  - PrimaryCount — liczba replik podstawowych, w węźle 
  - ReplicaCount — liczba całkowita stanowe replik w węźle
  - Liczba — liczba wszystkich obiektów usługi (bezstanowe i stanowe) w węźle

| Metryka | Obciążenia bezstanowe wystąpienia | Stanowe dodatkowej obciążenia | Stanowego obciążenia podstawowego | Waga |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |0 |
| ReplicaCount |0 |1 |1 |0 |
| Licznik |1 |1 |1 |0 |


W przypadku obciążeń podstawowych domyślne metryki Podaj zadowalający dystrybucji pracy w klastrze. W poniższym przykładzie Zobacz, co się stanie po możemy utworzyć dwie usługi i polegać na domyślne metryki równoważenia. Pierwszej usługi jest usługi stanowej trzy partycji i replik docelowy Ustaw rozmiar trzech. Drugi usługa jest usługi bezstanowej z jednej partycji i liczby wystąpień trzy.

Oto, możesz uzyskać:

<center>
![Układ klastra z domyślne metryki][Image1]
</center>

Niektóre rzeczy do uwzględnienia:
  - Repliki podstawowej dla usługi stanowej są rozproszone na kilku węzłów
  - Repliki dla tej samej partycji są w różnych węzłach
  - Całkowita liczba kolory podstawowe i pomocnicze bazy danych jest dystrybuowane w klastrze
  - Całkowita liczba obiektów usługi równomiernie są przydzielane na każdym węźle

Dobrym!

Domyślne metryki ponosić działa jako rozpoczęcia. Jednak domyślne metryki tylko prowadzi należy do tej pory. Na przykład: jakie jest prawdopodobieństwo, że podziału schemat można pobrać wyników doskonale nawet wykorzystania przez wszystkie partycje? Co to ryzyko, że obciążenia dla danej usługi jest stałe, wraz z upływem czasu, lub w taki sam nawet na między wieloma partycjami teraz?

Można uruchomić z właśnie domyślne metryki. Jednak taka zwykle oznacza, że Twoje użycie klastra mniejszą i nierówna bardziej, niż chcesz. Jest to spowodowane domyślne metryki nie adaptacyjną i zakładają, że wszystko, co jest równoważne. Na przykład podstawowego, który jest zajęty i, który nie jest zarówno przyczyniają się do Metryka PrimaryCount "1". W najgorszym przypadku za pomocą domyślne metryki może również spowodować overscheduled węzłów, co powoduje problemy z wydajnością. Jeśli interesuje Cię uzyskanie maksymalne wykorzystanie klastra i unikanie problemów z wydajnością, należy użyć niestandardowych metryk i raportowania dynamicznego obciążenia.

## <a name="custom-metrics"></a>Metryki niestandardowe
Metryki są konfigurowane na poszczególnych o nazwie usług wystąpień podczas tworzenia usługi.

Niektóre właściwości opisujące go ma wszystkie metryki: nazwę, waga i obciążenia domyślne.

* Nazwa metryki: Nazwa metryki. Nazwa metryki jest unikatowym identyfikatorem dla metryki w klastrze z punktu widzenia menedżera zasobów.
* Waga: Waga metryki definiuje, jak ważna ta metryka jest określana względem innych metryk dla tej usługi.
* Domyślne obciążenia: Obciążenia domyślne odpowiada inaczej w zależności od tego, czy usługa jest bezstanowych i stanowych.
  * W przypadku usług bezstanowych wszystkie metryki ma jedną właściwość o nazwie DefaultLoad
  * Dla stanowych usług można zdefiniować:
    * PrimaryDefaultLoad: Pobiera tej usługi domyślnej ilości ta metryka, w podstawowym
    * SecondaryDefaultLoad: Pobiera tej usługi domyślnej ilości ta metryka, czasie pomocniczego

> [!NOTE]
> Jeśli zdefiniujesz metryki niestandardowe i chcesz _również_ metryki domyślnego, konieczne _jawnie_ Dodaj domyślne metryki kopii i zdefiniuj wagi i wartości dla nich. Jest to spowodowane należy zdefiniować relację między domyślne metryki i Twoje metryki niestandardowe. Na przykład może być potrzebne informacje ConnectionCount lub WorkQueueDepth więcej niż podstawowy dystrybucji. Domyślnie waga metryki PrimaryCount jest wysoki, więc chcesz zmniejszyć na nośnik, po dodaniu z metryki, aby upewnić się, że ich mają pierwszeństwo.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiowanie metryki dla usługi — przykład
Załóżmy, że ma następującą konfigurację:

  - Metryka o nazwie "ConnectionCount" raportów usługi
  - Należy użyć domyślne metryki 
  - Przycisk niektórych pomiarów i dowiedzieć się, że zwykle podstawową replikę tej usługi zajmuje 20 jednostek "ConnectionCount"
  - Pomocnicze bazy danych użyj 5 jednostek "ConnectionCount"
  - Wiesz, że "ConnectionCount" jest najważniejszych Metryka pod względem wydajności tej konkretnej usługi zarządzania
  - Nadal chcesz zrównoważonym replik podstawowych. Równoważenie replik podstawowych zazwyczaj dobrym pomysłem jest bez względu na okoliczności. Pomaga to zapobiec utracie niektóre domeny węzła lub fault z wpływające na większości replik podstawowych wraz z jej. 
  - W przeciwnym razie domyślne metryki są poprawnie

Oto kod, który można zapisać do utworzenia usługi w tej konfiguracji metryki:

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

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Powyższe przykłady i pozostałej części tego dokumentu opisano zarządzanie metryki na podstawie ciągu o nazwie — usługi. Istnieje również możliwość zdefiniowania metryki dla usług w usłudze _typu_ poziom. Jest to osiągnąć, określając je w manifestach Twojej usługi. Definiowanie metryki na poziomie typu nie jest zalecane z kilku powodów. Po pierwsze urząd są nazwy metryki często określonego środowiska. Jeżeli nie istnieje ostateczne kontraktu w miejscu, nie może być pewności, że metryka "Rdzeni" w jednym środowisku nie w innych "MiliCores" lub "Rdzeni". Jeśli Twoje metryki są zdefiniowane w manifeście, należy utworzyć nowe manifesty na środowisko. Prowadzi to zwykle mnożenie różnych manifestów tylko drobne różnice, które mogą prowadzić do zarządzania trudności.  
>
> Metryka obciążenia często są przypisywane na podstawie poszczególnych o nazwie usług wystąpień. Na przykład załóżmy, że utworzenie jednego wystąpienia usługi dla CustomerA, która planuje się rzadziej. Również Załóżmy, że utworzyć inny dla CustomerB mającego większe obciążenie. W takim przypadku będzie prawdopodobnie chcesz dostosować obciążenia domyślne dla tych usług. Jeśli masz metryki i obciążeń zdefiniowany za pomocą manifestów, a chcesz obsługiwać ten scenariusz, wymaga innej aplikacji i typów usług dla każdego klienta. Wartości zdefiniowane w czasie tworzenia usługi przesłaniają akcje zdefiniowane w manifeście, więc można który służy do ustawiania określonych wartości domyślnych. Jednak operacją powoduje, że zadeklarowany w manifestach odpowiadające nie usługa działa z wartości. Może to prowadzić do pomyłek. 
>

Dla przypomnienia: Jeśli chcesz użyć domyślne metryki, nie trzeba było w ogóle touch kolekcji metryki też wykonywać specjalne podczas tworzenia usługi. Domyślne metryki uzyskać użyć automatycznie, gdy nie zdefiniowano żadnych innych użytkowników. 

Teraz przejdźmy do każdego z tych ustawień bardziej szczegółowo i omówione ma to wpływ na zachowanie.

## <a name="load"></a>Ładowanie
Całe punkt definiowania metryk jest do reprezentowania niektóre obciążenia. *Obciążenia* jest, jaka część danej metryki jest używany przez niektóre wystąpienia usługi lub replikę z danego węzła. Obciążenia można skonfigurować w dowolnym momencie. Na przykład:

  - Obciążenia można zdefiniować podczas tworzenia usługi. Ta metoda jest wywoływana _obciążenia domyślne_.
  - Informacje dotyczące pomiaru, takich jak obciążeń domyślne usługi można zaktualizować po utworzeniu usługi. Ta metoda jest wywoływana _uaktualnianie usługi_. 
  - Obciążenia dla danej partycji można przywrócić wartości domyślne dla tej usługi. Ta metoda jest wywoływana _zresetowania obciążenia partycji_.
  - Obciążenia mogą być zgłaszane na poszczególnych obiektów usługi dynamicznie w czasie wykonywania. Ta metoda jest wywoływana _raportowania obciążenia_. 
  
Wszystkie strategie można w tej samej usługi swojego okresu istnienia. 

## <a name="default-load"></a>Domyślne obciążenia
*Domyślne obciążenia* jest znacznie metryki zużywa każdego obiektu usługi (bezstanowych wystąpienia lub stanowych repliki) tej usługi. Menedżer zasobów klastra używa tego numeru obciążenia obiektu usługi, dopóki nie odbierze inne informacje, takie jak raportu dynamicznego obciążenia. W przypadku prostszych usług obciążenie domyślne jest definicji statycznych. Obciążenia domyślne nigdy nie są aktualizowane i jest używany przez czas ich istnienia usługi. Domyślne ładuje Wielkiej działa proste pojemności planowanie scenariuszy, w którym niektórych ilości zasobów są przeznaczone wyłącznie dla różnych obciążeń i nie należy zmieniać.

> [!NOTE]
> Aby uzyskać więcej informacji dotyczących zarządzania pojemności i definiowanie pojemności dla węzłów w klastrze, zobacz [w tym artykule](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Menedżer zasobów klastra umożliwia stanowych usług do określenia obciążenia różne domyślne kolory podstawowe i pomocnicze bazy danych. Usługi bezstanowej można określić tylko jedną wartość, która ma zastosowanie do wszystkich wystąpień. Dla usługi stanowej, obciążenia domyślny dla podstawowego i pomocniczego repliki są zwykle różnych ponieważ repliki są różnego rodzaju pracy w każdej roli. Na przykład kolory podstawowe zwykle służą odczytów i zapisów i obsługę większości obciążeń obliczeniowych, podczas gdy pomocnicze bazy danych nie. Zazwyczaj obciążenia domyślny dla repliki podstawowej jest wyższa niż obciążenia domyślny dla replik pomocniczych. Liczb rzeczywistych powinien są zależne od swoich własnych pomiarów.

## <a name="dynamic-load"></a>Obciążenie dynamicznego
Załóżmy, że działała usługi przez pewien czas. Niektóre monitorowania z zostały zauważyć, że:

1. Niektóre partycje lub wystąpienia danej usługi zużywać więcej zasobów niż inne
2. Niektóre usługi mają obciążenia, która różni się w czasie.

Istnieje wiele rzeczy, które mogły spowodować tego rodzaju wahania obciążenia. Na przykład różnych usług lub partycje są skojarzone z różnych klientów z innymi wymaganiami. Obciążenia można również zmienić, ponieważ ilość pracy, usługa nie może być różna w ciągu dnia. Niezależnie od przyczyny jest zazwyczaj nie jeden numer używanego dla domyślnego. Jest to szczególnie istotne, jeśli chcesz pobrać większości wykorzystania spoza klastra. Wartość, wybrać dla domyślnego obciążenia jest niewłaściwy pewien czas działanie. Nieprawidłowe domyślne ładuje wynik w klastrze Menedżera zasobów nad lub pod alokacji zasobów. W związku z tym masz węzłów, które są powyżej lub poniżej wykorzystywane mimo, że Menedżer zasobów klastra sądzi, że klaster jest rozmieszczana. Domyślne obciążenia są nadal dobrej, ponieważ zapewniają niektóre informacje do umieszczenia początkowej, ale nie są one zakończenie wątku dla obciążeń prawdziwe. Do przechwytywania dokładnie zmieniających się wymagań dotyczących zasobów, Menedżer zasobów klastra umożliwia każdego obiektu usługi można zaktualizować własną obciążenia w czasie wykonywania. Jest to obciążenie dynamicznego raportowania.

Raporty Obciążenie dynamicznego Zezwalaj repliki lub wystąpienia, aby dostosować ich ładowania metryki alokacji/zgłoszone za pośrednictwem ich istnienia. Repliki usługi lub wystąpienia, który był niskich i bezczynny będzie zazwyczaj raport, aby korzystał z niskim ilości danej metryki. Replika zajęta lub wystąpienie rozpoczną przesyłanie raportów o tym, że używają więcej.

Raportowania obciążenia dla repliki lub wystąpienie umożliwia Menedżera zasobów klastra, aby uporządkować obiekty poszczególnych usług w klastrze. Reorganizacja usług gwarantuje, że otrzymują zasoby, które wymagają. Usługi zajęty skutecznie Uzyskaj "odzyskiwaniu" zasoby od innych repliki lub wystąpienia, które są aktualnie ten mniej pracy lub niskie.

W ramach niezawodnej usługi kodu w celu raportowania obciążenia dynamicznie wygląda następująco:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Usługa może raportować na dowolnym zdefiniowane w czasie tworzenia metryki. Jeśli obciążenia raportów usługi dla metryki, który nie jest skonfigurowany do użycia, sieć szkieletowa usług ignoruje tego raportu. Jeśli istnieją inne metryki zgłoszone w tym samym czasie, które są prawidłowe, te raporty są akceptowane. Kod usługi można mierzyć i zgłaszać wszystkie metryki wiadomo, jak i operatory można będzie określić konfigurację metryki, aby użyć bez konieczności zmiany kodu usługi. 

### <a name="updating-a-services-metric-configuration"></a>Trwa aktualizowanie konfiguracji metryki usługi
Lista metryki skojarzony z usługą i właściwości tych metryk może być aktualizowany dynamicznie, gdy usługa jest na żywo. Dzięki temu eksperymenty i elastyczność. Przykłady gdy jest to przydatne są:

  - wyłączanie Metryka z raportem buggy określonej usługi
  - ponowne konfigurowanie wag metryki na podstawie żądanego zachowania
  - Włączanie nowej Metryka tylko wtedy, gdy kod został już wdrożony i zweryfikować za pośrednictwem innych mechanizmów
  - Zmiana obciążenia domyślne usługi na podstawie obserwowanych zachowania i zużycia

Główne interfejsy API do zmiany konfiguracji metryki są `FabricClient.ServiceManagementClient.UpdateServiceAsync` w języku C# i `Update-ServiceFabricService` w programie PowerShell. Niezależnie od informacje z poniższych interfejsów API natychmiast zastępuje istniejące informacje metryki dla usługi. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Mieszanie domyślne wartości obciążenia i raportów obciążenia dynamiczne
Domyślne obciążenia i obciążeń dynamicznych może służyć do tej samej usługi. Gdy usługa korzysta zarówno obciążenia domyślne i raportów obciążenia dynamicznego, obciążenia domyślna służy jako szacunkową dopóki wyświetlani dynamicznych raportów. Domyślne obciążenia jest prawidłowa, ponieważ umożliwia Menedżera zasobów klastra coś do pracy z. Obciążenia domyślne umożliwia Menedżera zasobów klastra można umieścić w dobrej lokalizacje obiektów usługi, po ich utworzeniu. Jeśli podano żadnych informacji obciążenia domyślne, umieszczania usług jest skutecznie losowych. Nadejściu raportów obciążenia później początkowe położenie losowe często jest nieprawidłowy, a Menedżer zasobów klastra musi przenieść usług.

Teraz pobrać naszych poprzedni przykład i zobacz, co się stanie po dodamy niektóre metryki niestandardowe i raportowania dynamicznego obciążenia. W tym przykładzie używamy "MemoryInMb" jako przykład metryka.

> [!NOTE]
> Pamięć jest jednym z metryki systemu, które można sieci szkieletowej usług [regulują zasobów](service-fabric-resource-governance.md), i raportowania samodzielnie jest zazwyczaj trudne. Nie faktycznie Oczekujemy na raport dotyczący zużycie pamięci; Pamięć jest używana jako pomoc do zapoznawanie funkcje Menedżera zasobów klastra.
>

Załóżmy zakładają, że początkowo utworzyliśmy usługi stanowej przy użyciu następującego polecenia:

Środowiska PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Dla przypomnienia ta składnia jest ("MetricName, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Zobaczmy, jakie jeden układ możliwe klastra może wyglądać jak:

<center>
![Klaster zrównoważony z domyślnych i niestandardowych metryk][Image2]
</center>

Kilka rzeczy, które są warto zauważyć:

* Każdej repliki pomocniczej w partycji może mieć własne obciążenia
* Ogólne metryki Szukaj zrównoważone. Pamięci, stosunek minimalnego i maksymalnego obciążenia jest 1,75 (węzeł z najbardziej obciążenia jest N3, jest najmniej N2 i 28/16 = 1,75).

Istnieje kilka kwestii, które nadal trzeba wyjaśnić:

* Co to ustalić, czy stosunek 1,75 było uzasadnione? Jak Menedżer zasobów klastra sprawdzić czy jest wystarczająca, czy istnieje więcej pracy?
* Gdy równoważenia stanie?
* Co oznacza że pamięci zostało ważona "High"?

## <a name="metric-weights"></a>Metryki wagi
Ważne jest śledzenie te same metryki w różnych usługach. Tym globalnych widoku jest, co umożliwia Menedżera zasobów klastra, aby śledzić użycie w klastrze, równoważenie zużycie na węzłach i upewnij się, że węzły nie przekazywane pojemności. Jednak usługi mogą mieć różne widoki dotyczące znaczenie tej samej metryki. Ponadto w klastrze z wieloma metryki i wiele usług doskonale zrównoważonym rozwiązania może nie istnieć dla wszystkich metryki. Jak Menedżer zasobów klastra powinna obsługiwać tych sytuacji?

Metryki wag Zezwalaj Menedżera zasobów klastra, aby określić sposób równoważenie klastra po doskonałe odpowiedzi. Metryki wag również umożliwić Menedżera zasobów klastra, aby równoważyć określonych usług inaczej. Metryki mogą mieć czterech wagi różnych poziomów: Zero, niski, średni i wysoki. Metryka o wadze Zero wspiera nic podczas określania, czy elementy są zrównoważonym lub nie. Jednak jej obciążenia nadal przyczyniają się do zarządzania pojemności. Metryki Zero wagę są nadal przydatne i są często używane jako część zachowanie usługi i monitorowania wydajności. [W tym artykule](service-fabric-diagnostics-event-generation-infra.md) zamieszczono więcej informacji dotyczących stosowania metryk do monitorowania i diagnostyki usług. 

Rzeczywistego wpływ różne metryki obciążenia w klastrze jest, że Menedżer zasobów klastra generuje różnych rozwiązań. Wag metryki Poinformuj Menedżera zasobów klastra, czy niektóre metryki są ważniejsze od innych użytkowników. Gdy istnieje doskonałe rozwiązanie Menedżera zasobów klastra można preferowane rozwiązania, które saldo wyższej metryki ważoną lepiej. Jeśli usługa sądzi określonej metryki jest bez znaczenia, może znajdzie ich używania tego metryki imbalanced. Dzięki temu inna usługa nawet dystrybucji niektóre metryki ważne jest, aby go pobrać.

Oto przykład niektórych raportów obciążenia i metrykę, jak różne przeprowadzi wyniki w różnych alokacji w klastrze. W tym przykładzie przedstawiono, że przełączenie względną wagę metryki powoduje Menedżera zasobów klastra, aby utworzyć różne zasady usług.

<center>
![Przykład waga metryki i jak wpływa na rozwiązań do równoważenia][Image3]
</center>

W tym przykładzie istnieją cztery różne usługi, wszystkie wartości różnych raportowania dla dwóch różnych metryki MetricA i MetricB. W przypadku jednego wszystkich usług definiowania MetricA jest jednym z najważniejszych (wagi = wysoki) i MetricB jako nieistotnych (wagi = niski). W związku z tym widzimy umieszczenie usługi w Menedżera zasobów klastra, więc MetricA lepiej jest rozmieszczana niż MetricB. "Lepiej zrównoważonym" oznacza, że MetricA ma mniejszy ma odchylenie standardowe niższe niż MetricB. W drugim przypadku możemy odwrotnej wag metryki. W związku z tym Menedżer zasobów klastra zamienia usług A i B, korzystać z alokacji gdzie MetricB lepiej jest zrównoważonym niż MetricA.

> [!NOTE]
> Metryki wag określić, jak Menedżer zasobów klastra powinna saldo, ale nie podczas równoważenia powinno się zdarzyć. Aby uzyskać więcej informacji na temat funkcji równoważenia, zapoznaj się z [w tym artykule](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globalne wag metryki
Załóżmy, że ServiceA definiuje MetricA jako wagi wysokiej i ServiceB ustawia grubość MetricA niski lub Zero. Co to jest rzeczywista wagi, która kończy się używany?

Istnieje wiele wagi, śledzonych dla każdego metryki. Pierwsza waga jest definiowane metryki podczas tworzenia usługi. Inne waga jest globalne wagi, która jest obliczana automatycznie. Menedżer zasobów klastra używa oba te wagi podczas oceniania rozwiązania. Biorąc pod uwagę zarówno wag jest ważne. Dzięki temu Menedżer zasobów klastra do zrównoważenia poszczególnych usług zgodnie z jego własnej priorytetów i upewnij się również poprawną alokację klastra jako całość.

Co się stanie, jeśli Menedżera zasobów klastra nie zależy Ci na zachowaniu saldo zarówno globalne i lokalne? Jest również łatwe do utworzenia rozwiązania, które są globalnie zrównoważonym, ale co spowodować równowadze zasobów niska dla poszczególnych usług. W poniższym przykładzie załóżmy przyjrzeć się skonfigurowano tylko domyślne metryki usługi i zobacz, co się dzieje, gdy tylko globalne saldo jest uznawany za:

<center>
![Wpływ globalne rozwiązanie tylko][Image4]
</center>

W przykładzie top w oparciu jedynie globalnego równoważenia w rzeczywistości jest rozmieszczana klastra jako całość. Wszystkie węzły mają tej samej liczby kolory podstawowe i te same repliki łączna liczba. Jednak jeśli przyjrzymy się rzeczywista wpływ tej alokacji nie jest tak dobre: utraty dowolnego węzła ma wpływ określonego obciążenia nieproporcjonalnie, ponieważ zajmuje całe jego kolory podstawowe. Na przykład jeśli pierwszy węzeł nie powiedzie się trzy kolory podstawowe dla trzech różnych partycji usługi koło wszystkie zostałyby utracone. Z drugiej strony usługi trójkąt i sześciokąt mają ich utratę repliki partycji. Powoduje to nie przerw w działaniu, oprócz posiadania odzyskać dół repliki.

W przykładzie dolnej Menedżera zasobów klastra został rozesłany replik oparte na globalnym i dla usługi równoważenia. Podczas obliczania wyniku rozwiązanie zapewnia najbardziej wagi globalne rozwiązanie i części (można konfigurować) do poszczególnych usług. Globalne saldo metryka jest obliczany na podstawie średnią metryki wag z każdej usługi. Każda usługa jest rozmieszczana zgodnie z własną zdefiniowanych wag metryki. Daje to pewność, że usługi równoważenia między sobą zgodnie z ich własnych potrzeb. W związku z tym jeśli sam pierwszego węzła nie powiedzie się błędu jest dystrybuowana do wszystkich partycji wszystkich usług. Wpływ na każdy jest taka sama.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usługi [informacje na temat konfigurowania usługi](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definiowanie metryki defragmentacji jest jednym ze sposobów skonsolidować węzłów zamiast go rozkładanie obciążenia. Aby dowiedzieć się, jak skonfigurować defragmentacji, zajrzyj do [w tym artykule](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Aby dowiedzieć się o jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz artykuł na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Rozpocznij od początku i [wprowadzenie do usługi sieci szkieletowej klastra Menedżera zasobów](service-fabric-cluster-resource-manager-introduction.md)
- Koszt przeniesienia jest jednym ze sposobów sygnalizowania Menedżera zasobów do klastra, że niektórych usług są droższe do przeniesienia od innych. Aby dowiedzieć się więcej na temat koszt przeniesienia, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
