---
title: Przykłady zapytań Lucene — usługa Azure Search
description: Składnia zapytań Lucene dla Wyszukiwanie rozmyte, wyszukiwanie w sąsiedztwie, promowanie terminów, wyszukiwanie wyrażenia regularnego i wyszukiwania symboli wieloznacznych w usłudze Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Lucene query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: affe9084c488984747c4bafca5b8e9536cd6dba8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485418"
---
# <a name="query-examples-using-full-lucene-search-syntax-advanced-queries-in-azure-search"></a>Przykłady zapytań przy użyciu składni wyszukiwania w usłudze "pełnej" Lucene (zaawansowanych zapytań w usłudze Azure Search)

Podczas tworzenia zapytań do usługi Azure Search, możesz zastąpić domyślną [prosty analizator zapytań](query-simple-syntax.md) z udogodniona [analizator składni zapytań Lucene w usłudze Azure Search](query-lucene-syntax.md) sformułowanie specjalistyczne i zaawansowane zapytania definicje. 

Analizator Lucene obsługuje konstrukcje złożonego zapytania, np. zapytania należące do pola, rozmyte i wyszukiwanie symbol wieloznaczny prefiksu, wyszukiwanie w sąsiedztwie, promowanie terminów i wyszukiwanie wyrażenia regularnego. Dodatkową moc jest dostarczany z wymagania dodatkowe przetwarzanie, dzięki czemu możesz spodziewać się nieco więcej czasu wykonywania. W tym artykule możesz przejrzeć przykłady pokazujące dostępne operacje zapytań, korzystając z pełnej składni.

