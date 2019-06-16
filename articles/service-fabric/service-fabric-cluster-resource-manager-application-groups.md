---
title: Menedżer zasobów klastra usługi Service Fabric - grup aplikacji | Dokumentacja firmy Microsoft
description: Omówienie funkcji grupy aplikacji w usługi Service Fabric Menedżer zasobów klastra
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 7e90dc00a8e042e48d8016e25dda04c15ce9f619
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62114077"
---
# <a name="introduction-to-application-groups"></a>Wprowadzenie do grup aplikacji
Menedżer zasobów klastra usługi Service Fabric zarządza zwykle zasobów klastra przez rozłożenie obciążenia (reprezentowane za pomocą [metryki](service-fabric-cluster-resource-manager-metrics.md)) równomiernie w całym klastrze. Usługa Service Fabric zarządza się pojemności węzłów w klastrze i klaster jako całość przy użyciu [pojemności](service-fabric-cluster-resource-manager-cluster-description.md). Metryki i wydajności działają doskonale nadaje się do wielu obciążeń, ale wzorce, które intensywnie korzystają z różnych wystąpieniach aplikacji Service Fabric Service czasami Przenieś dodatkowe wymagania. Na przykład możesz chcieć:

- Niektóre wydajność na węzłach w klastrze usługi w ramach niektórych wystąpień aplikacji o nazwie rezerwowa
- Ogranicz całkowitą liczbę węzłów, korzystających z usług w ramach wystąpienie nazwane aplikacji (zamiast rozkładanie je przez cały klaster)
- Definiowanie pojemności na wystąpienie nazwane aplikacji, aby ograniczyć liczbę tych usług lub użycie łączna liczba zasobów usług wewnątrz niego

Aby spełnić te wymagania, Menedżer zasobów klastra usługi Service Fabric obsługuje funkcję o nazwie grupy aplikacji.

## <a name="limiting-the-maximum-number-of-nodes"></a>Ograniczenie maksymalnej liczby węzłów
Najprostszym przypadku użycia pojemności aplikacji jest, gdy wystąpienie aplikacji musi być ograniczone do niektórych maksymalną liczbę węzłów. To konsoliduje wszystkie usługi w ramach tego wystąpienia aplikacji na liczbę maszyn w zestawie. Konsolidacja jest przydatne, gdy próbujesz przewidywać, a limit wykorzystania zasobów fizycznych przez usługi w ramach tego wystąpienia aplikacji o nazwie. 

Na poniższej ilustracji przedstawiono wystąpienie aplikacji z lub bez maksymalną liczbę węzłów zdefiniowane:

<center>

![Wystąpienie aplikacji, definiując maksymalnej liczby węzłów][Image1]
</center>

W tym przykładzie po lewej stronie aplikacja nie ma maksymalną liczbę węzłów zdefiniowane, a ma trzy usługi. Menedżer zasobów klastra ma rozszerzane wszystkie repliki na sześciu dostępnych węzłów, aby osiągnąć najlepszą równowagę w klastrze (zachowanie domyślne). W tym przykładzie prawo widzimy tej samej aplikacji, które są ograniczone do trzech węzłów.

Parametr, który kontroluje to zachowanie jest nazywany MaximumNodes. Ten parametr można ustawić podczas tworzenia aplikacji lub aktualizacji dla wystąpienia aplikacji, która została już uruchomiona.

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

W zestawie węzłów Menedżer zasobów klastra nie gwarantuje obiekty, które usługa pobieranie umieszczone razem lub węzły, które Pobierz używane.

## <a name="application-metrics-load-and-capacity"></a>Metryki aplikacji, obciążenia i wydajności
Grupy aplikacji również umożliwiają definiowanie metryk związane z wystąpienia danej aplikacji o nazwie i pojemności tego wystąpienia aplikacji dla tych metryk. Metryki aplikacji pozwalają na śledzenie, zarezerwować i ograniczyć zużycie zasobów usług wewnątrz tego wystąpienia aplikacji.

Dla każdego metryki aplikacji istnieją dwie wartości, które można ustawić:

