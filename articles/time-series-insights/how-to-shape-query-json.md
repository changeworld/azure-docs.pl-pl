---
title: Najlepsze rozwiązania dotyczące kształtowania JSON w zapytaniach usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, jak poprawić wydajność zapytań usługi Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: dpalled
ms.custom: seodec18
ms.openlocfilehash: 089285637bb740fea47f1fd07de0906dfe46662b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244459"
---
# <a name="shape-json-to-maximize-query-performance"></a>Kształt JSON, aby zmaksymalizować wydajność zapytań 

Ten artykuł zawiera wskazówki dotyczące sposobu kształt JSON, aby zmaksymalizować wydajność zapytań usługi Azure Time Series Insights.

## <a name="video"></a>Połączenia wideo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Poznaj najlepsze rozwiązania dotyczące kształtowania JSON w celu spełnienia potrzeb dotyczących magazynu.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Najlepsze praktyki
Zastanów się, jak wysyłanie zdarzeń do usługi Time Series Insights. To znaczy zawsze możesz:

1. jak najbardziej wydajny wysyłanie danych przez sieć.
1. Upewnij się, że dane są przechowywane w taki sposób, dzięki czemu można wykonywać agregacji odpowiednie dla danego scenariusza.
1. Upewnij się, że nie będziesz osiągać limity maksymalna właściwości usługi Time Series Insights:
   - 600 właściwości (kolumny) dla środowisk S1.
   - 800-właściwości (kolumny) dla środowisk S2.

Poniższe wskazówki pomaga zapewnić najlepszą wydajność możliwego zapytania:

1. Nie używaj właściwości dynamicznych, takie jak identyfikator tagu, jako nazwa właściwości. To wykorzystania przyczynia się do osiągnięcia limitu maksymalnej właściwości.
1. Nie wysyłaj niepotrzebnych właściwości. Jeśli właściwość kwerendy nie jest wymagane, najlepiej jest nie do wysłania. Dzięki temu można uniknąć ograniczenia magazynu.
1. Użyj [dane referencyjne](time-series-insights-add-reference-data-set.md) unikać wysyłania danych statycznych za pośrednictwem sieci.
1. Udostępnianie właściwości wymiaru między wiele zdarzeń, aby bardziej efektywnie wysyłanie danych przez sieć.
1. Nie używaj tablicy głębokiego zagnieżdżenia. Usługa Time Series Insights obsługuje maksymalnie dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Usługa Time Series Insights spłaszcza tablic w komunikatach do wielu zdarzeń przy użyciu pary wartości właściwości.
1. Jeśli tylko kilka istnieją środki dla wszystkich lub większości zdarzeń, zaleca się wysłać te miary jako osobne właściwości w ramach tego samego obiektu. Wysyłając oddzielnie zmniejsza liczbę zdarzeń i może poprawić wydajność zapytań, ponieważ mniej zdarzeń wymagających przetworzenia. W przypadku kilku środków, wysyłając je jako wartości w pojedynczej właściwości minimalizuje prawdopodobieństwo osiągnięcia limitu maksymalnej właściwości.

## <a name="example-overview"></a>Omówienie przykładowego

Dwa poniższe przykłady pokazują, jak wysyłać zdarzenia, aby wyróżnić powyższe zalecenia. Po każdym przykładzie można zobaczyć, jak zalecenia zostały zastosowane.

Przykłady są oparte na scenariuszu, w której wiele urządzeń wysyłać pomiary i sygnały. Pomiary i sygnały, może być przepływ współczynnika, wykorzystanie ropa naftowa aparatu, temperatury i wilgotności. W pierwszym przykładzie istnieje kilka miar dla wszystkich urządzeń. Drugi przykład jest wiele urządzeń, a każde urządzenie wysyła wiele unikatowych pomiarów.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenariusz 1: Istnieje tylko kilka miar

> [!TIP]
> Zaleca się wysłać każdej miary lub sygnału jako osobne właściwości lub kolumn.

W poniższym przykładzie istnieje jeden komunikat o usłudze Azure IoT Hub gdzie zewnętrzne tablica zawiera sekcji udostępnianej typowe wartości wymiarów. Zewnętrzne tablicy używa danymi referencyjnymi w celu zwiększenia efektywności wiadomości. Dane referencyjne zawiera metadane urządzenia, które nie zmieniają się przy użyciu każdego zdarzenia, ale udostępnia właściwości przydatne do analizy danych. Przetwarzanie wsadowe typowe wartości wymiarów i wykorzystujących odwołanie, które dane zapisywane są przesyłane przez sieć w bajtach, co sprawia, że komunikat bardziej wydajne.

Przykładowy ładunek JSON:

