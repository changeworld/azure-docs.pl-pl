---
title: KQL — Krótki przewodnik
description: Lista użytecznych funkcji KQL i ich definicji z przykładami składni.
author: orspod
ms.author: orspodek
ms.reviewer: ''
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: e7196ccccb1203ce56b2e53fa358d091374cd3f8
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79139083"
---
# <a name="kql-quick-reference"></a>KQL — Krótki przewodnik

W tym artykule przedstawiono listę funkcji i ich opisów, które ułatwiają rozpoczęcie pracy przy użyciu języka zapytań Kusto.

| Operator/funkcja                               | Opis                           | Składnia                                           |
| :---------------------------------------------- | :------------------------------------ |:-------------------------------------------------|
|**Filtr/wyszukiwanie/warunek**                      |**_Znajdowanie odpowiednich danych przez filtrowanie lub wyszukiwanie_** |                      |
| [miejscu](/azure/kusto/query/whereoperator)                      | Filtry na określonym predykacie           | `T | where Predicate`                         |
| [gdzie Contains/ma](/azure/kusto/query/whereoperator)        | `Contains`: szuka dowolnego dopasowania podciągu <br> `Has`: szuka określonego wyrazu (lepsza wydajność)  | `T | where col1 contains/has "[search term]"`|
| [wyszukiwania](/azure/kusto/query/searchoperator)                    | Przeszukuje wszystkie kolumny w tabeli pod kątem wartości | `[TabularSource |] search [kind=CaseSensitivity] [in (TableSources)] SearchPredicate` |
| [czasochłonn](/azure/kusto/query/takeoperator)                        | Zwraca określoną liczbę rekordów. Użyj do testowania zapytania<br>**_Uwaga_** : `_take`_ i `_limit`_ są synonimami. | `T | take NumberOfRows` |
| [spraw](/azure/kusto/query/casefunction)                        | Dodaje instrukcję Condition, podobną do if/then/ElseIf w innych systemach. | `case(predicate_1, then_1, predicate_2, then_2, predicate_3, then_3, else)` |
| [itp](/azure/kusto/query/distinctoperator)                | Tworzy tabelę z odrębną kombinacją podanych kolumn tabeli wejściowej. | `distinct [ColumnName], [ColumnName]` |
| **Data/godzina**                                   |**_Operacje korzystające z funkcji daty i godziny_**               |                          |
|[temu](/azure/kusto/query/agofunction)                           | Zwraca przesunięcie czasu względem czasu wykonywania zapytania. Na przykład `ago(1h)` jest godzinę przed odczytem bieżącego zegara. | `ago(a_timespan)` |
| [format_datetime](/azure/kusto/query/format-datetimefunction)  | Zwraca dane w [różnych formatach daty](/azure/kusto/query/format-datetimefunction#supported-formats). | `format_datetime(datetime , format)` |
| [określonej](/azure/kusto/query/binfunction)                          | Zaokrągla wszystkie wartości w przedziale czasowym i grupuje je | `bin(value,roundTo)` |
| **Utwórz/usuń kolumny**                   |**_Dodawanie lub usuwanie kolumn w tabeli_** |                                                    |
| [drukowany](/azure/kusto/query/printoperator)                      | Wyprowadza pojedynczy wiersz z co najmniej jednym wyrażeniem skalarnym | `print [ColumnName =] ScalarExpression [',' ...]` |
| [projektu](/azure/kusto/query/projectoperator)                  | Wybiera kolumny do uwzględnienia w podanej kolejności. | `T | project ColumnName [= Expression] [, ...]` <br> Lub <br> `T | project [ColumnName | (ColumnName[,]) =] Expression [, ...]` |
| [projekt — poza](/azure/kusto/query/projectawayoperator)         | Wybiera kolumny, które mają zostać wykluczone z danych wyjściowych. | `T | project-away ColumnNameOrPattern [, ...]` |
| [sunąć](/azure/kusto/query/extendoperator)                    | Tworzy kolumnę obliczeniową i dodaje ją do zestawu wyników | `T | extend [ColumnName | (ColumnName[, ...]) =] Expression [, ...]` |
| **Sortowanie i agregowanie zestawu danych**                 |**_Restrukturyzacja danych przez sortowanie lub grupowanie ich w znaczący sposób_**|                  |
| [porządku](/azure/kusto/query/sortoperator)                        | Sortuje wiersze tabeli wejściowej przy użyciu co najmniej jednej kolumny w kolejności rosnącej lub malejącej. | `T | sort by expression1 [asc|desc], expression2 [asc|desc], …` |
| [Do góry](/azure/kusto/query/topoperator)                          | Zwraca pierwsze N wierszy zestawu danych, gdy zestaw danych jest sortowany przy użyciu `by` | `T | top numberOfRows by expression [asc|desc] [nulls first|last]` |
| [Podsumuj](/azure/kusto/query/summarizeoperator)              | Grupuje wiersze zgodnie z kolumnami `by` grupy i oblicza agregacje dla każdej grupy | `T | summarize [[Column =] Aggregation [, ...]] [by [Column =] GroupExpression [, ...]]` |
| [count](/azure/kusto/query/countoperator)                       | Zlicza rekordy w tabeli wejściowej (na przykład T)<br>Ten operator jest skrócony dla `summarize count() `| `T | count` |
| [join](/azure/kusto/query/joinoperator)                        | Scala wiersze dwóch tabel, aby utworzyć nową tabelę przez dopasowanie wartości określonych kolumn z każdej tabeli. Obsługuje pełny zakres typów sprzężenia: `flouter`, `inner`, `innerunique`, `leftanti`, `leftantisemi`, `leftouter`, `leftsemi`, `rightanti`, `rightantisemi`, `rightouter`, `rightsemi` | `LeftTable | join [JoinParameters] ( RightTable ) on Attributes` |
| [Unii](/azure/kusto/query/unionoperator)                      | Pobiera co najmniej dwie tabele i zwraca wszystkie wiersze | `[T1] | union [T2], [T3], …` |
| [zakresu](/azure/kusto/query/rangeoperator)                      | Generuje tabelę z serią arytmetyczną wartości | `range columnName from start to stop step step` |
| **Formatowanie danych**                                 | **_W wygodny sposób można zmienić strukturę danych na dane wyjściowe_** | |
| [wyszukiwania](/azure/kusto/query/lookupoperator)                    | Rozszerza kolumny tabeli faktów z wartościami wyszukanymi w tabeli wymiarów | `T1 | lookup [kind = (leftouter|inner)] ( T2 ) on Attributes` |
| [MV — rozwiń](/azure/kusto/query/mvexpandoperator)               | Włącza dynamiczne tablice do wierszy (rozszerzanie o wiele wartości) | `T | mv-expand Column` |
| [przetwarzania](/azure/kusto/query/parseoperator)                      | Oblicza wyrażenie ciągu i analizuje jego wartość w co najmniej jednej kolumnie obliczeniowej. Służy do tworzenia struktury danych bez struktury. | `T | parse [kind=regex  [flags=regex_flags] |simple|relaxed] Expression with * (StringConstant ColumnName [: ColumnType]) *...` |
| [Utwórz serię](/azure/kusto/query/make-seriesoperator)          | Tworzy serię określonych wartości agregowanych wzdłuż określonej osi | `T | make-series [MakeSeriesParamters] [Column =] Aggregation [default = DefaultValue] [, ...] on AxisColumn from start to end step step [by [Column =] GroupExpression [, ...]]` |
| [wpuść](/azure/kusto/query/letstatement)                         | Tworzy powiązanie nazwy z wyrażeniami, które mogą odwoływać się do jego wartości powiązanej. Wartości mogą być wyrażeniami lambda w celu utworzenia funkcji ad hoc w ramach zapytania. Użyj `let`, aby tworzyć wyrażenia na tabelach, których wyniki wyglądają jak nowa tabela. | `let Name = ScalarExpression | TabularExpression | FunctionDefinitionExpression` |
| **Ogólne**                                     | **_Różne operacje i funkcje_** | |
| [wywołuje](/azure/kusto/query/invokeoperator)                    | Uruchamia funkcję w tabeli, która otrzymuje jako dane wejściowe. | `T | invoke function([param1, param2])` |
| [Oceń wtyczkę](/azure/kusto/query/evaluateoperator)     | Oblicza rozszerzenia języka zapytań (wtyczki) | `[T |] evaluate [ evaluateParameters ] PluginName ( [PluginArg1 [, PluginArg2]... )` |
| **Dopasowywa**                               | **_Operacje, które wyświetlają dane w formacie graficznym_** | |
| [renderowania](/azure/kusto/query/renderoperator) | Renderuje wyniki jako graficzne dane wyjściowe | `T | render Visualization [with (PropertyName = PropertyValue [, ...] )]` |
