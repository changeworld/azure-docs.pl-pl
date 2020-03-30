---
title: Typy i kompozycja zapytań
titleSuffix: Azure Cognitive Search
description: Podstawy tworzenia kwerendy wyszukiwania w usłudze Azure Cognitive Search przy użyciu parametrów do filtrowania, wybierania i sortowania wyników.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 902f3628235cc8a4524ddc4dd8a5327592fe47e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282824"
---
# <a name="query-types-and-composition-in-azure-cognitive-search"></a>Typy zapytań i kompozycja w usłudze Azure Cognitive Search

W usłudze Azure Cognitive Search kwerenda jest pełną specyfikacją operacji w obie strony. Parametry na żądanie podają kryteria dopasowania do znajdowania dokumentów w indeksie, które pola do uwzględnienia lub wykluczenia, instrukcje wykonywania przekazywane do aparatu i dyrektywy dotyczące kształtowania odpowiedzi. Nieokreślone`search=*`( ), kwerenda jest uruchamiana względem wszystkich pól z możliwością wyszukiwania w trybie pełnotekstowym, zwracając wynik niezłomny ustawiony w dowolnej kolejności.

Poniższy przykład jest kwerendą reprezentatywną skonstruowaną w [interfejsie API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents). W tym przykładzie jest przeznaczony [dla indeksu demo hoteli](search-get-started-portal.md) i zawiera typowe parametry.

```
{
    "queryType": "simple" 
    "search": "+New York +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

+ **`queryType`** ustawia analizatora, który jest [domyślnym analizatorem prostych zapytań](search-query-simple-examples.md) (optymalnym dla pełnego wyszukiwania tekstu) lub [pełnym analizatorem zapytania Lucene](search-query-lucene-examples.md) używanym do zaawansowanych konstrukcji zapytań, takich jak wyrażenia regularne, wyszukiwanie zbliżeniowe, wyszukiwanie rozmyte i wieloznaczne, aby wymienić tylko kilka.

+ **`search`** zawiera kryteria dopasowania, zwykle tekst, ale często towarzyszy operatorów logicznych. Pojedyncze terminy autonomiczne są *zapytaniami terminowymi.* Kwerendy wieloczęściowe z ofertą są kluczowymi kwerendami *fraz.* Wyszukiwanie może być niezdefiniowane, jak w **`search=*`**, ale bardziej prawdopodobne składa się z terminów, fraz i operatorów podobnych do tego, co pojawia się w przykładzie.

+ **`searchFields`** ogranicza wykonywanie kwerend do określonych pól. Każde pole, które jest przypisane jako *przeszukiwalne* w schemacie indeksu jest kandydatem dla tego parametru.

Odpowiedzi są również kształtowane przez parametry, które zostaną uwzględnione w kwerendzie. W tym przykładzie zestaw wyników składa **`select`** się z pól wymienionych w instrukcji. Tylko pola oznaczone jako *możliwe do pobrania* mogą być używane w instrukcji $select. Ponadto tylko **`top`** 10 trafień są zwracane **`count`** w tej kwerendzie, podczas gdy informuje, ile dokumentów odpowiada ogólnej, która może być więcej niż to, co są zwracane. W tej kwerendzie wiersze są sortowane według klasyfikacji w kolejności malejącej.

W usłudze Azure Cognitive Search wykonanie zapytania jest zawsze na jednym indeksie, uwierzytelniony przy użyciu klucza interfejsu API pod warunkiem, że w żądaniu. W REST oba są dostarczane w nagłówkach żądań.

### <a name="how-to-run-this-query"></a>Jak uruchomić tę kwerendę

Aby wykonać tę kwerendę, użyj [Eksploratora wyszukiwania i indeksu demo hoteli](search-get-started-portal.md). 

Ten ciąg zapytania można wkleić do paska wyszukiwania eksploratora:`search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

## <a name="how-query-operations-are-enabled-by-the-index"></a>Jak operacje kwerendy są włączane przez indeks

