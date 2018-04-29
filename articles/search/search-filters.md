---
title: Filtry w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Filtruj według tożsamości zabezpieczeń użytkownika, języka, lokalizacji geograficznej lub wartości liczbowe, aby ograniczyć wyniki wyszukiwania na zapytania w usłudze Azure Search, Usługa wyszukiwania w chmurze hostowanej w systemie Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: heidist
ms.openlocfilehash: 82da742e6512e0acc8278a255c7e4e0516eaa8cb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="filters-in-azure-search"></a>Filtry w usłudze Azure Search 

A *filtru* zawiera kryteria wyboru dokumentów używanych w zapytaniu usługi Azure Search. Niefiltrowane wyszukiwanie obejmuje wszystkie dokumenty w indeksie. Filtr zakresów zapytania wyszukiwania do podzbioru dokumentów. Na przykład filtr można ograniczyć wyszukiwanie pełnotekstowe tylko te produkty o konkretną markę lub kolor, w punktach cen powyżej pewnej wartości progowej.

Niektóre funkcje wyszukiwania nakładają wymagań filtru jako część implementacji, ale możesz użyć filtrów w dowolnym momencie chcesz ograniczyć wyszukiwanie przy użyciu *oparte na wartościach* kryteria (zakresu wyszukiwania na typ produktu "books" dla kategorii" z systemem innym niż fikcja"opublikowana przez"Schuster Simona &").

Jeśli zamiast tego celem jest docelowe wyszukiwania na określonych danych *struktury* (zakresu wyszukiwania do pola recenzje klientów), istnieją alternatywnych metod opisanych poniżej.

## <a name="when-to-use-a-filter"></a>Kiedy należy używać filtru

Filtry są podstawowych do kilku środowiska wyszukiwania, w tym "Znajdź pobliżu", filtry nawigacji aspektowej i zabezpieczeń przedstawiające tylko dokumenty, które użytkownik może zobaczyć. W przypadku zastosowania jednego z tych środowisk, filtr jest wymagany. Jest dołączony do zapytania wyszukiwania udostępniający współrzędne używanie funkcji geolokalizacji kategorii aspektu wybrane przez użytkownika lub identyfikator zabezpieczeń żądającego filtru.

Przykładowe scenariusze są następujące:

1. Użyj filtru w celu tworzenia wycinków indeksu na podstawie wartości danych w indeksie. Podany schemat z Miasto, typ mieszkaniowych i infrastruktury, można utworzyć filtr, aby jawnie wybrać dokumenty, które spełniają kryteria (w Seattle, condos, waterfront). 

  Wyszukiwanie pełnotekstowe z tych samych wejściach często daje podobne wyniki, ale filtr jest bardziej dokładne, że wymaga dokładnego dopasowania warunku filtru dla treści w indeksie. 

2. Za pomocą filtru wyników wyszukiwania jest dostarczany z wymaganiem filtru:

 * [Poufności informacji](search-faceted-navigation.md) używa filtru do przekazania kopii kategorii aspekt wybrane przez użytkownika.
 * Geograficznie wyszukiwania używa filtru do przekazania aplikacji współrzędne bieżącą lokalizację w polu "Znajdź w pobliżu". 
 * Filtry zabezpieczeń przekazać identyfikatorów zabezpieczeń jako kryteria filtrowania, jeśli dopasowania w indeksie służy jako serwer proxy dla praw dostępu do dokumentu.

3. Użyj filtru, jeśli chcesz, aby kryteria wyszukiwania dla pola liczbowego. 

  Pól liczbowych są pobieranie w dokumencie i mogą być wyświetlane w wynikach wyszukiwania, ale nie są one wyszukiwanie (pod warunkiem wyszukiwania pełnotekstowego) pojedynczo. Kryteria wyboru na podstawie danych numerycznych, należy użyć filtru.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatywne metody zmniejszenie zakresu

Jeśli ma to wpływ zawężającej w wynikach wyszukiwania filtry nie są tylko opcja. Te możliwości mogą być lepszym rozwiązaniem, w zależności od celu:

 + `searchFields` Parametr kwerendy przywiązuje wyszukiwania określonych pól. Na przykład jeśli indeks udostępnia oddzielne pola opisy w języku angielskim i hiszpańskim, służy searchFields pod kątem określonych pól na potrzeby wyszukiwania pełnotekstowego. 

+ `$select` parametr jest używany do określenia pola do uwzględnienia w wyniku ustawiony, skutecznie przycinanie odpowiedź przed wysłaniem go do aplikacji wywołującej. Ten parametr nie uściślenie kwerendy lub Zmniejsz kolekcji dokumentów, ale jeśli szczegółowego odpowiedzi był Twój cel, ten parametr jest rozważyć możliwość użycia. 

