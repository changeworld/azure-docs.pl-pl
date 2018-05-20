---
title: Menedżer zasobów klastra usługi sieć szkieletowa — grup aplikacji | Dokumentacja firmy Microsoft
description: Omówienie funkcji grupy aplikacji w usługi sieć szkieletowa klastra Menedżer zasobów
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 4cae2370-77b3-49ce-bf40-030400c4260d
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 215efc1f0597f5199dd37baf4b109d7e76040aae
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="introduction-to-application-groups"></a>Wprowadzenie do grup aplikacji
Menedżer zasobów klastra usługi sieć szkieletowa zwykle zarządza zasobami klastra przez rozłożenie obciążenia (reprezentowane przez [metryki](service-fabric-cluster-resource-manager-metrics.md)) równomiernie w całym klastrze. Usługa Service Fabric zarządza pojemność węzłów w klastrze i klaster jako całość przy użyciu [pojemności](service-fabric-cluster-resource-manager-cluster-description.md). Metryki i wydajność pracy wygodne w przypadku wielu obciążeń, ale wzorców, które w znacznym stopniu wykorzystywane różnych wystąpień aplikacji sieci szkieletowej usług czasami przenieść dodatkowe wymagania. Na przykład można:

- Niektóre wydajność w węzłach klastra dla usług w ramach wystąpienia niektórych aplikacji o nazwie rezerwowa
- Ogranicz całkowitą liczbę węzłów, które działają na usługi w wystąpieniu o nazwie aplikacji (zamiast rozkładanie je za pośrednictwem całego klastra)
- Definiowanie pojemności w wystąpieniu o nazwie aplikacji, aby ograniczyć liczbę usług lub zużycie zasobów całkowita liczba usług w nim

Aby spełnić te wymagania, Menedżer zasobów klastra sieci szkieletowej usług obsługuje funkcję grupy aplikacji.

## <a name="limiting-the-maximum-number-of-nodes"></a>Ogranicza maksymalną liczbę węzłów
Najprostsza przypadek użycia pojemności aplikacji jest w przypadku wystąpienia aplikacji musi być ograniczone do niektórych maksymalną liczbę węzłów. Zakłada skonsolidowanie obsługi wszystkich usług w ramach danego wystąpienia aplikacji na zestaw liczby maszyn. Konsolidacja jest przydatne, gdy użytkownik próbuje odgadnąć lub cap użycia zasobów fizycznych przez usługi w ramach tego wystąpienia nazwanego aplikacji. 

Na poniższej ilustracji przedstawiono wystąpienie aplikacji z włączonymi i wyłączonymi maksymalną liczbę węzłów zdefiniowane:

<center>
![Maksymalna liczba węzłów Definiowanie wystąpienia aplikacji][Image1]
</center>

W przykładzie po lewej stronie aplikacja nie ma maksymalną liczbę węzłów zdefiniowana, a ma trzy usługi. Menedżer zasobów klastra ma rozszerzane wszystkie repliki na sześciu węzłów dostępne do osiągnięcia równowagę w klastrze (domyślnie). W tym przykładzie prawo widzimy tej samej aplikacji maksymalnie trzy węzły.

Parametr, który kontroluje to zachowanie jest nazywany wartość MaximumNodes. Ten parametr można ustawić podczas tworzenia aplikacji lub aktualizacji dla wystąpienia aplikacji, która została już uruchomiona.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –Name fabric:/AppName –MaximumNodes 5
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

W ramach zestawu węzłów klastra Menedżera zasobów nie gwarantuje obiekty, które usługa uzyskać umieszczone razem lub pobrać używane węzły, które.

## <a name="application-metrics-load-and-capacity"></a>Metryki aplikacji, obciążenia i pojemności
Grupy aplikacji pozwalają również do definiowania metryk powiązanych z wystąpienia danej aplikacji o nazwie oraz wydajności danego wystąpienia aplikacji dla tych metryk. Metryki aplikacji pozwalają na śledzenie, zastrzec i ograniczyć zużycie zasobów usług wewnątrz tego wystąpienia aplikacji.

Dla każdej aplikacji metryki istnieją dwie wartości, które można ustawić:

