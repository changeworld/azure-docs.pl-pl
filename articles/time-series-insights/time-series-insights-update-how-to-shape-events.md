---
title: Zdarzenia kształtu — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o najlepszych rozwiązaniach i sposobie kształtowania zdarzeń podczas wykonywania zapytań w usłudze Azure Time Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/24/2020
ms.custom: seodec18
ms.openlocfilehash: 99a2f32c3f76d7fec475c9b299f7208b4db29cfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650927"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Kształtowanie zdarzeń za pomocą usługi Azure Time Series Insights Preview

W tym artykule zdefiniowano najlepsze rozwiązania dotyczące kształtowania ładunków JSON do pozyskiwania w usłudze Azure Time Series Insights i maksymalizacji wydajności zapytań w wersji zapoznawczej.

## <a name="best-practices"></a>Najlepsze rozwiązania

Najlepiej jest dokładnie rozważyć sposób wysyłania zdarzeń do środowiska usługi Time Series Insights Preview. 

Najważniejsze najlepsze praktyki obejmują:

* Przesyłaj dane przez sieć tak efektywnie, jak to możliwe.
* Przechowuj dane w sposób, który pomaga agregować je bardziej odpowiednio dla twojego scenariusza.

Aby uzyskać najlepszą wydajność kwerendy, należy przestrzegać następujących reguł:

* Nie wysyłaj niepotrzebnych właściwości. Opłaty podglądowe usługi Time Series Insights według użycia. Najlepiej przechowywać i przetwarzać tylko te dane, które będą wyszukiwane.
* Użyj pól instancji dla danych statycznych. Ta praktyka pomaga uniknąć wysyłania danych statycznych przez sieć. Pola wystąpienia, składnik modelu szeregów czasowych, działają jak dane referencyjne w usłudze Time Series Insights, która jest ogólnie dostępna. Aby dowiedzieć się więcej o polach wystąpień, przeczytaj [artykuł Model szeregów czasowych](./time-series-insights-update-tsm.md).
* Udostępnij właściwości wymiaru między co najmniej dwoma zdarzeniami. Ta praktyka ułatwia bardziej efektywne wysyłanie danych przez sieć.
* Nie należy używać zagnieżdżania tablic głębokich. Aplikacja Time Series Insights Preview obsługuje maksymalnie dwa poziomy tablic zagnieżdżonych zawierających obiekty. Podgląd usługi Time Series Insights w usłudze Spłaszcza tablice w wiadomościach na wiele zdarzeń z parami wartości właściwości.
* Jeśli istnieje tylko kilka środków dla wszystkich lub większości zdarzeń, lepiej jest wysłać te środki jako oddzielne właściwości w tym samym obiekcie. Wysyłanie ich oddzielnie zmniejsza liczbę zdarzeń i może zwiększyć wydajność kwerendy, ponieważ mniej zdarzeń muszą być przetwarzane.

## <a name="column-flattening"></a>Spłaszczanie kolumny

Podczas pozyskiwania ładunki zawierające obiekty zagnieżdżone zostaną spłaszczone, tak aby nazwa kolumny była pojedynczą wartością z delineatorem.

* Na przykład następujące zagnieżdżone JSON:

   ```JSON
   "data": {
        "flow": 1.0172575712203979,
   },
   ```

   Staje `data_flow` się: po spłaszczeniu.

> [!IMPORTANT]
> * Usługa Azure Time Series Insights`_`Preview używa podkreśleń ( ) do wyznaczania kolumn.
> * Zwróć uwagę na różnicę w stosunku`.`do ogólnej dostępności, która używa kropek ( ) zamiast.

Bardziej złożone scenariusze przedstawiono poniżej.

#### <a name="example-1"></a>Przykład 1:

Poniższy scenariusz ma dwa (lub więcej) urządzenia, które wysyłają pomiary (sygnały): *Natężenie przepływu,* *Ciśnienie oleju silnikowego,* *Temperatura*i *Wilgotność*.

Istnieje jeden komunikat usługi Azure IoT Hub wysłany, gdzie tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiarów (zwróć uwagę na dwa wpisy urządzenia zawarte w wiadomości).

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

**Wynos:**

