---
title: Filtrowanie zaawansowane — Azure Event Grid IoT Edge | Microsoft Docs
description: Zaawansowane filtrowanie w Event Grid na IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992563"
---
# <a name="advanced-filtering"></a>Filtrowanie zaawansowane
Event Grid umożliwia określanie filtrów dla każdej właściwości w ładunku JSON. Te filtry są modelowane jako zestaw warunków `AND`, z każdym warunkiem zewnętrznym, które mają opcjonalne warunki wewnętrzne `OR`. Dla każdego warunku `AND` należy określić następujące wartości:

* `OperatorType` — typ porównania.
* `Key` — ścieżka JSON do właściwości, dla której ma zostać zastosowany filtr.
* `Value` — wartość odniesienia, względem której jest uruchamiany filtr (lub) `Values` — zestaw wartości referencyjnych, względem których jest uruchamiany filtr.

## <a name="json-syntax"></a>Składnia JSON

Składnia JSON dla filtru zaawansowanego jest następująca:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Filtrowanie wartości tablicy

Event Grid nie obsługuje filtrowania na tablicy wartości dzisiaj. Jeśli zdarzenie przychodzące ma wartość tablicową dla klucza filtru zaawansowanego, Operacja dopasowania nie powiedzie się. Zdarzenie przychodzące nie jest zgodne z subskrypcją zdarzeń.

## <a name="and-or-not-semantics"></a>I-lub-nie-semantyka

Zwróć uwagę, że w przykładowym kodzie JSON podanym wcześniej `AdvancedFilters` jest tablicą. Każdy `AdvancedFilter` element tablicy należy traktować jako warunek `AND`.

Dla operatorów, które obsługują wiele wartości (takich jak `NumberIn`, `NumberNotIn`, `StringIn`itd.), każda wartość jest traktowana jako warunek `OR`. Dlatego `StringBeginsWith("a", "b", "c")` będzie pasować do dowolnej wartości ciągu, która rozpoczyna się od `a` lub `b` lub `c`.

> [!CAUTION]
> Operatory NOT-`NumberNotIn` i `StringNotIn` zachowują się jak i warunki dla każdej wartości podaną w `Values` polu.
>
> Nie spowoduje to, że filtr akceptuje wszystkie filtry i obniża przeznaczenie filtrowania.

## <a name="floating-point-rounding-behavior"></a>Zachowanie zaokrąglania liczb zmiennoprzecinkowych

Event Grid używa typu .NET `decimal` do obsługi wszystkich wartości liczbowych. Wartości liczbowe określone w pliku JSON subskrypcji zdarzeń nie podlegają działaniu zaokrąglenia liczb zmiennoprzecinkowych.

## <a name="case-sensitivity-of-string-filters"></a>Rozróżnianie wielkości liter w filtrach ciągów

Wszystkie porównania ciągów nie uwzględniają wielkości liter. Nie ma możliwości zmiany tego zachowania już dziś.

## <a name="allowed-advanced-filter-keys"></a>Dozwolone zaawansowane klucze filtru

Właściwość `Key` może być dobrze znaną właściwości najwyższego poziomu lub być ścieżką JSON z wieloma kropkami, gdzie każda kropka oznacza przechodzenie do zagnieżdżonego obiektu JSON.

Event Grid nie ma żadnego specjalnego znaczenia dla znaku `$` w kluczu, w przeciwieństwie do specyfikacji wykryto.

### <a name="event-grid-schema"></a>Schemat siatki zdarzeń

Dla zdarzeń w schemacie Event Grid:

* ID
* Temat
* Temat
* Klasę
* wersja
* Data. Prop1
* Data. prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Niestandardowy schemat zdarzeń

Nie ma ograniczeń dotyczących `Key` w schemacie zdarzeń niestandardowych, ponieważ Event Grid nie wymusza żadnego schematu Envelope w ładunku.

## <a name="numeric-single-value-filter-examples"></a>Przykłady filtrów liczbowych o pojedynczej wartości

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Przykłady filtru wartości numerycznych

* Numer w
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Przykłady filtru wartości dla zakresu ciągów

* StringContains
* StringBeginsWith
* StringEndsWith
* Ciąg w
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Przykłady filtru pojedynczej wartości logicznej

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
