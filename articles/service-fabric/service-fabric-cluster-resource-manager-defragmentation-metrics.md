---
title: Defragmentacji metryk w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Omówienie przy użyciu defragmentacji lub pakowania jako strategii metryk w usłudze Service Fabric
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: e5ebfae5-c8f7-4d6c-9173-3e22a9730552
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6e041e41372c72c6792c1fb4a1fbdc3bbe475b21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844413"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentacji metryk i obciążenia w usłudze Service Fabric
Menedżer zasobów klastra Service Fabric Service domyślną strategię zarządzania metryk obciążenia w klastrze jest rozdzielenie obciążenia. Zapewnienie, że węzły są równomiernie wykorzystywane pozwala uniknąć gorące i zimne punkty, które mogą prowadzić do rywalizacji o zasoby i ograniczenia marnowania zasobów. Dystrybucja obciążeń w klastrze jest również najbezpieczniejszy pod względem pozostałych błędy, ponieważ gwarantuje to, że błąd nie przyjmuje się znaczną część danego obciążenia. 

Menedżer zasobów klastra usługi Service Fabric obsługuje innych strategii zarządzania obciążeniem, który jest defragmentacji. Defragmentacja oznacza, że zamiast próbować rozdystrybuować wykorzystanie metryki klastra, jego konsolidacji. Konsolidacja jest po prostu odwrócenie domyślne równoważenie strategii — zamiast minimalizując średni odchylenie standardowe metryki obciążenia, Menedżer zasobów klastra próbuje zwiększyć go.

## <a name="when-to-use-defragmentation"></a>Kiedy należy używać defragmentacji
Dystrybucja obciążenia w klastrze zużywa niektórych zasobów w każdym węźle. Niektórych obciążeń tworzenie usług, które są wyjątkowo dużą i używać większości lub wszystkich węzła. W takich przypadkach istnieje możliwość, że w przypadku dużych obciążeń, które tworzonych nie ma wystarczającej ilości miejsca na dowolnym węźle, aby uruchomić je. Dużych obciążeń nie są problemem w usłudze Service Fabric; w takich przypadkach Menedżer zasobów klastra ustala, że potrzebuje reorganizacja klastra, aby zwolnić miejsce dla to duże obciążenie. Jednak w tym samym czasie tego obciążenia ma oczekiwać na zaplanowane w klastrze.

W przypadku wielu usług i stanie, aby poruszać się, jego może potrwać dłuższy czas, w przypadku dużych obciążeń do umieszczenia w klastrze. Jest to bardziej prawdopodobne w przypadku innych obciążeń w klastrze są również dużych, a więc trwać dłużej reorganizacja. Zespół usługi Service Fabric mierzy czasy tworzenia w symulacji tego scenariusza. Stwierdziliśmy, że tworzenie dużych usług trwało znacznie zaraz po wykorzystaniu klastra stało się powyżej od 30% do 50%. Aby obsługiwać ten scenariusz, wprowadziliśmy defragmentacji jako równoważenia strategii. Stwierdziliśmy, że w przypadku dużych obciążeń, szczególnie te, których czas utworzenia było ważne defragmentacji naprawdę brały udział w tych nowych obciążeń pobieranie zaplanowane w klastrze.

Można skonfigurować defragmentacji metryk, aby Menedżer zasobów klastra do aktywnego próbuje zmniejszyć obciążenie usługi do mniejszej liczby węzłów. Pozwala to zagwarantować, że jest prawie zawsze miejsca dla dużych usług bez reorganizacja klastra. Brak konieczności reorganizować klastra umożliwia szybkie tworzenie dużych obciążeń.

Większość osób nie ma potrzeby defragmentacji. Usługi są zwykle jest małe, więc nie jest trudno znaleźć miejsca dla je w klastrze. Gdy reorganizacja jest możliwe, jej przebiega szybko, ponownie, ponieważ większość usług są małe i mogą zostać przeniesione, szybko i równolegle. Jeśli jednak masz duży usług i potrzebujesz je szybko tworzone następnie strategii defragmentacji jest dla Ciebie. Wady przy użyciu defragmentacji, następnie omówimy w dalszej części. 

## <a name="defragmentation-tradeoffs"></a>Defragmentacja wady i zalety
Defragmentacja zwiększyć impactfulness błędów, ponieważ więcej usługi są uruchomione w węzłach, które nie spełniają. Defragmentacja wydłuża się też kosztów, ponieważ zasoby w klastrze musi być przechowywany w rezerwie, oczekiwanie na tworzenie dużych obciążeń.

Poniższy diagram oferuje wizualną reprezentację dwa klastry, taki, który jest defragmentacji i taki, który nie jest. 

<center>

![Porównywanie równoważenia i defragmentacji klastrów][Image1]
</center>

W przypadku o zrównoważonym obciążeniu należy wziąć pod uwagę liczbę przepływów, które byłyby niezbędna do umieszczenia w jednym z największych obiektów usługi. Zdefragmentowanej klastra duże obciążenie może umieszczane w węzłach, cztery lub pięć bez konieczności oczekiwania na inne usługi przenieść.

## <a name="defragmentation-pros-and-cons"></a>Defragmentacja zalety i wady
Dlatego co to są tych pojęciach kompromisów? Poniżej przedstawiono szybki spis warto przemyśleć:

| Specjaliści defragmentacji | Defragmentacja wad |
| --- | --- |
| Umożliwia szybsze tworzenie dużych usług |Koncentraty obciążenia na mniejszą liczbę węzłów, zwiększając rywalizacji o zasoby |
| Pozwala zmniejszyć przenoszenia danych podczas tworzenia |Awarii może mieć wpływ na inne usługi i częściej |
| Pozwala na zaawansowane opis wymagań i odzyskiwanie miejsca |Bardziej złożone ogólnej konfiguracji zarządzania zasobami |

Możesz mieszać zdefragmentowanej i normalnym metryki w tym samym klastrze. Menedżer zasobów klastra próbuje skonsolidować możliwie jak najbardziej przy rozkładanie innych metryk defragmentacji. Wyniki mieszanie defragmentacji i równoważenie strategii zależy od kilku czynników, takich jak:
  - Liczba równoważenia metryki, a liczba defragmentacji metryk
  - Czy dowolna usługa używa oba rodzaje metryk 
  - Waga metryki
  - ładuje bieżącego metryki
  
Eksperymentowanie w usłudze jest wymagane do określenia dokładnej konfiguracji niezbędne. Zalecamy dokładne pomiary obciążeń, przed włączeniem defragmentacji metryk w środowisku produkcyjnym. Jest to szczególnie istotne w przypadku, gdy łączenie defragmentacji i metryki o zrównoważonym obciążeniu w ramach tej samej usługi. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurowanie defragmentacji metryk
Konfigurowanie defragmentacji metryk jest globalne decyzji w klastrze, a do defragmentacji można wybrać poszczególne metryki. Poniższe fragmenty kodu konfiguracji pokazują, jak skonfigurować defragmentacji metryk. W tym przypadku "Metric1" jest skonfigurowane jako metrykę defragmentacji, podczas gdy "Metric2" będą w dalszym ciągu być równoważony normalnie. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

za pomocą ClusterConfig.json dla autonomicznych wdrożeniach lub Template.json na platformie Azure hostowane klastrów:

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
- Menedżer zasobów klastra ma opcji man zawierająca opis klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tego artykułu na [opisujące klaster usługi Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
- Metryki są, jak Menedżer zasobów usługi Service Fabric klaster zarządza użycia i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryk i sposobach ich konfigurowania, zapoznaj się z [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
