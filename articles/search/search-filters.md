---
title: Filtrów określania zakresu wyniki wyszukiwania w indeksie — usługa Azure Search
description: Filtrowanie według tożsamości zabezpieczeń użytkownika, języka, lokalizacji geograficznej lub wartości liczbowych, aby ograniczyć wyniki wyszukiwania dla zapytań w usłudze Azure Search, Usługa wyszukiwania w hostowanej chmurze Microsoft Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a9e8d2cbc067fd92208fac778ba17c58bdc7a5e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289590"
---
# <a name="filters-in-azure-search"></a>Filtry w usłudze Azure Search 

A *filtru* przedstawiono kryteria wybierania dokumentów używanych w zapytaniu usługi Azure Search. Niefiltrowane wyszukiwanie obejmuje wszystkie dokumenty w indeksie. Filtr zakresów zapytania wyszukiwania do podzbioru dokumentów. Na przykład filtr można ograniczyć wyszukiwanie pełnotekstowe tylko te produkty mające konkretną markę lub kolor w punktach cenowych powyżej określonego progu.

Niektóre środowiska wyszukiwania nakłada filtr wymagań jako część wdrożenia, ale możesz użyć filtrów w dowolnym momencie chcesz ograniczyć wyszukiwanie przy użyciu *oparta na wartościach* kryteria (zakresu Wyszukaj produkt typu "books" category" inne niż fikcja"opublikowany przez"Simon i Schuster").

Jeśli zamiast tego celem jest docelowym wyszukiwanie w określonych danych *struktury* (zakresu wyszukiwania do pola recenzje klientów), istnieją alternatywne metody, w opisany poniżej.

## <a name="when-to-use-a-filter"></a>Kiedy należy używać filtru

Filtry są podstawowe do kilku środowiska wyszukiwania, w tym "Znajdź w pobliżu", nawigacji aspektowej i zabezpieczenia filtry przedstawiające te dokumenty, które użytkownik może wyświetlić. W przypadku zastosowania któregokolwiek z tych środowisk, filtr jest wymagany. Jest filtr dołączone do zapytania wyszukiwania, który zawiera współrzędne geolokalizacja kategorii aspekt wybrane przez użytkownika lub identyfikator zabezpieczeń obiektu żądającego.

Przykładowe scenariusze obejmują następujące czynności:

1. Użyj filtru w celu wycinek, indeksu na podstawie wartości danych w indeksie. Biorąc pod uwagę schematu z Miasto, mieszkalnictwa typu i pozwalającego, można utworzyć filtr, aby jawnie wybrać dokumenty, które spełniają kryteria (w Seattle condos, waterfront). 

   Wyszukiwanie pełnotekstowe przy użyciu tych samych wejściach często daje podobne wyniki, ale filtru jest bardziej precyzyjne, że wymaga dokładnego dopasowania termin z filtru dla treści w indeksie. 

2. Użyj filtru, jeśli środowisko wyszukiwania jest dostarczany z wymogiem filtru:

   * [Nawigacja aspektowa](search-faceted-navigation.md) używa filtru ponownie przekazać kategorii aspekt wybrane przez użytkownika.
   * Wyszukiwanie geograficzne używa filtru do przekazania aplikacji współrzędne bieżącego położenia w "Znajdź w pobliżu". 
   * Filtry zabezpieczeń należy przekazać identyfikatorów zabezpieczeń jako kryteria filtrowania, jeśli dopasowanie w indeksie służy jako serwer proxy dla prawa dostępu do dokumentu.

3. Użyj filtru, jeśli chcesz, aby kryteria wyszukiwania dotyczącej pola liczbowego. 

   Pola numeryczne są możliwe do pobierania w dokumencie i mogą być wyświetlane w wynikach wyszukiwania, ale nie są one można wyszukiwać (zależnie od wyszukiwanie pełnotekstowe) indywidualnie. Kryteria wyboru oparte na danych liczbowych, należy użyć filtru.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatywne metody zmniejszenie zakresu

Chcąc zawężające efekt w wynikach wyszukiwania, filtry nie są tylko opcja. Tych możliwości może być lepszym rozwiązaniem, w zależności od celu:

 + `searchFields` parametr zapytania przywiązuje wyszukiwanie określonych pól. Na przykład Indeks udostępnia oddzielne pola opisy w języku angielskim i hiszpańskim, można użyć searchFields pod kątem pola, które można użyć w celu wyszukiwania pełnotekstowego. 

+ `$select` parametr jest używany do określenia ustawić pola do uwzględnienia w wyniku, efektywnie przycinania odpowiedzi przed wysłaniem ich do aplikacji wywołującej. Ten parametr nie jest w stanie Uściślij zapytanie, lub zmniejszyć kolekcji dokumentów, ale jeśli szczegółowe odpowiedzi był Twój cel, ten parametr jest opcją do rozważenia. 

