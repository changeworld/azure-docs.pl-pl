---
title: Jak kształt zdarzeń za pomocą usługi Azure Time Series Insights (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu kształt zdarzeń za pomocą usługi Azure Time Series Insights (wersja zapoznawcza)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: edc1dac05a8ab4281eee3ee0eb4c5e6b7571b404
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856434"
---
# <a name="shaping-events-with-azure-time-series-insights-preview"></a>Kształtowanie zdarzeń za pomocą usługi Azure Time Series Insights (wersja zapoznawcza)

Ten artykuł zawiera wskazówki dotyczące kształtowania JSON, aby zmaksymalizować wydajność, w przypadku zapytań usługi Azure Time Series Insights (wersja zapoznawcza).

## <a name="best-practices"></a>Najlepsze praktyki

> [!NOTE]
> Dla usługi Azure TSI (wersja zapoznawcza) nie należy stosować ograniczenia właściwości 600 800 S1/S2.

Należy wziąć pod uwagę sposób wysyłania zdarzeń do usługi Azure TSI. To znaczy należy zawsze:

1. jak najbardziej wydajny wysyłanie danych przez sieć.
1. Upewnij się, że dane są przechowywane w sposób, który służy do wykonywania agregacje odpowiednie dla danego scenariusza.

Poniższe wskazówki zapewnia najlepszą wydajność zapytań możliwe:

1. Nie wysyłaj niepotrzebnych właściwości. Usługa TSI Przesyła (wersja zapoznawcza) zostanie są naliczane na użycie i jest to najlepsze rozwiązanie do przechowywania i przetwarzania danych, który zostanie kwerenda.
1. Użyj pola wystąpienia w przypadku danych statycznych unikać wysyłania danych statycznych za pośrednictwem sieci. Pola wystąpienia, składnik modelu szeregów czasowych działają podobnie jak odwoływać się do danych w usłudze TSI jest ogólnie dostępna. Aby dowiedzieć się, jak pole wystąpienia, przeczytaj [modeli szeregów czasowych](./time-series-insights-update-tsm.md).
1. Udostępnianie właściwości wymiaru wśród wielu zdarzeń, aby bardziej efektywnie wysyłanie danych przez sieć.
1. Nie używaj tablicy głębokiego zagnieżdżenia. Usługa TSI Przesyła obsługuje maksymalnie dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Usługa TSI Przesyła spłaszcza tablic w wiadomości, w wielu zdarzeń przy użyciu pary wartości właściwości.
1. Jeśli tylko kilka istnieją środki dla wszystkich lub większości zdarzeń, zaleca się wysłać te miary jako osobne właściwości w ramach tego samego obiektu. Wysyłając oddzielnie zmniejsza liczbę zdarzeń i mogą wprowadzać zapytania wydajniej zgodnie z mniejszą liczbą zdarzeń wymagających przetworzenia.

## <a name="example"></a>Przykład

Przykład jest oparty na scenariusz, w której wiele urządzeń wysyłać pomiary i sygnały. Pomiary i sygnały może być **przepływ współczynnika**, **wykorzystanie ropa naftowa aparatu**, **temperatury**, i **wilgotności**.

W poniższym przykładzie istnieje pojedynczy komunikat usługi IoT Hub, gdzie zewnętrzne tablica zawiera sekcji udostępnianej typowe wartości wymiarów. Zewnętrzne tablicy używa wystąpienia serii czasu danych w celu zwiększenia efektywności wiadomości. Wystąpienia serii czasu zawiera metadane urządzenia, która nie zmienia się przy użyciu każdego zdarzenia, ale udostępnia właściwości przydatne do analizy danych. Przetwarzanie wsadowe typowe wartości wymiarów i wykorzystujących metadane wystąpienia serii czasu, zapisywane są przesyłane przez sieć, tworząc bardziej wydajne wiadomości w bajtach.

Przykładowy ładunek JSON:

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

Czas wystąpienia serii (Uwaga: **identyfikator serii czasu** jest *deviceId*):

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

Usługa TSI Przesyła połączony tabeli (po spłaszczanie) podczas przeszukiwania. Tabela będzie zawierać dodatkowe kolumny, takie jak typ. W tym przykładzie przedstawiono, jak można przekształcić dane telemetryczne:

| deviceId  | Typ | L1 | PAMIĘCI PODRĘCZNEJ L2 | sygnatura czasowa | Seria. Przepływ współczynnika ft3/s | Seria. Aparat wykorzystanie ropa naftowa psi |
| ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| `FXXX` | Default_Type | SYMULATOR REVOLT | System baterii | 2018-01-17T01:17:00Z |    1.0172575712203979 |    34.7 |
| `FXXX` | LINE_DATA REVOLT | SYMULATOR |    System baterii |    2018-01-17T01:17:00Z | 2.445906400680542 |  49.2 |
| `FYYY` | LINE_DATA WSPÓLNE | SYMULATOR |    System baterii |    2018-01-17T01:18:00Z | 0.58015072345733643 |    22.2 |

Należy pamiętać, że w poprzednim przykładzie:

* Właściwości statyczne są przechowywane jako usługa TSI Przesyła do optymalizacji danych przesyłanych przez sieć.
* Usługa TSI Przesyła dane sprzężony w czasie wykonywania zapytań przy użyciu *timeSeriesId* zdefiniowany w wystąpieniu.
* Używane są dwie warstwy zagnieżdżenia, czyli maksymalną ilość zagnieżdżanie obsługiwane przez usługi TSI. Koniecznie można uniknąć głęboko zagnieżdżonych tablic.
* Miary są wysyłane jako osobne właściwości w ramach tego samego obiektu, ponieważ istnieje kilka miar. W tym miejscu **serii. Przepływ współczynnika psi**, serii **wykorzystanie ropa naftowa aparatu**, i **ft3/s** są unikatowe kolumny.

>[!IMPORTANT]
> Pola wystąpienia nie są przechowywane przy użyciu telemetrii, że są one przechowywane przy użyciu metadanych w **modelu szeregów czasowych**.
> Powyższej tabeli reprezentuje widok zapytania.

## <a name="next-steps"></a>Kolejne kroki

Aby umieścić te wytyczne w praktyce, zobacz [składni zapytań usługi Azure TSI](./time-series-insights-query-data-csharp.md) dowiedzieć się więcej o składni zapytań dla interfejsu API REST dostępu do danych usługi TSI.
