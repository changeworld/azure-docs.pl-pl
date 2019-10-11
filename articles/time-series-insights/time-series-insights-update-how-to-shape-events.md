---
title: Shape Events z podglądem Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak kształtować zdarzenia za pomocą wersji zapoznawczej Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: ed0594373c8702ab01b50facaf0ef5ece2d6c7e1
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274278"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Shape Events z podglądem Azure Time Series Insights

Ten artykuł pomaga w utworzeniu kształtu pliku JSON w celu zmaksymalizowania wydajności zapytań Azure Time Series Insights w wersji zapoznawczej.

## <a name="best-practices"></a>Najlepsze praktyki

Zastanów się, jak wysyłasz zdarzenia do Time Series Insights wersji zapoznawczej. Należy zawsze:

* Wysyłaj dane za pośrednictwem sieci tak efektywnie, jak to możliwe.
* Przechowuj swoje dane w sposób, który ułatwia agregowanie go do danego scenariusza.

Aby uzyskać najlepszą możliwą wydajność zapytań, wykonaj następujące czynności:

* Nie wysyłaj niepotrzebnych właściwości. Time Series Insights w wersji zapoznawczej naliczane są opłaty za użycie. Najlepiej przechowywać i przetwarzać dane, które będą używane do wykonywania zapytań.
* Użyj pól wystąpienia dla danych statycznych. To rozwiązanie pomaga uniknąć wysyłania danych statycznych przez sieć. Pola wystąpienia, składnik modelu szeregów czasowych, działają jak dane referencyjne w Time Series Insights ogólnie dostępnej usługi. Aby dowiedzieć się więcej o polach wystąpienia, zobacz [modele szeregów czasowych](./time-series-insights-update-tsm.md).
* Udostępnianie właściwości wymiaru między dwoma lub więcej zdarzeniami. Ta metoda zapewnia wydajniejsze wysyłanie danych przez sieć.
* Nie używaj głębokiego zagnieżdżania tablic. Wersja zapoznawcza Time Series Insights obsługuje do dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Time Series Insights w wersji zapoznawczej spłaszcza tablice w komunikatach do wielu zdarzeń z parami wartości właściwości.
* Jeśli istnieje tylko kilka miar dla wszystkich lub większości zdarzeń, lepiej jest wysłać te miary jako oddzielne właściwości w obrębie tego samego obiektu. Ich wysyłanie osobno zmniejsza liczbę zdarzeń i może poprawić wydajność zapytań, ponieważ wymaga przetworzenia mniejszych zdarzeń.

## <a name="example"></a>Przykład

Poniższy przykład jest oparty na scenariuszu, w którym co najmniej dwa urządzenia wysyłają pomiary lub sygnały. Pomiary lub sygnały mogą być *natężeniem przepływu*, *ciśnieniem oleju*, *temperatury*i *wilgotności*.

W poniższym przykładzie istnieje pojedynczy komunikat IoT Hub platformy Azure, w którym tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiarów. Zewnętrzna tablica używa danych wystąpienia szeregów czasowych, aby zwiększyć wydajność wiadomości. Wystąpienie szeregów czasowych zawiera metadane urządzenia, które nie zmieniają się przy każdym zdarzeniu, ale zapewnia przydatne właściwości analizy danych. Aby zaoszczędzić w bajtach przesyłanych przez sieć i zwiększyć efektywność komunikatów, należy rozważyć tworzenie partii wspólnych wartości wymiarów i korzystanie z metadanych wystąpienia szeregów czasowych.

### <a name="example-json-payload"></a>Przykładowy ładunek JSON

```JSON
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 1.0172575712203979,
                "Engine Oil Pressure psi ": 34.7
            },
            {
                "Flow Rate ft3/s": 2.445906400680542,
                "Engine Oil Pressure psi ": 49.2
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "Flow Rate ft3/s": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

### <a name="time-series-instance"></a>Wystąpienie szeregów czasowych 
> [!NOTE]
> Identyfikator szeregów czasowych to *deviceId*.

```JSON
{
    "timeSeriesId": [
      "FXXX"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "REVOLT SIMULATOR",
      "L2": "Battery System",
    }
  },
  {
    "timeSeriesId": [
      "FYYY"
    ],
    "typeId": "17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds": [
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description": null,
    "instanceFields": {
      "L1": "COMMON SIMULATOR",
      "L2": "Battery System",
    }
  },
```

Time Series Insights Podgląd łączy tabelę (po spłaszczeniu) podczas wykonywania zapytania. Tabela zawiera dodatkowe kolumny, takie jak **Type**. W poniższym przykładzie pokazano, jak można [kształtować](./time-series-insights-send-events.md#supported-json-shapes) dane telemetryczne.

| deviceId  | Typ | L1 | L2 | sygnatura czasowa | wiele. Szybkość przepływu ft3/s | wiele. Psi ciśnienie oleju silnikowego |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | WYKORZYSTANIEM | System baterii | 2018 R-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | WYKORZYSTANIEM |   System baterii |    2018 R-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA COMMON | WYKORZYSTANIEM |    System baterii |    2018 R-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

W powyższym przykładzie należy zwrócić uwagę na następujące kwestie:

* Właściwości statyczne są przechowywane w Time Series Insights wersji zapoznawczej w celu zoptymalizowania danych wysyłanych przez sieć.
* Time Series Insights dane podglądu są przyłączone w czasie zapytania przy użyciu identyfikatora szeregów czasowych, który jest zdefiniowany w wystąpieniu.
* Używane są dwie warstwy zagnieżdżania, które są najbardziej obsługiwane przez Time Series Insights wersja zapoznawcza. Należy unikać głęboko zagnieżdżonych tablic.
* Ponieważ istnieją pewne miary, są one wysyłane jako osobne właściwości w ramach tego samego obiektu. W przykładzie **Seria. Szybkość przepływu psi**, **Seria. Moduł PSI i serie ciśnienia oleju silnikowego** **. Szybkość przepływu ft3/s** są unikatowymi kolumnami.

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane przy użyciu telemetrii. Są one przechowywane z metadanymi w **modelu szeregów czasowych**.
> Powyższa tabela przedstawia widok zapytania.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z zaleceniami, zobacz [Azure Time Series Insights Składnia zapytania w wersji zapoznawczej](./time-series-insights-query-data-csharp.md). Dowiesz się więcej na temat składni zapytania dla interfejsu API REST usługi Time Series Insights w wersji zapoznawczej.
- Aby dowiedzieć się więcej o obsługiwanych kształtach JSON, zobacz [obsługiwane kształty JSON](./time-series-insights-send-events.md#supported-json-shapes).
