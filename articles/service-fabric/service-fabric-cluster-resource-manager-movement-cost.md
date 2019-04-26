---
title: 'Menedżer zasobów klastra usługi Service Fabric: Koszt przeniesienia | Dokumentacja firmy Microsoft'
description: Omówienie koszt przeniesienia do usługi Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f022f258-7bc0-4db4-aa85-8c6c8344da32
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1bd049e6f929b6c3247ca1842412d5527605e643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516607"
---
# <a name="service-movement-cost"></a>Kosztach przenoszenia usług
Czynnikiem, który Menedżer zasobów klastra usługi Service Fabric pod uwagę podczas próby określenia, jakie zmiany można wprowadzać w klastrze jest to koszt tych zmian. Pojęcie "cost" jest dłuższym czasem względem można zwiększyć ilość klastra. Koszt jest brana pod uwagę podczas przenoszenia usługi równoważenia, defragmentacji i inne wymagania. Celem jest, aby spełnić wymagania w taki sposób, co najmniej uciążliwe lub kosztowne. 

Przesunięcie czasu procesora CPU koszty usług i przepustowość, co najmniej sieci. W przypadku usług stanowych wymaga kopiowania stan tych usług, zużywa więcej pamięci i dysku. Minimalizacja kosztu rozwiązania, które Menedżer zasobów klastra Azure Service Fabric funkcjonuje ze pomaga upewnić się, że zasobów klastra nie są poświęcony niepotrzebnie. Jednak również nie chcesz zignorować rozwiązania, które może znacznie poprawić alokacji zasobów w klastrze.

Menedżer zasobów klastra ma dwa sposoby przetwarzania koszty i ograniczanie ich podczas próby zarządzania klastrem. Pierwszy mechanizm jest po prostu zliczania co przeniesienia, która powinna mieć. Jeśli dwa rozwiązania w zakresie są generowane przy użyciu mniej więcej taka sama waga (wynik), a następnie Menedżer zasobów klastra preferuje jeden generuje najniższy koszt (całkowita liczba przeniesień).

Ta strategia sprawdza się dobrze. Ale podobnie jak w przypadku domyślnych lub obciążeń statycznych jest mało prawdopodobne w złożonego systemu, przenosi wszystkie są takie same. Niektóre mogą być znacznie bardziej kosztowne.

## <a name="setting-move-costs"></a>Ustawienie przesuwanie kosztów 
Możesz określić domyślną przenieść kosztów dla usługi, po utworzeniu:

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

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamicznie określanie kosztu przeniesienia na zasadzie na replikę

Poprzedzających fragmentów są dostępne do określania MoveCost dla całej usługi jednocześnie z poza samą usługę. Jednak przenieść koszt jest najbardziej przydatna jest, gdy koszt przeniesienia obiektu określonej usługi zmienia się w jego czas. Ponieważ uwierzytelnienia usługi mają prawdopodobnie najlepsze pogląd na sposób kosztownych się one w celu przenoszenia w danym momencie, Brak interfejsu API usługi własne poszczególnych przenieść kosztów, podczas wykonywania raportu. 

C#:

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Wpływ na koszt przeniesienia
MoveCost zawiera cztery poziomy: Zero, niski, średni i wysoki. MoveCosts są względem siebie, z wyjątkiem Zero. Żadnych kosztów przenoszenia oznacza, że przepływ jest bezpłatna powinna nie wliczają wynik rozwiązania. Ustawienie przejście kosztować wysokiego jest *nie* gwarancji, że replika pozostanie w jednym miejscu.

<center>

![Koszt przenoszenia jako czynnika podczas Wybieranie repliki dla przepływu][Image1]
</center>

MoveCost pomoże Ci znaleźć rozwiązania, które spowodować zakłócenie co najmniej ogólną i jest najłatwiejsza do osiągnięcia jednocześnie nadal otrzymywanych saldo równoważne. Pojęcia usługi koszt może być względem wiele rzeczy. Najbardziej typowe czynniki podczas obliczania kosztów przeniesienia są następujące:

- Ilość stanu lub usługa musi przenieść dane.
- Koszt rozłączenia klientów. Przeniesienie replika podstawowa jest zazwyczaj bardziej kosztowne niż koszty przechodzenia do repliki pomocniczej.
- Koszt śledząc operacji przerywania. Niektóre operacje na danych magazynu poziom lub operacje wykonywane w odpowiedzi na wywołanie klienta są kosztowne. W pewnym momencie nie chcesz ich zatrzymania, jeśli nie masz. Dlatego podczas operacji się dzieje, zwiększasz kosztu przeniesienia tego obiektu usługi, aby zmniejszyć prawdopodobieństwo, że przemieszczał się. Po zakończeniu operacji, ustawisz koszt powrót do normalnego.

## <a name="enabling-move-cost-in-your-cluster"></a>Włączanie kosztu przeniesienia w klastrze
Aby bardziej szczegółowego MoveCosts należy wziąć pod uwagę MoveCost musi być włączona w klastrze. Bez tego ustawienia, domyślny tryb liczenia przenosi jest używany do obliczania MoveCost i MoveCost raporty są ignorowane.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

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
- Menedżera klastra usługi Service Fabric używa metryk do zarządzania, użycie i wydajność w klastrze. Aby dowiedzieć się więcej na temat metryk i sposobach ich konfigurowania, zapoznaj się z [obciążenia w usłudze Service Fabric za pomocą metryk i użycia zasobów na zarządzanie](service-fabric-cluster-resource-manager-metrics.md).
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z [równoważenie klastra usługi Service Fabric](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
