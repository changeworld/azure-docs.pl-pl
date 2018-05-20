---
title: Defragmentacja metryki w sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Omówienie przy użyciu defragmentacji lub pakowania jako strategię metryki w sieci szkieletowej usług
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a35ae5933729615d634359e64e31d43536d81431
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentacja metryki i obciążenia w sieci szkieletowej usług
Strategię domyślnego menedżera zasobów klastra sieci szkieletowej usług zarządzania metryki obciążenia w klastrze jest rozkładanie obciążenia. Zapewnienie, że węzły są wykorzystywane równomiernie pozwala uniknąć gorącego i zimnych miejsc prowadzących do rywalizacji i nieużywanego zasobów. Dystrybucji obciążeń w klastrze jest również najbezpieczniejszy pod względem pozostałych błędy, ponieważ gwarantuje, że błąd nie Wyjmij znaczną część danego obciążenia. 

Menedżer zasobów klastra sieci szkieletowej usług obsługę zarządzania obciążenia, który jest defragmentacji innych strategii. Defragmentacja oznacza, że zamiast w trakcie rozpowszechniają wykorzystania metryki klastra, jego są konsolidowane. Konsolidacji jest po prostu odwracanie domyślne równoważenie strategii — zamiast minimalizując odchylenie standardowe średni metryki obciążenia, próbuje powinna ona być Menedżera zasobów klastra.

## <a name="when-to-use-defragmentation"></a>Kiedy należy używać defragmentacji
Dystrybucja obciążenia w klastrze zużywa niektórych zasobów w każdym węźle. Niektórych zadań tworzenia usług, które są wyjątkowo dużą i używać większości lub wszystkich węzła. W takich przypadkach jest to możliwe, że w przypadku dużych obciążeń, uzyskiwanie utworzony nie ma wystarczającej ilości miejsca na dowolnym węźle, aby uruchomić je. Duże obciążenia nie są problem w sieci szkieletowej usług; w takich przypadkach klaster Resource Manager ustala, że potrzebuje do klastra, aby zwolnić miejsce dla to duże obciążenie reorganizowanie. Jednak w tym samym czasie aby obciążenie ma czekać do zaplanowania w klastrze.

Jeśli istnieje wiele usług i stanie, aby przenosić, jego może potrwać długo duże obciążenie można umieścić w klastrze. Jest to bardziej prawdopodobne, jeśli inne obciążenia w klastrze są także duży i dlatego trwać dłużej reorganizować. Zespół usługi sieć szkieletowa mierzone czasy tworzenia w symulacji tego scenariusza. Znaleziono się, że tworzenie dużych usług trwało dłużej zaraz po wykorzystaniu klastra otrzymano powyżej od 30% do 50%. Aby obsługiwać ten scenariusz, wprowadzono defragmentacji jako równoważenia strategii. Znaleziono się, że w przypadku dużych obciążeń, zwłaszcza tych, których czas utworzenia było ważne defragmentacji naprawdę pomocne te nowe obciążenia są planowane w klastrze.

Można skonfigurować metryki defragmentacji mają Menedżera zasobów klastra, aby aktywnie spróbuj zmniejszyć obciążenie usługi do mniej węzłów. Pomaga to zapewnić, że jest prawie zawsze miejsca dla dużych usług bez reorganizacja klastra. Brak konieczności reorganizować klastra umożliwia szybkie tworzenie dużych obciążeń.

Defragmentacja nie ma potrzeby większości użytkowników. Usług są zwykle jest mały, więc nie jest trudne do odszukania miejsce dla nich w klastrze. Gdy reorganizacji jest możliwe, przechodzi ona szybko ponownie ponieważ większość usług są małe i mogą zostać przeniesione szybkie i równolegle. Jeśli jednak duże usług i należy je szybko utworzyć następnie strategii defragmentacji jest dla Ciebie. Omówiono wady i zalety używania defragmentacji dalej. 

## <a name="defragmentation-tradeoffs"></a>Defragmentacja wady i zalety
Defragmentacji może zwiększyć impactfulness błędy, ponieważ w węzłach, które nie są uruchomione usługi więcej. Defragmentacji także może zwiększyć koszty, ponieważ zasobów w klastrze, musi być przechowywany w rezerwy oczekiwanie na tworzenie dużych obciążeń.

Poniższy diagram zapewnia wizualną reprezentację dwa klastry, który defragmentacji i jedną, która nie jest. 

<center>
![Porównywanie zrównoważonym i defragmentacji klastrów][Image1]
</center>

W przypadku zrównoważonym należy wziąć pod uwagę liczbę przesunięcia, które należy umieścić jedną z największych obiektów usługi. W klastrze zdefragmentowanej duże obciążenie może umieścić w węzłów czterech lub pięciu bez potrzeby czekania na inne usługi przenieść.

## <a name="defragmentation-pros-and-cons"></a>Defragmentacja zalet i wad
Co to są tych pojęć kompromisy? Poniżej przedstawiono szybki spis kwestii:

| Specjaliści defragmentacji | Defragmentacja wad |
| --- | --- |
| Umożliwia szybsze tworzenie dużych usług |Koncentraty obciążenia na mniejszą liczbę węzłów, zwiększenie rywalizacji |
| Umożliwia obniżyć przenoszenia danych podczas tworzenia |Błędy może mieć wpływ na inne usługi i przenoszenie więcej |
| Umożliwia zaawansowane opis wymagań i odzyskiwanie miejsca |Bardziej złożonych konfiguracji ogólnej zarządzanie zasobami |

Można mieszać zdefragmentowanej i normalnym metryki w tym samym klastrze. Menedżer zasobów klastra próbuje konsolidować możliwie często podczas rozkładanie innych metryk defragmentacji. Wyniki mieszanie defragmentacji i równoważenie strategii zależy od kilka czynników, takich jak:
  - Liczba równoważenia metryki, a liczba defragmentacji metryk
  - Określa, czy usługi używa obu typów metryk 
  - metryki wag
  - ładuje bieżący metryki
  
Eksperymenty jest niezbędne do określenia dokładnej konfiguracji niezbędne. Zalecamy dokładne pomiary obciążeń przed włączeniem metryki defragmentacji w środowisku produkcyjnym. Dotyczy to zwłaszcza mieszanie defragmentacji i zrównoważonym metryki w ramach tej samej usługi. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurowanie metryki defragmentacji
Konfigurowanie metryki defragmentacji jest globalne decyzji w klastrze, a do defragmentacji można wybrać poszczególnych metryki. Poniższe fragmenty kodu config przedstawiają sposób konfigurowania metryki dla defragmentacji. W takim przypadku "Metric1" jest skonfigurowana jako metrykę defragmentacji "Metric2" będzie nadal normalnie zrównoważenia. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

za pomocą pliku ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json dla platformy Azure hostowanej klastrów:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Kolejne kroki
- Menedżer zasobów klastra ma opcje man opisujące klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się w tym artykule na [opisujące klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
- Metryki są zarządzaniu Menedżer zasobów klastra sieci szkieletowej usług konsumenckich i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryki i sposobach ich konfigurowania, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