- **Całkowita liczba aplikacji, zdolność** — to ustawienie reprezentuje całkowita pojemność aplikacji dla określonej metryki. Menedżer zasobów klastra nie zezwala na tworzenie nowych usług, w ramach tego wystąpienia aplikacji, które mogłyby spowodować całkowite obciążenie przekracza tę wartość. Na przykład załóżmy, że wystąpienie aplikacji ma pojemności 10 już obciążenia pięć. Tworzenie usługi z obciążeniem całkowita domyślne 10 będzie niedozwolone.
- **Maksymalna pojemność węzła** — to ustawienie określa maksymalną całkowite obciążenie dla aplikacji w jednym węźle. Jeśli obciążenie odbywa się za pośrednictwem tej pojemności, Menedżer zasobów klastra przenosi replik do innych węzłów tak, że zmniejsza obciążenie.


Środowiska PowerShell:

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

## <a name="reserving-capacity"></a>Rezerwacji pojemności
Inne typowe zastosowanie dla grup aplikacji jest upewnij się, że zasoby w klastrze są zastrzeżone dla wystąpienia danej aplikacji. Po utworzeniu wystąpienia aplikacji zawsze jest zarezerwowany obszar.

Rezerwacji miejsca w klastrze dla aplikacji się natychmiast stanie nawet wtedy, gdy:
- wystąpienie aplikacji zostało utworzone, ale nie ma jeszcze żadnych usług w niej
- Liczba usług w ramach zmian wystąpienia aplikacji zawsze 
- usługi istnieje, ale nie korzysta z zasobów 

Rezerwacji zasobów dla wystąpienia aplikacji wymaga określenia dwóch dodatkowych parametrów: *MinimumNodes* i *NodeReservationCapacity*

- **Wartość MinimumNodes** — definiuje minimalna liczba węzłów, które wystąpienie aplikacji powinny być uruchamiane.  
- **NodeReservationCapacity** — jest to ustawienie na metryki dla aplikacji. Wartość jest kwota tej metryki zarezerwowany dla aplikacji na dowolnym węźle gdzie uruchomienia usług w tej aplikacji.

Łączenie **MinimumNodes** i **NodeReservationCapacity** gwarancje rezerwacji minimalna obciążenia dla aplikacji w klastrze. Jeśli ma mniej pozostałych pojemności w klastrze niż całkowita rezerwacji wymagane, tworzenie aplikacji nie powiedzie się. 

Oto przykład rezerwacji pojemności:

<center>
![Definiowanie pojemności zastrzeżone wystąpień aplikacji][Image2]
</center>

W przykładzie po lewej stronie aplikacje nie mają żadnych zdefiniowano zdolności produkcyjnych aplikacji. Menedżer zasobów klastra równoważy wszystko zgodnie z normalnym reguły.

W przykładzie po prawej stronie Załóżmy, że Application1 został utworzony z następującymi ustawieniami:

- Ustaw wartość MinimumNodes do dwóch
- Metryka zdefiniowana z aplikacji
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

Sieć szkieletowa usług rezerw pojemności na dwóch węzłów w Application1 i nie można używać usług z Aplikacja2 użycie wydajność, nawet jeśli istnieją takie obciążenia jest są używane przez usługi wewnątrz Application1 w czasie. Pojemność ten zastrzeżony aplikacji jest uznawany za liczby przed pozostałe zasoby, w tym węźle, a w ramach klastra i używane.  Rezerwacja jest odejmowany od pozostałe zasoby klastra, jednak użycie zastrzeżonego jest odejmowany od pojemności określonego węzła tylko wtedy, gdy co najmniej jedną usługę obiekt znajduje się na nim. Tego zastrzeżenia nowsze umożliwia elastyczność i lepsze wykorzystanie zasobów, ponieważ zasoby są tylko zarezerwowane na węzłach w razie potrzeby.

## <a name="obtaining-the-application-load-information"></a>Uzyskiwanie informacji o obciążenia aplikacji
Dla każdej aplikacji, która ma pojemność aplikacji zdefiniowane dla co najmniej jedną metrykę, który można uzyskać informacje na temat agregacji obciążenia zgłoszone przez repliki z jego usług.

Środowiska PowerShell:

