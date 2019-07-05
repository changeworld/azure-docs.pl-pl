---
title: Przykłady zapytań przy użyciu składni "prosta" wyszukiwania — usługa Azure Search
description: Przykłady prostego zapytania wyszukiwania pełnotekstowego, filtr wyszukiwania, wyszukiwanie geograficzne, wyszukiwanie aspektowe i inne ciągi zapytań, które umożliwia tworzenie zapytań względem indeksu usługi Azure Search.
author: HeidiSteen
manager: cgronlun
tags: Simple query analyzer syntax
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 4658de97bc8b8a175934286a5be3f074968ff7bd
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485373"
---
# <a name="query-examples-using-the-simple-search-syntax-in-azure-search"></a>Przykłady zapytań przy użyciu składni "prosta" wyszukiwania w usłudze Azure Search

[Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) wywołuje analizator składni zapytań domyślne do wykonywania zapytań wyszukiwania pełnotekstowego względem indeksu usługi Azure Search. Analizator prostego zapytania jest szybkie i obsługuje typowych scenariuszy w usłudze Azure Search, takich jak wyszukiwanie pełnotekstowe, wyszukiwanie filtrowane i aspektowa i wyszukiwanie geograficzne. W tym artykule należy przejrzeć przykłady pokazujące dostępne operacje zapytań, korzystając z prostą składnię.

