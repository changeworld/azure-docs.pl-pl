---
title: Typy zapytań i kompozycji — usługa Azure Search
description: Podstawy do tworzenia zapytań wyszukiwania w usłudze Azure Search przy użyciu parametrów, aby zastosować filtr, wybierz i sortować wyniki.
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/25/2019
ms.custom: seodec2018
ms.openlocfilehash: 6afc9325cb2aababb286f653323531c9ebb04b4a
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438805"
---
# <a name="how-to-compose-a-query-in-azure-search"></a>Jak tworzyć zapytania w usłudze Azure Search

W usłudze Azure Search zapytania jest pełną specyfikację obustronne operacji. Parametry żądania podać kryteria dopasowania do znajdowania dokumentów w indeksie, instrukcje wykonywania dla aparatu i dyrektyw kształtowania odpowiedzi. 

Żądanie zapytania jest zaawansowanych konstrukcji, określając pola, które są w zakresie, jak wyszukiwać, które pola, aby powrócić do sortowania lub filtrowania i tak dalej. Nie określono tego parametru, uruchomieniu zapytania względem wszystkie pola z możliwością wyszukiwania jako operacji wyszukiwania pełnotekstowego, zwracając wynik nie została ona oceniona zestawu w dowolnej kolejności.

## <a name="apis-and-tools-for-testing"></a>Interfejsy API i narzędzia do testowania

W poniższej tabeli wymieniono interfejsów API i oparte na narzędziu podejścia do przesyłania kwerend.

