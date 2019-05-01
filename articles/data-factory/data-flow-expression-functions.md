---
title: Wyrażenie funkcji w funkcji mapowania przepływu danych usługi Azure Data Factory
description: Więcej informacji na temat funkcji wyrażenia w mapowanie przepływu danych.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 6b4df70114dd481566ae1a666c91c1c9b5bad86f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717026"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Wyrażenia przekształcania danych w mapowanie przepływu danych 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Wyrażenie funkcji

W usłudze Data Factory umożliwia skonfigurowanie przekształcenia danych język wyrażeń funkcji mapowanie przepływu danych.

*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie oznacza pozytywny resztę dwóch cyfr.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartości arcus cosinus.
* ``acos(1) -> 0.0``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
To wyrażenie dodaje pary ciągów lub liczby. Dodaje wartość typu date do liczby dni. Jedna tablica podobnych typach dołącza do innego. 
* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``

**Dodaj** operator jest taka sama jak **+** operatora.
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
To wyrażenie dodaje dni do daty lub sygnatury czasowej. 
* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``

**AddDays** operator jest taka sama jak **+** operator dla daty.
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
To wyrażenie dodaje miesięcy do daty lub sygnatury czasowej.
* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Jest to wartość logiczna **i** operatora. To jest taka sama jak **&&** operatora.
* ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartość funkcji arcus sinus.
* ``asin(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartość funkcji odwrotny tangens.
* ``atan(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie zwraca wartość kąta w radianach między dodatnią osi x płaszczyznę i podać współrzędne punktu.
* ``atan2(0, 0) -> 0.0``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie pobiera średnią wartości kolumny.
* ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera średnią wartości kolumny.
* ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>byName</code>
==============================
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
To wyrażenie wybiera wartość w kolumnie według nazwy w strumieniu. Jeśli istnieje wiele dopasowań, zwracany jest pierwsze dopasowanie. W przypadku braku dopasowania, wyrażenie zwraca wartość NULL. Zwracana wartość musi być konwertowany na typ za pomocą jednej z funkcje konwersji typu (TO_DATE, to_string —...). Nazwy kolumn, które są znane w czasie projektowania, powinny być kierowane tylko według nazwy. Obliczona dane wejściowe nie są obsługiwane, ale można użyć podstawieniach parametrów.

* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
*********************************
<code>byPosition</code>
==============================
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
To wyrażenie wybiera wartość kolumny przez jego względne położenie (w oparciu o 1) w strumieniu. Jeśli pozycja jest poza zakresem, zwraca wartość NULL. Zwracana wartość musi być konwertowany na typ za pomocą jednej z funkcje konwersji typu (TO_DATE to_string — i tak dalej). Obliczona dane wejściowe nie są obsługiwane, ale można użyć podstawieniach parametrów.

* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Oparte na przemienne warunków, to wyrażenie ma zastosowanie jednej wartości lub innych. 
* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``

Jeśli liczba danych wejściowych jest parzysta, inne wartości ma wartość NULL dla ostatniego warunku.
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza pierwiastek modułu liczby.
* ``cbrt(8) -> 2.0``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie zwraca najmniejszą liczbę całkowitą, która nie jest mniejsza niż liczba.
* ``ceil(-0.1) -> 0``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
To wyrażenie łączy zmienną liczbę parametrów. **Concat** operator jest taka sama jak **+** operatora z ciągami.
* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
To wyrażenie łączy zmienną liczbę parametrów wraz z separatorem. Pierwszy parametr jest separatora.
* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartość funkcji cosinus.
* ``cos(10) -> -0.83907152907``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza cosinus hiperboliczny wartości.
* ``cosh(0) -> 1.0``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
To wyrażenie pobiera zagregowana liczba wartości. W przypadku kolumn opcjonalnych lub kolumny określony, wartość NULL w liczbie jest ignorowany.
* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
To wyrażenie pobiera łączna liczba unikatowych wartości właściwości zestawu kolumn.
* ``countDistinct(custId, custName) -> 60``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera zagregowana liczba wartości. Jeśli opcjonalna kolumna jest określony, wartość NULL w liczbie jest ignorowany.
* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera populacji kowariancję dwóch kolumn.
* ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera kowariancję dwóch kolumn w populacji.
* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera przykładową kowariancję dwóch kolumn.
* ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera przykładową kowariancję dwóch kolumn.
* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
To wyrażenie oblicza skrót CRC32 zestaw kolumn o różnych typów danych pierwotnych, biorąc pod uwagę długość w bitach których wartości mogą być tylko 0(256) 224, 256, 384, 512. Możesz użyć **crc32** operatora, aby obliczyć odcisku palca dla wiersza.
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
Ta funkcja oblicza pozycja wartości względem wszystkich wartości w partycji. Wynik jest liczbą wierszy poprzedniego lub równa bieżącego wiersza w kolejności partycji, podzielona przez łączną liczbę wierszy w partycji okna. Wszelkie wartości tie w kolejności, zwracają taką samą pozycję.
* ``cumeDist() -> 1``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
To wyrażenie pobiera bieżącą datę, kiedy zadanie zaczyna być uruchamiana. 
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``

Możesz przekazać opcjonalny strefy czasowej w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Lokalnej strefy czasowej jest ustawieniem domyślnym.
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
To wyrażenie pobiera bieżącą sygnaturę czasową, kiedy zadanie zaczyna być uruchamiana przy użyciu lokalnej strefy czasowej.
* ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
To wyrażenie pobiera bieżącą sygnaturę czasową jako czas UTC. 
* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``

Możesz przekazać opcjonalny strefy czasowej w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Lokalnej strefy czasowej jest ustawieniem domyślnym.
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Danej wartości typu date, to wyrażenie pobiera dzień miesiąca.
* ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Danej wartości typu date, to wyrażenie pobiera dzień tygodnia. 
* ``dayOfWeek(toDate('2018-06-08')) -> 7``

Wartości dni są następujące:
- 1 — niedziela
- 2 — poniedziałek 
- Przyciski ...
- 7 — sobota
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Danej wartości typu date, to wyrażenie pobiera dzień roku.
* ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie Konwertuje radiany na stopnie.
* ``degrees(3.141592653589793) -> 180``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
To wyrażenie oblicza pozycję wartości w grupy wartości. Wynik jest jedną oraz liczbę wierszy w poprzednim lub równa bieżącego wiersza w kolejności partycji. Wartości nie będzie generować przerwy w sekwencji. 
* ``denseRank(salesQtr, salesAmt) -> 1``

**DenseRank** operator działa, nawet jeśli dane nie są posortowane. Wygląda na zmianę wartości.
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
To wyrażenie dzieli dwóch cyfr. **Dzielenia** operator jest taka sama jak **/** operatora.
* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy ciąg kończy się podany ciąg.
* ``endsWith('great', 'eat') -> true``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jest to operator porównania równości. Jest taka sama jak **==** operatora.
* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
**EqualsIgnoreCase** operator jest operator equals porównania, które ignoruje wielkość liter. Jest taka sama jak **<=>** operatora.
* ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
To wyrażenie Oblicza silnię liczby.
* ``factorial(5) -> 120``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
To wyrażenie zawsze zwraca wartość false. 
* ``isDiscounted == false()``
* ``isDiscounted() == false``

Użyj `syntax(false())` działać, jeśli kolumna ma nazwę *false*.
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
To wyrażenie pobiera pierwszą wartość grupę kolumn. Jeśli drugi parametr zostanie pominięty `ignoreNulls`, zakłada się, wartość false.
* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie zwraca największa liczba całkowita, która nie jest większa niż liczba.
* ``floor(-0.1) -> -1``
*********************************
<code>fromUTC</code>
==============================
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
To wyrażenie konwertuje sygnaturę czasową od czasu UTC. Strefa czasowa można opcjonalnie przekazać w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Ustawieniem domyślnym jest bieżąca strefa czasowa.

* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
To porównanie **większą** operatora. Jest taka sama jak **>** operatora.
* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jest większe niż — lub równości operator porównania. Ten operator jest taka sama jak **>=** operatora.
* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
To wyrażenie zwraca największą wartość na liście wartości jako dane wejściowe. Zwraca wartość NULL, jeśli wszystkie dane wejściowe mają wartość NULL.
* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
To wyrażenie pobiera wartość godziny z sygnatury czasowej. 
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``

Możesz przekazać opcjonalny strefy czasowej w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Lokalnej strefy czasowej jest ustawieniem domyślnym.
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Na podstawie warunku, to wyrażenie ma zastosowanie jednej wartości lub innych. Inna wartość jest nieokreślony, jest uważany za wartość NULL. Obie wartości muszą być zgodne (numeryczny lub ciąg, na przykład).
* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy element w tablicy.
* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie konwertuje pierwszą literę każdego wyrazu na wielkie litery. Wyrazy są identyfikowane przez odejścia odstępu.
* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
To wyrażenie umożliwia znalezienie położenie podciągu wewnątrz ciągu (oparte na 1). Jeśli pozycja nie zostanie znaleziona, zwracany jest 0. 
* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy wiersze, które zostały oznaczone do usunięcia. 
* ``isDelete() -> true``
* ``isDelete(1) -> false``

Jeśli swoją transformację przyjmuje więcej niż jeden strumień wejściowy, można przekazać indeks strumienia (od 1). Indeks strumienia wartość domyślna to 1.
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy wiersze oznaczone jako błędy.
* ``isError() -> true``
* ``isError(1) -> false``

Jeśli swoją transformację przyjmuje więcej niż jeden strumień wejściowy, można przekazać indeks strumienia (od 1). Indeks strumienia wartość domyślna to 1.
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy wierszy jest pomijany.
* ``isIgnore() -> true``
* ``isIgnore(1) -> false``

Jeśli swoją transformację przyjmuje więcej niż jeden strumień wejściowy, można przekazać indeks strumienia (od 1). Indeks strumienia wartość domyślna to 1.
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy wiersze oznaczone do wstawienia. 
* ``isInsert() -> true``
* ``isInsert(1) -> false``

Jeśli swoją transformację przyjmuje więcej niż jeden strumień wejściowy, można przekazać indeks strumienia (od 1). Indeks strumienia wartość domyślna to 1.
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy wiersze na wyszukiwania. 
* ``isMatch() -> true``
* ``isMatch(1) -> false``

Jeśli swoją transformację przyjmuje więcej niż jeden strumień wejściowy, można przekazać indeks strumienia (od 1). Indeks strumienia wartość domyślna to 1.
*********************************
<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy wartość NULL.
* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy wiersze oznaczony do aktualizacji. 
* ``isUpdate() -> true``
* ``isUpdate(1) -> false``

Jeśli swoją transformację przyjmuje więcej niż jeden strumień wejściowy, można przekazać indeks strumienia (od 1). Indeks strumienia wartość domyślna to 1.
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera kurtoza kolumny.
* ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera kurtoza kolumny.
* ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
To wyrażenie pobiera wartość pierwszego parametru, które są oceniane *n* wiersze przed bieżącego wiersza. Drugi parametr jest liczba wierszy Wstecz. Wartość domyślna to 1. Jeśli nie ma dowolną liczbę wierszy, zwracany jest wartość NULL, jeśli nie określono wartości domyślnej.
* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
To wyrażenie pobiera ostatnią wartość grupę kolumn. Jeśli drugi parametr zostanie pominięty `ignoreNulls`, wyrażenie zwraca `false`.
* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Danej wartości typu date, to wyrażenie pobiera ostatni dzień miesiąca.
* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
To wyrażenie pobiera wartość pierwszego parametru, które są oceniane *n* wiersze po bieżącego wiersza. Drugi parametr jest liczba wierszy do wyszukiwania do przodu. Wartość domyślna to 1. Jeśli nie ma dowolną liczbę wierszy, zwracany jest wartość NULL, jeśli nie określono wartości domyślnej.
* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Jest mniejsze niż lub równości operator porównania. Jest taka sama jak **<=** operatora.
* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>left</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
To wyrażenie wyodrębnia rozpoczęcia podciąg pod indeksem 1 i użyje liczby znaków. **Po lewej stronie** operator jest taka sama jak **SUBSTRING (str, 1, n)**.
* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
To wyrażenie zwraca długość ciągu.
* ``length('kiddo') -> 5``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
To porównanie mniejszym-than-operator. Jest taka sama jak **<** operatora.
* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Jest mniejsze niż lub równości operator porównania. Jest taka sama jak **<=** operatora.
* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
To wyrażenie pobiera Levenshtein odległość między dwa ciągi.
* ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
W tym wyrażeniu wzorzec jest ciągiem, który jest literału. 
* ``like('icecream', 'ice%') -> true``

Wyjątki są następujące znaki specjalne:  
* `_` To dopasowuje dowolny znak w danych wejściowych. Jest on podobny do `.` w wyrażeniach regularnych POSIX.
* `%` To dopasowuje zero lub więcej znaków w danych wejściowych. Ten symbol jest podobny do `.*` w wyrażeniach regularnych POSIX.
* `''` Jest to znak ucieczki. Jeśli znak ucieczki poprzedza symbol specjalny lub inny znak ucieczki, następujący znak jest literału. Nie można wyeliminować jakikolwiek inny znak.
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
To wyrażenie umożliwia znalezienie położenie podciągu wewnątrz ciągu, zaczynając od niektórych pozycji (oparte na 1). Jeżeli pominięto pozycja oceny rozpoczyna się od początku ciągu. Jeśli pozycja nie zostanie znaleziona, zwracany jest 0. 
* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
To wyrażenie Oblicza logarytm. Można podać opcjonalne podstawowego lub liczbą Eulera, jeśli jest on używany.
* ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Wyrażenie to używa 10 podstawowej, aby obliczyć logarytm.
* ``log10(100) -> 2``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie ustawia ciąg małymi literami.
* ``lower('GunChus') -> 'gunchus'``
*********************************
<code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Za pomocą wypełnienia dostarczony, to wyrażenie po lewej stronie, podkładki ciągu do ciągu osiągnie o określonej długości. Ten ciąg jest równa lub większa od długości, jest uznawane za żadna operacja (pusta).
* ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* "lpad ("doskonałe"8" <> ") ->" <><great'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie po lewej stronie — przycina początkowe znaki ciągu. Jeśli drugi parametr jest nieokreślony, wyrażenie przycina odstępu. W przeciwnym razie przycina znak, który określa drugi parametr.
* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
To wyrażenie pobiera maksymalną wartość kolumny.
* ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera maksymalną wartość kolumny.
* ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
To wyrażenie oblicza Skrót MD5 zestaw kolumn różnych typów danych pierwotnych. Zwraca ciąg szesnastkowy 32 znaków. 
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``

Możesz użyć **md5** operatora, aby obliczyć odcisku palca dla wiersza.
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie pobiera średniej wartości kolumny. **Oznacza** operator jest taka sama jak Średni
* ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera średniej wartości kolumny. **MeanIf** operator jest taka sama jak **avgIf**.
* ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
To wyrażenie pobiera minimalną wartość kolumny.
* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera minimalną wartość kolumny.
* ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
To wyrażenie odejmowania liczb. Na przykład jego Odejmij przez liczbę dni od daty. **Minus** operator jest taka sama jak **-** operatora.
* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
To wyrażenie pobiera minuty wartość sygnatury czasowej. 
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``

Możesz przekazać opcjonalny strefy czasowej w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Lokalnej strefy czasowej jest ustawieniem domyślnym.
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
**Mod** operator oznacza parę modulo liczb. To jest taka sama jak **%** operatora.
* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
To wyrażenie pobiera wartość miesiąca, daty lub sygnatury czasowej.
* ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
To wyrażenie pobiera liczbę miesięcy między dwiema datami. 
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``

Możesz przekazać opcjonalny strefy czasowej w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Lokalnej strefy czasowej jest ustawieniem domyślnym.
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
To wyrażenie mnoży dwóch cyfr. **Mnożenia** operator jest taka sama jak * — operator.
* ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
**NTile** funkcja dzieli wierszy dla każdej partycji okna do *n* przedziały, w których należeć do zakresu od 1 do co najwyżej *n*. Wartości różnią się maksymalnie 1. Jeśli liczba wierszy w partycji nie równomiernie podzielić liczbę przedziałów, każdej pozostałe wartości jest wysyłana do koszyka, począwszy od pierwszego pakietu. 

* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``

**NTile** funkcja jest przydatna, gdy należy obliczyć tertiles, Kwartyle, deciles i inne typowe statystyki podsumowujące. Funkcja oblicza dwie zmienne podczas inicjowania. Jeden dodatkowy wiersz jest dodawana do zasobnika regularne. Obie zmienne są oparte na rozmiar bieżącej partycji. 

Podczas obliczania funkcja przechowuje informacje o bieżący numer wiersza, bieżący numer przedziału i numer wiersza, w którym zasobniku zmieni (bucketThreshold). Gdy numer wiersza osiąga próg zasobnik, wartość zasobnika zwiększa się o jeden. Próg zwiększa się rozmiar zasobnika plus jeden dodatkowy Jeśli bieżące zasobnika jest uzupełniana.
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie neguje liczbą. Okazuje się liczbami dodatnimi liczbami ujemnymi i odwrotnie.
* ``negate(13) -> -13``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
To wyrażenie zwraca następny unikatowy ciąg. Liczba znajduje się kolejnych tylko w ramach partycji. Jest poprzedzony `partitionId`.
* ``nextSequence() -> 12313112``
*********************************
<code>normalize</code>
==============================
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizuj wartość ciągu do oddzielania znaki akcentowane unicode * ``normalize('boys') -> 'boys'``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
**Nie** operator jest operator logiczny negacji.
* ``not(true) -> false``
* ``not(premium)``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
**NotEquals** operator jest operator not equals porównania. Jest taka sama jak **! =** operatora.
* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
To wyrażenie zwraca wartość NULL. 
* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``

Użyj funkcji **syntax(null())** Jeśli kolumna ma nazwę *null*. Każdej operacji, która używa operatora wartości NULL powoduje o wartości NULL.
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
**Lub** operator jest wartość logiczna **lub** operatora. Jest taka sama jak **||** operatora.
* ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
**PMod** operator oznacza pozytywny resztę dwóch cyfr.
* ``pmod(-20, 8) -> 4``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie wywołuje jedną liczbę do potęgi równej innej.
* ``power(10, 2) -> 100``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
To wyrażenie oblicza pozycję wartości w grupy wartości. Wynik jest jedną oraz liczbę wierszy w poprzednim lub równa bieżącego wiersza w kolejności partycji. Wartości produktu przerwy w sekwencji. 
* ``rank(salesQtr, salesAmt) -> 1``

**Ranga** operator działa, nawet jeśli dane nie są posortowane. Wyszukuje zmiany w wartościach.
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
To wyrażenie zwraca podciąg dopasowania dla wzorca wyrażenia regularnego danego. Ostatni parametr identyfikuje grupę dopasowania. Jeśli ostatni parametr zostanie pominięty, wartość domyślna to 1. 
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``

Użyj `<regex>`(odwrócony pojedynczy cudzysłów) próby dopasowania ciągu bez anulowania zapewnianego element.
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy ciąg pasuje do wzorca wyrażenia regularnego danego. 
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``

Użyj `<regex>`(odwrócony pojedynczy cudzysłów) próby dopasowania ciągu bez anulowania zapewnianego element.
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie zamienia wszystkie wystąpienia do wzorca wyrażenia regularnego inny podciąg do danego ciągu.
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``

Użyj `<regex>`(odwrócony pojedynczy cudzysłów) próby dopasowania ciągu bez anulowania zapewnianego element.
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
To wyrażenie dzieli ciąg na podstawie ogranicznika oparte na wyrażenie regularne. Zwraca tablicę ciągów.
* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie zamienia wszystkie wystąpienia podciągu inny podciąg do danego ciągu.
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie odwraca ciągu.
* ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
To wyrażenie zwraca podciąg z liczbą znaków z prawej strony. 
* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``

**Prawo** funkcji jest taka sama jak SUBSTRING (str LENGTH(str) - n, n).
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy ciąg pasuje do wzorca wyrażenia regularnego danego.
* ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Uwagi opcjonalny skalowania oraz opcjonalny trybu zaokrąglania, to wyrażenie zaokrągla liczbę. Jeżeli pominięto skali, wartość domyślna to 0. Jeżeli pominięto tryb, wartość domyślna to ROUND_HALF_UP(5). 

* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``

Są to zaokrąglanie wartości:
* 1 — ROUND_UP
* 2 - ROUND_DOWN
* 3 - ROUND_CEILING
* 4 — ROUND_FLOOR
* 5 - ROUND_HALF_UP
* 6 - ROUND_HALF_DOWN
* 7 - ROUND_HALF_EVEN
* 8 - ROUND_UNNECESSARY

*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
To wyrażenie przypisuje kolejny wiersz numerowanie wierszy w oknie, począwszy od 1.
* ``rowNumber() -> 1``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie po prawej stronie, podkładki ciąg przez podany uzupełnienia do ciągu osiągnie o określonej długości. Ten ciąg jest równa lub większa od długości, jest uznawane za żadna operacja (pusta).
* ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
<code>rtrim</code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie po prawej stronie — przycina początkowe znaki ciągu. Jeśli nie określisz drugi parametr wyrażenia przycina odstępu. W przeciwnym razie przycina dowolny znak, który określa drugi parametr.
* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
To wyrażenie pobiera druga wartość daty. 
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``

Możesz przekazać opcjonalny strefy czasowej w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Lokalnej strefy czasowej jest ustawieniem domyślnym.
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
To wyrażenie oblicza Skrót SHA-1 zestaw kolumn różnych typów danych pierwotnych. Zwraca ciąg szesnastkowy 40 znaków. 
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``

Możesz użyć `sha1` do obliczania odcisku palca dla wiersza.
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
To wyrażenie oblicza Skrót SHA-2 kolumny o różnych typów danych pierwotnych, biorąc pod uwagę długość w bitach których wartości mogą być tylko 0(256) 224, 256, 384, 512. 
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``

Możesz użyć `sha2` do obliczania odcisku palca dla wiersza.
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartość funkcji sinus.
* ``sin(2) -> 0.90929742682``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartość funkcji sinus hiperboliczny.
* ``sinh(0) -> 0.0``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera skośność kolumny.
* ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera skośność kolumny.
* ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
To wyrażenie wyodrębnia podzbiór tablicy, od pozycji. Pozycja jest oparty na 1. Jeżeli pominięto długość, wartość domyślna to końca ciągu.
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie pobiera kod soundex ciągu.
* ``soundex('genius') -> 'G520'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
To wyrażenie dzieli ciąg na podstawie ogranicznika. Zwraca tablicę ciągów.
* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza pierwiastek kwadratowy z liczby.
* ``sqrt(9) -> 3``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
To wyrażenie sprawdza, czy ciąg zaczyna się od podany ciąg.
* ``startsWith('great', 'gr') -> true``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera odchylenie standardowe kolumny.
* ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera odchylenie standardowe kolumny.
* ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera odchylenie standardowe populacji kolumny.
* ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera odchylenie standardowe populacji kolumny.
* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera odchylenie standardowe próbki kolumny.
* ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera odchylenie standardowe próbki kolumny.
* ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
To wyrażenie odejmuje miesięcy od daty. 
* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
**SubDays** operator jest taka sama jak **-** operator daty.
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
To wyrażenie odejmuje miesięcy od daty lub sygnatury czasowej.
* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
To wyrażenie zwraca podciąg o określonej długości od pozycji. Pozycja jest oparty na 1. Jeżeli pominięto długość, wartość domyślna to końca ciągu.
* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie pobiera łącznej sumy kolumny liczbowej.
* ``sum(col) -> value``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
To wyrażenie pobiera agregacji Suma odrębne wartości kolumny liczbowej.
* ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera łącznej sumy kolumny liczbowej. Można utworzyć warunek, według dowolnej kolumny.
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera łącznej sumy kolumny liczbowej. Można utworzyć warunek, według dowolnej kolumny.
* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartość funkcji tangens.
* ``tan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie oblicza wartość funkcji tangens hiperboliczny.
* ``tanh(0) -> 0.0``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
To wyrażenie konwertuje wartość `('t', 'true', 'y', 'yes', '1')` na wartość true. Konwertuje `('f', 'false', 'n', 'no', '0')` o wartości false. Dla każdej innej wartości zwraca wartość NULL.
* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Biorąc pod uwagę format opcjonalny daty, to wyrażenie konwertuje ciąg na wartość typu date. Można znaleźć Java SimpleDateFormat wszystkie formaty daty możliwe. 
* ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``

Jeżeli pominięto format daty są akceptowane kombinacji następujących: [yyyy, rrrr-[M] M, rrrr-[M] M - d [d] rrrr-[M] min [d]-d, rrrr-[M] min [d]-d, rrrr-[M] M-[d] dT *].
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
To wyrażenie konwertuje wszelkie numeryczny lub ciąg na wartość dziesiętną. 
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``

Jeśli nie określisz dokładności i skali, wartość domyślna to (10,2). Opcjonalne format dziesiętny języka Java można użyć do konwersji.

*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
To wyrażenie konwertuje wszelkie numeryczny lub ciąg na wartość podwójną. Opcjonalne format dziesiętny języka Java można użyć do konwersji.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
To wyrażenie konwertuje wszelkie numeryczny lub ciąg wartością zmiennoprzecinkową. Opcjonalne format dziesiętny języka Java można użyć do konwersji. 
* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``

**ToFloat** Funkcja obcina wszelkie podwójnej precyzji.
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
To wyrażenie konwertuje wszelkie numeryczny lub ciąg na wartość całkowitą. Opcjonalne format dziesiętny języka Java można użyć do konwersji. 
* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``

**ToInteger** Funkcja obcina dowolne long, float lub podwójny.
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
To wyrażenie konwertuje wszelkie numeryczny lub ciąg na wartość typu long. Opcjonalne format dziesiętny języka Java można użyć do konwersji. 
* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``

**ToLong** Funkcja obcina float dowolnej wartości podwójnej precyzji.
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
To wyrażenie konwertuje wszelkie numeryczny lub ciąg krótszej wartości. Opcjonalne format dziesiętny języka Java można użyć do konwersji. 
* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``

**ToShort** Funkcja obcina dowolną liczbę całkowitą, long, float lub podwójny.
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
To wyrażenie konwertuje typ danych pierwotnych na ciąg. 
* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``

Można określić format liczb i dat. Jeśli format nie jest określona, używana jest domyślna systemu. Domyślny format jest `yyyy-MM-dd`. Numery wykonaj format dziesiętny języka Java. Wszystkie formaty daty możliwe można znaleźć w SimpleDateFormat języka Java. 
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Podany format znacznika czasu opcjonalne, to wyrażenie konwertuje ciąg na wartość typu date. 
* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``

Zobacz Java SimpleDateFormat dla wszystkich możliwych formatów. Jeżeli pominięto sygnaturę czasową, domyślny wzorzec `yyyy-[M]M-[d]d hh:mm:ss[.f...]` jest używany.
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
To wyrażenie konwertuje sygnaturę czasową UTC. 
* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``

Możesz przekazać opcjonalny strefy czasowej w formie `'GMT'`, `'PST'`, `'UTC'`, `'America/Cayman'`. Wartość domyślna to bieżącej strefy czasowej.
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie zastępuje jednego zestawu znaków innego zestawu znaków w ciągu. Znaki mają jeden do jednego zastąpienia.
* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
To wyrażenie usuwa ciągiem znaków początkowe i końcowe. 
* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``

Jeśli nie określisz drugi parametr funkcji przycina odstępu. W przeciwnym razie przycina dowolny znak, który określa drugi parametr.

*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
To wyrażenie zawsze zwraca `true` wartość.  
* ``isDiscounted == true()``
* ``isDiscounted() == true``

Jeśli kolumna nosiła nazwę *true*, należy użyć funkcji **syntax(true())**.
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
To wyrażenie jest zgodny z typem kolumny. 
* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``

Funkcja ta jest widoczny tylko w wyrażeniach wzorzec: liczba odpowiada krótki, liczba całkowita, long, double, float lub decimal, całkowitego odpowiada krótko, liczby całkowitej, długie, ułamkowe dopasowania double, float, decimal i typu daty lub sygnatury czasowej dopasowania daty/godziny.
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
To wyrażenie ustawia ciąg pisane wielkimi literami.
* ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera wariancję kolumny.
* ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera wariancję kolumny.
* ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera wariancję populacji kolumny.
* ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera wariancję populacji kolumny.
* ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
To wyrażenie pobiera nieobciążonej wariancję kolumny.
* ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na podstawie kryterium, to wyrażenie pobiera nieobciążonej wariancję kolumny.
* ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Danej wartości typu date, to wyrażenie pobiera tydzień roku.
* ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Wyrażenie to używa logicznej **xor** operatora. Ten operator jest taka sama jak **^** operatora.
* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
To wyrażenie danej wartości typu date, i pobiera wartość roku.
* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się, jak za pomocą wyrażenia konstruktora](concepts-data-flow-expression-builder.md).
