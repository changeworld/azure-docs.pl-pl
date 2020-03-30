---
title: 'Menedżer zasobów klastra sieci szkieletowej usług: koszt ruchu'
description: Dowiedz się więcej o koszcie przesunięcia usług sieci szkieletowej usług i o tym, jak można je określić, aby pasowały do wszelkich potrzeb architektonicznych, w tym konfiguracji dynamicznej.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: af3e01d0d5a605c052be24eed8e14ee3449e2c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563347"
---
# <a name="service-movement-cost"></a>Koszt ruchu usługi
Czynnikiem, który Menedżer zasobów klastra sieci szkieletowej usług bierze pod uwagę podczas próby określenia, jakie zmiany należy wprowadzić w klastrze, jest koszt tych zmian. Pojęcie "koszt" jest wymieniane na ile można poprawić klaster. Koszt jest brany pod uwagę podczas przenoszenia usług do bilansowania, defragmentacji i innych wymagań. Celem jest spełnienie wymagań w sposób najmniej uciążliwy lub kosztowny.

Przenoszenie usług kosztuje czas procesora i przepustowość sieci co najmniej. W przypadku usług stanowych wymaga kopiowania stanu tych usług, zużywając dodatkową pamięć i dysk. Minimalizowanie kosztów rozwiązań, które zawiera w usłudze Azure Service Fabric Cluster Resource Manager pomaga zapewnić, że zasoby klastra nie są wydawane niepotrzebnie. Jednak również nie chcesz ignorować rozwiązania, które znacznie poprawiłyby alokację zasobów w klastrze.

Menedżer zasobów klastra ma dwa sposoby obliczania kosztów i ograniczania ich podczas próby zarządzania klastrem. Pierwszym mechanizmem jest po prostu liczenie każdego ruchu, który by zrobił. Jeśli dwa rozwiązania są generowane z mniej więcej taką samą sald (wynik), menedżer zasobów klastra preferuje ten z najniższym kosztem (całkowita liczba ruchów).

Ta strategia działa dobrze. Ale podobnie jak w przypadku obciążeń domyślnych lub statycznych, jest mało prawdopodobne, w każdym złożonym systemie, że wszystkie ruchy są równe. Niektóre z nich mogą być znacznie droższe.

## <a name="setting-move-costs"></a>Ustawianie kosztów przenoszenia 
Podczas jej tworzenia można określić domyślny koszt przeniesienia usługi:

Program PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C#: 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Można również określić lub zaktualizować MoveCost dynamicznie dla usługi po utworzeniu usługi: 

Program PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamiczne określanie kosztu przeniesienia dla repliki

Powyższe urywki są do określania MoveCost dla całej usługi na raz spoza samej usługi. Jednak koszt przeniesienia jest najbardziej przydatne jest, gdy koszt przeniesienia określonego obiektu usługi zmienia się w ciągu jego żywotności. Ponieważ same usługi prawdopodobnie mają najlepszy pomysł, jak kosztowne są one przenieść danego czasu, istnieje interfejs API dla usług do zgłaszania własnych kosztów poszczególnych przeprowadzek w czasie wykonywania. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Wpływ kosztów przeprowadzki
MoveCost ma pięć poziomów: Zero, Niski, Średni, Wysoki i BardzoWysoki. Mają zastosowanie następujące zasady:

* MoveCosts są względem siebie, z wyjątkiem Zero i VeryHigh. 
* Zerowy koszt ruchu oznacza, że ruch jest wolny i nie powinien być wliczany do wyniku rozwiązania.
* Ustawienie kosztu przeniesienia na Wysoki lub BardzoWysoki *nie* daje gwarancji, że replika *nigdy nie* zostanie przeniesiona.
* Repliki z bardzowysoki koszt przeniesienia zostaną przeniesione tylko wtedy, gdy istnieje naruszenie ograniczeń w klastrze, które nie może być ustalona w inny sposób (nawet jeśli wymaga przenoszenia wielu innych replik, aby naprawić naruszenie)



<center>

![Przenoszenie kosztu jako czynnika przy wyborze replik do ruchu][Image1]
</center>

MoveCost pomaga znaleźć rozwiązania, które powodują najmniejsze zakłócenia i są najłatwiejsze do osiągnięcia, a jednocześnie osiągają równoważną równowagę. Pojęcie kosztów usługi może być związane z wieloma rzeczami. Najczęstszymi czynnikami przy obliczaniu kosztu przeprowadzki są:

- Ilość stanu lub danych, które usługa musi przenieść.
- Koszt odłączenia klientów. Przenoszenie repliki podstawowej jest zwykle droższe niż koszt przeniesienia repliki pomocniczej.
- Koszt przerwania operacji w locie. Niektóre operacje na poziomie magazynu danych lub operacje wykonywane w odpowiedzi na wywołanie klienta są kosztowne. Po pewnym momencie, nie chcesz ich zatrzymać, jeśli nie trzeba. Tak więc podczas operacji trwa, można zwiększyć koszt przeniesienia tego obiektu usługi, aby zmniejszyć prawdopodobieństwo, że porusza. Po zakończeniu operacji należy ustawić koszt z powrotem do normalnego.

> [!IMPORTANT]
> Korzystanie bardzoWysoki koszt przeniesienia należy dokładnie rozważyć, ponieważ znacznie ogranicza zdolność Menedżera zasobów klastra, aby znaleźć globalnie optymalne rozwiązanie umieszczania w klastrze. Repliki z bardzowysoki koszt przeniesienia zostaną przeniesione tylko wtedy, gdy istnieje naruszenie ograniczeń w klastrze, które nie może być ustalona w inny sposób (nawet jeśli wymaga przenoszenia wielu innych replik, aby naprawić naruszenie)

## <a name="enabling-move-cost-in-your-cluster"></a>Włączanie kosztów przenoszenia w klastrze
Aby bardziej szczegółowe MoveCosts należy wziąć pod uwagę, MoveCost musi być włączona w klastrze. Bez tego ustawienia domyślny tryb zliczania ruchów jest używany do obliczania MoveCost, a raporty MoveCost są ignorowane.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki
- Menedżer zasobów klastra sieci szkieletowej usług używa metryk do zarządzania zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o metrykach i sposobie ich konfigurowania, zapoznaj się [z Zarządzanie zużyciem zasobów i ładowaniem w sieci szkieletowej usług za pomocą metryk](service-fabric-cluster-resource-manager-metrics.md).
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z [instrukcjami równoważenia klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
