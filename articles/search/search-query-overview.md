---
title: Zapytanie, typy i kompozycji w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Podstawy do tworzenia zapytań wyszukiwania w usłudze Azure Search przy użyciu parametrów, aby zastosować filtr, wybierz i sortować wyniki.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 098718293cda1699fb07e09fa81af94a95bbdeca
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715162"
---
# <a name="query-types-and-composition-in-azure-search"></a>Typy zapytań i składu w usłudze Azure Search

Tworzenia zapytania w usłudze Azure Search jest pełną specyfikację żądania: pasuje do kryteriów oraz parametrów kierowanie wykonywania zapytań i kształtowania odpowiedzi. Żądanie określa pola, które można uwzględnić, które pola, aby powrócić do sortowania lub filtrowania i tak dalej. Nie określono tego parametru, uruchomieniu zapytania względem wszystkie pola z możliwością wyszukiwania jako operacji wyszukiwania pełnotekstowego, zwracając wynik nie została ona oceniona zestawu w dowolnej kolejności.

## <a name="introduction-by-example"></a>Wprowadzenie według przykładu

Przykładami są przydatne w przypadku ilustrujący kluczowe założenia. Poniższy przykład formułować za pomocą [interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents), informuje żądania i odpowiedzi. W usłudze Azure Search wykonanie zapytania jest zawsze względem jednego indeksu, uwierzytelniony przy użyciu klucza api-key podany w żądaniu. 

```
{  
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"", 
    "searchFields": "description, city",  
    "count": "true", 
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "listingId"
 } 
```
Reprezentatywny zapytania w tym przykładzie przedstawiono kilka istotnych aspektów definicji zapytania, z analizatora dane wejściowe, kształtowanie zestawu wyników. Wykonanie zapytania jest zawsze względem jednego indeksu, uwierzytelniony przy użyciu klucza api-key podany w żądaniu. 

