---
title: Zdarzenia kształtu — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej o najlepszych rozwiązaniach i sposobach tworzenia kształtów zapytań w usłudze Azure Time Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/16/2019
ms.custom: seodec18
ms.openlocfilehash: fd744e6283b00b0dfdd50805cb628f5bc40ab8d6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846138"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Kształt zdarzeń za pomocą usługi Azure czas Series Insights w wersji zapoznawczej

Ten artykuł pomaga w utworzeniu kształtu pliku JSON na potrzeby pozyskiwania i zmaksymalizowania wydajności zapytań Azure Time Series Insights w wersji zapoznawczej.

## <a name="best-practices"></a>Najlepsze rozwiązania

Zastanów się, jak wysyłasz zdarzenia do Time Series Insights wersji zapoznawczej. To znaczy należy zawsze:

* jak najbardziej wydajny wysyłanie danych przez sieć.
* Store dane w sposób, który pomoże Ci agregować je bardziej odpowiednio do danego scenariusza.

Aby uzyskać najlepszą wydajność zapytań, wykonaj następujące czynności:

* Nie wysyłaj niepotrzebnych właściwości. Czas Series Insights w wersji zapoznawczej opłaty naliczane są na wykorzystanie. Najlepiej do przechowywania i przetwarzania danych, który zostanie kwerenda.
* Użyj pól wystąpień dla danych statycznych. Praktyka ta pomaga unikać wysyłania danych statycznych za pośrednictwem sieci. Pola wystąpienia, składnik modelu szeregów czasowych, działają jak dane referencyjne w usłudze Time Series Insights, która jest ogólnie dostępna. Aby dowiedzieć się więcej o polach wystąpienia, Odczytaj [model szeregów czasowych](./time-series-insights-update-tsm.md).
* Udostępnianie właściwości wymiaru spośród dwóch lub większej liczby zdarzeń. Pomaga to bardziej wydajne wysyłanie danych przez sieć.
* Nie używaj tablicy głębokiego zagnieżdżenia. Wersja zapoznawcza Time Series Insights obsługuje do dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Czas Series Insights w wersji zapoznawczej spłaszcza tablic w wiadomości do wielu zdarzeń przy użyciu pary wartości właściwości.
* Jeśli tylko kilka istnieją środki dla wszystkich lub większości zdarzeń, zaleca się wysłać te miary jako osobne właściwości w ramach tego samego obiektu. Ich wysyłanie osobno zmniejsza liczbę zdarzeń i może poprawić wydajność zapytań, ponieważ wymaga przetworzenia mniejszych zdarzeń.

W trakcie pozyskiwania ładunki zawierające zagnieżdżanie zostaną spłaszczone, tak aby nazwa kolumny była pojedynczą wartością z delineator. Time Series Insights w wersji zapoznawczej używa podkreśleń do rozliczenia. Należy zauważyć, że jest to zmiana z wersji na początku produktu, który używa okresów. W trakcie okresu zapoznawczego istnieje zastrzeżenie dotyczące spłaszczania, które przedstawiono w drugim przykładzie poniżej.

## <a name="examples"></a>Przykłady

Poniższy przykład opiera się na scenariuszu, gdy dwie lub większą liczbę urządzeń wysyłać pomiary i sygnały. Pomiary lub sygnały mogą być *natężeniem przepływu*, *ciśnieniem oleju*, *temperatury*i *wilgotności*.

W przykładzie istnieje pojedynczy komunikat IoT Hub platformy Azure, w którym tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiarów. Zewnętrzne tablicy używa wystąpienia serii czasu danych w celu zwiększenia efektywności wiadomości. 

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

### <a name="time-series-instance"></a>Wystąpienia serii czasu 

> [!NOTE]
> Identyfikator serii czasu jest *deviceId*.

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

Czas Series Insights w wersji zapoznawczej łączy tabeli (po spłaszczanie) podczas przeszukiwania. W tabeli przedstawiono dodatkowe kolumny **typu**. W poniższym przykładzie pokazano, jak można [kształtować](./time-series-insights-send-events.md#supported-json-shapes) dane telemetryczne.

| deviceId  | Typ | L1 | PAMIĘCI PODRĘCZNEJ L2 | sygnatura czasowa | Częstotliwość series_Flow ft3/s | series_Engine z użyciem nacisku oleju |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SYMULATOR | System baterii | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SYMULATOR |   System baterii |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA WSPÓLNE | SYMULATOR |    System baterii |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

W poprzednim przykładzie Pamiętaj o następujących kwestiach:

* Właściwości statyczne są przechowywane w czasu Series Insights w wersji zapoznawczej do optymalizacji danych przesyłanych przez sieć.
* Time Series Insights dane podglądu są przyłączone w czasie zapytania za pomocą identyfikatora szeregów czasowych zdefiniowanego w wystąpieniu.
* Używane są dwie warstwy zagnieżdżania. Ta liczba jest największa, która obsługuje Time Series Insights w wersji zapoznawczej. Koniecznie można uniknąć głęboko zagnieżdżonych tablic.
* Ponieważ istnieje kilka miar, są one wysyłane jako osobne właściwości w ramach tego samego obiektu. W przykładzie jest to unikatowymi kolumnami **interfejsu psi Series_Flow rate**, **series_Engine psi**oraz **series_Flow rate ft3/s** .

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane przy użyciu telemetrii. Są one przechowywane z metadanymi w modelu szeregów czasowych.
> Zgodnie z poprzednią tabelą reprezentuje widok zapytania.

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

Aby zapoznać się z zaleceniami, Przeczytaj [Azure Time Series Insights wersja zapoznawcza zapytania](./time-series-insights-query-data-csharp.md). Dowiesz się więcej na temat składni zapytania dla interfejsu API REST usługi Time Series Insights Preview na potrzeby dostępu do danych.
