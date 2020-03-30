---
title: Użyj pełnej składni kwerendy lucene
titleSuffix: Azure Cognitive Search
description: Składnia kwerendy lucene dla wyszukiwania rozmytego, wyszukiwania zbliżeniowego, zwiększania terminów, wyszukiwania wyrażeń regularnych i wyszukiwania symboli wieloznacznych w usłudze Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 745be21c2a7a09a09fdbbfd57a305d09a4fac3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793445"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Użyj "pełnej" składni wyszukiwania Lucene (zaawansowane zapytania w usłudze Azure Cognitive Search)

Podczas konstruowania kwerend dla usługi Azure Cognitive Search, można zastąpić [domyślny analizator prostych zapytań](query-simple-syntax.md) z bardziej [ekspansywny Analizator zapytania Lucene w usłudze Azure Cognitive Search](query-lucene-syntax.md) do formułowania definicji kwerend specjalistycznych i zaawansowanych. 

Analizator lucene obsługuje złożone konstrukcje zapytań, takie jak zapytania o zakresie pola, wyszukiwanie rozmyte i prefiksu z symbolami wieloznacznych, wyszukiwanie zbliżeniowe, zwiększanie terminów i wyszukiwanie wyrażeń regularnych. Dodatkowa moc jest wyposażona w dodatkowe wymagania dotyczące przetwarzania, więc należy się spodziewać nieco dłuższego czasu wykonywania. W tym artykule można przejść przez przykłady demonstrowania operacji kwerendy dostępne podczas korzystania z pełnej składni.

