---
title: Zaawansowane filtrowanie — usługa Azure Event Grid IoT Edge | Dokumenty firmy Microsoft
description: Zaawansowane filtrowanie w siatce zdarzeń w umyw ioT edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992563"
---
# <a name="advanced-filtering"></a>Filtrowanie zaawansowane
Usługa Event Grid umożliwia określenie filtrów na dowolnej właściwości w ładunku json. Filtry te są modelowane `AND` jako zestaw warunków, przy `OR` czym każdy warunek zewnętrzny ma opcjonalne warunki wewnętrzne. Dla `AND` każdego warunku należy określić następujące wartości:

* `OperatorType`- Rodzaj porównania.
* `Key`- Ścieżka json do właściwości, na której należy zastosować filtr.
* `Value`- Wartość referencyjna, względem której filtr `Values` jest uruchamiany (lub) — zestaw wartości referencyjnych, względem których filtr jest uruchamiany.

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

Usługa Event Grid nie obsługuje obecnie filtrowania tablicy wartości. Jeśli zdarzenie przychodzące ma wartość tablicy dla klucza filtru zaawansowanego, pasująca operacja kończy się niepowodzeniem. Zdarzenie przychodzące kończy się nie pasujące do subskrypcji zdarzenia.

## <a name="and-or-not-semantics"></a>Semantyka I-OR-NOT

Należy zauważyć, że w przykładzie json podane wcześniej, `AdvancedFilters` jest tablicą. Każdy element `AdvancedFilter` tablicy `AND` jako warunek.

Dla operatorów obsługujących wiele `NumberIn` `NumberNotIn`wartości `StringIn`(takich jak , , itp.), każda wartość jest traktowana `OR` jako warunek. Tak, `StringBeginsWith("a", "b", "c")` a będzie pasować do dowolnej `a` wartości `b` `c`ciągu, który zaczyna się albo lub lub .

> [!CAUTION]
> Operatory NOT `NumberNotIn` `StringNotIn` - i zachowują się jako `Values` warunki i dla każdej wartości podanej w polu.
>
> Nie spowoduje to, że filtr accept-all filtr i pokonać cel filtrowania.

## <a name="floating-point-rounding-behavior"></a>Zachowanie zaokrąglania zmiennoprzecinkowego

Usługa Event Grid `decimal` używa typu .NET do obsługi wszystkich wartości liczbowych. Wartości liczbowe określone w subskrypcji zdarzenia JSON nie podlegają zachowanie zaokrąglania zmiennoprzecinkowego.

## <a name="case-sensitivity-of-string-filters"></a>Wielkość liter filtrów ciągów

Wszystkie porównania ciągów są niewrażliwe na wielkości liter. Nie ma sposobu, aby zmienić to zachowanie dzisiaj.

## <a name="allowed-advanced-filter-keys"></a>Dozwolone zaawansowane klucze filtrów

Właściwość `Key` może być dobrze znaną właściwością najwyższego poziomu lub ścieżką json z wieloma kropkami, gdzie każda kropka oznacza przechodzenie do zagnieżdżonego obiektu json.

Usługa Event Grid nie ma żadnego `$` specjalnego znaczenia dla znaku w kluczu, w przeciwieństwie do specyfikacji JSONPath.

### <a name="event-grid-schema"></a>Schemat siatki zdarzeń

W przypadku zdarzeń w schemacie siatki zdarzeń:

* ID
* Temat
* Podmiot
* Typ zdarzenia
* DataVersion (Wersja danych)
* Dane.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Niestandardowy schemat zdarzeń

Nie ma żadnych ograniczeń `Key` w schemacie zdarzeń niestandardowych, ponieważ usługa Event Grid nie wymusza żadnego schematu koperty na ładunku.

## <a name="numeric-single-value-filter-examples"></a>Przykłady filtrów jednowartośćowych liczbowych

* LiczbaGreaterThan
* LiczbaGreaterThanOrEquals
* Liczba Bezduszny
* NumberLessThanOrEquals (Liczba bezliory)

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

## <a name="numeric-range-value-filter-examples"></a>Przykłady filtrów zakresu i wartości liczbowych

* LiczbaIn
* LiczbaNotIn

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

## <a name="string-range-value-filter-examples"></a>Przykłady filtrów zakresu ciągów

* CiągiZawiera
* StringBeginsWith
* StringEndsWith
* StringIn (Ciągin)
* StringNotIn (Niemy w ciągu ręczną

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

## <a name="boolean-single-value-filter-examples"></a>Przykłady filtrów logicznych z jedną wartością

* BoolEquals ( BoolEquals )

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