``` posh
Get-ServiceFabricApplicationLoad –ApplicationName fabric:/MyApplication1
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

Zapytanie ApplicationLoad zwraca podstawowe informacje o wydajności aplikacji, który został określony dla aplikacji. Informacje te obejmują informacje o węzłach minimalna i maksymalna liczba węzłów i liczba, która aplikacja jest obecnie zajęte. Zawiera także informacje o każdym Metryka obciążenia aplikacji, w tym:

* Nazwa metryki: Nazwa metryki.
* Reservationcapacity: Pojemności klastra zarezerwowane dla tej aplikacji w klastrze.
* Obciążenia aplikacji: Całkowita liczba obciążenia replik podrzędnych tej aplikacji.
* Pojemność aplikacji: Maksymalna dopuszczalna wartość obciążenia aplikacji.

## <a name="removing-application-capacity"></a>Usuwanie aplikacji, zdolność
Po ustawieniu parametrów aplikacji, zdolność dla aplikacji, można je usunąć za pomocą poleceń cmdlet interfejsów API aplikacji aktualizacji lub programu PowerShell. Na przykład:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

To polecenie usuwa wszystkie parametry zarządzania wydajności aplikacji z wystąpienia aplikacji. W tym MinimumNodes, wartość MaximumNodes i metryki aplikacji, jeśli istnieje. Polecenie powoduje natychmiastowe. Po wykonaniu tego polecenia, Menedżer zasobów klastra używa domyślnego zachowania związanych z zarządzaniem aplikacjami. Parametry pojemność aplikacji można określić ponownie za pomocą `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()`.

### <a name="restrictions-on-application-capacity"></a>Ograniczenia dotyczące wydajności aplikacji
Istnieje kilka ograniczeń w parametrach wydajność aplikacji, które muszą zostać zachowane. Jeśli występują błędy sprawdzania poprawności żadne zmiany nie została wykonana.

- Wszystkie parametry liczba całkowita musi być liczby nieujemnej.
- Wartość MinimumNodes nigdy nie może być większa niż wartość MaximumNodes.
- Jeśli zdefiniowano wydajności dla metryki obciążenia, są one musi wykonać następujące czynności:
  - Węzeł rezerwacji pojemność nie może być większa niż maksymalna pojemność węzła. Nie można na przykład ograniczyć wydajności dla metryki "Procesora" w węźle, aby dwie jednostki i zarezerwuj trzy jednostki w każdym węźle.
  - Jeśli określono wartość MaximumNodes, następnie produktu MaximumNodes i maksymalna pojemność węzła nie może być większa niż całkowita pojemność aplikacji. Na przykład załóżmy, że maksymalna pojemność węzła Metryka obciążenia, "CPU" jest ustawiony do ośmiu. Również Załóżmy, że maksymalna liczba węzłów jest ustawiony na 10. W takim przypadku całkowita pojemność aplikacji musi być większa niż 80 dla ta metryka obciążenia.

Ograniczenia są wymuszane zarówno podczas tworzenia aplikacji i aktualizacji.

## <a name="how-not-to-use-application-capacity"></a>Jak nie należy używać aplikacji, zdolność
- Nie należy korzystać z funkcji grupy aplikacji do aplikacji, aby ograniczyć _określonych_ podzbioru węzłów. Innymi słowy, można określić, że aplikacja działa na maksymalnie pięć węzłów, ale nie których określonych pięć węzłów w klastrze. Ograniczający aplikacji do określonych węzłów można osiągnąć za pomocą ograniczenia umieszczania usług.
- Nie należy próbować upewnij się, że dwie usługi z tej samej aplikacji są umieszczane na tych samych węzłów za pomocą wydajność aplikacji. Zamiast tego użyj [koligacji](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) lub [ograniczenia umieszczania](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Kolejne kroki
- Aby uzyskać więcej informacji na temat konfigurowania usługi [Dowiedz się więcej o konfigurowaniu usługi](service-fabric-cluster-resource-manager-configure-services.md)
- Aby dowiedzieć się o jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz artykuł na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Rozpocznij od początku i [wprowadzenie do usługi sieci szkieletowej klastra Menedżera zasobów](service-fabric-cluster-resource-manager-introduction.md)
- Aby uzyskać więcej informacji na temat działania metryki ogólnie rzecz biorąc, przeczytaj na [metryki obciążenia sieci szkieletowej usług](service-fabric-cluster-resource-manager-metrics.md)
- Menedżer zasobów klastra ma wiele opcji opisujące klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się w tym artykule na [opisujące klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
