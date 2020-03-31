---
title: Menedżer zasobów klastra sieci szkieletowej usług — grupy aplikacji
description: Omówienie funkcji grupy aplikacji w Menedżerze zasobów klastra sieci szkieletowej usług
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75452153"
---
# <a name="introduction-to-application-groups"></a>Wprowadzenie do grup aplikacji
Menedżer zasobów klastra sieci szkieletowej usług zazwyczaj zarządza zasobami klastra, równomiernie rozkładając obciążenie (reprezentowane za pośrednictwem [metryk)](service-fabric-cluster-resource-manager-metrics.md)w całym klastrze. Sieci szkieletowej usług zarządza pojemność węzłów w klastrze i klastra jako całości za pośrednictwem [pojemności](service-fabric-cluster-resource-manager-cluster-description.md). Metryki i pojemność działają świetnie dla wielu obciążeń, ale wzorce, które intensywnie korzystają z różnych wystąpień aplikacji sieci szkieletowej usług czasami wprowadzają dodatkowe wymagania. Na przykład możesz chcieć:

- Rezerwuj pewną pojemność w węzłach w klastrze dla usług w niektórych nazwanych wystąpieniach aplikacji
- Ogranicz całkowitą liczbę węzłów, na których działają usługi w wystąpieniu nazwanej aplikacji (zamiast rozmieszczać je na całym klastrze)
- Definiowanie pojemności w samym wystąpieniu nazwanej aplikacji w celu ograniczenia liczby usług lub całkowitego zużycia zasobów usług w nim znajduje się

Aby spełnić te wymagania, Menedżer zasobów klastra sieci szkieletowej usług obsługuje funkcję o nazwie Grupy aplikacji.

## <a name="limiting-the-maximum-number-of-nodes"></a>Ograniczanie maksymalnej liczby węzłów
Najprostszym przypadkiem użycia pojemności aplikacji jest, gdy wystąpienie aplikacji musi być ograniczone do określonej maksymalnej liczby węzłów. To konsoliduje wszystkie usługi w tym wystąpieniu aplikacji na określonej liczbie komputerów. Konsolidacja jest przydatna, gdy próbujesz przewidzieć lub ograniczyć wykorzystanie zasobów fizycznych przez usługi w tym wystąpieniu aplikacji o nazwie. 

Na poniższej ilustracji przedstawiono wystąpienie aplikacji z zdefiniowaną maksymalną liczbą węzłów i bez niej:

<center>

![Wystąpienie aplikacji definiujące maksymalną liczbę węzłów][Image1]
</center>

W lewym przykładzie aplikacja nie ma zdefiniowanej maksymalnej liczby węzłów i ma trzy usługi. Menedżer zasobów klastra rozłożył wszystkie repliki w sześciu dostępnych węzłach, aby osiągnąć najlepszą równowagę w klastrze (zachowanie domyślne). W prawym przykładzie widzimy tę samą aplikację ograniczoną do trzech węzłów.

Parametr, który kontroluje to zachowanie jest nazywany MaximumNodes. Ten parametr można ustawić podczas tworzenia aplikacji lub zaktualizować dla wystąpienia aplikacji, które było już uruchomione.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

W obrębie zestawu węzłów Menedżer zasobów klastra nie gwarantuje, które obiekty usługi są umieszczone razem lub które węzły są używane.

## <a name="application-metrics-load-and-capacity"></a>Metryki aplikacji, obciążenie i pojemność
Grupy aplikacji umożliwiają również definiowanie metryk skojarzonych z danym wystąpieniem nazwanej aplikacji i pojemnością tego wystąpienia aplikacji dla tych metryk. Metryki aplikacji umożliwiają śledzenie, rezerwowanie i ograniczanie zużycia zasobów usług wewnątrz tego wystąpienia aplikacji.

Dla każdej metryki aplikacji istnieją dwie wartości, które można ustawić:

