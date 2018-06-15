---
title: 'Koszt przeniesienia usługi Menedżer zasobów klastra sieci szkieletowej: | Dokumentacja firmy Microsoft'
description: Omówienie koszt przeniesienia dla usług sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 74b61967a796fca22ab86918235f1def27a22f91
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204927"
---
# <a name="service-movement-cost"></a>Usługa koszt przeniesienia
Współczynnik Menedżera zasobów klastra sieci szkieletowej usług pod uwagę podczas próby określenia, jakie zmiany można wprowadzać w klastrze jest koszt tych zmian. Pojęcie "koszt" jest przedmiotem handlu wyłączone przed można zwiększyć ilość klastra. Koszt jest brana pod uwagę podczas przenoszenia usługi równoważenia, defragmentacji i inne wymagania. Celem jest w celu spełnienia wymagań w zakresie zakłócenie najmniej kosztowne. 

Przesunięcie czasu procesora CPU koszty usługi i przepustowość co najmniej sieci. W przypadku usług stanowych wymaga kopiowanie stan tych usług, wykorzystywanie dodatkowej pamięci i dysku. Minimalizowania kosztów rozwiązania, które Menedżer zasobów klastra sieci szkieletowej usług Azure pojawia się z pomaga, upewnij się, że zasobów klastra nie są poświęconego niepotrzebnie. Jednak również nie chcesz zignorować rozwiązania, które może znacznie poprawić alokacji zasobów w klastrze.

Menedżer zasobów klastra ma dwa sposoby obliczenie kosztów i ograniczeniem im dostępu przy próbie do zarządzania klastrem. Mechanizm pierwszy jest po prostu zliczania każdy ruch, który może to spowodować. Jeśli dwa rozwiązania są generowane z o taka sama waga (wynik), a następnie Menedżera zasobów klastra preferuje jedną z najniższy koszt (całkowita liczba przenosi).

Ta strategia działa prawidłowo. Ale tak jak w przypadku domyślnych lub obciążeń statycznych, jest mało prawdopodobne w każdym systemie złożonych, że przenoszenie wszystkich są takie same. Niektóre są mogą być bardziej kosztowne.

## <a name="setting-move-costs"></a>Ustawienie przesuwanie kosztów 
Można określić domyślny Przenieś koszt usługi po utworzeniu:

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

Można również określić lub zaktualizować MoveCost dynamicznie usługi po utworzeniu usługi: 

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamicznie Określanie koszt przeniesienia na podstawie na repliki

Poprzedni fragmenty kodu są wszystkie służący do określania MoveCost dla całej usługi jednocześnie z poza samej usługi. Jednak przenieść koszt jest najbardziej przydatna jest, gdy koszt przeniesienia obiektu określonej usługi zmienia się w jego cykl życia. Ponieważ uwierzytelnienia usługi prawdopodobnie najważniejsze informacje o tym, jak kosztowne są można przenieść w danym momencie, jest interfejsem API usługi własnych poszczególne Przenieś koszt podczas wykonywania raportu. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Wpływ koszt przeniesienia
MoveCost zawiera cztery poziomy: Zero, niski, średni i wysoki. MoveCosts są względem siebie, z wyjątkiem Zero. Zero koszt przeniesienia oznacza, że przenoszenie jest bezpłatna i nie powinien odliczona wynik rozwiązania. Ustawianie wysokiego jest koszt przeniesienia *nie* gwarancji, że replika pozostanie w jednym miejscu.

<center>
![Koszt przeniesienia jako czynnik wyborze repliki dla przepływu][Image1]
</center>

MoveCost pomaga znaleźć rozwiązania, które powodują ogólną zakłóceń pracy i są najłatwiej osiągnięcia jednocześnie nadal otrzymywanych saldo równoważne. Pojęcie usługi koszt może być względem wiele rzeczy. Najbardziej typowe czynniki obliczając koszt przeniesienia są następujące:

- Kwota stan lub danych, aby przenieść usługę.
- Koszt rozłączenia klientów. Przenoszenie replikę podstawową jest zazwyczaj bardziej kosztowne niż koszt przenoszenia replikę pomocniczą.
- Koszt zakłócania pracy locie. Niektóre operacje na danych magazynu poziom lub są kosztowne operacje wykonywane w odpowiedzi na wywołanie przez klienta. W pewnym momencie nie chcesz ich zatrzymania, jeśli nie masz. Dlatego podczas operacji trwa, zwiększyć koszt przeniesienia tego obiektu usługi, aby zmniejszyć prawdopodobieństwo, że powoduje przeniesienie. Po zakończeniu operacji, ustawisz koszt do normalnego.

## <a name="enabling-move-cost-in-your-cluster"></a>Włączanie koszt przeniesienia w klastrze
Aby bardziej szczegółowego MoveCosts należy wziąć pod uwagę należy włączyć MoveCost w klastrze. Bez tego ustawienia to domyślny tryb liczenia przenosi jest używany do obliczania MoveCost i MoveCost raporty są ignorowane.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

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

## <a name="next-steps"></a>Kolejne kroki
- Menedżer zasobów klastra sieci szkieletowej usług wykorzystuje metryki Zarządzanie użycia i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryki i sposobach ich konfigurowania, zapoznaj się [Zarządzanie zużycia zasobów i obciążenia w sieci szkieletowej usług o metryki](service-fabric-cluster-resource-manager-metrics.md).
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie klastra, zapoznaj się [równoważenia klastra usługi sieć szkieletowa](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
