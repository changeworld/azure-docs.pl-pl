---
title: Filtrowanie w wynikach wyszukiwania
titleSuffix: Azure Cognitive Search
description: Filtruj według tożsamości zabezpieczeń użytkownika, języka, lokalizacji geograficznej lub wartości liczbowych, aby zmniejszyć wyniki wyszukiwania w kwerendach w usłudze Azure Cognitive Search, hostowej usłudze wyszukiwania w chmurze na platformie Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191021"
---
# <a name="filters-in-azure-cognitive-search"></a>Filtry w usłudze Azure Cognitive Search 

*Filtr* zawiera kryteria wyboru dokumentów używanych w kwerendzie usługi Azure Cognitive Search. Wyszukiwanie niefiltrowane zawiera wszystkie dokumenty w indeksie. Filtr zakresy kwerendy wyszukiwania do podzbioru dokumentów. Na przykład filtr może ograniczyć wyszukiwanie pełnotekstowe tylko do tych produktów o określonej marce lub kolorze, w cenach powyżej określonego progu.

Niektóre środowiska wyszukiwania nakładają wymagania dotyczące filtrów w ramach implementacji, ale można używać filtrów w dowolnym momencie, aby ograniczyć wyszukiwanie przy użyciu kryteriów *opartych na wartości* (wyszukiwanie zakresu do typu produktu "książki" dla kategorii "non-fiction" opublikowanej przez "Simon & Schuster").

Jeśli zamiast tego twoim celem jest ukierunkowane wyszukiwanie w określonych *strukturach* danych (wyszukiwanie zakresu do pola opinii klienta), istnieją alternatywne metody, opisane poniżej.

## <a name="when-to-use-a-filter"></a>Kiedy używać filtra

Filtry są podstawą kilku środowisk wyszukiwania, w tym "znajdź blisko mnie", nawigacji aspektowej i filtrów zabezpieczeń, które pokazują tylko te dokumenty, które użytkownik może zobaczyć. Jeśli zaimplementujesz jedno z tych środowisk, wymagany jest filtr. Jest to filtr dołączony do kwerendy wyszukiwania, który udostępnia współrzędne geolokalizacji, kategorię aspektu wybraną przez użytkownika lub identyfikator zabezpieczeń żądacza.

Przykładowe scenariusze są następujące:

1. Użyj filtru, aby pokroić indeks na podstawie wartości danych w indeksie. Biorąc pod uwagę schemat z miasta, typu mieszkania i udogodnienia, można utworzyć filtr jawnie wybrać dokumenty, które spełniają kryteria (w Seattle, mieszkania, nabrzeża). 

   Wyszukiwanie pełnotekstowe z tymi samymi wejściami często daje podobne wyniki, ale filtr jest bardziej precyzyjny, ponieważ wymaga dokładnego dopasowania terminu filtru do zawartości w indeksie. 

2. Użyj filtru, jeśli środowisko wyszukiwania jest dostarczane z wymaganiem filtru:

   * [Nawigacja aspektowa](search-faceted-navigation.md) używa filtru do przekazywania kategorii aspektu wybranej przez użytkownika.
   * Wyszukiwanie geograficzne używa filtru do przekazywania współrzędnych bieżącej lokalizacji w aplikacjach "znajdź blisko mnie". 
   * Filtry zabezpieczeń przekazują identyfikatory zabezpieczeń jako kryteria filtrowania, gdzie dopasowanie w indeksie służy jako serwer proxy praw dostępu do dokumentu.

3. Użyj filtru, jeśli chcesz, aby kryteria wyszukiwania w polu liczbowym. 

   Pola liczbowe można pobrać w dokumencie i mogą być wyświetlane w wynikach wyszukiwania, ale nie można ich przeszukiwać (z zastrzeżeniem wyszukiwania pełnotekstowego) indywidualnie. Jeśli potrzebujesz kryteriów wyboru opartych na danych liczbowych, użyj filtru.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatywne metody ograniczania zakresu

Jeśli chcesz uzyskać efekt zawężenia w wynikach wyszukiwania, filtry nie są jedynym wyborem. Te alternatywy mogą być lepiej dopasowane, w zależności od celu:

 + `searchFields`kody parametry zapytania wyszukiwania do określonych pól. Jeśli na przykład indeks zawiera oddzielne pola dla opisów w języku angielskim i hiszpańskim, można użyć searchFields do kierowania pól, które mają być używane do wyszukiwania pełnotekstowego. 

+ `$select`parametr jest używany do określenia, które pola mają być uwzględnione w zestawie wyników, skutecznie przycinając odpowiedź przed wysłaniem jej do aplikacji wywołującej. Ten parametr nie uściśla kwerendy ani nie zmniejsza kolekcji dokumentów, ale jeśli celem jest mniejsza odpowiedź, ten parametr jest opcją do rozważenia. 