- **Całkowita pojemność aplikacji** — to ustawienie reprezentuje całkowitą pojemność aplikacji dla określonej metryki. Menedżer zasobów klastra nie zezwala na tworzenie nowych usług w tym wystąpieniu aplikacji, które mogłyby spowodować całkowite obciążenie przekroczyć tę wartość. Załóżmy na przykład, że wystąpienie aplikacji miało pojemność 10 i miało już obciążenie pięciu. Utworzenie usługi o całkowitym obciążeniu domyślnym 10 byłoby niedozwolone.
- **Maksymalna pojemność węzła** — to ustawienie określa maksymalne całkowite obciążenie aplikacji w jednym węźle. Jeśli obciążenie zostanie przeładowane przez tę pojemność, Menedżer zasobów klastra przenosi repliki do innych węzłów, tak aby obciążenie zmniejszyło się.


Powershell:

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#:

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Rezerwowanie zdolności produkcyjnych
Innym typowym zastosowaniem dla grup aplikacji jest zapewnienie, że zasoby w klastrze są zarezerwowane dla danego wystąpienia aplikacji. Miejsce jest zawsze zarezerwowane podczas tworzenia wystąpienia aplikacji.

Rezerwowanie miejsca w klastrze dla aplikacji odbywa się natychmiast, nawet wtedy, gdy:
- wystąpienie aplikacji jest tworzone, ale nie ma jeszcze żadnych usług w nim
- liczba usług w instancji aplikacji zmienia się za każdym razem, gdy 
- usługi istnieją, ale nie zużywają zasobów 

Rezerwowanie zasobów dla wystąpienia aplikacji wymaga określenia dwóch dodatkowych parametrów: *MinimumNodes* i *NodeReservationCapacity*

- **MinimumNodes** - Definiuje minimalną liczbę węzłów, na których powinno być uruchamiane wystąpienie aplikacji.  
- **NodeReservationCapacity** — to ustawienie jest dla metryki dla aplikacji. Wartość jest kwotą tej metryki zarezerwowane dla aplikacji w dowolnym węźle, w którym działają usługi w tej aplikacji.

Łączenie **MinimumNodes** i **NodeReservationCapacity** gwarantuje minimalną rezerwację obciążenia dla aplikacji w klastrze. Jeśli w klastrze jest mniejsza pozostała pojemność niż wymagana całkowita rezerwacja, utworzenie aplikacji zakończy się niepowodzeniem. 

Przyjrzyjmy się przykładowi rezerwacji pojemności:

<center>

![Wystąpienia aplikacji definiujące pojemność zarezerwowaną][Image2]
</center>

W lewym przykładzie aplikacje nie mają zdefiniowanej pojemności aplikacji. Menedżer zasobów klastra równoważy wszystko zgodnie z normalnymi regułami.

W przykładzie po prawej stronie załóżmy, że aplikacja1 została utworzona z następującymi ustawieniami:

- MinimumNodes ustawiona na dwa
- Metryka aplikacji zdefiniowana za pomocą
  - WęzełRezerwacjaPojemność 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Sieć szkieletowa usług rezerwuje pojemność w dwóch węzłach dla aplikacji1 i nie zezwala na korzystanie z tej pojemności przez usługi z aplikacji2, nawet jeśli nie ma żadnego obciążenia, które jest zużywane przez usługi wewnątrz aplikacji1 w tym czasie. Ta pojemność aplikacji zarezerwowanej jest uważany za zużyte i liczy się z pozostałą pojemność w tym węźle i w klastrze.  Rezerwacja jest natychmiast odejmowana od pozostałej pojemności klastra, jednak zużycie zarezerwowane jest odejmowane od pojemności określonego węzła tylko wtedy, gdy umieszczony jest na niej co najmniej jeden obiekt usługi. Ta późniejsza rezerwacja umożliwia elastyczność i lepsze wykorzystanie zasobów, ponieważ zasoby są zarezerwowane tylko w węzłach, gdy jest to potrzebne.

## <a name="obtaining-the-application-load-information"></a>Uzyskiwanie informacji o obciążeniu aplikacji
Dla każdej aplikacji, która ma pojemność aplikacji zdefiniowane dla jednego lub więcej metryki można uzyskać informacje o obciążeniu agregacji zgłaszane przez repliki swoich usług.

Powershell:

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