Aby uruchomić to zapytanie, należy użyć [wyszukiwania Eksploratora i indeks pokaz nieruchomości](search-get-started-portal.md). W pasku wyszukiwania programu explorer można wkleić tego ciągu zapytania: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&orderby=listingId`

**Przeszukiwanie indeksu**

+ Analizator składni zapytań jest rozwiązaniem, należy ustawić za pomocą `queryType`. Większość programistów używa domyślnie [prosty analizator](search-query-simple-examples.md) w celu wyszukiwania pełnotekstowego, ale [pełne Lucene](search-query-lucene-examples.md) analizy jest wymagana dla zapytania specjalne form, na przykład wyszukiwanie rozmyte i wyrażeń regularnych.
+ Kryteria dopasowania dla dokumentów w indeksie jest ustawiony za pomocą `search` parametru. Wyszukiwanie może być Niezdefiniowany, podobnie jak w `search=*`, lecz więcej prawdopodobnie zawiera warunki, wyrażenia i operatory podobne wyświetlanych w przykładzie.
+ Zakres może być całego indeksu lub określonych pól, jak pokazano na `searchFields`.

**Tworzenie struktury odpowiedzi**

Inne parametry w przykładzie odnoszą się do wyników zapytania:

+ `count` Liczba dokumentów, jest zgodnych z zapytaniem.
+ `select` ogranicza pola zwrócona w odpowiedzi.
+ `top` limity wierszy lub dokumenty zwrócone w odpowiedzi. Wartością domyślną jest 50; przykład zmniejsza, do 10.
+ `orderby` Sortuje wyniki według pola.

**Włączanie operacji za pomocą atrybutów indeksu**

Projekt indeksu i zapytania projektu są ściśle powiązane w usłudze Azure Search. Chociaż nie jest tutaj widoczny, punkt krytyczny wiedzieć na początku jest *schemat indeksu*, za pomocą atrybutów w każdym polu, określa rodzaj zapytania można tworzyć. Atrybuty indeksu w polu określają dozwoloną operacji —, czy pole jest *wyszukiwanie* w indeksie, *pobieranie* w wynikach, *sortowanie*, * można filtrować*, i tak dalej. W tym przykładzie `"orderby": "listingId"` tylko wtedy, gdy pole listingId jest oznaczone jako *sortowanie* w schemacie indeksu. Aby uzyskać więcej informacji na temat atrybutów indeksu zobacz [utworzyć indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index).

Dozwolone operacje na poszczególnych pól są tylko jednym ze sposobów, czy definicja indeksu informuje wykonywania zapytania. Inne możliwości w indeksie, obejmują:

+ [Synonimy](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations)
+ [Analiza (lingwistyczna) tekst](https://docs.microsoft.com//rest/api/searchservice/language-support) i [analizy niestandardowe](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)
+ [Konstrukcje sugestora](https://docs.microsoft.com/rest/api/searchservice/suggesters) umożliwiające Autouzupełnianie i automatycznie sugestii
+ [Profile oceniania](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) , dodać logikę do klasyfikacji wyników wyszukiwania

Powyższe możliwości są wykonywane podczas wykonywania zapytania, ale zazwyczaj są implementowane w kodzie, jako atrybuty pola, a nie jako parametry zapytania.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Typy zapytań: wyszukiwania i filtrowania

W tym przykładzie wprowadzające parametru wyszukiwania została zidentyfikowana jako środki, które wyszukiwania kryteria są przekazywane do aparatu. W praktyce, istnieją dwa główne rodzaje zapytania: `search` i `filter`. 

+ `search` zapytania skanowania w poszukiwaniu dowolnej liczby terminów we wszystkich *wyszukiwanie* pola w indeksie i działa w sposób, jakiego oczekuje się aparat wyszukiwania, takich jak Google czy Bing. Przykłady użycia wprowadzenie `search` parametru.

+ `filter` zapytania oceny wyrażenia logicznego wszystkich *filtrowanie* pól w indeksie. W odróżnieniu od `search`, `filter` dokładna zawartość pola, w tym uwzględnianie wielkości liter w polach ciąg pasuje do zapytania.

Można użyć wyszukiwania i filtrowania, razem lub oddzielnie. Filtr autonomiczny bez ciąg zapytania jest przydatna, podczas wyrażenie filtru jest w stanie do pełnej kwalifikacji dokumentów zainteresowania. Bez ciąg zapytania ma żadnej analizy leksykalne lub językową, nie oceniania i nie klasyfikacji. Należy zauważyć, że ciąg wyszukiwania jest pusty.

```
POST /indexes/nycjobs/docs/search?api-version=2017-11-11  
    {  
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "count": "true"
    }
