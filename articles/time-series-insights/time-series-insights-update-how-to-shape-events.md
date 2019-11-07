---
title: Shape Events z podglądem Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak kształtować zdarzenia za pomocą wersji zapoznawczej Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/31/2019
ms.custom: seodec18
ms.openlocfilehash: 8b9dd10a4017d821794af037e502c784b10cd62f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585276"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Shape Events z podglądem Azure Time Series Insights

Ten artykuł pomaga w utworzeniu kształtu pliku JSON na potrzeby pozyskiwania i zmaksymalizowania wydajności zapytań Azure Time Series Insights w wersji zapoznawczej.

## <a name="best-practices"></a>Najlepsze praktyki

Zastanów się, jak wysyłasz zdarzenia do Time Series Insights wersji zapoznawczej. Należy zawsze:

* Wysyłaj dane za pośrednictwem sieci tak efektywnie, jak to możliwe.
* Przechowuj swoje dane w sposób, który ułatwia agregowanie go do danego scenariusza.

Aby uzyskać najlepszą wydajność zapytań, wykonaj następujące czynności:

* Nie wysyłaj niepotrzebnych właściwości. Time Series Insights w wersji zapoznawczej naliczane są opłaty za użycie. Najlepiej przechowywać i przetwarzać dane, które będą używane do wykonywania zapytań.
* Użyj pól wystąpienia dla danych statycznych. To rozwiązanie pomaga uniknąć wysyłania danych statycznych przez sieć. Pola wystąpienia, składnik modelu szeregów czasowych, działają jak dane referencyjne w usłudze Time Series Insights, która jest ogólnie dostępna. Aby dowiedzieć się więcej na temat pól wystąpień, zobacz [model szeregów czasowych](./time-series-insights-update-tsm.md).
* Udostępnianie właściwości wymiaru między dwoma lub więcej zdarzeniami. Ta metoda zapewnia wydajniejsze wysyłanie danych przez sieć.
* Nie używaj głębokiego zagnieżdżania tablic. Wersja zapoznawcza Time Series Insights obsługuje do dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Time Series Insights w wersji zapoznawczej spłaszcza tablice w komunikatach do wielu zdarzeń z parami wartości właściwości.
* Jeśli istnieje tylko kilka miar dla wszystkich lub większości zdarzeń, lepiej jest wysłać te miary jako oddzielne właściwości w obrębie tego samego obiektu. Ich wysyłanie osobno zmniejsza liczbę zdarzeń i może poprawić wydajność zapytań, ponieważ wymaga przetworzenia mniejszych zdarzeń.

W trakcie pozyskiwania ładunki zawierające zagnieżdżanie zostaną spłaszczone, tak aby nazwa kolumny była pojedynczą wartością z delineator. Time Series Insights w wersji zapoznawczej używa podkreśleń do rozliczenia. Należy zauważyć, że jest to zmiana z wersji na początku produktu, który używa okresów. W trakcie okresu zapoznawczego istnieje zastrzeżenie dotyczące spłaszczania, które przedstawiono w drugim przykładzie poniżej.

## <a name="examples"></a>Przykłady

Poniższy przykład jest oparty na scenariuszu, w którym co najmniej dwa urządzenia wysyłają pomiary lub sygnały. Pomiary lub sygnały mogą być *natężeniem przepływu*, *ciśnieniem oleju*, *temperatury*i *wilgotności*.

W przykładzie istnieje pojedynczy komunikat IoT Hub platformy Azure, w którym tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiarów. Zewnętrzna tablica używa danych wystąpienia szeregów czasowych, aby zwiększyć wydajność wiadomości. 

Wystąpienie szeregów czasowych zawiera metadane urządzenia. Te metadane nie zmieniają się przy każdym zdarzeniu, ale udostępniają przydatne właściwości analizy danych. Aby zaoszczędzić w bajtach przesyłanych przez sieć i zwiększyć efektywność komunikatów, należy rozważyć tworzenie wsadowe wspólnych wartości wymiarów i korzystanie z metadanych wystąpienia szeregów czasowych.

### <a name="example-1"></a>Przykład 1:

```JSON
[
  {
    "deviceId":"FXXX",
    "timestamp":"2018-01-17T01:17:00Z",
    "series":[
      {
        "Flow Rate ft3/s":1.0172575712203979,
        "Engine Oil Pressure psi ":34.7
      },
      {
        "Flow Rate ft3/s":2.445906400680542,
        "Engine Oil Pressure psi ":49.2
      }
    ]
  },
  {
    "deviceId":"FYYY",
    "timestamp":"2018-01-17T01:18:00Z",
    "series":[
      {
        "Flow Rate ft3/s":0.58015072345733643,
        "Engine Oil Pressure psi ":22.2
      }
    ]
  }
]
```

