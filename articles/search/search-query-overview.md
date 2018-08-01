---
title: Zapytanie podstawowe informacje dotyczące usługi Azure Search | Dokumentacja firmy Microsoft
description: Podstawy do tworzenia zapytań wyszukiwania w usłudze Azure Search przy użyciu parametrów, aby zastosować filtr, wybierz i sortować wyniki.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/27/2018
ms.openlocfilehash: 4650ad89850f32ae5e83a7ac1cd5eac096b180ed
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366450"
---
# <a name="query-fundamentals-in-azure-search"></a>Podstawy zapytania w usłudze Azure Search

Definicja zapytania w usłudze Azure Search jest pełną specyfikację żądania, które zawiera następujące elementy: Usługa punktu końcowego adresu URL, indeksu docelowego, klucz interfejsu api, używany do uwierzytelniania żądania, wersja interfejsu api i ciągu zapytania. 

Tworzenia ciągu zapytania składa się z parametrów, zgodnie z definicją w [interfejsu API wyszukiwania w dokumentach](https://docs.microsoft.com/rest/api/searchservice/search-documents). Najważniejsze jest **wyszukiwania =** parametr, który może być niezdefiniowana (`search=*`), ale najprawdopodobniej zawiera warunki, wyrażenia i operatory, podobny do poniższego przykładu:

```
"search": "seattle townhouse +\"lake\""
```

Inne parametry są używane do kierowania przetwarzanie zapytań i poszerz możliwości odpowiedzi. Jak są używane parametry przykładami zakresu wyszukiwania określonych pól, ustawienie Tryb wyszukiwania, aby modulowało odchylenie precision-recall oraz dodawanie liczbę, aby śledzić wyniki. 

```
{  
    "search": "seattle townhouse* +\"lake\"",  
    "searchFields": "description, city",  
    "searchMode": "all",
    "count": "true", 
    "queryType": "simple" 
 } 
```

Mimo że podstawowych definicji zapytania schematu indeksu jest równie ważne w sposób określa dozwolone operacje na podstawie pola według pola. Podczas tworzenia indeksu atrybuty pól określają dozwolone operacje. Na przykład, aby zakwalifikować się do wyszukiwania pełnotekstowego oraz włączenia w wynikach wyszukiwania, pola muszą być oznaczone jako zarówno *wyszukiwanie* i *pobieranie*.

Podczas przeszukiwania wykonanie jest zawsze względem jednego indeksu, uwierzytelniony przy użyciu klucza api-key podany w żądaniu. Nie można przyłączyć indeksów, lub utworzyć struktury danych niestandardowych lub tymczasowej jako element docelowy kwerendy.  

Wyniki zapytania są przesyłane strumieniowo jako dokumenty JSON w interfejsie API REST, mimo, że użycie interfejsów API programu .NET serializacji jest wbudowany. Wyniki można przekształcić przez ustawienie parametrów dla kwerendy, wybranie określonych pól dla wyniku

Aby podsumować, treść żądania zapytania określa zakres i operacje: pola, które można uwzględnić w wyszukiwaniu, które pola do uwzględnienia w wyniku, do sortowania lub filtrowania, i tak dalej. Nie określono tego parametru, uruchomieniu zapytania względem wszystkie pola z możliwością wyszukiwania jako operacji wyszukiwania pełnotekstowego, zwracając wynik nie została ona oceniona zestawu w dowolnej kolejności.

<a name="types-of-queries"></a>

## <a name="types-of-queries-search-and-filter"></a>Typy zapytań: wyszukiwania i filtrowania

Usługa Azure Search udostępnia wiele opcji tworzenia niezwykle wydajnych zapytań. Dwa najczęściej używane typy zapytań to `search` i `filter`. 

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

Elementy składni wyrażeń filtrów należą do podzbioru [języka filtru OData](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search). W przypadku zapytań wyszukiwania można używać [składni uproszczonej](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search) lub [składni zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search). Informacje na ten temat zostały podane poniżej.


## <a name="choose-a-syntax-simple-or-full"></a>Wybierz składni: prostych lub pełnych

Usługa Azure Search znajduje się na górze Apache Lucene i zapewnia wybór między dwa parsery zapytania dotyczące obsługi typowych, wyspecjalizowana zapytania. Typowym wyszukiwaniu żądania są formułowane przy użyciu domyślnego [prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search). Ta składnia obsługuje szereg typowych operatorów wyszukiwania, na przykład AND, OR, NOT, frazy, sufiks i pierwszeństwo operatorów.

[Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/Lucene-query-syntax-in-Azure-Search#bkmk_syntax), włączone po dodaniu **queryType = full** na żądanie udostępnia powszechnie zaakceptowany i ekspresyjny język zapytań opracowany jako część [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Przy użyciu tej składni pozwala wyspecjalizowane zapytania:

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

W ramach przesyłania żądań wyszukiwania do usługi Azure Search dostępnych jest szereg parametrów, które można określić, aby użyć ich razem z rzeczywistymi słowami wpisywanymi w polu wyszukiwania w aplikacji. Te parametry zapytań zapewniają lepszą kontrolę nad [wyszukiwaniem pełnotekstowym](search-lucene-query-architecture.md).

Wymagane elementy na żądania zapytania obejmują następujące składniki:

+ Usługa punktu końcowego i indeks kolekcji dokumentów wyrażone jako adres URL `https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`.
+ Wersja interfejsu API REST (tylko), wyrażone jako **parametru api-version =**
+ zapytania lub administratorem klucz api-key, wyrażone jako **klucz api-key =**
+ wyrażone jako ciąg zapytania **wyszukiwania =**, które mogą być nieokreślony, jeśli chcesz wykonać to puste wyszukiwanie. Możesz również wysłać tylko wyrażenia filtru jako **$filter =**.
+ **queryType =**, prostej lub pełnej, który można pominąć, jeśli chcesz użyć domyślnej prostą składnię.

Wszystkie inne parametry wyszukiwania są opcjonalne.

## <a name="apis-and-tools-for-testing"></a>Interfejsy API i narzędzia do testowania

W poniższej tabeli wymieniono interfejsów API i oparte na narzędziu podejścia do przesyłania kwerend.

| Metodologia | Opis |
|-------------|-------------|
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, który może służyć do tworzenie zapytań względem indeksu usługi Azure Search.  <br/>[Dowiedz się więcej.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Wyszukiwanie dokumentów (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET lub POST metod w indeksie, za pomocą parametrów zapytania, aby uzyskać dodatkowe dane wejściowe.  |
| [Narzędzie fiddler, Postman lub innego narzędzia do testowania HTTP](search-fiddler.md) | Wyjaśnia, jak skonfigurować nagłówek żądania i treści do wysyłania zapytań do usługi Azure Search.  |
| [Eksplorator wyszukiwania w witrynie Azure portal](search-explorer.md) | Zawiera pasek wyszukiwania i opcje dla opcji indeksu i interfejsu api-version. Wyniki są zwracane jako dokumenty JSON. <br/>[Dowiedz się więcej.](search-get-started-portal.md#query-index) | 

## <a name="manage-search-results"></a>Zarządzanie wynikami wyszukiwania

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

## <a name="see-also"></a>Zobacz także

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search (zapytanie analizy architektury](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania na platformie .NET](search-query-dotnet.md)
+ [Jak wykonywać zapytania w REST](search-query-rest-api.md)