Aby uzyskać więcej informacji na temat albo parametr zobacz [wyszukiwania dokumentów > żądanie > Parametry zapytania](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>Filtry w potoku zapytania

Podczas przeszukiwania analizator Filtr akceptuje kryteria jako dane wejściowe, konwertuje wyrażenie na niepodzielne wyrażeń logicznych i tworzy drzewo filtru, który jest następnie oceniany przez filtrowanie pól w indeksie.  

Filtrowanie odbywa się przed wyszukiwania, kwalifikujących się które dokumenty, które mają zostać objęte przetwarzania transmisji dla klientów na potrzeby pobierania dokumentów i oceny istotności. W połączeniu z ciągu wyszukiwania, filtr skutecznie zmniejsza obszar powierzchni operacji wyszukiwania kolejnych. Gdy jest używana samodzielnie (na przykład, gdy ciąg zapytania jest puste miejsce `search=*`), kryteria filtru są wyłącznie dane wejściowe. 

## <a name="filter-definition"></a>Definicja filtru

Filtry są wyrażeniami OData, przegubowe przy użyciu [podzestawu składnią protokołu OData V4 obsługiwane w usłudze Azure Search](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Można określić jeden filtr dla każdej **wyszukiwania** operacja, ale sam filtr może zawierać wiele pól, wiele kryteriów, a gdy używasz **ismatch** funkcji, wiele wyrażeń. W wyrażeniu filtru wieloczęściowy można określić predykaty w dowolnej kolejności. Brak żadnych istotnych korzyści w wydajności, Jeśli spróbujesz zmienić układ predykatów w określonej kolejności.

Stały limit na wyrażenie filtru jest maksymalny limit dla żądania. Całego żądania, z uwzględnieniem filtru, może zawierać maksymalnie 16 MB POST lub 8 KB GET. Limity zmienne odnoszą się do liczby klauzul w wyrażeniu filtru. Regułą jest, że w przypadku setek klauzule jest ryzyka limit. Firma Microsoft zaleca, projektowania aplikacji w taki sposób, czy nie generuje filtry niepowiązane rozmiar.

Poniższe przykłady reprezentują definicje prototypowe filtrów w kilku interfejsów API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
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

Poniższe przykłady ilustrują kilka wzorców projektowych dla scenariuszy filtru. Aby uzyskać więcej pomysłów, zobacz [składnia wyrażenia OData > przykłady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ Autonomiczny **$filter**, bez ciągu zapytania, przydatne, gdy wyrażenie filtru jest w stanie do pełnej kwalifikacji dokumentów zainteresowania. Bez ciąg zapytania ma żadnej analizy leksykalne lub językową, nie oceniania i nie klasyfikacji. Należy zauważyć, że ciąg wyszukiwania jest pusty.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Kombinacja ciągu zapytania i **$filter**, gdzie filtru tworzy podzbiór i ciągu zapytania udostępnia dane wejściowe termin w celu wyszukiwania pełnotekstowego w porównaniu z podzbioru filtrowane. Korzystanie z filtra za pomocą ciągu zapytania jest najczęstszym wzorcem kodu.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Złożone kwerendy, oddzielone "lub", każdy z własną kryteria filtrowania (na przykład "beagles" w "dog") lub "siamese" w "cat". LUB miał wyrażenia są obliczane osobno, przy użyciu odpowiedzi z każdym z nich połączone w jedną odpowiedź wysyłanych z powrotem do aplikacji wywołującej. Ten wzorzec projektowy odbywa się za pośrednictwem funkcji search.ismatch. Można użyć wersji oceniania (search.ismatch) lub wersji oceniania (search.ismatchscoring).

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Monitowanie następujące artykuły, aby uzyskać kompleksowe wskazówki na wybrane przypadki użycia:

+ [Filtry aspektów](search-filters-facets.md)
+ [Filtry języków](search-filters-language.md)
+ [Dostosowanie do zabezpieczeń](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Wymagania pola do filtrowania

W interfejsie API REST można filtrować jest *na* domyślnie. Pól z możliwością filtrowania zwiększyć rozmiar indeksu. Pamiętaj ustawić `filterable=FALSE` dla pól, które są rzeczywiście używane w filtrze nie jest planowane. Aby uzyskać więcej informacji na temat ustawień dotyczących definicje pól, zobacz [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

W zestawie SDK platformy .NET jest filtrowanie *poza* domyślnie. Interfejs API do ustawiania właściwości filtrowania [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). W przykładzie poniżej swój zestaw na BaseRate definicji pola.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Ponowne indeksowanie wymagania

Jeśli pole jest inne niż można filtrować i ma być filtrowania, należy dodać nowe pola lub ponownie skompiluj istniejącego pola. Zmiana definicji pola zmienia Struktura fizyczna indeksu. W usłudze Azure Search wszystkich ścieżek dostępu dozwolone są indeksowane w celu szybkość szybkie zapytania wymaga ponownej kompilacji struktur danych, gdy zmienią się definicje pól. 

Ponowne tworzenie poszczególnych pól może być operacją mały wpływ wymagające tylko operację scalania, która wysyła do indeksu, pozostawiając bez zmian w pozostałej części dokumentu, każdy istniejący klucz dokumentu i skojarzone wartości. Jeśli wystąpią wymagania ponowną kompilację, zobacz [akcji indeksowania (przekazanie, scalenie, mergeOrUpload, Usuń)](search-what-is-data-import.md#indexing-actions) listę opcji.


## <a name="text-filter-fundamentals"></a>Podstawowe informacje dotyczące tekstu filtru

Filtry tekstu obowiązują w przypadku pól ciągów, z których chcesz ściągnąć niektóre kolekcji dokumentów na podstawie wartości w głównej części.

Filtry tekstu składa się z ciągów nie ma poddawać analizie leksykalnej ani wyrazów, więc porównania dotyczą tylko dokładne dopasowania. Załóżmy na przykład, pole *f* zawiera "sunny day" `$filter=f eq 'Sunny'`nie jest zgodny, ale `$filter=f eq 'Sunny day'` będzie. 

Ciągi tekstowe jest rozróżniana wielkość liter. Istnieje nie małe-wielkość liter w wyrazie pisane słowa: `$filter=f eq 'Sunny day'` nie zostaną znalezione "słoneczny dzień".


| Podejście | Opis | 
|----------|-------------|
| [search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkcja, zapewniając rozdzielana przecinkami lista ciągów dla danego pola. Ciągi obejmują kryteria filtrowania, które są stosowane do wszystkich pól w zakresie dla zapytania. <br/><br/>`search.in(f, ‘a, b, c’)` są semantycznie równoważne `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, z tą różnicą, że znacznie szybciej wykonuje po duże listy wartości.<br/><br/>Firma Microsoft zaleca **search.in** działać w ramach [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) i w polu wszelkie filtry składa się z nieprzetworzony tekst do dopasowania wartości w danym polu. Ta metoda jest przeznaczona dla danej szybkości. Można oczekiwać, że czas odpowiedzi subsecond setki tysięcy wartości. W trakcie nie jawnego limit liczby elementów, które można przekazać do funkcji opóźnienie zwiększa się proporcjonalnie do liczby parametrów, których udzielasz. | 
| [search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Funkcja, która pozwala łączyć operacji wyszukiwania pełnotekstowego przy użyciu operacji ściśle logiczna filtrów w tym samym wyrażeniu filtru. Dzięki temu wiele kombinacji filtr zapytania w jednym żądaniu. Można również użyć go do *zawiera* filtr do filtru na częściowe ciągu w dłuższym ciągu. |  
| [$filter = ciąg operator pola](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Wyrażenie zdefiniowanych przez użytkownika, składa się z pola, operatory i wartości. | 

## <a name="numeric-filter-fundamentals"></a>Podstawowe informacje dotyczące filtru liczbowego

Pola numeryczne są `searchable` w kontekście wyszukiwania pełnotekstowego. Tylko ciągi podlegają wyszukiwanie pełnotekstowe. Na przykład po wprowadzeniu 99,99 jako wyszukiwany termin nie wrócisz naliczana stawka równa $99,99 elementów. Czy zamiast tego Zobacz elementy, które mają numer 99 w polach ciąg dokumentu. W związku z tym jeśli masz dane liczbowe, zakłada się, że użytkownik użyje ich filtry, w tym zakresy, aspektami, grup i tak dalej. 

Dokumenty, które zawierają pola numeryczne (cena, rozmiar jednostki SKU, identyfikator) Podaj te wartości w wynikach wyszukiwania, jeśli jest oznaczony `retrievable`. Punkt, w tym miejscu jest sam wyszukiwania pełnotekstowego nie ma zastosowania do typów pól liczbowych.

## <a name="next-steps"></a>Kolejne kroki

Najpierw zainstaluj **Eksploratora wyszukiwania** w portalu do przesyłania zapytań przy użyciu **$filter** parametrów. [Rzeczywistym nieruchomości — przykładowy indeks](search-get-started-portal.md) zawiera interesujące wyniki dla następujących filtrowanych zapytania po wklejeniu na pasku wyszukiwania:

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

Aby pracować z innymi przykładami, zobacz [składnia wyrażeń filtru OData > przykłady](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="see-also"></a>Zobacz także

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Search](search-lucene-query-architecture.md)
+ [Wyszukiwanie w dokumentach interfejsu API REST](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
