---
title: Szybkie referencje KQL
description: Lista przydatnych funkcji KQL i ich definicji z przykładami składni.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139083"
---
# <a name="kql-quick-reference"></a>Szybkie referencje KQL

W tym artykule przedstawiono listę funkcji i ich opisy ułatwiające rozpoczęcie korzystania z języka zapytań Kusto.

| Operator/funkcja                               | Opis                           | Składnia                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtr/Wyszukiwanie/Warunek**                      |**_Znajdowanie odpowiednich danych przez filtrowanie lub wyszukiwanie_** |                      |
| [where](/azure/kusto/query/whereoperator)                      | Filtry w określonym predykacie           | `T | where Predicate`                         |
| [gdzie zawiera/ma](/azure/kusto/query/whereoperator)        | `Contains`: Wyszukuje dowolne dopasowanie podciągów <br> `Has`: Szuka określonego słowa (lepsza wydajność)  | `T | where col1 contains/has "[search term]"`|
| [Szukaj](/azure/kusto/query/searchoperator)                    | Przeszukuje wszystkie kolumny w tabeli w poszukiwaniu wartości | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [wziąć](/azure/kusto/query/takeoperator)                        | Zwraca określoną liczbę rekordów. Służy do testowania kwerendy<br>**_Uwaga_** `_take`: `_limit`_ i _ są synonimami. | `T | take NumberOfRows` |
| [Przypadku](/azure/kusto/query/casefunction)                        | Dodaje instrukcję condition, podobną do if/then/elseif w innych systemach. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [distinct](/azure/kusto/query/distinctoperator)                | Tworzy tabelę z wyraźną kombinacją podanych kolumn tabeli wejściowej | `distinct [ColumnName], [ColumnName]` |
| **Data/godzina**                                   |**_Operacje korzystające z funkcji daty i godziny_**               |                          |
|[Temu](/azure/kusto/query/agofunction)                           | Zwraca przesunięcie czasu względem czasu wykonania kwerendy. Na przykład `ago(1h)` jest jedną godzinę przed odczytem bieżącego zegara. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Zwraca dane w [różnych formatach daty](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [Bin](/azure/kusto/query/binfunction)                          | Zaokrągla wszystkie wartości w przedziale czasu i grupuje je | `bin(value,roundTo)` |
| **Tworzenie/usuwanie kolumn**                   |**_Dodawanie lub usuwanie kolumn w tabeli_** |                                                    |
| [Drukowania](/azure/kusto/query/printoperator)                      | Wyprowadza pojedynczy wiersz z co najmniej jednym wyrażeń skalarnych | `print [ColumnName =] ScalarExpression [',' ...]` |
| [Projektu](/azure/kusto/query/projectoperator)                  | Wybiera kolumny do uwzględnienia w określonej kolejności | `T | project ColumnName [= Expression] [, ...]` <br> Lub <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt-away](/azure/kusto/query/projectawayoperator)         | Wybiera kolumny, które mają być wykluczone z danych wyjściowych | `T | project-away ColumnNameOrPattern [, ...]` |
| [Rozszerzyć](/azure/kusto/query/extendoperator)                    | Tworzy kolumnę obliczeniową i dodaje ją do zestawu wyników | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Sortowanie i agregowanie zestawu danych**                 |**_Restrukturyzacja danych poprzez sortowanie lub grupowanie ich w znaczący sposób_**|                  |
| [Sortowania](/azure/kusto/query/sortoperator)                        | Sortuje wiersze tabeli wprowadzania według jednej lub więcej kolumn w kolejności rosnącej lub malejącej | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Do góry](/azure/kusto/query/topoperator)                          | Zwraca pierwsze wiersze N zestawu danych, gdy zestaw danych jest sortowany przy użyciu`by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Podsumować](/azure/kusto/query/summarizeoperator)              | Grupuje wiersze zgodnie `by` z kolumnami grupy i oblicza agregacje dla każdej grupy | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [Liczba](/azure/kusto/query/countoperator)                       | Zlicza rekordy w tabeli wprowadzania (na przykład T)<br>Ten operator jest skrótem od`summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | Scala wiersze dwóch tabel, tworząc nową tabelę, dopasowując wartości określonych kolumn z każdej tabeli. Obsługuje pełną gamę typów `flouter`sprzężeń: `leftouter` `leftsemi`, `rightanti` `rightantisemi`, `rightouter` `inner` `innerunique`, `leftanti`, `leftantisemi`, , , , , ,`rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Unii](/azure/kusto/query/unionoperator)                      | Przyjmuje dwie lub więcej tabel i zwraca wszystkie ich wiersze | `[T1] | union [T2], [T3], …` |
| [Zakres](/azure/kusto/query/rangeoperator)                      | Generuje tabelę z arytmetyczną serią wartości | `range columnName from start to stop step step` |
| **Formatowanie danych**                                 | **_Restrukturyzacja danych do produkcji w użyteczny sposób_** | |
| [Wyszukiwania](/azure/kusto/query/lookupoperator)                    | Rozszerza kolumny tabeli faktów z wartościami wyszukanym w tabeli wymiarów | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [mv-rozwiń](/azure/kusto/query/mvexpandoperator)               | Przekształca tablice dynamiczne w wiersze (rozszerzenie wielowartowiowe) | `T | mv-expand Column` |
| [Przeanalizować](/azure/kusto/query/parseoperator)                      | Oblicza wyrażenie ciągu i analizuje jego wartość w jednej lub kilku kolumnach obliczeniowych. Służy do strukturyzowania danych nieustrukturyzowanych. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [make-series](/azure/kusto/query/make-seriesoperator)          | Tworzy serię określonych zagregowanych wartości wzdłuż określonej osi | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [Niech](/azure/kusto/query/letstatement)                         | Wiąże nazwę z wyrażeniami, które mogą odwoływać się do jego wartości powiązanej. Wartości mogą być wyrażeniami lambda do tworzenia funkcji ad hoc jako część kwerendy. Służy `let` do tworzenia wyrażeń nad tabelami, których wyniki wyglądają jak nowa tabela. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Ogólne**                                     | **_Różne operacje i funkcja_** | |
| [Wywołać](/azure/kusto/query/invokeoperator)                    | Uruchamia funkcję w tabeli, która otrzymuje jako dane wejściowe. | `T | invoke function([param1, param2])` |
| [ocena pluginName](/azure/kusto/query/evaluateoperator)     | Ocenia rozszerzenia języka zapytań (wtyczki) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Wizualizacji**                               | **_Operacje, które wyświetlają dane w formacie graficznym_** | |
| [Renderowania](/azure/kusto/query/renderoperator) | Renderuje wyniki jako dane wyjściowe graficzne | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