> [!Note]
> Wiele konstrukcje specjalne zapytania włączane przy użyciu pełnej składni zapytań Lucene nie są [przeanalizowany tekst](search-lucene-query-architecture.md#stage-2-lexical-analysis), które mogą być Zaskakujące, jeśli oczekujesz, wynikające lub Lematyzacja. Poddawać analizie leksykalnej jest realizowane wyłącznie na warunkach pełne (zapytanie termin lub frazę zapytania). Typy zapytań z warunkami niekompletne (prefiks zapytania zapytanie symboli wieloznacznych, zapytania wyrażenia regularnego, rozmyte zapytania) są dodawane bezpośrednio w drzewie zapytań, z pominięciem na etapie analizy. West jest tylko przekształcenie wykonywane na warunkach niekompletne zapytanie. 
>

## <a name="formulate-requests-in-postman"></a>Sformułowania żądań w narzędziu Postman

Poniższe przykłady korzystać z indeksu wyszukiwania Pokazowa składający się z dostępnych zadań na podstawie zestawu danych, dostarczone przez [miasta w Nowym Jorku OpenData](https://opendata.cityofnewyork.us/) inicjatywy. Te dane nie uważa się bieżących lub ukończone. Indeks znajduje się w piaskownicy usługi obsługiwane przez firmę Microsoft, co oznacza, że nie ma potrzeby subskrypcji platformy Azure lub usługi Azure Search, aby wypróbować te zapytania.

Konieczne jest Postman lub równoważne narzędzie do wystawiania żądań HTTP na GET. Aby uzyskać więcej informacji, zobacz [Eksploruj za pomocą klientów REST](search-get-started-postman.md).

### <a name="set-the-request-header"></a>Ustaw nagłówek żądania

1. W nagłówku żądania ustaw **Content-Type** do `application/json`.

2. Dodaj **klucz interfejsu api**i ustaw go na następujący ciąg: `252044BE3886FE4A8E3BAA4F595114BB`. Jest to klucz zapytania usługi wyszukiwania piaskownicy hostingu indeksu Pokazowa.

Po określeniu nagłówek żądania, można użyć ponownie go dla wszystkich zapytań w tym artykule wymienić tylko **wyszukiwania =** ciągu. 

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-header.png)

### <a name="set-the-request-url"></a>Ustaw adres URL żądania

Żądanie jest sparowana z adres URL usługi Azure Search punktu końcowego i wyszukaj ciąg zawierający polecenie GET.

  ![Nagłówek żądania narzędzia Postman](media/search-query-lucene-examples/postman-basic-url-request-elements.png)

Kompozycja adresu URL zawiera następujące elementy:

+ **`https://azs-playground.search.windows.net/`** jest to usługa wyszukiwania w piaskownicy jest obsługiwana przez zespół usługi Azure Search. 
+ **`indexes/nycjobs/`** jest indeksem Pokazowa w kolekcji indeksów tej usługi. Nazwa usługi i indeksu są wymagane dla żądania.
+ **`docs`** to kolekcji documents zawierający całą zawartość można wyszukiwać. Klucz interfejsu api zapytań podany w nagłówku żądania działa tylko na operacje odczytu, wybieranie kolekcji dokumentów.
+ **`api-version=2019-05-06`** Ustawia wartość api-version, czyli wymaganego parametru na każde żądanie.
+ **`search=*`** jest ciągiem zapytania, które początkowego zapytania ma wartość null, zwraca 50 pierwszych wyników (domyślnie).

## <a name="send-your-first-query"></a>Wyślij pierwszego zapytania

Jako kroku weryfikacji, wklej następujące żądanie GET, a następnie kliknij przycisk **wysyłania**. Wyniki są zwracane w postaci pełnych dokumentów JSON. Całe dokumenty zostaną zwrócone, co pozwala wyświetlić wszystkie pola i wszystkie wartości.

Wklej ten adres URL do klienta REST, jako kroku weryfikacji i wyświetlić strukturę dokumentu.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=*
  ```

Ciąg zapytania **`search=*`** , odpowiada nieokreślonego wyszukiwania wyszukiwania o wartości null ani być pusta. To najprostsza wyszukiwania, które można wykonać.

Opcjonalnie można dodać **`$count=true`** do adresu URL, aby zwrócić liczbę dokumentów spełniających kryteria wyszukiwania. W ciągu wyszukiwania puste to wszystkie dokumenty w indeksie (około 2800 w przypadku Pokazowa).

## <a name="how-to-invoke-full-lucene-parsing"></a>Jak wywołać pełnej analizy Lucene

Dodaj **queryType = full** do wywołania składni pełne zapytanie w języku, Zastępowanie domyślnego prosta składnia zapytań. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&search=*
```

Wszystkie przykłady w niniejszym artykule określ **queryType = full** wyszukiwania parametr, wskazujący, że pełnej składni jest obsługiwane przez analizator składni zapytań Lucene. 

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Przykład 1: Zapytanie zakresu do listy pól

W pierwszym przykładzie nie jest specyficzne dla Lucene, ale możemy prowadzić z nim wprowadzenie pojęcia pierwsze zapytanie podstawowe: pole zakresu. W tym przykładzie zakres całego zapytania i odpowiedzi na kilka konkretnych pól. Ważne jest wiedza, jak i struktury można odczytać odpowiedź w formacie JSON, po narzędzie Postman lub wyszukiwania Eksploratora. 

Celu skrócenia programu, zapytanie jest przeznaczona tylko *business_title* pola, a następnie określa tylko tytuły biznesowe są zwracane. **SearchFields** parametr ogranicza wykonywanie zapytania do tylko pola business_title i **wybierz** określa pola, które znajdują się w odpowiedzi.

### <a name="partial-query-string"></a>Ciąg zapytania częściowego

```http
&search=*&searchFields=business_title&$select=business_title
```

W tym miejscu jest tego samego zapytania przy użyciu wielu pól na liście rozdzielonych przecinkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

Miejsca do magazynowania po przecinkach są opcjonalne.

> [!Tip]
> Korzystając z interfejsu API REST, od kodu aplikacji, nie zapomnij kodowanie adresu URL parametrów, takich jak `$select` i `searchFields`.

### <a name="full-url"></a>Pełny adres URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&search=*&searchFields=business_title&$select=business_title
```

Odpowiedź dla tego zapytania powinien wyglądać podobnie do poniższej zrzut ekranu.

  ![Postman przykładowa odpowiedź](media/search-query-lucene-examples/postman-sample-results.png)

Być może Zauważyłeś, wynik wyszukiwania w odpowiedzi. Jednolite wyniki 1 wystąpić, gdy jest nie rangę, albo ponieważ wyszukiwanie pełnotekstowe nie jest wyszukiwanie lub ponieważ żadne kryteria nie została zastosowana. Dla wartości null wyszukiwania przy użyciu kryteriów wiersze wrócić w dowolnej kolejności. Umieszczonego kryteria wyszukiwania rzeczywiste zobaczysz wyniki ewoluować w istotne wartości wyszukiwania.

## <a name="example-2-fielded-search"></a>Przykład 2: Fielded wyszukiwania

Pełnej składni Lucene obsługuje zakresu wyrażeń poszczególnych wyszukiwania z określonym polem. W tym przykładzie wyszukuje tytuły firm z starszy termin w nich, ale nie młodszych.

### <a name="partial-query-string"></a>Ciąg zapytania częściowego

```http
$select=business_title&search=business_title:(senior NOT junior)
```

W tym miejscu jest tego samego zapytania przy użyciu wielu pól.

```http
$select=business_title, posting_type&search=business_title:(senior NOT junior) AND posting_type:external
```

### <a name="full-url"></a>Pełny adres URL

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&$select=business_title&search=business_title:(senior NOT junior)
```

  ![Postman przykładowa odpowiedź](media/search-query-lucene-examples/intrafieldfilter.png)

Można zdefiniować operacji wyszukiwania fielded z **fieldName:searchExpression** składnię, w którym wyrażenie wyszukiwania może być pojedynczego wyrazu lub frazy lub bardziej złożone wyrażenie w nawiasach, opcjonalnie wraz z operatorami logicznymi. Oto kilka przykładów:

- `business_title:(senior NOT junior)`
- `state:("New York" OR "New Jersey")`
- `business_title:(senior NOT junior) AND posting_type:external`

Pamiętaj umieścić wielu ciągów w cudzysłowach, jeśli chcesz, aby mogło zostać ocenione jako pojedynczą jednostkę, jak w tym przypadku przeszukiwanie dla dwóch różnych lokalizacjach w obu ciągów `state` pola. Upewnij się również, operator jest wielką literą, jak widać, z użyciem NOT i AND.

Pole określone w **fieldName:searchExpression** musi być polu możliwym do przeszukania. Zobacz [Create Index (Azure Search Service interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/create-index) szczegółowe informacje na temat używania atrybuty indeksu w definicji pola.

> [!NOTE]
> W powyższym przykładzie firma Microsoft nie musiał użyć `searchFields` parametru ponieważ każda część zapytania ma nazwę pola jawnie określony. Jednak nadal można użyć `searchFields` parametru, jeśli chcesz uruchomić zapytanie, gdzie niektóre elementy są ograniczone do określonego pola, a pozostałe można zastosować do kilku pól. Na przykład, zapytanie `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` będzie pasował `senior NOT junior` wyłącznie `business_title` pola, gdy byłby on zgodny z "external" `posting_type` pola. Nazwa pola w **fieldName:searchExpression** zawsze pierwszeństwo przed `searchFields` parametr, który jest, dlaczego w tym przykładzie, nie musimy uwzględnić `business_title` w `searchFields` parametru.

## <a name="example-3-fuzzy-search"></a>Przykład 3: Wyszukiwanie rozmyte

Pełna składnia Lucene obsługuje również Wyszukiwanie rozmyte dopasowywanie na zasadach, które mają podobne konstrukcji. Aby wykonać wyszukiwanie rozmyte, należy dołączyć tylda `~` symbolu na końcu pojedynczego wyrazu z opcjonalnym parametrem, wartość z zakresu od 0 do 2, określającą odległość edycji. Na przykład `blue~` lub `blue~1` zwróci niebieski, blues i przyklej.

### <a name="partial-query-string"></a>Ciąg zapytania częściowego

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~
```

Wyrażenia nie są obsługiwane bezpośrednio, ale można określić dopasowywania rozmytego w części zamiennych frazę.

```http
searchFields=business_title&$select=business_title&search=business_title:asosiate~ AND comm~ 
```


### <a name="full-url"></a>Pełny adres URL

To zapytanie wyszukuje zadania za pomocą termin "Skojarz" (celowo błędna):

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:asosiate~
```
  ![Wyszukiwanie rozmyte odpowiedzi](media/search-query-lucene-examples/fuzzysearch.png)


> [!Note]
> Rozmyte zapytania nie są [analizowane](search-lucene-query-architecture.md#stage-2-lexical-analysis). Typy zapytań z warunkami niekompletne (prefiks zapytania zapytanie symboli wieloznacznych, zapytania wyrażenia regularnego, rozmyte zapytania) są dodawane bezpośrednio w drzewie zapytań, z pominięciem na etapie analizy. West jest tylko przekształcenie wykonywane na warunkach niekompletne zapytanie.
>

## <a name="example-4-proximity-search"></a>Przykład 4: Wyszukiwanie w sąsiedztwie
Wyszukiwanie w sąsiedztwie służą do znajdowania warunki znajdujących się blisko siebie w dokumencie. Wstaw tyldy "~" symbolu na końcu frazy następuje liczbę słów, które Utwórz granicę odległości między elementami. Na przykład, "port lotniczy hotel" ~ 5 znajdzie hotelu warunki i port lotniczy w ciągu 5 słów od siebie w dokumencie.

### <a name="partial-query-string"></a>Ciąg zapytania częściowego

```http
searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```

### <a name="full-url"></a>Pełny adres URL

W tym zapytaniu dla zadań z terminem "analityka starszy" gdzie rozdzielone nie więcej niż jeden wyraz:

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~1
```
  ![Zapytanie odległości między elementami](media/search-query-lucene-examples/proximity-before.png)

Wypróbuj ją ponownie usuwanie wyrazów między termin "starszy analityka". Należy zauważyć, że dokumenty 8 są zwracane dla tego zapytania, w przeciwieństwie do 10 poprzednie zapytanie.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:%22senior%20analyst%22~0
```

## <a name="example-5-term-boosting"></a>Przykład 5: promowanie
Promowanie terminów odnosi się do dokumentu większe, jeśli zawiera on wzmocnione termin względem dokumentów, które nie zawierają termin klasyfikacji. Zwiększ termin, użyj daszek, "^", symbol ze współczynnikiem boost (liczba) na końcu okresu, w przypadku wyszukiwania. 

### <a name="full-urls"></a>Pełne adresy URL

W tym "przed" zapytania wyszukiwania zadań z terminem *analityka komputera* i zwróć uwagę, Brak wyników z obu wyrazów *komputera* i *analityka*, jeszcze  *komputer* zadań znajdują się na początku wyników.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst
```
  ![Promowanie przed](media/search-query-lucene-examples/termboostingbefore.png)

W zapytaniu "po", powtórz wyszukiwanie, tym razem zwiększania wyniku wyniki z terminem *analityka* przez okres *komputera* oba te wyrazy nie istnieją. 

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:computer%20analyst%5e2
```
Bardziej ludzki można odczytać wersji powyższym zapytaniu jest `search=business_title:computer analyst^2`. Dla zapytania wymagającego `^2` zakodowanymi w formacie `%5E2`, które jest trudniejsze, aby zobaczyć.

  ![Promowanie po](media/search-query-lucene-examples/termboostingafter.png)

Promowanie terminów różni się od profile oceniania, w tym profile oceniania zwiększania niektóre pola, a nie konkretne terminy. Poniższy przykład pomoże zilustrować różnice.

Należy wziąć pod uwagę profil oceniania, co zwiększa przywiązanie podanej w niektóre pola, takie jak **gatunku** w przykładzie musicstoreindex. Promowanie terminów może służyć do dalszych Zwiększ niektórych wyszukiwania wyższe niż inne postanowienia. Na przykład "rock ^ 2 elektronicznych" spowoduje zwiększenie dokumentów, które zawierają terminy wyszukiwania w **gatunku** pola wyższa niż inne pola z możliwością wyszukiwania w indeksie. Ponadto dokumenty zawierające frazę "rock" zostanie wyznaczona ranga wyższe niż inne wyszukiwany termin "elektronicznego" w wyniku wartość boost termin (2).

Po ustawienie poziomie współczynnik wyższa współczynnika zwiększenie wydajności, tym większe znaczenie termin będzie względem innych terminy wyszukiwania. Domyślnie współczynnik boost wynosi 1. Chociaż współczynnik boost musi być dodatnią, może być mniejsza niż 1 (na przykład 0.2).


## <a name="example-6-regex"></a>Przykład 6: Regex

Wyszukiwanie wyrażenia regularnego znalezienia dopasowania na podstawie zawartości między ukośnikami "/", zgodnie z opisem w [klasy RegExp](https://lucene.apache.org/core/4_10_2/core/org/apache/lucene/util/automaton/RegExp.html).

### <a name="partial-query-string"></a>Ciąg zapytania częściowego

```http
searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

### <a name="full-url"></a>Pełny adres URL

W tym zapytaniu wyszukiwania zadań z termin starszy lub inny poziom: `search=business_title:/(Sen|Jun)ior/`.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:/(Sen|Jun)ior/
```

  ![Wyrażenie regularne zapytania](media/search-query-lucene-examples/regex.png)

> [!Note]
> Wyrażenie regularne zapytania nie są [analizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). West jest tylko przekształcenie wykonywane na warunkach niekompletne zapytanie.
>

## <a name="example-7-wildcard-search"></a>Przykład 7: Wyszukiwanie symboli wieloznacznych
Możesz użyć składni powszechnie wielu (\*) lub pojedynczego wyszukiwania symboli wieloznacznych znaku (?). Należy pamiętać, że analizator składni zapytań Lucene obsługuje korzystanie z tych symboli za pomocą pojedynczego terminu i nie frazę.

### <a name="partial-query-string"></a>Ciąg zapytania częściowego

```http
searchFields=business_title&$select=business_title&search=business_title:prog*
```

### <a name="full-url"></a>Pełny adres URL

W tym zapytaniu wyszukiwania zadań, które zawierają prefiks programu, który zamieści tytuły firm z warunkami programowania programisty w nim. Nie można użyć * lub? symbol jako pierwszego znaku wyszukiwania.

```GET
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&queryType=full&$count=true&searchFields=business_title&$select=business_title&search=business_title:prog*
```
  ![Symbol wieloznaczny zapytania](media/search-query-lucene-examples/wildcard.png)

> [!Note]
> Symbol wieloznaczny zapytania nie są [analizowane](https://docs.microsoft.com/azure/search/search-lucene-query-architecture#stage-2-lexical-analysis). West jest tylko przekształcenie wykonywane na warunkach niekompletne zapytanie.
>

## <a name="next-steps"></a>Kolejne kroki
Spróbuj określić analizatora zapytań Lucene w kodzie. Poniższe linki wyjaśniono, jak skonfigurować zapytania wyszukiwania dla środowiska .NET i interfejsu API REST. Łączy należy użyć składni proste domyślnego, konieczne będzie Zastosuj zdobytą wiedzę z tego artykułu, aby określić **queryType**.

* [Zapytanie indeksu usługi Azure Search przy użyciu zestawu .NET SDK](search-query-dotnet.md)
* [Zapytanie indeksu usługi Azure Search przy użyciu interfejsu API REST](search-create-index-rest-api.md)

Odwołanie do dodatkowej składni, architektura zapytania i przykłady można znaleźć w następujących łączy:

+ [Przykłady zapytań składni prosty](search-query-simple-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Pełna składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)