Aby uzyskać więcej informacji na temat jednego z tych [parametrów,](/rest/api/searchservice/search-documents#query-parameters)zobacz Wyszukiwanie dokumentów > Żądanie > parametry kwerendy .


## <a name="how-filters-are-executed"></a>Sposób wykonywania filtrów

W czasie kwerendy analizator filtru akceptuje kryteria jako dane wejściowe, konwertuje wyrażenie na niepodzielne wyrażenia logiczne reprezentowane jako drzewo, a następnie ocenia drzewo filtru nad filtrowanymi polami w indeksie.

Filtrowanie odbywa się w połączeniu z wyszukiwaniem, kwalifikując dokumenty do uwzględnienia w dalszym przetwarzaniu do pobierania dokumentów i oceniania trafności. Po sparowaniu z ciągiem wyszukiwania filtr skutecznie zmniejsza zestaw odwołań kolejnej operacji wyszukiwania. Gdy używany samodzielnie (na przykład, gdy `search=*`ciąg zapytania jest pusty, gdzie), kryteria filtrowania jest jedynym wejściem. 

## <a name="defining-filters"></a>Definiowanie filtrów
Filtry są wyrażeniami OData, wyrażonymi przy użyciu [podzbioru składni OData V4 obsługiwanego w usłudze Azure Cognitive Search.](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 

Można określić jeden filtr dla każdej operacji **wyszukiwania,** ale sam filtr może zawierać wiele pól, wiele kryteriów, a jeśli używasz funkcji **ismatch,** wiele wyrażeń wyszukiwania pełnotekstowego. W wyrażeniu filtru wieloczęściowego można określić predykaty w dowolnej kolejności (z zastrzeżeniem reguł pierwszeństwa operatora). Nie ma znaczącego przyrostu wydajności, jeśli spróbujesz zmienić kolejność predykatów w określonej kolejności.

Jednym z limitów wyrażenia filtru jest maksymalny limit rozmiaru żądania. Całe żądanie, łącznie z filtrem, może mieć maksymalnie 16 MB dla postu lub 8 KB dla GET. Istnieje również limit liczby klauzul w wyrażeniu filtru. Dobrą zasadą jest to, że jeśli masz setki klauzul, jesteś na ryzyko uruchomienia w limicie. Zaleca się projektowanie aplikacji w taki sposób, aby nie generować filtry o rozmiarze nieograniczonym.

Poniższe przykłady przedstawiają definicje filtrów prototypowych w kilku interfejsach API.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrowanie wzorców użycia

Poniższe przykłady ilustrują kilka wzorców użycia dla scenariuszy filtrów. Aby uzyskać więcej pomysłów, zobacz [Składnia wyrażenia OData > Przykłady](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ Samodzielny **$filter**, bez ciągu zapytania, przydatne, gdy wyrażenie filtru jest w stanie w pełni zakwalifikować dokumenty zainteresowania. Bez ciągu zapytania nie ma analizy leksykalne lub językowej, nie oceniania i nie ma rankingu. Zwróć uwagę, że ciąg wyszukiwania to tylko gwiazdka, co oznacza "dopasuj wszystkie dokumenty".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ Kombinacja ciągu zapytania i **$filter**, gdzie filtr tworzy podzbiór, a ciąg zapytania udostępnia dane wejściowe terminów do wyszukiwania pełnotekstowego w filtrowanym podzbiorze. Dodanie terminów (teatry odległości spaceru) wprowadza wyniki wyszukiwania w wynikach, gdzie dokumenty, które najlepiej pasują do warunków, są wyżej klasyfikowane. Za pomocą filtru z ciągiem zapytania jest najczęściej wzorzec użycia.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Zapytania złożone, oddzielone "lub", każdy z własnymi kryteriami filtrowania (na przykład "beagle" w "pies" lub "syjamski" w "kot"). Wyrażenia w `or` połączeniu z są oceniane indywidualnie, z unii dokumentów dopasowania każdego wyrażenia wysyłane z powrotem w odpowiedzi. Ten wzorzec użycia `search.ismatchscoring` jest osiągany za pomocą funkcji. Można również użyć wersji bez `search.ismatch`oceniania, .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Możliwe jest również łączenie wyszukiwania pełnotekstowego `search.ismatchscoring` za pomocą filtrów `and` zamiast `or`, ale `search` jest `$filter` to funkcjonalnie równoważne użyciu parametrów i parametrów w żądaniu wyszukiwania. Na przykład następujące dwa zapytania dają ten sam wynik:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Aby uzyskać wyczerpujące wskazówki dotyczące konkretnych przypadków użycia, należy wykonać następujące działania:

+ [Filtry aspektów](search-filters-facets.md)
+ [Filtry języków](search-filters-language.md)
+ [Dostosowanie do zabezpieczeń](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Wymagania dotyczące pola filtrowania

W interfejsie API REST można filtrować jest domyślnie *włączony* dla prostych pól. Pola filtrowane zwiększają rozmiar indeksu; należy ustawić `"filterable": false` dla pól, których nie zamierzasz używać w filtrze. Aby uzyskać więcej informacji o ustawieniach definicji pól, zobacz [Tworzenie indeksu](https://docs.microsoft.com/rest/api/searchservice/create-index).

W pliku .NET SDK filtrowanie jest domyślnie *wyłączone.* Można filtrować pole, ustawiając [właściwość IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) odpowiedniego `true`obiektu [Field](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) na . Można to również zrobić deklaratywnie przy użyciu [IsFilterable atrybut](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). W poniższym przykładzie atrybut jest `BaseRate` ustawiony na właściwości klasy modelu, która jest mapowana do definicji indeksu.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Możliwość filtrowania istniejącego pola

Nie można modyfikować istniejących pól, aby można je było filtrować. Zamiast tego należy dodać nowe pole lub odbudować indeks. Aby uzyskać więcej informacji na temat odbudowywania indeksu lub pól ponownego wypełniania, zobacz [Jak odbudować indeks usługi Azure Cognitive Search](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Podstawowe informacje o filtrze tekstu

Filtry tekstowe pasują do pól ciągów do dosłownych, które są dostępne w filtrze. W przeciwieństwie do wyszukiwania pełnotekstowego nie ma analizy leksykalne lub podziału wyrazów dla filtrów tekstu, więc porównania są tylko dla dokładnych dopasowań. Załóżmy na *f* przykład, że pole `$filter=f eq 'Sunny'` f zawiera `$filter=f eq 'sunny day'` "słoneczny dzień", nie pasuje, ale będzie. 

W ciągach tekstowych rozróżniana jest wielkość liter. Nie ma dolnej obudowy wielkich liter: `$filter=f eq 'Sunny day'` nie znajdzie "słoneczny dzień".

### <a name="approaches-for-filtering-on-text"></a>Podejścia do filtrowania tekstu

| Podejście | Opis | Kiedy stosować |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Funkcja, która pasuje do pola względem rozdzielonej listy ciągów. | Zalecane dla [filtrów zabezpieczeń](search-security-trimming-for-azure-search.md) i filtrów, w których wiele nieprzetworzonych wartości tekstu musi być dopasowanych do pola ciągu. Funkcja **search.in** jest przeznaczona do szybkości i jest znacznie szybsza niż `eq` jawne porównywanie pola z każdym ciągiem za pomocą i `or`. | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Funkcja, która umożliwia mieszanie operacji wyszukiwania pełnotekstowego z operacjami filtru ściśle logicznego w tym samym wyrażeniu filtru. | Użyj **search.ismatch** (lub jego odpowiednik punktacji, **search.ismatchscoring),** jeśli chcesz wiele kombinacji filtr wyszukiwania w jednym żądaniu. Można również użyć go do *filtru zawiera* do filtrowania na ciąg częściowy w ciągu większym. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Wyrażenie zdefiniowane przez użytkownika składające się z pól, operatorów i wartości. | Użyj tego, aby znaleźć dokładne dopasowania między polem ciągu a wartością ciągu. |

## <a name="numeric-filter-fundamentals"></a>Podstawy filtru numerycznego

Pola liczbowe `searchable` nie są w kontekście wyszukiwania pełnotekstowego. Tylko ciągi znaków podlegają wyszukiwaniu pełnotekstowe. Jeśli na przykład wpiszesz 99,99 jako wyszukiwany termin, nie otrzymasz przedmiotów w cenie 99,99 USD. Zamiast tego zostaną wyświetlone elementy, które mają liczbę 99 w polach ciągu dokumentu. W związku z tym, jeśli masz dane liczbowe, założenie jest, że będzie ich używać do filtrów, w tym zakresów, aspektów, grup i tak dalej. 

Dokumenty zawierające pola liczbowe (cena, rozmiar, jednostka SKU, identyfikator) zawierają te `retrievable`wartości w wynikach wyszukiwania, jeśli pole jest zaznaczone . Chodzi o to, że samo wyszukiwanie pełnotekstowe nie ma zastosowania do typów pól liczbowych.

## <a name="next-steps"></a>Następne kroki

Najpierw spróbuj **Eksploratora wyszukiwania** w portalu, aby przesłać zapytania z **$filter** parametrami. [Indeks przykładu nieruchomości](search-get-started-portal.md) zapewnia interesujące wyniki dla następujących filtrowanych zapytań po wklejeniu ich do paska wyszukiwania:

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

Aby pracować z większą liczniejszymi przykładami, zobacz [Składnia wyrażenia filtru OData > przykłady](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

## <a name="see-also"></a>Zobacz też

+ [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Interfejs API REST wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Prosta składnia zapytań](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Składnia zapytań Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Obsługiwane typy danych](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
