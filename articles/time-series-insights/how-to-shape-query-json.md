---
title: Najlepsze rozwiązania w zakresie kształtowania JSON w zapytaniach Insights serii czasu Azure.
description: Dowiedz się, jak zwiększyć wydajność Insights serii czasu zapytania.
services: time-series-insights
author: ashannon7
manager: timlt
ms.service: time-series-insights
ms.topic: article
ms.date: 05/24/2018
ms.author: bryanla
ms.openlocfilehash: 29919a3ce8c18982c88f7f0e6bbd774c612e9c44
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660929"
---
# <a name="how-to-shape-json-to-maximize-query-performance"></a>Jak kształtu JSON, aby zmaksymalizować wydajność zapytań 

Ten artykuł zawiera wskazówki dotyczące kształtowania JSON, aby zmaksymalizować wydajność kwerend Azure czas serii Insights (TSI).

## <a name="best-practices"></a>Najlepsze praktyki

Należy się zastanowić, jak wysyłać zdarzenia do szczegółowych danych serii czasu. To znaczy należy zawsze:

1. jak najbardziej wydajny wysyłanie danych przez sieć.
2. Upewnij się, że dane są przechowywane w taki sposób, który umożliwia przeprowadzanie agregacji odpowiednie dla danego scenariusza.
3. Upewnij się, nie osiągnęła TSI w granicach maksymalna właściwość
   - 600 właściwości (kolumny) dla środowisk S1.
   - właściwości 800 (kolumny) dla środowisk S2.

Poniższe wskazówki zapewnia najlepszą wydajność zapytań możliwe:

1. Nie używaj właściwości dynamicznych, takie jak identyfikator tagu jako nazwy właściwości, jak przyczynia się do naciśnięcie właściwości maksymalny limit.
2. Nie wysyłaj niepotrzebnych właściwości. Jeśli właściwość kwerendy nie jest wymagane, najlepiej nie przesyłać je i uniknąć ograniczenia magazynu.
3. Użyj [danych referencyjnych](time-series-insights-add-reference-data-set.md), aby unikać wysyłania danych statycznych za pośrednictwem sieci.
4. Udostępnianie właściwości wymiaru między wiele zdarzeń, aby efektywniej wysyłanie danych przez sieć.
5. Nie używaj tablicy głębokiego zagnieżdżenia. TSI obsługuje maksymalnie dwa poziomy tablice zagnieżdżone, które zawierają obiekty. TSI spłaszcza tablic w wiadomości, w wielu zdarzeń z par wartości właściwości.
6. Jeśli tylko dla wszystkich lub większości zdarzeń istnieje kilka miar, zaleca się wysyłanie tych środków jako osobne właściwości w ramach tego samego obiektu. Wysyłając je oddzielnie zmniejsza liczbę zdarzeń i mogą spowodować, że zapytania więcej wydajności, zgodnie z mniejszą liczbą zdarzeń muszą być przetworzone. W przypadku kilku środków wysłaniem ich jako wartości w jednej właściwości minimalizuje możliwość naciśnięcie właściwości maksymalny limit.

## <a name="examples"></a>Przykłady

Dwa poniższe przykłady pokazują wysyłania zdarzeń, aby wyróżnić powyższe zalecenia. Po każdym przykładzie widać, jak zalecenia zostały zastosowane.

Przykłady są oparte na scenariusz, gdzie wiele urządzeń wysłać pomiarów lub sygnałów. Pomiary lub sygnały można szybkość przepływu, aparat wydobycie ropy naftowej wykorzystania, temperatury i wilgotności. W pierwszym przykładzie istnieje kilka miar dla wszystkich urządzeń. W drugim przykładzie istnieje wiele urządzeń, a każdy wysyła wiele unikatowych wartości.

### <a name="scenario-only-a-few-measurementssignals-exist"></a>Scenariusza: istnieje tylko kilka miar/sygnałów

**Zalecenie:** wysłać każdy pomiar jako osobne właściwości/kolumny.