```

Przy stosowaniu, najpierw jest stosowany filtr do całego indeksu, a następnie wyszukiwanie jest wykonywane na wynikach filtrowania. Z tego względu filtrowanie może być przydatne, jeśli chcemy poprawić wydajność zapytań, ponieważ pozwala ono zawęzić zestaw dokumentów przetwarzany przez zapytanie wyszukiwania.

Elementy składni wyrażeń filtrów należą do podzbioru [języka filtru OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). Dla kwerend wyszukiwania, można użyć dowolnego [uproszczoną składnię](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) lub [składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search) zostały podane poniżej.


## <a name="choose-a-syntax-simple-or-full"></a>Wybierz składni: prostych lub pełnych

Usługa Azure Search znajduje się na górze Apache Lucene i zapewnia wybór między dwa parsery zapytania dotyczące obsługi typowych, wyspecjalizowana zapytania. Typowym wyszukiwaniu żądania są formułowane przy użyciu domyślnego [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search). Ta składnia obsługuje szereg typowych operatorów wyszukiwania, na przykład AND, OR, NOT, frazy, sufiks i pierwszeństwo operatorów.

[Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), włączone po dodaniu `queryType=full` na żądanie udostępnia powszechnie przyjętym i ekspresyjny język zapytań opracowany jako część [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Przy użyciu tej składni pozwala wyspecjalizowane zapytania:

+ [Zapytania należące do pola](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fields)
+ [Wyszukiwanie rozmyte](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_fuzzy)
+ [Wyszukiwanie w sąsiedztwie](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_proximity)
+ [promowanie](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_termboost)
+ [wyszukiwanie wyrażenia regularnego](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_regex)
+ [Wyszukiwanie symboli wieloznacznych](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_wildcard)

Operatory logiczne są prawie takie same w obu składni w dodatkowych formatach w pełnej Lucene:

+ [Operatory logiczne prostej składni](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search#operators-in-simple-search)
+ [Operatory logiczne w pełnej składni Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_boolean)

## <a name="required-and-optional-elements"></a>Elementy wymagane i opcjonalne

Zapytania są zawsze kierowane na jeden indeks. Nie można przyłączyć indeksów, lub utworzyć struktury danych niestandardowych lub tymczasowej jako element docelowy kwerendy. 

W ramach przesyłania żądań wyszukiwania do usługi Azure Search dostępnych jest szereg parametrów, które można określić, aby użyć ich razem z rzeczywistymi słowami wpisywanymi w polu wyszukiwania w aplikacji. Te parametry zapytań zapewniają lepszą kontrolę nad [wyszukiwaniem pełnotekstowym](search-lucene-query-architecture.md).

Wymagane elementy na żądania zapytania obejmują następujące składniki:

+ Usługa punktu końcowego i indeks kolekcji dokumentów wyrażone jako adres URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ Wersja interfejsu API REST (tylko), wyrażone jako `api-version`
+ zapytania lub administratorem klucz api-key, wyrażone jako `api-key`
+ wyrażone jako ciąg zapytania `search`, które mogą być nieokreślony, jeśli chcesz wykonać to puste wyszukiwanie. Możesz również wysłać tylko wyrażenia filtru jako `$filter`.
+ `queryType`, prostej lub pełnej, który można pominąć, jeśli chcesz użyć domyślnej prostą składnię.

Wszystkie inne parametry wyszukiwania są opcjonalne.

## <a name="manage-search-results"></a>Zarządzanie wynikami wyszukiwania 

Wyniki zapytania są przesyłane strumieniowo jako dokumenty JSON w interfejsie API REST, mimo, że użycie interfejsów API programu .NET serializacji jest wbudowany. Wyniki można przekształcić przez ustawienie parametrów dla kwerendy, wybranie określonych pól dla wyniku

Parametry zapytania może służyć do struktury zestawu wyników w następujący sposób:

+ Ograniczanie lub adapterów przetwarzania wsadowego liczbę dokumentów w wynikach (50 domyślnie)
+ Wybieranie pola do uwzględnienia w wynikach
+ Ustawienie kolejności sortowania
+ Dodawanie trafień zaznaczenie, aby zwrócić uwagę czytelnika na pasujące terminy w treści wyników wyszukiwania

### <a name="tips-for-unexpected-results"></a>Porady dotyczące nieoczekiwane wyniki

Od czasu do czasu substancji i nie struktury wyników jest nieoczekiwany. Gdy niż oczekiwana zobaczyć wyniki zapytania, możesz spróbować tych modyfikacji kwerendy, aby sprawdzić, czy poprawić wyniki:

+ Zmiana `searchMode=any` (domyślna), aby `searchMode=all` będą musieli dopasowanie wszystkie kryteria, a nie jakąkolwiek kryteriów. Jest to szczególnie istotne, gdy operatory logiczne są uwzględnione zapytania.

+ Zmień technika zapytania, jeśli tekst lub poddawać analizie leksykalnej, jest to konieczne, ale typ zapytania wyklucza językowej przetwarzania. Podczas wyszukiwania pełnotekstowego tekst lub poddawać analizie leksykalnej automatycznie koryguje błędy pisowni, formularze programu word w liczbie pojedynczej niż liczby mnogiej i nawet nieregularne zlecenia lub rzeczowniki. Dla niektórych kwerend, takich jak rozmyte lub wyszukiwania symboli wieloznacznych, analiza tekstu nie jest częścią zapytania przetwarzania potoku. W niektórych scenariuszach wyrażeń regularnych zostały użyte jako obejście tego problemu. 

### <a name="paging-results"></a>Stronicowanie wyników
Usługa Azure Search ułatwia implementowanie stronicowania wyników wyszukiwania. Przy użyciu parametrów `top` i `skip` można sprawnie wysyłać żądania wyszukiwania, które umożliwiają uzyskanie całego zbioru wyników w postaci zarządzanych, uporządkowanych podzbiorów, co pozwala łatwo stosować dobre praktyki dotyczące obsługi interfejsu użytkownika wyszukiwania. Razem z mniejszymi podzbiorami wyników można również odbierać liczbę dokumentów w całym zbiorze wyników wyszukiwania.

Więcej informacji o stronicowaniu wyników wyszukiwania można znaleźć w artykule [How to page search results in Azure Search](search-pagination-page-layout.md) (Sposoby stronicowania wyników wyszukiwania w usłudze Azure Search).

### <a name="ordering-results"></a>Porządkowanie wyników
Usługa Azure Search udostępnia możliwość przekazania wyników zapytania wyszukiwania uporządkowanych według wartości w określonym polu. Domyślnie usługa Azure Search porządkuje wyniki w oparciu o rangę wyniku wyszukiwania poszczególnych dokumentów, który jest określany na podstawie wagi [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Jeśli chcesz, aby usługa Azure Search zwracała wyniki uporządkowane według wartości innej niż wynik wyszukiwania, możesz użyć parametru wyszukiwania `orderby`. Można określić wartość parametru `orderby`, dołączając nazwy pól i wywołania funkcji [`geo.distance()`](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search) w przypadku wartości geoprzestrzennych. Po każdym wyrażeniu można umieścić element `asc` w celu wskazania, że wyniki mają być sortowane w kolejności rosnącej, lub element `desc`, jeśli wyniki mają być sortowane w kolejności malejącej. Domyślnie jest stosowana kolejność rosnąca.


### <a name="hit-highlighting"></a>Wyróżnianie trafień
Dostępne w usłudze Azure Search parametry `highlight`, `highlightPreTag` i `highlightPostTag` umożliwiają łatwe wyróżnianie tej części wyników, która dokładnie odpowiada zapytaniu wyszukiwania. Można wskazać, w których polach *z możliwością wyszukiwania* ma zostać wyróżniony dopasowany tekst, a także dokładnie określić tagi ciągów, które mają zostać dodane na początku i na końcu dopasowanego tekstu zwracanego przez usługę Azure Search.

## <a name="apis-and-tools-for-testing"></a>Interfejsy API i narzędzia do testowania

W poniższej tabeli wymieniono interfejsów API i oparte na narzędziu podejścia do przesyłania kwerend.

| Metodologia | Opis |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, który może służyć do tworzenie zapytań względem indeksu usługi Azure Search.  <br/>[Dowiedz się więcej.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Wyszukiwanie dokumentów (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET lub POST metod w indeksie, za pomocą parametrów zapytania, aby uzyskać dodatkowe dane wejściowe.  |
| [Narzędzie fiddler, Postman lub innego narzędzia do testowania HTTP](search-fiddler.md) | Wyjaśnia, jak skonfigurować nagłówek żądania i treści do wysyłania zapytań do usługi Azure Search.  |
| [Eksplorator wyszukiwania w witrynie Azure portal](search-explorer.md) | Zawiera pasek wyszukiwania i opcje dla opcji indeksu i interfejsu api-version. Wyniki są zwracane jako dokumenty JSON. <br/>[Dowiedz się więcej.](search-get-started-portal.md#query-index) | 

## <a name="see-also"></a>Zobacz także

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search (zapytanie analizy architektury](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania na platformie .NET](search-query-dotnet.md)
+ [Jak wykonywać zapytania w REST](search-query-rest-api.md)
