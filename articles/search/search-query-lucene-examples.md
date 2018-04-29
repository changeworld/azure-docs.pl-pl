---
title: Przykłady zapytań Lucene dla usługi Azure Search | Dokumentacja firmy Microsoft
description: Składnia zapytań Lucene dla Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie zwiększania termin, wyrażenie regularne wyszukiwania i wyszukiwania symboli wieloznacznych.
author: LiamCa
manager: jlembicz
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/21/2017
ms.author: liamca
ms.openlocfilehash: c83b3b0d9c0cc99ba8a76dc4a6b2f83ed6de49dc
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="lucene-query-syntax-examples-for-building-queries-in-azure-search"></a>Przykłady składni zapytań Lucene do tworzenia zapytań w usłudze Azure Search
Podczas tworzenia zapytań dla usługi wyszukiwanie Azure, można użyć albo domyślnie [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) lub alternatywnej [analizator składni zapytań Lucene w usłudze Azure Search](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search). Analizator składni zapytań Lucene obsługuje bardziej złożonych konstrukcje zapytania, takie jak zapytania należące do zakresu pola, Wyszukiwanie rozmyte wyszukiwanie w sąsiedztwie, termin zwiększania i wyrażeń regularnych wyszukiwania.

W tym artykule można przejrzeć przykłady pokazujące dostępne operacje zapytań przy użyciu pełnej składni.

## <a name="viewing-the-examples-in-jsfiddle"></a>Wyświetlanie w przykładach w JSFiddle

