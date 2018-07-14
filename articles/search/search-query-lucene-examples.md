---
title: Przykłady zapytań Lucene dla usługi Azure Search | Dokumentacja firmy Microsoft
description: Składnia zapytań Lucene dla Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, promowanie terminów, wyszukiwanie wyrażenia regularnego i wyszukiwania symboli wieloznacznych.
author: LiamCa
manager: pablocas
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: liamca
ms.openlocfilehash: 24fa427ad67a953020370a16b4d156c82a0a1cf6
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036670"
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Przykłady składni zapytań Lucene do tworzenia zapytań w usłudze Azure Search
Podczas tworzenia zapytań do usługi Azure Search, możesz użyć albo domyślnie [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) lub alternatywne [analizator składni zapytań Lucene w usłudze Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Analizator składni zapytań Lucene obsługuje bardziej złożonych konstrukcje zapytań, takich jak zapytania należące do pola, Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, promowanie terminów i wyszukiwanie wyrażenia regularnego.

W tym artykule możesz przejrzeć przykłady pokazujące dostępne operacje zapytań, korzystając z pełnej składni.

## <a name="viewing-the-examples-in-jsfiddle"></a>Wyświetlanie przykładów w JSFiddle

Wszystkie przykłady w niniejszym artykule są wykonywalnego zapytań, które uruchamiać wstępnie załadowane indeksu wyszukiwania w [JSFiddle](https://jsfiddle.net), Edytor kodu w trybie online dla testowania skryptu i HTML. 

Aby je uruchomić, kliknij prawym przyciskiem myszy zapytanie adresy URL, aby otworzyć JSFiddle w osobnym oknie przeglądarki.

> [!NOTE]
> Poniższe przykłady korzystać z indeksu wyszukiwania, składający się z dostępnych zadań na podstawie zestawu danych, dostarczone przez [miasta w Nowym Jorku OpenData](https://nycopendata.socrata.com/) inicjatywy. Te dane nie uważa się bieżących lub ukończone. Indeks znajduje się w piaskownicy usługi obsługiwane przez firmę Microsoft. Nie potrzebujesz subskrypcji platformy Azure lub usługi Azure Search, aby wypróbować te zapytania.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Jak wywołać pełnej analizy Lucene

Wszystkie przykłady w niniejszym artykule określ **queryType = full** wyszukiwania parametr, wskazujący, że pełnej składni jest obsługiwane przez analizator składni zapytań Lucene. 

**Przykład 1** — kliknij prawym przyciskiem myszy poniższy fragment zapytania, aby otworzyć go w nowej strony przeglądarki, która ładuje JSFiddle i uruchamia zapytanie:

* [& queryType = full & wyszukiwania = *](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

W nowym oknie przeglądarki źródłowe języka JavaScript i HTML dane wyjściowe są prezentowane obok siebie. Skrypt odwołuje się do pełnej kwerendy (nie tylko fragment kodu, jak pokazano na link). Pełne zapytanie jest wyświetlany w adresach URL dla każdego przykładu. 

To zapytanie zwraca dokumenty w naszym indeksie zadań New York City (nycjobs załadowane w piaskownicy usługi). Celu skrócenia programu zapytanie określa tylko biznesowych, które są zwracane tytułów. Pełne zapytanie w podstawowym jest następująca:

    https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**SearchFields** parametr ogranicza wyszukiwanie do tylko pola tytułu biznesowych. **QueryType** ustawiono **pełne**, który powoduje, że usługa Azure Search, aby użyć analizatora zapytań Lucene dla tego zapytania.

> [!NOTE]
> Aby uzyskać podstawowe informacje dotyczące przetwarzania zapytań, zobacz [jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md). Aby uzyskać więcej informacji na temat parametrów wyszukiwania, zobacz [wyszukiwania dokumentów (Azure Search Service interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Operacja fielded kwerendy
W przykładach w tym artykule można zmodyfikować, określając **fieldname:searchterm** konstrukcji, aby zdefiniować operacji fielded zapytania, gdzie pole jest tylko jedno słowo, a termin wyszukiwania jest również pojedynczego wyrazu lub frazy, opcjonalnie za pomocą Operatory logiczne. Oto kilka przykładów:

* business_title:(senior NOT junior)
* Stan: ("New York" i "Nowe Jersey")

Pamiętaj umieścić wielu ciągów w cudzysłowach, jeśli chcesz, aby oba ciągi, które ma zostać obliczone jako pojedynczą jednostkę, jak w tym przypadku wyszukiwania dla dwóch różnych miast w polu lokalizacji. Upewnij się również, operator jest wielką literą, jak widać, z użyciem NOT i AND.

Pole określone w **fieldname:searchterm** musi być polu możliwym do przeszukania. Zobacz [Create Index (Azure Search Service interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index) szczegółowe informacje na temat używania atrybuty indeksu w definicji pola.

**Przykład 2** — kliknij prawym przyciskiem myszy poniższy fragment zapytania to zapytanie szuka tytuły firm z starszy termin w nich, ale nie młodszych:

* [& queryType = full & wyszukiwania = business_title:senior nie młodszych](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Przykład wyszukiwania rozmytego
Wyszukiwanie rozmyte znajduje dopasowań w warunkach, które mają podobne konstrukcji. Na [dokumentacji Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), Wyszukiwanie rozmyte opierają się na [odległość Damerau Levenshtein](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Aby wykonać wyszukiwanie rozmyte, należy dołączyć tylda `~` symbolu na końcu pojedynczego wyrazu z opcjonalnym parametrem, wartość z zakresu od 0 do 2, określającą odległość edycji. Na przykład `blue~` lub `blue~1` zwróci niebieski, blues i przyklej.

**Przykład 3** — kliknij prawym przyciskiem myszy poniższy fragment zapytania. To zapytanie wyszukuje zadań z skojarzenia termin (gdzie go jest błędnie wpisana):

* [& queryType = full & Wyszukiwanie = business_title:asosiate ~](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Rozmyte zapytania nie są [analizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), które mogą być Zaskakujące, jeśli oczekujesz, wynikające lub Lematyzacja. Poddawać analizie leksykalnej jest realizowane wyłącznie na warunkach pełne (zapytanie termin lub frazę zapytania). Typy zapytań z warunkami niekompletne (prefiks zapytania zapytanie symboli wieloznacznych, zapytania wyrażenia regularnego, rozmyte zapytania) są dodawane bezpośrednio w drzewie zapytań, z pominięciem na etapie analizy. West jest tylko przekształcenie wykonywane na warunkach niekompletne zapytanie.
>

## <a name="proximity-search-example"></a>Przykładowe wyszukiwanie w sąsiedztwie
Wyszukiwanie w sąsiedztwie służą do znajdowania warunki znajdujących się blisko siebie w dokumencie. Wstaw tyldy "~" symbolu na końcu frazy następuje liczbę słów, które Utwórz granicę odległości między elementami. Na przykład, "port lotniczy hotel" ~ 5 znajdzie hotelu warunki i port lotniczy w ciągu 5 słów od siebie w dokumencie.

**Przykład 4** — kliknij prawym przyciskiem myszy zapytanie. Wyszukaj zadania z terminem "starszy analityka" gdzie rozdzielone nie więcej niż jedno słowo w:

* [& queryType = full & wyszukiwania = business_title: "starszy analityka" ~ 1](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Przykład 5** — Wypróbuj ją ponownie usuwanie wyrazów między termin "starszy analityka".

* [& queryType = full & wyszukiwania = business_title: "starszy analityka" ~ 0](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Termin zwiększania wyniku przykłady
Promowanie terminów odnosi się do dokumentu większe, jeśli zawiera on wzmocnione termin względem dokumentów, które nie zawierają termin klasyfikacji. To różni się od profile oceniania, w tym profile oceniania zwiększania niektóre pola, a nie konkretne terminy. Poniższy przykład pomoże zilustrować różnice.

Należy wziąć pod uwagę profil oceniania, co zwiększa przywiązanie podanej w niektóre pola, takie jak **gatunku** w przykładzie musicstoreindex. Promowanie terminów może służyć do dalszych Zwiększ niektórych wyszukiwania wyższe niż inne postanowienia. Na przykład "rock ^ 2 elektronicznych" spowoduje zwiększenie dokumentów, które zawierają terminy wyszukiwania w **gatunku** pola wyższa niż inne pola z możliwością wyszukiwania w indeksie. Ponadto dokumenty zawierające frazę "rock" zostanie wyznaczona ranga wyższe niż inne wyszukiwany termin "elektronicznego" w wyniku wartość boost termin (2).

Zwiększ termin, użyj daszek, "^", symbol ze współczynnikiem boost (liczba) na końcu okresu, w przypadku wyszukiwania. Im wyższa współczynnika zwiększenie wydajności, tym większe znaczenie termin będzie względem innych terminy wyszukiwania. Domyślnie współczynnik boost wynosi 1. Chociaż współczynnik boost musi być dodatnią, może być mniejsza niż 1 (na przykład 0.2).

**Przykład 6** — kliknij prawym przyciskiem myszy zapytanie. Zadania z terminem "komputera analityka", gdzie widać Brak wyników z komputera słów i analityka, ale analityka zadań znajdują się na początku wyników wyszukiwania.

* [& queryType = full & wyszukiwania = business_title:computer analityka](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Przykład 7** — spróbuj ponownie, ten czas zwiększenia wyników z komputerem termin przez analityka termin, jeśli oba te wyrazy nie istnieją.

* [& queryType = full & wyszukiwania = business_title:computer ^ 2 analityka](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Przykładowe wyrażenie regularne
Wyszukiwanie wyrażenia regularnego znalezienia dopasowania na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasy RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Przykład 8** — kliknij prawym przyciskiem myszy zapytanie. Wyszukaj zadania z oboma termin starszy lub inny poziom.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

Adres URL, w tym przykładzie nie będą poprawnie renderowane na stronie. Jako obejście Skopiuj poniższy adres URL i wklej go w przeglądarce adres URL: `https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Przykład wyszukiwania symboli wieloznacznych
Możesz użyć składni powszechnie wielu (\*) lub pojedynczego wyszukiwania symboli wieloznacznych znaku (?). Należy pamiętać, że analizator składni zapytań Lucene obsługuje korzystanie z tych symboli za pomocą pojedynczego terminu i nie frazę.

**Przykład 9** — kliknij prawym przyciskiem myszy zapytanie. Wyszukaj zadania zawierające prefiks programu, który zamieści tytuły firm z warunkami programowania programisty w nim.

* [& queryType = pełnej & $select = business_title & wyszukiwania = business_title:prog*](https://jsfiddle.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2017-11-11%26queryType=full%26$select=business_title%26search=business_title:prog*)

Nie można użyć * lub? symbol jako pierwszego znaku wyszukiwania.

## <a name="next-steps"></a>Kolejne kroki
Spróbuj określić analizatora zapytań Lucene w kodzie. Poniższe linki wyjaśniono, jak skonfigurować zapytania wyszukiwania dla środowiska .NET i interfejsu API REST. Łączy należy użyć składni proste domyślnego, konieczne będzie Zastosuj zdobytą wiedzę z tego artykułu, aby określić **queryType**.

* [Zapytanie indeksu usługi Azure Search przy użyciu zestawu .NET SDK](search-query-dotnet.md)
* [Zapytanie indeksu usługi Azure Search przy użyciu interfejsu API REST](search-query-rest-api.md)

## <a name="see-also"></a>Zobacz także

 [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md)