| Metodologia | Opis |
|-------------|-------------|
| [Eksplorator wyszukiwania (portal)](search-explorer.md) | Zawiera pasek wyszukiwania i opcje dla opcji indeksu i interfejsu api-version. Wyniki są zwracane jako dokumenty JSON. <br/>[Dowiedz się więcej.](search-get-started-portal.md#query-index) | 
| [Postman lub Fiddler](search-fiddler.md) | Narzędzia do testowania sieci Web są doskonałym wyborem dla opracowywania wywołania REST. Interfejs API REST obsługuje każda operacja możliwe w usłudze Azure Search. W tym artykule Dowiedz się, jak skonfigurować nagłówek żądania HTTP i treści wysyłania żądań do usługi Azure Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, który może służyć do tworzenie zapytań względem indeksu usługi Azure Search.  <br/>[Dowiedz się więcej.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Wyszukiwanie dokumentów (interfejs API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET lub POST metod w indeksie, za pomocą parametrów zapytania, aby uzyskać dodatkowe dane wejściowe.  |

## <a name="a-first-look-at-query-requests"></a>Pierwsze spojrzenie na żądań zapytań

Przykładami są przydatne w przypadku wprowadzenia nowych pojęć. Zgodnie z językiem zapytań skonstruowane w [interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents), w tym przykładzie obiekty docelowe [nieruchomości pokaz indeksu](search-get-started-portal.md) oraz typowe parametry.

```
{
    "queryType": "simple" 
    "search": "seattle townhouse* +\"lake\"",
    "searchFields": "description, city",
    "count": "true",
    "select": "listingId, street, status, daysOnMarket, description",
    "top": "10",
    "orderby": "daysOnMarket"
}
```

+ **`queryType`** Ustawia analizator, co w usłudze Azure Search może być [domyślny prosty analizator zapytań](search-query-simple-examples.md) (optymalne w celu wyszukiwania pełnotekstowego) lub [pełnego analizatora zapytań Lucene](search-query-lucene-examples.md) używane dla zapytań zaawansowanych konstrukcji, takich jak wyrażeń regularnych , wyszukiwanie w sąsiedztwie, rozmyte i wyszukiwanie symboli wieloznacznych, kilka.

+ **`search`** zawiera dopasowanie kryteria, zwykle tekstu, ale często wraz z operatorami logicznymi. Postanowienia pojedynczą autonomiczną są *termin* zapytania. Są ujęte w cudzysłów wieloczęściowy kwerendy *kluczowych* zapytania. Wyszukiwanie może być Niezdefiniowany, podobnie jak w **`search=*`**, ale najprawdopodobniej składa się z warunków, wyrażenia i operatory, podobny do wyświetlanych w przykładzie.

+ **`searchFields`** jest opcjonalny, używany do ograniczenia wykonywania zapytania w określonych polach.

Odpowiedzi są również ukształtowane przez parametry, które należy uwzględnić w zapytaniu. W tym przykładzie zestawu wyników składa się z pól na liście **`select`** instrukcji. Najważniejsze 10 trafień są zwracane w tym zapytaniu, ale **`count`** informujący o tym, liczby dokumentów dopasowania ogólną. W tym zapytaniu wiersze są sortowane według daysOnMarket.

W usłudze Azure Search wykonanie zapytania jest zawsze względem jednego indeksu, uwierzytelniony przy użyciu klucza api-key podany w żądaniu. W spoczynku oba są dostarczane w nagłówkach żądania.

### <a name="how-to-run-this-query"></a>Sposób wykonania tego zapytania

Do wykonania tego zapytania, należy użyć [wyszukiwania Eksploratora i indeks pokaz nieruchomości](search-get-started-portal.md). 

W pasku wyszukiwania programu explorer można wkleić tego ciągu zapytania: `search=seattle townhouse +lake&searchFields=description, city&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Jak operacje zapytań są włączone przez indeks

Projekt indeksu i zapytania projektu są ściśle powiązane w usłudze Azure Search. Podstawowe fakt wiedzieć na początku jest fakt, że *schemat indeksu*, za pomocą atrybutów w każdym polu, określa rodzaj zapytania można tworzyć. 

Atrybuty indeksu w polu Określ dozwolone operacje —, czy pole jest *wyszukiwanie* w indeksie, *pobieranie* w wynikach, *sortowanie*,  *można filtrować*, i tak dalej. W ciągu zapytania przykład `"$orderby": "daysOnMarket"` działa tylko, ponieważ pole daysOnMarket jest oznaczony jako *sortowanie* w schemacie indeksu. 

![Definicja dla przykładu nieruchomości indeksu](./media/search-query-overview/realestate-sample-index-definition.png "definicja przykład nieruchomości indeksu")

Powyższym zrzucie ekranu jest częściowa lista atrybuty indeksu dla przykładu nieruchomości. Schemat indeksu całego można wyświetlić w portalu. Aby uzyskać więcej informacji na temat atrybutów indeksu zobacz [utworzyć indeks interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/create-index).

> [!Note]
> Niektóre funkcje zapytania włączono całego indeksu, a nie na poszczególnych pól. Te funkcje obejmują: [mapy synonimów](search-synonyms.md), [niestandardowe analizatory](index-add-custom-analyzers.md), [konstrukcji sugestora (dla automatycznego uzupełniania i automatycznego sugerowania)](index-add-suggesters.md), [oceniania logikę Klasyfikacja wyników](index-add-scoring-profiles.md).

## <a name="elements-of-a-query-request"></a>Elementy żądania zapytania

Zapytania są zawsze kierowane na jeden indeks. Nie można przyłączyć indeksów, lub utworzyć struktury danych niestandardowych lub tymczasowej jako element docelowy kwerendy. 

Wymagane elementy na żądania zapytania obejmują następujące składniki:

+ Usługa punktu końcowego i indeks kolekcję documents wyrażone jako adres URL zawierający składniki stałych i zdefiniowanych przez użytkownika: **`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`** (Tylko REST) jest niezbędne, ponieważ więcej niż jedna wersja interfejsu API jest dostępny przez cały czas. 
+ **`api-key`**, zapytania lub administratora klucz api-key, uwierzytelnia żądanie do usługi.
+ **`queryType`**, prostej lub pełnej, który można pominąć, jeśli używasz domyślnych wbudowanych prostą składnię.
+ **`search`** lub **`filter`** zapewnia dopasowanie kryteria, które mogą być nieokreślony, jeśli chcesz wykonać to puste wyszukiwanie. Oba typy zapytań są rozważane w kontekście prosty analizator, ale nawet zaawansowane zapytania wymaga parametru wyszukiwania do przekazywania wyrażeń złożonych zapytań.

Wszystkie inne parametry wyszukiwania są opcjonalne. Aby uzyskać pełną listę atrybutów, zobacz [Tworzenie indeksu (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Aby dokładniej poznać sposób parametry są używane podczas przetwarzania, zobacz [jak wyszukiwanie pełnotekstowe działa w usłudze Azure Search](search-lucene-query-architecture.md).

## <a name="choose-a-parser-simple--full"></a>Wybierz analizator: prosty | Pełna

Usługa Azure Search znajduje się na górze Apache Lucene i zapewnia wybór między dwa parsery zapytania dotyczące obsługi typowych, wyspecjalizowana zapytania. Żądania przy użyciu prostego analizatora są formułować za pomocą [prosta składnia zapytań](query-simple-syntax.md), wybrane jako domyślne dla szybkości i skuteczności w dowolnej postaci tekstu zapytania. Ta składnia obsługuje szereg typowych operatorów wyszukiwania, na przykład AND, OR, NOT, frazy, sufiks i pierwszeństwo operatorów.

[Pełnej składni zapytań Lucene](query-Lucene-syntax.md#bkmk_syntax), włączone po dodaniu `queryType=full` na żądanie udostępnia powszechnie przyjętym i ekspresyjny język zapytań opracowany jako część [Apache Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Pełna składnia rozszerza prostą składnię. Każde zapytanie, które piszesz prostą składnię działa pełna analizator Lucene. 

Poniższe przykłady ilustrują punkt: sam zapytania, ale przy użyciu ustawień inny znak uzyskanie różne wyniki. W pierwszym zapytaniu `^3` jest traktowany jako część termin wyszukiwania.

```
queryType=simple&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

Tego samego zapytania przy użyciu pełnego analizatora Lucene interpretuje boost w polu na "ranch", która zwiększa wyszukiwarkach wyników zawierających ten konkretny termin.

```
queryType=full&search=mountain beach garden ranch^3&searchFields=description&$count=true&$select=listingId, street, status, daysOnMarket, description&$top=10&$orderby=daysOnMarket
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy zapytań

Usługa Azure Search obsługuje szeroką gamę typów zapytań. 

| Typ zapytania | Sposób użycia | Więcej informacji i przykłady |
|------------|--------|-------------------------------|
| Wyszukiwanie tekstowe w dowolnej postaci | Parametr wyszukiwania i albo analizatora| Wyszukiwanie pełnotekstowe skanowania pod kątem jednego lub większej liczby terminów, we wszystkich *wyszukiwanie* pola w indeksie i działa w sposób, jakiego oczekuje się aparat wyszukiwania, takich jak Google czy Bing. Przykładu we wprowadzeniu jest wyszukiwanie pełnotekstowe.<br/><br/>Wyszukiwanie pełnotekstowe ulega Analiza tekstu przy użyciu standardowy analizator Lucene (domyślnie) na małe, wszystkie warunki, Usuń słowa ignorowane, takich jak "". Można zastąpić domyślną z [analizatory innej niż angielska](index-add-language-analyzers.md#language-analyzer-list) lub [wyspecjalizowane analizatory niezależny od języka](index-add-custom-analyzers.md#AnalyzerTable) , które modyfikują Analiza tekstu. Na przykład [— słowo kluczowe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) całą zawartość pola, które traktuje jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kodów pocztowych, identyfikatory i nazwy niektórych produktów. | 
| Wyszukiwanie filtrowane | [Wyrażenie filtru OData](query-odata-filter-orderby-syntax.md) i albo analizatora | Zapytania filtru oceny wyrażenia logicznego wszystkich *filtrowanie* pól w indeksie. W przeciwieństwie do wyszukiwania zapytanie filtru dopasowuje dokładnie zawartość pola, w tym uwzględnianie wielkości liter w polach ciągu. Inna różnica polega na tym, że filtr zapytania są wyrażone w składnia OData. <br/>[Przykładowe wyrażenie filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Wyszukiwanie geograficzne | [Typ Edm.GeographyPoint](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) na pole, wyrażenie filtru i albo analizatora | Współrzędne przechowywane w polu o Edm.GeographyPoint są używane do "Znajdź w pobliżu" lub opartą na mapie Wyszukaj kontrolki. <br/>[Wyszukiwanie geograficzne przykład](search-query-simple-examples.md#example-5-geo-search)|
| Zakres wyszukiwania | Wyrażenie filtru i prosty analizator składni | W usłudze Azure Search zapytania są tworzone za pomocą parametru filtru. <br/>[Przykład filtru zakresu](search-query-simple-examples.md#example-4-range-filters) | 
| [Filtrowanie w obrębie pola](query-lucene-syntax.md#bkmk_fields) | Parametr wyszukiwania i pełnego analizatora | Zbuduj wyrażenie złożonego zapytania, przeznaczone dla jednego pola. <br/>[Wewnątrz pola filtrowanie przykład](search-query-lucene-examples.md#example-2-intra-field-filtering) |
| [Wyszukiwanie rozmyte](query-lucene-syntax.md#bkmk_fuzzy) | Parametr wyszukiwania i pełnego analizatora | Dopasowań na warunkach o podobnych konstrukcja lub pisowni. <br/>[Przykład wyszukiwania rozmytego](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [Wyszukiwanie w sąsiedztwie](query-lucene-syntax.md#bkmk_proximity) | Parametr wyszukiwania i pełnego analizatora | Warunki znajduje znajdujące się blisko siebie nawzajem w dokumencie. <br/>[Przykładowe wyszukiwanie w sąsiedztwie](search-query-lucene-examples.md#example-4-proximity-search) |
| [promowanie](query-lucene-syntax.md#bkmk_termboost) | Parametr wyszukiwania i pełnego analizatora | Szereguje wyższe dokumentu, jeśli zawiera on wzmocnione termin względem innych użytkowników, które nie. <br/>[Przykład zwiększenie termin](search-query-lucene-examples.md#example-5-term-boosting) |
| [wyszukiwanie wyrażenia regularnego](query-lucene-syntax.md#bkmk_regex) | Parametr wyszukiwania i pełnego analizatora | Dopasowania na podstawie zawartości wyrażenia regularnego. <br/>[Przykładowe wyrażenie regularne](search-query-lucene-examples.md#example-6-regex) |
|  [symbol wieloznaczny; Prefiks może też wyszukiwania](query-lucene-syntax.md#bkmk_wildcard) | Parametr wyszukiwania i pełnego analizatora | Dopasowania na podstawie prefiksu i tyldy (`~`) lub pojedynczy znak (`?`). <br/>[Przykład wyszukiwania symboli wieloznacznych](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Zarządzanie wynikami wyszukiwania 

Wyniki zapytania są przesyłane strumieniowo jako dokumenty JSON w interfejsie API REST, mimo, że użycie interfejsów API programu .NET serializacji jest wbudowany. Wyniki można przekształcić przez ustawienie parametrów dla kwerendy, wybranie określonych pól dla odpowiedzi.

Parametry zapytania może służyć do struktury zestawu wyników w następujący sposób:

+ Ograniczanie lub adapterów przetwarzania wsadowego liczbę dokumentów w wynikach (50 domyślnie)
+ Wybieranie pola do uwzględnienia w wynikach
+ Ustawienie kolejności sortowania
+ Dodawanie trafień zaznaczenie, aby zwrócić uwagę czytelnika na pasujące terminy w treści wyników wyszukiwania

### <a name="tips-for-unexpected-results"></a>Porady dotyczące nieoczekiwane wyniki

Od czasu do czasu substancji i nie struktury wyników jest nieoczekiwany. Gdy niż oczekiwana zobaczyć wyniki zapytania, możesz spróbować tych modyfikacji kwerendy, aby sprawdzić, czy poprawić wyniki:

+ Zmiana **`searchMode=any`** (domyślna), aby **`searchMode=all`** będą musieli dopasowanie wszystkie kryteria, a nie jakąkolwiek kryteriów. Jest to szczególnie istotne, gdy operatory logiczne są uwzględnione zapytania.

+ Zmień technika zapytania, jeśli tekst lub poddawać analizie leksykalnej, jest to konieczne, ale typ zapytania wyklucza językowej przetwarzania. Wyszukiwanie pełnotekstowe, tekst lub autocorrects poddawać analizie leksykalnej błędów pisowni, formularze programu word w liczbie pojedynczej niż liczby mnogiej i nawet nieregularne zlecenia lub rzeczowniki. Dla niektórych kwerend, takich jak rozmyte lub wyszukiwania symboli wieloznacznych, analiza tekstu nie jest częścią zapytania przetwarzania potoku. W niektórych scenariuszach wyrażeń regularnych zostały użyte jako obejście tego problemu. 

### <a name="paging-results"></a>Stronicowanie wyników
Usługa Azure Search ułatwia implementowanie stronicowania wyników wyszukiwania. Za pomocą **`top`** i **`skip`** parametry, można sprawnie wysyłać żądania wyszukiwania, które umożliwiają uzyskanie całego zbioru wyników wyszukiwania w zarządzanych, uporządkowanych podzbiorów, który pozwala łatwo stosować dobre wyszukiwania interfejsu użytkownika rozwiązania. Razem z mniejszymi podzbiorami wyników można również odbierać liczbę dokumentów w całym zbiorze wyników wyszukiwania.

Więcej informacji o stronicowaniu wyników wyszukiwania można znaleźć w artykule [How to page search results in Azure Search](search-pagination-page-layout.md) (Sposoby stronicowania wyników wyszukiwania w usłudze Azure Search).

### <a name="ordering-results"></a>Porządkowanie wyników
Usługa Azure Search udostępnia możliwość przekazania wyników zapytania wyszukiwania uporządkowanych według wartości w określonym polu. Domyślnie usługa Azure Search porządkuje wyniki w oparciu o rangę wyniku wyszukiwania poszczególnych dokumentów, który jest określany na podstawie wagi [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Jeśli chcesz, aby usługa Azure Search zwracała wyniki uporządkowane według wartości innej niż wynik wyszukiwania, możesz użyć **`orderby`** parametru wyszukiwania. Można określić wartość **`orderby`** parametr dołączając nazwy pól i wywołania [  **`geo.distance()` funkcja** ](query-odata-filter-orderby-syntax.md) przypadku wartości geoprzestrzennych. Każde wyrażenie może następować `asc` do wskazania, że wyniki są żądane w kolejności rosnącej, a **`desc`** do wskazania, że wyniki są sortowane w kolejności malejącej. Domyślnie jest stosowana kolejność rosnąca.


### <a name="hit-highlighting"></a>Wyróżnianie trafień
W usłudze Azure Search, podkreślając części, w wynikach wyszukiwania, zgodne z zapytaniem wyszukiwania umożliwiają łatwe za pomocą **`highlight`**, **`highlightPreTag`**, i **`highlightPostTag`** parametrów. Można wskazać, w których polach *z możliwością wyszukiwania* ma zostać wyróżniony dopasowany tekst, a także dokładnie określić tagi ciągów, które mają zostać dodane na początku i na końcu dopasowanego tekstu zwracanego przez usługę Azure Search.

## <a name="see-also"></a>Zobacz także

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search (zapytanie analizy architektury)](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak wykonywać zapytania na platformie .NET](search-query-dotnet.md)
+ [Jak wykonywać zapytania w REST](search-create-index-rest-api.md)
