---
title: Defragmentacja metryk w sieci szkieletowej usług Azure
description: Dowiedz się więcej o używaniu defragmentacji lub pakowania jako strategii dla metryk w sieci szkieletowej usług. Ta technika jest przydatna w przypadku bardzo dużych usług.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bba459be4408f4a4bc438bb33b0570a91e84f2cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75563364"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Defragmentacja metryk i obciążenia w sieci szkieletowej usług
Domyślną strategią Menedżera zasobów klastra sieci szkieletowej usług do zarządzania metrykami obciążenia w klastrze jest dystrybucja obciążenia. Zapewnienie, że węzły są równomiernie wykorzystywane, pozwala uniknąć gorących i zimnych punktów, które prowadzą zarówno do rywalizacji, jak i zmarnowanych zasobów. Dystrybucja obciążeń w klastrze jest również najbezpieczniejszym pod względem zachowanych awarii, ponieważ zapewnia, że awaria nie wyjmuje dużego procentu danego obciążenia. 

Menedżer zasobów klastra sieci szkieletowej usług obsługuje inną strategię zarządzania obciążeniem, czyli defragmentacją. Defragmentacja oznacza, że zamiast próbować dystrybuować wykorzystanie metryki w klastrze, jest konsolidowana. Konsolidacja to tylko odwrócenie domyślnej strategii równoważenia — zamiast minimalizować średnie odchylenie standardowe obciążenia metryki, Menedżer zasobów klastra próbuje go zwiększyć.

## <a name="when-to-use-defragmentation"></a>Kiedy stosować defragmentację
Dystrybucja obciążenia w klastrze zużywa niektóre zasoby w każdym węźle. Niektóre obciążenia tworzą usługi, które są wyjątkowo duże i zużywają większość lub wszystkie węzła. W takich przypadkach jest możliwe, że gdy istnieją duże obciążenia coraz tworzone, że nie ma wystarczającej ilości miejsca w dowolnym węźle, aby je uruchomić. Duże obciążenia nie są problemem w sieci szkieletowej usług; w takich przypadkach Menedżer zasobów klastra określa, że musi zreorganizować klaster, aby zrobić miejsce dla tego dużego obciążenia. Jednak w międzyczasie, że obciążenie musi czekać do zaplanowania w klastrze.

Jeśli istnieje wiele usług i stanu do poruszania się, może upłynąć dużo czasu dla dużego obciążenia, które mają być umieszczone w klastrze. Jest to bardziej prawdopodobne, jeśli inne obciążenia w klastrze są również duże, a więc trwać dłużej, aby reorganizacji. Zespół sieci szkieletowej usług zmierzył czasy tworzenia w symulacjach tego scenariusza. Okazało się, że tworzenie dużych usług trwało znacznie dłużej, gdy tylko wykorzystanie klastra przekroczyło 30% do 50%. Aby obsłużyć ten scenariusz, wprowadziliśmy defragmentację jako strategię równoważenia. Okazało się, że dla dużych obciążeń, zwłaszcza tych, w których czas tworzenia był ważny, defragmentacja naprawdę pomogła tym nowym obciążeniom uzyskać zaplanowane w klastrze.

Metryki defragmentacji można skonfigurować tak, aby Menedżer zasobów klastra aktywnie próbował skondensować obciążenie usług w mniejszej liczbie węzłów. Pomaga to zapewnić, że prawie zawsze jest miejsce na duże usługi bez reorganizacji klastra. Brak konieczności reorganizacji klastra umożliwia szybkie tworzenie dużych obciążeń.

Większość ludzi nie potrzebuje defragmentacji. Usługi są zwykle małe, więc nie jest trudno znaleźć dla nich miejsce w klastrze. Gdy reorganizacja jest możliwa, idzie szybko, ponownie, ponieważ większość usług jest mała i może być przenoszona szybko i równolegle. Jeśli jednak masz duże usługi i potrzebujesz ich szybko utworzonych, strategia defragmentacji jest dla Ciebie. Omówimy kompromisy z wykorzystaniem defragmentacji dalej. 

## <a name="defragmentation-tradeoffs"></a>Defragmentacji kompromisów
Defragmentacja może zwiększyć wpływ błędów, ponieważ więcej usług są uruchomione w węzłach, które nie powiodą się. Defragmentacja może również zwiększyć koszty, ponieważ zasoby w klastrze muszą być przechowywane w rezerwie, czekając na utworzenie dużych obciążeń.

Poniższy diagram daje wizualną reprezentację dwóch klastrów, jeden, który jest defragmentacji i jeden, który nie jest. 

<center>

![Porównywanie klastrów zrównoważonych i defragmentowanych][Image1]
</center>

W przypadku zrównoważonym należy wziąć pod uwagę liczbę ruchów, które byłyby niezbędne do umieszczenia jednego z największych obiektów usługi. W klastrze defragmentacji duże obciążenie można umieścić w węzłach cztery lub pięć bez konieczności oczekiwania na inne usługi do przeniesienia.

## <a name="defragmentation-pros-and-cons"></a>Defragmentacja plusy i minusy
Więc jakie są te inne kompromisy koncepcyjne? Oto krótka tabela rzeczy do przemyślenia:

| Plusy defragmentacji | Defragmentacja Minusy |
| --- | --- |
| Umożliwia szybsze tworzenie dużych usług |Koncentruje się na mniejszej liczbie węzłów, zwiększając rywalizację |
| Umożliwia mniejszy ruch danych podczas tworzenia |Awarie mogą mieć wpływ na więcej usług i powodować więcej zmian |
| Umożliwia bogaty opis wymagań i regenerację przestrzeni |Bardziej złożona ogólna konfiguracja zarządzania zasobami |

Metryki defragmentacji i normalnej można mieszać w tym samym klastrze. Menedżer zasobów klastra próbuje skonsolidować metryki defragmentacji w jak największym stopniu, podczas gdy rozmieszczanie innych. Wyniki mieszania strategii defragmentacji i równoważenia zależą od kilku czynników, w tym:
  - liczba wskaźników równoważenia a liczba wskaźników defragmentacji
  - Określa, czy którakolwiek z usług korzysta z obu typów metryk 
  - wagi metryczne
  - obciążenia prądowe metryki
  
Eksperymentowanie jest wymagane do określenia dokładnej konfiguracji niezbędne. Firma Microsoft zaleca dokładny pomiar obciążeń przed włączeniem metryk defragmentacji w produkcji. Jest to szczególnie ważne podczas mieszania defragmentacji i zrównoważonych metryk w ramach tej samej usługi. 

## <a name="configuring-defragmentation-metrics"></a>Konfigurowanie metryk defragmentacji
Konfigurowanie metryk defragmentacji jest decyzją globalną w klastrze, a poszczególne metryki można wybrać do defragmentacji. Poniższe fragmenty konfiguracyjne pokazują, jak skonfigurować metryki do defragmentacji. W takim przypadku "Metric1" jest skonfigurowany jako metryka defragmentacji, podczas gdy "Metric2" będzie nadal normalnie równoważona. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

za pośrednictwem clusterconfig.json dla wdrożeń autonomicznych lub Template.json dla klastrów hostowanych platformy Azure:

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


## <a name="next-steps"></a>Następne kroki
- Menedżer zasobów klastra ma opcje man do opisywania klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tym [artykułem opisującym klaster sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
- Metryki są jak menedżer zasobów klastra sieci szkieletowej usług zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o danych i sposobie ich konfigurowania, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