Składnia zapytania alternatywne jest [pełne Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), takich jak obsługa bardziej złożonych struktur, zapytania, rozmyte i wyszukiwania symboli wieloznacznych, co może zająć dodatkowy czas przetwarzania. Aby uzyskać więcej informacji i przykłady pokazujące pełną składnię, zobacz [przykłady zapytań składni Lucene](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Sformułowania żądań w narzędziu Postman

Poniższe przykłady korzystać z indeksu wyszukiwania Pokazowa składający się z dostępnych zadań na podstawie zestawu danych, dostarczone przez [miasta w Nowym Jorku OpenData](https://nycopendata.socrata.com/) inicjatywy. Te dane nie uważa się bieżących lub ukończone. Indeks znajduje się w piaskownicy usługi obsługiwane przez firmę Microsoft, co oznacza, że nie ma potrzeby subskrypcji platformy Azure lub usługi Azure Search, aby wypróbować te zapytania.

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

Ciąg zapytania **`search=*`** , odpowiada nieokreślonego wyszukiwania wyszukiwania o wartości null ani być pusta. Nie jest to szczególnie przydatne, ale jest najprostszym wyszukiwania, które można wykonać.

Opcjonalnie można dodać **`$count=true`** do adresu URL, aby zwrócić liczbę dokumentów spełniających kryteria wyszukiwania. W ciągu wyszukiwania puste to wszystkie dokumenty w indeksie (około 2800 w przypadku Pokazowa).

## <a name="how-to-invoke-simple-query-parsing"></a>Jak wywołać proste zapytanie analizy

Dla zapytań interakcyjnych, nie trzeba niczego określać: proste jest ustawieniem domyślnym. W kodzie, jeśli wcześniej wywoływany **queryType = full** składni pełnej kwerendy, można zresetować domyślnej z **queryType = proste**.

## <a name="example-1-field-scoped-query"></a>Przykład 1: Zakres pola zapytania

W pierwszym przykładzie nie jest specyficzne dla analizatora składni, ale możemy prowadzić z nim wprowadzenie pierwszego zapytanie podstawowe pojęcia: zawierania. W tym przykładzie zakresów, wykonywania zapytań i odpowiedzi na kilka konkretnych pól. Ważne jest wiedza, jak i struktury można odczytać odpowiedź w formacie JSON, po narzędzie Postman lub wyszukiwania Eksploratora. 

Celu skrócenia programu, zapytanie jest przeznaczona tylko *business_title* pola, a następnie określa tylko tytuły biznesowe są zwracane. Składnia jest **searchFields** ograniczyć wykonywanie zapytania do tylko pola business_title i **wybierz** można określić pola, które mają zostać uwzględnione w odpowiedzi.

### <a name="partial-query-string"></a>Ciąg zapytania częściowego

```http
searchFields=business_title&$select=business_title&search=*
```

W tym miejscu jest tego samego zapytania przy użyciu wielu pól na liście rozdzielonych przecinkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Pełny adres URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Odpowiedź dla tego zapytania powinien wyglądać podobnie do poniższej zrzut ekranu.

  ![Postman przykładowa odpowiedź](media/search-query-lucene-examples/postman-sample-results.png)

Być może Zauważyłeś, wynik wyszukiwania w odpowiedzi. Jednolite wyniki 1 wystąpić, gdy jest nie rangę, albo ponieważ wyszukiwanie pełnotekstowe nie jest wyszukiwanie lub ponieważ żadne kryteria nie została zastosowana. Dla wartości null wyszukiwania przy użyciu kryteriów wiersze wrócić w dowolnej kolejności. Umieszczonego rzeczywiste kryteria zobaczysz wyniki ewoluować w istotne wartości wyszukiwania.

## <a name="example-2-look-up-by-id"></a>Przykład 2: Wyszukiwanie według Identyfikatora

W tym przykładzie jest to nieco nietypowe, ale podczas obliczania wyszukiwań, możesz chcieć sprawdzić całą zawartość określonego dokumentu, aby zrozumieć, dlaczego zostało dołączone lub wykluczone z wyników. Aby zwrócić pojedynczego dokumentu w całości, użyj [operacji wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/lookup-document) podawać identyfikatora dokumentu.

Wszystkie dokumenty ma unikatowy identyfikator. Aby wypróbować składnia zapytania wyszukiwania, najpierw zwrócić listę dokumentu identyfikatory tak, aby można było znaleźć należy użyć. Pokazowa, identyfikatory są przechowywane w `id` pola.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

Następny przykład jest zapytaniem wyszukiwania zwraca określony dokument na podstawie `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", która znajdowała się pierwszy w poprzedniej odpowiedzi. Następujące zapytanie zwraca całego dokumentu, nie tylko wybranych pól. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Przykład 3: Zapytania filtru

[Składnia filtru](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) to wyrażenie OData, które można używać z **wyszukiwania** lub samodzielnie. Filtr autonomiczny bez parametru wyszukiwania jest przydatna, podczas wyrażenie filtru jest w stanie do pełnej kwalifikacji dokumentów zainteresowania. Bez ciąg zapytania nie ma żadnej analizy leksykalne lub językową nie oceniania (wszystkie wyniki są 1) i nie klasyfikacji. Należy zauważyć, że ciąg wyszukiwania jest pusty.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "select=job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Przy stosowaniu, najpierw jest stosowany filtr do całego indeksu, a następnie wyszukiwanie jest wykonywane na wynikach filtrowania. Z tego względu filtrowanie może być przydatne, jeśli chcemy poprawić wydajność zapytań, ponieważ pozwala ono zawęzić zestaw dokumentów przetwarzany przez zapytanie wyszukiwania.

  ![Odpowiedzi na zapytanie filtru](media/search-query-simple-examples/filtered-query.png)

Jeśli chcesz wypróbować w narzędziu Postman przy użyciu GET można wkleić w tym ciągu:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Jest innym wydajnym sposobem łączenia filtru i wyszukiwania **`search.ismatch*()`** w wyrażeniu filtru, w którym można korzystać z zapytania wyszukiwania w filtrze. To wyrażenie filtru używa symbolu wieloznacznego w *plan* wybrać business_title, w tym planie termin, planner, planowania i tak dalej.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Aby uzyskać więcej informacji na temat funkcji, zobacz [search.ismatch w "Przykładach filtrów"](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="example-4-range-filters"></a>Przykład 4: Filtry zakresów

Filtrowanie na podstawie zakresu jest świadczona za pośrednictwem **`$filter`** wyrażenia dla każdego typu danych. Poniższe przykłady wyszukiwanie pól liczbowych i. 

Typy danych są ważne w filtry zakresów i działają najlepiej, jeśli dane liczbowe znajduje się w pól liczbowych i dane ciągu w polach ciągu. Dane liczbowe w polach ciąg nie jest odpowiednia dla zakresów, ponieważ ciągi numeryczne są porównywalne w usłudze Azure Search. 

Poniższe przykłady są w formacie WPIS, aby zwiększyć czytelność (zakresu liczbowego, a następnie zakres tekstu):

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  ![Filtr zakresu dla zakresów liczbowych](media/search-query-simple-examples/rangefilternumeric.png)


```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  ![Filtr zakresu dla zakresów tekstu](media/search-query-simple-examples/rangefiltertext.png)

Możesz również wypróbować je w narzędziu Postman przy użyciu GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Wielopoziomowe za pośrednictwem zakresów wartości jest typowym wymogiem aplikacji wyszukiwania. Aby uzyskać więcej informacji i przykładów dotyczących tworzenia filtrów dla aspektów struktury nawigacji, zobacz ["Filtrowania na podstawie zakresu" w *jak wdrożyć nawigację aspektową*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Przykład 5: Wyszukiwanie geograficzne

Indeks przykładów zawiera pole geo_location o współrzędne geograficzne. W tym przykładzie użyto [funkcja geo.distance](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples) która filtruje na dokumentach w obrębie obwód punkt początkowy na zewnątrz do dowolnego odległość (w kilometrach), którą należy podać. Ostatnia wartość w zapytaniu (4), aby zmniejszyć lub zwiększyć prawdopodobieństwo kwerendy, można dostosować.

Poniższy przykład jest w formacie WPIS, aby zwiększyć czytelność:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Aby uzyskać bardziej czytelne wyniki wyniki wyszukiwania są usuwane identyfikator zadania, stanowisko i lokalizacja służbowa. Począwszy od współrzędne zostały uzyskane z losowe dokument w indeksie (w tym przypadku dla lokalizacji pracy na wyspie Staten.

Możesz również wypróbować to w narzędziu Postman przy użyciu GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Przykład 6: Dokładność wyszukiwania

Termin zapytania są pojedyncze terminy, może być wiele z nich, które zostaną ocenione niezależnie. Wyrażenie zapytania są ujęte w znaki cudzysłowu i jego ocenie jako ciąg verbatim. Dokładność dopasowania jest kontrolowana przez operatorów i searchMode.

Przykład 1: **`&search=fire`** zwraca wyniki 150, gdzie wszystkie dopasowania zawierać fire programu word, zawarty w dokumencie.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Przykład 2: **`&search=fire department`** zwraca wyniki, 2002. Dopasowania są zwracane dla dokumentów zawierających pożar lub działów.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Przykład 3: **`&search="fire department"`** zwraca wyniki 82. Otaczający ciąg w cudzysłowie to wyszukiwanie verbatim na oba warianty pojęć i dopasowania znajdują się na warunki tokenami w indeksie, składający się z warunków połączone. To wyjaśnia, dlaczego wyszukiwania, takie jak **`search=+fire +department`** nie odpowiada. Oba warunki są wymagane, ale są skanowane pod kątem niezależnie. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Przykład 7: Wartości logiczne z searchMode

Prosta składnia obsługuje operatory logiczne w formie znaków (`+, -, |`). Parametr searchMode informuje kompromis między dokładności i odwołania, za pomocą `searchMode=any` favoring odwołania (dopasowywania dla dowolnego kryterium kwalifikuje się dokument w zestawie wyników), i `searchMode=all` favoring precyzji (wszystkie kryteria muszą się zgadzać). Wartość domyślna to `searchMode=any`, który mylące, jeśli są układania zapytania przy użyciu wielu operatorów i wprowadzenie szersze zamiast mniejszą niż wyniki. Jest to szczególnie istotne, z użyciem NOT, w którym wyniki obejmują wszystkie dokumenty "nie zawiera" konkretny termin.

Przy użyciu domyślnego searchMode (wszystkie), są zwracane dokumenty 2800: termin zawierającymi wiele części "fire dział", a także wszystkie dokumenty, które nie mają termin "Metrotech Centrum".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![Wyszukaj wszystkie tryb](media/search-query-simple-examples/searchmodeany.png)

Zmiana searchMode do `all` wymusza skumulowany efekt kryteriami i zwraca mniejszy zestaw wyników — dokumenty 21 - składający się z dokumentów zawierających cała fraza "fire dział" minus te zadania pod adresem Metrotech Centrum.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![Tryb wyszukiwania wszystkie](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Przykład 8: Tworzenie struktury wyników

Kilka kontroli parametry, które pola są w wyszukiwaniu powoduje liczby zwracanych w każdej partii i porządek sortowania dokumentów. W tym przykładzie resurfaces kilka poprzednich przykładach ograniczania wyników określonych pól za pomocą **$select** instrukcji i kryteria wyszukiwania verbatim, zwracając 82 dopasowań 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Dołączany na poprzednim przykładzie, można sortować według tytułu. Tego rodzaju działania, ponieważ jest civil_service_title *sortowanie* w indeksie.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Stronicowanie wyników jest implementowany przy użyciu **$top** parametru, w tym przypadku zwracając dokumentów pierwsze 5:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Aby uzyskać dalej 5, Pomiń pierwszej partii:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Kolejne kroki
Spróbuj określić zapytania w kodzie. Poniższe linki wyjaśniono, jak skonfigurować zapytania wyszukiwania dla środowiska .NET i interfejsu API REST przy użyciu domyślnego prostą składnię.

* [Zapytanie indeksu usługi Azure Search przy użyciu zestawu .NET SDK](search-query-dotnet.md)
* [Zapytanie indeksu usługi Azure Search przy użyciu interfejsu API REST](search-create-index-rest-api.md)

Odwołanie do dodatkowej składni, architektura zapytania i przykłady można znaleźć w następujących łączy:

+ [Przykłady zapytań składni Lucene do tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Pełne zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Składnia filtru i Orderby](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
