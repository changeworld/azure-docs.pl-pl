---
title: Zarządzanie obciążenia aplikacji usługi Azure Service Fabric przy użyciu metryk | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sposobu konfigurowania i używania metryki w usłudze Service Fabric do użycia zasobów usługi zarządzania.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 0d622ea6-a7c7-4bef-886b-06e6b85a97fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1a61de6b0b6f73e112dd69108272ded3a67497e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60516763"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Zarządzanie zużycia zasobów i obciążenia w usłudze Service Fabric za pomocą metryk
*Metryki* są zasoby, które swoje najważniejsze usługi informacje, które są dostarczane przez węzły w klastrze. Metryka to wszystkich elementów, które mają być zarządzane, aby zwiększyć lub monitorowanie wydajności usługi. Na przykład może obserwować zużycie pamięci, aby dowiedzieć się, jeśli usługa jest przeciążony. Innym zastosowaniem jest ustalenie, czy usługa może przenieść innym miejscu, gdzie pamięci jest mniejsza ograniczonego w celu uzyskania lepszej wydajności.

Np. pamięci, dysku i procesora CPU są przykładami metryki. Te metryki są metryki fizycznych, zasoby, które odnoszą się do zasobów fizycznych na węzeł, który muszą być zarządzane. Metryki można też (i często są) miar logiczne. Metryki logiczne są elementów, takich jak "MyWorkQueueDepth" lub "MessagesToProcess" lub "TotalRecords". Metryki logiczne są zdefiniowane przez aplikację i pośrednio odpowiadają użycie niektórych zasobów fizycznych. Miar logiczne są powszechne, ponieważ może być trudne do miary i raport użycia zasobów fizycznych na podstawie za daną usługę. Złożoność pomiaru i raportowania metryk fizycznego jest też, dlaczego Usługa Service Fabric udostępnia kilka domyślnych metryk.

## <a name="default-metrics"></a>Domyślne metryki
Załóżmy, że chcesz rozpocząć pisanie i wdrażanie usługi. Na tym etapie nie wiadomo, jakie zasoby fizyczne lub logiczne zużywa. To świetnie! Menedżer zasobów klastra usługi Service Fabric używa niektóre metryki domyślnej, jeśli określono nie innych metryk. Oto one:

  - PrimaryCount — liczba replikami podstawowymi na węzeł 
  - ReplicaCount - liczba całkowita stanowych replik w węźle
  - Liczba — łącznej liczby wszystkich obiektów usługi (stanowe i bezstanowe) w węźle

| Metryka | Obciążenia bezstanowe wystąpienia | Stanowe dodatkowej obciążenia | Stanowe podstawowego obciążenia | Waga |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Wysoka |
| ReplicaCount |0 |1 |1 |Średni |
| Count |1 |1 |1 |Małe |


W przypadku obciążeń podstawowych domyślnych metryk oferują znośnego rozłożenia pracy w klastrze. W poniższym przykładzie Zobaczmy, co się stanie, gdy firma Microsoft tworzy się dwie usługi i polegają na domyślnych metryk dla równoważenia. Pierwszej usługi jest stanowej usługi za pomocą trzech partycje i repliki docelowej Ustaw rozmiar trzy. Druga usługa jest usługę bezstanową, za pomocą jednej partycji i liczby wystąpień trzech.

Oto co uzyskujesz:

<center>

![Układ klastra przy użyciu domyślnych metryk][Image1]
</center>

Niektóre kwestie, należy zwrócić uwagę:
  - Repliki podstawowej dla usługi stanowej są rozproszone na kilku węzłów.
  - Repliki dla tej samej partycji znajdują się w różnych węzłach
  - Całkowita liczba kolory podstawowe i pomocnicze bazy danych jest dystrybuowane w klastrze
  - Całkowita liczba obiektów usługi równomiernie są przydzielane w każdym węźle

Dobre!

