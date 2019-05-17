---
title: Najlepsze rozwiązania dotyczące kształtowania JSON w zapytaniach usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: Dowiedz się, jak poprawić wydajność zapytań usługi Azure Time Series Insights.
services: time-series-insights
author: ashannon7
manager: cshankar
ms.service: time-series-insights
ms.topic: article
ms.date: 05/09/2019
ms.author: anshan
ms.custom: seodec18
ms.openlocfilehash: 535fe9a7920db89e434b4cc1b66aa38bf72a7829
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790077"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Sposób kształtowania JSON, aby zmaksymalizować wydajność zapytań 

Ten artykuł zawiera wskazówki dotyczące kształtowania JSON, aby zmaksymalizować wydajność zapytań usługi Azure Time Series Insights.

## <a name="video"></a>Wideo

### <a name="learn-best-practices-for-shaping-json-to-meet-your-storage-needsbr"></a>Poznaj najlepsze rozwiązania dotyczące kształtowania JSON w celu spełnienia potrzeb dotyczących magazynu.</br>

> [!VIDEO https://www.youtube.com/embed/b2BD5hwbg5I]

## <a name="best-practices"></a>Najlepsze rozwiązania

Należy wziąć pod uwagę sposób wysyłania zdarzeń do usługi Time Series Insights. To znaczy należy zawsze:

1. jak najbardziej wydajny wysyłanie danych przez sieć.
1. Upewnij się, że dane są przechowywane w sposób, który służy do wykonywania agregacje odpowiednie dla danego scenariusza.
1. Upewnij się, nie trafień limitów maksymalnej właściwości Time Series Insights:
   - 600 właściwości (kolumny) dla środowisk S1.
   - 800-właściwości (kolumny) dla środowisk S2.

Poniższe wskazówki zapewnia najlepszą wydajność zapytań możliwe:

1. Nie używaj właściwości dynamicznych, takie jak identyfikator tagu w postaci nazwy właściwości, ponieważ przyczynia się do osiągnięcia limitu maksymalnej właściwości.
1. Nie wysyłaj niepotrzebnych właściwości. Jeśli właściwość kwerendy nie jest wymagane, najlepiej jest nie można wysłać go i uniknąć ograniczenia magazynu.
1. Użyj [dane referencyjne](time-series-insights-add-reference-data-set.md), aby unikać wysyłania danych statycznych za pośrednictwem sieci.
1. Udostępnianie właściwości wymiaru wśród wielu zdarzeń, aby bardziej efektywnie wysyłanie danych przez sieć.
1. Nie używaj tablicy głębokiego zagnieżdżenia. Usługa Time Series Insights obsługuje maksymalnie dwóch poziomów zagnieżdżonych tablic, które zawierają obiekty. Usługa Time Series Insights spłaszcza tablic w wiadomości, w wielu zdarzeń przy użyciu pary wartości właściwości.
1. Jeśli tylko kilka istnieją środki dla wszystkich lub większości zdarzeń, zaleca się wysłać te miary jako osobne właściwości w ramach tego samego obiektu. Wysyłając oddzielnie zmniejsza liczbę zdarzeń i mogą wprowadzać zapytania wydajniej zgodnie z mniejszą liczbą zdarzeń wymagających przetworzenia. W przypadku kilku środków, wysyłając jako wartości w pojedynczej właściwości minimalizuje prawdopodobieństwo osiągnięcia limitu maksymalnej właściwości.

## <a name="example-overview"></a>Omówienie przykładowego

Dwa poniższe przykłady pokazują, wysyłanie zdarzeń, aby wyróżnić powyższe zalecenia. Po każdym przykładzie można zobaczyć, jak zalecenia zostały zastosowane.

Przykłady są oparte na scenariuszu, w której wiele urządzeń wysyłać pomiary i sygnały. Pomiary i sygnały, może być przepływ współczynnika, wykorzystanie ropa naftowa aparatu, temperatury i wilgotności. W pierwszym przykładzie istnieje kilka miar dla wszystkich urządzeń. W drugim przykładzie istnieje wiele urządzeń, a każda wysyła wiele unikatowych pomiarów.

## <a name="scenario-one-only-a-few-measurements-exist"></a>Scenariusz 1: istnieje tylko kilka miar

> [!TIP]
> **Zalecenie**: wysyłanie każdy pomiar/sygnał jako osobne właściwości/kolumny.

W poniższym przykładzie istnieje pojedynczy komunikat usługi IoT Hub, gdzie zewnętrzne tablica zawiera sekcji udostępnianej typowe wartości wymiarów. Zewnętrzne tablicy używa danymi referencyjnymi w celu zwiększenia efektywności wiadomości. Dane referencyjne zawiera metadane urządzenia, która nie zmienia się przy użyciu każdego zdarzenia, ale udostępnia właściwości przydatne do analizy danych. Przetwarzanie wsadowe typowe wartości wymiarów i wykorzystujących dane referencyjne, zapisuje na bajty przesyłane przez sieć, dzięki czemu wiadomości bardziej wydajne.

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

* Tabela danych odwołania (właściwość klucza jest **deviceId**):

   | deviceId | messageId | deviceLocation |
   | --- | --- | --- |
   | FXXX | WIERSZ\_DANYCH | EU |
   | FYYY | WIERSZ\_DANYCH | USA |

* Tabela zdarzeń Series Insights czasu (po spłaszczanie):

   | deviceId | messageId | deviceLocation | timestamp | Seria. Przepływ współczynnika ft3/s | Seria. Aparat wykorzystanie ropa naftowa psi |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | WIERSZ\_DANYCH | EU | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
   | FXXX | WIERSZ\_DANYCH | EU | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
   | FYYY | WIERSZ\_DANYCH | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Powyżej:

- **DeviceId** kolumny służy jako nagłówek kolumny w przypadku różnych urządzeń w floty. Próby nawiązania deviceId wartość własną nazwę właściwości, czy mają ograniczoną łącznej liczby urządzeń 595 (S1 środowiska) lub 795 (środowiskach S2), z pięciu kolumnach.

- Unika się niepotrzebnych właściwości, na przykład informacji marka i model, itp. Ponieważ one nie będzie można wykonywać zapytania w przyszłości, eliminując ich umożliwia lepsze sieci i wydajność magazynu.

- Dane referencyjne służy do zmniejszenia liczby bajtów przesyłanych w sieci. Dwa atrybuty **messageId** i **deviceLocation**, są łączone za pomocą właściwości klucza **deviceId**. Te dane są łączone z danych telemetrycznych w czasie transferu danych przychodzących i następnie przechowywane w usłudze Time Series Insights do wykonywania zapytań.

- Używane są dwie warstwy zagnieżdżenia, czyli maksymalną ilość zagnieżdżanie obsługiwane przez usługi Time Series Insights. Koniecznie można uniknąć głęboko zagnieżdżonych tablic.

- Miary są wysyłane jako osobne właściwości w ramach tego samego obiektu, ponieważ istnieje kilka miar. W tym miejscu **serii. Przepływ współczynnika psi** i **serii. Aparat wykorzystanie ropa naftowa ft3/s** są unikatowe kolumny.

## <a name="scenario-two-several-measures-exist"></a>Scenariusz 2: istnieje kilka miar

> [!TIP]
> **Zalecenie:** wysyłanie pomiarów jako "type", "jednostka", "value" krotek.

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

* Dane referencyjne (właściwości klucza są **deviceId** i **series.tagId**):

   | deviceId | series.tagId | messageId | deviceLocation | type | jednostka |
   | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | WIERSZ\_DANYCH | EU | Przepływ | ft3/s |
   | FXXX | oilPressure | WIERSZ\_DANYCH | EU | Wykorzystanie ropa naftowa aparatu | psi |
   | FYYY | pumpRate | WIERSZ\_DANYCH | USA | Przepływ | ft3/s |
   | FYYY | oilPressure | WIERSZ\_DANYCH | USA | Wykorzystanie ropa naftowa aparatu | psi |

* Tabela zdarzeń Series Insights czasu (po spłaszczanie):

   | deviceId | series.tagId | messageId | deviceLocation | type | jednostka | timestamp | Series.Value |
   | --- | --- | --- | --- | --- | --- | --- | --- |
   | FXXX | pumpRate | WIERSZ\_DANYCH | EU | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 | 
   | FXXX | oilPressure | WIERSZ\_DANYCH | EU | Wykorzystanie ropa naftowa aparatu | psi | 2018-01-17T01:17:00Z | 34.7 |
   | FXXX | pumpRate | WIERSZ\_DANYCH | EU | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 | 
   | FXXX | oilPressure | WIERSZ\_DANYCH | EU | Wykorzystanie ropa naftowa aparatu | PSI | 2018-01-17T01:17:00Z | 49.2 |
   | FYYY | pumpRate | WIERSZ\_DANYCH | USA | Przepływ | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
   | FYYY | oilPressure | WIERSZ\_DANYCH | USA | Wykorzystanie ropa naftowa aparatu | psi | 2018-01-17T01:18:00Z | 22.2 |

Powyżej:

- Kolumny **deviceId** i **series.tagId** służyć jako nagłówki kolumn dla różnych urządzeń i tagów w floty. Każdej jako atrybut do jego własnej będzie mieć ograniczone zapytanie, aby 594 (S1 środowiska) lub 794 (środowiskach S2) łączna liczba urządzeń z innymi kolumnami sześć.

- niepotrzebne właściwości zostały unikać z powodów wymienionych w pierwszym przykładzie.

- dane referencyjne służy do zmniejszenia liczby bajtów przesyłanych w sieci, wprowadzając **deviceId**, unikatowe pary **messageId** i **deviceLocation**. Jest używany klucz złożony, **series.tagId**, unikatowe pary **typu** i **jednostce**. Klucz złożony umożliwia **deviceId** i **series.tagId** pary służący do odwoływania się do czterech wartości: **messageId, deviceLocation, typ,** i **jednostki** . Te dane są łączone z danych telemetrycznych w czasie transferu danych przychodzących i następnie przechowywane w usłudze Time Series Insights do wykonywania zapytań.

- używane są dwie warstwy zagnieżdżenia, z powodu cytowane w pierwszym przykładzie.

### <a name="for-both-scenarios"></a>W obu scenariuszach

Jeśli właściwość z dużą liczbą możliwych wartości, najlepiej Wyślij jako odrębne wartości w jednej kolumnie, zamiast tworzenia nowej kolumny dla każdej wartości. W poprzednich dwóch przykładach:

  - W pierwszym przykładzie istnieje kilka właściwości, które mają kilka wartości, więc upewnij się, każdy osobne właściwości.
  - Jednak w drugim przykładzie widać środki nie są określone jako poszczególne właściwości, ale raczej tablicę wartości/środków w ramach wspólnej właściwości serii. Nowy klucz zostanie wysłany, **Identyfikator_tagu** , która tworzy nową kolumnę **series.tagId** spłaszczonej tabeli. Nowe właściwości są tworzone, **typu** i **jednostki**, przy użyciu danych referencyjnych, co uniemożliwia limit właściwość trafienia.

## <a name="next-steps"></a>Kolejne kroki

- Odczyt [składni zapytania usługi Azure Time Series Insights](/rest/api/time-series-insights/ga-query-syntax) dowiedzieć się więcej o składni zapytań dla interfejsu API REST dostępu do danych usługi Time Series Insights.

- Dowiedz się, [jak zdarzenia kształtu](./time-series-insights-send-events.md).