### <a name="time-series-instance"></a>Wystąpienie szeregów czasowych 

> [!NOTE]
> Identyfikator szeregów czasowych to *deviceId*.

```JSON
[
  {
    "timeSeriesId":[
      "FXXX"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"REVOLT SIMULATOR",
      "L2":"Battery System"
    }
  },
  {
    "timeSeriesId":[
      "FYYY"
    ],
    "typeId":"17150182-daf3-449d-adaf-69c5a7517546",
    "hierarchyIds":[
      "b888bb7f-06f0-4bfd-95c3-fac6032fa4da"
    ],
    "description":null,
    "instanceFields":{
      "L1":"COMMON SIMULATOR",
      "L2":"Battery System"
    }
  }
]
```

Time Series Insights Podgląd łączy tabelę (po spłaszczeniu) podczas wykonywania zapytania. Tabela zawiera dodatkowe kolumny, takie jak **Type**. W poniższym przykładzie pokazano, jak można [kształtować](./time-series-insights-send-events.md#supported-json-shapes) dane telemetryczne.

| deviceId  | Typ | L1 | L2 | sygnatura czasowa | series_Flow rate ft3/s | series_Engine |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | WYKORZYSTANIEM | System baterii | 2018 R-01-17T01:17:00Z |   1.0172575712203979 |    34,7 |
| `FXXX` | Default_Type | WYKORZYSTANIEM |   System baterii |    2018 R-01-17T01:17:00Z | 2.445906400680542 |  49,2 |
| `FYYY` | LINE_DATA COMMON | WYKORZYSTANIEM |    System baterii |    2018 R-01-17T01:18:00Z | 0.58015072345733643 |    22,2 |

W powyższym przykładzie należy zwrócić uwagę na następujące kwestie:

* Właściwości statyczne są przechowywane w Time Series Insights wersji zapoznawczej w celu zoptymalizowania danych wysyłanych przez sieć.
* Time Series Insights dane podglądu są przyłączone w czasie zapytania za pomocą identyfikatora szeregów czasowych zdefiniowanego w wystąpieniu.
* Używane są dwie warstwy zagnieżdżania. Ta liczba jest największa, która obsługuje Time Series Insights w wersji zapoznawczej. Należy unikać głęboko zagnieżdżonych tablic.
* Ponieważ istnieją pewne miary, są one wysyłane jako osobne właściwości w ramach tego samego obiektu. W przykładzie jest to unikatowe kolumny w usłudze **Series_Flow rate** **, psi i** **series_Flow rate ft3/s** .

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane przy użyciu telemetrii. Są one przechowywane z metadanymi w modelu szeregów czasowych.
> Powyższa tabela przedstawia widok zapytania.

### <a name="example-2"></a>Przykład 2:

Weź pod uwagę następujące dane JSON:

```JSON
{
  "deviceId": "FXXX",
  "timestamp": "2019-01-18T01:17:00Z",
  "data": {
        "flow": 1.0172575712203979,
    },
  "data_flow" : 1.76435072345733643
}
```
W powyższym przykładzie właściwość spłaszczone `data_flow` zaprezentuje kolizję nazw z właściwością `data_flow`. W takim przypadku *Najnowsza* wartość właściwości spowoduje zastąpienie wcześniejszej wartości. Jeśli to zachowanie jest wyzwaniem dla scenariuszy firmy, skontaktuj się z zespołem ds. platformy TSI.

> [!WARNING] 
> W przypadkach, gdy zduplikowane właściwości znajdują się w tym samym ładunku zdarzeń z powodu spłaszczania lub innego mechanizmu, najnowsza wartość właściwości jest przechowywana, overwritting wszystkie poprzednie wartości.


## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z zaleceniami, zobacz [Azure Time Series Insights Składnia zapytania w wersji zapoznawczej](./time-series-insights-query-data-csharp.md). Dowiesz się więcej na temat składni zapytania dla interfejsu API REST usługi Time Series Insights Preview na potrzeby dostępu do danych.
- Aby dowiedzieć się więcej o obsługiwanych kształtach JSON, zobacz [obsługiwane kształty JSON](./time-series-insights-send-events.md#supported-json-shapes).