Metryki domyślnej świetnie działa jako rozpoczęcia. Jednak domyślnych metryk tylko przeprowadzi należy do tej pory. Na przykład: Jakie jest prawdopodobieństwo, że podział schemat możesz pobrać wyniki doskonale nawet wykorzystanie przez wszystkie partycje? Jakie jest prawdopodobieństwo, że obciążenie dla danej usługi jest stałe, wraz z upływem czasu, lub w taki sam nawet na wielu partycjach teraz?

Można uruchomić przy użyciu tylko domyślnych metryk. Jednak to zazwyczaj oznacza, że wykorzystaniu klastra dolnej i bardziej nierówne, niż chcesz. Jest to spowodowane domyślnych metryk nie są funkcje adaptacyjnego sterowania i zakładają, że wszystko jest równoważne. Na przykład podstawowy, który jest zajęty i taki, który nie jest zarówno przyczyniają się do metryki PrimaryCount "1". W najgorszym przypadku za pomocą domyślnych metryk może również spowodować overscheduled węzłów powodujące problemy z wydajnością. Jeśli interesuje Cię uzyskanie maksymalnie wykorzystać możliwości klastra i unikanie problemów z wydajnością, należy użyć niestandardowych metryk i dynamiczne raportowanie obciążenia.

## <a name="custom-metrics"></a>Metryki niestandardowe
Metryki są skonfigurowane na podstawie poszczególnych o nazwie service wystąpień, podczas tworzenia usługi.

Dowolnej metryce ma kilka właściwości, które je: nazwę, waga i obciążenia domyślne.

* Nazwa metryki: Nazwa metryki. Nazwa metryki jest unikatowy identyfikator dla metryki w ramach klastra z punktu widzenia menedżera zasobów.
* Waga: Waga metryki definiuje, jak ważny jest ta metryka względem innych metryk dla tej usługi.
* Załaduj domyślne: Obciążenia domyślne jest reprezentowany w różny sposób w zależności od tego, czy usługa jest bezstanowe lub stanowe.
  * W przypadku usług bezstanowych wszystkie metryki ma jedną właściwość o nazwie DefaultLoad
  * W przypadku usług stanowych, jaką zdefiniujesz:
    * PrimaryDefaultLoad: Domyślna ilość ta metryka ta usługa pobiera w podstawowym
    * SecondaryDefaultLoad: Domyślna ilość ta metryka ta usługa pobiera w pomocniczy

> [!NOTE]
> Jeśli zdefiniujesz metryki niestandardowe, a użytkownik chce _również_ Użyj metryki domyślnej, musisz _jawnie_ dodawania domyślnych metryk kopii i definiowania dla nich wartości i wagi. Jest to spowodowane należy zdefiniować relację między domyślnych metryk i metryki niestandardowe. Na przykład może być interesujące Cię ConnectionCount lub WorkQueueDepth więcej niż podstawowy dystrybucji. Domyślnie waga metryki PrimaryCount jest wysoki, więc chcesz ograniczyć na nośnik, po dodaniu innych metryk, aby upewnić się, że ich wyższy priorytet.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Definiowanie metryki dla usługi — przykład
Załóżmy, że chcesz użyć następującej konfiguracji:

  - Usługa raporty Metryka o nazwie "ConnectionCount"
  - Należy zawsze używać domyślnych metryk 
  - Wykonałeś niektóre pomiarów i dowiedzieć się, że zwykle podstawową replikę tej usługi trwa maksymalnie 20 jednostek "ConnectionCount"
  - Pomocnicze bazy danych użyj 5 jednostek "ConnectionCount"
  - Wiesz, że "ConnectionCount" jest najważniejsze metryki pod względem wydajności tej konkretnej usługi zarządzania
  - Nadal chcesz replikami podstawowymi zrównoważone. Równoważenie replik podstawowych ogólnie jest dobry pomysł, niezależnie od tego, co. Pozwala to zapobiec utratę niektórych domeny węzła lub błędów z wpływ na większość replikami podstawowymi zapotrzebowaniu. 
  - W przeciwnym razie domyślnych metryk są w dobrym stanie