- **Całkowita pojemność aplikacji** — to ustawienie reprezentuje całkowitą pojemność aplikacji dla określonej metryki. Menedżer zasobów klastra nie zezwala na tworzenie wszelkie nowe usługi, w ramach tego wystąpienia aplikacji, które spowodują całkowite obciążenie przekracza tę wartość. Na przykład załóżmy, że wystąpienie aplikacji ma pojemność 10 i był już obciążenia do 5. Tworzenie usługi z obciążeniem domyślne całkowita 10 będzie niedozwolone.
- **Maksymalna pojemność węzła** — to ustawienie umożliwia określenie maksymalnego obciążenia całkowitą dla aplikacji w jednym węźle. Jeśli obciążenia przechodzi przez tę pojemność, Menedżer zasobów klastra przenosi repliki do innych węzłów, aby zmniejszy się obciążenie.


Program PowerShell:

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

## <a name="reserving-capacity"></a>Rezerwowanie pojemności
Innym typowym zastosowaniem dla grup aplikacji jest zapewnienie, że zasobów w klastrze są zarezerwowane dla wystąpienia danej aplikacji. Zawsze jest zarezerwowany obszar, gdy tworzone jest wystąpienie aplikacji.

Rezerwowania miejsca, w klastrze, dla aplikacji się natychmiast stanie nawet wtedy, gdy:
- wystąpienie aplikacji zostanie utworzony, ale nie ma jeszcze żadnych usług w nim
- każdym razem, gdy zmieni się liczba usług w ramach wystąpienia aplikacji 
- usługi istnieją, ale nie są korzystający z zasobów 

Rezerwacji zasobów dla wystąpienia aplikacji wymaga określenia pliku dwa dodatkowe parametry: *MinimumNodes* i *NodeReservationCapacity*

- **MinimumNodes** — określa minimalną liczbę węzłów, które wystąpienia aplikacji powinny być uruchamiane.  
- **NodeReservationCapacity** — to ustawienie jest na metrykę dla aplikacji. Wartość jest ilość w przypadku tej metryki zarezerwowane dla aplikacji na dowolnym węźle gdzie uruchamianą w usługi w tej aplikacji.

Łącząc **MinimumNodes** i **NodeReservationCapacity** gwarancje rezerwacji minimalne obciążenie dla aplikacji w klastrze. Ma mniej pozostałej pojemności w klastrze niż łączna liczba rezerwacji wymagane, tworzenie aplikacji nie powiedzie się. 

Spójrzmy na przykład pojemność rezerwacji:

<center>

![Wystąpienia aplikacji, definiując rezerwowanie pojemności][Image2]
</center>

W przykładzie po lewej stronie aplikacje nie mają zdefiniowanych wydajność aplikacji. Menedżer zasobów klastra równoważy wszystko, czego zgodnie z normalnym zestawów reguł.

W tym przykładzie po prawej stronie Załóżmy, że Application1 została utworzona z następującymi ustawieniami:

- MinimumNodes równa dwa
- Metryka zdefiniowana za pomocą aplikacji
  - NodeReservationCapacity 20

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

Usługa Service Fabric rezerwy pojemności na dwóch węzłach dla Application1, a nie Zezwalaj usługom z Aplikacja2 korzystanie z tej pojemności, nawet gdy pojawią się, że bez obciążenia zużywanych przez usługi w Application1 w czasie. Tej pojemności zastrzeżonych aplikacji jest uznawany za używane i zmniejsza Pozostała pojemność, w tym węźle, a w klastrze.  Rezerwacja jest odejmowany od pozostałej pojemności klastra natychmiast, jednak użycie zastrzeżonego jest odejmowany od pojemności określonego węzła, tylko wtedy, gdy co najmniej jedną usługę obiekt jest umieszczany na nim. Tę rezerwację nowszej umożliwia elastyczność i lepsze wykorzystanie zasobów, ponieważ zasoby są tylko zarezerwowane na węzłach w razie.

## <a name="obtaining-the-application-load-information"></a>Uzyskiwanie informacji o obciążenia aplikacji
Dla każdej aplikacji, który ma zdolność aplikacji zdefiniowane dla co najmniej jedną metrykę, którą można uzyskać informacje na temat zagregowanego obciążenia zgłosił repliki z jego usług.

Program PowerShell:

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