> [!Note]
> Wiele konstrukcji specjalistycznych zapytań włączonych za pośrednictwem pełnej składni zapytania Lucene nie jest [analizowanych tekstu,](search-lucene-query-architecture.md#stage-2-lexical-analysis)co może być zaskakujące, jeśli spodziewasz się wynikającej lub lemmatyzacji. Analiza leksykalne jest przeprowadzana tylko na pełnych terminach (kwerenda terminowa lub kwerenda frazowa). Typy zapytań z niekompletnymi terminami (kwerenda prefiksu, kwerenda wieloznaczna, zapytanie regularne, kwerenda rozmyta) są dodawane bezpośrednio do drzewa kwerend, z pominięciem etapu analizy. Jedyną transformacją wykonano na niekompletnych warunkach kwerendy jest małe przysłości. 
>

## <a name="formulate-requests-in-postman"></a>Formułowanie żądań w listonoszu

Poniższe przykłady wykorzystują indeks wyszukiwania zadań NYC składający się z dostępnych zadań na podstawie zestawu danych dostarczonego przez inicjatywę [City of New York OpenData.](https://opendata.cityofnewyork.us/) Dane te nie powinny być uważane za aktualne lub kompletne. Indeks znajduje się w usłudze piaskownicy świadczonej przez firmę Microsoft, co oznacza, że nie potrzebujesz subskrypcji platformy Azure ani usługi Azure Cognitive Search, aby wypróbować te zapytania.

To, czego potrzebujesz, to Listonosz lub równoważne narzędzie do wystawiania żądania HTTP na GET. Aby uzyskać więcej informacji, zobacz [Eksploruj z klientami REST](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Ustawianie nagłówka żądania

1. W nagłówku żądania ustaw typ `application/json` **zawartości** na .

2. Dodaj **api-key**i ustaw go na `252044BE3886FE4A8E3BAA4F595114BB`ten ciąg: . Jest to klucz zapytania dla usługi wyszukiwania piaskownicy obsługującej indeks zadań NYC.

Po określeniu nagłówka żądania można go ponownie użyć dla wszystkich zapytań w tym artykule, zamieniając tylko **ciąg wyszukiwania=.** 

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Ustawianie adresu URL żądania

Request to polecenie GET sparowane z adresem URL zawierającym punkt końcowy usługi Azure Cognitive Search i ciąg wyszukiwania.

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Skład adresu URL ma następujące elementy:

+ **`https://azs-playground.search.windows.net/`** to usługa wyszukiwania piaskownicy obsługiwana przez zespół deweloperów usługi Azure Cognitive Search. 
+ **`indexes/nycjobs/`** jest nyc jobs indeksu w kolekcji indeksów tej usługi. Zarówno nazwa usługi, jak i indeks są wymagane w żądaniu.
+ **`docs`** to kolekcja dokumentów zawierająca całą zawartość, która można przeszukiwać. Klucz interfejsu api kwerendy podany w nagłówku żądania działa tylko w operacjach odczytu dotyczących kolekcji dokumentów.
+ **`api-version=2019-05-06`** ustawia wersję api, która jest wymaganym parametrem przy każdym żądaniu.
+ **`search=*`** jest ciągiem zapytania, który w początkowej kwerendzie ma wartość null, zwracając pierwsze 50 wyników (domyślnie).

## <a name="send-your-first-query"></a>Wysyłanie pierwszego zapytania

Jako krok weryfikacji wklej następujące żądanie do GET i kliknij przycisk **Wyślij**. Wyniki są zwracane w postaci pełnych dokumentów JSON. Zwracane są całe dokumenty, co pozwala zobaczyć wszystkie pola i wszystkie wartości.

Wklej ten adres URL do klienta REST jako krok sprawdzania poprawności i wyświetlić strukturę dokumentu.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Ciąg zapytania **`search=*`**, jest nieokreślonym wyszukiwaniem równoważnym wyszukiwaniu null lub puste. To najprostsze wyszukiwanie, które możesz zrobić.

Opcjonalnie można dodać **`$count=true`** do adresu URL, aby zwrócić liczbę dokumentów spełniających kryteria wyszukiwania. Na pusty ciąg wyszukiwania, jest to wszystkie dokumenty w indeksie (około 2800 w przypadku zadań NYC).

## <a name="how-to-invoke-full-lucene-parsing"></a>Jak wywołać pełną analizowanie Lucene

Dodaj **queryType=full,** aby wywołać pełną składnię kwerendy, zastępując domyślną składnię kwerendy prostej. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Wszystkie przykłady w tym artykule określają **queryType=full** search parametr, wskazując, że pełna składnia jest obsługiwana przez analizator zapytania lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Przykład 1: Kwerenda o zakresie do listy pól

Ten pierwszy przykład nie jest specyficzne dla Lucene, ale prowadzimy z nim do wprowadzenia pierwszej koncepcji kwerendy podstawowej: zakres pola. W tym przykładzie zakresy całej kwerendy i odpowiedzi na tylko kilka określonych pól. Wiedza o tym, jak ustrukturyzować czytelną odpowiedź JSON, jest ważna, gdy narzędziem jest Listonosz lub Eksplorator wyszukiwania. 

W przypadku zwięzłości kwerenda jest przeznaczona tylko dla pola *business_title* i określa zwracane są tylko tytuły firm. Parametr **searchFields** ogranicza wykonywanie kwerendy tylko do pola business_title, a **select** określa, które pola są uwzględnione w odpowiedzi.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
&search=*&searchFields=business_title&$select=business_title
```

Oto ta sama kwerenda z wieloma polami na liście rozdzielanych przecinkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Spacje po przecinkach są opcjonalne.

> [!Tip]
> Korzystając z interfejsu API REST z kodu aplikacji, nie zapomnij `$select` o `searchFields`parametrach kodowania adresów URL, takich jak i .

### <a name="full-url"></a>Pełny adres URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Odpowiedź dla tej kwerendy powinna wyglądać podobnie do poniższego zrzutu ekranu.

  ![Odpowiedź przykładowa listonosza](media/search-query-lucene-examples/postman-sample-results.png)

Być może wynik wyszukiwania został zauważony w odpowiedzi. Jednolite wyniki 1 występują, gdy nie ma rangi, ponieważ wyszukiwanie nie było wyszukiwaniem pełnotekstowym lub nie stosowano żadnych kryteriów. W przypadku wyszukiwania wartości null bez kryteriów wiersze wracają w dowolnej kolejności. Po dodaniu rzeczywistych kryteriów wyszukiwania wyniki wyszukiwania ewoluują w miarępłciowe wartości.

## <a name="example-2-fielded-search"></a>Przykład 2: Wyszukiwanie fielded

Pełna składnia lucene obsługuje określanie zakresu poszczególnych wyrażeń wyszukiwania do określonego pola. W tym przykładzie wyszukuje tytuły firm z terminem senior w nich, ale nie junior.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
$select=business_title&search=business_title:(senior NOT junior)
```

Oto ta sama kwerenda z wieloma polami.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Pełny adres URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Odpowiedź przykładowa listonosza](media/search-query-lucene-examples/intrafieldfilter.png)

Operację wyszukiwania z polem za pomocą **fieldName:searchExpression** składni, w której wyrażenie wyszukiwania może być pojedynczym wyrazem lub frazą, lub bardziej złożonym wyrażeniem w nawiasach, opcjonalnie za pomocą operatorów logicznych. Oto kilka przykładów:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Pamiętaj, aby umieścić wiele ciągów w cudzysłowie, jeśli chcesz, aby oba ciągi były oceniane `state` jako pojedyncza jednostka, jak w tym przypadku wyszukiwanie dwóch różnych lokalizacji w polu. Upewnij się również, że operator jest kapitalizowany, jak widać z NOT i I.

Pole określone w **poluName:searchExpression** musi być polem z wyszukujalnym. Zobacz [Tworzenie indeksu (azure cognitive search REST API),](https://docs.microsoft.com/rest/api/searchservice/create-index) aby uzyskać szczegółowe informacje na temat sposobu, w jaki atrybuty indeksu są używane w definicjach pól.

> [!NOTE]
> W powyższym przykładzie nie trzeba `searchFields` używać parametru, ponieważ każda część kwerendy ma nazwę pola jawnie określoną. Jednak nadal można użyć `searchFields` parametru, jeśli chcesz uruchomić kwerendę, gdzie niektóre części są ograniczone do określonego pola, a reszta może mieć zastosowanie do kilku pól. Na `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` przykład kwerenda `senior NOT junior` będzie pasować tylko do `business_title` pola, podczas `posting_type` gdy będzie pasować do "zewnętrznego" z polem. Nazwa pola podana w **poluName:searchExpression** zawsze `searchFields` ma pierwszeństwo przed parametrem, dlatego w `business_title` tym `searchFields` przykładzie nie musimy uwzględniać w parametrze.

## <a name="example-3-fuzzy-search"></a>Przykład 3: Wyszukiwanie rozmyte

Pełna składnia Lucene obsługuje również wyszukiwanie rozmyte, dopasowując się do warunków o podobnej konstrukcji. Aby wykonać wyszukiwanie rozmyte, należy `~` dołączyć symbol tyldy na końcu pojedynczego wyrazu z opcjonalnym parametrem, wartością od 0 do 2, która określa odległość edycji. Na przykład `blue~` `blue~1` lub zwróci niebieski, niebieski i klej.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Frazy nie są obsługiwane bezpośrednio, ale można określić dopasowanie rozmyte na części składowe frazy.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Pełny adres URL

Ta kwerenda wyszukuje zadania z terminem "associate" (celowo błędnie napisane):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Rozmyta odpowiedź wyszukiwania](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Rozmyte zapytania nie są [analizowane](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy zapytań z niekompletnymi terminami (kwerenda prefiksu, kwerenda wieloznaczna, zapytanie regularne, kwerenda rozmyta) są dodawane bezpośrednio do drzewa kwerend, z pominięciem etapu analizy. Jedyną transformacją wykonano na niekompletnych warunkach kwerendy jest małe przysłości.
>

## <a name="example-4-proximity-search"></a>Przykład 4: Wyszukiwanie zbliżeniowe
Wyszukiwanie zbliżeniowe służy do znajdowania terminów, które znajdują się blisko siebie w dokumencie. Wstaw symbol tyldy "~" na końcu frazy, po której następuje liczba słów tworzących granicę bliskości. Na przykład "lotnisko hotelowe"~5 znajdzie warunki hotel i lotnisko w ciągu 5 słów od siebie w dokumencie.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Pełny adres URL

W tej kwerendzie dla zadań z terminem "starszy analityk", gdzie jest oddzielony nie więcej niż jednym słowem:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Zapytanie zbliżeniowe](media/search-query-lucene-examples/proximity-before.png)

Spróbuj ponownie usunąć słowa między terminem "starszy analityk". Należy zauważyć, że 8 dokumentów są zwracane dla tej kwerendy, w przeciwieństwie do 10 dla poprzedniej kwerendy.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Przykład 5: Zwiększenie terminów
Termin zwiększenie odnosi się do rankingu dokumentu wyżej, jeśli zawiera wzmocniony termin, w stosunku do dokumentów, które nie zawierają terminu. Aby zwiększyć termin, użyj daszka "^", symbol z współczynnikiem wzmocnienia (liczbą) na końcu wyszukiwanego terminu. 

### <a name="full-urls"></a>Pełne adresy URL

W tym "przed" zapytanie, szukaj pracy z terminem *analityk komputerowy* i zauważyć, że nie ma wyników zarówno z *komputerem* słowa i *analityka,* ale zadania *komputera* są na szczycie wyników.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Termin zwiększenie przed](media/search-query-lucene-examples/termboostingbefore.png)

W zapytaniu "po" powtórz wyszukiwanie, tym razem zwiększając wyniki z *terminem analityk* nad terminem *komputer,* jeśli oba słowa nie istnieją. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Bardziej czytelną dla człowieka wersją `search=business_title:computer analyst^2`powyższej kwerendy jest . Dla wykonalne zapytanie, `^2` jest `%5E2`zakodowany jako , co jest trudniejsze do zobaczenia.

  ![Okres zwiększenie po](media/search-query-lucene-examples/termboostingafter.png)

Zwiększanie terminów różni się od profili punktacji tym, że profile punktacji zwiększają niektóre pola, a nie konkretne terminy. Poniższy przykład pomaga zilustrować różnice.

Należy wziąć pod uwagę profil punktacji, który zwiększa dopasowania w określonym polu, takich jak **gatunek** w przykładzie musicstoreindex. Zwiększenie terminów może być wykorzystane do dalszego zwiększenia niektórych wyszukiwanych terminów wyższych niż inne. Na przykład "rock^2 electronic" zwiększy dokumenty zawierające wyszukiwane terminy w polu **gatunku** wyższe niż inne pola z wyszukujalne w indeksie. Ponadto dokumenty zawierające wyszukiwane hasło "rock" będą klasyfikowane wyżej niż inne wyszukiwane hasło "elektroniczne" w wyniku terminu wartość dodana (2).

Przy ustalaniu poziomu współczynnika, im wyższy współczynnik wzmocnienia, tym bardziej odpowiedni termin będzie w stosunku do innych wyszukiwanych haseł. Domyślnie współczynnik wzmocnienia wynosi 1. Chociaż współczynnik wzmocnienia musi być dodatni, może być mniejszy niż 1 (na przykład 0,2).


## <a name="example-6-regex"></a>Przykład 6: Regex

Wyszukiwanie wyrażeń regularnych znajduje dopasowanie na podstawie zawartości między ukośnikami "/", zgodnie z dokumentami w [klasie RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Pełny adres URL

W tym pytaniu wyszukaj oferty pracy `search=business_title:/(Sen|Jun)ior/`z terminem Senior lub Junior: .

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Zapytanie programu Regex](media/search-query-lucene-examples/regex.png)

> [!Note]
> Kwerendy regex nie są [analizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Jedyną transformacją wykonano na niekompletnych warunkach kwerendy jest małe przysłości.
>

## <a name="example-7-wildcard-search"></a>Przykład 7: Wyszukiwanie symboli wieloznacznych
Można użyć ogólnie rozpoznaną składni\*dla wielu ( ) lub pojedynczych (?) symboli wieloznacznych. Należy zauważyć, że analizator zapytania Lucene obsługuje użycie tych symboli z jednym terminem, a nie frazą.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Pełny adres URL

W tej kwerendzie wyszukaj zadania zawierające prefiks "prog", który zawiera tytuły firm z terminami programowania i programisty w nim. Nie można użyć * lub ? jako pierwszy znak wyszukiwania.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Kwerenda wieloznaczna](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Kwerendy wieloznaczne nie są [analizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). Jedyną transformacją wykonano na niekompletnych warunkach kwerendy jest małe przysłości.
>

## <a name="next-steps"></a>Następne kroki
Spróbuj określić analizator zapytania Lucene w kodzie. Poniższe łącza wyjaśniają, jak skonfigurować zapytania wyszukiwania dla interfejsu API .NET i REST. Łącza używają domyślnej prostej składni, więc należy zastosować to, czego nauczyłeś się z tego artykułu, aby określić **typ kwerendy**.

* [Zapytanie o indeks przy użyciu sdk .NET](search-query-dotnet.md)
* [Zapytanie o indeks przy użyciu interfejsu API REST](search-create-index-rest-api.md)

Dodatkowe odwołanie do składni, architekturę kwerend i przykłady można znaleźć w następujących łączach:

+ [Przykłady kwerend składni prostych](search-query-simple-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Pełna składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)