```json
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

* Odwołanie do tabeli danych, która ma właściwość klucza **deviceId**:

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | WIERSZ\_DANYCH | EU |
   | FYYY | WIERSZ\_DANYCH | USA |

* Czas Series Insights — tabela zdarzeń, po spłaszczeniu:

   | deviceId | messageId | deviceLocation | timestamp | Seria. Przepływ współczynnika ft3/s | Seria. Aparat wykorzystanie ropa naftowa psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | WIERSZ\_DANYCH | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | WIERSZ\_DANYCH | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | WIERSZ\_DANYCH | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Uwagi dotyczące tych dwóch tabel:

- **DeviceId** kolumny służy jako nagłówek kolumny w przypadku różnych urządzeń w floty. Wartość deviceId nazwy właściwości ogranicza łącznej liczby urządzeń 595 (dla środowisk S1) lub 795 (dla środowisk S2) z pięciu kolumnach.
- Aby uzyskać przykład, producenta i modelu informacji unika się niepotrzebne właściwości. Ponieważ właściwości nie można zbadać w przyszłości, eliminując ich umożliwia lepsze sieci i wydajność magazynu.
- Dane referencyjne służy do zmniejszenia liczby bajtów przesyłanych w sieci. Dwa atrybuty **messageId** i **deviceLocation** są sprzęgane przy użyciu właściwości klucza **deviceId**. Tych danych jest sprzężony z danych telemetrycznych w czasie transferu danych przychodzących i następnie przechowywane w usłudze Time Series Insights do wykonywania zapytań.
- Używane są dwie warstwy zagnieżdżenia, czyli maksymalną ilość zagnieżdżanie obsługiwane przez usługi Time Series Insights. Koniecznie można uniknąć głęboko zagnieżdżonych tablic.
- Miary są wysyłane jako osobne właściwości w ramach tego samego obiektu, ponieważ istnieje kilka miar. W tym miejscu **serii. Przepływ współczynnika psi** i **serii. Aparat wykorzystanie ropa naftowa ft3/s** są unikatowe kolumny.

## <a name="scenario-two-several-measures-exist"></a>Scenariusz 2: Istnieje kilka miar

> [!TIP]
> Zaleca się wysłanie pomiary "type", "jednostka" i "wartość" krotek.

Przykładowy ładunek JSON:

```json
[
    {
        "deviceId": "FXXX",
        "timestamp": "2018-01-17T01:17:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 1.0172575712203979
            },
            {
                "tagId": "oilPressure",
                "value": 49.2
            },
            {
                "tagId": "pumpRate",
                "value": 2.445906400680542
            },
            {
                "tagId": "oilPressure",
                "value": 34.7
            }
        ]
    },
    {
        "deviceId": "FYYY",
        "timestamp": "2018-01-17T01:18:00Z",
        "series": [
            {
                "tagId": "pumpRate",
                "value": 0.58015072345733643
            },
            {
                "tagId": "oilPressure",
                "value": 22.2
            }
        ]
    }
]
```

* Tabela danych odwołania, który zawiera właściwości klucza **deviceId** i **series.tagId**:

   | deviceId | series.tagId | messageId | deviceLocation | — typ | Jednostki |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | WIERSZ\_DANYCH | EU | Przepływ | ft3/s |
   | FXXX | oilPressure | WIERSZ\_DANYCH | EU | Wykorzystanie ropa naftowa aparatu | psi |
   | FYYY | pumpRate | WIERSZ\_DANYCH | USA | Przepływ | ft3/s |
   | FYYY | oilPressure | WIERSZ\_DANYCH | USA | Wykorzystanie ropa naftowa aparatu | psi |

* Czas Series Insights — tabela zdarzeń, po spłaszczeniu:

   | deviceId | series.tagId | messageId | deviceLocation | — typ | Jednostki | timestamp | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | WIERSZ\_DANYCH | EU | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | WIERSZ\_DANYCH | EU | Wykorzystanie ropa naftowa aparatu | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | WIERSZ\_DANYCH | EU | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | WIERSZ\_DANYCH | EU | Wykorzystanie ropa naftowa aparatu | psi | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | WIERSZ\_DANYCH | USA | Przepływ | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | WIERSZ\_DANYCH | USA | Wykorzystanie ropa naftowa aparatu | psi | 2018-01-17T01:18:00Z | 22.2 |

Uwagi dotyczące tych dwóch tabel:

- Kolumny **deviceId** i **series.tagId** służyć jako nagłówki kolumn dla różnych urządzeń i tagów w floty. Przy użyciu każdego swój własny atrybut ogranicza zapytanie do 594 (dla środowisk S1) lub 794 (dla środowisk S2) łączna liczba urządzeń, za pomocą sześciu innych kolumn.
- niepotrzebne właściwości zostały unikać z powodów wymienionych w pierwszym przykładzie.
- Dane referencyjne służy do zmniejszenia liczby bajtów przesyłanych w sieci, wprowadzając **deviceId**, która jest użyta unikatową parę **messageId** i **deviceLocation**. Klucz złożony **series.tagId** służy do unikatową parę **typu** i **jednostki**. Klucz złożony umożliwia **deviceId** i **series.tagId** pary ma być używany do odwoływania się do czterech wartości: **messageId, deviceLocation, typ,** i **jednostki**. Tych danych jest sprzężony z danych telemetrycznych w momencie transferu danych przychodzących. Następnie są przechowywane w usługi Time Series Insights w przypadku zapytań.
- używane są dwie warstwy zagnieżdżenia, z powodu cytowane w pierwszym przykładzie.

### <a name="for-both-scenarios"></a>W obu scenariuszach

Dla właściwości o dużej liczby możliwych wartości najlepiej jest do wysłania jako odrębne wartości w jednej kolumnie zamiast tworzenia nowej kolumny do każdej wartości. W poprzednich dwóch przykładach:

  - W pierwszym przykładzie kilka właściwości mają kilka wartości, dlatego należy upewnić się, każdy oddzielny właściwości.
  - W drugim przykładzie środki nie są określone jako poszczególnych właściwości. Zamiast tego są one tablicę wartości lub środków w ramach wspólnej właściwości serii. Nowy klucz **Identyfikator_tagu** zostanie wysłany, która tworzy nową kolumnę **series.tagId** spłaszczonej tabeli. Nowe właściwości **typu** i **jednostki** są tworzone przy użyciu danych referencyjnych, nie zostanie osiągnięty limit właściwości.

## <a name="next-steps"></a>Kolejne kroki

- Odczyt [składni zapytania usługi Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) dowiedzieć się więcej o składni zapytań dla interfejsu API REST dostępu do danych usługi Time Series Insights.
- Dowiedz się, [jak zdarzenia kształtu](./time-series-insights-send-events.md).