Zapytanie ApplicationLoad zwraca podstawowych informacji o wydajności aplikacji, który został określony dla aplikacji. Informacje te obejmują informacje węzłów minimalna i maksymalna liczba węzłów, a numer który obecnie zajmuje aplikacji. Zawiera także informacje o poszczególnych metryki obciążenia aplikacji, w tym:

* Nazwa metryki: Nazwa metryki.
* Pojemność rezerwacji: Pojemność klastra, zarezerwowane dla tej aplikacji w klastrze.
* Obciążenie aplikacji: Łączna liczba obciążenia repliki podrzędnego tej aplikacji.
* Wydajność aplikacji: Maksymalna dozwolona wartość obciążenia aplikacji.

## <a name="removing-application-capacity"></a>Usuwanie pojemności aplikacji
Po ustawieniu parametrów wydajności aplikacji dla aplikacji, można je usunąć za pomocą polecenia cmdlet Update interfejsy API z aplikacji lub programu PowerShell. Na przykład:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

To polecenie usuwa wszystkie parametry zarządzania wydajności aplikacji z wystąpienia aplikacji. W tym MinimumNodes, MaximumNodes i metryki aplikacji, jeśli istnieje. Polecenie powoduje natychmiastowe. Po wykonaniu tego polecenia, Menedżer zasobów klastra używa domyślnego zachowania do zarządzania aplikacjami. Parametry wydajność aplikacji można określić ponownie za pomocą `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Ograniczenia dotyczące pojemności aplikacji
Istnieje kilka ograniczeń dotyczących parametrów wydajności aplikacji, które muszą zostać zachowane. Jeśli występują błędy sprawdzania poprawności żadne zmiany nie będzie mieć miejsce.

- Wszystkie parametry całkowitą muszą być liczbami nieujemnej wartości.
- MinimumNodes nigdy nie musi być większa niż MaximumNodes.
- Jeśli zdefiniowano pojemności dla metryki obciążenia, są one muszą wykonać następujące czynności:
  - Pojemność rezerwacji węzła nie może być większa niż maksymalna pojemność węzła. Nie można na przykład ograniczenia wydajności dla metryki "CPU" w węźle, aby dwie jednostki i zarezerwuj trzech jednostek w każdym węźle.
  - Jeśli MaximumNodes jest określony, następnie produktu MaximumNodes oraz maksymalną pojemność węzła nie może być większa niż całkowita wydajność aplikacji. Na przykład załóżmy, że maksymalna pojemność węzła dla metryki obciążenia, "CPU" jest ustawiony na 8. Ponadto Załóżmy, że maksymalna liczba węzłów jest ustawiony na 10. W tym przypadku łączna pojemność aplikacji musi być większa niż 80 dla tej metryki obciążenia.

Ograniczenia są wymuszane, zarówno podczas tworzenia aplikacji i aktualizacji.

## <a name="how-not-to-use-application-capacity"></a>Jak nie używać pojemności aplikacji
- Nie należy korzystać z funkcji grupy aplikacji, aby ograniczyć aplikację do _określonych_ podzbioru węzłów. Innymi słowy, można określić, że aplikacja działa na co najwyżej pięcioma węzłami, ale nie które określonych pięciu węzłów w klastrze. Ograniczając aplikacji do określonych węzłów można osiągnąć za pomocą ograniczeń rozmieszczania dla usług.
- Nie należy próbować Użyj wydajności aplikacji, aby upewnić się, że dwie usługi z tej samej aplikacji, są umieszczane w węzłach tego samego. Zamiast tego użyć [koligacji](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) lub [ograniczeniami dotyczącymi umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usług [Dowiedz się więcej na temat konfigurowania usługi](service-fabric-cluster-resource-manager-configure-services.md)
- Aby dowiedzieć się o zarządza Menedżer zasobów klastra i równoważy obciążenie w klastrze, zapoznaj się z artykułem na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Zacznij od początku i [zapoznaj się z wprowadzeniem do usługi Service Fabric Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md)
- Aby uzyskać więcej informacji na temat sposobu działania metryki zazwyczaj Czytaj [metryk obciążenia sieci szkieletowej usług](service-fabric-cluster-resource-manager-metrics.md)
- Menedżer zasobów klastra ma wiele opcji do opisywania klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tego artykułu na [opisujące klaster usługi Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