W poniższym przykładzie jest pojedynczym komunikacie Centrum IoT, gdzie zewnętrzne tablica zawiera współdzielonej sekcji wspólne wartości wymiaru. Zewnętrzne tablicy używa danych referencyjnych w celu zwiększenia efektywności wiadomości. Dane referencyjne zawiera urządzenia metadane, nie zmienia się każde zdarzenie, które dostarcza właściwości, przydatne do analizy danych. Zarówno przetwarzanie wsadowe wspólne wartości wymiaru, jak i w związku z tym wykorzystanie danych referencyjnych, zapisuje na bajty przesyłane przez sieć, udostępniając komunikat bardziej wydajne.

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
                "Flow Rate psi": 0.58015072345733643,
                "Engine Oil Pressure psi ": 22.2
            }
        ]
    }
]
```

Tabela danych odwołania (właściwość klucza jest deviceId):

| deviceId | messageId | deviceLocation |
| --- | --- | --- |
| FXXX | WIERSZ\_DANYCH | Unia Europejska |
| FYYY | WIERSZ\_DANYCH | USA |

Tabela zdarzeń Insights serii czasu (po spłaszczanie):

| deviceId | messageId | deviceLocation | sygnatura czasowa | Seria. Szybkość przepływu ft3/s | Seria. Aparat wykorzystania wydobycie ropy naftowej psi |
| --- | --- | --- | --- | --- | --- |
| FXXX | WIERSZ\_DANYCH | Unia Europejska | 2018-01-17T01:17:00Z | 1.0172575712203979 | 34.7 |
| FXXX | WIERSZ\_DANYCH | Unia Europejska | 2018-01-17T01:17:00Z | 2.445906400680542 | 49.2 |
| FYYY | WIERSZ\_DANYCH | USA | 2018-01-17T01:18:00Z | 0.58015072345733643 | 22.2 |

Należy pamiętać, że w poprzednim przykładzie:

- **DeviceId** kolumny służy jako nagłówek kolumny dla różnych urządzeń w floty. Próby nawiązania wartość deviceId własną nazwę właściwości będzie ograniczona łączna liczba urządzeń do 595 (środowiskach S1) lub 795 (środowiskach S2), z innymi kolumnami pięć.

- Unika się niepotrzebne właściwości, na przykład informacje o modelu i upewnij, itp. Ponieważ one nie będzie można wykonać zapytania w przyszłości, eliminując ich umożliwia lepsze sieci i wydajność magazynu.

- Dane referencyjne służy do zmniejszyć liczbę bajtów przesyłanych w sieci. Dwa atrybuty **messageId** i **deviceLocation** , są łączone za pomocą właściwości klucza **deviceId**. Te dane są łączone z danych telemetrycznych w czasie wejściowych i następnie przechowywane w TSI do wykonywania zapytań.

- Używane są dwie warstwy zagnieżdżenia, czyli maksymalna ilość obsługiwana przez TSI zagnieżdżenia. Bardzo ważne, aby uniknąć głęboko zagnieżdżone tablice jest.

- Środki są wysyłane jako osobne właściwości w ramach tego samego obiektu, ponieważ ma kilka miar. W tym miejscu **serii. Przepływ psi szybkość** i **serii. Aparat wykorzystania wydobycie ropy naftowej ft3/s** są unikatowe kolumny.

### <a name="scenario-several-measures-exist"></a>Scenariusza: istnieje kilka miar

**Zalecenie:** wysłać pomiarów jako "type", "jednostki", "value" spójnych kolekcji.

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

Dane referencyjne (właściwości klucza są deviceId i series.tagId):

| deviceId | series.tagId | messageId | deviceLocation | type | jednostka |
| --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | WIERSZ\_DANYCH | Unia Europejska | Przepływ | ft3/s |
| FXXX | oilPressure | WIERSZ\_DANYCH | Unia Europejska | Aparat wydobycie ropy naftowej wykorzystania | psi |
| FYYY | pumpRate | WIERSZ\_DANYCH | USA | Przepływ | ft3/s |
| FYYY | oilPressure | WIERSZ\_DANYCH | USA | Aparat wydobycie ropy naftowej wykorzystania | psi |

Tabela zdarzeń Insights serii czasu (po spłaszczanie):

| deviceId | series.tagId | messageId | deviceLocation | type | jednostka | sygnatura czasowa | Series.Value |
| --- | --- | --- | --- | --- | --- | --- | --- |
| FXXX | pumpRate | WIERSZ\_DANYCH | Unia Europejska | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 1.0172575712203979 |
| FXXX | oilPressure | WIERSZ\_DANYCH | Unia Europejska | Aparat wydobycie ropy naftowej wykorzystania | psi | 2018-01-17T01:17:00Z | 34.7 |
| FXXX | pumpRate | WIERSZ\_DANYCH | Unia Europejska | Przepływ | ft3/s | 2018-01-17T01:17:00Z | 2.445906400680542 |
| FXXX | oilPressure | WIERSZ\_DANYCH | Unia Europejska | Aparat wydobycie ropy naftowej wykorzystania | PSI | 2018-01-17T01:17:00Z | 49.2 |
| FYYY | pumpRate | WIERSZ\_DANYCH | USA | Przepływ | ft3/s | 2018-01-17T01:18:00Z | 0.58015072345733643 |
| FYYY | oilPressure | WIERSZ\_DANYCH | USA | Aparat wydobycie ropy naftowej wykorzystania | psi | 2018-01-17T01:18:00Z | 22.2 |

Zanotuj następujące w poprzednim przykładzie i podobne do pierwszego przykładu:

- kolumny **deviceId** i **series.tagId** służyć jako nagłówków kolumn dla różnych urządzeń i tagi w floty. Przy użyciu własnego atrybut będzie ograniczona zapytanie do 594 (środowiskach S1) lub 794 (środowiskach S2) łączna liczba urządzeń z innymi kolumnami sześć.

- niepotrzebnych właściwości zostały unikać z powodu odnosiło się w pierwszym przykładzie.

- dane referencyjne służy do zmniejszyć liczbę bajtów przesyłanych w sieci przez wprowadzenie **deviceId**, unikatowe pary **messageId** i **deviceLocation**. Jest używany klucz złożony, **series.tagId**, unikatowe pary **typu** i **jednostce**. Umożliwia klucza złożonego **deviceId** i **series.tagId** pary mają być używane, aby odwołać się do cztery wartości: **messageId, deviceLocation, typ,** i **jednostki** . Te dane są łączone z danych telemetrycznych w czasie wejściowych i następnie przechowywane w TSI do wykonywania zapytań.

- używane są dwie warstwy zagnieżdżenia, z powodu odnosiło się w pierwszym przykładzie.

### <a name="for-both-scenarios"></a>W obu przypadkach

Jeśli właściwość z dużą liczbą możliwych wartości, najlepiej jest wysyłać je jako różne wartości w jednej kolumnie, zamiast tworzenia nowej kolumny, dla każdej wartości. W poprzednich przykładach dwóch:
  - W pierwszym przykładzie istnieje kilka właściwości, które mają kilka wartości, więc jest odpowiednia do tworzenia, każdego oddzielne właściwości. 
  - Jednak w drugim przykładzie widać miary nie są określone jako poszczególnych właściwości, ale raczej tablicy wartości/środków w ramach typowych właściwości serii. Nowy klucz jest wysyłane, **tagId** , która tworzy nową kolumnę, **series.tagId** w spłaszczonym tabeli. Nowe właściwości są tworzone, **typu** i **jednostki**, przy użyciu danych referencyjnych, co uniemożliwia limit właściwości trafienia.

## <a name="next-steps"></a>Kolejne kroki

Zastosowanie niniejsze wytyczne, zobacz [Składnia kwerendy Azure czas serii Insights](/rest/api/time-series-insights/time-series-insights-reference-query-syntax) dowiedzieć się więcej o składni zapytania dla danych TSI dostęp do interfejsu API REST.