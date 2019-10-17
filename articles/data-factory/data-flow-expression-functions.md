---
title: Funkcje wyrażeń w funkcji przepływu danych mapowania Azure Data Factory
description: Dowiedz się więcej o funkcjach wyrażeń w mapowaniu przepływu danych.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 27d968aa5202fbeb38be9a2416514d2185c1d8b9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72436739"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Wyrażenia transformacji danych w mapowaniu przepływu danych 



## <a name="expression-functions"></a>Funkcje wyrażeń

W Data Factory Użyj języka wyrażeń funkcji mapowanie przepływu danych, aby skonfigurować przekształcenia danych.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Wartość bezwzględna liczby.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Oblicza wartość cosinusa odwrotnego * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Dodaje parę ciągów lub cyfr. Dodaje datę do kilku dni. Dodaje czas trwania do sygnatury czasowej. Dołącza jedną tablicę podobnego typu do innej. Analogicznie jak operator + * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Dodaj dni do daty lub sygnatury czasowej. Analogicznie jak operator + dla daty * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2<br/><br/>
Dodaj miesiące do daty lub sygnatury czasowej. Opcjonalnie można przekazać strefę czasową * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Operator logiczny i. Analogicznie jak & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Oblicza odwrotną wartość sinusa * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza odwrotną wartość styczną * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Zwraca kąt w radianach między dodatnią osią x płaszczyzny i punktem podanym przez współrzędne * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera średnią wartości w kolumnie * ``avg(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera średnią wartości w kolumnie * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Wybiera wartość kolumny według nazwy w strumieniu. Opcjonalną nazwę strumienia można przekazać jako drugi argument. Jeśli istnieje wiele dopasowań, zwracane jest pierwsze dopasowanie. W przypadku braku dopasowania zwraca wartość NULL. Zwracana wartość musi być typu konwertowana przez jedną z funkcji konwersji typu (TO_DATE, TO_STRING...).  Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawienia parametrów * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Wybiera wartość kolumny według pozycji względnej (w oparciu o 1) w strumieniu. Jeśli pozycja jest poza zakresem, zwraca wartość NULL. Zwracana wartość musi być typu konwertowana przez jedną z funkcji konwersji typu (TO_DATE, TO_STRING...) Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawiania parametrów * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Na podstawie zmiennych warunków stosuje jedną wartość lub drugą. Jeśli liczba danych wejściowych jest parzysta, druga jest domyślnie zerowa dla ostatniego warunku * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Oblicz katalog główny modułu z liczbą * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
Zwraca najmniejszą liczbę całkowitą nie mniejszą od liczby * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2<br/><br/>
Zwraca pierwszą wartość spoza wartości null z zestawu danych wejściowych. Wszystkie dane wejściowe powinny być tego samego typu * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Porównuje dwie wartości tego samego typu. Zwraca ujemną liczbę całkowitą, jeśli wartość1 < wartość2, 0 Jeśli Wartość1 = = wartość2, wartość dodatnia, jeśli wartość1 > wartość2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Łączy jednocześnie zmienną liczbę ciągów. Analogicznie jak operator + z ciągami * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Łączy zmienną liczbę ciągów wraz z separatorem. Pierwszy parametr jest separatorem * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zwraca wartość true, jeśli dowolny element w podanej tablicy ma wartość true w podanym predykacie. Zawiera oczekiwane odwołanie do jednego elementu w funkcji predykatu jako #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Oblicza wartość cosinusa * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza cosinus hiperboliczny wartości * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera zagregowaną liczbę wartości. Jeśli kolumny opcjonalne są określone, ignoruje wartości NULL w liczniku * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Pobiera zagregowaną liczbę różnych wartości zestawu kolumn * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera zagregowaną liczbę wartości. Jeśli opcjonalna kolumna jest określona, ignoruje wartości NULL w liczniku * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera kowariancję populacji między dwiema kolumnami * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera kowariancję populacji dwóch kolumn * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera kowariancję przykładową dwóch kolumn * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera kowariancję przykładową dwóch kolumn * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza wartość skrótu CRC32 zestawu kolumn o różnych typach danych pierwotnych o długości bitowej, która może zawierać tylko wartości 0 (256), 224, 256, 384, 512. Można go użyć do obliczenia odcisku palca wiersza * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
Funkcja CumeDist oblicza pozycję wartości względem wszystkich wartości w partycji. Wynik jest liczbą wierszy poprzedzających lub równych bieżącemu wierszowi w kolejności partycji podzieloną przez łączną liczbę wierszy w partycji okna. Wszystkie wartości równe w kolejności będą oceniane do tego samego położenia.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Pobiera bieżącą datę, kiedy to zadanie zostanie uruchomione. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Pobiera bieżącą sygnaturę czasową, gdy zadanie zaczyna działać z lokalną strefą czasową * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera bieżącą sygnaturę czasową jako UTC. Jeśli bieżący czas ma być interpretowany w innej strefie czasowej niż strefa czasowa klastra, można przekazać opcjonalną strefę czasu w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Domyślnie jest to Bieżąca strefa czasowa. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. Użyj [SimpleDateFormat](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html) do przekonwertowania czasu UTC na inną strefę czasową, użyj fromUTC ().
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Pobiera dzień miesiąca z określoną datą * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera dzień tygodnia, w którym nadana została dana data. 1 — niedziela, 2-poniedziałek..., 7-Sobota * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera dzień roku z uwzględnieniem daty * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Czas trwania w milisekundach dla liczby dni * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
Konwertuje radiany na stopnie * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza rangę wartości w grupie wartości. Wynik to jeden i liczba wierszy poprzedzających lub równych bieżącemu wierszowi w kolejności partycji. Wartości nie będą powodować przerw w sekwencji. Długoterminowa ranga działa nawet wtedy, gdy dane nie są sortowane i wyszukują zmiany w wartościach * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Dzieli parę liczb. Analogicznie jak operator/* ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy ciąg jest zakończony przy użyciu podanego ciągu * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Porównanie operatora równości. Analogicznie jak = = operator * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Operator porównania jest ignorowany wielkości liter. Analogicznie jak < = >, * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Oblicz silnię liczby * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
Zawsze zwraca wartość false. Użyj składni funkcji (false ()), jeśli istnieje kolumna o nazwie "false" * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Filtruje elementy z tablicy, które nie spełniają podanego predykatu. Filtr oczekuje odwołania do jednego elementu w funkcji predykatu jako #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera pierwszą wartość grupy kolumn. Jeśli drugi parametr ignoreNulls zostanie pominięty, przyjmuje się wartość false * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zwraca największą liczbę całkowitą nie większą niż liczba * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2<br/><br/>
Konwertuje na sygnaturę czasową z czasu UTC. Opcjonalnie można przekazać strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Jest to domyślne ustawienie timezoneRefer języka Java SimpleDateFormat dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Operator porównywania porównania. Analogicznie jak > operator * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Porównanie z operatorem większym niż lub równym. Analogicznie jak > = operator * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zwraca największą wartość z listy wartości jako dane wejściowe pomijające wartości null. Zwraca wartość null, jeśli wszystkie dane wejściowe mają wartość null * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Sprawdza wartość kolumny według nazwy w strumieniu. Opcjonalną nazwę strumienia można przekazać jako drugi argument.  Nazwy kolumn znane w czasie projektowania powinny być rozkierowane tylko według ich nazwy. Obliczane dane wejściowe nie są obsługiwane, ale można użyć podstawień parametrów * ``hasColumn('parent')`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera wartość godziny dla sygnatury czasowej. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Czas trwania w milisekundach dla liczby godzin * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
Mapuje każdy element tablicy do nowego elementu przy użyciu podanego wyrażenia. Mapa oczekuje odwołania do jednego elementu w funkcji wyrażenia jako #item i odwołania do indeksu elementu jako #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2<br/><br/>
W oparciu o warunek stosuje jedną wartość lub drugą. Jeśli inne nie określono, jest uznawane za NULL. Obie wartości muszą być zgodne (numeryczne, ciąg...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Sprawdza, czy wartość nie jest RÓWNa NULL, i zwraca alternatywę. Sprawdza wszystkie dane wejściowe do momentu znalezienia pierwszej wartości innej niż null * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Sprawdza, czy element znajduje się w tablicy * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Konwertuje pierwszą literę każdego wyrazu na wielkie litery. Wyrazy są identyfikowane jako oddzielone odstępami * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2<br/><br/>
Znajduje pozycję (1) podciągu w ciągu. Zwraca wartość 0, jeśli nie znaleziono * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Sprawdza, czy wiersz jest oznaczony do usunięcia. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Wartość domyślna indeksu strumienia to 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy wiersz jest oznaczony jako błąd. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Wartość domyślna indeksu strumienia to 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy wiersz jest oznaczony jako ignorowany. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Wartość domyślna indeksu strumienia to 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy wiersz jest oznaczony do wstawienia. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Wartość domyślna indeksu strumienia to 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy wiersz jest dopasowany podczas wyszukiwania. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Wartość domyślna indeksu strumienia to 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy wartość jest RÓWNa NULL * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy wiersz jest oznaczony do aktualizacji. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Wartość domyślna indeksu strumienia to 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy wiersz jest oznaczony do wstawienia. W przypadku transformacji z więcej niż jednym strumieniem wejściowym można przekazać indeks (od 1 do) strumienia. Wartość domyślna indeksu strumienia to 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera wartość kurtoza kolumny * ``kurtosis(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów Pobiera wartość kurtoza kolumny * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera wartość pierwszego parametru oceniane n wierszy przed bieżącym wierszem. Drugi parametr to liczba wierszy do wyszukania, a wartość domyślna to 1. Jeśli nie ma tylu wierszy, zwracana jest wartość null, chyba że zostanie określona wartość domyślna * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera ostatnią wartość grupy kolumn. Jeśli drugi parametr ignoreNulls zostanie pominięty, przyjmuje się wartość false * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera ostatnią datę miesiąca uwzględniającą datę * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera wartość pierwszego parametru oceniane n wierszy po bieżącym wierszu. Drugi parametr to liczba wierszy do przeszukania, a wartość domyślna to 1. Jeśli nie ma tylu wierszy, zwracana jest wartość null, chyba że zostanie określona wartość domyślna * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Porównanie z operatorem mniejszym niż lub równym. Analogicznie jak < = operator * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Wyodrębnij podciąg, rozpoczynając od indeksu 1 o liczbie znaków. Analogicznie jak podciąg (str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zwraca długość ciągu * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2<br/><br/>
Operator minus porównania. Analogicznie jak < operator * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Porównanie z operatorem mniejszym niż lub równym. Analogicznie jak < = operator * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera odległość Levenshtein między dwoma ciągami * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2<br/><br/>
Wzorzec jest ciągiem, który jest dopasowany dosłownie. Wyjątki są następujące specjalne symbole: _ dopasowuje dowolny znak w danych wejściowych (podobny do. w wyrażeniach regularnych POSIX)% dopasowuje zero lub więcej znaków w danych wejściowych (podobnie jak. * w wyrażeniach regularnych POSIX).
Znak ucieczki to "". Jeśli znak ucieczki poprzedza symbol specjalny lub inny znak ucieczki, poniższy znak jest dopasowany dosłownie. Nie można użyć żadnego znaku ucieczki.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Znajduje pozycję (1) podciągu w ciągu, rozpoczynając od określonej pozycji. Jeśli pozycja zostanie pominięta, jest uznawana za od początku ciągu. Zwraca wartość 0, jeśli nie znaleziono * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Oblicza wartość dziennika. Opcjonalną wartość bazową można podać w innym przypadku, jeśli jest używana * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza wartość dziennika na podstawie 10 podstawowych * ``log10(100) -> 2`` @ no__t-1 @ no__t-2<br/><br/>
Małe litery ciągu * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
Po lewej stronie jest to ciąg, który został dostarczony przez podane uzupełnienie, dopóki nie ma określonej długości. Jeśli ciąg jest równy lub większy niż długość, zostaje on przycięty do długości * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 "" LPAD ("Dumbo", 8, "< >")-> "< > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
Lewy przycina ciąg znaków wiodących. Jeśli drugi parametr jest nieokreślony, przycina odstępy. W przeciwnym razie przycinanie dowolnego znaku określonego w drugim parametrze * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Mapuje każdy element tablicy do nowego elementu przy użyciu podanego wyrażenia. Mapa oczekuje odwołania do jednego elementu w funkcji wyrażenia jako #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera maksymalną wartość kolumny * ``max(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów Pobiera maksymalną wartość kolumny * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza skrót MD5 zestawu kolumn o różnych typach danych pierwotnych i zwraca ciąg szesnastkowy o 32 znaku. Można go użyć do obliczenia odcisku palca wiersza * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera średnią wartości w kolumnie. Analogicznie jak średnia * ``mean(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera znaczenie wartości w kolumnie. Analogicznie jak avgIf * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera wartość milisekundy daty. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871`` @ no__t-2 @ no__t-3<br/><br/>
Czas trwania w milisekundach dla liczby milisekund * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera minimalną wartość kolumny * ``min(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera minimalną wartość kolumny * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Odejmuje liczby. Odejmij od daty i liczby dni. Substract czas trwania z sygnatury czasowej. Substract dwa sygnatury czasowe, aby uzyskać różnicę w milisekundach. Analogicznie jak operator * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Pobiera wartość minuty sygnatury czasowej. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Czas trwania w milisekundach dla liczby minut * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
Moduł par numerów. Analogicznie jak operator% * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera wartość miesiąca daty lub sygnatury czasowej * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera liczbę miesięcy między dwiema datami. Obliczenia można zaokrąglić. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677`` @ no__t-2 @ no__t-3<br/><br/>
Mnoży parę liczb. Analogicznie jak operator * * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Funkcja NTile dzieli wiersze dla każdej partycji okna na przedziały `n` z zakresu od 1 do maksymalnie `n`. Wartości zasobnika różnią się o co najwyżej 1. Jeśli liczba wierszy w partycji nie jest równo podzielona na liczbę przedziałów, pozostałe wartości są dystrybuowane jeden na przedział, rozpoczynając od pierwszego przedziału. Funkcja NTile jest przydatna do obliczania wartości tertiles, kwartyls, deciles i innych typowych statystyk podsumowujących. Funkcja oblicza dwie zmienne podczas inicjalizacji: rozmiar regularnego zasobnika będzie mieć jeden dodatkowy wiersz. Obie zmienne są zależne od rozmiaru bieżącej partycji. Podczas obliczeń funkcja śledzi bieżący numer wiersza, bieżący zasobnik i numer wiersza, w którym zostanie zmieniony zasobnik (bucketThreshold). Gdy bieżąca liczba wierszy osiągnie próg zasobnika, wartość przedziału jest zwiększana o jeden, a próg jest zwiększany o rozmiar zasobnika (plus jeden dodatkowy, jeśli bieżący zasobnik jest uzupełniony).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negacja liczby. Włącza liczbę dodatnią na wartość ujemną i odwrotnie * ``negate(13) -> -13`` @ no__t-1 @ no__t-2<br/><br/>
Zwraca następną unikatową sekwencję. Numer występuje tylko po jednej partycji i jest poprzedzony prefiksem * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2<br/><br/>
Normalizowanie wartości ciągu w celu oddzielenia znaków akcentu Unicode * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2<br/><br/>
Operator logiczny negacji * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Porównanie operatora not Equals. Analogicznie jak operator! = * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zwraca wartość NULL. Użyj składni funkcji (null ()), jeśli istnieje kolumna o nazwie "null". Każda operacja, która używa, spowoduje, że wartość NULL * ``isNull('dumbo' + null) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Operator logiczny OR. Analogicznie jak | | * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Dodatnia para par liczb.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Zwraca bieżący identyfikator partycji. wiersz wejściowy jest w * ``partitionId()`` @ no__t-1 @ no__t-2<br/><br/>
Podnosi jedną liczbę do potęgi innego * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza rangę wartości w grupie wartości. Wynik to jeden i liczba wierszy poprzedzających lub równych bieżącemu wierszowi w kolejności partycji. Wartości będą generować przerwy w sekwencji. Ranga działa nawet wtedy, gdy dane nie są sortowane i wyszukują zmiany w wartościach * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Gromadzi elementy w tablicy. Funkcja ograniczania oczekuje odwołania do akumulowana i jednego elementu w pierwszej funkcji wyrażenia jako #acc i #item i oczekuje, że wartość wyniku #result do użycia w drugiej funkcji wyrażenia * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Wyodrębnij pasujący podciąg dla danego wzorca wyrażenia regularnego. Ostatni parametr identyfikuje grupę dopasowania i domyślnie ma wartość 1 w przypadku pominięcia. Użyj "<regex>" (cudzysłów tylny), aby dopasować ciąg bez ucieczki * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Sprawdza, czy ciąg jest zgodny z danym wzorcem wyrażenia regularnego. Użyj "<regex>" (cudzysłów tylny), aby dopasować ciąg bez ucieczki * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zamień wszystkie wystąpienia wzorca wyrażenia regularnego na inny podciąg w danym ciągu Użyj "<regex>" (cudzysłów tylny), aby dopasować ciąg bez ucieczki * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Dzieli ciąg na podstawie ogranicznika na podstawie wyrażenia regularnego i zwraca tablicę ciągów * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Zamień wszystkie wystąpienia podciągu na inny podciąg w podanym ciągu. Jeśli ostatni parametr zostanie pominięty, jest on domyślnie pustym ciągiem * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Odwraca ciąg * ``reverse('gunchus') -> 'suhcnug'`` @ no__t-1 @ no__t-2<br/><br/>
Wyodrębnia podciąg z liczbą znaków z prawej strony. Analogicznie jak podciąg (str, LENGTH (str)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Sprawdza, czy ciąg pasuje do danego wzorca wyrażenia regularnego * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zaokrągla liczbę w postaci opcjonalnej skali i opcjonalnego trybu zaokrąglania. W przypadku pominięcia skali wartość domyślna to 0.  Jeśli tryb zostanie pominięty, zostanie przyjmowana wartość domyślna ROUND_HALF_UP (5). Wartości zaokrąglania obejmują 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Przypisuje Sekwencyjne numerowanie wierszy w oknie, rozpoczynając od 1 * ``rowNumber()`` @ no__t-1 @ no__t-2<br/><br/>
Prawe konsole do ciągu przez dostarczone uzupełnienie, dopóki nie ma określonej długości. Jeśli ciąg jest równy lub większy niż długość, zostanie on przycięty do długości * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
Prawy przycina ciąg znaków wiodących. Jeśli drugi parametr jest nieokreślony, przycina odstępy. W przeciwnym razie przycinanie dowolnego znaku określonego w drugim parametrze * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera drugą wartość daty. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Lokalna strefa czasowa jest używana domyślnie. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59`` @ no__t-2 @ no__t-3<br/><br/>
Czas trwania w milisekundach dla liczby sekund * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza skrót SHA-1 zestawu kolumn o różnych typach danych pierwotnych i zwraca ciąg szesnastkowy znaku 40. Można go użyć do obliczenia odcisku palca wiersza * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza skrót SHA-2 zestawu kolumn o różnych typach danych pierwotnych o długości bitowej, która może zawierać tylko wartości 0 (256), 224, 256, 384, 512. Można go użyć do obliczenia odcisku palca wiersza * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza wartość sinusa * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza wartość sinus hiperboliczny * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera skośność kolumny * ``skewness(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera skośność kolumny * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Wyodrębnia podzbiór tablicy z położenia. Pozycja ma 1 na podstawie. Jeśli długość zostanie pominięta, domyślnie zostanie zakończona ciąg * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Sortuje tablicę przy użyciu podanej funkcji predykatu. Funkcja Sort oczekuje odwołania do dwóch kolejnych elementów w funkcji Expression jako #item1 i #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Pobiera kod SOUNDEX dla ciągu * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
Dzieli ciąg na podstawie ogranicznika i zwraca tablicę ciągów * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
Oblicza pierwiastek kwadratowy z liczby * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2<br/><br/>
Sprawdza, czy ciąg rozpoczyna się od podanego ciągu * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera odchylenie standardowe kolumny * ``stdDev(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera odchylenie standardowe kolumny * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera odchylenie standardowe populacji w kolumnie * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera odchylenie standardowe populacji kolumny * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera przykładowe odchylenie standardowe kolumny * ``stddevSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera przykładowe odchylenie standardowe kolumny * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Odejmij miesiące od daty lub sygnatury czasowej. Analogicznie jak operator dla daty * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2<br/><br/>
Odejmij miesiące od daty lub sygnatury czasowej * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
Wyodrębnia podciąg o określonej długości z położenia. Pozycja ma 1 na podstawie. Jeśli długość zostanie pominięta, domyślnie zostanie zakończona ciąg * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Pobiera zagregowaną sumę kolumny liczbowej * ``sum(col)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera zagregowaną sumę różnych wartości w kolumnie liczbowej * ``sumDistinct(col)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera zagregowaną sumę kolumny liczbowej. Warunek może być oparty na dowolnej kolumnie * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Na podstawie kryteriów pobiera zagregowaną sumę kolumny liczbowej. Warunek może być oparty na dowolnej kolumnie * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Oblicza wartość styczną * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Oblicza wartość tangensa hiperbolicznego * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2<br/><br/>
Konwertuje wszystkie wartości typu liczbowego/daty/sygnatury czasowej na reprezentację binarną * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2<br/><br/>
Konwertuje wartość ('t ', 'true ', ' y ', ' yes ', ' 1 ') na wartość true i ("f", "false", "n", "No", "0") na wartość false i NULL dla dowolnej innej wartości * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konwertuje wejściowy ciąg daty na datę przy użyciu opcjonalnego formatu daty wejściowej. Odwołaj się do SimpleDateFormat języka Java, aby uzyskać dostęp do dostępnych formatów. Jeśli format daty wejścia zostanie pominięty, domyślny format to rrrr-[M] M-[d] d. Akceptowane formaty: [rrrr, rrrr-[M] M, rrrr-[M] M-[d] d, rrrr-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Konwertuje wszystkie wartości liczbowe lub ciągi na wartość dziesiętną. Jeśli precyzja i skala nie są określone, wartość domyślna to (10, 2). Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Opcjonalny format ustawień regionalnych w formie języka BCP47, na przykład en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Konwertuje wszystkie wartości liczbowe lub ciągi na wartość typu Double. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Opcjonalny format ustawień regionalnych w formie języka BCP47, na przykład en-US, de, zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Konwertuje wszystkie wartości liczbowe lub ciągi na wartość zmiennoprzecinkową. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina wszystkie podwójne * ``toFloat(123.45) -> 123.45f`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konwertuje wartość liczbową lub ciąg na liczbę całkowitą. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina wszystkie długie, zmiennoprzecinkowe, podwójne * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konwertuje dowolną liczbę lub ciąg na wartość długą. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina wszystkie zmiennoprzecinkowe * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konwertuje dowolną liczbę lub ciąg na krótką wartość. Do konwersji można użyć opcjonalnego formatu dziesiętnego języka Java. Obcina liczbę całkowitą, Long, float, Double * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Konwertuje pierwotny typ danych na ciąg. Dla liczb i dat można określić format. W przypadku nieokreślony system jest wybierany domyślnie. Format dziesiętny Java jest używany dla liczb. Zapoznaj się z SimpleDateFormat języka Java dla wszystkich możliwych formatów daty; domyślny format to RRRR-MM-DD * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Konwertuje ciąg na sygnaturę czasową, używając opcjonalnego formatu sygnatury czasowej. Wszystkie możliwe formaty można znaleźć w SimpleDateFormat języka Java. Jeśli znacznik czasu zostanie pominięty, domyślny wzorzec. rrrr-[M] M-[d] d hh: mm: SS [. f...]. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Sygnatura czasowa obsługuje maksymalnie milisekund dokładności z wartością 999Refer języka Java SimpleDateFormat dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Konwertuje sygnaturę czasową na czas UTC. Można przekazać opcjonalną strefę czasową w postaci "GMT", "PST", "UTC", "Ameryka/Kajmany". Jest to domyślne ustawienie timezoneRefer języka Java SimpleDateFormat dla dostępnych formatów. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zastąp jeden zestaw znaków innym zestawem znaków w ciągu. Znaki mają od 1 do 1 zastępowanie * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Przycina ciąg wiodących i końcowych znaków. Jeśli drugi parametr jest nieokreślony, przycina odstępy. W przeciwnym razie przycinanie dowolnego znaku określonego w drugim parametrze * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zawsze zwraca wartość true. Użyj składni funkcji (true ()), jeśli istnieje kolumna o nazwie "true" * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Dopasowuje typ kolumny. Mogą być używane tylko w wyrażeniach wzorców. liczba jest zgodna z wartością Short, Integer, Long, Double, float lub decimal, całk dopasowuje Short, Integer, Long, ułamkowe dopasowanie Double, float, Decimal i DateTime dopasowuje typ daty lub sygnatury czasowej * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Wielkie litery ciągu * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
Zwraca wygenerowany identyfikator UUID * ``uuid()`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera wariancję kolumny * ``variance(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera wariancję kolumny * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera wariancję populacji kolumny * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera wariancję populacji kolumny * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera wariancję nieobciążonej kolumny * ``varianceSample(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na podstawie kryteriów pobiera wariancję nieobciążonej kolumny * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Pobiera tydzień roku z uwzględnieniem daty * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2<br/><br/>
Czas trwania w milisekundach dla liczby tygodni * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
Logiczny operator XOR. Analogicznie jak ^ operator * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Pobiera wartość year dla daty * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Następne kroki

[Dowiedz się, jak używać programu Expression Builder](concepts-data-flow-expression-builder.md).