Poniżej przedstawiono kod, który należy napisać utworzyć usługę za pomocą tej konfiguracji metryki:

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

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> Powyższe przykłady i pozostałej części tego dokumentu opisano zarządzanie metryki na podstawie poszczególnych-o nazwie service. Istnieje również możliwość zdefiniowania metryki dla usługi w usłudze _typu_ poziom. Jest to osiągnąć, określając je w manifestach Twojej usługi. Definiowanie typu metryki poziomu nie jest zalecane z kilku powodów. Pierwszym powodem jest to, że nazwy metryki są często specyficznymi dla środowiska. Jeżeli nie istnieje ostateczne umowy w miejscu, nie może być pewności, że metryki "Rdzeni" w jednym środowisku nie w innych "MiliCores" lub "Rdzeni". Jeśli metryki są zdefiniowane w manifeście, musisz utworzyć nowe manifesty na środowisko. Prowadzi to zwykle rozprzestrzenianie różnych manifesty z niewielkimi modyfikacjami, co może prowadzić do zarządzania trudności.  
>
> Metryki obciążenia często są przypisane na podstawie poszczególnych o nazwie service wystąpień. Załóżmy na przykład, że możesz utworzyć jedno wystąpienie usługi dla CustomerA, który zamierza rzadziej go używać. Ponadto Załóżmy, że możesz utworzyć inny dla CustomerB, który ma większe obciążenie. W takim przypadku prawdopodobnie chcesz dostosować domyślne obciążenia za te usługi. Jeśli masz metryki i obciążeń, zdefiniowanych za pomocą manifesty i mają być obsługiwane w tym scenariuszu, wymaga innej aplikacji i typów usług dla każdego klienta. Wartości definiowany w czasie tworzenia usługi przesłaniają akcje zdefiniowane w manifeście, dzięki czemu można użyć, aby skonfigurować określone ustawienia domyślne. Jednak tą operacją powoduje, że wartości zadeklarowane w manifestach nie zgadzać się usługa jest wykonywany z. Może to prowadzić do pomyłek. 
>

Przypomnienie: Jeśli chcesz użyć domyślnych metryk, nie trzeba w ogóle zbierania metryk na wszystkich lub wykonywać żadnych specjalnych czynności podczas tworzenia usługi. Metryki domyślnej Pobierz używane automatycznie, gdy nie inne są zdefiniowane. 

Teraz przejdźmy przez każde z tych ustawień, które bardziej szczegółowo i omówimy zachowanie, które ma to wpływ.

## <a name="load"></a>Ładowanie
Celem definiowania metryk jest do reprezentowania niektórych obciążenia. *Obciążenia* to, ile dana metryka jest używane przez niektóre wystąpienie usługi lub replikę z danego węzła. Obciążenia można skonfigurować w niemal dowolnym momencie. Na przykład:

  - Obciążenia można zdefiniować podczas tworzenia usługi. Jest to nazywane _obciążenia domyślne_.
  - Informacje dotyczące pomiaru obciążeniami domyślny, w tym dla usługi zaktualizowane po utworzeniu usługi. Jest to nazywane _uaktualnianie usługi_. 
  - Obciążenia dla danej partycji można zresetować do wartości domyślnych dla danej usługi. Jest to nazywane _Resetowanie obciążenia partycji_.
  - Obciążenia mogą być zgłaszane w poszczególnych obiektów usługi dynamicznie w czasie wykonywania. Jest to nazywane _raportowania obciążenia_. 
  
Wszystkie z następujących strategii może służyć w ramach tej samej usługi przez cały okres ich istnienia. 

## <a name="default-load"></a>Załaduj domyślne
*Załaduj domyślne* jest dużo metryki zużywa każdego obiektu usługi (wystąpienia bezstanowe lub stanowe repliki) tej usługi. Menedżer zasobów klastra używa tego numeru obciążenia obiektu usługi, dopóki nie odbierze inne informacje, takie jak raportu dynamicznego obciążenia. Prostsze usług obciążenia domyślny jest statyczny definicji. Obciążenia domyślnego nigdy nie jest aktualizowany i jest używany przez okres istnienia usługi. Domyślne ładuje działa doskonale nadaje się do prostych dotyczący planowania pojemności na scenariuszach, gdzie niektóre ilości zasobów są przeznaczone dla różnych obciążeń i nie należy zmieniać.