Kwerenda ApplicationLoad zwraca podstawowe informacje o pojemności aplikacji, który został określony dla aplikacji. Informacje te obejmują informacje o minimalnych węzłach i maksymalnych węzłach oraz numer, który aplikacja aktualnie zajmuje. Zawiera również informacje o każdej metryki obciążenia aplikacji, w tym:

* Nazwa metryki: nazwa metryki.
* Pojemność rezerwacji: Pojemność klastra, która jest zarezerwowana w klastrze dla tej aplikacji.
* Obciążenie aplikacji: Całkowite obciążenie replik podrzędnych tej aplikacji.
* Pojemność aplikacji: Maksymalna dozwolona wartość obciążenia aplikacji.

## <a name="removing-application-capacity"></a>Usuwanie pojemności aplikacji
Po ustawieniu parametrów pojemności aplikacji dla aplikacji można je usunąć za pomocą interfejsów API aktualizacji aplikacji lub poleceń cmdlet programu PowerShell. Przykład:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

To polecenie usuwa wszystkie parametry zarządzania pojemnością aplikacji z wystąpienia aplikacji. Obejmuje to minimumnodes, MaximumNodes i metryki aplikacji, jeśli istnieje. Efekt polecenia jest natychmiastowy. Po zakończeniu tego polecenia Menedżer zasobów klastra używa domyślnego zachowania do zarządzania aplikacjami. Parametry pojemności aplikacji można `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`określić ponownie za pomocą pliku .

### <a name="restrictions-on-application-capacity"></a>Ograniczenia dotyczące pojemności aplikacji
Istnieje kilka ograniczeń dotyczących parametrów pojemności aplikacji, które muszą być przestrzegane. Jeśli występują błędy sprawdzania poprawności, nie zachodzą żadne zmiany.

- Wszystkie parametry liczb całkowitych muszą być liczbami nieujemniej.
- MinimumNodes nigdy nie może być większa niż MaximumNodes.
- Jeśli metryki obciążenia są zdefiniowane, muszą one być zgodne z tymi regułami:
  - Pojemność rezerwacji węzła nie może być większa niż maksymalna pojemność węzła. Na przykład nie można ograniczyć pojemność metryki "CPU" w węźle do dwóch jednostek i spróbuj zarezerwować trzy jednostki w każdym węźle.
  - Jeśli określono MaximumNodes, a następnie produkt MaximumNodes i maksymalna pojemność węzła nie może być większa niż całkowita pojemność aplikacji. Załóżmy na przykład, że maksymalna pojemność węzła dla metryki obciążenia "CPU" jest ustawiona na osiem. Załóżmy również, że ustawisz maksymalną liczbę węzłów na 10. W takim przypadku całkowita pojemność aplikacji musi być większa niż 80 dla tej metryki obciążenia.

Ograniczenia są wymuszane zarówno podczas tworzenia aplikacji, jak i aktualizacji.

## <a name="how-not-to-use-application-capacity"></a>Jak nie używać pojemności aplikacji
- Nie próbuj używać funkcji grupy aplikacji, aby ograniczyć aplikację do _określonego_ podzbioru węzłów. Innymi słowy można określić, że aplikacja działa na co najwyżej pięć węzłów, ale nie które określone pięć węzłów w klastrze. Ograniczanie aplikacji do określonych węzłów można osiągnąć za pomocą ograniczeń umieszczania dla usług.
- Nie próbuj używać pojemności aplikacji, aby upewnić się, że dwie usługi z tej samej aplikacji są umieszczane w tych samych węzłach. Zamiast tego należy użyć ograniczeń [koligacji](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) lub [położenia .](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="next-steps"></a>Następne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług, [dowiedz się więcej o konfigurowaniu usług](service-fabric-cluster-resource-manager-configure-services.md)
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z artykułem na temat [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Zacznij od początku i [uzyskaj wprowadzenie do Menedżera zasobów klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-introduction.md)
- Aby uzyskać więcej informacji na temat ogólnego działania metryk metryk [obciążenia sieci szkieletowej usług](service-fabric-cluster-resource-manager-metrics.md)
- Menedżer zasobów klastra ma wiele opcji opisu klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tym [artykułem opisującym klaster sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
