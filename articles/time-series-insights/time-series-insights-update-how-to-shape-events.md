---
title: Kształt zdarzeń za pomocą usługi Azure czas Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: e1eb0d7450e1a7f263f29b8d4657547dd85d4276
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883293"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Kształt zdarzeń za pomocą usługi Azure czas Series Insights w wersji zapoznawczej

Ten artykuł pomoże Ci kształt pliku JSON, aby zmaksymalizować wydajność kwerend Azure czas Series Insights w wersji zapoznawczej.

## <a name="best-practices"></a>Najlepsze praktyki

Zastanów się, jak wysyłasz zdarzenia do Time Series Insights wersji zapoznawczej. To znaczy należy zawsze:

* jak najbardziej wydajny wysyłanie danych przez sieć.
* Store dane w sposób, który pomoże Ci agregować je bardziej odpowiednio do danego scenariusza.

Aby uzyskać najlepszą wydajność zapytań możliwe wykonaj następujące czynności:

* Nie wysyłaj niepotrzebnych właściwości. Czas Series Insights w wersji zapoznawczej opłaty naliczane są na wykorzystanie. Najlepiej do przechowywania i przetwarzania danych, który zostanie kwerenda.
* Użyj pól wystąpień dla danych statycznych. Praktyka ta pomaga unikać wysyłania danych statycznych za pośrednictwem sieci. Pola wystąpienia, składnik modelu szeregów czasowych, działają jak dane referencyjne w Time Series Insights ogólnie dostępnej usługi. Aby dowiedzieć się więcej na temat wystąpienia pól, zobacz [modeli szeregów czasowych](./time-series-insights-update-tsm.md).
* Udostępnianie właściwości wymiaru spośród dwóch lub większej liczby zdarzeń. Pomaga to bardziej wydajne wysyłanie danych przez sieć.
* Nie używaj tablicy głębokiego zagnieżdżenia. Wersja zapoznawcza Time Series Insights obsługuje do dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Czas Series Insights w wersji zapoznawczej spłaszcza tablic w wiadomości do wielu zdarzeń przy użyciu pary wartości właściwości.
* Jeśli tylko kilka istnieją środki dla wszystkich lub większości zdarzeń, zaleca się wysłać te miary jako osobne właściwości w ramach tego samego obiektu. Ich wysyłanie osobno zmniejsza liczbę zdarzeń i może poprawić wydajność zapytań, ponieważ wymaga przetworzenia mniejszych zdarzeń.

## <a name="example"></a>Przykład

Poniższy przykład opiera się na scenariuszu, gdy dwie lub większą liczbę urządzeń wysyłać pomiary i sygnały. Pomiary lub sygnały mogą być *natężeniem przepływu*, *ciśnieniem oleju*, *temperatury*i *wilgotności*.

W poniższym przykładzie istnieje pojedynczy komunikat IoT Hub platformy Azure, w którym tablica zewnętrzna zawiera wspólną sekcję wspólnych wartości wymiarów. Zewnętrzne tablicy używa wystąpienia serii czasu danych w celu zwiększenia efektywności wiadomości. Wystąpienia serii czasu zawiera metadanych urządzeń, która nie zmienia się przy użyciu każdego zdarzenia, ale zapewnia użytecznych właściwości do analizy danych. Aby zapisać na bajty przesyłane przez sieć, a komunikat był bardziej wydajne, należy wziąć pod uwagę dzielenia na partie typowe wartości wymiarów i wykorzystujących metadanych wystąpienia serii czasu.

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

### <a name="time-series-instance"></a>Wystąpienia serii czasu 
> [!NOTE]
> Identyfikator serii czasu jest *deviceId*.

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

Czas Series Insights w wersji zapoznawczej łączy tabeli (po spłaszczanie) podczas przeszukiwania. W tabeli przedstawiono dodatkowe kolumny **typu**. W poniższym przykładzie pokazano, jak można [kształtować](./time-series-insights-send-events.md#json) dane telemetryczne.

| deviceId  | Typ | L1 | PAMIĘCI PODRĘCZNEJ L2 | sygnatura czasowa | Seria. Przepływ współczynnika ft3/s | Seria. Aparat wykorzystanie ropa naftowa psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SYMULATOR | System baterii | 2018-01-17T01:17:00Z |   1.0172575712203979 |    34.7 |
| `FXXX` | Default_Type | SYMULATOR |   System baterii |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA WSPÓLNE | SYMULATOR |    System baterii |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

W poprzednim przykładzie Pamiętaj o następujących kwestiach:

* Właściwości statyczne są przechowywane w czasu Series Insights w wersji zapoznawczej do optymalizacji danych przesyłanych przez sieć.
* Time Series Insights dane podglądu są przyłączone w czasie zapytania przy użyciu identyfikatora szeregów czasowych, który jest zdefiniowany w wystąpieniu.
* Używane są dwie warstwy zagnieżdżenia, który jest najbardziej obsługiwaną przez czas Series Insights w wersji zapoznawczej. Koniecznie można uniknąć głęboko zagnieżdżonych tablic.
* Ponieważ istnieje kilka miar, są one wysyłane jako osobne właściwości w ramach tego samego obiektu. W tym przykładzie **serii. Przepływ współczynnika psi**, **serii. Psi wykorzystanie ropa naftowa aparatu**, i **serii. Przepływ współczynnika ft3/s** są unikatowe kolumny.

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane przy użyciu telemetrii. Są one przechowywane z metadanymi w **modelu szeregów czasowych**.
> Zgodnie z poprzednią tabelą reprezentuje widok zapytania.

## <a name="next-steps"></a>Kolejne kroki

- Aby umieścić te wytyczne w praktyce, zobacz [składni zapytań Azure czas Series Insights w wersji zapoznawczej](./time-series-insights-query-data-csharp.md). Dowiesz się więcej o składni zapytań dla danych czasu Series Insights w wersji zapoznawczej dostęp do interfejsu API REST.
- Aby dowiedzieć się więcej na temat obsługiwane kształty JSON, zobacz [kształty JSON obsługiwany](./time-series-insights-send-events.md#json).