> [!NOTE]
> Aby uzyskać więcej informacji na temat zarządzania wydajnością i definiowanie pojemności dla węzłów w klastrze, zobacz [w tym artykule](service-fabric-cluster-resource-manager-cluster-description.md#capacity).
> 

Menedżer zasobów klastra umożliwia usług stanowych do określenia obciążeniem różne domyślne kolory podstawowe i pomocnicze bazy danych. Bezstanowej usługi można określić tylko jedną wartość, która ma zastosowanie do wszystkich wystąpień. Dla usług stanowych, obciążenia domyślnej dla lokacji podstawowej i pomocniczej repliki są zazwyczaj inny, ponieważ repliki wykonaj różnego rodzaju pracy w każdej roli. Na przykład kolory podstawowe zazwyczaj obsługiwać operacje odczytu i zapisu, a obsługę większości obciążeń obliczeniowych, gdy pomocnicze bazy danych nie obsługują. Zazwyczaj obciążenia domyślny dla repliki podstawowej jest wyższa niż obciążenia domyślny dla replik pomocniczych. Liczby rzeczywiste powinna zależeć od własne miary.

## <a name="dynamic-load"></a>Dynamicznego obciążenia
Załóżmy, że działała usługi przez jakiś czas. Za pomocą pewne informacje monitorowania, Zauważyłeś, że:

1. Niektóre partycje lub wystąpień danego usługi zużywać więcej zasobów niż inne
2. Niektóre usługi mają obciążenia, który zmienia się wraz z upływem czasu.

Istnieje wiele rzeczy, które może spowodować, że tego rodzaju wahania obciążenia. Na przykład różne usługi lub partycje są skojarzone z różnych klientów z różnymi wymaganiami. Obciążenie może również ulec zmianie, ponieważ ilość pracy wykonywanej przez usługą różnych porach dnia. Niezależnie od przyczyny jest zwykle nie jeden numer używanego do domyślnego. Jest to szczególnie istotne, jeśli chcesz uzyskać większość wykorzystania poza klastrem. Dowolna wartość wybranego obciążenia domyślny jest błędne pewien czas. Nieprawidłowa domyślna ładuje wynik w Menedżer zasobów klastra za pośrednictwem lub w obszarze alokacji zasobów. Co w efekcie masz węzły, które są powyżej lub poniżej wykorzystywane mimo, że Menedżer zasobów klastra według klastra jest równoważone. Domyślne obciążenia są nadal dobre, ponieważ zapewniają one pewne informacje do umieszczenia początkowej, ale nie jest pełną wątku dla rzeczywistych obciążeń. Aby przechwycić dokładnie zmiany wymagań dotyczących zasobów, Menedżer zasobów klastra umożliwia każdego obiektu usługi zaktualizować swój własny obciążenia podczas wykonywania. Jest to nazywane dynamiczne raportowanie obciążenia.

Raporty dynamiczne obciążenia umożliwiają replik lub wystąpień, aby dostosować ich obciążenia zgłosił/alokacji metryk okresie ich istnienia. Repliki usługi lub wystąpienia, które zostało zimnego i bezczynny zazwyczaj będzie zgłaszać wykorzystywała niskiej ilości danej metryki. Czy używają więcej raport będzie zajęty replik lub wystąpień.

Raportowanie obciążenie poszczególnych replik lub wystąpień umożliwia Menedżer zasobów klastra reorganizacja obiektów poszczególnych usług w klastrze. Reorganizacja usług pomaga upewnić się, że staną się zasoby, których potrzebują. Zajęte usług skutecznie dotrzeć do "odzyskać" zasoby od innych replik lub wystąpień, które są aktualnie zimnego lub wykonujesz mniej pracy.

W ramach usług Reliable Services kod w celu raportowania obciążenia dynamicznie wygląda następująco:

Kod:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Usługa może zgłaszać na żadnym z metryk dla niej zdefiniowane w czasie jego tworzenia. Jeśli obciążenie raporty usługi dla metryki, który nie jest skonfigurowany do używania, Usługa Service Fabric ignoruje tego raportu. Jeśli istnieją inne metryki zgłoszone w tym samym czasie, które są prawidłowe, te raporty są akceptowane. Kod obsługi można mierzyć i zgłosić wszystkie metryki wie, jak i operatorów można określić metryki konfigurację, aby używać bez konieczności zmieniania kodu usługi. 

### <a name="updating-a-services-metric-configuration"></a>Trwa aktualizowanie konfiguracji metryk usług
Listy metryk związane z usługą i właściwości te metryki może być aktualizowany dynamicznie, podczas gdy usługa jest aktywna. Dzięki temu eksperymentowania i elastyczność. Niektóre przykłady gdy jest to przydatne są:

  - wyłączając metryki z raportem wymagał poprawek dla określonej usługi
  - ponowne konfigurowanie wagi metryki w oparciu o żądane zachowanie
  - Włączanie nowych metrykach tylko wtedy, gdy kod został już wdrożony i zweryfikować za pośrednictwem innych mechanizmów
  - Zmiana obciążenia domyślnej usługi na podstawie obserwacji zachowanie i użycie

Główny interfejsy API dotyczące zmieniania konfiguracji metryki są `FabricClient.ServiceManagementClient.UpdateServiceAsync` w języku C# i `Update-ServiceFabricService` w programie PowerShell. Wszelkich informacji, określ za pomocą tych interfejsów API, które natychmiast zastępuje istniejące informacje metryki dla usługi. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Mieszanie domyślne wartości obciążenia i raporty dynamiczne obciążenia
Domyślne obciążenia i obciążeniami dynamicznych może służyć do tej samej usługi. Gdy usługa korzysta z domyślnego obciążenia i raporty dynamiczne obciążenia, obciążenie domyślne służy jako oszacowanie, dopóki dynamiczne raporty widoczne. Załaduj domyślne jest dobra, ponieważ daje on Menedżer zasobów klastra coś, co chcesz pracować. Załaduj domyślne umożliwia Menedżer zasobów klastra, aby umieścić obiekty usługi w lokalizacjach dobre podczas ich tworzenia. Jeśli nie podano żadnych informacji obciążenia domyślne, umieszczania usług jest skutecznie losowa. Nadejściu raportów obciążenia później początkowe położenie losowe często jest nieprawidłowy, a Menedżer zasobów klastra musi przenieść usługi.

Teraz pobrać poprzedniego przykładu i zobacz, co się stanie po dodaniu niektóre metryki niestandardowe i dynamiczne raportowanie obciążenia. W tym przykładzie używamy "MemoryInMb" jako metrykę przykładu.

> [!NOTE]
> Pamięć jest jednej z metryk systemu, która może być usługi Service Fabric [określają zasobów](service-fabric-resource-governance.md), i raportowania samodzielnie jest zazwyczaj trudne. Nie faktycznie oczekujemy, że można sporządzić raport na temat użycia pamięci; Pamięć jest używany tutaj jako pomoc do nauki o możliwościach, Menedżer zasobów klastra.
>

Teraz zakładają, że początkowo utworzyliśmy stanowej usługi za pomocą następującego polecenia:

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Przypominamy ta składnia jest ("MetricName MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Zobaczmy, jakie jeden układ klastra możliwe może wyglądać jak:

<center>

![Zrównoważony klastra za pomocą domyślnych i niestandardowych metryk][Image2]
</center>

Kilka rzeczy, które są warte odnotowania:

* Każdej repliki pomocnicze w ramach partycji może mieć własnych obciążenia
* Ogólne metryki wyszukiwania o zrównoważonym obciążeniu. Pamięć, stosunek maksymalne i minimalne obciążenia jest 1,75 (węzeł z najbardziej obciążenia jest N3, jest najmniej, N2 i 28/16 = 1,75).

Istnieją pewne elementy, które wciąż potrzebujemy wyjaśnienie:

* Co to ustalić, czy stosunek 1,75 zakończyła się uzasadnione? Jak Menedżer zasobów klastra sprawdzić czy jest wystarczająco dobre czy ma więcej pracy do wykonania?
* Gdy równoważenia odbywa się?
* Co oznacza że pamięć została ważona "Wysokiego"?

## <a name="metric-weights"></a>Waga metryki
Śledzenie te same metryki w różnych usługach jest ważne. Ten globalny widok polega na tym, co pozwala Menedżer zasobów klastra do śledzenia użycia w klastrze, równoważyć zużycia w węzłach i upewnij się, że węzły nie są kierowane przez pojemność. Jednak usługi mogą mieć różne widoki tego znaczenie tych samych metryk. Ponadto w klastrze przy użyciu wielu metryk i wiele usług, doskonale o zrównoważonym obciążeniu rozwiązań może nie istnieć dla wszystkich metryk. Jak Menedżer zasobów klastra powinien obsługiwać tych sytuacji?

Waga metryki umożliwiają Menedżer zasobów klastra do określania sposobu równoważenia klastra po doskonałe odpowiedzi. Waga metryki również umożliwiać Menedżer zasobów klastra równoważenia usług określone inaczej. Metryki może mieć cztery poziomy różne wagi: Zero, niski, średni i wysoki. Metryka o wadze wynoszącej Zero przyczynia się nic, podczas wybierania, czy elementy są równoważone, czy nie. Jednakże jej obciążenia nadal przyczyniają się do zarządzania wydajnością. Metryki za pomocą wagi, zerowego przydają się nadal i są często używane jako część zachowanie usługi i monitorowania wydajności. [W tym artykule](service-fabric-diagnostics-event-generation-infra.md) zamieszczono więcej informacji dotyczących użytkowania metryk monitorowania i diagnostyki usługi. 

Rzeczywistego wpływu różne metryki obciążenia w klastrze jest, że Menedżer zasobów klastra generuje różne rozwiązania. Metryki wagi Poinformuj Menedżer zasobów klastra, że niektóre metryki są ważniejsze niż inne. Gdy istnieje doskonałe rozwiązanie Menedżer zasobów klastra można preferują rozwiązania, które równoważyć wyższe metryki ważona lepiej. Jeśli usługa uzna za określonej metryki nie jest ważna, jego może się okazać ich użycie w przypadku tej metryki imbalanced. Dzięki temu inna usługa równomiernego rozłożenia niektóre metryki, ważne jest, aby go pobrać.

Spójrzmy na przykład niektóre raporty obciążenia i jak różne metryki przeprowadzi wyniki w różnych alokacji w klastrze. W tym przykładzie widzimy, że przełączenie Względna waga metryk powoduje, że Menedżer zasobów klastra, aby tworzyć różne rozmieszczenia usług.

<center>

![Przykład waga metryki i jej wpływ na rozwiązań do równoważenia][Image3]
</center>

W tym przykładzie istnieją cztery różne usługi, wszystkie raportowania różne wartości dla dwóch różnych metryk MetricA i MetricB. W przypadku jednego wszystkich usług definiowania MetricA jest jednym z najważniejszych (waga = wysoki) i MetricB jako "nieważne" (waga = niski). Zobaczymy, co w efekcie umieszcza usług w Menedżer zasobów klastra, dzięki czemu MetricA lepiej jest równoważone niż MetricB. "Lepiej o zrównoważonym obciążeniu" oznacza, że MetricA ma mniejszy ma niższy odchylenie standardowe niż MetricB. W drugim przypadku firma Microsoft odwrócenie metryki wagi. W rezultacie Menedżer zasobów klastra zamienia usług A i B, co pozwoli uzyskać przydział gdzie MetricB lepiej jest o zrównoważonym obciążeniu niż MetricA.

> [!NOTE]
> Wagi metryki, określić, jak Menedżer zasobów klastra powinien równoważyć, ale nie po równoważenia ma się zdarzyć. Aby uzyskać więcej informacji na temat równoważenia, zapoznaj się z [w tym artykule](service-fabric-cluster-resource-manager-balancing.md)
>

### <a name="global-metric-weights"></a>Globalne wagi metryki
Załóżmy, że ServiceA definiuje MetricA jako wysokiej wagi i ServiceB ustawia grubość MetricA niski lub Zero. Co to jest rzeczywiste wagi, która kończy przyzwyczaja?

Istnieje wiele wagi, które są śledzone dla każdego metryki. Pierwsza waga jest definiowane metryki, podczas tworzenia usługi. Inne waga jest globalne wagi, która jest obliczana automatycznie. Menedżer zasobów klastra używa oba te wagi podczas oceniania rozwiązania. Ważne jest uwzględnieniu obu wagi. Dzięki temu Menedżer zasobów klastra, aby równoważyć poszczególnych usług zgodnie z jego własnej priorytety i upewnij się również poprawną alokację klastra jako całości.

Co się stanie, jeśli Menedżer zasobów klastra nie interesują saldo zarówno globalne i lokalne? Jest również łatwe do utworzenia rozwiązania, które są globalnie równoważone, ale który powoduje, że w równowadze zasobów niska dla poszczególnych usług. W poniższym przykładzie możemy przyjrzeć się skonfigurowano tylko domyślnych metryk usługi i zobacz, co się stanie, gdy tylko globalne saldo jest uważany za:

<center>

![Wpływ to globalne rozwiązanie tylko][Image4]
</center>

W górnym przykładzie oparte tylko na globalne równoważenia rzeczywiście jest równoważone klastra jako całości. Wszystkie węzły mają ten sam liczba kolory podstawowe i te same repliki łączna liczba. Jednak jeśli przyjrzymy się rzeczywisty wpływ ten przydział nie jest tak dobre: utraty dowolny węzeł ma wpływ na określonego obciążenia nieproporcjonalnie, ponieważ zajmuje całe jego kolory podstawowe. Na przykład jeśli pierwszy węzeł nie powiedzie się trzy kolory podstawowe dla trzech różnych partycji usługi okrąg wszystkie zostałyby utracone. Z drugiej strony usług trójkąt i sześciokąt ma ich partycje utracić repliki. Spowoduje to nie przerw w działaniu, oprócz posiadania odzyskać dół repliki.

W tym przykładzie dolnej Menedżer zasobów klastra ma rozproszonej repliki oparte na globalnym i dla usługi równoważenia. Podczas obliczania wyniku rozwiązanie zapewnia najbardziej wagi globalne rozwiązanie i części (z możliwością konfiguracji) do poszczególnych usług. Globalne równoważenia metryka jest obliczany na podstawie średniej metryki wagi od poszczególnych usług. Każda usługa jest równoważone zgodnie z własną zdefiniowanych wagi metryki. Daje to gwarancję, że usługi są równoważone między sobą zgodnie z ich własnych potrzeb. W rezultacie w przypadku awarii tego samego węzła pierwszego błędu jest rozłożona na wszystkie partycje wszystkich usług. Wpływ na każdy jest taka sama.

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług [informacje na temat konfigurowania usługi](service-fabric-cluster-resource-manager-configure-services.md)(service-fabric-cluster-resource-manager-configure-services.md)
- Definiowanie defragmentacji metryk jest jednym ze sposobów Konsolidacja obciążenia w węzłach zamiast rozkładanie go. Aby dowiedzieć się, jak skonfigurować defragmentacji, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
- Aby dowiedzieć się o zarządza Menedżer zasobów klastra i równoważy obciążenie w klastrze, zapoznaj się z artykułem na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Zacznij od początku i [zapoznaj się z wprowadzeniem do usługi Service Fabric Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md)
- Koszt przeniesienia jest jednym ze sposobów sygnalizowanie Menedżer zasobów klastra, że niektóre usługi są bardziej kosztowne przenieść niż inne. Aby dowiedzieć się więcej na temat koszt przeniesienia się [w tym artykule](service-fabric-cluster-resource-manager-movement-cost.md)

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
