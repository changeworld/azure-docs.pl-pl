---
title: Kształt zdarzeń za pomocą usługi Azure czas Series Insights w wersji zapoznawczej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przekształcać zdarzeń Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 344f3a8b1bdfb789251479baffeb4df15bbf2842
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718360"
---
# <a name="shape-events-with-azure-time-series-insights-preview"></a>Kształt zdarzeń za pomocą usługi Azure czas Series Insights w wersji zapoznawczej

Ten artykuł pomoże Ci kształt pliku JSON, aby zmaksymalizować wydajność kwerend Azure czas Series Insights w wersji zapoznawczej.

## <a name="best-practices"></a>Najlepsze praktyki

Należy wziąć pod uwagę sposób wysyłania zdarzeń do czasu Series Insights w wersji zapoznawczej. To znaczy należy zawsze:

* jak najbardziej wydajny wysyłanie danych przez sieć.
* Store dane w sposób, który pomoże Ci agregować je bardziej odpowiednio do danego scenariusza.

Aby uzyskać najlepszą wydajność zapytań możliwe wykonaj następujące czynności:

* Nie wysyłaj niepotrzebnych właściwości. Czas Series Insights w wersji zapoznawczej opłaty naliczane są na wykorzystanie. Najlepiej do przechowywania i przetwarzania danych, który zostanie kwerenda.
* Użyj pól wystąpień dla danych statycznych. Praktyka ta pomaga unikać wysyłania danych statycznych za pośrednictwem sieci. Pola wystąpienia, składnik modelu szeregów czasowych działają podobnie jak odwoływać się do danych w usłudze GA Insights serii czasu. Aby dowiedzieć się więcej na temat wystąpienia pól, zobacz [modeli szeregów czasowych](./time-series-insights-update-tsm.md).
* Udostępnianie właściwości wymiaru spośród dwóch lub większej liczby zdarzeń. Pomaga to bardziej wydajne wysyłanie danych przez sieć.
* Nie używaj tablicy głębokiego zagnieżdżenia. Czas Series Insights w wersji zapoznawczej obsługuje maksymalnie dwóch poziomów zagnieżdżonych tablic zawierających obiekty. Czas Series Insights w wersji zapoznawczej spłaszcza tablic w wiadomości do wielu zdarzeń przy użyciu pary wartości właściwości.
* Jeśli tylko kilka istnieją środki dla wszystkich lub większości zdarzeń, zaleca się wysłać te miary jako osobne właściwości w ramach tego samego obiektu. Wysyłając oddzielnie zmniejsza liczbę zdarzeń i ponieważ mniej zdarzeń wymagających przetworzenia, praktyka wprowadzać zapytania wydajniej.

## <a name="example"></a>Przykład

Poniższy przykład opiera się na scenariuszu, gdy dwie lub większą liczbę urządzeń wysyłać pomiary i sygnały. Pomiary i sygnały może być *przepływ współczynnika*, *wykorzystanie ropa naftowa aparatu*, *temperatury*, i *wilgotności*.

W poniższym przykładzie istnieje pojedynczy komunikat usługi IoT Hub, gdzie zewnętrzne tablica zawiera sekcji udostępnianej typowe wartości wymiarów. Zewnętrzne tablicy używa wystąpienia serii czasu danych w celu zwiększenia efektywności wiadomości. Wystąpienia serii czasu zawiera metadanych urządzeń, która nie zmienia się przy użyciu każdego zdarzenia, ale zapewnia użytecznych właściwości do analizy danych. Aby zapisać na bajty przesyłane przez sieć, a komunikat był bardziej wydajne, należy wziąć pod uwagę dzielenia na partie typowe wartości wymiarów i wykorzystujących metadanych wystąpienia serii czasu.

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
                "Flow Rate psi": 0.58015072345733643,
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

Czas Series Insights w wersji zapoznawczej łączy tabeli (po spłaszczanie) podczas przeszukiwania. W tabeli przedstawiono dodatkowe kolumny **typu**. W poniższym przykładzie pokazano, jak można [kształt](./time-series-insights-send-events.md#json) dane telemetryczne:

| deviceId  | Typ | L1 | PAMIĘCI PODRĘCZNEJ L2 | sygnatura czasowa | Seria. Przepływ współczynnika ft3/s | Seria. Aparat wykorzystanie ropa naftowa psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SYMULATOR REVOLT | System baterii | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA REVOLT | SYMULATOR |    System baterii |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA WSPÓLNE | SYMULATOR |    System baterii |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

W poprzednim przykładzie Pamiętaj o następujących kwestiach:

* Właściwości statyczne są przechowywane w czasu Series Insights w wersji zapoznawczej do optymalizacji danych przesyłanych przez sieć.
* Dane o czasie Series Insights w wersji zapoznawczej jest dołączony w czasie wykonywania zapytań przy użyciu *timeSeriesId* zdefiniowanego w wystąpieniu.
* Używane są dwie warstwy zagnieżdżenia, który jest najbardziej obsługiwaną przez czas Series Insights w wersji zapoznawczej. Koniecznie można uniknąć głęboko zagnieżdżonych tablic.
* Ponieważ istnieje kilka miar, są one wysyłane jako osobne właściwości w ramach tego samego obiektu. W tym przykładzie **serii. Przepływ współczynnika psi**, **serii. Psi wykorzystanie ropa naftowa aparatu**, i **serii. Przepływ współczynnika ft3/s** są unikatowe kolumny.

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane przy użyciu telemetrii. Są one przechowywane przy użyciu metadanych w **modelu szeregów czasowych**.
> Zgodnie z poprzednią tabelą reprezentuje widok zapytania.

## <a name="next-steps"></a>Kolejne kroki

- Aby umieścić te wytyczne w praktyce, zobacz [składni zapytań Azure czas Series Insights w wersji zapoznawczej](./time-series-insights-query-data-csharp.md). Dowiesz się więcej o składni zapytań dla danych czasu Series Insights w wersji zapoznawczej dostęp do interfejsu API REST.

- Aby dowiedzieć się więcej na temat obsługiwane kształty JSON, zobacz [kształty JSON obsługiwany](./time-series-insights-send-events.md#json).