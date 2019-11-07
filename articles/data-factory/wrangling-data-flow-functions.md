---
title: Funkcje transformacji przepływu danych przetwarzanie w Azure Data Factory
description: Omówienie dostępnych funkcji przepływu danych przetwarzanie w Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 3274641f7b118e13b3ed727f609ce7471fd66b54
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682286"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Funkcje transformacji w przepływie danych przetwarzanie

Przepływ danych przetwarzanie w Azure Data Factory umożliwia wykonywanie bezobsługowego przetwarzania danych Agile i przetwarzanie w skali chmury. Przepływ danych przetwarzanie integruje się z usługą [Power Query online](https://docs.microsoft.com/powerquery-m/power-query-m-reference) i udostępnia funkcje Power Query M dla przetwarzanie danych za pośrednictwem wykonywania platformy Spark. 

Obecnie nie wszystkie Power Query funkcje M są obsługiwane dla przetwarzanie danych, mimo że są dostępne podczas tworzenia. Podczas kompilowania przepływów danych przetwarzanie zostanie wyświetlony monit z następującym komunikatem o błędzie, jeśli funkcja nie jest obsługiwana:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Poniżej znajduje się lista obsługiwanych Power Query funkcji M.

## <a name="column-management"></a>Zarządzanie kolumnami

* Wybór: [Table. SelectColumns](https://docs.microsoft.com/powerquery-m/table-selectcolumns)
* Usuwanie: [Table. RemoveColumns](https://docs.microsoft.com/powerquery-m/table-removecolumns)
* Zmiana nazwy: [Table. RenameColumns](https://docs.microsoft.com/powerquery-m/table-renamecolumns), [Table. PrefixColumns](https://docs.microsoft.com/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](https://docs.microsoft.com/powerquery-m/table-transformcolumnnames)
* Zmiana kolejności: [Table. ReorderColumns](https://docs.microsoft.com/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Filtrowanie wierszy

Użyj funkcji M [. SelectRows](https://docs.microsoft.com/powerquery-m/table-selectrows) , aby odfiltrować następujące warunki:

* Równość i nierówność
* Porównania liczbowe, tekstowe i daty (ale nie daty i godziny)
* Informacje liczbowe, takie jak [Number. Isparzyste](https://docs.microsoft.com/powerquery-m/number-iseven)/[nieparzyste](https://docs.microsoft.com/powerquery-m/number-iseven)
* Zawieranie tekstu przy użyciu [Text. Contains](https://docs.microsoft.com/powerquery-m/text-contains), [Text. StartsWith](https://docs.microsoft.com/powerquery-m/text-startswith)lub [Text. EndsWith](https://docs.microsoft.com/powerquery-m/text-endswith)
* Zakresy dat, w tym wszystkie funkcje "IsIn' [Dates](https://docs.microsoft.com/powerquery-m/date-functions)" 
* Kombinacje tych elementów za pomocą i, lub, lub nie warunki

## <a name="adding-and-transforming-columns"></a>Dodawanie i przekształcanie kolumn

Następujące funkcje M dodają lub przekształcają kolumny: [Table. addColumn](https://docs.microsoft.com/powerquery-m/table-addcolumn), [Table. TransformColumns](https://docs.microsoft.com/powerquery-m/table-transformcolumns), [Table. ReplaceValue](https://docs.microsoft.com/powerquery-m/table-replacevalue), [Table. DuplicateColumn](https://docs.microsoft.com/powerquery-m/table-duplicatecolumn). Poniżej przedstawiono obsługiwane funkcje transformacji.

* Arytmetyka liczbowa
* Łączenie tekstu
* Date andTime arytmetyczne (operatory arytmetyczne, [Date. AddDays](https://docs.microsoft.com/powerquery-m/date-adddays), [Date. addmiesiącach](https://docs.microsoft.com/powerquery-m/date-addmonths), [Date. addćwiartks](https://docs.microsoft.com/powerquery-m/date-addquarters), [Date. Addtygs](https://docs.microsoft.com/powerquery-m/date-addweeks), [Date. AddYears](https://docs.microsoft.com/powerquery-m/date-addyears))
* Czasy trwania mogą być używane dla operacji arytmetycznych daty i godziny, ale muszą zostać przekształcone na inny typ przed zapisaniem w zlewie (operatory arytmetyczne, [#duration](https://docs.microsoft.com/powerquery-m/sharpduration), [czas trwania. dni](https://docs.microsoft.com/powerquery-m/duration-days), [czas trwania. godz](https://docs.microsoft.com/powerquery-m/duration-hours) [., czas trwania: minuty](https://docs.microsoft.com/powerquery-m/duration-minutes), [ Czas trwania. s](https://docs.microsoft.com/powerquery-m/duration-seconds), [Duration. TotalDays](https://docs.microsoft.com/powerquery-m/duration-totaldays), [Duration. TotalHours](https://docs.microsoft.com/powerquery-m/duration-totalhours), [Duration. TotalMinutes](https://docs.microsoft.com/powerquery-m/duration-totalminutes), [Duration. TotalSeconds](https://docs.microsoft.com/powerquery-m/duration-totalseconds))    
* Większość standardowych, naukowych i trygonometrycznych funkcji liczbowych (wszystkie funkcje w ramach [operacji](https://docs.microsoft.com/powerquery-m/number-functions#operations), [zaokrąglania](https://docs.microsoft.com/powerquery-m/number-functions#rounding)i [trygonometryczne](https://docs.microsoft.com/powerquery-m/number-functions#trigonometry) , *z wyjątkiem* Number. silnia, Number. permutacje i Number).
* Zastąpienie (preplacement[. ReplaceText](https://docs.microsoft.com/powerquery-m/replacer-replacetext), [replace. ReplaceValue](https://docs.microsoft.com/powerquery-m/replacer-replacevalue), [Text. Replace](https://docs.microsoft.com/powerquery-m/text-replace), [Text. Remove](https://docs.microsoft.com/powerquery-m/text-remove))
* Wyodrębnianie tekstu pozycyjnego ([Text. PositionOf](https://docs.microsoft.com/powerquery-m/text-positionof), [Text. length](https://docs.microsoft.com/powerquery-m/text-length), [Text. Start](https://docs.microsoft.com/powerquery-m/text-start), [Text. end](https://docs.microsoft.com/powerquery-m/text-end), [Text. Middle](https://docs.microsoft.com/powerquery-m/text-middle), [Text. ReplaceRange](https://docs.microsoft.com/powerquery-m/text-replacerange), [Text. RemoveRange](https://docs.microsoft.com/powerquery-m/text-removerange))
* Podstawowe formatowanie tekstu ([Text. Lower](https://docs.microsoft.com/powerquery-m/text-lower), [Text. Upper](https://docs.microsoft.com/powerquery-m/text-upper), [text. Trim](https://docs.microsoft.com/powerquery-m/text-trim)/[Start](https://docs.microsoft.com/powerquery-m/text-trimstart)/[End](https://docs.microsoft.com/powerquery-m/text-trimend), [Text. PadStart](https://docs.microsoft.com/powerquery-m/text-padstart)/[End](https://docs.microsoft.com/powerquery-m/text-padend), [Text. Reverse](https://docs.microsoft.com/powerquery-m/text-reverse))
* Funkcje daty i godziny ([Date. Day](https://docs.microsoft.com/powerquery-m/date-day), [Date. month](https://docs.microsoft.com/powerquery-m/date-month), [Date. Year](https://docs.microsoft.com/powerquery-m/date-year) [Time. Hour](https://docs.microsoft.com/powerquery-m/time-hour), [Time. minute](https://docs.microsoft.com/powerquery-m/time-minute), [Time. Second](https://docs.microsoft.com/powerquery-m/time-second), [Date. DayOfWeek](https://docs.microsoft.com/powerquery-m/date-dayofweek), [Date. dzieńroku](https://docs.microsoft.com/powerquery-m/date-dayofyear), [Date. DaysInMonth](https://docs.microsoft.com/powerquery-m/date-daysinmonth))
* Wyrażenie if (ale gałęzie muszą mieć zgodne typy)
* Filtry wierszy jako kolumna logiczna
* Stałe Number, text, Logical, date i DateTime

<a name="mergingjoining-tables"></a>Scalanie/Łączenie tabel
----------------------
* Power Query generuje zagnieżdżone sprzężenie (Table. NestedJoin; użytkownicy mogą również ręcznie napisać [tabelę. AddJoinColumn](https://docs.microsoft.com/powerquery-m/table-addjoincolumn)).
    Użytkownicy muszą następnie rozwinąć zagnieżdżoną kolumnę sprzężenia do niezagnieżdżonego sprzężenia (Table. ExpandTableColumn, nieobsługiwane w żadnym innym kontekście).
* Tabela funkcji M [. Join](https://docs.microsoft.com/powerquery-m/table-join) można pisać bezpośrednio, aby uniknąć potrzeby dodatkowego kroku rozszerzania, ale użytkownik musi upewnić się, że nie ma żadnych zduplikowanych nazw kolumn wśród sprzężonych tabel
* Obsługiwane rodzaje sprzężeń: [wewnętrzne](https://docs.microsoft.com/powerquery-m/joinkind-inner), [LeftOuter](https://docs.microsoft.com/powerquery-m/joinkind-leftouter), [RightOuter](https://docs.microsoft.com/powerquery-m/joinkind-rightouter), [FullOuter](https://docs.microsoft.com/powerquery-m/joinkind-fullouter)
* Obie [wartości. Equals](https://docs.microsoft.com/powerquery-m/value-equals) i [Value. NullableEquals](https://docs.microsoft.com/powerquery-m/value-nullableequals) są obsługiwane jako porównania klucza równości

## <a name="group-by"></a>Grupuj według

Użyj [tabeli Table. Group](https://docs.microsoft.com/powerquery-m/table-group) do agregowania wartości.
* Musi być używana z funkcją agregacji
* Obsługiwane funkcje agregacji: [Table. RowCount](https://docs.microsoft.com/powerquery-m/table-rowcount), [list. sum](https://docs.microsoft.com/powerquery-m/list-sum), [list. Count](https://docs.microsoft.com/powerquery-m/list-count), [list. Average](https://docs.microsoft.com/powerquery-m/list-average), [list. min](https://docs.microsoft.com/powerquery-m/list-min), List [. Max](https://docs.microsoft.com/powerquery-m/list-max), [list. StandardDeviation](https://docs.microsoft.com/powerquery-m/list-standarddeviation), list. [First](https://docs.microsoft.com/powerquery-m/list-first), [list. Last](https://docs.microsoft.com/powerquery-m/list-last)

## <a name="sorting"></a>sortowan

Użyj [tabeli. Sort](https://docs.microsoft.com/powerquery-m/table-sort) , aby sortować wartości.

## <a name="reducing-rows"></a>Zmniejszanie liczby wierszy

Zachowuj i usuwaj najważniejsze, zachowuj zakres (odpowiadające funkcje M, tylko obsługiwane liczby, a nie warunki: [Table. FirstN](https://docs.microsoft.com/powerquery-m/table-firstn), [Table. Skip](https://docs.microsoft.com/powerquery-m/table-skip), [Table. RemoveFirstN](https://docs.microsoft.com/powerquery-m/table-removefirstn), [Table. Range](https://docs.microsoft.com/powerquery-m/table-range), [Table. MinN](https://docs.microsoft.com/powerquery-m/table-minn), [Table. MaxN](https://docs.microsoft.com/powerquery-m/table-maxn))

## <a name="known-unsupported-functionality"></a>Znane Nieobsługiwane funkcje

Poniżej znajdują się funkcje, które nie są obsługiwane. Ta lista nie jest wyczerpująca i może ulec zmianie.
* Scal kolumny (można uzyskać za pomocą addColumn)
* Podziel kolumnę
* Dołącz zapytania
* "Użyj pierwszego wiersza jako nagłówków" i "Użyj nagłówków jako pierwszego wiersza"

## <a name="next-steps"></a>Następne kroki