* Przykład JSON ma tablicę zewnętrzną, która używa danych [wystąpienia szeregów czasowych](./time-series-insights-update-tsm.md#time-series-model-instances) w celu zwiększenia wydajności wiadomości. Mimo że metadane urządzenia wystąpienia szeregów czasowych prawdopodobnie się nie zmienią, często udostępnia przydatne właściwości do analizy danych.

* JSON łączy dwa lub więcej komunikatów (po jednym z każdego urządzenia) w jedną oszczędność ładunku na przepustowość w czasie.

* Poszczególne punkty danych serii dla każdego urządzenia są łączone w jeden atrybut **serii,** zmniejszając potrzebę ciągłego przesyłania strumieniowego aktualizacji dla każdego urządzenia.

> [!TIP]
> Aby zmniejszyć liczbę komunikatów wymaganych do wysyłania danych i uczynić dane telemetryczne bardziej wydajne, należy wziąć pod uwagę przetwarzanie wsadowych wspólnych wartości wymiarów i metadanych wystąpienia szeregów czasowych w jednym ładunku JSON.

#### <a name="time-series-instance"></a>Wystąpienie szeregów czasowych 

Przyjrzyjmy się bliżej, jak używać [wystąpienia szeregów czasowych](./time-series-insights-update-tsm.md#time-series-model-instances) do bardziej optymalnego kształtowania JSON. 

> [!NOTE]
> Poniższe [identyfikatory szeregów czasowych](./time-series-insights-update-how-to-id.md) to *identyfikatory deviceIds*.

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

Aplikacja Time Series Insights Preview łączy tabelę (po spłaszczeniu) w czasie kwerendy. Tabela zawiera dodatkowe kolumny, takie jak **Typ**.

| deviceId  | Typ | L1 (właso. | L2 (l2) | sygnatura czasowa | series_Flow Szybkość ft3/s | series_Engine Ciśnienie oleju psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | Simulator | System baterii | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | Simulator |   System baterii |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA WSPÓLNY | Simulator |    System baterii |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

> [!NOTE]
>  Poprzednia tabela reprezentuje widok kwerendy w [Eksploratorze podglądu szeregów czasowych](./time-series-insights-update-explorer.md).

**Wynos:**

* W poprzednim przykładzie właściwości statyczne są przechowywane w podglądzie usługi Time Series Insights w celu optymalizacji danych przesyłanych przez sieć.
* Dane usługi Time Series Insights Preview są dołączane w czasie zapytania za pomocą identyfikatora szeregów czasowych zdefiniowanego w wystąpieniu.
* Używane są dwie warstwy zagnieżdżania. Ta liczba jest najbardziej, że usługi Time Series Insights Preview obsługuje. Jest to bardzo ważne, aby uniknąć głęboko zagnieżdżonych tablic.
* Ponieważ istnieje kilka miar, są one wysyłane jako oddzielne właściwości w tym samym obiekcie. W tym przykładzie **series_Flow Rate psi**, **series_Engine Oil Pressure psi**i **series_Flow Rate ft3/s** są unikatowymi kolumnami.

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane za pomocą danych telemetrycznych. Są one przechowywane z metadanymi w modelu szeregów czasowych.

#### <a name="example-2"></a>Przykład 2:

Należy wziąć pod uwagę następujące JSON:

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

W powyższym przykładzie spłaszczona `data["flow"]` właściwość przedstawia kolizję nazewnictwa z właściwością. `data_flow`

W takim przypadku *wartość najnowszej* właściwości zastąpi wcześniejszą. 

> [!TIP]
> Skontaktuj się z zespołem Time Series Insights, aby uzyskać więcej pomocy!

> [!WARNING] 
> * W przypadkach, gdy zduplikowane właściwości są obecne w tym samym (liczba pojedyncza) ładunku zdarzenia z powodu spłaszczenia lub innego mechanizmu, najnowsza wartość właściwości > jest przechowywana, nadpisując wszystkie poprzednie wartości.
> * Seria połączonych zdarzeń nie zastąpi się nawzajem.

## <a name="next-steps"></a>Następne kroki

* Aby wprowadzić te wskazówki w życie, przeczytaj [składnię kwerendy usługi Azure Time Series Insights Preview](./time-series-insights-query-data-csharp.md). Dowiesz się więcej na temat składni kwerendy dla interfejsu API REST usługi Rest usługi Time Series Insights [preview.](https://docs.microsoft.com/rest/api/time-series-insights/preview)

* Połącz najlepsze rozwiązania JSON z [modelem serii czasowych](./time-series-insights-update-how-to-tsm.md).
