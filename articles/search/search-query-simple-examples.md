---
title: Tworzenie prostego zapytania
titleSuffix: Azure Cognitive Search
description: Ucz się na przykład, uruchamiając kwerendy oparte na prostej składni wyszukiwania pełnotekstowego, wyszukiwania filtrów, wyszukiwania geograficznego, fasetowanego wyszukiwania względem indeksu usługi Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3a801af7b97954510139a009a6d1344b281cf056
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261809"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Tworzenie prostego zapytania w usłudze Azure Cognitive Search

W usłudze Azure Cognitive Search [składnia kwerendy proste](query-simple-syntax.md) wywołuje domyślny analizator kwerendy do wykonywania kwerend wyszukiwania pełnotekstowego względem indeksu. Ten analizator jest szybki i obsługuje typowe scenariusze, w tym wyszukiwanie pełnotekstowe, wyszukiwanie filtrowane i apektowane oraz wyszukiwanie geograficzne. 

W tym artykule używamy przykładów do zilustrowania prostej składni, wypełniając `search=` parametr operacji Wyszukiwanie [dokumentów.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

Alternatywną składnią kwerendy jest [Pełna lucen](query-lucene-syntax.md), obsługująca bardziej złożone struktury zapytań, takie jak wyszukiwanie rozmyte i wieloznaczne, co może zająć dodatkowy czas na przetworzenie. Aby uzyskać więcej informacji i przykładów demonstrujących pełną składnię, zobacz [Użyj pełnej składni luceny](search-query-lucene-examples.md).

## <a name="formulate-requests-in-postman"></a>Formułowanie żądań w listonoszu

Poniższe przykłady wykorzystują indeks wyszukiwania zadań NYC składający się z dostępnych zadań na podstawie zestawu danych dostarczonego przez inicjatywę [City of New York OpenData.](https://nycopendata.socrata.com/) Dane te nie powinny być uważane za aktualne lub kompletne. Indeks znajduje się w usłudze piaskownicy świadczonej przez firmę Microsoft, co oznacza, że nie potrzebujesz subskrypcji platformy Azure ani usługi Azure Cognitive Search, aby wypróbować te zapytania.

To, czego potrzebujesz, to Listonosz lub równoważne narzędzie do wystawiania żądania HTTP na GET. Aby uzyskać więcej informacji, zobacz [Szybki start: Eksploruj interfejs API REST usługi Azure Cognitive Search przy użyciu usługi Postman](search-get-started-postman.md).

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

Ciąg zapytania **`search=*`**, jest nieokreślonym wyszukiwaniem równoważnym wyszukiwaniu null lub puste. Nie jest to szczególnie przydatne, ale jest to najprostsze wyszukiwanie, które możesz zrobić.

Opcjonalnie można dodać **`$count=true`** do adresu URL, aby zwrócić liczbę dokumentów spełniających kryteria wyszukiwania. Na pusty ciąg wyszukiwania, jest to wszystkie dokumenty w indeksie (około 2800 w przypadku zadań NYC).

## <a name="how-to-invoke-simple-query-parsing"></a>Jak wywołać proste analizowanie kwerend

W przypadku zapytań interaktywnych nie trzeba niczego określać: domyślnie jest to tylkozespl. W kodzie, jeśli wcześniej wywoływano **queryType=full** dla pełnej składni kwerendy, można zresetować wartość domyślną za pomocą **queryType=simple**.

## <a name="example-1-field-scoped-query"></a>Przykład 1: Kwerenda o zakresie pola

Ten pierwszy przykład nie jest specyficzne dla analizatora, ale prowadzimy z nim do wprowadzenia pierwszej koncepcji kwerendy podstawowej: containment. W tym przykładzie zakresy wykonywania kwerendy i odpowiedzi na tylko kilka określonych pól. Wiedza o tym, jak ustrukturyzować czytelną odpowiedź JSON, jest ważna, gdy narzędziem jest Listonosz lub Eksplorator wyszukiwania. 

W przypadku zwięzłości kwerenda jest przeznaczona tylko dla pola *business_title* i określa zwracane są tylko tytuły firm. Składnia jest **searchFields** ograniczyć wykonywanie kwerendy tylko do pola business_title i **wybierz,** aby określić, które pola są uwzględnione w odpowiedzi.

### <a name="partial-query-string"></a>Częściowy ciąg zapytania

```http
searchFields=business_title&$select=business_title&search=*
```

Oto ta sama kwerenda z wieloma polami na liście rozdzielanych przecinkami.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Pełny adres URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=business_title&$select=business_title&search=*
```

Odpowiedź dla tej kwerendy powinna wyglądać podobnie do poniższego zrzutu ekranu.

  ![Odpowiedź przykładowa listonosza](media/search-query-lucene-examples/postman-sample-results.png)

Być może wynik wyszukiwania został zauważony w odpowiedzi. Jednolite wyniki 1 występują, gdy nie ma rangi, ponieważ wyszukiwanie nie było wyszukiwaniem pełnotekstowym lub nie stosowano żadnych kryteriów. W przypadku wyszukiwania wartości null bez kryteriów wiersze wracają w dowolnej kolejności. Po dodaniu rzeczywistych kryteriów wyniki wyszukiwania ewoluują w miarępłciowe wartości.

## <a name="example-2-look-up-by-id"></a>Przykład 2: Wyszukuj według identyfikatora

W tym przykładzie jest nieco nietypowe, ale podczas oceny zachowań wyszukiwania, można sprawdzić całą zawartość określonego dokumentu, aby zrozumieć, dlaczego został uwzględniony lub wykluczony z wyników. Aby zwrócić pojedynczy dokument w całości, użyj [operacji odnośnika,](https://docs.microsoft.com/rest/api/searchservice/lookup-document) aby przekazać w identyfikatorze dokumentu.

Wszystkie dokumenty mają unikatowy identyfikator. Aby wypróbować składnię kwerendy odnośnej, najpierw zwróć listę identyfikatorów dokumentów, aby można było go znaleźć. W przypadku zadań w nowym Jorku identyfikatory są przechowywane w `id` polu.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchFields=id&$select=id&search=*
```

Następnym przykładem jest kwerenda odnośnika `id` zwracająca określony dokument na podstawie "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", który pojawił się jako pierwszy w poprzedniej odpowiedzi. Następująca kwerenda zwraca cały dokument, a nie tylko wybrane pola. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2019-05-06&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Przykład 3: Filtrowanie zapytań

[Składnia filtru](https://docs.microsoft.com/azure/search/search-query-odata-filter) jest wyrażeniem OData, którego można używać z **wyszukiwaniem** lub samodzielnie. Filtr autonomiczny, bez parametru wyszukiwania, jest przydatny, gdy wyrażenie filtru jest w stanie w pełni zakwalifikować interesujące dokumenty. Bez ciągu zapytania nie ma analizy leksykalne lub językowej, nie ocenia ocenia (wszystkie wyniki są 1) i nie ma rankingu. Zwróć uwagę, że ciąg wyszukiwania jest pusty.

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Używany razem filtr jest stosowany najpierw do całego indeksu, a następnie wyszukiwanie jest wykonywane na wynikach filtru. Z tego względu filtrowanie może być przydatne, jeśli chcemy poprawić wydajność zapytań, ponieważ pozwala ono zawęzić zestaw dokumentów przetwarzany przez zapytanie wyszukiwania.

  ![Odpowiedź na kwerendę filtrującą](media/search-query-simple-examples/filtered-query.png)

Jeśli chcesz wypróbować to w Postman przy użyciu GET, można wkleić w tym ciągu:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Innym zaawansowanym sposobem łączenia **`search.ismatch*()`** filtru i wyszukiwania jest wyrażenie filtru, w którym można użyć zapytania wyszukiwania w filtrze. To wyrażenie filtru używa symbolu wieloznacznego na *planie,* aby wybrać business_title w tym plan termin, planista, planowanie i tak dalej.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Aby uzyskać więcej informacji na temat funkcji, zobacz [search.ismatch w "Przykłady filtrowania"](https://docs.microsoft.com/azure/search/search-query-odata-full-text-search-functions#examples).

## <a name="example-4-range-filters"></a>Przykład 4: Filtry zakresu

Filtrowanie zakresu jest **`$filter`** obsługiwane za pomocą wyrażeń dla dowolnego typu danych. Poniższe przykłady wyszukiwania przez pola liczbowe i ciąg. 

Typy danych są ważne w filtrach zakresu i działają najlepiej, gdy dane liczbowe znajdują się w polach liczbowych, a dane ciągów w polach ciągów. Dane liczbowe w polach ciągów nie są odpowiednie dla zakresów, ponieważ ciągi liczbowe nie są porównywalne w usłudze Azure Cognitive Search. 

Poniższe przykłady są w formacie POST dla czytelności (zakres numeryczny, a następnie zakres tekstu):

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

Można również wypróbować te w Postman za pomocą GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Fasetowanie w zakresie wartości jest typowe wymaganie aplikacji wyszukiwania. Aby uzyskać więcej informacji i przykładów dotyczących tworzenia filtrów dla struktur nawigacji aspektowej, zobacz ["Filtrowanie na podstawie zakresu" w *jak zaimplementować nawigację aspektową*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Przykład 5: Wyszukiwanie geograficzne

Przykładowy indeks zawiera pole geo_location ze współrzędnymi szerokości i długości geograficznej. W tym przykładzie użyto [funkcji geo.distance,](https://docs.microsoft.com/azure/search/search-query-odata-geo-spatial-functions#examples) która filtruje dokumenty w obwodzie punktu początkowego, na dowolną odległość (w kilometrach), którą udostępniasz. Można dostosować ostatnią wartość w kwerendzie (4), aby zmniejszyć lub powiększyć powierzchnię kwerendy.

Poniższy przykład jest w formacie POST dla czytelności:

```http
POST /indexes/nycjobs/docs/search?api-version=2019-05-06
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
Aby uzyskać bardziej czytelne wyniki, wyniki wyszukiwania są przycinane w celu uwzględnienia identyfikatora zadania, stanowiska i lokalizacji pracy. Współrzędne początkowe zostały uzyskane z losowego dokumentu w indeksie (w tym przypadku dla miejsca pracy na wyspie Staten.

Możesz również wypróbować to w Postman za pomocą GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Przykład 6: Precyzja wyszukiwania

Zapytania terminowe są pojedyncze terminy, być może wiele z nich, które są oceniane niezależnie. Zapytania fraz są ujęte w cudzysłów i oceniane jako ciąg dosłownie. Dokładność dopasowania jest kontrolowana przez operatorów i searchMode.

Przykład 1: **`&search=fire`** zwraca 150 wyników, gdzie wszystkie dopasowania zawierają słowo ogień gdzieś w dokumencie.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire
```

Przykład 2: **`&search=fire department`** zwraca wyniki z 2002 roku. Dopasowania są zwracane dla dokumentów zawierających straż pożarną lub dział.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search=fire department
```

Przykład 3: **`&search="fire department"`** zwraca 82 wyniki. Załączanie ciągu w cudzysłów jest dosłownym wyszukiwaniem w obu terminach, a dopasowania znajdują się na warunkach tokenizowanych w indeksie składającym się z połączonych terminów. To wyjaśnia, dlaczego **`search=+fire +department`** wyszukiwanie jak nie jest równoważne. Oba terminy są wymagane, ale są skanowane w celu niezależnego. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Przykład 7: Wartości logiczne z trybem wyszukiwania

Prosta składnia obsługuje operatory logiczne w`+, -, |`postaci znaków ( ). Parametr searchMode informuje o kompromisach między precyzją a odwołaniem, z `searchMode=any` faworyzowaniem odwołania (dopasowywanie `searchMode=all` do dowolnych kryteriów kwalifikuje dokument dla zestawu wyników) i faworyzowanie precyzji (wszystkie kryteria muszą być zgodne). Wartość domyślna to `searchMode=any`, co może być mylące, jeśli układasz zapytanie z wieloma operatorami i otrzymujesz szersze zamiast węższych wyników. Jest to szczególnie prawdziwe w przypadku NOT, gdzie wyniki obejmują wszystkie dokumenty "nie zawierające" określonego terminu.

Za pomocą domyślnego searchMode (dowolny), 2800 dokumenty są zwracane: te zawierające wieloczęściowy termin "straż pożarna", plus wszystkie dokumenty, które nie mają terminu "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  ![tryb wyszukiwania dowolny](media/search-query-simple-examples/searchmodeany.png)

Zmiana searchMode `all` wymusza skumulowany wpływ na kryteria i zwraca mniejszy zestaw wyników - 21 dokumentów - składający się z dokumentów zawierających całą frazę "straż pożarna", minus te zadania pod adresem Centrum Metrotech.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  ![tryb wyszukiwania wszystkie](media/search-query-simple-examples/searchmodeall.png)

## <a name="example-8-structuring-results"></a>Przykład 8: Wyniki strukturyzowania

Kilka parametrów kontroluje, które pola znajdują się w wynikach wyszukiwania, liczbę dokumentów zwróconych w każdej partii i kolejność sortowania. W tym przykładzie pojawia się kilka poprzednich przykładów, ograniczając wyniki do określonych pól przy użyciu **instrukcji $select** i kryteriów wyszukiwania dosłownego, zwracając 82 dopasowania 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
W poprzednim przykładzie można sortować według tytułu. Ten sortowanie działa, ponieważ civil_service_title można *sortować* w indeksie.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Wyniki stronicowania są implementowane przy użyciu parametru **$top,** w tym przypadku zwracając 5 najważniejszych dokumentów:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Aby uzyskać następne 5, pomiń pierwszą partię:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2019-05-06&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Następne kroki
Spróbuj określić kwerendy w kodzie. Poniższe łącza wyjaśniają sposób konfigurowania zapytań wyszukiwania dla interfejsu API .NET i REST przy użyciu domyślnej prostej składni.

* [Zapytanie o indeks przy użyciu sdk .NET](search-query-dotnet.md)
* [Zapytanie o indeks przy użyciu interfejsu API REST](search-create-index-rest-api.md)

Dodatkowe odwołanie do składni, architekturę kwerend i przykłady można znaleźć w następujących łączach:

+ [Przykłady kwerend składni lucene do tworzenia zaawansowanych zapytań](search-query-lucene-examples.md)
+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Pełne zapytanie lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Składnia filtru i kolejności](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