Aby uzyskać więcej informacji na temat albo parametru zobacz [dokumenty wyszukiwania > żądania > Parametry zapytania](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtry w potoku zapytania

Podczas przeszukiwania analizator filtru akceptuje kryteria jako dane wejściowe, konwertuje wyrażenie na atomic wyrażeń logicznych i tworzy drzewo filtru, który następnie jest obliczane w filtrowanie pól w indeksie.  

Filtrowanie występuje przed wyszukiwania kwalifikowanie dokumentów, w celu przetwarzania podrzędnego pobierania dokumentu i oceniania przydatności. Podczas parowania zawierające ciąg wyszukiwania, filtr skutecznie zmniejsza powierzchni operacji wyszukiwania kolejne. Gdy użyte bez parametrów (na przykład, gdy ciąg zapytania jest pusty, gdy `search=*`), kryteria filtru są wyłącznie danych wejściowych. 

## <a name="filter-definition"></a>Definicja filtru

Filtry są wyrażenia OData, przegubowe przy użyciu [podzbiór protokołu OData V4 składni obsługiwane w usłudze Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Można określić jeden filtr dla każdego **wyszukiwania** operacji, ale sam filtr może zawierać wielu pól, wiele kryteriów, a jeśli używasz **ismatch** funkcji, wiele wyrażeń. W wyrażeniu filtru wieloczęściowych można określić predykaty w dowolnej kolejności. Jeśli spróbujesz zmienić predykaty w określonej kolejności jest żadnych istotnych korzyści w wydajności.

Nienaruszalne ograniczenie wyrażenie filtru jest maksymalny limit na żądanie. Cały żądania, łącznie z filtra, może zawierać maksymalnie 16 MB dla żądania POST, lub 8 KB GET. Limity zmienne skorelowany liczba klauzul w wyrażeniu filtru. Regułą jest Jeśli masz setki klauzule zagraża powodowania limit. Firma Microsoft zaleca projektowania aplikacji w taki sposób, że nie generuje filtry niepowiązany rozmiar.

Poniższe przykłady przedstawiają definicje filtrów prototypowego w kilka interfejsów API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

```

## <a name="filter-design-patterns"></a>Wzorce projektowe filtru

Poniższe przykłady przedstawiają kilka wzorce projektowe dla scenariuszy filtru. Aby uzyskać więcej informacji, zobacz [składni wyrażeń OData > przykłady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ Autonomiczny **$filter**, bez ciąg zapytania, przydatne w przypadku wyrażenia filtru jest w stanie do pełnej kwalifikacji dokumenty zainteresowań. Bez ciąg zapytania jest nie leksykalne lub językową analiza, nie oceniania oraz nie klasyfikacji. Należy zauważyć, że ciąg wyszukiwania jest pusta.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinacja ciąg zapytania i **$filter**, gdzie filtru tworzy podzbiór i ciągu zapytania zapewnia wejść terminu wyszukiwania pełnotekstowego przez filtrowane podzbioru. Przy użyciu filtru zawierające ciąg zapytania jest najbardziej typowych wzorzec kodu.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Złożone kwerendy, oddzielone "lub", każde z nich własne kryteria filtru (na przykład "beagles" w "dog") lub "siamese" w "kot". LUB miał wyrażenia są oceniane oddzielnie, odpowiedzi z każdą z nich połączone w jedną odpowiedzi wysyłane z powrotem do aplikacji wywołującej. Ten wzorzec projektowy odbywa się za pośrednictwem funkcji search.ismatch. Możesz użyć wersji oceniania (search.ismatch) lub wersji oceniania (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Nawiązanie z tych artykułów kompleksowe wskazówki na przypadków użycia określonego:

+ [Filtry aspektów](search-filters-facets.md)
+ [Filtry języków](search-filters-language.md)
+ [Dostosowanie do zabezpieczeń](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Pole wymagań dotyczących filtrowania

W interfejsie API REST jest filtrowanie *na* domyślnie. Można filtrować polami zwiększyć rozmiar indeksu; należy ustawić `filterable=FALSE` dla pól, które nie mają być rzeczywiście używane w filtrze. Aby uzyskać więcej informacji na temat ustawień dotyczących definicje pól, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

W zestawie SDK .NET jest filtrowanie *poza* domyślnie. Interfejs API do ustawiania właściwości można filtrować jest [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). W przykładzie poniżej jej zestawu w definicji pola BaseRate.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Indeksowanie wymagania

Jeśli pole jest nie można filtrować i ma być możliwości filtrowania, należy dodać nowe pole lub przebudować istniejącego pola. Zmiana definicji pola zmienia Struktura fizyczna indeksu. W usłudze Azure Search wszystkich ścieżek dozwolonych dostępu są indeksowane dla zapytania szybkiego prędkości wymaga odbudowania struktur danych po zmianie definicji pola. 

Ponowne tworzenie poszczególnych pól może być operacją niski wpływ wymagające tylko operację scalania, która wysyła istniejący klucz dokumentu i skojarzone wartości do indeksu, pozostawiając bez zmian w pozostałej części każdego dokumentu. Jeśli wystąpią wymaganie odbudowy, zobacz następujące linki instrukcje:

 + [Indeksowanie akcji przy użyciu zestawu SDK platformy .NET](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Indeksowanie akcji za pomocą interfejsu API usługi REST](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Podstawowe informacje dotyczące filtru tekstu

Filtry tekstu są prawidłowe dla pól ciągów, z których chcesz ściągnąć niektórych kolekcji dokumentów na podstawie wartości w ramach Boże wyszukiwania.

Tekst filtrów składa się z ciągów nie nie leksykalne analizy lub dzielenia wyrazów, więc porównania dotyczą tylko dokładne dopasowania. Załóżmy na przykład, pole *f* zawiera "słoneczna day", `$filter=f eq 'Sunny'`nie jest zgodny, ale `$filter=f eq 'Sunny day'` będzie. 

Ciągi jest rozróżniana wielkość liter. Nie istnieje obudowy nie niższej pisane słów: `$filter=f eq 'Sunny day'` nie będzie zawierał "słoneczny dzień".


| Podejście | Opis | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkcja dostarczanie rozdzielana przecinkami lista ciągów dla danego pola. Ciągi obejmują kryteria filtrowania, które są stosowane do wszystkich pól w zakresie zapytania. <br/><br/>`search.in(f, ‘a, b, c’)` jest semantycznie równoważne `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, z tą różnicą, że go wykonuje znacznie szybciej, gdy lista wartości jest długa.<br/><br/>Firma Microsoft zaleca **search.in** działać w ramach [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) i wszystkie filtry składa się z pierwotnych tekst, który można dopasować wartości w danym polu. Ta metoda jest przeznaczona dla szybkości. Można oczekiwać, że czas odpowiedzi subsecond kilkuset do kilku tysięcy wartości. Gdy nie ma żadnego jawnego limitu liczby elementów, które można przekazać do funkcji, opóźnienia zwiększa się proporcjonalnie do liczby ciągów podane przez użytkownika. | 
| [Search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkcja, która umożliwia mieszać operacji wyszukiwania pełnotekstowego z operacjami filtr ściśle wartości logicznych, w tym samym wyrażeniu filtru. Umożliwia on wiele kombinacji Filtr kwerendy w jedno żądanie. Można również użyć go w celu *zawiera* do filtru na ciąg częściowe ciągu większy. |  
| [$filter = pole ciąg operatora](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Wyrażenie użytkownika składa się z pola, operatory i wartości. | 

## <a name="numeric-filter-fundamentals"></a>Podstawowe informacje dotyczące filtru numeryczne

Pól liczbowych nie są `searchable` w kontekście wyszukiwania pełnotekstowego. Tylko ciągi podlegają wyszukiwania pełnotekstowego. Na przykład po wprowadzeniu 99,99 jako terminu wyszukiwania nie powrócisz zakres cen w 99,99 $. Zamiast tego czy są wyświetlane elementy, które mają numer 99 w polach ciąg dokumentu. W związku z tym jeśli dane liczbowe, zakłada się, że będzie używać ich filtrów, w tym zakresy, aspekty, grup i tak dalej. 

Dokumenty zawierające pól liczbowych (cena, rozmiar jednostki SKU; identyfikator) podaj tych wartości w wynikach wyszukiwania, jeśli pole jest oznaczone jako `retrievable`. Punkt, w tym miejscu jest wyszukiwanie pełnotekstowe w tej samej nie ma zastosowania do typów pól liczbowych.

## <a name="next-steps"></a>Kolejne kroki

Najpierw spróbuj **Eksplorator wyszukiwania** w portalu, aby przesłać zapytania z **$filter** parametrów. [Indeksu rzeczywistym nieruchomości próby](search-get-started-portal.md) zapewnia następujące filtrowane zapytania podczas wklejania do pasek wyszukiwania interesujące wyników:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Aby pracować z więcej przykładów, zobacz [składni wyrażeń filtrów OData > przykłady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Zobacz także

+ [Ile wyszukiwanie pełnotekstowe działa w usłudze Azure Search](search-lucene-query-architecture.md)
+ [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