Wszystkie przykłady w tym artykule są wykonywalnego kwerendy działać względem wstępnie załadowane indeksu wyszukiwania w [JSFiddle](https://jsfiddle.net), Edytor online kodu do testowania skryptu, jak i HTML. 

Aby uruchomić je, kliknij prawym przyciskiem myszy kwerendę adresy URL, aby otworzyć JSFiddle w osobnym oknie przeglądarki.

> [!NOTE]
> Poniższe przykłady wykorzystać indeksu wyszukiwania, składające się z zadania dostępne w oparciu o zestawu danych przedstawionego przez [OpenData miasta Nowy Jork](https://nycopendata.socrata.com/) inicjatywy. Nie należy traktować te dane, bieżący i kompletne. Indeks znajduje się w usłudze piaskownicy obsługiwane przez firmę Microsoft. Nie trzeba subskrypcji platformy Azure lub usługi Azure Search, aby spróbować tych zapytań.
>


## <a name="how-to-invoke-full-lucene-parsing"></a>Jak można wywołać pełnej analizy Lucene

Określ wszystkie przykłady w tym artykule **kwerendami typu = pełny** parametru i wskazujący, że pełnej składni jest obsługiwane przez analizator składni zapytań Lucene wyszukiwania. 

**Przykład 1** — kliknij prawym przyciskiem myszy poniższy fragment zapytania, aby otworzyć go w nowej strony przeglądarki, która ładuje JSFiddle i uruchamia kwerendy:

* [& kwerendami typu pełnej = & Wyszukaj = *](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*)

W nowym oknie przeglądarki źródła JavaScript i HTML dane wyjściowe są prezentowane obok siebie. Skrypt odwołuje się pełne zapytanie (nie tylko fragmentu, jak pokazano w łączu). Pełne zapytanie jest wyświetlany w adresach URL, na przykład każdego. 

Ta kwerenda zwraca dokumentów z indeksu zadania nowego Jorku (nycjobs załadowane w piaskownicy usługi). Jednak zapytanie określa tylko biznesowych, które są zwracane tytułów. Pełne zapytanie w podstawowej wygląda następująco:

    http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26searchFields=business_title%26$select=business_title%26queryType=full%26search=*

**SearchFields** parametr ogranicza wyszukiwanie tylko pole tytułu biznesowych. **Kwerendami typu** ustawiono **pełne**, który powoduje, że usługi Azure Search, aby użyć analizator składni zapytań Lucene dla tego zapytania.

> [!NOTE]
> Aby uzyskać podstawowe informacje dotyczące przetwarzania zapytań, zobacz [jak pełne wyszukiwanie pełnotekstowe działa w usłudze Azure Search](search-lucene-query-architecture.md). Aby uzyskać więcej informacji o parametrach wyszukiwania, zobacz [wyszukiwania dokumentów (wyszukiwania usługi interfejsu API REST Azure)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).
>

### <a name="fielded-query-operation"></a>Operacja fielded kwerendy
Przykłady w tym artykule można zmieniać, określając **fieldname:searchterm** konstrukcji, aby zdefiniować operacji fielded zapytania, gdy pole jest pojedynczego wyrazu i wyszukiwany element jest również pojedynczy wyraz lub frazę, opcjonalnie z operatorami logicznymi. Oto kilka przykładów:

* business_title:(senior NOT junior)
* Stan: ("Warszawa" i "Nowe Jersey")

Pamiętaj umieścić wielu ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi ma zostać obliczone jako pojedyncza jednostka, jak w tym przypadku wyszukiwanie dwa różne miejscowości, w polu lokalizacji. Upewnij się również, operator Wielka Zobacz z użyciem NOT i AND.

Określone w pole **fieldname:searchterm** musi być polem można wyszukiwać. Zobacz [Create Index (wyszukiwania usługi interfejsu API REST Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index) szczegółowe informacje na temat używania atrybuty indeksu w definicji pola.

**Przykład 2** — kliknij prawym przyciskiem myszy to zapytanie szuka tytuły biznesowych z wyższych termin w nich, ale nie młodszych następujący fragment kodu zapytania:

* [& kwerendami typu pełnej = & Wyszukaj = business_title:senior nie młodszych](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:senior+NOT+junior)

## <a name="fuzzy-search-example"></a>Przykład wyszukiwanie rozmyte
Wyszukiwanie rozmyte znajduje dopasowań w kategoriach, które mają podobne konstrukcji. Na [dokumentacji Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), Wyszukiwanie rozmyte są oparte na [odległość Damerau Levenshtein](https://en.wikipedia.org/wiki/Damerau%e2%80%93Levenshtein_distance).

Aby wykonać wyszukiwanie rozmyte, Dołącz tylda "~" symbol na końcu pojedynczego wyrazu parametr opcjonalny, wartość z zakresu od 0 do 2, określającą odległość edycji. Na przykład "niebieski ~" lub "niebieski ~ 1" zwróci blue, niebieskie i łączenia.

**Przykład 3** — kliknij prawym przyciskiem myszy poniższy fragment zapytania. To zapytanie wyszukuje zadań z skojarzenia termin (gdzie on jest błędna):

* [& kwerendami typu pełnej = & Wyszukaj = business_title:asosiate ~](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:asosiate~)

> [!Note]
> Rozmytego zapytania nie są [przeanalizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis), może być zaskakująco, Jeśli przypuszczasz, danych lub Lematyzacja. Analizy leksykalne jest realizowane wyłącznie na warunkach Pełna (kwerendy termin lub frazę kwerendy). Typy zapytań z warunkami niekompletne (prefiks zapytania, wieloznaczne, zapytania wyrażenia regularnego, rozmytego zapytania) są dodawane bezpośrednio do drzewa zapytania, pomijanie etap analizy. Transformacja tylko wykonywane na warunkach niekompletne zapytania jest lowercasing.
>

## <a name="proximity-search-example"></a>Przykład wyszukiwania bliskiego zasięgu
Wyszukiwanie w sąsiedztwie są używane do wyszukiwania warunków które znajdują się obok siebie w dokumencie. Wstaw tyldy "~" symbol na końcu frazy wraz z numerem słów tworzących granicę zbliżeniowe. Na przykład "hoteli lotnisku" ~ 5 znajdzie hoteli warunki i lotniczego w ciągu 5 słów od siebie w dokumencie.

**Przykład 4** — kliknij prawym przyciskiem myszy kwerendę. Wyszukaj zadania z terminem "wyższych analityka" gdzie oddzielone co najwyżej jedno słowo w:

* [& kwerendami typu pełnej = & Wyszukaj = business_title: "wyższych analityka" ~ 1](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~1)

**Przykład 5** — Wypróbuj ponownie usuwanie wyrazów między termin "wyższych analityka".

* [& kwerendami typu pełnej = & Wyszukaj = business_title: "wyższych analityka" ~ 0](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:%22senior%20analyst%22~0)

## <a name="term-boosting-examples"></a>Termin zwiększania przykłady
Termin zwiększania odnosi się do klasyfikacji większe, jeśli zawiera on boosted termin, względem dokumentów, które nie zawierają termin dokumentu. To różni się od oceniania profile, w tym profile oceniania zwiększania niektórych pól, zamiast określonych warunków. Poniższy przykład ułatwia zilustrowanie różnice.

Należy wziąć pod uwagę profilu oceniania, która zwiększa pasuje w niektórych pól, takich jak **genre** w przykładzie musicstoreindex. Termin zwiększania może posłużyć do dalszego zwiększenia niektórych wyszukiwania wyższe niż inne warunki. Na przykład "skale ^ 2 elektronicznych" spowoduje zwiększenie dokumenty zawierające terminy wyszukiwania w **genre** pola wyższa niż innych pól z możliwością wyszukiwania w indeksie. Ponadto dokumenty zawierające wyszukiwany termin "skale" zostanie wyznaczona ranga wyższe niż inne wyszukiwany termin "elektronicznego" w wyniku wartość zwiększanie wyniku termin (2).

Aby zwiększyć termin, użyj karetki, "^", symbol współczynnik zwiększanie wyniku (numer) na końcu wyszukiwanego terminu. Im wyższa współczynnika zwiększenie wydajności, tym większe znaczenie termin będzie względem innych terminy wyszukiwania. Domyślnie współczynnik zwiększanie wyniku to 1. Mimo że współczynnik zwiększanie wyniku musi być dodatni, może być mniejszy niż 1 (na przykład 0,2).

**Przykład 6** — kliknij prawym przyciskiem myszy kwerendę. Wyszukiwanie zadań terminem "komputer analityka", gdzie widzimy nie są wyniki z komputera słów i analityka jeszcze zadania analityka znajdowały się u góry wyników.

* [& kwerendami typu pełnej = & Wyszukaj = business_title:computer analityka](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

**Przykład 7** — spróbuj go ponownie, ten czas zwiększania wyników z komputerem termin przez analityka termin, jeśli oba słowa nie istnieją.

* [& kwerendami typu pełnej = & Wyszukaj = business_title:computer ^ 2 analityka](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26$select=business_title%26queryType=full%26search=business_title:computer%5e2%20analyst)

## <a name="regular-expression-example"></a>Przykład wyrażenia regularnego
Wyszukaj wyrażenie regularne znalezienia dopasowania na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasy RegExp](http://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

**Przykład 8** — kliknij prawym przyciskiem myszy kwerendę. Wyszukiwanie zadań przy użyciu jednej termin wyższych lub inny poziom.

* `&queryType=full&$select=business_title&search=business_title:/(Sen|Jun)ior/`

W tym przykładzie adres URL nie będzie zwracał poprawnie na tej stronie. Jako obejście Skopiuj poniższy adres URL i wklej go w przeglądarce adres URL: `http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:/(Sen|Jun)ior/)`

## <a name="wildcard-search-example"></a>Przykład wyszukiwania symboli wieloznacznych
Za pomocą składni powszechnie wielu (\*) lub pojedynczego wyszukiwania symboli wieloznacznych znaku (?). Należy pamiętać, że analizator składni zapytań Lucene obsługuje korzystanie z tych symboli z pojedynczy termin, a nie frazę.

**Przykład 9** — kliknij prawym przyciskiem myszy kwerendę. Wyszukiwanie zadania zawierające prefiks programu, które zawierałoby tytułów biznesowych z warunkami programowania programisty w nim.

* [& kwerendami typu = pełnych & $select = business_title & wyszukiwania = business_title:prog*](http://fiddle.jshell.net/liamca/gkvfLe6s/1/?index=nycjobs&apikey=252044BE3886FE4A8E3BAA4F595114BB&query=api-version=2016-09-01%26queryType=full%26$select=business_title%26search=business_title:prog*)

Nie można użyć * i? symbol jako pierwszy znak wyszukiwania.

## <a name="next-steps"></a>Kolejne kroki
Spróbuj określić analizator składni zapytań Lucene w kodzie. Poniższe linki wyjaśniono, jak skonfigurować zapytania wyszukiwania dla środowiska .NET i interfejsu API REST. Łącza, użyj składni proste domyślne, należy zastosować znasz z tego artykułu, aby określić **kwerendami typu**.

* [Tworzenie zapytań względem indeksu wyszukiwania Azure przy użyciu zestawu .NET SDK](search-query-dotnet.md)
* [Tworzenie zapytań względem indeksu wyszukiwania Azure przy użyciu interfejsu API REST](search-query-rest-api.md)

## <a name="see-also"></a>Zobacz także

 [Ile wyszukiwanie pełnotekstowe działa w usłudze Azure Search](search-lucene-query-architecture.md)