Projektowanie indeksów i projektowania zapytań są ściśle powiązane w usłudze Azure Cognitive Search. Istotnym faktem, aby wiedzieć z góry jest to, że *schemat indeksu*, z atrybutami w każdym polu, określa rodzaj kwerendy, które można utworzyć. 

Atrybuty indeksu w polu ustawiają dozwolone operacje — czy pole *można przeszukiwać* w indeksie, *można je pobrać* w wynikach, *sortować*, *filtrować*itd. W przykładowym ciągu `"$orderby": "Rating"` kwerendy działa tylko dlatego, że pole Ocena jest oznaczone jako *sortowalne* w schemacie indeksu. 

![Definicja indeksu dla próbki hotelu](./media/search-query-overview/hotel-sample-index-definition.png "Definicja indeksu dla próbki hotelu")

Powyższy zrzut ekranu to częściowa lista atrybutów indeksu dla przykładu hoteli. Można wyświetlić cały schemat indeksu w portalu. Aby uzyskać więcej informacji na temat atrybutów [indeksu,](https://docs.microsoft.com/rest/api/searchservice/create-index)zobacz Tworzenie interfejsu API REST indeksu .

> [!Note]
> Niektóre funkcje kwerendy jest włączona w całym indeksie, a nie na podstawie pola. Te możliwości obejmują: [mapy synonimów,](search-synonyms.md) [analizatory](index-add-custom-analyzers.md) [niestandardowe, konstrukcje sugesty (dla autouzupełniania i sugerowanych zapytań),](index-add-suggesters.md) [logikę oceniania wyników rankingowych.](index-add-scoring-profiles.md)

## <a name="elements-of-a-query-request"></a>Elementy żądania kwerendy

Zapytania są zawsze skierowane do jednego indeksu. Nie można łączyć indeksów ani tworzyć niestandardowych lub tymczasowych struktur danych jako docelowego zapytania. 

Wymagane elementy w żądaniu kwerendy obejmują następujące składniki:

+ Zbieranie dokumentów punktu końcowego usługi i indeksu, wyrażone jako adres URL zawierający składniki stałe i zdefiniowane przez użytkownika:**`https://<your-service-name>.search.windows.net/indexes/<your-index-name>/docs`**
+ **`api-version`**(tylko REST) jest konieczne, ponieważ więcej niż jedna wersja API jest dostępna przez cały czas. 
+ **`api-key`**, kwerendy lub administratora api-key, uwierzytelnia żądanie do usługi.
+ **`queryType`**, proste lub pełne, które można pominąć, jeśli używasz wbudowanej domyślnej prostej składni.
+ **`search`** lub **`filter`** podaje kryteria dopasowania, które mogą być nieokreślone, jeśli chcesz przeprowadzić puste wyszukiwanie. Oba typy zapytań są omówione w kategoriach prostego analizatora, ale nawet zaawansowane zapytania wymagają parametru wyszukiwania do przekazywania złożonych wyrażeń zapytania.

Wszystkie inne parametry wyszukiwania są opcjonalne. Aby uzyskać pełną listę atrybutów, zobacz [Tworzenie indeksu (REST)](https://docs.microsoft.com/rest/api/searchservice/create-index). Aby dokładniej przyjrzeć się, jak parametry są używane podczas przetwarzania, zobacz [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search.](search-lucene-query-architecture.md)

## <a name="choose-apis-and-tools"></a>Wybieranie interfejsów API i narzędzi

W poniższej tabeli wymieniono interfejsy API i metody oparte na narzędziach do przesyłania zapytań.

| Metodologia | Opis |
|-------------|-------------|
| [Eksplorator wyszukiwania (portal)](search-explorer.md) | Udostępnia pasek wyszukiwania i opcje wyboru indeksu i wersji interfejsu API. Wyniki są zwracane jako dokumenty JSON. Zalecane do eksploracji, testowania i sprawdzania poprawności. <br/>[Dowiedz się więcej.](search-get-started-portal.md#query-index) | 
| [Listonosz lub inne narzędzia REST](search-get-started-postman.md) | Narzędzia do testowania stron internetowych są doskonałym wyborem do formułowania wywołań REST. Interfejs API REST obsługuje każdą możliwą operację w usłudze Azure Cognitive Search. W tym artykule dowiesz się, jak skonfigurować nagłówek żądania HTTP i treść do wysyłania żądań do usługi Azure Cognitive Search.  |
| [SearchIndexClient (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) | Klient, który może służyć do kwerendy indeksu usługi Azure Cognitive Search.  <br/>[Dowiedz się więcej.](search-howto-dotnet-sdk.md#core-scenarios)  |
| [Dokumenty wyszukiwania (INTERFEJS API REST)](https://docs.microsoft.com/rest/api/searchservice/search-documents) | GET lub POST metody w indeksie, przy użyciu parametrów kwerendy dla dodatkowych danych wejściowych.  |

## <a name="choose-a-parser-simple--full"></a>Wybierz analizator: prosty | Pełne

Usługa Azure Cognitive Search znajduje się na szczycie Apache Lucene i daje wybór między dwoma analizatorami zapytań do obsługi typowych i wyspecjalizowanych zapytań. Żądania przy użyciu prostego analizatora są formułowane przy użyciu [prostej składni kwerendy](query-simple-syntax.md), wybranej jako domyślna dla jego szybkości i skuteczności w kwerendach tekstowych w postaci swobodnej. Ta składnia obsługuje wiele typowych operatorów wyszukiwania, w tym operatory AND, OR, NOT, frazy, sufiksu i pierwszeństwa.

[Pełna składnia kwerendy Lucene,](query-Lucene-syntax.md#bkmk_syntax)włączona `queryType=full` po dodaniu do żądania, udostępnia powszechnie przyjęty i ekspresyjny język zapytania opracowany jako część [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Pełna składnia rozszerza prostą składnię. Każde zapytanie, które piszesz dla prostej składni, jest uruchamiane w pełnej analizatorze lucene. 

Poniższe przykłady ilustrują punkt: ta sama kwerenda, ale z różnymi ustawieniami queryType, dają różne wyniki. W pierwszym zapytaniu `^3` `historic` po jest traktowany jako część wyszukiwanego terminu. Najlepszym wynikiem dla tego zapytania jest "Marquis Plaza & Suites", który ma *ocean* w opisie

```
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

To samo zapytanie przy użyciu pełnego analizatora lucene interpretuje `^3` jako booster termin w terenie. Zmiana analizatorów zmienia rangę, a wyniki zawierają termin *historyczny* przenoszący się na szczyt.

```
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typy zapytań

Usługa Azure Cognitive Search obsługuje szeroki zakres typów zapytań. 

| Typ kwerendy | Sposób użycia | Przykłady i więcej informacji |
|------------|--------|-------------------------------|
| Wyszukiwanie tekstu w postaci swobodnej | Parametr wyszukiwania i analizator analizatora| Wyszukiwanie pełnotekstowe skanuje jeden lub więcej terminów we wszystkich polach *z możliwością wyszukiwania* w indeksie i działa tak, jak oczekiwano, że wyszukiwarka, taka jak Google lub Bing, będzie działać. Przykładem we wstępie jest wyszukiwanie pełnotekstowe.<br/><br/>Wyszukiwanie pełnotekstowe jest poddawane analizie tekstu przy użyciu standardowego analizatora Lucene (domyślnie) do wszystkich terminów, usuń słowa stop, takie jak "the". Wartość domyślną można zastąpić [analizatorami nieanglojęzycznymi](index-add-language-analyzers.md#language-analyzer-list) lub [wyspecjalizowanymi analizatorami niezależny od języka,](index-add-custom-analyzers.md#AnalyzerTable) które modyfikują analizę tekstu. Przykładem jest [słowo kluczowe,](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) które traktuje całą zawartość pola jako pojedynczy token. Jest to przydatne w przypadku danych, takich jak kody pocztowe, identyfikatory i niektóre nazwy produktów. | 
| Wyszukiwanie filtrowane | [Wyrażenie filtru OData](query-odata-filter-orderby-syntax.md) i albo analizatora | Kwerendy filtrujące oceniają wyrażenie logiczne we wszystkich polach *filtrowalnych* w indeksie. W przeciwieństwie do wyszukiwania kwerenda filtrująca dopasowuje dokładną zawartość pola, w tym wielkość liter w polach ciągów. Inną różnicą jest to, że kwerendy filtru są wyrażone w składni OData. <br/>[Przykład wyrażenia filtru](search-query-simple-examples.md#example-3-filter-queries) |
| Wyszukiwanie geograficzne | [Edm.GeographyPoint wpisz](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) w polu, wyrażenie filtru i albo analizatora | Współrzędne przechowywane w polu o Edm.GeographyPoint są używane do "znajdź blisko mnie" lub kontroli wyszukiwania na podstawie mapy. <br/>[Przykład wyszukiwania geograficznego](search-query-simple-examples.md#example-5-geo-search)|
| Wyszukiwanie zasięgu | wyrażenie filtru i prosty analizator | W usłudze Azure Cognitive Search zapytania zakresowe są tworzone przy użyciu parametru filtru. <br/>[Przykład filtru zakresu](search-query-simple-examples.md#example-4-range-filters) | 
| [Wyszukiwanie w polu wymówionym](query-lucene-syntax.md#bkmk_fields) | Parametr wyszukiwania i pełna analizator | Tworzenie wyrażenia kwerendy złożonej przeznaczonej dla pojedynczego pola. <br/>[Przykład wyszukiwania w polu polem](search-query-lucene-examples.md#example-2-fielded-search) |
| [wyszukiwanie rozmyte](query-lucene-syntax.md#bkmk_fuzzy) | Parametr wyszukiwania i pełna analizator | Pasuje na warunkach o podobnej konstrukcji lub pisowni. <br/>[Przykład wyszukiwania rozmytego](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [wyszukiwanie zbliżeniowe](query-lucene-syntax.md#bkmk_proximity) | Parametr wyszukiwania i pełna analizator | Znajduje terminy, które znajdują się blisko siebie w dokumencie. <br/>[Przykład wyszukiwania zbliżeniowego](search-query-lucene-examples.md#example-4-proximity-search) |
| [zwiększenie kadencji](query-lucene-syntax.md#bkmk_termboost) | Parametr wyszukiwania i pełna analizator | Ranga dokumentu wyżej, jeśli zawiera wzmocniony termin, w stosunku do innych, które nie. <br/>[Przykład pobudzania terminów](search-query-lucene-examples.md#example-5-term-boosting) |
| [wyszukiwanie wyrażeń regularnych](query-lucene-syntax.md#bkmk_regex) | Parametr wyszukiwania i pełna analizator | Dopasowuje na podstawie zawartości wyrażenia regularnego. <br/>[Przykład wyrażenia regularnego](search-query-lucene-examples.md#example-6-regex) |
|  [wyszukiwanie symboli wieloznacznych lub prefiksów](query-lucene-syntax.md#bkmk_wildcard) | Parametr wyszukiwania i pełna analizator | Dopasowania na podstawie prefiksu`~`i tyldy ( ) lub pojedynczego znaku (`?`). <br/>[Przykład wyszukiwania symboli wieloznacznych](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="manage-search-results"></a>Zarządzanie wynikami wyszukiwania 

Wyniki kwerendy są przesyłane strumieniowo jako dokumenty JSON w interfejsie API REST, chociaż w przypadku korzystania z interfejsów API platformy .NET trwa trwae serialowanie. Wyniki można kształtować, ustawiając parametry kwerendy, wybierając określone pola dla odpowiedzi.

Parametry kwerendy mogą służyć do struktury zestawu wyników w następujący sposób:

+ Ograniczanie lub przetwarzanie partii liczby dokumentów w wynikach (domyślnie 50)
+ Wybieranie pól do uwzględnienia w wynikach
+ Ustawianie kolejności sortowania
+ Dodawanie najważniejszych punktów, aby zwrócić uwagę na pasujące terminy w treści wyników wyszukiwania

### <a name="tips-for-unexpected-results"></a>Wskazówki dotyczące nieoczekiwanych wyników

Czasami substancja, a nie struktura wyników są nieoczekiwane. Gdy wyniki kwerendy nie są to, czego oczekujesz, można spróbować tych modyfikacji kwerendy, aby sprawdzić, czy wyniki poprawić:

+ Zmień **`searchMode=any`** (domyślnie), aby **`searchMode=all`** wymagać dopasowania do wszystkich kryteriów zamiast dowolnego z kryteriów. Jest to szczególnie ważne, gdy operatory logiczne są uwzględniane kwerendy.

+ Zmień technikę kwerendy, jeśli tekst lub analiza leksykalne jest konieczne, ale typ kwerendy wyklucza przetwarzania językowego. W wyszukiwaniu pełnotekstowym, autokorekty analizy tekstowej lub leksykowej dla błędów pisowni, pojedynczej liczby mnogiej formy wyrazów, a nawet nieregularne czasowniki lub rzeczowniki. W przypadku niektórych zapytań, takich jak wyszukiwanie rozmyte lub wieloznaczne, analiza tekstu nie jest częścią potoku analizowania kwerend. W niektórych scenariuszach wyrażenia regularne zostały użyte jako obejście. 

### <a name="paging-results"></a>Stronicowanie wyników
Usługa Azure Cognitive Search ułatwia implementowanie stronicowania wyników wyszukiwania. Za pomocą **`top`** **`skip`** i parametrów, można płynnie wystawiać żądania wyszukiwania, które pozwalają na odbieranie całkowitego zestawu wyników wyszukiwania w zarządzaniu, uporządkowane podzbiory, które łatwo włączyć dobre praktyki interfejsu użytkownika wyszukiwania. Razem z mniejszymi podzbiorami wyników można również odbierać liczbę dokumentów w całym zbiorze wyników wyszukiwania.

Więcej informacji na temat wyników wyszukiwania stronicowania można dowiedzieć się w artykule [Jak porównać wyniki wyszukiwania na stronie w usłudze Azure Cognitive Search](search-pagination-page-layout.md).

### <a name="ordering-results"></a>Porządkowanie wyników
Podczas odbierania wyników dla kwerendy wyszukiwania, można zażądać, aby usługa Azure Cognitive Search wyświetlała wyniki uporządkowane według wartości w określonym polu. Domyślnie usługa Azure Cognitive Search zamawia wyniki wyszukiwania na podstawie rangi wyniku wyszukiwania każdego dokumentu, który pochodzi od [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf).

Jeśli chcesz, aby usługa Azure Cognitive Search zwracała wyniki uporządkowane według **`orderby`** wartości innej niż wynik wyszukiwania, możesz użyć parametru wyszukiwania. Można określić wartość **`orderby`** parametru, aby uwzględnić nazwy pól i wywołania [** `geo.distance()` funkcji**](query-odata-filter-orderby-syntax.md) dla wartości geoprzestrzennych. Po każdym wyrażeniu `asc` można wskazać, że wyniki są **`desc`** wymagane w porządku rosnącym i wskazać, że wyniki są wymagane w porządku malejącym. Domyślnie jest stosowana kolejność rosnąca.


### <a name="hit-highlighting"></a>Wyróżnianie trafień
W usłudze Azure Cognitive Search podkreślenie dokładnej części wyników wyszukiwania, **`highlight`** **`highlightPreTag`** która **`highlightPostTag`** odpowiada zapytaniu wyszukiwania, jest łatwe przy użyciu programu , i parametrów. Można określić, które pola *zbywalne* powinny mieć podkreślony dopasowany tekst, a także określając dokładne znaczniki ciągów, które mają być dołączane do początku i końca dopasowanego tekstu, który zwraca usługa Azure Cognitive Search.

## <a name="see-also"></a>Zobacz też

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search (architektura analizowania zapytań)](search-lucene-query-architecture.md)
+ [Eksplorator wyszukiwania](search-explorer.md)
+ [Jak kwerendy w .NET](search-query-dotnet.md)
+ [Jak zapytać w REST](search-create-index-rest-api.md)
